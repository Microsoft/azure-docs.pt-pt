---
title: Topologias de Apache Storm com o C# Visual Studio e o Azure HDInsight
description: Saiba como criar topologias Storm no C#. Crie uma topologia de contagem de palavras simples no Visual Studio usando as ferramentas do Hadoop para Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: e6f6ba131a4fb5dd31f113afd2b6de2d65aeaea0
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915165"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Desenvolver C# topologias para Apache Storm usando as ferramentas de data Lake para o Visual Studio

Saiba como criar uma topologia C# de Apache Storm usando as ferramentas do Azure Data Lake (Apache Hadoop) para o Visual Studio. Este documento explica o processo de criar um projeto Storm no Visual Studio, testá-lo localmente e implantá-lo em um Apache Storm no cluster HDInsight do Azure.

Você também aprenderá a criar topologias híbridas C# que usam componentes Java e.

> [!NOTE]  
> Embora as etapas neste documento dependam de um ambiente de desenvolvimento do Windows com o Visual Studio, o projeto compilado pode ser enviado para um cluster HDInsight baseado em Linux ou Windows. Somente os clusters baseados em Linux criados após 28 de outubro de 2016 oferecem suporte a topologias SCP.NET.

Para usar uma C# topologia com um cluster baseado em Linux, você deve atualizar o pacote NuGet Microsoft. scp. net. SDK usado pelo seu projeto para a versão 0.10.0.6 ou posterior. A versão do pacote também tem de corresponder à versão principal do Storm instalada no HDInsight.

| Versão do HDInsight | Versão do Apache Storm | Versão do SCP.NET | Versão padrão do mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(somente no HDInsight baseado em Windows) | ND |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> As topologias C# em clusters baseados em Linux têm de utilizar o .NET 4.5 e utilizar o Mono para serem executadas no cluster do HDInsight. Verifique a [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/) para obter possíveis incompatibilidades.

## <a name="install-visual-studio"></a>Instalar o Visual Studio

Você pode desenvolver C# topologias com o scp.NET usando uma das seguintes versões do Visual Studio:

* Visual Studio 2012 com atualização 4

