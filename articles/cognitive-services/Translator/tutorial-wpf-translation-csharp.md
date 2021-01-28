---
title: 'Tutorial: Criar uma aplicação de tradução com WPF, C# - Tradutor'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma aplicação WPF para realizar tradução de texto, deteção de idiomas e verificação ortográfica com uma única chave de subscrição.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 05/26/2020
ms.author: lajanuar
ms.custom: devx-track-csharp
ms.openlocfilehash: dd5b83908ae7c6d62acd3391933028685facf755
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927481"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Tutorial: Criar uma aplicação de tradução com o WPF

Neste tutorial, você vai construir uma aplicação [da Windows Presentation Foundation (WPF)](/visualstudio/designers/getting-started-with-wpf) que utiliza os Serviços Cognitivos Azure para tradução de texto, deteção de idiomas e verificação ortográfica com uma única chave de subscrição. Especificamente, a sua aplicação irá ligar para APIs do Tradutor e [Bing Spell Check](https://azure.microsoft.com/services/cognitive-services/spell-check/).

O que é WPF? É uma estrutura de UI que cria aplicações de clientes de desktop. A plataforma de desenvolvimento WPF suporta um vasto conjunto de funcionalidades de desenvolvimento de aplicações, incluindo um modelo de aplicações, recursos, controlos, gráficos, layout, ligação de dados, documentos e segurança. É um subconjunto do Quadro .NET, por isso, se já construiu aplicações com o Quadro .NET utilizando ASP.NET ou Formas do Windows, a experiência de programação deve ser familiar. O WPF utiliza a aplicação extensível Markup Language (XAML) para fornecer um modelo declarativo para a programação de aplicações, que iremos rever nas próximas secções.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Criar um projeto WPF no Visual Studio
> * Adicione conjuntos e pacotes NuGet ao seu projeto
> * Crie uI da sua aplicação com XAML
> * Use o Tradutor para obter línguas, traduzir texto e detetar a língua de origem
> * Utilize a API de Verificação ortográfica Bing para validar a sua entrada e melhorar a precisão da tradução
> * Execute a sua aplicação WPF

### <a name="cognitive-services-used-in-this-tutorial"></a>Serviços Cognitivos utilizados neste tutorial

Esta lista inclui os Serviços Cognitivos utilizados neste tutorial. Siga o link para navegar na referência API para cada recurso.

| Serviço | Funcionalidade | Descrição |
|---------|---------|-------------|
| Tradutor | [Obter Línguas](./reference/v3-0-languages.md) | Recupere uma lista completa de idiomas suportados para tradução de texto. |
| Tradutor | [Traduzir](./reference/v3-0-translate.md) | Traduza o texto em mais de 70 línguas. |
| Tradutor | [Detetar](./reference/v3-0-detect.md) | Detetar a linguagem do texto de entrada. Inclui pontuação de confiança para deteção. |
| Verificação Ortográfica do Bing | [Verificação ortográfica](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Erros ortográficos corretos para melhorar a precisão da tradução. |

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuarmos, vai precisar do seguinte:

* Uma assinatura dos Serviços Cognitivos Azure. [Obtenha uma chave de Serviços Cognitivos.](../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource)
* Uma máquina Windows
* [Visual Studio 2019](https://www.visualstudio.com/downloads/) - Comunidade ou Empresa

> [!NOTE]
> Recomendamos a criação da subscrição na região oeste dos EUA para este tutorial. Caso contrário, terá de alterar pontos finais e regiões no código à medida que trabalha neste exercício.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Criar uma aplicação WPF no Visual Studio

A primeira coisa a fazer é criar o nosso projeto no Estúdio Visual.

1. Abra o Visual Studio. **Selecione Criar um novo projeto.**
1. Na **Criação de um novo projeto,** localizar e selecionar **a Aplicação WPF (.NET Framework)**. Pode selecionar C# do **Idioma** para reduzir as opções.
1. Selecione **Next** e, em seguida, nomeie o seu projeto `MSTranslatorDemo` .
1. Desa esta versão-quadro para **.NET Framework 4.7.2** ou posterior, e selecione **Criar**.
   ![Insira o nome e a versão quadro no Visual Studio](media/name-wpf-project-visual-studio.png)

O seu projeto foi criado. Vai notar que há dois separadores abertos: `MainWindow.xaml` e `MainWindow.xaml.cs` . Ao longo deste tutorial, vamos adicionar código a estes dois ficheiros. Vamos modificar `MainWindow.xaml` para a interface de utilizador da aplicação. Vamos modificar `MainWindow.xaml.cs` as nossas chamadas para Tradutor e Bing Spell Check.
   ![Reveja o seu ambiente](media/blank-wpf-project.png)

Na próxima secção, vamos adicionar conjuntos e um pacote NuGet ao nosso projeto para funcionalidades adicionais, como a análise do JSON.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Adicione referências e pacotes NuGet ao seu projeto

O nosso projeto requer um punhado de conjuntos de quadros .NET e NewtonSoft.Js, que vamos instalar usando o gestor de pacotes NuGet.

### <a name="add-net-framework-assemblies"></a>Adicionar conjuntos de quadros .NET

Vamos adicionar conjuntos ao nosso projeto para serializar e deserizar objetos, e gerir pedidos e respostas HTTP.

1. Localize o seu projeto no Explorador de Soluções do Estúdio Visual. Clique com o botão direito no seu projeto e, em seguida, **selecione Add > Reference**, que abre o Reference **Manager**.
1. O separador **Assemblies** lista todas as assembleias-quadro .NET que estão disponíveis para referência. Utilize a barra de pesquisa no canto superior direito para procurar referências.
   ![Adicionar referências de montagem](media/add-assemblies-2019.png)
1. Selecione as seguintes referências para o seu projeto:
   * [System.Runtime.Serialization](/dotnet/api/system.runtime.serialization)
   * [System.Web](/dotnet/api/system.web)
   * System.Web.Extensions
   * [Sistema.Windows](/dotnet/api/system.windows)
1. Depois de adicionar estas referências ao seu projeto, pode clicar **em OK** para fechar **o Reference Manager**.

> [!NOTE]
> Se quiser saber mais sobre referências de montagem, consulte [Como: Adicionar ou remover referências utilizando o Gestor de Referência](/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager).

### <a name="install-newtonsoftjson"></a>Instale NewtonSoft.Jsem

A nossa aplicação utilizará NewtonSoft.Jspara deserizar objetos JSON. Siga estas instruções para instalar a embalagem.

1. Localize o seu projeto no Explorador de Soluções do Visual Studio e clique com o botão direito no seu projeto. **Selecione Gerir pacotes nuget**.
1. Localize e selecione o separador **Procurar.**
1. Introduza [NewtonSoft.Jsna](https://www.nuget.org/packages/Newtonsoft.Json/) barra de pesquisa.

    ![Localizar e instalar NewtonSoft.Js](media/nuget-package-manager.png)

1. Selecione o pacote e clique **em Instalar**.
1. Quando a instalação estiver concluída, feche a lingueta.

## <a name="create-a-wpf-form-using-xaml"></a>Criar um formulário WPF usando XAML

Para utilizar a sua aplicação, vai precisar de uma interface de utilizador. Utilizando o XAML, criaremos um formulário que permite aos utilizadores selecionarem idiomas de entrada e tradução, introduzir texto para traduzir e exibir a saída de tradução.

Vamos ver o que estamos a construir.

![Interface de utilizador WPF XAML](media/translator-text-csharp-xaml.png)

A interface do utilizador inclui estes componentes:

| Nome | Tipo | Descrição |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | Apresenta uma lista dos idiomas suportados pelo Microsoft Tradutor para tradução de texto. O utilizador seleciona o idioma de origem que está a traduzir. |
| `ToLanguageComboBox` | ComboBox | Apresenta a mesma lista de idiomas que `FromComboBox` , mas é usado para selecionar o idioma a que o utilizador está a traduzir. |
| `TextToTranslate` | TextBox | Permite que o utilizador introduza texto a traduzir. |
| `TranslateButton` | Botão | Utilize este botão para traduzir texto. |
| `TranslatedTextLabel` | Etiqueta | Apresenta a tradução. |
| `DetectedLanguageLabel` | Etiqueta | Apresenta a linguagem detetada do texto a traduzir `TextToTranslate` (). |

> [!NOTE]
> Estamos a criar este formulário usando o código fonte XAML, no entanto, pode criar o formulário com o editor no Visual Studio.

Vamos adicionar o código ao nosso projeto.

1. No Estúdio Visual, selecione o separador para `MainWindow.xaml` .
1. Copie este código para o seu projeto e, em seguida, selecione **'Ficheiro > Save MainWindow.xaml'** para guardar as alterações.
   ```xaml
   <Window x:Class="MSTranslatorDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorDemo"
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
Deverá agora ver uma pré-visualização da interface de utilizador da aplicação no Visual Studio. Deve parecer-se com a imagem acima.

É isso, a tua forma está pronta. Agora vamos escrever um código para usar a tradução de texto e a verificação ortográfica de Bing.

> [!NOTE]
> Sinta-se livre para ajustar este formulário ou criar o seu próprio.

## <a name="create-your-app"></a>Crie a sua app

`MainWindow.xaml.cs` contém o código que controla a nossa aplicação. Nas próximas secções, vamos adicionar código para preencher os nossos menus suspensos, e chamar um punhado de API exposto pelo Tradutor e Bing Spell Check.

* Quando o programa começa e `MainWindow` é instantâneo, o `Languages` método do Tradutor é chamado para recuperar e povoar as nossas quedas de seleção linguística. Isto acontece uma vez no início de cada sessão.
* Quando o botão **'Traduzir'** é clicado, a seleção e o texto do utilizador são recuperados, a verificação ortográfica é realizada na entrada e a tradução e o idioma detetado são apresentados para o utilizador.
  * O `Translate` método do Tradutor é chamado a traduzir texto de `TextToTranslate` . Esta chamada também inclui os `to` `from` e os idiomas selecionados usando os menus suspensos.
  * O `Detect` método do Tradutor é chamado para determinar a linguagem de texto de `TextToTranslate` .
  * Bing Spell Check é utilizado para validar `TextToTranslate` e ajustar erros ortográficos.

Todo o nosso projeto está encapsulado na `MainWindow : Window` aula. Vamos começar por adicionar código para definir a sua chave de subscrição, declarar pontos finais para Tradutor e Bing Spell Check e inicializar a aplicação.

1. No Estúdio Visual, selecione o separador para `MainWindow.xaml.cs` .
1. Substitua as declarações pré-povoadas `using` pelo seguinte.  
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
1. Localize a `MainWindow : Window` classe e substitua-a por este código:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator and Bing Spell Check
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
1. Adicione a sua chave de subscrição de Serviços Cognitivos e guarde.

Neste bloco de códigos, declaramos duas variáveis de membro que contêm informações sobre línguas disponíveis para tradução:

| Variável | Tipo | Descrição |
|----------|------|-------------|
|`languageCodes` | Matriz de cordas |Coloca em cache os códigos de idioma. O serviço Translator utiliza códigos curtos, como `en` para inglês, para identificar idiomas. |
|`languageCodesAndTitles` | Dicionário ordenado | Mapeia os nomes "amigáveis" na interface do utilizador de volta para os códigos curtos utilizados na API. São mantidos ordenados por ordem alfabética, sem ter em conta as maiúsculas/minúsculas. |

Então, dentro do `MainWindow` construtor, adicionámos o tratamento de erros `HandleExceptions` com. Este tratamento de erros garante que um alerta é fornecido se uma exceção não for tratada. Em seguida, é executado um cheque para confirmar que a chave de subscrição fornecida tem 32 caracteres de comprimento. Um erro é lançado se a tecla for inferior/superior a 32 caracteres.

Se houver teclas que têm pelo menos o comprimento certo, a chamada faz com que `InitializeComponent()` a interface do utilizador seja rolando, localizando, carregando e instantaneamente a descrição do XAML da janela principal da aplicação.

Por último, adicionámos código para chamar métodos para recuperar idiomas para tradução e para preencher os menus suspensos para a interface de utilizador da nossa aplicação. Não se preocupe, chegaremos ao código por trás destas chamadas em breve.

## <a name="get-supported-languages"></a>Obter idiomas suportados

O Tradutor suporta atualmente mais de 70 línguas. Uma vez que o novo suporte linguístico será adicionado ao longo do tempo, recomendamos chamar o recurso idiomas exposto pelo Tradutor em vez de codificar a lista de idiomas na sua app.

Nesta secção, vamos criar um `GET` pedido ao recurso Idiomas, especificando que queremos uma lista de idiomas disponíveis para tradução.

> [!NOTE]
> O recurso Languages permite filtrar o suporte linguístico com os seguintes parâmetros de consulta: transliteração, dicionário e tradução. Para mais informações, consulte [a referência API.](./reference/v3-0-languages.md)

Antes de irmos mais longe, vamos dar uma olhada na saída da amostra para uma chamada para o recurso Languages:

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

A partir desta saída, podemos extrair o código linguístico e o `name` de uma língua específica. A nossa aplicação utiliza NewtonSoft.Jspara deserizar o objeto JSON [`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) ( ).

Retomando o local onde paramos na última secção, vamos adicionar um método para obter idiomas suportados na nossa app.

1. No Visual Studio, abra a conta para `MainWindow.xaml.cs` .
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

O `GetLanguagesForTranslate()` método cria um pedido HTTP GET e utiliza o parâmetro de cadeia de consulta é usado para limitar `scope=translation` o âmbito do pedido a línguas apoiadas para tradução. O cabeçalho `Accept-Language` com o valor `en` é adicionado para que os idiomas suportados sejam devolvidos em inglês.

A resposta json é analisada e convertida num dicionário. Em seguida, os códigos linguísticos são adicionados à `languageCodes` variável membro. Os pares de chave/valor que contêm os códigos de idioma e os nomes amigáveis de idiomas são colocados num ciclo e adicionados à variável de membro `languageCodesAndTitles`. Os menus suspensos no formulário exibem os nomes amigáveis, mas os códigos são necessários para solicitar a tradução.

## <a name="populate-language-drop-down-menus"></a>Menus de drop-down de linguagem povoativa

A interface do utilizador é definida usando XAML, pelo que não precisa de fazer muito para a configurar para além da chamada `InitializeComponent()` . A única coisa que precisa de fazer é adicionar os nomes linguísticos amigáveis aos menus **De traduzir** e **traduzir para** os menus suspensos. O `PopulateLanguageMenus()` método adiciona os nomes.

1. No Visual Studio, abra a conta para `MainWindow.xaml.cs` .
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

Este método itera sobre o `languageCodesAndTitles` dicionário e adiciona cada chave a ambos os menus. Depois de os menus serem preenchidos, o padrão de e para os idiomas é definido para **Detetar** e **Inglês,** respectivamente.

> [!TIP]
> Sem uma seleção predefinida para os menus, o utilizador pode clicar em **Traduzir**, sem primeiro escolher um idioma "para" ou "de". As predefinições eliminam a necessidade de lidar com este problema.

Agora que `MainWindow` foi inicializado e a interface de utilizador criada, este código não será executado até que o botão **'Traduzir'**  seja clicado.

## <a name="detect-language-of-source-text"></a>Linguagem de deteção do texto-fonte

Agora vamos criar um método para detetar a linguagem do texto de origem (texto introduzido na nossa área de texto) usando o Tradutor. O valor devolvido por este pedido será usado no nosso pedido de tradução mais tarde.

1. No Visual Studio, abra a conta para `MainWindow.xaml.cs` .
2. Adicione este código ao seu projeto abaixo do `PopulateLanguageMenus()` método:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator
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

Este método cria um pedido HTTP `POST` ao recurso Detect. É preciso um único `text` argumento, que é transmitido como o corpo do pedido. Mais tarde, quando criarmos o nosso pedido de tradução, o texto introduzido na nossa UI será passado a este método de deteção de idiomas.

Além disso, este método avalia a pontuação de confiança da resposta. Se a pontuação for maior do que `0.5` , então o idioma detetado é apresentado na nossa interface de utilizador.

## <a name="spell-check-the-source-text"></a>Feitiço verifique o texto de origem

Agora vamos criar um método para soletrar o nosso texto de origem usando a API de Verificação ortográfica de Bing. A verificação ortográfica garante que vamos recuperar traduções precisas do Tradutor. Quaisquer correções ao texto de origem são transmitidas no nosso pedido de tradução quando o botão **'Traduzir'** é clicado.

1. No Visual Studio, abra a conta para `MainWindow.xaml.cs` .
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

## <a name="translate-text-on-click"></a>Traduzir texto no clique

A última coisa que precisamos de fazer é criar um método que seja invocado quando o botão **Traduzir** na nossa interface de utilizador é clicado.

1. No Visual Studio, abra a conta para `MainWindow.xaml.cs` .
1. Adicione este código ao seu projeto abaixo do `CorrectSpelling()` método e guarde:  
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
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
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

O primeiro passo é obter as línguas "de" e "para" e o texto que o utilizador inseriu no nosso formulário. Se a língua de origem estiver definida para **Detetar,** `DetectLanguage()` é chamada para determinar a linguagem do texto de origem. O texto pode estar numa linguagem que o Tradutor não apoia. Nesse caso, apresente uma mensagem para informar o utilizador e devolva sem traduzir o texto.

Se o idioma de origem é inglês (seja especificado ou detetado), verifique a ortografia de texto com `CorrectSpelling()` e aplique as correções precisas. O texto corrigido é adicionado de volta à área de texto para que o utilizador veja que foi feita uma correção.

O código para traduzir texto deve parecer familiar: construir o URI, criar um pedido, enviá-lo e analisar a resposta. A matriz JSON pode conter mais do que um objeto para tradução, no entanto, a nossa aplicação apenas requer um.

Após um pedido bem sucedido, `TranslatedTextLabel.Content` é substituído pelo , que atualiza a interface do utilizador para exibir o texto `translation` traduzido.

## <a name="run-your-wpf-app"></a>Execute a sua aplicação WPF

É isso, tens uma aplicação de tradução em funcionamento construída com recurso ao WPF. Para executar a sua aplicação, clique no botão **Iniciar** no Estúdio Visual.

## <a name="source-code"></a>Código de origem

O código-fonte para este projeto está disponível no GitHub.

* [Explore o código fonte](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência do Tradutor da Microsoft](./reference/v3-0-reference.md)
