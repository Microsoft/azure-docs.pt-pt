---
title: Topologias do Apache Storm com o Visual Studio e do c# – Azure HDInsight
description: Saiba como criar topologias Storm em c#. Crie uma topologia de contagem de palavras simples no Visual Studio com as ferramentas do Hadoop para Visual Studio.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 1bcb50829dca59f8a467c2c1d2381b5463ef9471
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437399"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Desenvolver topologias c# para Apache Storm, utilizando as ferramentas do Data Lake para Visual Studio

Saiba como criar um C# topologia de Apache Storm, utilizando as ferramentas do Azure Data Lake (Apache Hadoop) para o Visual Studio. Este documento descreve detalhadamente o processo de criação de um projeto do Storm no Visual Studio, testá-lo localmente e implementá-lo para um Apache Storm no cluster de HDInsight do Azure.

Também irá aprender a criar topologias híbridas que utilizam o c# e componentes em Java.

> [!NOTE]  
> Embora os passos neste documento dependem de um ambiente de desenvolvimento do Windows com o Visual Studio, o projeto compilado pode ser submetido para o cluster do Linux ou do HDInsight baseado em Windows. Apenas os clusters baseados em Linux criados após a 28 de Outubro de 2016, suportam SCP.NET topologias.

Para utilizar uma topologia c# com um cluster baseado em Linux, tem de atualizar o pacote NuGet Microsoft utilizado pelo seu projeto para a versão 0.10.0.6 ou posterior. A versão do pacote também tem de corresponder à versão principal do Storm instalada no HDInsight.

| Versão do HDInsight | Versão do Apache Storm | Versão SCP.NET | Versão Mono predefinida |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(apenas no HDInsight baseado em Windows) | ND |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> As topologias C# em clusters baseados em Linux têm de utilizar o .NET 4.5 e utilizar o Mono para serem executadas no cluster do HDInsight. Verifique [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) quanto a potenciais incompatibilidades.

## <a name="install-visual-studio"></a>Instalar o Visual Studio

Pode desenvolver topologias c# com CP.NET ao utilizar uma das seguintes versões do Visual Studio:

* O Visual Studio 2012 com atualização 4

