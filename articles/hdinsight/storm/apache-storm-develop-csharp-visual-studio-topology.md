---
title: Topoologias apache storm com Visual Studio e C# - Azure HDInsight
description: Aprenda a criar topoologias de tempestade em C#. Crie uma topologia de contagem de palavras no Estúdio Visual utilizando as ferramentas Hadoop para O Estúdio Visual.
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/31/2019
ms.openlocfilehash: 1903c2faab865152d1f3666f3c9dadd745058b56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612296"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Desenvolva topoologias C# para Apache Storm usando as ferramentas data lake para estúdio visual

Aprenda a criar uma topologia de tempestade C# Apache utilizando as ferramentas Azure Data Lake (Apache Hadoop) para o Estúdio Visual. Este documento percorre o processo de criação de um projeto storm no Visual Studio, testando-o localmente, e implantando-o para uma tempestade Apache no cluster Azure HDInsight.

Você também aprende a criar topoologias híbridas que usam componentes C# e Java.

As topoologias C# utilizam .NET 4.5 e usam mono para funcionar no cluster HDInsight. Para obter informações sobre potenciais incompatibilidades, consulte a [compatibilidade de Mono](https://www.mono-project.com/docs/about-mono/compatibility/). Para utilizar uma topologia C#, `Microsoft.SCP.Net.SDK` tem de atualizar o pacote NuGet utilizado pelo seu projeto para a versão 0.10.0.6 ou posterior. A versão do pacote também tem de corresponder à versão principal do Storm instalada no HDInsight.

| Versão HDInsight | Versão Apache Storm | versão SCP.NET | Versão Padrão Mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>Pré-requisito

Um aglomerado de tempestade Apache no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** for **Cluster type**.

## <a name="install-visual-studio"></a>Instalar o Visual Studio

Pode desenvolver topoologias C# com SCP.NET utilizando [o Visual Studio.](https://visualstudio.microsoft.com/downloads/) As instruções aqui utilizam o Visual Studio 2019, mas também pode suster versões anteriores do Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instale ferramentas data lake para estúdio visual

