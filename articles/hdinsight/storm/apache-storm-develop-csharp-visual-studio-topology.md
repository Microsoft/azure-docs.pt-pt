---
title: Apache Storm toplogies com Visual Studio e C# - Azure HDInsight
description: Aprenda a criar topologias storm em C#. Crie uma topologia de contagem de palavras em Estúdio Visual utilizando as ferramentas Hadoop para Estúdio Visual.
ROBOTS: NOINDEX
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5360057ec2dfbd2b40c6f086f5e910103c544b62
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865456"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Desenvolver topologias C# para a Tempestade Apache utilizando as ferramentas data lake para o Estúdio Visual

Aprenda a criar uma topologia da Tempestade Apache C# utilizando as ferramentas Azure Data Lake (Apache Hadoop) para o Visual Studio. Este documento percorre o processo de criação de um projeto Storm em Visual Studio, testando-o localmente, e implantando-o para uma Tempestade Apache no cluster Azure HDInsight.

Também aprende a criar topologias híbridas que usam componentes C# e Java.

As topologias C# usam .NET 4.5 e utilizam o Mono para funcionar no cluster HDInsight. Para obter informações sobre possíveis incompatibilidades, consulte [Mono compatibilidade.](https://www.mono-project.com/docs/about-mono/compatibility/) Para utilizar uma topologia C#, tem de atualizar o `Microsoft.SCP.Net.SDK` pacote NuGet utilizado pelo seu projeto para a versão 0.10.0.6 ou posterior. A versão do pacote também tem de corresponder à versão principal do Storm instalada no HDInsight.

| Versão HDInsight | Versão Apache Storm | versão SCP.NET | Versão Mono padrão |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>Pré-requisito

Um aglomerado de tempestade Apache em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** for **Cluster type**.

## <a name="install-visual-studio"></a>Instalar o Visual Studio

Pode desenvolver topologias C# com SCP.NET utilizando [o Visual Studio](https://visualstudio.microsoft.com/downloads/). As instruções aqui usam o Visual Studio 2019, mas também podes usar versões anteriores do Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instale ferramentas do Data Lake para o Estúdio Visual

Para instalar ferramentas do Data Lake para o Visual Studio, siga os passos em [Começar a usar ferramentas do Data Lake para o Visual Studio.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)

## <a name="install-java"></a>Instalar Java

Quando envias uma topologia storm do Visual Studio, SCP.NET gera um ficheiro zip que contém a topologia e as dependências. Java é usado para criar estes ficheiros zip, porque usa um formato mais compatível com clusters baseados em Linux.