* Visual Studio 2013 com a atualização 4 ou [Visual Studio 2013 comunidade](https://go.microsoft.com/fwlink/?LinkId=517284)

* Comunidade do Visual Studio 2015 ou [visual studio 2015](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (qualquer edição)

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar o Data Lake Tools para Visual Studio

Para instalar o Data Lake Tools para Visual Studio, siga as etapas em Introdução ao [uso de ferramentas de data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Instalar o Java

Quando você envia uma topologia Storm do Visual Studio, o SCP.NET gera um arquivo zip que contém a topologia e as dependências. O Java é usado para criar esses arquivos zip, pois ele usa um formato mais compatível com clusters baseados em Linux.

1. Instale o Java Developer Kit (JDK) 7 ou posterior em seu ambiente de desenvolvimento. Você pode obter o Oracle JDK da [Oracle](https://aka.ms/azure-jdks). Você também pode usar [outras distribuições de Java](https://openjdk.java.net/).

2. A `JAVA_HOME` variável de ambiente deve apontar para o diretório que contém Java.

3. A `PATH` variável de ambiente deve incluir `%JAVA_HOME%\bin` o diretório.

Você pode usar o seguinte C# aplicativo de console para verificar se o Java e o JDK estão instalados corretamente:

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

## <a name="apache-storm-templates"></a>Modelos de Apache Storm

As ferramentas de Data Lake para o Visual Studio fornecem os seguintes modelos:

| Tipo de projeto | Demonstrar |
| --- | --- |
| Aplicativo Storm |Um projeto de topologia Storm vazio. |
| Exemplo de gravador do SQL do Storm Azure |Como gravar no banco de dados SQL do Azure. |
| Exemplo de leitor de Azure Cosmos DB Storm |Como ler de Azure Cosmos DB. |
| Exemplo de gravador de Azure Cosmos DB Storm |Como gravar em Azure Cosmos DB. |
| Exemplo de leitor de EventHub do Storm |Como ler os hubs de eventos do Azure. |
| Exemplo de gravador de EventHub do Storm |Como gravar nos hubs de eventos do Azure. |
| Exemplo de leitor HBase do Storm |Como ler do HBase em clusters HDInsight. |
| Exemplo de gravador HBase do Storm |Como gravar no HBase em clusters HDInsight. |
| Exemplo híbrido do Storm |Como usar um componente Java. |
| Exemplo do Storm |Uma topologia básica de contagem de palavras. |

> [!WARNING]  
> Nem todos os modelos funcionam com o HDInsight baseado em Linux. Os pacotes NuGet usados pelos modelos podem não ser compatíveis com o mono. Verifique o documento de [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/) e use o [.net Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) para identificar possíveis problemas.

Nas etapas deste documento, você usa o tipo de projeto de aplicativo Storm básico para criar uma topologia.

### <a name="apache-hbase-templates-notes"></a>Notas de modelos do Apache HBase

Os modelos de leitor e gravador do HBase usam a API REST do HBase, não a API do Java do HBase, para se comunicar com um HBase no cluster HDInsight.

### <a name="eventhub-templates-notes"></a>Notas de modelos do EventHub

> [!IMPORTANT]  
> O componente Spout do EventHub baseado em Java incluído com o modelo de leitor do EventHub pode não funcionar com o Storm no HDInsight versão 3,5 ou posterior. Uma versão atualizada deste componente está disponível no [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Para obter um exemplo de topologia que usa esse componente e funciona com o Storm no HDInsight 3,5, consulte [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Criar uma C# topologia

1. Abra o Visual Studio, selecione **arquivo** > **novo**e, em seguida, selecione **projeto**.

2. Na janela **novo projeto** , expanda**modelos** **instalados** > e selecione **Azure data Lake**. Na lista de modelos, selecione **aplicativo Storm**. Na parte inferior da tela, digite **WordCount** como o nome do aplicativo.

    ![Captura de tela da janela novo projeto](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Depois de criar o projeto, você deverá ter os seguintes arquivos:

   * **Program.cs**: Esse arquivo define a topologia do seu projeto. Uma topologia padrão que consiste em um Spout e um parafuso é criada por padrão.

   * **Spout.cs**: Um exemplo de Spout que emite números aleatórios.

   * **Bolt.cs**: Um rolo de exemplo que mantém uma contagem de números emitidos pelo Spout.

     Quando você cria o projeto, o NuGet baixa o [pacote SCP.net](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)mais recente.

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementar o Spout

1. Abra **Spout.cs**. Os esgotamentos são usados para ler dados em uma topologia de uma fonte externa. Os principais componentes para um Spout são:

   * **NextTuple**: Chamado pelo Storm quando o Spout tem permissão para emitir novas tuplas.

   * **Confirmação** (somente topologia transacional): Lida com confirmações iniciadas por outros componentes na topologia para tuplas enviadas do Spout. A confirmação de uma tupla permite que o Spout saiba que foi processado com êxito pelos componentes downstream.

   * **Falha** (somente topologia transacional): Lida com tuplas que executam o processamento de outros componentes na topologia. A implementação de um método Fail permite que você emita novamente a tupla para que ela possa ser processada novamente.

2. Substitua o conteúdo da classe **Spout** pelo seguinte texto: Esse Spout emite aleatoriamente uma frase na topologia.

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

1. Exclua o arquivo **Bolt.cs** existente do projeto.

2. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **novo item**. Na lista, selecione **parafuso do Storm**e insira **Splitter.cs** como o nome. Repita esse processo para criar um segundo parafuso chamado **Counter.cs**.

   * **Splitter.cs**: Implementa um raio que divide frases em palavras individuais e emite um novo fluxo de palavras.

   * **Counter.cs**: Implementa um raio que conta cada palavra e emite um novo fluxo de palavras e a contagem de cada palavra.

     > [!NOTE]  
     > Esses parafusos são lidos e gravados em fluxos, mas você também pode usar um raio para se comunicar com fontes como um banco de dados ou serviço.

3. Abra **Splitter.cs**. Ele tem apenas um método por padrão: **Execute**. O método Execute é chamado quando o raio recebe uma tupla para processamento. Aqui, você pode ler e processar tuplas de entrada e emitir tuplas de saída.

4. Substitua o conteúdo da classe do **divisor** pelo código a seguir:

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

5. Abra **Counter.cs**e substitua o conteúdo da classe pelo seguinte código:

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

Esgotamentos e parafusos são organizados em um grafo, que define como os dados fluem entre os componentes. Para essa topologia, o grafo é o seguinte:

![Diagrama de como os componentes são organizados](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

As frases são emitidas do Spout e são distribuídas para instâncias do parafuso do divisor. O parafuso do divisor divide as frases em palavras, que são distribuídas para o parafuso do contador.

Como a contagem de palavras é mantida localmente na instância do contador, você deseja garantir que as palavras específicas fluam para a mesma instância do balcão do contador. Cada instância mantém o controle de palavras específicas. Como o parafuso do separador não mantém nenhum estado, na verdade não importa qual instância do divisor recebe qual frase.

Abra **Program.cs**. O método importante é **GetTopologyBuilder**, que é usado para definir a topologia que é enviada ao Storm. Substitua o conteúdo de **GetTopologyBuilder** pelo código a seguir para implementar a topologia descrita anteriormente:

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

## <a name="submit-the-topology"></a>Enviar a topologia

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**.

   > [!NOTE]  
   > Se solicitado, insira as credenciais para sua assinatura do Azure. Se você tiver mais de uma assinatura, entre com a que contém o Storm no cluster HDInsight.

2. Selecione seu Storm no cluster HDInsight na lista suspensa **cluster Storm** e, em seguida, selecione **Enviar**. Você pode monitorar se o envio foi bem-sucedido usando a janela **saída** .

3. Quando a topologia for enviada com êxito, as **topologias Storm** para o cluster devem aparecer. Selecione a topologia **WordCount** na lista para exibir informações sobre a topologia em execução.

   > [!NOTE]  
   > Você também pode exibir **topologias Storm** de **Gerenciador de servidores**. Expanda **Azure** > **HDInsight**, clique com o botão direito do mouse em um cluster Storm no HDInsight e selecione **Exibir topologias Storm**.

    Para exibir informações sobre os componentes na topologia, clique duas vezes no componente no diagrama.

4. Na exibição de **Resumo da topologia** , clique em **eliminar** para interromper a topologia.

   > [!NOTE]  
   > As topologias do Storm continuam a ser executadas até que sejam desativadas ou o cluster seja excluído.

## <a name="transactional-topology"></a>Topologia transacional

A topologia anterior não é transacional. Os componentes na topologia não implementam a funcionalidade de reprodução de mensagens. Para obter um exemplo de uma topologia transacional, crie um projeto e selecione **amostra do Storm** como o tipo de projeto.

As topologias transacionais implementam o seguinte para oferecer suporte à reprodução de dados:

* **Cache de metadados**: O Spout deve armazenar metadados sobre os dados emitidos, para que os dados possam ser recuperados e emitidos novamente se ocorrer uma falha. Como os dados emitidos pelo exemplo são pequenos, os dados brutos de cada tupla são armazenados em um dicionário para reprodução.

* **ACK**: Cada parafuso na topologia pode chamar `this.ctx.Ack(tuple)` para confirmar que ele processou com êxito uma tupla. Quando todos os parafusos confirmaram a tupla, o `Ack` método de Spout é invocado. O `Ack` método permite que o Spout remova os dados que foram armazenados em cache para reprodução.

* **Falha**: Cada raio pode chamar `this.ctx.Fail(tuple)` para indicar que o processamento falhou para uma tupla. A falha é propagada `Fail` para o método do Spout, em que a tupla pode ser reproduzida usando metadados armazenados em cache.

* **ID da sequência**: Ao emitir uma tupla, uma ID de sequência exclusiva pode ser especificada. Esse valor identifica a tupla para processamento de reprodução (ACK e Fail). Por exemplo, o Spout no projeto de **exemplo do Storm** usa o seguinte ao emitir dados:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Esse código emite uma tupla que contém uma frase para o fluxo padrão, com o valor de ID de sequência contido em **lastSeqId**. Para este exemplo, **lastSeqId** é incrementado para cada tupla emitida.

Conforme demonstrado no projeto de **exemplo do Storm** , se um componente é transacional pode ser definido em tempo de execução, com base na configuração.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia híbrida com C# e Java

Você também pode usar as ferramentas de Data Lake para o Visual Studio para criar topologias híbridas C# , em que alguns componentes são e outros são Java.

Para obter um exemplo de uma topologia híbrida, crie um projeto e selecione **exemplo híbrido do Storm**. Este tipo de exemplo demonstra os seguintes conceitos:

* **Spout** e  **C# parafuso**do Java: Definido em **HybridTopology_javaSpout_csharpBolt**.

    * Uma versão transacional é definida em **HybridTopologyTx_javaSpout_csharpBolt**.

* Spout e **parafuso Java**: **C#** Definido em **HybridTopology_csharpSpout_javaBolt**.

    * Uma versão transacional é definida em **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]  
  > Esta versão também demonstra como usar o código Clojure de um arquivo de texto como um componente Java.


Para alternar a topologia que é usada quando o projeto é enviado, mova a `[Active(true)]` instrução para a topologia que você deseja usar antes de enviá-la ao cluster.

> [!NOTE]  
> Todos os arquivos Java necessários são fornecidos como parte desse projeto na pasta **JavaDependency** .

Considere o seguinte ao criar e enviar uma topologia híbrida:

* Use **JavaComponentConstructor** para criar uma instância da classe Java para um Spout ou um parafuso.

* Use **Microsoft. scp. Storm. Multilang. CustomizedInteropJSONSerializer** para serializar dados dentro ou fora de componentes Java de objetos Java para JSON.

* Ao enviar a topologia para o servidor, você deve usar a opção **configurações adicionais** para especificar os **caminhos de arquivo Java**. O caminho especificado deve ser o diretório que contém os arquivos JAR que contêm suas classes Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

A versão SCP.NET 0.9.4.203 introduz uma nova classe e um método especificamente para trabalhar com o Hub de eventos Spout (um Spout de Java que lê de hubs de eventos). Quando você cria uma topologia que usa um Spout de Hub de eventos, use os seguintes métodos:

* Classe **EventHubSpoutConfig** : Cria um objeto que contém a configuração para o componente Spout.

* Método **TopologyBuilder. SetEventHubSpout** : Adiciona o componente Spout do hub de eventos à topologia.

> [!NOTE]  
> Você ainda deve usar o **CustomizedInteropJSONSerializer** para serializar dados produzidos pelo Spout.

## <a id="configurationmanager"></a>Usar ConfigurationManager

Não use **ConfigurationManager** para recuperar valores de configuração dos componentes de Spout e de base. Isso pode causar uma exceção de ponteiro NULL. Em vez disso, a configuração do seu projeto é passada para a topologia do Storm como um par de chave e valor no contexto de topologia. Cada componente que se baseia em valores de configuração deve recuperá-los do contexto durante a inicialização.

O código a seguir demonstra como recuperar esses valores:

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

Se você usar um `Get` método para retornar uma instância do componente, deverá garantir que ele passe os `Context` parâmetros e `Dictionary<string, Object>` para o construtor. O exemplo a seguir é um `Get` método básico que passa corretamente esses valores:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Como atualizar o SCP.NET

Versões recentes da atualização do pacote de suporte do SCP.NET por meio do NuGet. Quando uma nova atualização estiver disponível, você receberá uma notificação de atualização. Para verificar manualmente se há uma atualização, siga estas etapas:

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet**.

2. No Gerenciador de pacotes, selecione **atualizações**. Se uma atualização estiver disponível, ela será listada. Clique em **Atualizar** para o pacote para instalá-lo.

> [!IMPORTANT]  
> Se o projeto foi criado com uma versão anterior do SCP.NET que não usou o NuGet, você deve executar as seguintes etapas para atualizar para uma versão mais recente:
>
> 1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet**.
> 2. Usando o campo de **pesquisa** , procure e adicione, **Microsoft. scp. net. SDK** , ao projeto.

## <a name="troubleshoot-common-issues-with-topologies"></a>Solucionar problemas comuns com topologias

### <a name="null-pointer-exceptions"></a>Exceções de ponteiro nulo

Quando você está usando uma C# topologia com um cluster HDInsight baseado em Linux, os componentes de Spout e do componente que usam **ConfigurationManager** para ler definições de configuração em tempo de execução podem retornar exceções de ponteiro nulo.

A configuração do seu projeto é passada para a topologia do Storm como um par de chave e valor no contexto de topologia. Ele pode ser recuperado do objeto Dictionary que é passado para seus componentes quando eles são inicializados.

Para obter mais informações, consulte a seção [ConfigurationManager](#configurationmanager) deste documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Ao usar uma C# topologia com um cluster HDInsight baseado em Linux, você pode encontrar o seguinte erro:

    System.TypeLoadException: Failure has occurred while loading a type.

Esse erro ocorre quando você usa um binário que não é compatível com a versão do .NET à qual o mono dá suporte.

Para clusters HDInsight baseados em Linux, certifique-se de que seu projeto usa binários compilados para o .NET 4,5.

### <a name="test-a-topology-locally"></a>Testar uma topologia localmente

Embora seja fácil implantar uma topologia em um cluster, em alguns casos, talvez seja necessário testar uma topologia localmente. Use as etapas a seguir para executar e testar a topologia de exemplo neste artigo localmente em seu ambiente de desenvolvimento.

> [!WARNING]  
> O teste local funciona apenas para topologias básicas C#somente. Você não pode usar o teste local para topologias híbridas ou topologias que usam vários fluxos.

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Propriedades**. Nas propriedades do projeto, altere o **tipo de saída** para **aplicativo de console**.

    ![Captura de tela das propriedades do projeto, com o tipo de saída realçado](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Lembre-se de alterar o **tipo de saída** para **biblioteca de classes** antes de implantar a topologia em um cluster.

2. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **novo item**. Selecione **classe**e insira **LocalTest.cs** como o nome da classe. Por fim, clique em **Adicionar**.

3. Abra **LocalTest.cs**e adicione a seguinte instrução **using** na parte superior:

    ```csharp
    using Microsoft.SCP;
    ```

4. Use o código a seguir como o conteúdo da classe **LocalTest** :

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

    Reserve um tempo para ler os comentários do código. Esse código usa **LocalContext** para executar os componentes no ambiente de desenvolvimento e ele persiste o fluxo de dados entre componentes em arquivos de texto na unidade local.

1. Abra **Program.cs**e adicione o seguinte ao método **Main** :

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

2. Salve as alterações e, em seguida, clique em **F5** ou selecione **depurar** > **Iniciar Depuração** para iniciar o projeto. Uma janela do console deve aparecer e o status do log como o andamento dos testes. Quando os **testes terminarem** aparecer, pressione qualquer tecla para fechar a janela.

3. Use o **Windows Explorer** para localizar o diretório que contém o projeto. Por exemplo: **C:\Users\<your_user_name > \Documents\Visual Studio 2013 \ Projects\WordCount\WordCount**. Nesse diretório, abra **bin**e clique em **depurar**. Você deve ver os arquivos de texto que foram produzidos quando os testes foram executados: Sentences. txt, Counter. txt e Splitter. txt. Abra cada arquivo de texto e inspecione os dados.

   > [!NOTE]  
   > Os dados de cadeia de caracteres persistem como uma matriz de valores decimais nesses arquivos. Por exemplo, \[[97.103.111]] no arquivo **Splitter. txt** é a palavra *e*.

> [!NOTE]  
> Certifique-se de definir o **tipo de projeto** de volta para biblioteca de **classes** antes de implantar em um Storm no cluster HDInsight.

### <a name="log-information"></a>Informações de log

Você pode registrar facilmente as informações de seus componentes de topologia `Context.Logger`usando o. Por exemplo, o comando a seguir cria uma entrada de log informativo:

```csharp
Context.Logger.Info("Component started");
```

As informações registradas podem ser exibidas no **log do serviço do Hadoop**, que é encontrado em **Gerenciador de servidores**. Expanda a entrada do seu Storm no cluster HDInsight e, em seguida, expanda **log do serviço do Hadoop**. Por fim, selecione o arquivo de log a ser exibido.

> [!NOTE]  
> Os logs são armazenados na conta de armazenamento do Azure que é usada pelo cluster. Para exibir os logs no Visual Studio, você deve entrar na assinatura do Azure que possui a conta de armazenamento.

### <a name="view-error-information"></a>Exibir informações de erro

Para exibir os erros que ocorreram em uma topologia em execução, use as seguintes etapas:

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse no cluster Storm no HDInsight e selecione **Exibir topologias Storm**.

2. Para o **Spout** e os **parafusos**, a coluna **último erro** contém informações sobre o último erro.

3. Selecione a **ID do Spout** ou a **ID do parafuso** do componente que tem um erro listado. Na página de detalhes exibida, informações adicionais sobre o erro são listadas na seção **erros** na parte inferior da página.

4. Para obter mais informações, selecione uma **porta** na seção **executores** da página para ver o log do trabalho do Storm nos últimos minutos.

### <a name="errors-submitting-topologies"></a>Erros ao enviar topologias

Se você encontrar erros ao enviar uma topologia para o HDInsight, poderá encontrar logs para os componentes do lado do servidor que lidam com o envio da topologia em seu cluster HDInsight. Para recuperar esses logs, use o seguinte comando de uma linha de comando:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Substitua __sshuser__ pela conta de usuário SSH para o cluster. Substitua __ClusterName__ pelo nome do cluster HDInsight. Para obter mais informações sobre `scp` como `ssh` usar o e o hdinsight, consulte [usar SSH com hdinsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Os envios podem falhar por vários motivos:

* O JDK não está instalado ou não está no caminho.
* As dependências de Java necessárias não são incluídas no envio.
* Dependências incompatíveis.
* Nomes de topologia duplicados.

Se o `hdinsight-scpwebapi.out` log contiver `FileNotFoundException`um, isso pode ser causado pelas seguintes condições:

* O JDK não está no caminho do ambiente de desenvolvimento. Verifique se o JDK está instalado no ambiente de desenvolvimento e se `%JAVA_HOME%/bin` está no caminho.
* Você não tem uma dependência de Java. Verifique se você está incluindo todos os arquivos. jar necessários como parte do envio.

## <a name="next-steps"></a>Passos seguintes

Para obter um exemplo de processamento de dados de hubs de eventos, consulte [processar eventos dos hubs de eventos do Azure com o Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Para obter um exemplo de C# uma topologia que divide dados de fluxo em vários fluxos, consulte [ C# exemplo do Storm](https://github.com/Blackmist/csharp-storm-example).

Para descobrir mais informações sobre a C# criação de topologias, consulte [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para obter mais maneiras de trabalhar com o HDInsight e mais exemplos do Storm em HDInsight, consulte os seguintes documentos:

**Microsoft SCP.NET**

* [Guia de programação do SCP](apache-storm-scp-programming-guide.md)

**Apache Storm no HDInsight**

* [Implantar e monitorar topologias com Apache Storm no HDInsight](apache-storm-deploy-monitor-topology.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)

**Apache Hadoop no HDInsight**

* [Usar Apache Hive com Apache Hadoop no HDInsight](../hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o Apache Hadoop no HDInsight](../hadoop/hdinsight-use-pig.md)
* [Usar Apache Hadoop MapReduce com Apache Hadoop no HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase no HDInsight**

* [Introdução ao Apache HBase no HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
