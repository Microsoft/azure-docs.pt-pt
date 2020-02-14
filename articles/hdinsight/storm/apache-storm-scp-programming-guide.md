---
title: Guia de programação do SCP.NET para Storm no Azure HDInsight
description: Saiba como usar o SCP.NET para criar. Topologias Storm baseadas em rede para uso com o Storm em execução no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186867"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Guia de programação do SCP para Apache Storm no Azure HDInsight

O SCP é uma plataforma para a construção de aplicações de processamento de dados em tempo real, fiáveis, consistentes e de alto desempenho. É construído em cima da [Tempestade Apache,](https://storm.incubator.apache.org/)que é um sistema de processamento de fluxo projetado por comunidades de software de código aberto. Nathan Marz criou a Tempestade. Foi publicado como fonte aberta pelo Twitter. A tempestade utiliza [o Apache ZooKeeper,](https://zookeeper.apache.org/)que é outro projeto Apache que permite uma coordenação distribuída altamente fiável e gestão do Estado.

O projeto SCP tem deportado não só storm on Windows, mas também extensões adicionadas ao projeto e personalização para o ambiente Windows. As extensões incluem a experiência de desenvolvimento .NET e as bibliotecas .NET. A personalização inclui a implementação baseada no Windows.

Com as extensões e personalização, você não precisa de bifurcar os projetos de software de código aberto. Você pode usar ambientes derivados que são construídos em cima da tempestade.

## <a name="processing-model"></a>Modelo de processamento

Os dados no SCP são modelados como fluxos contínuos de tuplas. Tipicamente, os tuples:

1. Flui para uma fila.
1. São recolhidos e transformados pela lógica empresarial alojada dentro de uma topologia storm.
1. Ou têm a sua saída canalizada como tuples para outro sistema SCP ou estão comprometidas em lojas como sistemas de ficheiros distribuídos e bases de dados como o SQL Server.

![Um diagrama de uma fila alimentando dados para o processamento, que por sua vez alimenta uma loja de dados](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Em Storm, uma topologia de aplicação define um gráfico de cálculo. Cada nó numa topologia contém lógica de processamento. As ligações entre nós indicam fluxo de dados.

Os nódosos que injetam dados de entrada na topologia são _chamados bicos_. Pode usá-los para sequenciar os dados. Os dados de entrada podem vir de uma fonte como registos de ficheiros, uma base de dados transacional ou um contador de desempenho do sistema.

Os nós que têm fluxos de dados de entrada e de saída são _chamados parafusos_. Fazem a filtragem de dados, seleções e agregação.

A SCP apoia os melhores esforços, pelo menos uma vez, e exatamente uma vez o processamento de dados.

Numa aplicação de processamento de fluxo distribuída, podem ocorrer erros durante o processamento de dados. Tais erros incluem uma falha de rede, uma falha na máquina ou um erro no seu código. Pelo menos uma vez o tratamento garante que todos os dados são processados pelo menos uma vez, reproduzindo automaticamente os mesmos dados quando um erro acontece.

Pelo menos uma vez o processamento é simples e fiável, e combina com muitas aplicações. Mas quando uma aplicação requer uma contagem exata, pelo menos uma vez o processamento é insuficiente porque os mesmos dados podem ser repetidos na topologia da aplicação. Nesse caso, exatamente uma vez o processamento garante que o resultado está correto mesmo quando os dados são reproduzidos e processados várias vezes.

O SCP permite que os desenvolvedores .NET criem aplicações de processamento de dados em tempo real enquanto utilizam uma Máquina Virtual Java (JVM) com storm. Um JVM e .NET comunicam através de tomadas locais tCP. Cada bico/parafuso é um par de processos .NET/Java, onde a lógica do utilizador funciona num processo .NET como plug-in.

Para construir uma aplicação de processamento de dados em cima do SCP, siga estes passos:

1. Desenhe e implemente bicos para retirar dados das filas.
1. Desenhe e implemente parafusos que processem os dados de entrada e guarde-os para lojas externas como uma base de dados.
1. Desenhe a topologia, em seguida, submeta-a e executá-la.

A topologia define os vértices e os dados que fluem entre eles. O SCP pega numa especificação de topologia e implanta-a num aglomerado de tempestades, onde cada vértice funciona num nó lógico. O programador de tarefas storm cuida da falha e escala.

Este artigo usa alguns exemplos simples para passar por como construir aplicações de processamento de dados com SCP.

## <a name="scp-plug-in-interface"></a>Interface plug-in SCP

Os plug-ins SCP são aplicações autónomas. Podem funcionar dentro do Estúdio Visual durante o desenvolvimento e ser ligados ao oleoduto Storm após a implantação da produção.

Escrever um plug-in SCP é o mesmo que escrever qualquer outra aplicação de consola windows. A plataforma SCP.NET declara algumas interfaces para bico/parafuso. O seu código plug-in implementa estas interfaces. O principal objetivo deste design é deixá-lo focar-se na sua lógica de negócio, deixando a plataforma SCP.NET lidar com outras coisas.

O seu código plug-in implementa uma das seguintes interfaces. Qual interface depende se a topologia é transacional ou não transacional e se o componente é um bico ou um parafuso.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchbolt**

### <a name="iscpplugin"></a>ISCPPlugin

**IsCPPlugin** é a interface comum para muitos plug-ins. Atualmente, é uma interface de boneco.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**IsCPSpout** é a interface para um bico não transacional.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Quando o **NextTuple** C# for chamado, o seu código pode emitir um ou mais tuples. Se não houver nada a emitir, esse método deverá retornar sem emitir nada.

Os métodos **NextTuple**, **Ack**e **Fail** são todos chamados C# num laço apertado num único fio de um processo. Quando não houver tuples para emitir, tenha o **NextTuple** a dormir por um curto período de tempo como 10 milissegundos. Este sono ajuda a evitar desperdiçar a disponibilidade do CPU.

Os métodos **Ack** e **Fail** são chamados apenas quando um ficheiro de especificação permite o mecanismo de reconhecimento. O parâmetro *seqId* identifica a tuple que é reconhecida ou falhou. Se o reconhecimento for ativado numa topologia não transacional, a seguinte função **Emit** deve ser utilizada num bico:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Se uma topologia não transacional não apoiar o reconhecimento, **Ack** e **Fail** podem ser deixados como funções vazias.

O parâmetro de entrada *parms* nestas funções especifica um dicionário vazio e é reservado para uso futuro.

### <a name="iscpbolt"></a>ISCPBolt

**O ISCPBolt** é a interface para um parafuso não transacional.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Quando uma nova tuple está disponível, a função **Executar** é chamada para processá-la.

### <a name="iscptxspout"></a>ISCPTxSpout

**IsCPTxSpout** é a interface para um bico transacional.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Tal como os seus homólogos não transacionais, **NextTx,** **Ack**e **Fail** são todos chamados num loop apertado num único fio de um C# processo. Quando não houver tuples para emitir, tenha **nextTx** a dormir por um curto período de tempo como 10 milissegundos. Este sono ajuda a evitar desperdiçar a disponibilidade do CPU.

Quando a **NextTx** é chamada para iniciar uma nova transação, o parâmetro de saída *seqId* identifica a transação. A transação também é usada em **Ack** e **Fail**. O seu método **NextTx** pode emitir dados para o lado de Java. Os dados são armazenados em ZooKeeper para dar suporte à reprodução. Como o ZooKeeper tem capacidade limitada, o seu código deve emitir apenas metadados e não dados em massa num bico de transações.

Como a Storm repete automaticamente uma transação falhada, o **Fail** normalmente não será chamado. Mas se o SCP puder verificar os metadados emitidos por um bico de transação, pode ligar para **o Fail** quando os metadados forem inválidos.

O parâmetro de entrada *parms* nestas funções especifica um dicionário vazio e é reservado para uso futuro.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** é a interface para um parafuso transacional.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

O método **Executar** é chamado quando uma nova tuple chega ao parafuso. O método **FinishBatch** é chamado quando esta transação termina. O parâmetro de entrada *parms* está reservado para uso futuro.

Para uma topologia transacional, **stormTxAttempt** é uma classe importante. Tem dois membros: **TxId** e **TryId**. O membro **TxId** identifica uma transação específica. Uma transação pode ser tentada várias vezes se falhar e for reproduzida.

SCP.NET cria um novo objeto **ISCPBatchBolt** para processar cada objeto **StormTxAttempt,** tal como o que a Tempestade faz em Java. O objetivo deste projeto é apoiar o processamento paralelo de transações. Após a conclusão de uma tentativa de transação, o objeto **ISCPBatchBolt** correspondente é destruído e o lixo recolhido.

## <a name="object-model"></a>Modelo de objeto

O SCP.NET também fornece um conjunto simples de objetos-chave para os desenvolvedores programarem. Os objetos são **Context,** **StateStore**e **SCPRuntime.** São discutidos nesta secção.

### <a name="context"></a>Contexto

O objeto **Context** proporciona um ambiente de funcionamento para uma aplicação. Cada instância **ISCPPlugin** de **ISCPSpout,** **ISCPBolt,** **ISCPTxSpout,** ou **ISCPBatchBolt** tem uma instância de **contexto** correspondente. A funcionalidade fornecida pelo **Context** está dividida nestas duas partes:

* A parte estática, que C# está disponível em todo o processo
* A parte dinâmica, que está disponível apenas para a instância específica de **Contexto**

### <a name="static-part"></a>Parte estática

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

O objeto **Logger** é fornecido para fins de exploração madeireira.

O objeto **pluginType** indica o tipo C# de plug-in do processo. Se o processo for executado em modo de teste local sem Java, o tipo plug-in é **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

A propriedade **Config** obtém parâmetros de configuração C# do lado java, que os passa quando um plug-in é inicializado. Os parâmetros **Config** são divididos em duas partes: **stormConf** e **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

A parte **stormConf** é parâmetros definidos pela Tempestade, e a parte **pluginConf** é parâmetros definidos pela SCP. Segue-se um exemplo:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

O tipo **TopologyContext** obtém o contexto de topologia. É mais útil para múltiplos componentes paralelos. Segue-se um exemplo:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Parte dinâmica

As seguintes interfaces são pertinentes para uma determinada instância de **Contexto,** que é criada pela plataforma SCP.NET e transmitida para o seu código:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Para um bico não transacional que apoie o reconhecimento, é fornecido o seguinte método:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Um parafuso não transacional que suporte o reconhecimento deve ligar explicitamente para **Ack** ou **Fail** com a tuple que recebeu. Ao emitir uma nova tuple, o parafuso também deve especificar as âncoras do tuple. São fornecidos os seguintes métodos:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

O objeto **StateStore** fornece serviços de metadados, geração de sequênciamo-monotónica e coordenação sem espera. Você pode construir abstrações de conmoeda distribuídade nível mais alto na **StateStore**. Estas abstrações incluem fechaduras distribuídas, filas distribuídas, barreiras e serviços de transação.

As aplicações SCP podem usar o objeto **do Estado** para serializar informações no [Apache ZooKeeper](https://zookeeper.apache.org/). Esta capacidade é especialmente valiosa para uma topologia transacional. Se um bico transacional parar de responder e reiniciar, o **Estado** pode recuperar as informações necessárias do ZooKeeper e reiniciar o gasoduto.

O objeto **stateStore** tem estes principais métodos:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

O objeto **do Estado** tem estes métodos principais:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Quando o **modo simples** está definido como **verdadeiro,** o método **'Cometer'** elimina o ZNode correspondente em ZooKeeper. Caso contrário, o método elimina o ZNode atual e adiciona um novo nó no\_PATH comprometido.

### <a name="scpruntime"></a>SCPRuntime

A classe **SCPRuntime** fornece os seguintes dois métodos:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

O método **Inicializar** inicializa o ambiente de execução do SCP. Neste método, C# o processo liga-se ao lado de Java para obter parâmetros de configuração e contexto de topologia.

O método **LaunchPlugin** inicia o ciclo de processamento de mensagens. Neste ciclo, C# o plug-in recebe mensagens do lado de Java. Estas mensagens incluem tuples e sinais de controlo. O plug-in processa então as mensagens, talvez ligando para o método de interface fornecido pelo seu código.

O parâmetro de entrada para **LaunchPlugin** é um delegado. O método pode devolver um objeto que implementa a interface **ISCPSpout,** **ISCPBolt,** **ISCPTxSpout**ou **ISCPBatchBolt.**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Para o **ISCPBatchBolt,** pode obter um objeto **StormTxAttempt** do parâmetro *parms* e usá-lo para avaliar se a tentativa é uma tentativa repetida. O cheque para uma tentativa de repetição é muitas vezes feito no parafuso de compromisso. O exemplo do HelloWorldTx mais tarde neste artigo demonstra este cheque.

Os plug-ins SCP podem normalmente ser executados em dois modos: modo de teste local e modo regular.

#### <a name="local-test-mode"></a>Modo de teste local

Neste modo, os plug-ins SCP no seu C# código funcionam dentro do Estúdio Visual durante a fase de desenvolvimento. Pode utilizar a interface **ILocalContext** neste modo. A interface fornece métodos para serializar os tuples emitidos para ficheiros locais e lê-los de volta para RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Modo regular

Neste modo, o processo Storm Java executa os plug-ins SCP. Aqui está um exemplo:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Linguagem de especificação de topologia

A Especificação de Topologia SCP é uma linguagem específica do domínio (DSL) para descrever e configurar topoologias SCP. É baseado no [Clojure DSL da tempestade](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) e é estendido pela SCP.

Pode submeter especificações de topologia diretamente a um aglomerado de tempestades para execução através do comando **runSpec.**

SCP.NET adicionou as seguintes funções para definir topoologias transacionais:

| Nova função | Parâmetros | Descrição |
| --- | --- | --- |
| **tx-topolopy** |*topologia-nome*<br />*spout-mapa*<br />*bolt-map* |Define uma topologia transacional com o nome topologia, mapa de definição de bicos e mapa de definição de parafusos. |
| **scp-tx-bico** |*nome executivo*<br />*args*<br />*campos* |Define um bico transacional. A função executa a aplicação especificada pelo *nome executivo* e utiliza *args*.<br /><br />O parâmetro dos *campos* especifica os campos de saída para o bico. |
| **scp-tx-batch-bolt** |*nome executivo*<br />*args*<br />*campos* |Define um parafuso de lote transacional. A função executa a aplicação especificada pelo *nome executivo* e utiliza *args.*<br /><br />O parâmetro dos *campos* especifica os campos de saída para o parafuso. |
| **scp-tx-commit-bolt** |*nome executivo*<br />*args*<br />*campos* |Define um parafuso de compromisso transacional. A função executa a aplicação especificada pelo *nome executivo* e utiliza *args*.<br /><br />O parâmetro dos *campos* especifica os campos de saída para o parafuso. |
| **nontx-topologia** |*topologia-nome*<br />*spout-mapa*<br />*bolt-map* |Define uma topologia não transacional com o nome topologia, mapa de definição de bicos e mapa de definição de parafusos. |
| **scp-spout** |*nome executivo*<br />*args*<br />*campos*<br />*parameters* |Define um bico não transacional. A função executa a aplicação especificada pelo *nome executivo* e utiliza *args*.<br /><br />O parâmetro dos *campos* especifica os campos de saída para o bico.<br /><br />O parâmetro dos *parâmetros* é opcional. Utilize-o para especificar parâmetros como "nontransactional.ack.enabled". |
| **scp-bolt** |*nome executivo*<br />*args*<br />*campos*<br />*parameters* |Define um parafuso não transacional. A função executa a aplicação especificada pelo *nome executivo* e utiliza *args*.<br /><br />O parâmetro dos *campos* especifica os campos de saída para o parafuso<br /><br />O parâmetro dos *parâmetros* é opcional. Utilize-o para especificar parâmetros como "nontransactional.ack.enabled". |

SCP.NET define as seguintes palavras-chave:

| Palavra-chave | Descrição |
| --- | --- |
| **:nome** |O nome de topologia |
| **:topologia** |A topologia utilizando as funções na tabela anterior e funções incorporadas |
| **:p** |A dica do paralelismo para cada bico ou parafuso |
| **:config** |Se configurar parâmetros ou atualizar os existentes |
| **:esquema** |O esquema do riacho |

SCP.NET também define estes parâmetros frequentemente utilizados:

| Parâmetro | Descrição |
| --- | --- |
| "plugin.name" |O nome do ficheiro C# .exe do plug-in |
| "plugin. Args" |Os argumentos plug-in |
| "output. Schema" |O esquema de saída |
| "transacional. ACK. Enabled" |Se o reconhecimento é permitido para uma topologia não transacional |

O comando **runSpec** é implantado juntamente com os pedaços. Aqui está o uso do comando:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

O parâmetro *de recurso-dir* é opcional. Especifique-o quando C# pretender ligar uma aplicação. O diretório especificado contém a aplicação, dependências e configurações.

O parâmetro *de classe* também é opcional. Especifica o caminho de classe Java se o ficheiro de especificação contiver um bico ou parafuso Java.

## <a name="miscellaneous-features"></a>Características diversas

### <a name="input-and-output-schema-declarations"></a>Declarações de input e de saída

Os C# seus processos podem emitir tuples. Para isso, a plataforma serializa tuples em **objetos byte[]** e transfere os objetos para o lado java. A tempestade transfere estas tuples para os alvos.

Nos componentes a C# jusante, os processos recebem tuples de volta do lado de Java e convertem-nos para os tipos originais da plataforma. Todas estas operações estão escondidas pela plataforma.

Para apoiar a serialização e a desserialização, o seu código precisa de declarar o esquema da entrada e saída. O esquema é definido como um dicionário. O ID do fluxo é a chave do dicionário. O valor-chave são os tipos das colunas. Um componente pode declarar vários fluxos.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

A seguinte função é adicionada a um objeto de **contexto:**

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Os desenvolvedores devem assegurar que os tuples emitidos obedeçam ao esquema definido para um fluxo. Caso contrário, o sistema lançará uma exceção de tempo de funcionação.

### <a name="multistream-support"></a>Suporte multistream

O SCP permite que o seu código emita ou receba de vários fluxos distintos ao mesmo tempo. O objeto **context** reflete este suporte como parâmetro de ID de fluxo opcional do método **Emit.**

Foram adicionados dois métodos no objeto SCP.NET **Contexto.** Emitem uma ou mais tuples para riachos específicos. O parâmetro *streamId* é uma corda. O seu valor deve C# ser o mesmo tanto no código como na especificação de definição de topologia.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Emitir para um fluxo inexistente causa exceções ao tempo de execução.

### <a name="fields-grouping"></a>Agrupamento de campos

O agrupamento de campos embutidos em Storm não funciona corretamente em SCP.NET. Do lado do representante de Java, o tipo de dados de todos os campos é, na **verdade, byte[]** . O agrupamento de campos usa o código de hash do **objeto byte** para fazer o agrupamento. O código hash é o endereço deste objeto na RAM. Assim, o agrupamento será errado para objetos multibyteques que partilham o mesmo conteúdo, mas não o mesmo endereço.

SCP.NET adiciona um método de agrupamento personalizado, e utiliza o conteúdo do objeto **byte[]** para fazer o agrupamento. Num ficheiro de especificações, a sintaxe parece-se com este exemplo:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

No ficheiro de especificações anterior:

* `scp-field-group` especifica que o agrupamento é um agrupamento de campo personalizado implementado pela SCP.
* `:tx` ou `:non-tx` especifica se a topologia é transacional. Você precisa desta informação porque o índice inicial é diferente entre topoologias transacionais e não transacionais.
* `[0,1]` especifica um conjunto de iDs de campo de hash que começam com zero.

### <a name="hybrid-topology"></a>Topologia híbrida

O código da tempestade nativa está escrito em Java. SCP.NET melhorou a Tempestade C# para te deixar escrever código para lidar com a tua lógica de negócio. Mas SCP.NET também suporta topologias híbridas, que contêm não só C# bicos/parafusos, mas também bicos/parafusos java.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Especificar java spout/bolt em um ficheiro de especificação

Pode utilizar **scp-spout** e **scp-bolt** num ficheiro de especificação para especificar bicos e parafusos java. Segue-se um exemplo:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Aqui `microsoft.scp.example.HybridTopology.Generator` é o nome da classe java.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Especifique o caminho de classe Java num comando runSpec

Se quiser submeter topologia que contenha bicos ou parafusos java, compile-os primeiro para produzir ficheiros JAR. Em seguida, especifique o caminho de classe java que contém os ficheiros JAR quando submeter topologia. Segue-se um exemplo:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Aqui, `examples\HybridTopology\java\target\` é a pasta que contém o ficheiro Java spout/bolt JAR.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialização e desserialização entre Java eC#

Um componente SCP inclui o C# lado java e o lado. Para interagir com bicos/parafusos java nativos, a serialização e a C# desserialização devem ocorrer entre o lado java e o lado, como ilustrado no gráfico seguinte:

![Diagrama do componente Java enviado para componente SCP, que depois envia para um componente java diferente](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serialização no lado de Java e C# desserialização ao lado

Em primeiro lugar, fornecer a implementação padrão para C# a serialização no lado de Java e desserialização no lado.

Especifique o método de serialização do lado java num ficheiro de especificação.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Especifique o C# método de C# desserialização do lado no seu código.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Se o tipo de dados não for muito complexo, esta implementação padrão deve lidar com a maioria dos casos. Aqui estão os casos em que pode ligar a sua própria implementação:

* O seu tipo de dados é demasiado complexo para a implementação padrão.
* O desempenho da sua implementação padrão não satisfaz os seus requisitos.

A interface de serialização no lado de Java é definida como:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

A interface de desserialização no C# lado é definida como:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serialização no C# lado e desserialização no lado de Java

Especifique o C# método de C# serialização do lado no seu código.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Especifique o método de desserialização do lado java num ficheiro de especificação.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Aqui, `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` é o nome do desserializer, e `"microsoft.scp.example.HybridTopology.Person"` é a classe alvo a que os dados são desserializados.

Também pode ligar a sua C# própria implementação de um serializador e um desserializer Java.

Este código é a C# interface para o serializador:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Este código é a interface para o desserializador Java:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Modo anfitrião SCP

Neste modo, pode compilar o seu código como DLL e utilizar o SCPHost.exe, conforme fornecido pela SCP para submeter uma topologia. Um ficheiro de especificação parece com este código:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Aqui, `"plugin.name"` é especificado como `"SCPHost.exe"`, que é fornecido pelo SCP SDK. SCPHost.exe aceita três parâmetros na seguinte ordem:

1. O nome DLL, que é `"HelloWorld.dll"` neste exemplo.
1. O nome da classe, que é `"Scp.App.HelloWorld.Generator"` neste exemplo.
1. O nome de um método estático público, que pode ser invocado para obter uma instância de **ISCPPlugin**.

No modo anfitrião, compile o seu código como DLL para invocação pela plataforma SCP. Como a plataforma pode então obter o controlo total de toda a lógica de processamento, recomendamos que submeta topologia no modo anfitrião sCP. Fazê-lo simplifica a experiência de desenvolvimento. Também lhe traz mais flexibilidade e melhor compatibilidade para lançamentos posteriores.

## <a name="scp-programming-examples"></a>Exemplos de programação do SCP

### <a name="helloworld"></a>HelloWorld

O exemplo simples do HelloWorld mostra um gosto de SCP.NET. Usa uma topologia não transacional com um bico chamado **gerador** e dois parafusos **chamados splitter** e **counter**. O bico do **gerador** gera aleatoriamente frases e emite estas frases para **dividir**. O parafuso **splitter** divide as frases em palavras e emite estas palavras para o **contra-parafuso.** O **contra-parafuso** usa um dicionário para registar a ocorrência de cada palavra.

Este exemplo tem dois ficheiros de especificações: HelloWorld.spec e HelloWorld\_EnableAck.spec. O C# código pode descobrir se o reconhecimento é permitido obtendo o objeto `pluginConf` do lado de Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Se o reconhecimento for ativado no bico, um dicionário caches os tuples que não foram reconhecidos. Se `Fail` for chamado, a tuple falhada é repetida.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

O exemplo seguinte do HelloWorldTx demonstra como implementar topologia transacional. O exemplo tem um bico chamado **gerador**, um parafuso de lote chamado **contagem parcial**, e um parafuso de compromisso chamado **contagem-soma**. O exemplo também tem três ficheiros de texto existentes: DataSource0.txt, DataSource1.txt e DataSource2.txt.

Em cada transação, o **spout** do gerador seleciona aleatoriamente dois ficheiros dos três ficheiros existentes e emite os dois nomes de ficheiros para o parafuso **de contagem parcial.** O parafuso **de contagem parcial:**

1. Obtém um nome de arquivo da túnica recebida.
1. Abre o ficheiro correspondente.
1. Conta o número de palavras no ficheiro.
1. Emite a contagem de palavras para o parafuso **de contagem de soma.**

O parafuso **de contagem resume** a contagem total.

Para conseguir exatamente uma vez semântica, o parafuso de **contagem-soma** precisa de avaliar se é uma transação reproduzida. Neste exemplo, tem a seguinte variável membro estática:

```csharp
public static long lastCommittedTxId = -1; 
```

Quando uma instância **ISCPBatchBolt** é criada, obtém o valor do objeto `txAttempt` a partir de parâmetros de entrada.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

Quando `FinishBatch` é chamada, `lastCommittedTxId` é atualizado se não for uma transação reproduzida.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Esta topologia contém um bico C# java e um parafuso. Utiliza a implementação padrão de serialização e desserialização fornecida pela plataforma SCP. Consulte o ficheiro HybridTopology.spec nos exemplos\\pasta HybridTopology para obter os detalhes do ficheiro de especificação. Consulte também SubmitTopology.bat para saber como especificar o caminho de classe Java.

### <a name="scphostdemo"></a>SCPHostDemo

Este exemplo é, na essência, o mesmo que o HelloWorld. A única diferença é que o seu código é compilado como Um DLL e a topologia é submetida utilizando SCPHost.exe. Consulte a secção de modo de acolhimento SCP para obter uma explicação mais detalhada.

## <a name="next-steps"></a>Passos seguintes

Por exemplo, das topoologias apache storm criadas usando SCP, consulte os seguintes artigos:

* [Desenvolva C# topoologias para Apache Storm no HDInsight usando o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Eventos de processo saqueados de Hubs de Eventos Azure com Tempestade Apache no HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Processar dados de sensores de veículos a partir de Centros de Eventos usando apache storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrair, transformar e carregar (ETL) dos Hubs de Eventos Azure para Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