Para instalar ferramentas data lake para estúdio visual, siga os passos em [Get começou a usar ferramentas data lake para estúdio visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Instalar Java

Quando submete uma topologia storm do Visual Studio, SCP.NET gera um ficheiro zip que contém a topologia e dependências. Java é usado para criar estes ficheiros zip, porque usa um formato mais compatível com clusters baseados em Linux.

1. Instale o Java Developer Kit (JDK) 7 ou mais tarde no seu ambiente de desenvolvimento. Pode sacá-lo da [Oracle.](https://openjdk.java.net/) Também pode utilizar [outras distribuições java.](/java/azure/jdk/)

2. Desloque a `JAVA_HOME` variável ambiental para o diretório que contém Java.

3. Desloque a `PATH` `%JAVA_HOME%\bin` variável ambiental para incluir o diretório.

Pode construir e executar a seguinte aplicação de consola C# para verificar se a Java e o JDK estão corretamente instalados:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Modelos de tempestade apache

As ferramentas data lake para o Estúdio Visual fornecem os seguintes modelos:

| Tipo de projeto | Demonstra |
| --- | --- |
| Aplicação de tempestade |Um projeto de topologia de tempestade vazio. |
| Amostra de escritor Storm Azure SQL |Como escrever na Base de Dados Azure SQL. |
| Tempestade Azure Cosmos DB Reader Sample |Como ler de Azure Cosmos DB. |
| Tempestade Azure Cosmos DB Writer Sample |Como escrever para Azure Cosmos DB. |
| Amostra de leitor storm eventhub |Como ler a partir de Azure Event Hubs. |
| Amostra de escritor storm eventhub |Como escrever para o Azure Event Hubs. |
| Amostra de leitor de base de tempestade |Como ler a partir de HBase em clusters HDInsight. |
| Amostra de escritor de base de tempestade |Como escrever para HBase em clusters HDInsight. |
| Amostra híbrida da tempestade |Como utilizar um componente Java. |
| Amostra de tempestade |Uma topologia básica da contagem de palavras. |

> [!WARNING]  
> Nem todos os modelos funcionam com hDInsight baseado em Linux. Os pacotes NuGet utilizados pelos modelos podem não ser compatíveis com Mono. Para identificar potenciais problemas, consulte a [compatibilidade de Mono](https://www.mono-project.com/docs/about-mono/compatibility/) e utilize o [.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

Nos passos deste documento, você usa o tipo básico de projeto de aplicação de tempestade para criar uma topologia.

### <a name="apache-hbase-templates"></a>Modelos Apache HBase

O leitor hbase e os modelos de escritor usam o HBase REST API, e não o HBase Java API, para comunicar com uma Base HNo no cluster HDInsight.

### <a name="eventhub-templates"></a>Modelos eventHub

> [!IMPORTANT]  
> O componente de bico do EventHub baseado em Java incluído com o modelo do Leitor EventHub pode não funcionar com storm na versão 3.5 ou posterior do HDInsight. Uma versão atualizada deste componente está disponível no [GitHub.](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib)

Para um exemplo de topologia que utiliza este componente e trabalha com storm on HDInsight 3.5, consulte [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Criar uma topologia C#

Para criar um projeto de topologia C# no Estúdio Visual:

1. Abra o Visual Studio.

1. Na janela **Iniciar,** selecione **Criar um novo projeto**.

1. Na **Janela Criar uma nova** janela de projeto, percorra e escolha a **Aplicação tempestade,** em seguida, selecione **Next**.

1. Na **configuração** da sua nova janela de projeto, insira um nome de **Projeto** do *WordCount,* vá ou crie um caminho de diretório de **Localização** para o projeto e, em seguida, selecione **Criar**.

    ![Aplicação de tempestade, Configure a sua nova caixa de diálogo de projeto, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

Depois de ter criado o projeto, deve ter os seguintes ficheiros:

* *Program.cs*: A definição de topologia para o seu projeto. Uma topologia padrão que consiste num bico e um parafuso é criada por padrão.

* *Spout.cs*: Um bico de exemplo que emite números aleatórios.

* *Bolt.cs*: Um parafuso de exemplo que mantém uma contagem de números emitidos pelo bico.

Ao criar o projeto, o NuGet descarrega o mais recente [pacote de SCP.NET.](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)

### <a name="implement-the-spout"></a>Implementar o bico

Em seguida, adicione o código para o bico, que é usado para ler dados numa topologia de uma fonte externa. Este bico emite aleatoriamente uma frase na topologia.

1. Abre *Spout.cs.* Os componentes principais para um bico são:

   * `NextTuple`: Chamado por Tempestade quando o bico é permitido emitir novos tuples.

   * `Ack`(apenas topologia transacional): Manuseia os agradecimentos iniciados por outros componentes da topologia para tuples enviados do bico. Reconhecer uma tuple permite ao bico saber que foi processado com sucesso por componentes a jusante.

   * `Fail`(apenas topologia transacional): Lida com tuples que estão a processar outros componentes na topologia. A implementação de um `Fail` método permite-lhe reemitir a túnica para que possa ser processada novamente.

2. Substitua o `Spout` conteúdo da classe pelo seguinte texto:

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Implementar os parafusos

Agora crie dois parafusos storm neste exemplo:

1. Elimine o ficheiro *Bolt.cs* existente do projeto.

2. No **Solution Explorer,** clique no projeto e selecione **Adicionar** > **Novo item**. Da lista, selecione **Storm Bolt**, e *introduza Splitter.cs* como o nome. No código do novo ficheiro, altere `WordCount`o nome do espaço de nome para . Em seguida, repita este processo para criar um segundo parafuso chamado *Counter.cs*.

   * *Splitter.cs*: Implementa um parafuso que divide as frases em palavras individuais e emite um novo fluxo de palavras.

   * *Counter.cs:* Implementa um parafuso que conta cada palavra, e emite um novo fluxo de palavras e a contagem para cada palavra.

     > [!NOTE]  
     > Estes parafusos lêem e escrevem para streams, mas também podem usar um parafuso para comunicar com fontes como uma base de dados ou serviço.

3. Aberto *Splitter.cs.* Tem apenas um método `Execute`por defeito: . O `Execute` método é chamado quando o parafuso recebe uma túnica para processamento. Aqui, você pode ler e processar tuples de entrada, e emitir tuples de saída.

4. Substitua o `Splitter` conteúdo da classe pelo seguinte código:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Abra *Counter.cs*e substitua o conteúdo da classe pelo seguinte código:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Defina a topologia

Bicos e parafusos são dispostos num gráfico, que define como os dados fluem entre componentes. Para esta topologia, o gráfico é o seguinte:

![Diagrama de arranjo de componentes de bico e parafuso, topologia da tempestade](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

O bico emite frases que são distribuídas em instâncias do parafuso Splitter. O parafuso Splitter quebra as frases em palavras, que são distribuídas para o contra-parafuso.

Como a contra-instância detém a contagem de palavras localmente, você deve ter certeza de que palavras específicas fluem para a mesma instância contra parafuso. Cada instância acompanha palavras específicas. Como o parafuso Splitter não mantém nenhum estado, realmente não importa qual o caso do divisor recebe qual sentença.

Abrir *Program.cs.* O método `GetTopologyBuilder`importante é que é usado para definir a topologia que é submetida à Tempestade. Substitua o `GetTopologyBuilder` conteúdo do seguinte código para implementar a topologia descrita anteriormente:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Submeter a topologia

Está agora pronto para submeter a topologia ao seu cluster HDInsight.

1. Navegue para **ver** > o**Server Explorer**.

1. Clique no **Azure,** selecione **Connect to Microsoft Azure Subscription...**, e complete o processo de início de sessão.

1. No **Solution Explorer,** clique no projeto e escolha **Submeter-se à tempestade no HDInsight**.

1. Na caixa de diálogo De submissão de **Topologia,** sob a lista de drop-down do **Storm Cluster,** escolha a sua tempestade no cluster HDInsight e, em seguida, selecione **Submeter**. Pode verificar se a submissão foi bem sucedida visualizando o painel **de saída.**

    Quando a topologia tiver sido submetida com sucesso, a janela de visualização de previsão de previsão de **tempestades** para o cluster deve aparecer. Escolha a topologia **wordCount** da lista para visualizar informações sobre a topologia em execução.

    ![Janela de vista de topologia de tempestade, cluster HDInsight, Estúdio Visual](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > Também pode ver **as Topologs** storm do **Server Explorer.** Expanda **o Azure** > **HDInsight,** clique à direita numa tempestade no cluster HDInsight e, em seguida, escolha **ver as topoologias**da tempestade .

    Para ver informações sobre os componentes da topologia, selecione um componente no diagrama.

1. Na secção Resumo de **Topologia,** selecione **Matar** para parar a topologia.

    > [!NOTE]  
    > As topoologias da tempestade continuam a funcionar até serem desativadas, ou o cluster é apagado.

## <a name="transactional-topology"></a>Topologia transacional

A topologia anterior não é transacional. Os componentes da topologia não implementam a funcionalidade para reproduzir mensagens. Para um exemplo de uma topologia transacional, crie um projeto e selecione **Storm Sample** como o tipo de projeto.

As topoologias transaccionales implementam o seguinte para suportar a repetição de dados:

* **Caching metadados**: O bico deve armazenar metadados sobre os dados emitidos, para que os dados possam ser recuperados e emitidos novamente se ocorrer uma falha. Como os dados emitidos pela amostra são pequenos, os dados brutos de cada tuple são armazenados num dicionário para repetição.

* **Ack:** Cada parafuso na topologia pode ligar `this.ctx.Ack(tuple)` para reconhecer que processou com sucesso uma tuple. Quando todos os parafusos reconheceram `Ack` a tuplia, o método do bico é invocado. O `Ack` método permite que o bico remova os dados que foram emcachepara repetição.

* **Falha**: Cada `this.ctx.Fail(tuple)` parafuso pode ligar para indicar que o processamento falhou para uma tufurete. A falha propaga-se `Fail` ao método do bico, onde a tuple pode ser repetida utilizando metadados em cache.

* **ID de sequência**: Ao emitir uma tuple, pode especificar-se um ID de sequência único. Este valor identifica o tuple`Ack` `Fail`para o processamento de reprodução ( e ) processamento. Por exemplo, o bico no projeto **Storm Sample** utiliza a seguinte chamada de método ao emitir dados:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Este código emite uma tuple que contém uma frase para o `lastSeqId`fluxo padrão, com o valor de identificação da sequência contido em . Para este `lastSeqId` exemplo, é incrementado para cada tuple emitido.

Como demonstrado no projeto **Storm Sample,** se um componente é transacional pode ser definido no tempo de execução, com base na configuração.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia híbrida com C# e Java

Também pode usar ferramentas data lake para o Estúdio Visual para criar topoologias híbridas, onde alguns componentes são C# e outros são Java.

Para um exemplo de uma topologia híbrida, crie um projeto e selecione **Storm Hybrid Sample**. Este tipo de amostra demonstra os seguintes conceitos:

* **Java spout** e **C#** bolt `HybridTopology_javaSpout_csharpBolt` : Definido na aula.

  Uma versão transacional é `HybridTopologyTx_javaSpout_csharpBolt` definida na classe.

* **C# bico** e **java bolt**: `HybridTopology_csharpSpout_javaBolt` Definido na aula.

  Uma versão transacional é `HybridTopologyTx_csharpSpout_javaBolt` definida na classe.

  > [!NOTE]  
  > Esta versão também demonstra como usar o código Clojure a partir de um ficheiro de texto como componente Java.

Para mudar a topologia que é usada quando `[Active(true)]` o projeto é submetido, desloque a declaração para a topologia que pretende utilizar, antes de a submeter ao cluster.

> [!NOTE]  
> Todos os ficheiros Java que são necessários são fornecidos como parte deste projeto na pasta *JavaDependency.*

Considere o seguinte quando estiver a criar e submeter uma topologia híbrida:

* Utilize `JavaComponentConstructor` para criar uma instância da classe Java para um bico ou parafuso.

* Utilize `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` para serializar dados dentro ou fora dos componentes java de objetos Java para JSON.

* Ao submeter a topologia ao servidor, deve utilizar a opção de **configurações adicionais** para especificar os **caminhos**do Ficheiro Java . O caminho especificado deve ser o diretório que tem os ficheiros JAR que contêm as suas aulas de Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET versão 0.9.4.203 introduz uma nova classe e método especificamente para trabalhar com o bico do Event Hub (um bico java que lê a partir de Event Hubs). Quando criar uma topologia que usa um bico do Hub de Evento (por exemplo, utilizando o modelo de amostra de leitor storm **EventHub),** use as seguintes APIs:

* `EventHubSpoutConfig`classe: Cria um objeto que contém a configuração para o componente de bico.

* `TopologyBuilder.SetEventHubSpout`método: Adiciona o componente de bico do Centro de Eventos à topologia.

> [!NOTE]  
> Deve ainda utilizar `CustomizedInteropJSONSerializer` os dados para serializar os dados produzidos pelo bico.

## <a name="use-configurationmanager"></a>Utilizar O Gestor de Configuração

Não utilize o **Configurador** para recuperar valores de configuração a partir de componentes de parafusos e bicos. Fazê-lo pode causar uma exceção de ponteiro nulo. Em vez disso, passe a configuração para o seu projeto para a topologia storm como um par chave e valor no contexto de topologia. Cada componente que se baseie nos valores de configuração deve recuperá-los do contexto durante a inicialização.

O seguinte código demonstra como recuperar estes valores:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Se utilizar `Get` um método para devolver uma instância do seu componente, deve certificar-se de que passa os `Context` parâmetros e `Dictionary<string, Object>` parâmetros ao construtor. O exemplo seguinte `Get` é um método básico que passa corretamente estes valores:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Como atualizar SCP.NET

Lançamentos recentes de SCP.NET upgrade de pacote de suporte através do NuGet. Quando uma nova atualização estiver disponível, recebe uma notificação de upgrade. Para verificar manualmente se há uma atualização, siga estes passos:

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet**.

2. Do gestor do pacote, selecione **Atualizações**. Se estiver disponível uma atualização para o pacote de suporte SCP.NET, está listado. Selecione **'Atualizar** para a embalagem' e, em seguida, na caixa de diálogo **'Preview Changes',** selecione **OK** para o instalar.

> [!IMPORTANT]  
> Se o seu projeto foi criado com uma versão anterior de SCP.NET que não utilizassem o NuGet, tem de executar os seguintes passos para atualizar para uma versão mais recente:
>
> 1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet**.
> 2. Utilizando o campo **de pesquisa,** procure `Microsoft.SCP.Net.SDK` e adicione, em seguida, ao projeto.

## <a name="troubleshoot-common-issues-with-topologies"></a>Problemas de resolução de problemas com topoologias

### <a name="null-pointer-exceptions"></a>Exceções de ponteiros nulos

Quando estiver a utilizar uma topologia C# com um cluster HDInsight baseado em Linux, os componentes de parafusos e bicos que utilizam o **ConfiguraçãoManager** para ler as definições de configuração no tempo de execução podem retornar exceções ao ponteiro nulo.

A configuração para o seu projeto é passada para a topologia da tempestade como um par chave e valor no contexto de topologia. Pode ser recuperado do objeto do dicionário que é passado para os seus componentes quando são inicializados.

Para mais informações, consulte a secção [De Utilização Configuração Manager](#use-configurationmanager) deste documento.

### <a name="systemtypeloadexception"></a>Sistema.TypeLoadException

Quando estiver a usar uma topologia C# com um cluster HDInsight baseado em Linux, pode deparar-se com o seguinte erro:

`System.TypeLoadException: Failure has occurred while loading a type.`

Este erro ocorre quando se utiliza um binário que não é compatível com a versão de .NET que Mono suporta.

Para os clusters HDInsight baseados em Linux, certifique-se de que o seu projeto utiliza binários compilados para .NET 4.5.

### <a name="test-a-topology-locally"></a>Teste uma topologia localmente

Embora seja fácil implementar uma topologia para um cluster, em alguns casos, você pode precisar testar uma topologia localmente. Use os seguintes passos para executar e testar o exemplo de topologia neste artigo localmente no seu ambiente de desenvolvimento.

> [!WARNING]  
> Os testes locais só funcionam para topoologias básicas, só de C#. Não é possível utilizar testes locais para topologias híbridas ou topoologias que utilizem vários fluxos.

1. No **Solution Explorer,** clique no projeto e selecione **Propriedades**. Nas propriedades do projeto. Em seguida, altere o **tipo de saída** para **aplicação**da consola .

   ![Aplicação hDInsight Storm, propriedades do projeto, tipo de saída](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Lembre-se de mudar o **tipo de saída** de volta para a Biblioteca de **Classes** antes de colocar a topologia num cluster.

1. No **Solution Explorer,** clique no projeto e, em seguida, selecione **Adicionar** > **Novo Item**. Selecione **Classe**e introduza *LocalTest.cs* como nome de classe. Finalmente, selecione **Adicionar**.

1. Aberto *LocalTest.cs*, e `using` adicione a seguinte declaração no topo:

    ```csharp
    using Microsoft.SCP;
    ```

1. Utilize o seguinte código como `LocalTest` conteúdo da classe:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Tire um momento para ler os comentários do código. Este código `LocalContext` utiliza para executar os componentes no ambiente de desenvolvimento. Persiste o fluxo de dados entre componentes para ficheiros de texto na unidade local.

1. Abra *Program.cs*e adicione o `Main` seguinte código ao método:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

1. Guarde as alterações e, em seguida, selecione **F5** ou escolha **Debug** > **Start Debugging** para iniciar o projeto. Deve aparecer uma janela de consola e registar o estado à medida que os testes progridem. Quando `Tests finished` aparecer, selecione qualquer tecla para fechar a janela.

1. Utilize o **Windows Explorer** para localizar o diretório que contém o seu projeto. (Por exemplo: *C:\\Utilizadores\\\<your_user_name\\>\\ \\fonte\\repos WordCount WordCount*.) Em seguida, neste diretório, abra *bin*, e, em seguida, selecione *Debug*. Deve ver os ficheiros de texto que foram produzidos quando os testes foram *realizados: frases.txt,* *counter.txt*e *splitter.txt*. Abra cada ficheiro de texto e inspecione os dados.

   > [!NOTE]  
   > Os dados de cordas persistem como uma variedade de valores decimais nestes ficheiros. Por exemplo, `[[97,103,111]]` no ficheiro **splitter.txt** representa a palavra *atrás*.

> [!NOTE]  
> Certifique-se de que o **tipo de Projeto** volta à Biblioteca de **Classes** nas propriedades do projeto antes de ser implantado para uma tempestade no cluster HDInsight.

### <a name="log-information"></a>Informação de registo

Pode registar facilmente informações dos seus `Context.Logger`componentes de topologia utilizando . Por exemplo, o seguinte comando cria uma entrada de registo informacional:

`Context.Logger.Info("Component started");`

As informações registadas podem ser visualizadas a partir do Registo de **Serviço hadoop**, que é encontrado no **Server Explorer**. Expanda a entrada para a sua tempestade no cluster HDInsight e, em seguida, expanda o **Registo de Serviço hadoop**. Por fim, selecione o ficheiro de registo para visualizar.

> [!NOTE]  
> Os registos são armazenados na conta de armazenamento Azure que é utilizada pelo seu cluster. Para ver os registos no Estúdio Visual, tem de iniciar sessão na subscrição Azure que detém a conta de armazenamento.

### <a name="view-error-information"></a>Ver informações de erro

Para ver erros ocorridos numa topologia em execução, utilize os seguintes passos:

1. A partir do **Server Explorer,** clique à direita na tempestade no cluster HDInsight e selecione **Ver Topoologias**de Tempestade .

   Para o **bico** e **parafusos,** a coluna **Last Error** contém informações sobre o último erro.

2. Selecione o **ID de spout** ou **bolt** id para o componente que tem um erro listado. A página de detalhes apresenta informações adicionais de erro na secção **Erros** na parte inferior da página.

3. Para obter mais informações, selecione um **Porto** da secção **executores** da página, para ver o registo dos trabalhadores da Tempestade nos últimos minutos.

### <a name="errors-submitting-topologies"></a>Erros de apresentação de topoologias

Se encontrar erros submetendo uma topologia ao HDInsight, pode encontrar registos para os componentes do lado do servidor que lidam com a submissão de topologia no seu cluster HDInsight. Para descarregar estes registos, utilize o seguinte comando a partir de uma linha de comando:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Substitua o *sshuser* pela conta de utilizador SSH para o cluster. Substitua o nome de *cluster* pelo nome do cluster HDInsight. Para obter mais `scp` `ssh` informações sobre a utilização e com o HDInsight, consulte [Use SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

As submissões podem falhar por múltiplas razões:

* O JDK não está instalado ou não está no caminho.
* As dependências java necessárias não estão incluídas na submissão.
* As dependências são incompatíveis.
* Os nomes de topologia são duplicados.

Se o ficheiro de registo *hdinsight-scpwebapi.out* contiver um `FileNotFoundException`, a exceção pode ser causada pelas seguintes condições:

* O JDK não está no caminho do ambiente de desenvolvimento. Verifique se o JDK está instalado no `%JAVA_HOME%/bin` ambiente de desenvolvimento, e que está no caminho.
* Estás a perder uma dependência de Java. Certifique-se de que está a incluir os ficheiros .jar necessários como parte da submissão.

## <a name="next-steps"></a>Passos seguintes

Para um exemplo de processamento de dados de Event Hubs, consulte [eventos de processo sacar em Hubs de Eventos Azure com Tempestade no HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Para um exemplo de uma topologia C# que divide os dados de fluxo em vários fluxos, consulte [c# exemplo](https://github.com/Blackmist/csharp-storm-example)de tempestade .

Para descobrir mais informações sobre a criação de topoologias C#, consulte [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para mais formas de trabalhar com o HDInsight e mais tempestades nas amostras hDInsight, consulte os seguintes documentos:

**Microsoft SCP.NET**

* [Guia de programação do SCP para a Tempestade Apache em Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm no HDInsight**

* [Implementar e gerir topoologias da tempestade Apache no Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Exemplo Apache Storm topoologias em Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop no HDInsight**

* [O que é Apache Hive e HiveQL no Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Utilizar o MapReduce no Apache Hadoop no HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase no HDInsight**

* [Utilizar o Apache HBase no Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
