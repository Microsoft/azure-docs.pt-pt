---
title: 'Tutorial: Criar um aplicativo de tradução com o C# WPF,-API de tradução de texto'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo Windows Presentation Foundation (WPF) que usa APIs de serviço cognitiva para tradução de texto, detecção de idioma e verificação ortográfica com uma única chave de assinatura. Este exercício mostrará como usar os recursos do API de Tradução de Texto e Verificação Ortográfica do Bing API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: b929d0c0da2a812a1c8595536f09931e4edd0fd9
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594914"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Tutorial: Criar um aplicativo de tradução com o WPF

Neste tutorial, você criará um aplicativo [Windows Presentation Foundation (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2017) que usa os serviços cognitivas do Azure para tradução de texto, detecção de idioma e verificação ortográfica com uma única chave de assinatura. Especificamente, seu aplicativo chamará APIs de Tradução de Texto e [verificação ortográfica do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/).

O que é o WPF? É uma estrutura de interface do usuário que cria aplicativos cliente de desktop. A plataforma de desenvolvimento do WPF dá suporte a um amplo conjunto de recursos de desenvolvimento de aplicativos, incluindo um modelo de aplicativo, recursos, controles, elementos gráficos, layout, vinculação de dados, documentos e segurança. É um subconjunto da .NET Framework, portanto, se você já tiver criado aplicativos com o .NET Framework usando ASP.NET ou Windows Forms, a experiência de programação deverá ser familiar. O WPF usa o Extensible app Markup Language (XAML) para fornecer um modelo declarativo para programação de aplicativo, que vamos examinar nas próximas seções.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um projeto do WPF no Visual Studio
> * Adicionar assemblies e pacotes NuGet ao seu projeto
> * Criar a interface do usuário do seu aplicativo com XAML
> * Usar o API de Tradução de Texto para obter idiomas, traduzir texto e detectar o idioma de origem
> * Usar a API Verificação Ortográfica do Bing para validar sua entrada e melhorar a precisão da tradução
> * Executar seu aplicativo WPF

### <a name="cognitive-services-used-in-this-tutorial"></a>Serviços cognitivas usados neste tutorial

Essa lista inclui os serviços cognitivas usados neste tutorial. Siga o link para procurar a referência de API para cada recurso.

| Serviço | Funcionalidade | Descrição |
|---------|---------|-------------|
| Texto do Tradutor | [Obter idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Recupere uma lista completa de idiomas com suporte para tradução de texto. |
| Texto do Tradutor | [Traduzir](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Traduza o texto em mais de 60 idiomas. |
| Texto do Tradutor | [Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Detectar o idioma do texto de entrada. Inclui a pontuação de confiança para detecção. |
| Verificação Ortográfica do Bing | [Verificação Ortográfica](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Corrigir erros de ortografia para melhorar a precisão da tradução. |

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, você precisará do seguinte:

* Uma assinatura dos serviços cognitivas do Azure. [Obtenha uma chave de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#multi-service-resource).
* Um computador Windows
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) – Comunidade ou empresa

> [!NOTE]
> É recomendável criar a assinatura na região oeste dos EUA para este tutorial. Caso contrário, você precisará alterar os pontos de extremidade e as regiões no código conforme você trabalha neste exercício.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Criar um aplicativo WPF no Visual Studio

A primeira coisa que precisamos fazer é configurar nosso projeto no Visual Studio.

1. Abra o Visual Studio. Em seguida, selecione **arquivo > novo projeto de >** .
2. No painel esquerdo, localize e selecione **Visual C#** . Em seguida, selecione **aplicativo WPF (.NET Framework)** no painel central.
   ![Criar um aplicativo WPF no Visual Studio](media/create-wpf-project-visual-studio.png)
3. Nomeie seu projeto `MSTranslatorTextDemo`, defina a versão do Framework como **.NET Framework 4.5.2 ou posterior**e clique em **OK**.
4. Seu projeto foi criado. Você observará que há duas guias abertas: `MainWindow.xaml` e. `MainWindow.xaml.cs` Ao longo deste tutorial, adicionaremos código a esses dois arquivos. O primeiro para a interface do usuário do aplicativo; o último para nossas chamadas para Tradução de Texto e Verificação Ortográfica do Bing.
   ![Examine seu ambiente](media/blank-wpf-project.png)

Na próxima seção, vamos adicionar assemblies e um pacote NuGet ao nosso projeto para obter funcionalidade adicional, como a análise de JSON.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Adicionar referências e pacotes NuGet ao seu projeto

Nosso projeto requer alguns .NET Framework assemblies e o NewtonSoft. JSON, que instalaremos usando o Gerenciador de pacotes NuGet.

### <a name="add-net-framework-assemblies"></a>Adicionar assemblies de .NET Framework

Vamos adicionar assemblies ao nosso projeto para serializar e desserializar objetos e para gerenciar solicitações e respostas HTTP.

1. Localize seu projeto no Gerenciador de Soluções do Visual Studio (painel direito). Clique com o botão direito do mouse em seu projeto e selecione **adicionar > referência...** , que abre o **Gerenciador de referências**.
   ![Adicionar referências de assembly](media/add-assemblies-sample.png)
2. A guia assemblies lista todos os assemblies .NET Framework que estão disponíveis para referência. Use a barra de pesquisa no canto superior direito da tela para pesquisar essas referências e adicioná-las ao seu projeto:
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Web.Extensions](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
3. Depois de adicionar essas referências ao seu projeto, você pode clicar em **OK** para fechar o **Gerenciador de referências**.

> [!NOTE]
> Se você quiser saber mais sobre referências de assembly, consulte [como: Adicionar ou remover referência usando o Gerenciador](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2017)de referências.

### <a name="install-newtonsoftjson"></a>Instalar o NewtonSoft. JSON

Nosso aplicativo usará NewtonSoft. JSON para desserializar objetos JSON. Siga estas instruções para instalar o pacote.

1. Localize seu projeto no Gerenciador de Soluções do Visual Studio e clique com o botão direito do mouse no seu projeto. Selecione **gerenciar pacotes NuGet...** .
2. Localize e selecione a guia **procurar** .
3. Digite [NewtonSoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) na barra de pesquisa.
   ![Localize e instale o NewtonSoft. JSON](media/add-nuget-packages.png)
4. Selecione o pacote e clique em **instalar**.
5. Quando a instalação for concluída, feche a guia.

## <a name="create-a-wpf-form-using-xaml"></a>Criar um formulário WPF usando XAML

Para usar seu aplicativo, você precisará de uma interface do usuário. Usando o XAML, criaremos um formulário que permite aos usuários selecionar idiomas de entrada e de tradução, inserir texto a ser convertido e exibir a saída da tradução.

Vamos dar uma olhada no que estamos criando.

![Interface do usuário XAML WPF](media/translator-text-csharp-xaml.png)

O interfigurador de usuário inclui estes componentes:

| Nome | Tipo | Descrição |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | Exibe uma lista dos idiomas com suporte do Microsoft Translator para tradução de texto. O utilizador seleciona o idioma de origem que está a traduzir. |
| `ToLanguageComboBox` | ComboBox | Exibe a mesma lista de idiomas que `FromComboBox`, mas é usada para selecionar o idioma para o qual o usuário está convertendo. |
| `TextToTranslate` | TextBox | Permite que o usuário insira o texto a ser traduzido. |
| `TranslateButton` | Botão | Use esse botão para traduzir o texto. |
| `TranslatedTextLabel` | Etiqueta | Exibe a tradução. |
| `DetectedLanguageLabel` | Etiqueta | Exibe o idioma detectado do texto a ser traduzido (`TextToTranslate`). |

> [!NOTE]
> No entanto, estamos criando esse formulário usando o código-fonte XAML. você pode criar o formulário com o editor no Visual Studio.

Vamos adicionar o código ao nosso projeto.

1. No Visual Studio, selecione a guia para `MainWindow.xaml`.
2. Copie esse código em seu projeto e salve-o.
   ```xaml
   <Window x:Class="MSTranslatorTextDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorTextDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
3. Agora você deve ver uma visualização da interface do usuário do aplicativo no Visual Studio. Ele deve ser semelhante à imagem acima.

É isso, seu formulário está pronto. Agora, vamos escrever um código para usar a tradução de texto e Verificação Ortográfica do Bing.

> [!NOTE]
> Sinta-se à vontade para ajustar esse formulário ou criar o seu próprio.

## <a name="create-your-app"></a>Criar seu aplicativo

`MainWindow.xaml.cs`contém o código que controla nosso aplicativo. Nas próximas seções, vamos adicionar o código para preencher nossos menus suspensos e chamar uma série de APIs expostas por Tradução de Texto e Verificação Ortográfica do Bing.

* Quando o programa é iniciado `MainWindow` e é instanciado, `Languages` o método da API de tradução de texto é chamado para recuperar e preencher os menus suspensos de seleção de idioma. Isso acontece uma vez no início de cada sessão.
* Quando o botão **traduzir** é clicado, a seleção de idioma e o texto do usuário são recuperados, a verificação ortográfica é executada na entrada e a tradução e o idioma detectado são exibidos para o usuário.
  * O `Translate` método do API de tradução de texto é chamado para converter texto de `TextToTranslate`. Essa chamada também inclui os `to` idiomas `from` e selecionados usando os menus suspensos.
  * O `Detect` método da API de tradução de texto é chamado para determinar o idioma do texto de `TextToTranslate`.
  * Verificação ortográfica do Bing é usado para validar `TextToTranslate` e ajustar erros de ortografia.

Todo o `MainWindow : Window` nosso projeto é encapsulado na classe. Vamos começar adicionando o código para definir sua chave de assinatura, declarar pontos de extremidade para Tradução de Texto e Verificação Ortográfica do Bing e inicializar o aplicativo.

1. No Visual Studio, selecione a guia para `MainWindow.xaml.cs`.
2. Substitua as instruções preenchidas `using` previamente pelo seguinte.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
3. Localize a `MainWindow : Window` classe e substitua-a por este código:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
   1. Adicione sua chave de assinatura de serviços cognitivas e salve.

Nesse bloco de código, declaramos duas variáveis de membro que contêm informações sobre os idiomas disponíveis para tradução:

| Variável | Type | Descrição |
|----------|------|-------------|
|`languageCodes` | matriz de cadeias de caracteres |C aches os códigos de idioma. O serviço Translator utiliza códigos curtos, como `en` para inglês, para identificar idiomas. |
|`languageCodesAndTitles` | Dicionário classificado | Mapeia os nomes "amigáveis" na interface do utilizador de volta para os códigos curtos utilizados na API. São mantidos ordenados por ordem alfabética, sem ter em conta as maiúsculas/minúsculas. |

Em seguida, dentro `MainWindow` do Construtor, adicionamos o tratamento de `HandleExceptions`erros com. Isso garante que um alerta seja fornecido se uma exceção não for tratada. Em seguida, uma verificação é executada para confirmar se a chave de assinatura fornecida tem 32 caracteres de comprimento. Um erro será gerado se a chave for menor que/maior que 32 caracteres.

Se houver chaves que tenham pelo menos o comprimento certo, a `InitializeComponent()` chamada obterá a interface do usuário sem a localização, o carregamento e a instanciação da descrição XAML da janela principal do aplicativo.

Por fim, adicionamos o código para chamar métodos para recuperar idiomas para tradução e para preencher os menus suspensos para a interface do usuário de nosso aplicativo. Não se preocupe, obteremos o código por trás dessas chamadas em breve.

## <a name="get-supported-languages"></a>Obter idiomas suportados

O API de Tradução de Texto atualmente dá suporte a mais de 60 idiomas. Como o novo suporte a idioma será adicionado ao longo do tempo, é recomendável chamar o recurso de idiomas exposto por Tradução de Texto em vez de codificar a lista de idiomas em seu aplicativo.

Nesta seção, criaremos uma `GET` solicitação para o recurso de idiomas, especificando que queremos uma lista de idiomas disponíveis para tradução.

> [!NOTE]
> O recurso de idiomas permite filtrar o suporte a idiomas com os seguintes parâmetros de consulta: transliteração, dicionário e tradução. Para obter mais informações, consulte [referência de API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Antes de continuarmos, vamos dar uma olhada em uma saída de exemplo para uma chamada para o recurso de idiomas:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

A partir dessa saída, podemos extrair o código de idioma e `name` o de um idioma específico. Nosso aplicativo usa NewtonSoft. JSON para desserializar o objeto JSON ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Selecionando onde parou na última seção, vamos adicionar um método para obter os idiomas com suporte para nosso aplicativo.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

O `GetLanguagesForTranslate()` método cria uma solicitação HTTP Get e usa o `scope=translation` parâmetro de cadeia de caracteres de consulta usado para limitar o escopo da solicitação a idiomas com suporte para tradução. O cabeçalho `Accept-Language` com o valor `en` é adicionado para que os idiomas suportados sejam devolvidos em inglês.

A resposta JSON é analisada e convertida em um dicionário. Em seguida, os códigos de idioma são `languageCodes` adicionados à variável de membro. Os pares de chave/valor que contêm os códigos de idioma e os nomes amigáveis de idiomas são colocados num ciclo e adicionados à variável de membro `languageCodesAndTitles`. Os menus suspensos no formulário exibem os nomes amigáveis, mas os códigos são necessários para solicitar a tradução.

## <a name="populate-language-drop-down-menus"></a>Preencher menus suspensos de idioma

A interface do usuário é definida usando XAML, portanto, você não precisa fazer muito para configurá-la além `InitializeComponent()`da chamada. A única coisa que você precisa fazer é adicionar os nomes de idiomas amigáveis aos menus suspensos **converter** e **converter em** , isso é feito com o `PopulateLanguageMenus()` método.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto abaixo do `GetLanguagesForTranslate()` método:
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Esse método itera sobre o `languageCodesAndTitles` dicionário e adiciona cada chave aos dois menus. Depois que os menus são preenchidos, os idiomas padrão de e para são definidos para **detectar** e **em inglês** , respectivamente.

> [!TIP]
> Sem uma seleção predefinida para os menus, o utilizador pode clicar em **Traduzir**, sem primeiro escolher um idioma "para" ou "de". As predefinições eliminam a necessidade de lidar com este problema.

Agora que `MainWindow` foi inicializado e a interface do usuário criada, esse código não será executado até que o botão **traduzir** seja clicado.

## <a name="detect-language-of-source-text"></a>Detectar idioma do texto de origem

Agora, vamos criar um método para detectar o idioma do texto de origem (texto inserido em nossa área de texto) usando o API de Tradução de Texto. O valor retornado por essa solicitação será usado em nossa solicitação de tradução posteriormente.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto abaixo do `PopulateLanguageMenus()` método:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Esse método cria uma solicitação `POST` http para o recurso de detecção. Ele usa um único argumento, `text`que é passado como o corpo da solicitação. Mais tarde, quando criamos nossa solicitação de tradução, o texto inserido em nossa interface do usuário será passado para esse método para detecção de idioma.

Além disso, esse método avalia a pontuação de confiança da resposta. Se a pontuação for maior que `0.5`, o idioma detectado será exibido em nossa interface do usuário.

## <a name="spell-check-the-source-text"></a>Verificar a ortografia do texto de origem

Agora vamos criar um método para verificar a ortografia de nosso texto de origem usando a API Verificação Ortográfica do Bing. Isso garante que obteremos traduções precisas de API de Tradução de Texto. As correções no texto de origem são passadas junto em nossa solicitação de tradução quando o botão **traduzir** é clicado.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto abaixo do `DetectLanguage()` método:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Traduzir texto ao clicar

A última coisa que precisamos fazer é criar um método que é invocado quando o botão **traduzir** em nossa interface do usuário é clicado.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto abaixo do `CorrectSpelling()` método e salve:  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "app/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

A primeira etapa é obter os idiomas "from" e "to" e o texto inserido pelo usuário em nosso formulário. Se o idioma de origem estiver definido como detectar `DetectLanguage()` , será chamado para determinar o idioma do texto de origem. O texto pode estar em um idioma ao qual a API do tradutor não dá suporte. Nesse caso, exiba uma mensagem para informar o usuário e retorne sem traduzir o texto.

Se o idioma de origem é inglês (seja especificado ou detetado), verifique a ortografia de texto com `CorrectSpelling()` e aplique as correções precisas. O texto corrigido é adicionado de volta à área de texto para que o usuário veja que foi feita uma correção.

O código para traduzir o texto deve parecer familiar: criar o URI, criar uma solicitação, enviá-la e analisar a resposta. A matriz JSON pode conter mais de um objeto para tradução, no entanto, nosso aplicativo requer apenas um.

Após uma solicitação bem-sucedida, `TranslatedTextLabel.Content` é substituído `translation`pelo, que atualiza a interface do usuário para exibir o texto traduzido.

## <a name="run-your-wpf-app"></a>Executar seu aplicativo WPF

É isso, você tem um aplicativo de tradução funcional criado usando o WPF. Para executar seu aplicativo, clique no botão **Iniciar** no Visual Studio.

## <a name="source-code"></a>Código de origem

O código-fonte deste projeto está disponível no GitHub.

* [Explorar código-fonte](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API de Texto do Microsoft Translator](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
