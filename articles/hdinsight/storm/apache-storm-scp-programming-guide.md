---
title: Guia de programação do SCP.NET para Storm no Azure HDInsight
description: Saiba como usar o SCP.NET para criar. Topologias Storm baseadas em rede para uso com o Storm em execução no Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: b7bb26cd35daf67a3337907aded18e3302b19d81
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813879"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Guia de programação do SCP para Apache Storm no Azure HDInsight

O SCP é uma plataforma para criar um aplicativo de processamento de dados em tempo real, confiável, consistente e de alto desempenho. Ele se baseia no [Apache Storm](https://storm.incubator.apache.org/) – um sistema de processamento de fluxo projetado pelas comunidades de OSS. O Storm foi projetado por Nathan Marz e foi aberto com origem pelo Twitter. Ele aproveita [Apache ZooKeeper](https://zookeeper.apache.org/), outro projeto do Apache para habilitar a coordenação distribuída e o gerenciamento de estado altamente confiáveis. 

Não apenas o projeto SCP porta Storm no Windows, mas também o projeto adicionou extensões e personalização para o ecossistema do Windows. As extensões incluem a experiência do desenvolvedor .NET e as bibliotecas, a personalização inclui a implantação baseada no Windows. 

A extensão e a personalização são feitas de forma que não precisamos bifurcar os projetos de OSS e poderíamos aproveitar os ecossistemas derivados criados com base no Storm.

## <a name="processing-model"></a>Modelo de processamento
Os dados no SCP são modelados como fluxos contínuos de tuplas. Normalmente, as tuplas fluem em alguma fila primeiro, depois escolhidas e transformadas pela lógica comercial hospedada em uma topologia Storm, por fim, a saída pode ser canalizada como tuplas para outro sistema SCP ou ser confirmada em lojas como sistema de arquivos distribuído ou bancos de dados como SQL Server.

![Um diagrama de uma fila que alimenta dados para processamento, que alimenta um armazenamento de dados](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

No Storm, uma topologia de aplicativo define um grafo de computação. Cada nó em uma topologia contém a lógica de processamento e os links entre nós indicam o fluxo de dados. Os nós para injetar dados de entrada na topologia são chamados de _esgotamentos_, que podem ser usados para sequenciar os dados. Os dados de entrada podem residir em logs de arquivo, banco de dados transacional, contador de desempenho do sistema, etc. Os nós com fluxos de dados de entrada e saída são chamados de _parafusos_, que fazem a filtragem de dados e as seleções e a agregação reais.

O SCP dá suporte a melhores esforços, processamento de dados pelo menos uma vez e exatamente uma vez. Em um aplicativo de processamento de streaming distribuído, vários erros podem ocorrer durante o processamento de dados, como interrupção de rede, falha de máquina ou erro de código do usuário, etc. O processamento pelo menos uma vez garante que todos os dados serão processados pelo menos uma vez, reproduzindo automaticamente os mesmos dados quando ocorrer um erro. O processamento pelo menos uma vez é simples e confiável e se adapta a muitos aplicativos. No entanto, quando um aplicativo requer contagem exata, o processamento pelo menos uma vez é insuficiente, já que os mesmos dados podem ser reproduzidos na topologia do aplicativo. Nesse caso, o processamento de exatamente uma vez é projetado para garantir que o resultado esteja correto mesmo quando os dados podem ser reproduzidos e processados várias vezes.

O SCP permite que os desenvolvedores do .NET desenvolvam aplicativos de processo de dados em tempo real, aproveitando a JVM (Máquina Virtual Java) com o Storm nos bastidores. O .NET e a JVM se comunicam por meio de soquetes TCP locais. Basicamente, cada Spout/parafuso é um par de processos .NET/Java, onde a lógica do usuário é executada no processo do .NET como um plug-in.

Para criar um aplicativo de processamento de dados na parte superior do SCP, várias etapas são necessárias:

* Projete e implemente os esgotamentos para efetuar pull de dados da fila.
* Projete e implemente os parafusos para processar os dados de entrada e salve os dados em armazenamentos externos, como um Database.
* Crie a topologia e, em seguida, envie e execute a topologia. A topologia define os vértices e os fluxos de dados entre os vértices. O SCP pegará a especificação de topologia e a implantará em um cluster Storm, onde cada vértice será executado em um nó lógico. O failover e o dimensionamento serão resolvidos pelo Agendador de tarefas do Storm.

Este documento usa alguns exemplos simples para percorrer como criar um aplicativo de processamento de dados com o SCP.

## <a name="scp-plugin-interface"></a>Interface do plug-in do SCP
Os plug-ins do SCP (ou aplicativos) são EXEs autônomos que podem ser executados dentro do Visual Studio durante a fase de desenvolvimento e conectados ao pipeline Storm após a implantação em produção. Escrever o plug-in SCP é exatamente o mesmo que escrever outros aplicativos de console padrão do Windows. A plataforma SCP.NET declara alguma interface para Spout/parafuso e o código de plug-in do usuário deve implementar essas interfaces. A principal finalidade desse design é que o usuário possa se concentrar em suas próprias lógicas de negócios e deixar outras coisas a serem tratadas pela plataforma SCP.NET.

O código de plug-in do usuário deve implementar uma das seguintes interfaces, dependendo se a topologia é transacional ou não transacional e se o componente é um Spout ou um parafuso.

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
ISCPSpout é a interface para Spout não transacionais.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Quando `NextTuple()` é chamado, o código\# de usuário do C pode emitir uma ou mais tuplas. Se não houver nada a emitir, esse método deverá retornar sem emitir nada. Deve-se observar que `NextTuple()`, `Ack()`e `Fail()` são todos chamados em um loop rígido em um único thread no processo C\# . Quando não há tuplas a serem emitidas, é educado ter o NextTuple Sleep por um curto período de tempo (como 10 milissegundos) para não desperdiçar muita CPU.

`Ack()`e `Fail()` são chamados somente quando o mecanismo de confirmação está habilitado no arquivo de especificação. O `seqId` é usado para identificar a tupla que é confirmada ou falhou. Portanto, se ACK estiver habilitado em topologia não transacional, a seguinte função de emissão deverá ser usada em Spout:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Se ACK não tiver suporte em topologia não transacional, o e `Ack()` `Fail()` pode ser deixado como uma função vazia.

O `parms` parâmetro de entrada nessas funções é um dicionário vazio, ele é reservado para uso futuro.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt é a interface para um parafuso não transacional.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Quando uma nova tupla está disponível, `Execute()` a função é chamada para processá-la.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout é a interface para Spout transacional.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Assim como sua parte do contador `NextTx()`não transacional, `Ack()`, e `Fail()` são todas chamadas em um loop rígido em um único thread no processo C\# . Quando não há dados a serem emitidos, é educado ter `NextTx` a suspensão por um curto período de tempo (10 milissegundos) para não perder muita CPU.

`NextTx()`é chamado para iniciar uma nova transação, o parâmetro `seqId` out é usado para identificar a transação, que também é usada no e `Fail()`no. `Ack()` No `NextTx()`, o usuário pode emitir dados para o lado do Java. Os dados são armazenados em ZooKeeper para dar suporte à reprodução. Como a capacidade de ZooKeeper é limitada, o usuário só deve emitir metadados, não dados em massa em Spout transacional.

O Storm repetirá uma transação automaticamente se falhar, portanto `Fail()` , não deve ser chamado em caso normal. Mas se o SCP puder verificar os metadados emitidos por Spout transacional, ele poderá `Fail()` chamar quando os metadados forem inválidos.

O `parms` parâmetro de entrada nessas funções é um dicionário vazio, ele é reservado para uso futuro.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt é a interface para o raio transacional.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()`é chamado quando há uma nova tupla chegando ao parafuso. `FinishBatch()`é chamado quando esta transação é encerrada. O `parms` parâmetro de entrada é reservado para uso futuro.

Para topologia transacional, há um conceito importante – `StormTxAttempt`. Ele tem dois campos, `TxId` e `AttemptId`. `TxId`é usado para identificar uma transação específica e, para uma determinada transação, pode haver várias tentativas se a transação falhar e for reproduzida. O scp.NET cria um novo objeto ISCPBatchBolt para processar `StormTxAttempt`cada um, assim como o Storm faz em Java. A finalidade desse design é dar suporte ao processamento de transações paralelas. O usuário deve ter em mente que, se a tentativa da transação for concluída, o objeto ISCPBatchBolt correspondente será destruído e coletado como lixo.

## <a name="object-model"></a>Modelo de objeto
O SCP.NET também fornece um conjunto simples de objetos-chave para os desenvolvedores programarem. Eles são **Context**, **StateStore**e **SCPRuntime**. Eles são discutidos na parte restante desta seção.

### <a name="context"></a>Contexto
O contexto fornece um ambiente em execução para o aplicativo. Cada instância de ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) tem uma instância de contexto correspondente. A funcionalidade fornecida pelo contexto pode ser dividida em duas partes: (1) a parte estática, que está disponível em todo o processo\# C, (2) a parte dinâmica, que está disponível somente para a instância de contexto específica.

### <a name="static-part"></a>Parte estática
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger`é fornecido para fins de log.

`pluginType`é usado para indicar o tipo de plug-in\# do processo C. Se o processo\# C for executado no modo de teste local (sem Java), o tipo de `SCP_NET_LOCAL`plug-in será.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config`é fornecido para obter parâmetros de configuração do lado do Java. Os parâmetros são passados do lado do Java quando\# o plug-in C é inicializado. Os `Config` parâmetros são divididos em duas partes `stormConf` : `pluginConf`e.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf`os parâmetros são definidos pelo Storm `pluginConf` e são os parâmetros definidos pelo SCP. Por exemplo:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext`é fornecido para obter o contexto de topologia, ele é mais útil para componentes com vários paralelismo. Segue-se um exemplo:

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

### <a name="dynamic-part"></a>Parte dinâmica
As interfaces a seguir são pertinentes a uma determinada instância de contexto. A instância de contexto é criada pela plataforma SCP.NET e passada para o código do usuário:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Para a confirmação de suporte de Spout não transacional, o seguinte método é fornecido:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Para a confirmação de suporte a um raio não transacional, `Ack()` ela `Fail()` deve ser explicitamente ou a tupla recebida. E ao emitir uma nova tupla, ela também deve especificar as âncoras da nova tupla. Os métodos a seguir são fornecidos.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore`fornece serviços de metadados, geração de sequência monotônico e coordenação de espera livre. Abstrações de simultaneidade distribuídas de nível superior podem `StateStore`ser criadas em, incluindo bloqueios distribuídos, filas distribuídas, barreiras e serviços de transação.

Os aplicativos SCP podem usar `State` o objeto para manter algumas informações em [Apache ZooKeeper](https://zookeeper.apache.org/), especialmente para a topologia transacional. Fazendo isso, se Spout transacional falhar e reiniciar, ele poderá recuperar as informações necessárias do ZooKeeper e reiniciar o pipeline.

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

Para o `Commit()` método, quando simplemode é definido como true, ele exclui o ZNode correspondente em ZooKeeper. Caso contrário, ele excluirá o ZNode atual e adicionará um novo nó no\_caminho confirmado.

### <a name="scpruntime"></a>SCPRuntime
O SCPRuntime fornece os dois métodos a seguir:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()`é usado para inicializar o ambiente de tempo de execução do SCP. Nesse método, o processo C\# se conecta ao lado do Java e obtém os parâmetros de configuração e o contexto da topologia.

`LaunchPlugin()`é usado para disparar o loop de processamento de mensagens. Nesse loop, o plug-\# in C recebe mensagens do lado do Java (incluindo tuplas e sinais de controle) e, em seguida, processa as mensagens, talvez chamando o método de interface fornecido pelo código do usuário. O parâmetro de entrada para `LaunchPlugin()` o método é um delegado que pode retornar um objeto que implementa a interface ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Para ISCPBatchBolt, podemos obter `StormTxAttempt` de `parms`e usá-lo para avaliar se é uma tentativa reproduzida. A verificação de uma tentativa de reprodução geralmente é feita no parafuso de confirmação e é demonstrada no `HelloWorldTx` exemplo.

Em geral, os plug-ins do SCP podem ser executados em dois modos aqui:

1. Modo de teste local: Nesse modo, os plug-ins do SCP (\# o código do usuário C) são executados dentro do Visual Studio durante a fase de desenvolvimento. `LocalContext`pode ser usado nesse modo, que fornece o método para serializar as tuplas emitidas para arquivos locais e lê-las de volta na memória.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Modo normal: Nesse modo, os plug-ins do SCP são iniciados pelo processo Java do Storm.
   
    Aqui está um exemplo de inicialização do plug-in SCP:
   
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
A especificação de topologia SCP é uma linguagem específica de domínio para descrever e configurar topologias SCP. Ele se baseia na DSL Clojure do Storm (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) e é estendido pelo SCP.

As especificações de topologia podem ser enviadas diretamente para o cluster Storm para execução por meio do comando ***runspec*** .

SCP.NET adicionou as seguintes funções para definir topologias transacionais:

| **Novas funções** | **Parâmetros** | **Descrição** |
| --- | --- | --- |
| **tx-topolopy** |nome da topologia<br />mapa de Spout<br />bolt-map |Definir uma topologia transacional com o nome da topologia &nbsp;, o mapa de definição de esgotamento e o mapa de definição dos parafusos |
| **scp-tx-spout** |nome do arquivo<br />args<br />campo |Defina um Spout transacional. Ele executa o aplicativo com ***exec-Name*** usando ***args***.<br /><br />Os ***campos*** são os campos de saída para Spout |
| **scp-tx-batch-bolt** |nome do arquivo<br />args<br />campo |Defina um parafuso de lote transacional. Ele executa o aplicativo com ***exec-Name*** usando ***args.***<br /><br />Os campos são os campos de saída para o parafuso. |
| **scp-tx-commit-bolt** |nome do arquivo<br />args<br />campo |Defina um parafuso de confirmação transacional. Ele executa o aplicativo com ***exec-Name*** usando ***args***.<br /><br />Os ***campos*** são os campos de saída para o parafuso |
| **nontx-topolopy** |nome da topologia<br />mapa de Spout<br />bolt-map |Definir uma topologia não transacional com o nome da&nbsp; topologia, o mapa de definição de esgotamento e o mapa de definição dos parafusos |
| **scp-spout** |nome do arquivo<br />args<br />campo<br />parameters |Defina um Spout não transacional. Ele executa o aplicativo com ***exec-Name*** usando ***args***.<br /><br />Os ***campos*** são os campos de saída para Spout<br /><br />Os ***parâmetros*** são opcionais, usando-os para especificar alguns parâmetros, como "transacional. ACK. Enabled". |
| **scp-bolt** |nome do arquivo<br />args<br />campo<br />parameters |Defina um parafuso não transacional. Ele executa o aplicativo com ***exec-Name*** usando ***args***.<br /><br />Os ***campos*** são os campos de saída para o parafuso<br /><br />Os ***parâmetros*** são opcionais, usando-os para especificar alguns parâmetros, como "transacional. ACK. Enabled". |

SCP.NET tem as seguintes palavras-chave definidas:

| **palavras-chave** | **Descrição** |
| --- | --- |
| **: nome** |Definir o nome da topologia |
| **: topologia** |Defina a topologia usando as funções anteriores e crie aquelas. |
| **:p** |Defina a dica de paralelismo para cada Spout ou parafuso. |
| **:config** |Definir o parâmetro de configuração ou atualizar os existentes |
| **:schema** |Defina o esquema do fluxo. |

E parâmetros usados com frequência:

| **Parâmetro** | **Descrição** |
| --- | --- |
| **"plugin.name"** |nome do arquivo exe do C# plug-in |
| **"plugin. Args"** |args de plug-in |
| **"output.schema"** |Esquema de saída |
| **"transacional. ACK. Enabled"** |Se ACK está habilitado para topologia não transacional |

O comando runspec é implantado junto com os bits, o uso é semelhante a:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

O parâmetro ***Resource-dir*** é opcional, você precisa especificá-lo quando quiser conectar um aplicativo C\# , e esse diretório contém o aplicativo, as dependências e as configurações.

O parâmetro ***classpath*** também é opcional. Ele é usado para especificar o classpath do Java se o arquivo de especificação contiver Spout ou parafuso do Java.

## <a name="miscellaneous-features"></a>Recursos diversos
### <a name="input-and-output-schema-declaration"></a>Declaração de esquema de entrada e saída
Os usuários podem emitir tuplas em\# processos C, a plataforma precisa serializar a tupla em byte [], transferir para o lado do Java e o Storm transferirá essa tupla para os destinos. Enquanto isso, nos componentes downstream\# , os processos C receberão tuplas de volta do lado do Java e os converterão para os tipos originais por plataforma, todas essas operações serão ocultadas pela plataforma.

Para dar suporte à serialização e desserialização, o código do usuário precisa declarar o esquema das entradas e saídas.

O esquema de fluxo de entrada/saída é definido como um dicionário. A chave é Streamid. O valor é os tipos das colunas. O componente pode ter vários fluxos declarados.

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


No objeto de contexto, temos a seguinte API adicionada:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Os desenvolvedores devem garantir que as tuplas emitidas obedeçam ao esquema definido para esse fluxo, caso contrário, o sistema gerará uma exceção de tempo de execução.

### <a name="multi-stream-support"></a>Suporte a vários fluxos
O SCP dá suporte ao código de usuário para emitir ou receber de vários fluxos distintos ao mesmo tempo. O suporte reflete no objeto de contexto, pois o método de emissão usa um parâmetro de ID de fluxo opcional.

Dois métodos no objeto de contexto SCP.NET foram adicionados. Eles são usados para emitir tupla ou tuplas para especificar Streamid. O streamid é uma cadeia de caracteres e precisa ser consistente em C\# e na especificação de definição de topologia.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

A emissão para um fluxo não existente causa exceções de tempo de execução.

### <a name="fields-grouping"></a>Agrupamento de campos
O agrupamento de campos internos no Storm não está funcionando corretamente no SCP.NET. No lado do proxy Java, todos os tipos de dados de campos são, na verdade, Byte [], e o agrupamento de campos usa o código hash do objeto byte [] para executar o agrupamento. O código hash do objeto byte [] é o endereço deste objeto na memória. Portanto, o agrupamento ficará incorreto para dois objetos byte [] que compartilham o mesmo conteúdo, mas não o mesmo endereço.

SCP.NET adiciona um método de agrupamento personalizado e usa o conteúdo do byte [] para fazer o agrupamento. No arquivo **spec** , a sintaxe é como:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Aqui

1. "SCP-Field-Group" significa "Agrupamento de campo personalizado implementado pelo SCP".
2. ": TX" ou ": não TX" significa que se trata de uma topologia transacional. Precisamos dessas informações, já que o índice inicial é diferente em topologias TX versus não TX.
3. [0, 1] significa um conjunto de hash de IDs de campo, a partir de 0.

### <a name="hybrid-topology"></a>Topologia híbrida
O Storm nativo é escrito em Java. E o scp.net o aprimorou para permitir que\# os desenvolvedores de c\# escrevam código c para lidar com a lógica de negócios. Mas ele também dá suporte a topologias híbridas, que\# contêm não apenas C-esgotamentos/parafusos, mas também Spout/parafusos Java.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Especificar Spout/parafuso do Java no arquivo de especificação
No arquivo de especificação, "SCP-Spout" e "SCP-parafuso" também podem ser usados para especificar esgotamentos e parafusos de Java, aqui está um exemplo:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Aqui `microsoft.scp.example.HybridTopology.Generator` está o nome da classe Java Spout.

### <a name="specify-java-classpath-in-runspec-command"></a>Especifique o classpath Java no comando runSpec
Se você quiser enviar a topologia que contém esgotamentos ou parafusos de Java, primeiro você precisará compilar os limites ou os parafusos do Java e obter os arquivos jar. Em seguida, você deve especificar o classpath Java que contém os arquivos jar ao enviar a topologia. Segue-se um exemplo:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Aqui **estão\\exemplos\\HybridTopology\\destinoJava\\**  é a pasta que contém o arquivo JAR Spout/do Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialização e desserialização entre Java e C\#
O componente SCP inclui lado do Java\# e do lado do C. Para interagir com esgotamentos/limites de Java nativos, a serialização/desserialização deve ser realizada entre o lado do Java e\# a C, conforme ilustrado no grafo a seguir.

![diagrama do componente Java enviando ao componente SCP enviando para o componente Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serialização no lado do Java e desserialização no\# lado do C**
   
   Primeiro, fornecemos a implementação padrão para serialização no lado do Java e desserialização no lado do C\# . O método de serialização no lado do Java pode ser especificado no arquivo de especificação:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   O método de desserialização no\# lado do c deve ser especificado\# no código do usuário C:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Essa implementação padrão deve lidar com a maioria dos casos, desde que o tipo de dados não seja muito complexo. Para determinados casos, porque o tipo de dados do usuário é muito complexo ou porque o desempenho de nossa implementação padrão não atende ao requisito do usuário, os usuários podem fazer o plug-in de sua própria implementação.
   
   A interface Serialize no lado do Java é definida como:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   A interface de desserialização no\# lado do C é definida como:
   
   interface pública ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serialização no lado\# do C e desserialização no lado do Java**
   
   O método de serialização no\# lado do c deve ser especificado\# no código do usuário C:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   O método de desserialização no lado do Java deve ser especificado no arquivo de especificação:
   
     (SCP-Spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Aqui, "Microsoft. scp. Storm. Multilang. CustomizedInteropJSONDeserializer" é o nome do desserializador e "Microsoft. scp. example. HybridTopology. Person" é a classe de destino para a qual os dados são desserializados.
   
   O usuário também pode conectar sua própria implementação do serializador C\# e do desserializador Java. Esse código é a interface para o\# serializador C:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Esse código é a interface para o desserializador Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Modo de host SCP
Nesse modo, o usuário pode compilar seus códigos para a DLL e usar o SCPHost. exe fornecido pelo SCP para enviar a topologia. O arquivo de especificação é semelhante a este código:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Aqui, `plugin.name` é especificado conforme `SCPHost.exe` fornecido pelo SCP SDK. O SCPHost. exe aceita três parâmetros:

1. O primeiro é o nome da dll, que está `"HelloWorld.dll"` neste exemplo.
2. O segundo é o nome da classe, que está `"Scp.App.HelloWorld.Generator"` neste exemplo.
3. O terceiro é o nome de um método estático público, que pode ser invocado para obter uma instância de ISCPPlugin.

No modo de host, o código do usuário é compilado como DLL e é invocado pela plataforma SCP. Portanto, a plataforma SCP pode obter controle total de toda a lógica de processamento. Portanto, recomendamos que nossos clientes enviem topologia no modo de host SCP, já que ele pode simplificar a experiência de desenvolvimento e nos fornecer mais flexibilidade e melhor compatibilidade com versões anteriores para o lançamento posterior também.

## <a name="scp-programming-examples"></a>Exemplos de programação do SCP
### <a name="helloworld"></a>HelloWorld
**HelloWorld** é um exemplo simples para mostrar um gosto de SCP.net. Ele usa uma topologia não transacional, com um **Spout chamado** **gerador**, e dois pinos chamados separador e **contador**. O **gerador** de Spout gera frases aleatoriamente e emite essas sentenças para o **divisor**. O **divisor** de raio divide as frases em palavras e emite essas palavras ao **balcão** . O "contador" de raio usa um dicionário para registrar o número de ocorrência de cada palavra.

Há dois arquivos de especificação, **HelloWorld. spec** e **HelloWorld\_EnableAck. spec** para este exemplo. No código C\# , ele pode descobrir se a confirmação está habilitada obtendo o pluginConf do lado do Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

No Spout, se ACK estiver habilitado, um dicionário será usado para armazenar em cache as tuplas que não foram confirmadas. Se Fail () for chamado, a tupla com falha será reproduzida:

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
O exemplo **HelloWorldTx** demonstra como implementar a topologia transacional. Ele tem um Spout chamado **gerador**, um parafuso de lote chamado **contagem parcial**e um parafuso de confirmação chamado **Count-Sum**. Também há três arquivos txt pré-criados: **DataSource0. txt**, **DataSource1. txt**e **DataSource2. txt**.

Em cada transação, o **gerador** de Spout seleciona aleatoriamente dois arquivos dos três arquivos previamente criados e emite os dois nomes de arquivo para o parafuso de **contagem parcial** . A **contagem parcial** de parafusos Obtém o nome do arquivo da tupla recebida, depois abre o arquivo e conta o número de palavras nesse arquivo e, finalmente, emite o número da palavra para o parafuso **contagem-soma** . O parafuso **contagem-soma** resume a contagem total.

Para atingir a semântica **exatamente uma vez** , a **soma da contagem** de confirmações precisa avaliar se é uma transação reproduzida. Neste exemplo, ele tem uma variável de membro estática:

    public static long lastCommittedTxId = -1; 

Quando uma instância de ISCPBatchBolt é criada, ela obtém `txAttempt` os parâmetros de entrada de:

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

Quando `FinishBatch()` for chamado, o `lastCommittedTxId` será atualizado se não for uma transação reproduzida.

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
Essa topologia contém um Spout Java e um parafuso\# C. Ele usa a implementação padrão de serialização e desserialização fornecida pela plataforma SCP. Consulte a pasta **HybridTopology. spec** em **\\exemplos HybridTopology** para obter os detalhes do arquivo de especificação e **SubmitTopology. bat** para saber como especificar o classpath Java.

### <a name="scphostdemo"></a>SCPHostDemo
Este exemplo é o mesmo que HelloWorld em essência. A única diferença é que o código do usuário é compilado como DLL e a topologia é enviada usando SCPHost. exe. Consulte a seção "modo de host do SCP" para obter uma explicação mais detalhada.

## <a name="next-steps"></a>Próximos Passos
Para obter exemplos de topologias de Apache Storm criadas usando o SCP, consulte os seguintes documentos:

* [Desenvolver C# topologias para Apache Storm no HDInsight usando o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Processar eventos dos hubs de eventos do Azure com o Apache Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Processar dados de sensor de veículo dos hubs de eventos usando Apache Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [ETL (extração, transformação e carregamento) dos hubs de eventos do Azure para o Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
