### Tutorial: **Gerenciamento de Estado no Flutter com Provider**

Neste tutorial, vamos aprender a implementar o gerenciamento de estado no Flutter usando o pacote **Provider**. Veremos como criar um aplicativo simples de gerenciamento de tarefas, onde os usuários podem adicionar e remover tarefas, e a interface será atualizada automaticamente sempre que o estado mudar.

---

### **Pré-requisitos**
Antes de começar, certifique-se de ter o seguinte instalado:
- Flutter SDK
- Um editor de código como **VS Code** ou **Android Studio**

### **Passo 1: Criando o Projeto Flutter**

Abra o terminal e crie um novo projeto Flutter com o seguinte comando:

```bash
flutter create state_management_app
```

Em seguida, navegue até o diretório do projeto:

```bash
cd state_management_app
```

Abra o projeto no seu editor de código.

---

### **Passo 2: Adicionando o Provider**

Para gerenciar o estado, vamos usar o pacote **Provider**. Abra o arquivo `pubspec.yaml` e adicione a dependência:

```yaml
dependencies:
  flutter:
    sdk: flutter
  provider: ^6.0.0
```

Depois, execute o comando para instalar as dependências:

```bash
flutter pub get
```

---

### **Passo 3: Criando o Modelo de Estado**

Vamos criar uma classe que representará nosso estado. Crie um arquivo chamado `task_model.dart` na pasta `lib/models` e adicione o seguinte código:

```dart
import 'package:flutter/foundation.dart';

class TaskModel with ChangeNotifier {
  List<String> _tasks = [];

  List<String> get tasks => _tasks;

  void addTask(String task) {
    _tasks.add(task);
    notifyListeners();
  }

  void removeTask(int index) {
    _tasks.removeAt(index);
    notifyListeners();
  }
}
```

### **Explicação**:
- `TaskModel` gerencia uma lista de tarefas (_tasks).
- `ChangeNotifier` é uma classe que permite notificar os widgets quando o estado muda.
- `notifyListeners()` é chamado sempre que uma tarefa é adicionada ou removida, avisando os widgets que eles precisam ser reconstruídos.

---

### **Passo 4: Configurando o Provider**

Agora, vamos configurar o **Provider** no arquivo `main.dart` para que ele possa ser usado em toda a aplicação.

Abra o arquivo `lib/main.dart` e modifique o código para incluir o Provider:

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'models/task_model.dart';

void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => TaskModel()),
      ],
      child: MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: TaskScreen(),
    );
  }
}
```

### **Explicação**:
- Usamos o `ChangeNotifierProvider` para fornecer o estado (`TaskModel`) à árvore de widgets.
- `MultiProvider` permite adicionar mais provedores no futuro, se necessário.

---

### **Passo 5: Criando a Interface de Usuário**

Vamos criar a tela onde o usuário pode adicionar e remover tarefas. Crie um arquivo chamado `task_screen.dart` na pasta `lib/screens` e adicione o seguinte código:

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../models/task_model.dart';

class TaskScreen extends StatelessWidget {
  final TextEditingController taskController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Task Manager'),
      ),
      body: Column(
        children: [
          Padding(
            padding: const EdgeInsets.all(8.0),
            child: TextField(
              controller: taskController,
              decoration: InputDecoration(
                labelText: 'Enter Task',
                border: OutlineInputBorder(),
              ),
            ),
          ),
          ElevatedButton(
            onPressed: () {
              if (taskController.text.isNotEmpty) {
                Provider.of<TaskModel>(context, listen: false)
                    .addTask(taskController.text);
                taskController.clear();
              }
            },
            child: Text('Add Task'),
          ),
          Expanded(
            child: Consumer<TaskModel>(
              builder: (context, taskModel, child) {
                return ListView.builder(
                  itemCount: taskModel.tasks.length,
                  itemBuilder: (context, index) {
                    return ListTile(
                      title: Text(taskModel.tasks[index]),
                      trailing: IconButton(
                        icon: Icon(Icons.delete),
                        onPressed: () {
                          Provider.of<TaskModel>(context, listen: false)
                              .removeTask(index);
                        },
                      ),
                    );
                  },
                );
              },
            ),
          ),
        ],
      ),
    );
  }
}
```

### **Explicação**:
- O `TextField` captura a entrada do usuário para adicionar uma tarefa.
- O `ElevatedButton` adiciona a tarefa ao modelo quando pressionado.
- Usamos o `Consumer<TaskModel>` para observar mudanças no estado. Sempre que uma tarefa é adicionada ou removida, a lista é atualizada automaticamente.

---

### **Passo 6: Executando a Aplicação**

Agora que o código está completo, vamos rodar a aplicação. No terminal, execute:

```bash
flutter run
```

Você verá uma tela onde poderá adicionar e remover tarefas. Quando uma tarefa é adicionada, ela aparecerá imediatamente na lista. Quando uma tarefa é removida, ela desaparece da lista automaticamente.

---

### **Conclusão**

Neste tutorial, você aprendeu a:
- Configurar o **Provider** para gerenciar o estado no Flutter.
- Criar um modelo de estado usando o **ChangeNotifier**.
- Usar o **Consumer** para refletir mudanças no estado na interface de usuário.

O **Provider** é uma das maneiras mais eficientes de gerenciar o estado em aplicações Flutter. Ele é simples de usar e permite que os widgets observem mudanças no estado de forma reativa.

---

### **Desafio**
Como desafio, adicione uma funcionalidade para **editar** uma tarefa. Para isso, crie um botão que permita ao usuário modificar uma tarefa existente.

Espero que este tutorial tenha sido útil!