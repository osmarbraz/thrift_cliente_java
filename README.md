# Cliente Calculadora com Apache Thrift em Java

## Sobre o projeto

Este projeto implementa o **cliente gráfico de uma calculadora distribuída**, desenvolvido em Java com Swing e Apache Thrift. Ele permite que o usuário informe dois números, escolha uma operação matemática e envie a solicitação a um servidor remoto. O servidor executa a operação e devolve o resultado para ser exibido na interface.

O cliente faz parte do projeto de calculadora distribuída que utiliza o servidor Thrift configurado, por padrão, na porta `9090`.

## Objetivos didáticos

O projeto demonstra:

- Comunicação entre aplicações por meio de uma rede.
- Uso do Apache Thrift para chamadas remotas de métodos.
- Separação entre interface gráfica e controle da aplicação.
- Criação de conexões TCP com `TSocket`.
- Uso do protocolo binário `TBinaryProtocol`.
- Consumo de um serviço remoto por meio de uma classe cliente gerada pelo Thrift.

## Tecnologias utilizadas

- **Java** — linguagem de programação.
- **Java Swing** — construção da interface gráfica.
- **Apache Maven** — gerenciamento de dependências e configuração de compilação do projeto.
- **Apache Thrift** — comunicação entre cliente e servidor.
- **TCP** — transporte dos dados entre as aplicações.
- **TBinaryProtocol** — protocolo usado para serializar e transmitir as mensagens Thrift.

> A versão das bibliotecas Apache Thrift do cliente deve ser compatível com a utilizada pelo servidor e com as classes geradas a partir da definição do serviço.

## Estrutura do projeto

Os principais arquivos do cliente são:

| Arquivo | Responsabilidade |
|---|---|
| `pom.xml` | Configura o projeto Maven, identifica o artefato, define Java 8 e declara a dependência `libthrift` na versão `0.10.0`. |
| `calculadora.thrift` | Define o serviço `CalculadoraService` e as quatro operações remotas. |
| `src/main/java/Principal.java` | Ponto de entrada da aplicação. Cria o controlador e inicia o cliente. |
| `src/main/java/CtlCalculadora.java` | Cria e exibe a janela da calculadora. |
| `src/main/java/FrmCalculadora.java` | Interface gráfica e lógica que conecta ao servidor e solicita as operações. |
| `src/main/java/CalculadoraService.java` | Código cliente gerado pelo Apache Thrift, contendo os métodos disponibilizados pelo serviço. |
| `src/main/java/FrmCalculadora.form` | Arquivo de formulário visual, caso o projeto utilize o editor de formulários da IDE. |

## Definição do serviço: `calculadora.thrift`

O arquivo fornecido contém esta definição:

``` thrift
service CalculadoraService
{
    double multiplicar(1:double n1, 2:double n2),
    double dividir(1:double n1, 2:double n2),
    double somar(1:double n1, 2:double n2),
    double subtrair(1:double n1, 2:double n2)
}
```

A definição declara o nome do serviço, os métodos disponíveis e seus parâmetros. Os identificadores `1` e `2` identificam os campos dos argumentos na definição Thrift. A ordem em que os métodos aparecem no arquivo não altera a finalidade matemática de cada operação.

O arquivo `CalculadoraService.java` fornecido indica que o código foi gerado pelo compilador Apache Thrift 0.10.0. Caso a definição `.thrift` seja alterada, o código Java gerado deve ser atualizado de forma compatível com essa definição.

## Operações disponíveis

O cliente permite selecionar uma das quatro operações:

| Operação | Método remoto | Exemplo |
|---|---|---|
| Soma | `somar(n1, n2)` | `3 + 2 = 5` |
| Subtração | `subtrair(n1, n2)` | `3 − 2 = 1` |
| Multiplicação | `multiplicar(n1, n2)` | `3 × 2 = 6` |
| Divisão | `dividir(n1, n2)` | `6 ÷ 2 = 3` |

## Como funciona a comunicação

Quando o usuário clica em **Calcular**, o método `calcular()` executa as seguintes etapas:

