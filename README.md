### Roteiro de Aula: **Gerenciamento de Estado com Flutter usando Provider**

**Objetivo:**  
Ensinar os conceitos de gerenciamento de estado no Flutter utilizando o pacote **Provider**. Ao final da aula, os alunos serão capazes de criar uma aplicação que gerencie o estado de maneira eficiente e entender como o estado reflete na interface do usuário.

---

### 1. **Introdução (10 minutos)**

#### A. **Abertura**

**Instrutor fala:**  
"Bem-vindos à aula sobre gerenciamento de estado no Flutter. Hoje, vamos aprender a trabalhar com estado utilizando o pacote Provider, que é uma solução eficiente e muito utilizada na comunidade Flutter. Mas, antes de começarmos a implementação, é importante entender o que significa gerenciar o estado de uma aplicação."

**Slide 1: O que é Estado?**

**Instrutor fala:**  
"O estado refere-se a qualquer dado que pode mudar durante a execução da aplicação e que afeta a interface do usuário. Por exemplo, pense em uma lista de tarefas. Toda vez que uma nova tarefa é adicionada ou removida, precisamos atualizar a interface para refletir essa mudança. É isso que chamamos de 'gerenciamento de estado'."

#### B. **Problemas Sem Gerenciamento de Estado**

**Instrutor fala:**  
"Se não gerenciarmos bem o estado, podemos acabar com uma aplicação confusa e difícil de manter. Imagine uma tela onde múltiplos widgets precisam acessar e modificar o mesmo dado. Sem um bom gerenciamento, fica complicado garantir que a interface e o estado estejam sempre sincronizados. E é aí que entra a nossa solução: o Provider."

#### C. **Solução: Provider**

**Slide 2: Provider - Padrão Observer**

**Instrutor fala:**  
"O pacote Provider é uma maneira simples e eficiente de gerenciar o estado no Flutter. Ele segue o padrão Observer, onde um widget pode observar mudanças de estado e ser notificado quando algo muda. O Provider facilita isso ao permitir que qualquer parte da aplicação acesse e modifique o estado de maneira centralizada. Hoje, vamos explorar como usá-lo em uma aplicação real."

---

### 2. **Criação do Projeto (15 minutos)**

#### A. **Configuração Inicial**

**Slide 3: Criando o Projeto Flutter**

**Instrutor fala:**  
"Agora vamos começar com a parte prática. Primeiro, vamos criar um novo projeto Flutter. Abra o terminal e execute o seguinte comando para criar o projeto que usaremos hoje:  
`flutter create state_management_app`."

- Aguarde os alunos criarem o projeto.

**Instrutor fala:**  
"Agora que o projeto está criado, vamos abrir no editor de código de sua escolha. Pode ser o VS Code ou o Android Studio, o que você preferir."

**Slide 4: Instalando o Provider**

**Instrutor fala:**  
"O próximo passo é instalar o pacote Provider, que será o responsável por gerenciar o estado da nossa aplicação. Para isso, abra o arquivo `pubspec.yaml` e adicione a seguinte dependência:  
`provider: ^6.0.0`  
Depois, execute o comando `flutter pub get` para instalar o pacote."

- Aguarde os alunos instalarem a dependência.

#### B. **Estrutura do Projeto**

**Slide 5: Estrutura do Projeto**

**Instrutor fala:**  
"Nosso projeto vai seguir uma estrutura simples, com três pastas principais:
- **models**: onde vamos armazenar nossos modelos de dados.
- **screens**: onde vamos criar as telas da aplicação.

Essa organização facilita a manutenção do código, separando claramente a lógica de negócio da interface do usuário."

---

### 3. **Implementação do Gerenciamento de Estado (30 minutos)**

#### A. **Criação do Modelo**

**Slide 6: Criando o Modelo de Estado**

**Instrutor fala:**  
"Agora vamos criar o nosso modelo de dados, que vai representar o estado da aplicação. Vamos começar criando um arquivo chamado `task_model.dart` na pasta `lib/models`. Nesse arquivo, vamos usar a classe `ChangeNotifier`, que nos permitirá notificar a interface sobre mudanças no estado."

