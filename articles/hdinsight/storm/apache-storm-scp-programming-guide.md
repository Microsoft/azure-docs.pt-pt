---
title: Guia de programação SCP.NET para o Storm no HDInsight do Azure
description: Saiba como utilizar SCP.NET para criar. Com base em NET topologias do Storm para utilização com o Storm em execução no Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: 848663c509fd3635b33b8e7735feb940da215bfa
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441819"
---
# <a name="scp-programming-guide"></a>Guia de programação do SCP
SCP é uma plataforma para criar em tempo real, fiável e consistente e o aplicativo de processamento de dados de elevado desempenho. Ele é criado por cima de [Apache Storm](https://storm.incubator.apache.org/) – um sistema criado por Comunidades de sistemas operacionais de processamento em fluxo. Storm destina-se por Nathan Marz e foi aberto é obtido ao Twitter. Ela aproveita [Apache ZooKeeper](https://zookeeper.apache.org/), outro projeto do Apache para ativar altamente fiável distribuído de gerenciamento de estado e de coordenação. 

Não apenas o projeto de SCP portado Storm no Windows, mas também o projeto adicionado extensões e personalização para o ecossistema do Windows. As extensões incluem a experiência do desenvolvedor .NET e bibliotecas, a personalização inclui a implantação com base em Windows. 

A extensão e personalização é feita de forma que não precisamos de bifurcar os projetos de sistemas operacionais e que pudéssemos tirar partido ecossistemas derivadas, criadas sobre o Storm.

## <a name="processing-model"></a>Modelo de processamento
Os dados no SCP são modelados como sequências contínuas de cadeias de identificação. Normalmente, as tuplas de fluxo na fila de alguns pela primeira vez, em seguida, pegou e transformada pela lógica de negócios hospedada dentro de uma topologia do Storm, por fim a saída pode ser enviada por pipe como cadeias de identificação para outro sistema de SCP, ou ser comprometida em arquivos, como o sistema de ficheiros distribuído ou bases de dados como o SQL Server.

![Um diagrama de uma fila de alimentação de dados para processamento, que alimenta um arquivo de dados](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

No Storm, uma topologia de aplicação define um gráfico de computação. Cada nó numa topologia contém lógica de processamento e links entre nós indicam o fluxo de dados. Os nós para inserir dados de entrada para a topologia são chamados _spouts_, que podem ser utilizadas para os dados de sequência. Os dados de entrada podem residir nos registos de ficheiros, o banco de dados transacional, o contador de desempenho do sistema etc. Os nós com ambos os fluxos de dados de entrada e de saída são chamados _bolts_, que fazer a filtragem de dados reais e seleções e a agregação.

SCP suporta melhores esforços, pelo-menos-uma vez e exatamente-processamento de dados uma vez. Num aplicativo de processamento de transmissão em fluxo distribuída, vários erros podem ocorrer durante o processamento de dados, como falha de rede, falha de máquina ou erro de código do usuário etc. Processamento pelo-menos-uma vez garante que todos os dados serão processados pelo menos uma vez ao reproduzir automaticamente os mesmos dados, quando ocorre um erro. Processamento pelo-menos-uma vez é simples e fiável e adequada para o bem muitos aplicativos. No entanto, quando um aplicativo exigir contagem exata, processamento pelo-menos-uma vez é insuficiente, uma vez que os mesmos dados potencialmente poderiam ser reproduzidos na topologia de aplicativo. Nesse caso, exatamente-depois do processamento foi criado para certificar-se de que o resultado está correto, mesmo quando os dados podem ser reproduzidos e processados múltiplas vezes.

SCP permite aos programadores de .NET desenvolver aplicativos de processo de dados em tempo real, enquanto beneficia no Java Virtual Machine (JVM) com o Storm nos bastidores. O .NET e o JVM se comunicar por meio de soquetes TCP de locais. Basicamente, cada Spout/Bolt é um par de processo do .net e o Java, onde a lógica do usuário é executada no processo de .net como um plug-in.

Para criar um aplicativo de processamento de dados sobre o SCP, são necessárias várias etapas:

* Crie e implemente os Spouts para receber dados de fila.
* Criar e implementar os Bolts para processar os dados de entrada e salvar dados em armazenamentos externos, como uma base de dados.
* A topologia de design, em seguida, submeter e executar a topologia. A topologia define vértices e os dados de fluxos entre os vértices. SCP irá demorar a especificação de topologia e implementá-la num cluster do Storm, onde cada vértice é executada num nó lógico. A ativação pós-falha e o dimensionamento irão ser resolvido pelo agendador de tarefas de Storm.

Este documento usa alguns exemplos simples para percorrer a como criar aplicações de processamento de dados com o SCP.

## <a name="scp-plugin-interface"></a>Interface de plug-in do SCP
Plug-ins do SCP (ou aplicações) são EXEs autónomo que ambos podem executar dentro do Visual Studio durante a fase de desenvolvimento e ser conectadas ao Storm pipeline após a implantação em produção. Escrever o plug-in do SCP é da mesma quanto escrever qualquer outros padrão Windows aplicativos de console. Plataforma SCP.NET declara uma interface de spout/bolt e código do plug-in do usuário deve implementar essas interfaces. O objetivo principal desse design é que o usuário possa se concentrar em seus próprios demonstração de Teoremas de negócios e deixar as outras coisas para ser processado pela plataforma SCP.NET.

O código de plug-in do usuário deve implementar uma das interfaces seguinte, depende se a topologia é transacional ou não transacional e se o componente está um spout ou bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin é a interface comum para todos os tipos de plug-ins. Atualmente, é uma interface fictícia.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout é a interface de spout não transacional.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Quando `NextTuple()` é chamado, o C\# código do usuário pode emitir um ou mais cadeias de identificação. Se não houver nada para emitir, esse método deve retornar sem emitir nada. É importante observar que `NextTuple()`, `Ack()`, e `Fail()` são chamados num loop estreito num único thread em C\# processo. Quando não há nenhum tuplas para emitir, é educada ter NextTuple suspensão por um curto período de tempo (por exemplo, 10 milissegundos), portanto, não para o lixo muita CPU.

`Ack()` e `Fail()` são chamados apenas quando o mecanismo de confirmação estiver ativado no ficheiro de especificação. O `seqId` é utilizado para identificar a tupla que é confirmada ou falha. Portanto, se ack estiver ativada numa topologia não transacional, a seguinte função emit deve ser utilizada em Spout:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Se o ack não é suportada numa topologia não transacional, a `Ack()` e `Fail()` pode ser deixado como função vazia.

O `parms` parâmetro de entrada nestas funções é um dicionário vazio, está reservado para utilização futura.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt é a interface para o bolt não transacional.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Quando a nova tupla está disponível, o `Execute()` função é chamada para processá-lo.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout é a interface de spout transacional.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Tal como parte dos mesmos não transacional contra-argumentação, `NextTx()`, `Ack()`, e `Fail()` são chamados num loop estreito num único thread em C\# processo. Quando não há nenhum dado para emitir, é educada ter `NextTx` suspensão por um curto período de tempo (10 milissegundos), portanto, não para o lixo muita CPU.

`NextTx()` é chamado para iniciar uma nova transação, o parâmetro de saída `seqId` é utilizado para identificar a transação, o que também é usada na `Ack()` e `Fail()`. No `NextTx()`, utilizador, pode emitir dados para o lado de Java. Os dados são armazenados no ZooKeeper para oferecer suporte a reprodução. Como a capacidade do ZooKeeper é limitada, o utilizador deve emitir apenas de metadados, não em massa dados em spout transacional.

Storm irá reproduzir uma transação automaticamente se falhar, isso `Fail()` não deve ser chamado no caso de normal. Mas se SCP pode verificar os metadados emitido pelo spout transacional, ele pode chamar `Fail()` quando os metadados são inválidos.

O `parms` parâmetro de entrada nestas funções é um dicionário vazio, está reservado para utilização futura.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt é a interface para o bolt transacional.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` é chamado quando há novas cadeias de identificação que chegam ao bolt. `FinishBatch()` é chamado quando esta transação seja terminada. O `parms` parâmetro de entrada está reservado para utilização futura.

Para a topologia transacional, é um conceito importante – `StormTxAttempt`. Ele tem dois campos, `TxId` e `AttemptId`. `TxId` é utilizado para identificar uma transação específica, e para uma determinada transação, pode haver várias tentativas se a transação falha e é reproduzido. SCP.NET cria um novo objeto de ISCPBatchBolt para processar cada `StormTxAttempt`, tais como o que faz o Storm no Java. O objetivo deste design é suportar o processamento de transações paralelas. Utilizador deve manter isso em mente que, se a tentativa de transação é concluída, o objeto ISCPBatchBolt correspondente é destruído e coletados pelo lixo.

## <a name="object-model"></a>Modelo de objeto
SCP.NET também fornece um conjunto simples de objetos de chave para os desenvolvedores a programar com o. Eles estão **contexto**, **statestore de SMS**, e **SCPRuntime**. Eles serão discutidos na parte restante desta seção.

### <a name="context"></a>Contexto
Contexto fornece um ambiente em execução para a aplicação. Cada instância de ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) tem uma instância de contexto correspondente. A funcionalidade fornecida pelo contexto pode ser dividida em duas partes: (1) a parte estática, o que está disponível no C todo\# processar, (2) a parte dinâmica, que só está disponível para a instância de contexto específica.

### <a name="static-part"></a>Parte estático
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` é fornecido para fins de registo.

`pluginType` é utilizado para indicar o tipo de plug-in de C\# processo. Se o C\# processo é executado no modo de teste local (sem Java), o tipo de plug-in é `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` é fornecido para obter parâmetros de configuração do lado do Java. Os parâmetros são transmitidos do lado do Java quando C\# Plug-in é inicializado. O `Config` parâmetros são divididos em duas partes: `stormConf` e `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` é a parâmetros definidos pelo Storm e `pluginConf` é os parâmetros definidos pelo SCP. Por exemplo:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` é fornecido para obter o contexto de topologia, é mais útil para os componentes com vários paralelismo. Segue-se um exemplo:

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

### <a name="dynamic-part"></a>Parte dinâmico
As seguintes interfaces são pertinentes para uma determinada instância de contexto. A instância de contexto é criada pela plataforma de CP.NET e passada para o código de utilizador:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Para não transacional spout ack de suporte, é fornecido o seguinte método:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Para o bolt não transacional ack de suporte, ele deve explicitamente `Ack()` ou `Fail()` a tupla que recebeu. E quando emitir novas cadeias de identificação, ele também tem de especificar as âncoras da nova tupla. Os métodos seguintes são fornecidos.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` Fornece serviços de metadados, geração de sequência monotónica e coordenação livre de espera. Abstrações de simultaneidade distribuída de nível superior podem ser criadas no `StateStore`, incluindo bloqueios distribuídos, filas distribuídas, as barreiras e serviços de transação.

SCP os aplicativos podem utilizar o `State` objeto para manter algumas informações no [Apache ZooKeeper](https://zookeeper.apache.org/), especialmente para topologia transacional. Assim, se spout transacional falhar e reiniciar, pode obter as informações necessárias do ZooKeeper e reinicie o pipeline.

O `StateStore` objeto tem principalmente estes métodos:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

O `State` objeto tem principalmente estes métodos:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

Para o `Commit()` método, quando simpleMode está definido como true, elimina o ZNode correspondente no ZooKeeper. Caso contrário, elimina o ZNode atual e adicionar um novo nó no CONSOLIDADO\_caminho.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime fornece os seguintes dois métodos:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` é usado para inicializar o ambiente de tempo de execução do SCP. Nesse método, o C\# processo liga ao lado do Java e obtém parâmetros de configuração e o contexto de topologia.

`LaunchPlugin()` é utilizado para iniciar o loop de processamento da mensagem. Nesse loop, o C\# Plug-in recebe mensagens de formulário do lado do Java (incluindo sinais de cadeias de identificação e controlo) e, em seguida, processar as mensagens, talvez chamando o método de interface fornecem pelo código do usuário. O parâmetro de entrada para o método `LaunchPlugin()` é um delegado que pode retornar um objeto que implementam a interface de ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Para ISCPBatchBolt, podemos obter `StormTxAttempt` partir `parms`e utilizá-lo para avaliar se é uma tentativa de repetido. A verificação de uma tentativa de repetição, muitas vezes, é feita no bolt de consolidação, e é demonstrado no `HelloWorldTx` exemplo.

Em termos gerais, os plug-ins do SCP podem ser executadas em dois modos aqui:

1. Modo de teste local: Neste modo, os plug-ins do SCP (o C\# código do usuário) são executados dentro do Visual Studio durante a fase de desenvolvimento. `LocalContext` pode ser utilizado neste modo, o que fornece o método para serializar as tuplas emitidas para ficheiros locais e lê-los novamente para a memória.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Modo normal: Neste modo, os plug-ins do SCP são iniciados pelo processo de java de storm.
   
    Eis um exemplo de iniciar o plug-in do SCP:
   
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

## <a name="topology-specification-language"></a>Linguagem de especificação de topologia
Especificação de topologia do SCP é uma linguagem específica do domínio para descrever e configurar topologias de SCP. Baseia-se no Clojure DSL do Storm (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) e é estendido pelo SCP.

Especificações de topologia, podem ser submetidas diretamente ao cluster do storm para execução através de ***runspec*** comando.

SCP.NET adicionou as seguintes funções para definir topologias transacional:

| **Novas funções** | **Parâmetros** | **Descrição** |
| --- | --- | --- |
| **tx-topolopy** |nome de topologia<br />spout-map<br />bolt-map |Definir uma topologia de transacional com o nome de topologia, &nbsp;spouts mapa de definição e o mapa de definição de bolts |
| **scp-tx-spout** |exec-name<br />args<br />campos |Defina um spout transacional. Ele executa o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***campos*** é os campos de saída para spout |
| **scp-tx-batch-bolt** |exec-name<br />args<br />campos |Defina um Bolt Batch transacional. Ele executa o aplicativo com ***exec-name*** usando ***args.***<br /><br />Os campos é os campos de saída para o bolt. |
| **scp-tx-commit-bolt** |exec-name<br />args<br />campos |Defina um bolt de consolidação transacional. Ele executa o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***campos*** é os campos de saída para o bolt |
| **nontx-topolopy** |nome de topologia<br />spout-map<br />bolt-map |Definir uma topologia não transacional com o nome de topologia,&nbsp; spouts mapa de definição e o mapa de definição de bolts |
| **scp-spout** |exec-name<br />args<br />campos<br />parâmetros |Defina um spout não transacional. Ele executa o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***campos*** é os campos de saída para spout<br /><br />O ***parâmetros*** são opcionais, utilizá-la para especificar alguns parâmetros, como "nontransactional.ack.enabled". |
| **scp-bolt** |exec-name<br />args<br />campos<br />parâmetros |Defina um não transacional Bolt. Ele executa o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***campos*** é os campos de saída para o bolt<br /><br />O ***parâmetros*** são opcionais, utilizá-la para especificar alguns parâmetros, como "nontransactional.ack.enabled". |

SCP.NET tem as seguintes palavras-chave definidas:

| **palavras-chave** | **Descrição** |
| --- | --- |
| **:name** |Definir o nome de topologia |
| **: topologia** |Definir a topologia usando as funções anteriores e criar nas. |
| **:p** |Defina a sugestão de paralelismo para cada spout ou bolt. |
| **:config** |Definir parâmetros de configurar ou atualizar os existentes |
| **:schema** |Defina o esquema de Stream. |

E os parâmetros usados com freqüência:

| **Parâmetro** | **Descrição** |
| --- | --- |
| **"plugin.name"** |nome do ficheiro exe do plug-in c# |
| **"plugin.args"** |argumentos de plug-in |
| **"output.schema"** |Esquema de saída |
| **"nontransactional.ack.enabled"** |Se o ack está ativado para a topologia de não transacional |

O comando runspec é implementado juntamente com os bits, a utilização é parecido com:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

O ***recursos dir*** parâmetro é opcional, terá de especificá-lo quando deseja conectar um C\# aplicação e este diretório contém a aplicação, as dependências e configurações.

O ***classpath*** parâmetro também é opcional. É utilizado para especificar o caminho da classe de Java, se o ficheiro spec contém Java Spout ou Bolt.

## <a name="miscellaneous-features"></a>Diversas funcionalidades
### <a name="input-and-output-schema-declaration"></a>Entrada e a declaração de esquema de saída
Os utilizadores podem emitir as tuplas em C\# processos, a plataforma precisa serializar a cadeia de identificação em byte [], transferir para o lado Java e Storm irá transferir esta cadeia de identificação para os destinos. Enquanto isso em componentes downstream, C\# processos receberá as tuplas de lado java e convertê-lo para os tipos originais pela plataforma, todas essas operações estão ocultos pela plataforma.

Para suportar a serialização e desserialização, o código de utilizador tem de declarar o esquema das entradas e saídas.

O esquema de fluxo de entrada/saída está definido como um dicionário. A chave é o StreamId. O valor é os tipos de colunas. O componente pode ter vários fluxos declarados.

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


Objeto de contexto, temos a seguinte API adicionada:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Os desenvolvedores devem garantir que as tuplas emitidas obedecem o esquema definido para esse fluxo, caso contrário, o sistema lançará uma exceção de tempo de execução.

### <a name="multi-stream-support"></a>Suporte de vários Stream
SCP oferece suporte a código do usuário para emitir ou receber de vários fluxos diferentes ao mesmo tempo. O suporte reflete no objeto de contexto, como o método Emit assume um parâmetro de ID de fluxo opcional.

Foram adicionados dois métodos no objeto de contexto SCP.NET. Elas são usadas para emitir a cadeia de identificação ou cadeias de identificação para especificar StreamId. O StreamId é uma cadeia de caracteres e tem de ser consistente em ambos os C\# e a especificação de definição de topologia.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

A emissão de um fluxo não existente faz com que exceções de tempo de execução.

### <a name="fields-grouping"></a>Agrupamento de campos
O agrupamento de campos incorporados no Storm não está a funcionar corretamente no SCP.NET. No lado do Proxy de Java, todos os tipos de dados de campos são, na verdade, byte [] e os campos de agrupamento usa o código de hash de objeto do byte [] para executar o agrupamento. O código de hash de objeto do byte [] é o endereço desse objeto na memória. Assim, o agrupamento será errado para objetos de [] de dois bytes que partilham o mesmo conteúdo mas não o mesmo endereço.

SCP.NET adiciona um método de agrupamento personalizados, e utiliza o conteúdo do byte [] para fazer o agrupamento. Na **SPEC** ficheiro, a sintaxe é parecida com:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Aqui,

1. "grupo de campos de scp" significa "Agrupamento de campo personalizado implementado pelo SCP".
2. ": tx"ou": não tx" significa que se se trata de topologia transacional. Precisamos estas informações como o índice de início é diferente no tx versus não tx topologias.
3. [0,1] significa um conjunto de hash de Ids de campo, a partir de 0.

### <a name="hybrid-topology"></a>Topologia de híbrida
O Storm nativo é escrito em Java. E SCP.Net melhorou para permitir C\# os desenvolvedores escrevam C\# código para manipular a lógica de negócios. Mas também dá suporte a topologias híbridas, que contém não apenas C\# spouts/bolts, mas o Java Spout/Bolts.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Especificar Java Spout/Bolt num ficheiro de especificação
No ficheiro de especificação, "scp spout" e "bolt do scp" também podem ser utilizados para especificar Java Spouts e Bolts, aqui está um exemplo:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Aqui `microsoft.scp.example.HybridTopology.Generator` é o nome da classe Spout de Java.

### <a name="specify-java-classpath-in-runspec-command"></a>Especifique o caminho da classe de Java no runSpec comando
Se pretende submeter a topologia que contém o Java Spouts ou Bolts, precisa primeiro compilar o Java Spouts ou Bolts e obter os ficheiros Jar. Em seguida, deverá especificar o caminho da classe de java que contém os ficheiros Jar ao submeter a topologia. Segue-se um exemplo:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Aqui **exemplos\\HybridTopology\\java\\destino\\**  é a pasta que contém o ficheiro Jar do Spout/Bolt de Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialização e desserialização entre Java e C\#
Componente de SCP inclui lado Java e C\# lado. Para interagir com nativos Spouts de Java/Bolts, serialização/desserialização devem ser realizada entre lado Java e C\# lado, conforme ilustrado no gráfico seguinte.

![Diagrama de componente de java a enviar para o componente de SCP enviar para o componente de Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serialização no lado Java e a desserialização em C\# lado**
   
   Em primeiro lugar, fornecemos a implementação padrão para serialização no lado Java e a desserialização em C\# lado. O método de serialização no lado Java pode ser especificado no ficheiro SPEC:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   O método de anulação da serialização em C\# lado deve ser especificado no C\# código do usuário:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Esta implementação padrão deve processar a maioria dos casos, fornecido o tipo de dados não é demasiado complexo. Em determinados casos, porque o tipo de dados de utilizador é demasiado complexo ou porque o desempenho da nossa implementação predefinida não cumpre requisitos do usuário, os utilizadores podem Plug-in sua própria implementação.
   
   A interface de serialize no lado java é definida como:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   A interface de deserialize em C\# lado é definido como:
   
   interface pública ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serialização no C\# lado e a desserialização do lado do Java**
   
   O método de serialização em C\# lado deve ser especificado no C\# código do usuário:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   O método de desserialização no lado Java deve ser especificado no ficheiro SPEC:
   
     (scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Aqui o "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" é o nome do desserializador e "microsoft.scp.example.HybridTopology.Person" é que a classe de destino, os dados é desserializada para.
   
   Utilizador também pode usar sua própria implementação de C\# serializador e o desserializador de Java. Esse código é a interface para C\# serializador:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Esse código é a interface para desserializador Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Modo de anfitrião do SCP
Neste modo, utilizador pode compilar seus códigos para a DLL e utilize SCPHost.exe fornecida pelo SCP para submeter a topologia. O ficheiro spec é semelhante a este código:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Aqui, `plugin.name` é especificado como `SCPHost.exe` fornecida pelo SDK do SCP. SCPHost.exe aceita três parâmetros:

1. A primeira é o nome da DLL, que é `"HelloWorld.dll"` neste exemplo.
2. A segunda é o nome da classe, que é `"Scp.App.HelloWorld.Generator"` neste exemplo.
3. O terceiro é o nome de um método público estático, que pode ser invocado para obter uma instância de ISCPPlugin.

No modo de anfitrião, o código do usuário é compilado como DLL e é invocado pela plataforma do SCP. Então, plataforma de SCP pode obter o controlo total sobre a lógica de processamento inteiro. Por isso, recomendamos que os nossos clientes para submeter a topologia no modo de SCP do anfitrião, uma vez que ele pode simplificar a experiência de desenvolvimento e nos deem mais flexibilidade e melhor compatibilidade com versões anteriores para também a versão posterior.

## <a name="scp-programming-examples"></a>Exemplos de programação do SCP
### <a name="helloworld"></a>HelloWorld
**HelloWorld** é um exemplo simples para mostrar uma amostra das SCP.Net. Utiliza uma topologia não transacional, com uma chamada de spout **generator**e dois bolts chamados **splitter** e **contador**. O spout **generator** aleatoriamente gera frases e emitir essas frases para **splitter**. O bolt **splitter** divide as frases para palavras e emite essas palavras para **contador** bolt. O bolt "contador" utiliza um dicionário para registar o número de ocorrência de cada palavra.

Existem dois ficheiros spec, **HelloWorld.spec** e **HelloWorld\_EnableAck.spec** para este exemplo. Em C\# código, ele pode descobrir se o ack está ativado, obtendo o pluginConf do lado do Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Spout, se ack estiver ativada, um dicionário é utilizado para colocar em cache as tuplas que não terem sido confirmadas. Se Fail() for chamado, a tupla com falha é reproduzida:

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

### <a name="helloworldtx"></a>HelloWorldTx
O **HelloWorldTx** exemplo demonstra como implementar a topologia transacional. Tem um spout chamado **gerador**, chamado de um bolt do batch **parcial-count**, e um bolt de consolidação chamado **soma da contagem**. Também existem três ficheiros txt previamente criada: **DataSource0.txt**, **DataSource1.txt**, e **DataSource2.txt**.

Em cada transação, o spout **gerador** aleatoriamente seleciona dois arquivos de três arquivos previamente criados e emitir os nomes de dois ficheiro para o **contagem de parcial** bolt. O bolt **parcial-count** obtém o ficheiro da tupla recebida, dê um nome, em seguida, abra o ficheiro e contar o número de palavras neste ficheiro e finalmente emitir o número de palavras para o **soma da contagem** bolt. O **soma da contagem** bolt resume a contagem total.

Para alcançar **exatamente uma vez** semântica, o bolt de consolidação **soma da contagem** tem de avaliar se é uma transação repetida. Neste exemplo, ele tem uma variável de membro estático:

    public static long lastCommittedTxId = -1; 

Quando é criada uma instância de ISCPBatchBolt, obtém o `txAttempt` de parâmetros de entrada:

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

Quando `FinishBatch()` é chamado, o `lastCommittedTxId` será atualizada se não for uma transação repetida.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
Esta topologia contém um Spout de Java e um C\# Bolt. Ele usa a implementação de serialização e desserialização de padrão fornecida pela plataforma do SCP. Consulte a **HybridTopology.spec** na **exemplos\\HybridTopology** pasta para os detalhes do arquivo spec, e **SubmitTopology.bat** para como especificar Java caminho da classe.

### <a name="scphostdemo"></a>SCPHostDemo
Neste exemplo é o mesmo que HelloWorld essencialmente. A única diferença é que o código do usuário é compilado como DLL e a topologia é submetida ao utilizar SCPHost.exe. Consulte a seção "Modo de anfitrião do SCP" para explicação mais detalhada.

## <a name="next-steps"></a>Próximos Passos
Para obter exemplos de topologias do Apache Storm criados usando o SCP, consulte os seguintes documentos:

* [Desenvolver topologias c# para Apache Storm no HDInsight com o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Processar dados de sensores de veículos de Hubs de eventos com o Apache Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrair, transformar e carregar (ETL) dos Hubs de eventos do Azure para o Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