* Visual Studio 2013 com a atualização 4 ou [Comunidade do Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 ou [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (qualquer edição)

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar Data Lake tools para Visual Studio

Para instalar as ferramentas do Data Lake para Visual Studio, siga os passos em [começar a utilizar as ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Instalar o Java

Quando submete uma topologia do Storm a partir do Visual Studio, SCP.NET gera um ficheiro zip que contém a topologia e as dependências. Java é utilizado para criar esses arquivos zip, porque ele usa um formato mais compatível com clusters baseados em Linux.

1. Instale o Java Developer Kit (JDK) 7 ou posterior no seu ambiente de desenvolvimento. Pode obter o JDK Oracle partir [Oracle](https://aka.ms/azure-jdks). Também pode utilizar [outras distribuições do Java](https://openjdk.java.net/).

2. O `JAVA_HOME` variável de ambiente tem de apontar para o diretório que contém o Java.

3. O `PATH` variável de ambiente tem de incluir o `%JAVA_HOME%\bin` diretório.

Pode utilizar a aplicação de consola c# seguinte para verificar se Java e o JDK estão corretamente instalados:

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

## <a name="apache-storm-templates"></a>Modelos do Apache Storm

As ferramentas do Data Lake para Visual Studio fornecem os seguintes modelos:

| Tipo de projeto | Demonstra |
| --- | --- |
| Aplicação Storm |Um projeto de topologia do Storm vazio. |
| Exemplo de gravador SQL do Azure do Storm |Como escrever para a base de dados do Azure SQL. |
| Exemplo de leitor do Storm do Azure Cosmos DB |Como ler a partir do Azure Cosmos DB. |
| Exemplo de escritor do Storm do Azure Cosmos DB |Como escrever para o Azure Cosmos DB. |
| Exemplo de leitor de EventHub do Storm |Como ler a partir de Event Hubs do Azure. |
| Exemplo de gravador de EventHub do Storm |Como escrever para os Hubs de eventos do Azure. |
| Exemplo de leitor de HBase do Storm |Como ler a partir do HBase no HDInsight clusters. |
| Exemplo de gravador de HBase do Storm |Como escrever em clusters do HDInsight HBase. |
| Exemplo do Storm híbrido |Como utilizar um componente de Java. |
| Exemplo do Storm |Uma topologia de contagem de palavras básica. |

> [!WARNING]  
> Nem todos os modelos funcionam com o HDInsight baseado em Linux. Pacotes de NuGet utilizados pelos modelos podem não ser compatíveis com Mono. Verifique a [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) documentar e utilizar os [analisador de portabilidade do .NET](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) para identificar potenciais problemas.

Os passos neste documento, vai utilizar o tipo de projeto de aplicação Storm básico para criar uma topologia.

### <a name="apache-hbase-templates-notes"></a>Notas de modelos de Apache HBase

Os modelos de leitor e gravador de HBase utilizam a API de REST de HBase, não, a API de Java de HBase, para comunicar com um HBase num cluster do HDInsight.

### <a name="eventhub-templates-notes"></a>Notas de modelos de EventHub

> [!IMPORTANT]  
> O componente de spout de EventHub baseadas em Java incluído com o modelo de leitor de EventHub poderá não funcionar com o Storm no HDInsight versão 3.5 ou posterior. Uma versão atualizada deste componente está disponível em [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Para uma topologia de exemplo que utiliza este componente e funciona com o Storm no HDInsight 3.5, consulte [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Criar uma topologia de c#

1. Abra o Visual Studio, selecione **arquivo** > **New**e, em seguida, selecione **projeto**.

2. Do **novo projeto** janela, expanda **instalado** > **modelos**e selecione **Azure Data Lake**. Na lista de modelos, selecione **aplicação Storm**. Na parte inferior do ecrã, introduza **WordCount** como o nome da aplicação.

    ![Janela de captura de ecrã do novo projeto](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Depois de ter criado o projeto, deve ter os seguintes ficheiros:

   * **Program.cs**: Esse arquivo define a topologia para o seu projeto. Uma topologia de predefinição que consiste num spout e um bolt é criada por predefinição.

   * **Spout.cs**: Spout de exemplo que emite números aleatórios.

   * **Bolt.cs**: Um bolt de exemplo que mantém uma contagem de números emitidas pelo spout.

     Quando criar o projeto, o NuGet transfere a versão mais recente [SCP.NET pacote](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementar o spout

1. Open **Spout.cs**. Spouts são utilizados para ler os dados numa topologia de uma origem externa. Os principais componentes de um spout são:

   * **NextTuple**: Chamado por Storm quando o spout tem permissão para emitir novas cadeias de identificação.

   * **ACK** (apenas na topologia transacional): Processa as confirmações iniciadas por outros componentes da topologia tuplas enviadas a partir de spout de. Ter consciência de uma cadeia de identificação permite spout sabe que foi processado com êxito por componentes downstream.

   * **Falhar** (apenas na topologia transacional): Processa as tuplas que são falhar processamento de outros componentes da topologia. Implementar um método de ativação permite-lhe emitir novamente a cadeia de identificação para que possa ser processado novamente.

2. Substitua os conteúdos do **Spout** classe com o seguinte texto: Este spout emite aleatoriamente uma frase para a topologia.

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

### <a name="implement-the-bolts"></a>Implementar os práticos

1. Elimine o existente **Bolt.cs** arquivo do projeto.

2. Na **Explorador de soluções**, clique com o botão direito no projeto e selecione **Add** > **novo item**. Na lista, selecione **Bolt do Storm**e introduza **Splitter.cs** como o nome. Repita este processo para criar um segundo bolt denominado **Counter.cs**.

   * **Splitter.cs**: Implementa um bolt que divide as frases em palavras individuais e emite um novo fluxo de palavras.

   * **Counter.cs**: Implementa um bolt que contabiliza cada palavra e emite um novo fluxo de palavras e a contagem de cada palavra.

     > [!NOTE]  
     > Estes bolts de leitura e escrita aos fluxos, mas também pode utilizar um bolt para comunicar com origens, como uma base de dados ou serviço.

3. Open **Splitter.cs**. Ele tem apenas um método por predefinição: **Executar**. O método Execute é chamado quando o bolt recebe uma cadeia de identificação para processamento. Aqui, pode ler e processar as tuplas de entrada e emitir as tuplas de saída.

4. Substitua os conteúdos do **Splitter** classe com o código a seguir:

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

5. Open **Counter.cs**e substitua os conteúdos de classe com o código a seguir:

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

Spouts e bolts são dispostos num gráfico, que define a forma como os dados fluem entre componentes. Para esta topologia, o gráfico é o seguinte:

![Diagrama de como os componentes são dispostos](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

As frases são emitidas a spout e são distribuídas para as instâncias do bolt do divisor. O bolt do Splitter divide as frases em palavras, o que são distribuídas para o bolt do contador.

Porque a contagem de palavras é mantida localmente na instância do contador, quer certificar-se de que palavras específicas do fluxo para a mesma instância de bolt do contador. Cada instância mantém um registo de palavras específicas. Uma vez que o bolt do Splitter mantém sem estado, realmente não importa qual instância do separador recebe qual frase.

Open **Program.cs**. É o método importante **GetTopologyBuilder**, que é utilizado para definir a topologia que foi submetida para o Storm. Substitua o conteúdo do **GetTopologyBuilder** com o código a seguir para implementar a topologia descrita anteriormente:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

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

1. Na **Explorador de soluções**, clique com o botão direito no projeto e selecione **submeter para Storm no HDInsight**.

   > [!NOTE]  
   > Se lhe for pedido, introduza as credenciais para a sua subscrição do Azure. Se tiver mais de uma subscrição, inicie sessão no que contém o seu cluster Storm no HDInsight.

2. Selecione o Storm no cluster do HDInsight a partir da **Cluster do Storm** na lista pendente e, em seguida, selecione **submeter**. Pode monitorizar se a submissão for bem sucedida, utilizando o **saída** janela.

3. Quando a topologia foi submetida com êxito, o **topologias do Storm** para o cluster deve aparecer. Selecione o **WordCount** topologia da lista para ver informações sobre a topologia em execução.

   > [!NOTE]  
   > Também pode ver **topologias do Storm** partir **Explorador de servidores**. Expanda **Azure** > **HDInsight**, um Storm num cluster do HDInsight com o botão direito e, em seguida, selecione **Zobrazit Topologie Stormu**.

    Para ver informações sobre os componentes da topologia, faça duplo clique o componente no diagrama.

4. Do **resumo da topologia** ver, clique em **Kill** para parar a topologia.

   > [!NOTE]  
   > Topologias do Storm continuam a ser executado até que eles são desativados ou o cluster é eliminado.

## <a name="transactional-topology"></a>Topologia transacional

A topologia anterior é não transacional. Os componentes da topologia não implementam funcionalidades para reproduzir as mensagens. Para obter um exemplo de uma topologia transacional, crie um projeto e selecione **exemplo de Storm** como o tipo de projeto.

Topologias de transacionais implementam o seguinte para suportar a repetição de dados:

* **Colocação em cache de metadados**: O spout tem de armazenar metadados sobre os dados emitidos, para que os dados possam ser recuperados e emitidos novamente se ocorrer uma falha. Uma vez que os dados emitidos pelo exemplo forem pequenos, os dados não processados para cada tupla são armazenados num dicionário para repetição.

* **Ack**: Pode chamar cada bolt na topologia `this.ctx.Ack(tuple)` para confirmar que foi processado com êxito uma cadeia de identificação. Quando todos os bolts confirmar a tupla, o `Ack` método da spout é invocado. O `Ack` método permite que o spout remover os dados que foi colocado em cache para repetição.

* **Falhar**: Pode chamar cada bolt `this.ctx.Fail(tuple)` para indicar que o processamento falhou para uma cadeia de identificação. A falha é propagada para o `Fail` método de spout, onde a cadeia de identificação pode ser repetida usando armazenados em cache de metadados.

* **ID de sequência**: Quando emitir uma cadeia de identificação, pode ser especificado um ID de sequência exclusivo. Este valor identifica a cadeia de identificação para o processamento de repetição (Ack e falha). Por exemplo, o spout no **exemplo de Storm** projeto utiliza o seguinte quando emitir dados:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Esse código emite uma cadeia de identificação que contém uma frase no fluxo padrão, com o valor de ID de sequência contido no **lastSeqId**. Neste exemplo, **lastSeqId** é incrementada para cada tupla emitida.

Como demonstrado na **exemplo de Storm** do projeto, se um componente é transacional pode ser definido em tempo de execução, com base na configuração.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia de híbrida com c# e Java

Também pode utilizar ferramentas do Data Lake para Visual Studio para criar topologias híbridas, onde alguns componentes estão em C# e outras são Java.

Para obter um exemplo de uma topologia de híbrida, crie um projeto e selecione **Storm híbrida exemplo**. Este tipo de exemplo demonstra os seguintes conceitos:

* **Spout de Java** e  **C# bolt**: Definido na **HybridTopology_javaSpout_csharpBolt**.

    * Uma versão transacional é definida no **HybridTopologyTx_javaSpout_csharpBolt**.

* **C#spout** e **Java bolt**: Definido na **HybridTopology_csharpSpout_javaBolt**.

    * Uma versão transacional é definida no **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]  
  > Esta versão também demonstra como utilizar o código de Clojure de um arquivo de texto como um componente de Java.


Para mudar a topologia que é utilizada quando o projeto é submetido, mover o `[Active(true)]` instrução para a topologia que pretende utilizar, antes de enviá-lo para o cluster.

> [!NOTE]  
> Todos os arquivos de Java que são necessários são fornecidos como parte deste projeto na **JavaDependency** pasta.

Quando estiver a criar e submeter uma topologia de híbrida, considere o seguinte:

* Uso **JavaComponentConstructor** para criar uma instância da classe Java para um spout ou bolt.

* Uso **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** para serializar os dados para dentro ou fora de componentes em Java de objetos Java no JSON.

* Ao submeter a topologia para o servidor, tem de utilizar o **configurações adicionais** opção para especificar a **caminhos de ficheiro de Java**. O caminho especificado deve ser o diretório que contém os ficheiros JAR que contêm as suas classes Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Versão SCP.NET 0.9.4.203 introduz uma nova classe e método especificamente para trabalhar com o spout do Hub de eventos (um spout de Java que lê a partir dos Hubs de eventos). Quando cria uma topologia que utiliza um spout do Hub de eventos, utilize os seguintes métodos:

* **EventHubSpoutConfig** classe: Cria um objeto que contém a configuração para o componente de spout.

* **TopologyBuilder.SetEventHubSpout** método: Adiciona o componente de spout do Hub de eventos para a topologia.

> [!NOTE]  
> Ainda tem de utilizar o **CustomizedInteropJSONSerializer** para serializar dados produzidos pelo spout.

## <a id="configurationmanager"></a>Usar ConfigurationManager

Não utilize **ConfigurationManager** para recuperar valores de configuração do bolt e spout componentes. Se o fizer, pode fazer com que uma exceção de ponteiro nulo. Em vez disso, a configuração para o seu projeto é passada para a topologia de Storm como um par de chave e valor no contexto de topologia. Cada componente que se baseia em valores de configuração tem recuperá-los do contexto durante a inicialização.

O código a seguir demonstra como obter estes valores:

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
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Se utilizar um `Get` método para retornar uma instância do seu componente, tem de garantir que ele passa de ambos os `Context` e `Dictionary<string, Object>` parâmetros para o construtor. O exemplo seguinte é um basic `Get` método corretamente transmite esses valores:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Como atualizar SCP.NET

Lançamentos recentes de SCP.NET suportam a atualização do pacote através do NuGet. Quando uma nova atualização está disponível, receberá uma notificação de atualização. Para verificar uma atualização manualmente, siga estes passos:

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet**.

2. A partir do Gestor de pacotes, selecione **atualizações**. Se está disponível uma atualização, estão listadas. Clique em **atualização** para o pacote para instalá-lo.

> [!IMPORTANT]  
> Se o projeto foi criado com uma versão anterior do SCP.NET que não utilizou o NuGet, tem de efetuar os seguintes passos para atualizar para uma versão mais recente:
>
> 1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet**.
> 2. Utilizar o **pesquisa** campo, procure e, em seguida, adicione **Microsoft** ao projeto.

## <a name="troubleshoot-common-issues-with-topologies"></a>Resolver problemas comuns com topologias

### <a name="null-pointer-exceptions"></a>Exceções de ponteiro nulo

Quando estiver a utilizar uma topologia c# com um cluster do HDInsight baseado em Linux, bolt e componentes que usam de spout **ConfigurationManager** para ler as definições de configuração em tempo de execução podem retornar exceções de ponteiro nulo.

A configuração para o seu projeto é passada para a topologia de Storm como um par de chave e valor no contexto de topologia. Ele pode ser obtido a partir o objeto de dicionário que é transmitido para os componentes quando eles são inicializados.

Para obter mais informações, consulte a [ConfigurationManager](#configurationmanager) seção deste documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Quando estiver a utilizar uma topologia c# com um cluster do HDInsight baseado em Linux, pode encontrar o seguinte erro:

    System.TypeLoadException: Failure has occurred while loading a type.

Este erro ocorre quando usar um binário que não é compatível com a versão do .NET que suporte o Mono.

Para os clusters do HDInsight baseado em Linux, certifique-se de que o seu projeto usa os binários compilados para o .NET 4.5.

### <a name="test-a-topology-locally"></a>Testar localmente uma topologia

Embora seja fácil de implementar uma topologia para um cluster, em alguns casos, se pretender testar localmente uma topologia. Utilize os seguintes passos para executar e testar a topologia de exemplo neste tutorial localmente no seu ambiente de desenvolvimento.

> [!WARNING]  
> Realização de testes locais só funciona para basic, c#-apenas topologias. Não é possível utilizar a realização de testes locais para topologias híbridas ou topologias que utilizam múltiplos fluxos.

1. Na **Explorador de soluções**, clique com o botão direito no projeto e selecione **propriedades**. Nas propriedades do projeto, alterar os **tipo de saída** ao **aplicação de consola**.

    ![Captura de ecrã das propriedades do projeto, com o tipo de saída realçado](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Não se esqueça de alterar o **tipo de saída** voltar ao **biblioteca de classes** antes de implementar a topologia para um cluster.

2. Na **Explorador de soluções**, clique com o botão direito no projeto e, em seguida, selecione **Add** > **Novo Item**. Selecione **classe**e introduza **LocalTest.cs** como o nome da classe. Por fim, clique em **adicionar**.

3. Open **LocalTest.cs**e adicione as seguintes **usando** instrução na parte superior:

    ```csharp
    using Microsoft.SCP;
    ```

4. Utilize o seguinte código como o conteúdo do **LocalTest** classe:

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

    Dedique uns momentos para ler os comentários do código. Esse código usa **LocalContext** executar os componentes no ambiente de desenvolvimento e persistir o fluxo de dados entre os componentes em arquivos de texto na unidade local.

1. Open **Program.cs**e adicione o seguinte para o **Main** método:

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

2. Guardar as alterações e, em seguida, clique em **F5** ou selecione **depurar** > **iniciar depuração** para iniciar o projeto. Uma janela de console deve aparecer e registo de estado como o progresso de testes. Quando **testes concluída** for apresentada, prima qualquer tecla para fechar a janela.

3. Uso **Windows Explorer** para localizar o diretório que contém o seu projeto. Por exemplo: **C:\Users\<your_user_name > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Neste diretório, abra **Bin**e, em seguida, clique em **depurar**. Deverá ver os ficheiros de texto que foram produzidos quando os testes foi executada: sentences.txt counter.txt e splitter.txt. Abrir cada arquivo de texto e inspecionar os dados.

   > [!NOTE]  
   > Persistir os dados de cadeia de caracteres como uma matriz de valores decimais nesses arquivos. Por exemplo, \[[97,103,111]] no **splitter.txt** arquivo é a palavra *e*.

> [!NOTE]  
> Certifique-se de que definir o **tipo de projeto** voltar ao **biblioteca de classes** antes de implementar um cluster Storm no HDInsight.

### <a name="log-information"></a>Informações de registo

Pode registar facilmente informações a partir de seus componentes de topologia utilizando `Context.Logger`. Por exemplo, o comando seguinte cria uma entrada de registo informativa:

```csharp
Context.Logger.Info("Component started");
```

Com sessão iniciada informações podem ser visualizadas a partir do **registo de serviço do Hadoop**, que se encontra no **Explorador de servidores**. Expanda a entrada para o Storm no cluster do HDInsight e, em seguida, expanda **registo de serviço do Hadoop**. Por fim, selecione o ficheiro de registo para ver.

> [!NOTE]  
> Os registos são armazenados na conta de armazenamento do Azure que é utilizada pelo seu cluster. Para ver os registos no Visual Studio, tem de iniciar sessão subscrição do Azure, que é proprietário da conta de armazenamento.

### <a name="view-error-information"></a>Ver informações de erro

Para ver os erros que ocorreram numa topologia em execução, utilize os seguintes passos:

1. Partir **Explorador de servidores**, o Storm num cluster do HDInsight com o botão direito e selecione **topologias do Storm de vista**.

2. Para o **Spout** e **Bolts**, o **último erro** coluna contém informações sobre o último erro.

3. Selecione o **Id de Spout** ou **Bolt Id** para o componente que tem um erro listado. Na página de detalhes, que é apresentado, adicional erro informações estão listadas na **erros** seção na parte inferior da página.

4. Para obter mais informações, selecione um **porta** partir do **executores** secção da página, para ver o registo de função de trabalho do Storm nos últimos minutos.

### <a name="errors-submitting-topologies"></a>Submeter as topologias de erros

Se encontrar erros submeter uma topologia para HDInsight, pode encontrar registos para os componentes de servidor que tratam de submissão de topologia no seu cluster do HDInsight. Para obter estes registos, utilize o seguinte comando numa linha de comandos:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Substitua __sshuser__ com a conta de utilizador SSH para o cluster. Substitua __clustername__ com o nome do HDInsight cluster. Para obter mais informações sobre como usar `scp` e `ssh` com o HDInsight, veja [utilizar o SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

As submissões podem falhar por vários motivos:

* JDK não está instalado ou não está no caminho.
* Dependências de Java necessárias não estão incluídas no envio.
* Dependências de incompatíveis.
* Duplicar nomes de topologia.

Se o `hdinsight-scpwebapi.out` registo contém um `FileNotFoundException`, isto pode dever-se as seguintes condições:

* O JDK não está a ser o caminho no ambiente de desenvolvimento. Certifique-se de que está instalado no ambiente de desenvolvimento e de que o JDK `%JAVA_HOME%/bin` está no caminho.
* Uma dependência de Java estão em falta. Certifique-se de que está a incluir todos os ficheiros. JAR necessário como parte de envio.

## <a name="next-steps"></a>Passos Seguintes

Para obter um exemplo de processamento de dados dos Hubs de eventos, consulte [processar eventos dos Hubs de eventos do Azure com o Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Para obter um exemplo de uma topologia de c# que divide a transmitir dados em vários fluxos, consulte [exemplo de c# Storm](https://github.com/Blackmist/csharp-storm-example).

Descobrir mais informações sobre como criar topologias c#, veja [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para obter mais maneiras de trabalhar com o HDInsight e mais Storm no HDInsight exemplos, consulte os seguintes documentos:

**Microsoft SCP.NET**

* [Guia de programação do SCP](apache-storm-scp-programming-guide.md)

**O Apache Storm no HDInsight**

* [Implementar e monitorizar topologias Apache Storm no HDInsight](apache-storm-deploy-monitor-topology.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)

**Apache Hadoop no HDInsight**

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](../hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](../hadoop/hdinsight-use-pig.md)
* [Utilizar o Apache Hadoop MapReduce com o Apache Hadoop no HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**O Apache HBase no HDInsight**

* [Introdução ao Apache HBase no HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