1. Instale o Kit de Desenvolvimento Java (JDK) 7 ou mais tarde no seu ambiente de desenvolvimento. Pode obter o Oráculo JDK da [Oracle.](https://openjdk.java.net/) Também pode utilizar [outras distribuições java.](/java/azure/jdk/)

2. Desaprote `JAVA_HOME` a variável ambiental para o diretório que contém Java.

3. Desaprote a `PATH` variável ambiente para incluir o `%JAVA_HOME%\bin` diretório.

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
| Aplicação de tempestade |Um projeto vazio de topologia da Tempestade. |
| Tempestade Azure SQL Writer Sample |Como escrever para a Base de Dados Azure SQL. |
| Tempestade Azure Cosmos DB Reader Sample |Como ler do Azure Cosmos DB. |
| Tempestade Azure Cosmos DB Amostra de Escritor |Como escrever ao Azure Cosmos DB. |
| Amostra de leitor storm EventHub |Como ler no Azure Event Hubs. |
| Amostra de escritor storm EventHub |Como escrever ao Azure Event Hubs. |
| Amostra de leitor de HBase de tempestade |Como ler a HBase em clusters HDInsight. |
| Amostra de escritor da tempestade HBase |Como escrever para a HBase em clusters HDInsight. |
| Amostra híbrida da tempestade |Como usar um componente Java. |
| Amostra de tempestade |Uma topologia básica de contagem de palavras. |

> [!WARNING]  
> Nem todos os modelos funcionam com HDInsight baseado em Linux. As embalagens NuGet utilizadas pelos modelos podem não ser compatíveis com a Mono. Para identificar potenciais problemas, consulte [a compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/) e utilize o [Analisador de Portabilidade .NET](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

Nos passos deste documento, você usa o tipo básico de projeto de Aplicação de Tempestade para criar uma topologia.

### <a name="apache-hbase-templates"></a>Modelos Apache HBase

Os modelos de leitor e escritor HBase utilizam a API HBase REST, não a HBase Java API, para comunicar com um HBase no cluster HDInsight.

### <a name="eventhub-templates"></a>Modelos eventHub

> [!IMPORTANT]  
> O componente de bico EventHub baseado em Java incluído com o modelo EventHub Reader pode não funcionar com Storm na versão HDInsight 3.5 ou posterior. Uma versão atualizada deste componente está disponível no [GitHub.](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib)

Para uma topologia de exemplo que usa este componente e trabalha com Storm on HDInsight 3.5, consulte [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Criar uma topologia C#

Para criar um projeto de topologia C# no Estúdio Visual:

1. Abra o Visual Studio.

1. Na janela **Iniciar,** **selecione Criar um novo projeto.**

1. Na **janela criar uma nova** janela do projeto, desloque-se e escolha a **Aplicação de Tempestade,** em seguida, selecione **Next**.

1. Na **janela Configure** a sua nova janela de projeto, insira um nome de **Projeto** do *WordCount,* vá ou crie um percurso diretório de **localização** para o projeto e, em seguida, selecione **Criar**.

    :::image type="content" source="./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png" alt-text="Aplicação storm, Configure a sua nova caixa de diálogo de projeto, Visual Studio" border="true":::

Depois de ter criado o projeto, deverá ter os seguintes ficheiros:

* *Programa.cs*: A definição de topologia para o seu projeto. Uma topologia padrão que consiste de um bico e um parafuso é criado por padrão.

* *Bico.cs*: Um bico de exemplo que emite números aleatórios.

* *Parafuso.cs*: Um parafuso de exemplo que mantém uma contagem de números emitidos pelo bico.

Ao criar o projeto, o NuGet descarrega o mais recente [pacote de SCP.NET.](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)

### <a name="implement-the-spout"></a>Implementar o bico

Em seguida, adicione o código para o bico, que é usado para ler dados numa topologia de uma fonte externa. Este bico emite aleatoriamente uma frase na topologia.

1. Open *Spout.cs*. Os principais componentes para um bico são:

   * `NextTuple`: Chamado por tempestade quando o bico é permitido emitir novos tuples.

   * `Ack` (apenas topologia transacional): Cabos de reconhecimento iniciados por outros componentes na topologia para tuples enviados do bico. Reconhecer um tuple permite que o bico saiba que foi processado com sucesso por componentes a jusante.

   * `Fail` (apenas topologia transacional): Manuseia tuples que estão a processar outros componentes na topologia. A implementação de um `Fail` método permite-lhe reedição da tuple para que possa ser processada novamente.

2. Substitua o conteúdo da `Spout` classe pelo seguinte texto:

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

1. Elimine o ficheiro *bolt.cs* existente do projeto.

2. No **Solution Explorer,** clique com o botão direito no projeto e selecione **Adicionar**  >  **Novo item**. Da lista, selecione **Storm Bolt**, e *introduza Splitter.cs* como o nome. No código do novo ficheiro, altere o nome do espaço de nome para `WordCount` . Em seguida, repita este processo para criar um segundo parafuso chamado *Counter.cs*.

   * *Splitter.cs*: Implementa um parafuso que divide frases em palavras individuais, e emite um novo fluxo de palavras.

   * *Contra.cs*: Implementa um parafuso que conta cada palavra e emite um novo fluxo de palavras e a contagem para cada palavra.

     > [!NOTE]  
     > Estes parafusos lêem e escrevem para streams, mas também pode usar um parafuso para comunicar com fontes como uma base de dados ou um serviço.

3. Open *Splitter.cs*. Tem apenas um método por defeito: `Execute` . O `Execute` método é chamado quando o parafuso recebe um tuple para processamento. Aqui, você pode ler e processar tuples de entrada, e emitir tuples de saída.

4. Substitua o conteúdo da `Splitter` classe pelo seguinte código:

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

5. Abrir *o contador.cs*, e substituir o conteúdo da classe pelo seguinte código:

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

### <a name="define-the-topology"></a>Definir a topologia

Bicos e parafusos estão dispostos num gráfico, que define como os dados fluem entre os componentes. Para esta topologia, o gráfico é o seguinte:

:::image type="content" source="./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png" alt-text="Diagrama de arranjo de componente de bico e parafuso, topologia da tempestade" border="false":::

O bico emite frases que são distribuídas em instâncias do parafuso Splitter. O parafuso Splitter quebra as frases em palavras, que são distribuídas no parafuso de contador.

Como a instância contra a página de contador detém a contagem de palavras localmente, pretende certificar-se de que as palavras específicas fluem para a mesma instância do parafuso de contador. Cada caso acompanha palavras específicas. Uma vez que o parafuso Splitter não mantém nenhum estado, não importa qual o caso do divisa que recebe qual sentença.

Abra o *Program.cs*. O método importante é `GetTopologyBuilder` , que é usado para definir a topologia que é submetida a Tempestade. Substitua o conteúdo do `GetTopologyBuilder` seguinte código para implementar a topologia descrita anteriormente:

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

## <a name="submit-the-topology"></a>Submeta a topologia

Está agora pronto para submeter a topologia ao seu cluster HDInsight.

1. Navegue para **ver o** Explorador  >  **do Servidor**.

1. Clique com o botão direito **Azure**, **selecione Connect to Microsoft Azure Subscription...** e complete o processo de inscrição.

1. No **Solution Explorer,** clique à direita no projeto e escolha **Submeter-se a Tempestade em HDInsight**.

1. Na caixa de diálogo **'Enviar Topologia',** sob a lista de recuos do **Storm Cluster,** escolha a tempestade no cluster HDInsight e, em seguida, selecione **Enviar por isso**. Pode verificar se a submissão é bem sucedida visualizando o painel **de saída.**

    Quando a topologia tiver sido submetida com sucesso, deve aparecer a janela **Storm Toplogies View** para o cluster. Escolha a topologia **wordCount** da lista para ver informações sobre a topologia de execução.

    :::image type="content" source="./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png" alt-text="Janela de vista de topologia de tempestade, cluster HDInsight, Estúdio Visual" border="true":::

    > [!NOTE]  
    > Também pode ver **As Topologias** de Tempestade do Explorador de **Servidores.** Expanda **a Azure**  >  **HDInsight,** clique à direita numa tempestade no cluster HDInsight e, em seguida, escolha **Ver Toplogies storm**.

    Para visualizar informações sobre os componentes na topologia, selecione um componente no diagrama.

1. Na secção Resumo da **Topologia,** selecione **Kill** para parar a topologia.

    > [!NOTE]  
    > As topologias da tempestade continuam a funcionar até serem desativadas, ou o cluster é eliminado.

## <a name="transactional-topology"></a>Topologia transacional

A topologia anterior não é transacional. Os componentes da topologia não implementam a funcionalidade de reprodução de mensagens. Para um exemplo de uma topologia transacional, crie um projeto e selecione **Storm Sample** como o tipo de projeto.

As topologias transacionais implementam as seguintes para apoiar a repetição de dados:

* **Caching de metadados**: O bico deve armazenar metadados sobre os dados emitidos, para que os dados possam ser recuperados e emitidos novamente se ocorrer uma falha. Como os dados emitidos pela amostra são pequenos, os dados brutos de cada tuple são armazenados num dicionário para repetição.

* **Ack**: Cada parafuso na topologia pode chamar `this.ctx.Ack(tuple)` para reconhecer que processou com sucesso uma tuple. Quando todos os parafusos reconhecerem a tuple, o `Ack` método do bico é invocado. O `Ack` método permite que o bico remova dados que foram cached para repetição.

* **Falha:** Cada parafuso pode ligar `this.ctx.Fail(tuple)` para indicar que o processamento falhou para uma tuple. A falha propaga-se ao `Fail` método do bico, onde a tuple pode ser reproduzida utilizando metadados em cache.

* **ID de sequência**: Ao emitir um tuple, pode ser especificado um ID de sequência único. Este valor identifica o tuple para o processamento de repetição `Ack` `Fail` (e) para repetição . Por exemplo, o bico no projeto **Storm Sample** utiliza a seguinte chamada de método ao emitir dados:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Este código emite um tuple que contém uma frase para o fluxo padrão, com o valor de identificação de sequência contido em `lastSeqId` . Para este exemplo, `lastSeqId` é incrementado para cada tuple emitido.

Como demonstrado no projeto **Storm Sample,** se um componente é transacional pode ser definido em tempo de execução, com base na configuração.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia híbrida com C# e Java

Também pode usar ferramentas do Data Lake para o Visual Studio para criar topologias híbridas, onde alguns componentes são C# e outros são Java.

Para um exemplo de topologia híbrida, crie um projeto e selecione **Storm Hybrid Sample**. Este tipo de amostra demonstra os seguintes conceitos:

* **Bico de Java** e **parafuso C#**: Definido na `HybridTopology_javaSpout_csharpBolt` classe.

  Uma versão transacional é definida na `HybridTopologyTx_javaSpout_csharpBolt` classe.

* **C# bico** e **bolt Java**: Definido na `HybridTopology_csharpSpout_javaBolt` classe.

  Uma versão transacional é definida na `HybridTopologyTx_csharpSpout_javaBolt` classe.

  > [!NOTE]  
  > Esta versão também demonstra como usar o código Clojure a partir de um ficheiro de texto como componente Java.

Para mudar a topologia que é usada quando o projeto é submetido, mova a `[Active(true)]` declaração para a topologia que pretende utilizar, antes de submetê-la ao cluster.

> [!NOTE]  
> Todos os ficheiros Java que são necessários são fornecidos como parte deste projeto na pasta *JavaDependency.*

Considere o seguinte quando estiver a criar e submeter uma topologia híbrida:

* Utilize `JavaComponentConstructor` para criar uma instância da classe Java para um bico ou parafuso.

* Utilize `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` para serializar dados dentro ou fora dos componentes java de objetos Java para JSON.

* Ao submeter a topologia ao servidor, deve utilizar a opção **configurações adicionais** para especificar os caminhos do **Ficheiro Java**. O caminho especificado deve ser o diretório que tem os ficheiros JAR que contêm as suas aulas de Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET versão 0.9.4.203 introduz uma nova classe e método especificamente para trabalhar com o bico do Event Hub (um bico java que lê a partir de Event Hubs). Quando criar uma topologia que utiliza um bico de Centro de Eventos (por exemplo, utilizando o modelo de amostra de **leitor Storm EventHub),** utilize as seguintes APIs:

* `EventHubSpoutConfig` classe: Cria um objeto que contém a configuração para o componente do bico.

* `TopologyBuilder.SetEventHubSpout` método: Adiciona o componente de bico do Event Hub à topologia.

> [!NOTE]  
> Deve ainda utilizar os `CustomizedInteropJSONSerializer` dados para serializar os dados produzidos pelo bico.

## <a name="use-configurationmanager"></a>Utilizar ConfiguraçãoManager

Não utilize **o ConfigurationManager** para recuperar os valores de configuração dos componentes de parafusos e bicos. Fazê-lo pode causar uma exceção ao ponteiro nulo. Em vez disso, passe a configuração do seu projeto para a topologia da Tempestade como um par chave e valor no contexto da topologia. Cada componente que se baseie em valores de configuração deve recuperá-los do contexto durante a inicialização.

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

Se utilizar um `Get` método para devolver uma instância do seu componente, deve certificar-se de que passa tanto os `Context` `Dictionary<string, Object>` parâmetros como os parâmetros para o construtor. Segue-se um método básico `Get` que passa corretamente estes valores:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Como atualizar SCP.NET

Lançamentos recentes de SCP.NET atualização do pacote de suporte através do NuGet. Quando uma nova atualização está disponível, recebe uma notificação de upgrade. Para verificar manualmente se há uma atualização, siga estes passos:

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet**.

2. A partir do gestor do pacote, selecione **Updates**. Se estiver disponível uma atualização para o pacote de suporte SCP.NET, está listado. Selecione **Update** for the package e, em seguida, na caixa de diálogo **De pré-visualização Alterações,** selecione **OK** para instalá-lo.

> [!IMPORTANT]  
> Se o seu projeto foi criado com uma versão anterior de SCP.NET que não utilizaram o NuGet, tem de executar os seguintes passos para atualizar para uma versão mais recente:
>
> 1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet**.
> 2. Utilizando o campo **De pesquisa,** procure e adicione, e adicione, `Microsoft.SCP.Net.SDK` ao projeto.

## <a name="troubleshoot-common-issues-with-topologies"></a>Resolver problemas comuns com topologias

### <a name="null-pointer-exceptions"></a>Exceções de ponteiro nulo

Quando estiver a utilizar uma topologia C# com um cluster HDInsight baseado em Linux, os componentes de parafusos e bicos que utilizam **o ConfigurationManager** para ler as definições de configuração em tempo de execução podem devolver exceções nulas de ponteiro.

A configuração para o seu projeto é passada para a topologia da tempestade como um par chave e valor no contexto de topologia. Pode ser recuperado do objeto do dicionário que é passado para os seus componentes quando são inicializados.

Para mais informações, consulte a secção ['Configuração utilização'](#use-configurationmanager) deste documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Quando estiver a utilizar uma topologia C# com um cluster HDInsight baseado em Linux, poderá encontrar o seguinte erro:

`System.TypeLoadException: Failure has occurred while loading a type.`

Este erro ocorre quando se utiliza um binário que não é compatível com a versão de .NET que a Mono suporta.

Para clusters HDInsight baseados em Linux, certifique-se de que o seu projeto utiliza binários compilados para .NET 4.5.

### <a name="test-a-topology-locally"></a>Teste uma topologia local

Embora seja fácil implantar uma topologia num cluster, em alguns casos, você pode precisar testar uma topologia localmente. Use os seguintes passos para executar e testar o exemplo de topologia neste artigo localmente no seu ambiente de desenvolvimento.

> [!WARNING]  
> Os testes locais só funcionam para topologias básicas, apenas C#. Não é possível utilizar testes locais para topologias híbridas ou topologias que utilizam vários fluxos.

1. No **Solution Explorer,** clique com o botão direito do projeto e selecione **Propriedades.** Nas propriedades do projeto. Em seguida, altere o **tipo de saída** para a **aplicação de consola**.

   :::image type="content" source="./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png" alt-text="Aplicação HDInsight Storm, propriedades do projeto, tipo de saída" border="true":::

   > [!NOTE]
   > Lembre-se de mudar o **tipo de saída** de volta para a Biblioteca de **Classes** antes de colocar a topologia num cluster.

1. No **Solution Explorer,** clique com o botão direito no projeto e, em seguida, selecione **Adicionar**  >  **Novo Item**. Selecione **Class**, e *insira LocalTest.cs* como o nome da classe. Finalmente, **selecione Adicionar**.

1. Abra *o LocalTest.cs*, e adicione a seguinte `using` declaração no topo:

    ```csharp
    using Microsoft.SCP;
    ```

1. Utilize o seguinte código como o conteúdo da `LocalTest` classe:

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

    Tire um momento para ler os comentários de código. Este código utiliza `LocalContext` para executar os componentes no ambiente de desenvolvimento. Persiste o fluxo de dados entre os componentes e os ficheiros de texto na unidade local.

1. *Programa Aberto.cs*, e adicione o seguinte código ao `Main` método:

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

1. Guarde as alterações e, em seguida, selecione **F5** ou escolha **Debug**  >  **Start Debugging** para iniciar o projeto. Deve aparecer uma janela da consola e registar o estado dos testes. Quando `Tests finished` aparecer, selecione qualquer chave para fechar a janela.

1. Utilize o **Windows Explorer** para localizar o diretório que contém o seu projeto. (Por exemplo: *C: \\ Utilizadores \\ \<your_user_name> \\ origem \\ repos \\ WordCount \\ WordCount*.) Em seguida, neste diretório, abra *o Bin* e, em seguida, selecione *Debug*. Deve ver os ficheiros de texto que foram produzidos quando os testes foram realizados: *sentences.txt*, *counter.txt* e *splitter.txt*. Abra cada ficheiro de texto e inspecione os dados.

   > [!NOTE]  
   > Os dados de cadeia persistem como uma série de valores decimais nestes ficheiros. Por exemplo, `[[97,103,111]]` no **ficheirosplitter.txt** representa a palavra *atrás*.

> [!NOTE]  
> Certifique-se de que devolve o **tipo de Projeto** à Biblioteca de **Classes** nas propriedades do projeto antes de se implantar num cluster Storm on HDInsight.

### <a name="log-information"></a>Informação de registo

Pode registar facilmente informações a partir dos seus componentes de topologia utilizando `Context.Logger` . Por exemplo, o seguinte comando cria uma entrada de registo informativo:

`Context.Logger.Info("Component started");`

As informações registadas podem ser visualizadas a partir do Registo de **Serviços Hadoop,** que é encontrado no **Server Explorer**. Expanda a entrada para o seu cluster Storm on HDInsight e, em seguida, expanda o **Registo de Serviços Hadoop**. Por fim, selecione o ficheiro de registo para visualizar.

> [!NOTE]  
> Os registos são armazenados na conta de armazenamento Azure que é utilizada pelo seu cluster. Para visualizar os registos no Visual Studio, tem de iniciar sessão na subscrição do Azure que detém a conta de armazenamento.

### <a name="view-error-information"></a>Ver informações de erro

Para ver os erros ocorridos numa topologia em execução, utilize os seguintes passos:

1. A partir do **Server Explorer,** clique à direita no cluster Storm on HDInsight e selecione **Ver Topologias de Tempestade**.

   Para o **Bico** e **os Parafusos,** a coluna **Último Erro** contém informações sobre o último erro.

2. Selecione o **ID do Bico** ou **O ID** do parafuso para o componente que tem um erro listado. A página de detalhes apresenta informações adicionais de erro na secção **Erros** na parte inferior da página.

3. Para obter mais informações, selecione um **Porto** na secção **executores** da página, para ver o registo do trabalhador storm durante os últimos minutos.

### <a name="errors-submitting-topologies"></a>Erros na submissão de topologias

Se encontrar erros ao submeter uma topologia a HDInsight, pode encontrar registos para os componentes do lado do servidor que lidam com a submissão de topologia no seu cluster HDInsight. Para descarregar estes registos, utilize o seguinte comando a partir de uma linha de comando:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Substitua o *sshuser* pela conta de utilizador SSH pelo cluster. Substitua o *nome de cluster* pelo nome do cluster HDInsight. Para obter mais informações sobre a utilização `scp` e `ssh` com o HDInsight, consulte [Use SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

As submissões podem falhar por várias razões:

* O JDK não está instalado ou não está no caminho.
* As dependências necessárias de Java não estão incluídas na submissão.
* As dependências são incompatíveis.
* Os nomes da topologia são duplicados.

Se o ficheiro de registo *hdinsight-scpwebapi.out* contiver `FileNotFoundException` um, a exceção pode ser causada pelas seguintes condições:

* O JDK não está no caminho do ambiente de desenvolvimento. Verifique se o JDK está instalado no ambiente de desenvolvimento, e que `%JAVA_HOME%/bin` está no caminho.
* Falta-te uma dependência java. Certifique-se de que está a incluir quaisquer ficheiros .jar necessários como parte da submissão.

## <a name="next-steps"></a>Passos seguintes

Para um exemplo de processamento de dados de Centros de Eventos, consulte [eventos de processo de Azure Event Hubs com Storm on HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Para um exemplo de uma topologia C# que divide os dados de fluxo em vários fluxos, consulte [o exemplo da tempestade C#](https://github.com/Blackmist/csharp-storm-example).

Para descobrir mais informações sobre a criação de topologias C#, consulte [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para obter mais formas de trabalhar com a HDInsight e mais storm on HDInsight samples, consulte os seguintes documentos:

**Microsoft SCP.NET**

* [Guia de programação SCP para Apache Storm em Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm no HDInsight**

* [Implementar e gerir as topologias da Tempestade Apache em Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Exemplo Apache Storm toplogis in Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop em HDInsight**

* [O que é Apache Hive e HiveQL em Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Utilizar o MapReduce no Apache Hadoop no HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase no HDInsight**

* [Utilizar o Apache HBase no Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