**Instrutor fala:**  
"O `ChangeNotifier` é uma classe que faz parte do pacote Flutter. Ele nos permite notificar os ouvintes quando alguma coisa muda, fazendo com que a interface seja reconstruída automaticamente. Vamos ver isso na prática."

- Digite o código no arquivo `task_model.dart`:

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

**Instrutor fala:**  
"Como podem ver, nossa classe `TaskModel` tem uma lista de tarefas e dois métodos: `addTask` e `removeTask`. Toda vez que uma tarefa é adicionada ou removida, chamamos `notifyListeners()`, que notifica todos os widgets ouvintes sobre a mudança."

#### B. **Configurando o Provider**

**Slide 7: Configurando o Provider no Main**

**Instrutor fala:**  
"Agora que temos o modelo de estado, precisamos configurar o Provider para que ele possa ser acessado em qualquer parte da aplicação. Vamos fazer isso no arquivo `main.dart`."

- Digite o código no arquivo `main.dart`:

```dart
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
```

**Instrutor fala:**  
"Estamos usando `MultiProvider`, que nos permite adicionar múltiplos provedores caso seja necessário no futuro. Neste exemplo, estamos passando o nosso `TaskModel` como um `ChangeNotifierProvider`, permitindo que o estado seja gerenciado globalmente na aplicação."

#### C. **Implementação da Interface**

**Slide 8: Criando a Interface do Usuário**

**Instrutor fala:**  
"Agora vamos criar a interface. Precisamos de uma tela onde o usuário possa adicionar e remover tarefas. Vamos criar um arquivo chamado `task_screen.dart` na pasta `lib/screens` e implementar nossa interface."

- Digite o código no arquivo `task_screen.dart`:

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

**Instrutor fala:**  
"Nesse código, estamos usando um `TextField` para inserir as tarefas e um `ElevatedButton` para adicioná-las à lista. Usamos o `Consumer` para reconstruir a interface automaticamente quando o estado das tarefas muda, refletindo as mudanças de forma dinâmica."

---

### 4. **Demonstração da Aplicação (10 minutos)**

#### A. **Executando a Aplicação**

**Instrutor fala:**  
"Agora, vamos rodar a aplicação para ver como funciona. Abra o terminal e execute o comando `flutter run`. Aqui temos a tela onde podemos adicionar uma tarefa, e ela é listada logo abaixo. Vamos adicionar algumas tarefas para ver como funciona."

- Demonstre a inserção de tarefas e a remoção de tarefas na interface.

#### B. **Analisando o Fluxo**

**Instrutor fala:**  
"O fluxo de dados é simples. Quando adicionamos uma tarefa, o modelo de estado `TaskModel` atualiza a lista e notifica a interface, que se reconstrói automaticamente. Da mesma forma, ao remover uma tarefa, o modelo notifica e a interface atualiza."

---

### 5. **Encerramento (5 minutos)**

#### A. **Recapitulando**

**Instrutor fala:**  
"Hoje, vimos como o gerenciamento de estado é essencial para mantermos a interface sincronizada com os dados da aplicação. Utilizamos o Provider para gerenciar o estado e garantir que nossa UI reflita todas as mudanças no estado."

#### B. **Exercício Proposto**

**Instrutor fala:**  
"Para praticar, como exercício de casa, quero que vocês implementem uma funcionalidade de edição de tarefas. Ou seja, além de adicionar e remover, vocês devem permitir que o usuário edite uma tarefa já adicionada."

#### C. **Encerramento**

**Instrutor fala:**  
"Na próxima aula,

 vamos explorar outras formas de gerenciamento de estado, como o Riverpod. Até lá, pratiquem com o Provider. Obrigado a todos!"

---

Esse roteiro abrange desde os conceitos teóricos até a prática, incluindo as falas para orientar os alunos.
