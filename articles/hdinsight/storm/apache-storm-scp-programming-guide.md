---
title: SCP.NET guia de programação para tempestade em Azure HDInsight
description: Aprenda a usar SCP.NET para criar. Topologias de tempestade baseadas em NET para uso com tempestade em execução em Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: bd52157e2f0e20e9282d944b07f656c08d9e57da
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932633"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Guia de programação SCP para Apache Storm em Azure HDInsight

A SCP é uma plataforma para a construção de aplicações de processamento de dados em tempo real, fiáveis, consistentes e de alto desempenho. É construído em cima da [Tempestade Apache,](https://storm.incubator.apache.org/)que é um sistema de processamento de fluxo projetado por comunidades de software de código aberto. Nathan Marz criou a Tempestade. Foi publicado como fonte aberta pelo Twitter. Storm usa [Apache ZooKeeper](https://zookeeper.apache.org/), que é outro projeto Apache que permite coordenação distribuída altamente fiável e gestão do estado.

O projeto SCP tem portado não só Storm on Windows, mas também extensões adicionadas de projeto e personalização para o ambiente Windows. As extensões incluem a experiência de desenvolvimento .NET e bibliotecas .NET. A personalização inclui a implementação baseada no Windows.

Com as extensões e a personalização, não precisa de recorrer aos projetos de software de código aberto. Você pode usar ambientes derivados que são construídos em cima da tempestade.

## <a name="processing-model"></a>Modelo de processamento

Os dados em SCP são modelados como fluxos contínuos de tuples. Tipicamente, os tuples:

1. Flua para uma fila.
1. São recolhidos e transformados pela lógica de negócios hospedada dentro de uma topologia da tempestade.
1. Ou têm a sua saída canalizada como tuples para outro sistema SCP ou estão comprometidos com lojas como sistemas de ficheiros distribuídos e bases de dados como o SQL Server.

![Um diagrama de dados de alimentação de fila para o processamento, que por sua vez alimenta uma loja de dados](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Em Storm, uma topologia de aplicação define um gráfico de cálculo. Cada nó numa topologia contém lógica de processamento. As ligações entre os nós indicam o fluxo de dados.

Os nós que injetam dados de entrada na topologia são _chamados bicos_. Pode usá-los para sequenciar os dados. Os dados de entrada podem vir de uma fonte como registos de ficheiros, uma base de dados transacional ou um contador de desempenho do sistema.

Os nós que têm fluxos de dados de entrada e de saída são _chamados parafusos_. Fazem a filtragem de dados, seleções e agregação.

A SCP apoia os melhores esforços, pelo menos uma vez, e exatamente uma vez que o processamento de dados.

Numa aplicação distribuída de processamento de streaming, podem ocorrer erros durante o processamento de dados. Tais erros incluem uma falha de rede, uma falha na máquina ou um erro no seu código. Pelo menos uma vez, o processamento garante que todos os dados são tratados pelo menos uma vez, reproduzindo automaticamente os mesmos dados quando um erro ocorre.

Pelo menos uma vez o processamento é simples e fiável, e adequa-se a muitas aplicações. Mas quando uma aplicação requer uma contagem exata, pelo menos uma vez o processamento é insuficiente porque os mesmos dados podem ser reproduzidos na topologia da aplicação. Nesse caso, exatamente uma vez que o processamento garante que o resultado está correto mesmo quando os dados são reproduzidos e processados várias vezes.

O SCP permite que os desenvolvedores .NET criem aplicações de processamento de dados em tempo real enquanto utilizam uma Máquina Virtual Java (JVM) com storm. Um JVM e .NET comunicam através de tomadas locais TCP. Cada bico/parafuso é um par de processos .NET/Java, onde a lógica do utilizador funciona num processo .NET como um plug-in.

Para construir uma aplicação de processamento de dados em cima da SCP, siga estes passos:

1. Conceber e implementar bicos para extrair dados das filas.
1. Conceva e implemente parafusos que processam os dados de entrada e os guardam em lojas externas como uma base de dados.
1. Desenhe a topologia, em seguida, submeta-se e execute-a.

A topologia define os vértices e os dados que fluem entre eles. A SCP pega numa especificação de topologia e implanta-a num aglomerado de tempestades, onde cada vértice funciona num nó lógico. O programador de tarefas storm cuida do failover e do escalonamento.

Este artigo usa alguns exemplos simples para percorrer como construir aplicações de processamento de dados com SCP.

## <a name="scp-plug-in-interface"></a>Interface plug-in SCP

Os plug-ins SCP são aplicações autónomas. Podem correr dentro do Visual Studio durante o desenvolvimento e ser ligados ao oleoduto Storm após a implantação da produção.

Escrever um plug-in SCP é o mesmo que escrever qualquer outra aplicação de consola do Windows. A plataforma SCP.NET declara algumas interfaces para bico/parafuso. O seu código plug-in implementa estas interfaces. O principal objetivo deste design é deixar que você se concentre na sua lógica de negócio, deixando a SCP.NET plataforma lidar com outras coisas.

O seu código plug-in implementa uma das seguintes interfaces. Qual interface depende se a topologia é transacional ou não transacional e se o componente é um bico ou um parafuso.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**O ISCPPlugin** é a interface comum para muitos plug-ins. Atualmente, é uma interface falsa.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** é a interface para um bico não-transaccional.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Quando **o NextTuple** for chamado, o seu código C# pode emitir um ou mais tuples. Se não há nada a emitir, este método deve voltar sem emitir nada.

Os métodos **NextTuple**, **Ack** e **Fail** são todos chamados num loop apertado num único fio de um processo C#. Quando não houver tuples para emitir, ter **NextTuple** durma por um curto período de tempo como 10 milissegundos. Este sono ajuda a evitar desperdiçar a disponibilidade de CPU.

Os métodos **Ack** e **Fail** são chamados apenas quando um ficheiro de especificação permite o mecanismo de reconhecimento. O *parâmetro seqId* identifica o tuple que é reconhecido ou falhou. Se o reconhecimento for ativado numa topologia não transacional, deve ser utilizada a seguinte função **Emit** num bico:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Se uma topologia não transacional não apoiar o reconhecimento, **Ack** e **Fail** podem ser deixados como funções vazias.

O parâmetro de entrada *parms* nestas funções especifica um dicionário vazio e é reservado para uso futuro.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** é a interface para um parafuso não transacional.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Quando um novo tuple está disponível, a função **Executar** é chamada para processá-la.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** é a interface para um bico transacional.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Tal como os seus homólogos não transacionais, **NextTx**, **Ack** e **Fail** são todos chamados num loop apertado num único fio de um processo C#. Quando não houver tuples para emitir, ter **NextTx** dormir por um curto período de tempo como 10 milissegundos. Este sono ajuda a evitar desperdiçar a disponibilidade de CPU.

Quando **o NextTx** é chamado para iniciar uma nova transação, o parâmetro de saída *seqId* identifica a transação. A transação também é utilizada em **Ack** and **Fail**. O seu método **NextTx** pode emitir dados para o lado de Java. Os dados são armazenados no ZooKeeper para suportar a repetição. Como o ZooKeeper tem capacidade limitada, o seu código deve emitir apenas metadados e não dados em massa num bico transacional.

Como a Tempestade repete automaticamente uma transação falhada, **o Fail** normalmente não será chamado. Mas se o SCP puder verificar os metadados emitidos por um bico transacional, pode ligar para **'Fail'** quando os metadados são inválidos.

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

O método **Executar** é chamado quando um novo tuple chega ao parafuso. O método **FinishBatch** é chamado quando esta transação termina. O parâmetro de entrada *parms* é reservado para utilização futura.

Para uma topologia transacional, **stormTxAttempt** é uma classe importante. Tem dois membros: **TxId** e **TryId.** O membro **do TxId** identifica uma transação específica. Uma transação pode ser tentada várias vezes se falhar e for reproduzida.

SCP.NET cria um novo objeto **ISCPBatchBolt** para processar cada objeto **StormTxAttempt,** tal como o que a Tempestade faz em Java. O objetivo deste projeto é apoiar o processamento paralelo de transações. Após a conclusão de uma tentativa de transação, o objeto **ISCPBatchBolt** correspondente é destruído e o lixo recolhido.

## <a name="object-model"></a>Modelo de objeto

SCP.NET também fornece um conjunto simples de objetos chave para os desenvolvedores para programar. Os objetos são **Context,** **StateStore** e **SCPRuntime**. São discutidos nesta secção.

### <a name="context"></a>Contexto

O objeto **Context** proporciona um ambiente de execução a uma aplicação. Cada instância **ISCPPlugin** de **ISCPSpout**, **ISCPBolt,** **ISCPTxSpout,** ou **ISCPBatchBolt** tem uma instância **de contexto** correspondente. A funcionalidade fornecida pelo **Context** divide-se nestas duas partes:

* A parte estática, que está disponível em todo o processo C#
* A parte dinâmica, que está disponível apenas para a instância de **contexto** específico

### <a name="static-part"></a>Parte estática

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

O objeto **Madeireiro** é fornecido para fins de registo.

O objeto **pluginType** indica o tipo de plug-in do processo C#. Se o processo for executado no modo de teste local sem Java, o tipo de plug-in é **SCP_NET_LOCAL**.

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

A propriedade **Config** obtém parâmetros de configuração do lado java, que os passa quando um plug-in C# é inicializado. Os parâmetros **Config** são divididos em duas partes: **tempestadeConf** e **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

A parte **da tempestadeConf** são parâmetros definidos pela Tempestade, e a parte **pluginConf** são parâmetros definidos pela SCP. Eis um exemplo:

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

O tipo **TopologyContext** obtém o contexto de topologia. É mais útil para múltiplos componentes paralelos. Eis um exemplo:

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

As seguintes interfaces são pertinentes a uma determinada instância **de Contexto,** que é criada pela plataforma SCP.NET e passada para o seu código:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Para um bico não transacional que suporte o reconhecimento, é fornecido o seguinte método:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Um parafuso não transacional que suporte o reconhecimento deve explicitamente ligar **para Ack** ou **Fail** com o tuple que recebeu. Ao emitir um novo tuple, o parafuso também deve especificar as âncoras do tuple. São fornecidos os seguintes métodos:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

O objeto **StateStore** fornece serviços de metadados, geração de sequência monônica e coordenação sem espera. Pode construir abstrações de conuscção distribuídas de alto nível na **StateStore.** Estas abstrações incluem fechaduras distribuídas, filas distribuídas, barreiras e serviços de transação.

As aplicações SCP podem usar o objeto **do Estado** para serializar informações em [Apache ZooKeeper](https://zookeeper.apache.org/). Esta capacidade é especialmente valiosa para uma topologia transacional. Se um bico transacional parar de responder e reiniciar, o **Estado** pode recuperar as informações necessárias do ZooKeeper e reiniciar o gasoduto.

O objeto **StateStore** tem estes principais métodos:

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

O objeto **do Estado** tem estes principais métodos:

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

Quando **o SimpleMode** é definido como **verdadeiro,** o método **Commit** elimina o ZNode correspondente no ZooKeeper. Caso contrário, o método elimina o ZNode atual e adiciona um novo nó no \_ CAMINHO COMPROMETIDO.

### <a name="scpruntime"></a>SCPRuntime

A classe **SCPRuntime** fornece os seguintes dois métodos:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

O método **Initialize** inicializa o ambiente de execução SCP. Neste método, o processo C# conecta-se ao lado de Java para obter parâmetros de configuração e contexto de topologia.

O método **LaunchPlugin** inicia o ciclo de processamento de mensagens. Neste ciclo, o plug-in C# recebe mensagens do lado java. Estas mensagens incluem tuples e sinais de controlo. O plug-in processa então as mensagens, talvez ligando para o método de interface fornecido pelo seu código.

O parâmetro de entrada para **LaunchPlugin** é um delegado. O método pode devolver um objeto que implementa a interface **ISCPSpout,** **ISCPBolt,** **ISCPTxSpout** ou **ISCPBatchBolt.**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Para **o ISCPBatchBolt,** pode obter um objeto **StormTxAttempt** do parâmetro *parms* e usá-lo para avaliar se a tentativa é uma tentativa repetida. A verificação de uma tentativa de repetição é muitas vezes feita no parafuso de compromisso. O exemplo HelloWorldTx mais tarde neste artigo demonstra esta verificação.

Os plug-ins SCP podem normalmente funcionar em dois modos: modo de teste local e modo regular.

#### <a name="local-test-mode"></a>Modo de teste local

Neste modo, os plug-ins SCP no seu código C# são executados dentro do Visual Studio durante a fase de desenvolvimento. Pode utilizar a interface **ILocalContext** neste modo. A interface fornece métodos para serializar os tuples emitidos para os ficheiros locais e lê-los de volta para a RAM.

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

## <a name="topology-specification-language"></a>Língua de especificação de topologia

SCP Topology Specification é uma língua específica do domínio (DSL) para descrever e configurar topologias SCP. É baseado no [Clojure DSL da Tempestade](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) e é estendido pela SCP.

Pode submeter especificações de topologia diretamente a um cluster storm para execução através do comando **runSpec.**

SCP.NET adicionou as seguintes funções para definir topologias transacionais:

| Nova função | Parâmetros | Descrição |
| --- | --- | --- |
| **tx-toplopy** |*nome de topologia*<br />*mapa de bicos*<br />*mapa de parafusos* |Define uma topologia transacional com o nome topologia, mapa de definição de bicos e mapa de definição de parafusos. |
| **scp-tx-bico** |*nome executivo*<br />*args*<br />*fields* |Define um bico transacional. A função executa a aplicação especificada pelo *nome executivo* e utiliza *args*.<br /><br />O parâmetro *dos campos* especifica os campos de saída para o bico. |
| **scp-tx-lote-bolt** |*nome executivo*<br />*args*<br />*fields* |Define um parafuso de lote transacional. A função executa a aplicação especificada pelo *nome executivo* e usa *args.*<br /><br />O parâmetro *dos campos* especifica os campos de saída para o parafuso. |
| **scp-tx-commit-bolt** |*nome executivo*<br />*args*<br />*fields* |Define um parafuso de compromisso transacional. A função executa a aplicação especificada pelo *nome executivo* e utiliza *args*.<br /><br />O parâmetro *dos campos* especifica os campos de saída para o parafuso. |
| **nontx-topologia** |*nome de topologia*<br />*mapa de bicos*<br />*mapa de parafusos* |Define uma topologia não transacional com o nome de topologia, mapa de definição de bicos e mapa de definição de parafusos. |
| **scp-bico** |*nome executivo*<br />*args*<br />*fields*<br />*parâmetros* |Define um bico não transacional. A função executa a aplicação especificada pelo *nome executivo* e utiliza *args*.<br /><br />O parâmetro *dos campos* especifica os campos de saída para o bico.<br /><br />O parâmetro *dos parâmetros* é opcional. Utilize-o para especificar parâmetros como "nontransactional.ack.enabled". |
| **scp-bolt** |*nome executivo*<br />*args*<br />*fields*<br />*parâmetros* |Define um parafuso não transacional. A função executa a aplicação especificada pelo *nome executivo* e utiliza *args*.<br /><br />O parâmetro *dos campos* especifica os campos de saída para o parafuso<br /><br />O parâmetro *dos parâmetros* é opcional. Utilize-o para especificar parâmetros como "nontransactional.ack.enabled". |

SCP.NET define as seguintes palavras-chave:

| Palavra-chave | Descrição |
| --- | --- |
| **:nome** |O nome da topologia |
| **:topologia** |A topologia usando as funções na tabela anterior e funções incorporadas |
| **:p** |A dica de paralelismo para cada bico ou parafuso |
| **:config** |Quer configurar parâmetros ou atualizar os existentes |
| **:esquema** |O esquema do riacho |

SCP.NET também define estes parâmetros frequentemente utilizados:

| Parâmetro | Descrição |
| --- | --- |
| "plugin.name" |O .exe nome do ficheiro do plug-in C# |
| "plugin.args" |Os argumentos plug-in |
| "output.schema" |O esquema de saída |
| "nontransactional.ack.enabled" |Se o reconhecimento é habilitado para uma topologia não transacional |

O comando **runSpec** é implantado juntamente com os pedaços. Aqui está o uso do comando:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

O parâmetro *recursos-dir* é opcional. Especifique-o quando pretender ligar uma aplicação C#. O diretório especificado contém a aplicação, dependências e configurações.

O parâmetro *classpath* também é opcional. Especifica o apêndico java se o ficheiro de especificações contiver um bico ou parafuso java.

## <a name="miscellaneous-features"></a>Características diversas

### <a name="input-and-output-schema-declarations"></a>Declarações de esquemas de entrada e saída

Os seus processos C# podem emitir tuples. Para tal, a plataforma serializa os tuples em objetos **byte e** transfere os objetos para o lado de Java. A tempestade transfere estes tuples para os alvos.

Nos componentes a jusante, os processos C# recebem tuples de volta do lado de Java e convertem-nos nos tipos originais da plataforma. Todas estas operações estão escondidas pela plataforma.

Para apoiar a serialização e a deserialização, o seu código precisa de declarar o esquema da entrada e saída. O esquema é definido como um dicionário. A identificação do fluxo é a chave do dicionário. O valor chave são os tipos das colunas. Um componente pode declarar vários fluxos.

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

A seguinte função é adicionada a um objeto **de contexto:**

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Os desenvolvedores devem garantir que os tuples emitidos obedecem ao esquema definido para um fluxo. Caso contrário, o sistema lançará uma exceção ao tempo de execução.

### <a name="multistream-support"></a>Suporte multistream

O SCP permite que o seu código emita ou receba de vários fluxos distintos ao mesmo tempo. O  objeto contextualmente reflete este suporte como o parâmetro de ID de fluxo opcional do método **Emit.**

Foram adicionados dois métodos no objeto **de contexto** SCP.NET. Emitem um ou mais tuples para riachos específicos. O parâmetro *streamId* é uma corda. O seu valor deve ser o mesmo tanto no código C# como na especificação de definição de topologia.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

A emissão a uma corrente inexistente causa exceções no tempo de execução.

### <a name="fields-grouping"></a>Agrupamento de campos

Os campos embutidos agruparem-se em Storm não funcionam corretamente em SCP.NET. Do lado da procuração de Java, o tipo de dados de todos os campos é, na **verdade, byte[]**. O agrupamento de campos utiliza **o** código de haxixe do objeto para fazer o agrupamento. O código de haxixe é o endereço deste objeto na RAM. Assim, o agrupamento será errado para objetos multibyte que partilham o mesmo conteúdo, mas não o mesmo endereço.

SCP.NET adiciona um método de agrupamento personalizado, e utiliza o conteúdo do objeto **byte[]** para fazer o agrupamento. Num ficheiro de especificações, a sintaxe parece este exemplo:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

No ficheiro de especificações anteriores:

* `scp-field-group` especifica que o agrupamento é um agrupamento de campo personalizado implementado pela SCP.
* `:tx` ou `:non-tx` especifica se a topologia é transacional. Precisa desta informação porque o índice inicial é diferente entre as topologias transacionais e não transacionais.
* `[0,1]` especifica um conjunto de haxixe de iDs de campo que começam com zero.

### <a name="hybrid-topology"></a>Topologia híbrida

O código de tempestade nativa está escrito em Java. SCP.NET melhorou a Storm para te deixar escrever código C# para lidar com a tua lógica de negócio. Mas SCP.NET também suporta topologias híbridas, que contêm não só bicos/parafusos C# mas também bicos/parafusos java.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Especificar o bico/parafuso de Java num ficheiro de especificações

Pode utilizar **scp-bico** e **scp-bolt** num ficheiro de especificações para especificar bicos e parafusos java. Eis um exemplo:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Aqui `microsoft.scp.example.HybridTopology.Generator` está o nome da classe de bico de Java.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Especificar o apárp de Java num comando runSpec

Se pretender submeter topologia que contenha bicos ou parafusos java, compile-os primeiro para produzir ficheiros JAR. Em seguida, especifique o classepath java que contém os ficheiros JAR quando submeter a topologia. Eis um exemplo:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Aqui, `examples\HybridTopology\java\target\` é a pasta que contém o ficheiro JAR de bico/parafuso java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialização e deserialização entre Java e C #

Um componente SCP inclui o lado java e o lado C#. Para interagir com bicos/parafusos java nativos, a serialização e a deserialização devem ocorrer entre o lado de Java e o lado C#, conforme ilustrado no gráfico seguinte:

![Diagrama de componente java enviando para o componente SCP, que depois envia para um componente java diferente](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serialização no lado de Java e deserialização no lado C#

Primeiro, forneça a implementação padrão para serialização no lado java e a deserialização no lado C#.

Especifique o método de serialização do lado de Java num ficheiro de especificações.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Especifique o método de desserialização do lado C# no seu código C#.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Se o tipo de dados não for muito complexo, esta implementação padrão deve lidar com a maioria dos casos. Aqui estão os casos em que pode ligar a sua própria implementação:

* O seu tipo de dados é demasiado complexo para a implementação padrão.
* O desempenho da sua implementação padrão não satisfaz os seus requisitos.

A interface de serialização no lado java é definida como:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

A interface de deserialização no lado C# é definida como:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serialização no lado C# e deserialização no lado de Java

Especifique o método de serialização do lado C# no seu código C#.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Especifique o método de dessasserização do lado de Java num ficheiro de especificações.

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

Aqui, `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` é o nome do desercializador, e é a `"microsoft.scp.example.HybridTopology.Person"` classe-alvo a que os dados são desseerializados.

Também pode ligar a sua própria implementação de um serializer C# e um deserializador Java.

Este código é a interface para o serializer C#:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Este código é a interface para o deserializador java:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Modo anfitrião SCP

Neste modo, pode compilar o seu código como DLL e utilizar SCPHost.exe conforme fornecido pela SCP para submeter uma topologia. Um ficheiro de especificações parece este código:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Aqui, `"plugin.name"` é especificado como , que é fornecido pelo `"SCPHost.exe"` SCP SDK. SCPHost.exe aceita três parâmetros na seguinte ordem:

1. O nome DLL, que está `"HelloWorld.dll"` neste exemplo.
1. O nome da classe, que está `"Scp.App.HelloWorld.Generator"` neste exemplo.
1. O nome de um método estático público, que pode ser invocado para obter um exemplo de **ISCPPlugin**.

No modo anfitrião, compile o seu código como um DLL para invocação pela plataforma SCP. Como a plataforma pode então obter o controlo total de toda a lógica de processamento, recomendamos que submeta topologia no modo anfitrião SCP. Fazê-lo simplifica a experiência de desenvolvimento. Também lhe traz mais flexibilidade e melhor compatibilidade para trás para lançamentos posteriores.

## <a name="scp-programming-examples"></a>Exemplos de programação SCP

### <a name="helloworld"></a>OláWorld

O exemplo simples da HelloWorld mostra um gosto de SCP.NET. Usa uma topologia não transacional com um bico chamado **gerador** e dois parafusos **chamados splitter** e **contador.** O bico do **gerador** gera aleatoriamente frases e emite estas frases para **dividir.** O **parafuso splitter** divide as frases em palavras e emite estas palavras para o **contra-parafuso.** O **contador** usa um dicionário para registar a ocorrência de cada palavra.

Este exemplo tem dois ficheiros de especificações: HelloWorld.spec e HelloWorld \_ EnableAck.spec. O código C' pode descobrir se o reconhecimento é ativado obtendo o `pluginConf` objeto do lado de Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Se o reconhecimento for ativado no bico, um dicionário caches os tuples que não foram reconhecidos. Se `Fail` for chamado, a tuple falhada é reproduzida.

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

### <a name="helloworldtx"></a>OláWorldTx

O exemplo seguinte da HelloWorldTx demonstra como implementar topologia transacional. O exemplo tem um bico chamado **gerador**, um parafuso de lote chamado **contagem parcial**, e um parafuso de compromisso chamado **count-sum**. O exemplo também tem três ficheiros de texto existentes: DataSource0.txt, DataSource1.txt e DataSource2.txt.

Em cada transação, o bico do **gerador** seleciona aleatoriamente dois ficheiros dos três ficheiros existentes e emite os dois nomes de ficheiros para o parafuso **de contagem parcial.** O parafuso **de contagem parcial:**

1. Obtém um nome de arquivo da tuple recebida.
1. Abre o ficheiro correspondente.
1. Conta o número de palavras no ficheiro.
1. Emite a contagem de palavras para o parafuso **de contagem-soma.**

O parafuso **de contagem sum-sum** resume a contagem total.

Para conseguir exatamente uma vez semântica, o parafuso **de entrega de soma de contagem** precisa de avaliar se é uma transação repetida. Neste exemplo, tem a seguinte variável de membro estático:

```csharp
public static long lastCommittedTxId = -1; 
```

Quando uma instância **ISCPBatchBolt** é criada, obtém o valor do objeto a `txAttempt` partir de parâmetros de entrada.

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

Quando `FinishBatch` é chamado, `lastCommittedTxId` é atualizado se não for uma transação reproduzida.

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

Esta topologia contém um bico java e um parafuso C#. Utiliza a implementação de serialização e desserialização padrão fornecida pela plataforma SCP. Consulte o ficheiro HybridTopology.spec nos exemplos \\ da pasta HybridTopology para os detalhes do ficheiro de especificações. Consulte também SubmitTopology.bat para saber como especificar o apêssta de Java.

### <a name="scphostdemo"></a>SCPHostDemo

Este exemplo é essencialmente o mesmo que o HelloWorld. A única diferença é que o seu código é compilado como um DLL e a topologia é submetida usando SCPHost.exe. Consulte a secção de modo anfitrião SCP para obter uma explicação mais detalhada.

## <a name="next-steps"></a>Próximos passos

Por exemplo, as topologias da Tempestade Apache criadas com recurso ACP, consulte os seguintes artigos:

* [Desenvolver topologias C# para Apache Storm em HDInsight usando Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Eventos de processo de Azure Event Hubs com Tempestade Apache em HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Processar dados do sensor do veículo a partir de Centros de Eventos usando a Tempestade Apache em HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrair, transformar e carregar (ETL) dos Hubs de Eventos Azure para Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