1. Lê o endereço do servidor informado no campo **Host** e a porta configurada.
2. Cria um socket Thrift com `TSocket(host, porta)`.
3. Abre a conexão TCP com `transport.open()`.
4. Cria o protocolo de comunicação com `TBinaryProtocol`.
5. Instancia `CalculadoraService.Client`, que permite chamar os métodos remotos.
6. Lê os dois números e identifica a operação selecionada.
7. Invoca o método correspondente no servidor.
8. Exibe o resultado no campo **Resultado**.
9. Fecha o transporte após a chamada.

Representação simplificada do fluxo:

```text
Usuário
   |
   v
Interface gráfica (Swing)
   |
   v
Cliente Apache Thrift
   |
   | TCP + TBinaryProtocol
   v
Servidor Calculadora
   |
   v
Executa a operação matemática
   |
   v
Retorna o resultado ao cliente
   |
   v
Interface exibe o resultado
```

## Pré-requisitos

Antes de executar o cliente, verifique se você possui:

- JDK compatível com Java 8 ou configuração de compilação equivalente.
- Uma IDE Java, como Apache NetBeans, Eclipse ou IntelliJ IDEA.
- Maven instalado ou suporte Maven disponível na IDE.
- Dependência `org.apache.thrift:libthrift:0.10.0` resolvida pelo Maven.
- A classe `CalculadoraService.java` gerada para o serviço.
- O servidor da calculadora executando e acessível pela rede.

O projeto utiliza Maven para declarar as dependências. A execução pode ser feita pela IDE, após a resolução das dependências e a conferência da classe principal.

## Como executar

### Abrir e executar pela IDE

1. Abra ou importe o projeto como projeto Maven na IDE.
2. Aguarde a IDE resolver a dependência `libthrift`.
3. Verifique se a classe `CalculadoraService` e os demais arquivos gerados estão presentes.
4. Inicie o **servidor da calculadora** antes de iniciar o cliente.
5. Execute `Principal.java`.
6. Na janela da calculadora, informe o host e a porta do servidor.
7. Digite os dois números, selecione a operação e clique em **Calcular**.
8. Confira o resultado apresentado na interface.

## Configuração do servidor

A interface apresenta os seguintes valores iniciais:

- **Host:** `localhost`
- **Porta:** `9090`

Use `localhost` quando o cliente e o servidor estiverem na mesma máquina.

Se o servidor estiver em outro computador, substitua `localhost` pelo endereço IP ou nome de rede desse computador. Confirme também que:

- O servidor está iniciado.
- A porta configurada no cliente corresponde à porta em que o servidor está escutando.
- A rede permite a comunicação entre as máquinas.
- O firewall não está bloqueando a conexão.

## Tratamento de erros e limitações

Na implementação atual, exceções Thrift são capturadas e exibidas no console por meio de `printStackTrace()`. Para uma aplicação mais robusta, recomenda-se apresentar mensagens claras na interface e garantir o fechamento do transporte em um bloco `finally` ou com `try-with-resources`, conforme o suporte da biblioteca utilizada.

Outros pontos a considerar:

- A porta é convertida para inteiro; um valor inválido pode causar erro antes da tentativa de conexão.
- Os números são convertidos para `double`; entradas não numéricas podem gerar `NumberFormatException`.
- A divisão por zero não é validada na interface atual. Recomenda-se verificar o segundo número antes de enviar uma divisão.
- Se o servidor estiver indisponível, o cliente não conseguirá realizar a operação.
- O exemplo foi desenvolvido para fins didáticos e não inclui autenticação nem criptografia da comunicação.

## Conceitos importantes

- **Cliente:** aplicação que solicita um serviço.
- **Servidor:** aplicação que recebe as solicitações, executa as operações e devolve respostas.
- **Apache Thrift:** framework que facilita a comunicação entre aplicações por meio de serviços e tipos de dados definidos.
- **Socket:** ponto de comunicação entre aplicações em uma rede.
- **Protocolo:** define como as mensagens são codificadas e interpretadas.
- **Chamada remota:** permite invocar um método disponibilizado por outra aplicação como parte de um fluxo de programação.

## Conclusão

O cliente gráfico da calculadora demonstra como uma aplicação Java pode consumir um serviço remoto usando Apache Thrift. A interface Swing coleta os dados e a operação desejada, enquanto o cliente Thrift envia a solicitação ao servidor e recebe o resultado. Assim, a lógica de cálculo fica no servidor e a interação com o usuário fica no cliente.
