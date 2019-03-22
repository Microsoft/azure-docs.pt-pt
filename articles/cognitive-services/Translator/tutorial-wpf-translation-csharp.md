---
title: 'Tutorial: Criar uma aplicação de tradução com o WPF, C# -API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma aplicação do Windows Presentation Foundation (WPF) que usa APIs de serviços cognitivos para tradução de texto, deteção de idioma e ortográfica com uma chave de subscrição individual. Neste exercício mostram-lhe como utilizar funcionalidades da API de texto do Translator e a API de verificação ortográfica do Bing.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: erhopf
ms.openlocfilehash: f7f8e86f17b0fdb715afc96dba80db0746440cef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078130"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Tutorial: Criar uma aplicação de tradução com WPF

Neste tutorial, irá criar um [Windows Presentation Foundation (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2017) aplicação que utiliza os serviços cognitivos do Azure para tradução de texto, deteção de idioma e a verificação de ortografia com uma chave de subscrição individual. Especificamente, a aplicação irá chamar as APIs de texto do tradutor e [verificação ortográfica do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/).

O que é o WPF? É uma estrutura de interface do Usuário que cria aplicações de cliente de desktop. A plataforma de desenvolvimento do WPF suporta um vasto leque de funcionalidades de desenvolvimento de aplicações, incluindo um modelo de aplicação, recursos, controles, gráficos, layout, ligação de dados, documentos e segurança. É um subconjunto do .NET Framework, para que se anteriormente tiver criado aplicações com o .NET Framework usando o ASP.NET ou Windows Forms, a experiência de programação deve estar familiarizada. WPF utiliza a aplicação Extensible Markup Language (XAML) para fornecer um modelo declarativo para a aplicação de programação, que vamos rever nas próximas seções.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um projeto do WPF no Visual Studio
> * Adicionar assemblies e pacotes NuGet ao seu projeto
> * Criar a interface do Usuário da sua aplicação com XAML
> * Utilize a API de texto do Translator para obter idiomas, traduzir texto e detetar o idioma de origem
> * Utilizar a API de verificação de ortografia do Bing para validar a sua entrada e melhorar a precisão de tradução
> * Executar a sua aplicação WPF

### <a name="cognitive-services-used-in-this-tutorial"></a>Serviços cognitivos utilizados neste tutorial

Esta lista inclui os serviços cognitivos utilizado neste tutorial. Siga a ligação para procurar a referência da API para cada funcionalidade.

| Serviço | Funcionalidade | Descrição |
|---------|---------|-------------|
| Texto do Tradutor | [Obter idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Obter uma lista completa dos idiomas suportados para a tradução de texto. |
| Texto do Tradutor | [Traduzir](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Traduza texto em mais de 60 idiomas. |
| Texto do Tradutor | [Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Detecte o idioma do texto de entrada. Inclui a pontuação de confiança para a deteção. |
| Verificação Ortográfica do Bing | [Verificação ortográfica](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Corrigir os erros de ortografia para melhorar a precisão de tradução. |

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, terá o seguinte:

* Uma subscrição de serviços cognitivos do Azure. [Obter uma chave de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#multi-service-subscription).
* Uma máquina Windows
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) -Comunidade ou Enterprise

> [!NOTE]
> Recomendamos que crie a subscrição na região E.U.A. oeste para este tutorial. Caso contrário, terá de alterar os pontos de extremidade e regiões no código à medida que percorre este exercício.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Criar um aplicativo do WPF no Visual Studio

A primeira coisa que precisamos fazer é configurar nosso projeto no Visual Studio.

1. Abra o Visual Studio. Em seguida, selecione **ficheiro > novo > projeto**.
2. No painel esquerdo, localize e selecione **Visual C#** . Em seguida, selecione **WPF App (.NET Framework)** no painel central.
   ![Criar um aplicativo do WPF no Visual Studio](media/create-wpf-project-visual-studio.png)
3. Nomeie o projeto, defina a versão do framework para o **.NET Framework 4.5.2 ou posterior**, em seguida, clique em **OK**.
4. O projeto foi criado. Observará que há duas guias abertas: `MainWindow.xaml` e `MainWindow.xaml.cs`. Neste tutorial, estamos a adicionar código para esses dois arquivos. O primeiro para a interface do usuário do aplicativo; o segundo para nosso chamadas para o texto do tradutor e verificação de ortografia do Bing.
   ![Reveja o seu ambiente](media/blank-wpf-project.png)

Na próxima seção, vamos adicionar assemblies e um pacote NuGet ao nosso projeto para funcionalidades adicionais, como análise de JSON.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Adicionar referências e os pacotes NuGet ao seu projeto

Nosso projeto requer um punhado de assemblies do .NET Framework e Newtonsoft, a qual vamos instalar com o NuGet package manager.

### <a name="add-net-framework-assemblies"></a>Adicionar assemblies do .NET Framework

Vamos adicionar assemblies ao nosso projeto para serializar e desserializar objetos e para gerir pedidos HTTP e respostas.

1. Localize o seu projeto no Explorador de soluções do Visual Studio (painel direito). Clique com o botão direito do rato no seu projeto, em seguida, selecione **adicionar > referência...** , que abre **Gestor de referências**.
   ![Adicionar referências ao assembly](media/add-assemblies-sample.png)
2. O separador de assemblies apresenta uma lista de todos os assemblies do .NET Framework que estão disponíveis para referência. Utilize a barra de pesquisa no canto superior direito do ecrã para pesquisar essas referências e adicioná-los ao seu projeto:
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization?view=netframework-4.7.2)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web?view=netframework-4.7.2)
   * [System.Web.Extensions](https://docs.microsoft.com/dotnet/api/system.web?view=netframework-4.7.2)
3. Depois de adicionar estas referências ao seu projeto, pode clicar em **OK** para fechar **Gestor de referências**.

> [!NOTE]
> Se gostaria de saber mais sobre as referências de assembly, consulte o artigo [como: Adicionar ou remover a referência usando o Gerenciador de referência](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2017).

### <a name="install-newtonsoftjson"></a>Install NewtonSoft.Json

A nossa aplicação irá utilizar Newtonsoft para anular a serialização de objetos JSON. Siga estas instruções para instalar o pacote.

1. Localize o seu projeto no Explorador de soluções do Visual Studio e clique com o botão direito do rato no seu projeto. Selecione **gerir pacotes NuGet...** .
2. Localize e selecione o **procurar** separador.
3. Tipo [Newtonsoft](https://www.nuget.org/packages/Newtonsoft.Json/) na barra de pesquisa.
   ![Localizar e instalar Newtonsoft](media/add-nuget-packages.png)
4. Selecione o pacote e clique em **instalar**.
5. Quando a instalação estiver concluída, feche o separador.

## <a name="create-a-wpf-form-using-xaml"></a>Criar um formulário no WPF usando XAML

Para utilizar a sua aplicação, vai precisar de uma interface do usuário. Usando o XAML, vamos criar um formulário que permite aos utilizadores selecionar os idiomas de entrada e de tradução, introduza o texto a traduzir, e apresenta o resultado de tradução.

Vamos dar uma olhada no que estamos criando.

![Interface do usuário WPF XAML](media/translator-text-csharp-xaml.png)

O interfaceador de utilizador inclui estes componentes:

| Name | Tipo | Descrição |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | Apresenta uma lista dos idiomas suportados pelo Microsoft Translator para tradução de texto. O utilizador seleciona o idioma de origem que está a traduzir. |
| `ToLanguageComboBox` | ComboBox | Apresenta a mesma lista de idiomas como `FromComboBox`, mas é utilizado para selecionar o idioma é a tradução para o utilizador. |
| `TextToTranslate` | TextBox | Permite ao utilizador introduzir texto para ser convertido. |
| `TranslateButton` | Botão | Utilize este botão para traduzir texto. |
| `TranslatedTextLabel` | Label | Apresenta a tradução. |
| `DetectedLanguageLabel` | Label | Apresenta o idioma detetado do texto ser traduzida (`TextToTranslate`). |

> [!NOTE]
> Estamos a criar este formulário com o código-fonte XAML, no entanto, é possível criar o formulário com o editor no Visual Studio.

Vamos adicionar o código ao nosso projeto.

1. No Visual Studio, selecione o separador de `MainWindow.xaml`.
2. Copie esse código no seu projeto e guarde.
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
3. Agora, deverá ver uma pré-visualização da interface do usuário do aplicativo no Visual Studio. Deve ser semelhante à imagem acima.

É isso, seu formulário está pronto. Agora vamos escrever um código para utilizar a tradução de texto e a verificação ortográfica do Bing.

> [!NOTE]
> Pode ajustar este formulário ou criar os seus próprios.

## <a name="create-your-app"></a>Criar a sua aplicação

`MainWindow.xaml.cs` contém o código que controla a nossa aplicação. As secções seguintes, vamos adicionar código para preencher nossa menus de lista pendente e, para chamar um punhado de API exposta pelo texto de tradutor e verificação de ortografia do Bing.

* Quando o programa é iniciado e `MainWindow` é instanciado, o `Languages` método da API de texto do Translator denomina-se para obter e preencher nossa linguagem seleção mais listas pendentes. Isso ocorre uma vez no início de cada sessão.
* Quando o **Translate** botão é clicado, seleção de idioma e o texto do utilizador são recuperados, verificação de ortografia é executada na entrada e a tradução e o idioma detetado são apresentados para o utilizador.
  * O `Translate` é chamado de método da API de texto do Translator para traduzir texto de `TextToTranslate`. Essa chamada também inclui a `to` e `from` idiomas selecionados utilizando os menus de lista pendente.
  * O `Detect` método da API de texto do tradutor é chamado para determinar o idioma de texto de `TextToTranslate`.
  * Verificação ortográfica do Bing é usado para validar `TextToTranslate` e ajustar os erros de ortografia.

Todo o nosso projeto do é encapsulado no `MainWindow : Window` classe. Vamos começar por adicionar código para definir a chave de subscrição, declarar os pontos finais para o texto do tradutor e verificação de ortografia do Bing e inicializar a aplicação.

1. No Visual Studio, selecione o separador de `MainWindow.xaml.cs`.
2. Substitua o pré-preenchidos `using` instruções com o seguinte.  
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
3. Localize o `MainWindow : Window` de classe e substitua-o com este código:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api- version=3.0";
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
           System.Windows.app.Current.Shutdown();
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
               System.Windows.app.Current.Shutdown();
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
   1. Adicione a chave de subscrição de serviços cognitivos e guarde.

Este bloco de código, vamos declarar duas variáveis de membro que contêm informações sobre idiomas de disponíveis para a tradução:

| Variável | Type | Descrição |
|----------|------|-------------|
|`languageCodes` | matriz de cadeias de caracteres |C aches os códigos de idioma. O serviço Translator utiliza códigos curtos, como `en` para inglês, para identificar idiomas. |
|`languageCodesAndTitles` | Dicionário classificado | Mapeia os nomes "amigáveis" na interface do utilizador de volta para os códigos curtos utilizados na API. São mantidos ordenados por ordem alfabética, sem ter em conta as maiúsculas/minúsculas. |

Em seguida, dentro da `MainWindow` construtor, adicionamos o tratamento de erros com `HandleExceptions`. Isto garante que um alerta é fornecido se uma exceção não tratada. Em seguida, é executada uma verificação para confirmar a chave de subscrição fornecida é de 32 carateres de comprimento. É gerado um erro se a chave for menor que / superior a 32 carateres.

Se existirem chaves que tenham, pelo menos o comprimento certo, o `InitializeComponent()` chamada obtém a interface do usuário sem interrupção por localização, para carregar e instanciar a descrição de XAML da janela do aplicativo principal.

Por fim, adicionámos código para chamar métodos para obter os idiomas para a tradução e para preencher os menus de lista pendente de interface do usuário da nossa aplicação. Não se preocupe, voltaremos a code-behind essas chamadas em breve.

## <a name="get-supported-languages"></a>Obter idiomas suportados

Atualmente, a API de texto do Translator suporta mais de 60 idiomas. Uma vez que o suporte a novos idiomas será adicionado ao longo do tempo, recomendamos que ligue o recurso de idiomas exposto pelo texto do tradutor, em vez de codificar a lista de idiomas na sua aplicação.

Nesta secção, vamos criar um `GET` pedido para o recurso de idiomas, especificando o que queremos uma lista de idiomas disponíveis para a tradução.

> [!NOTE]
> O recurso de idiomas permite-lhe filtrar o suporte de idiomas com os seguintes parâmetros de consulta: Transliteração, dicionário e a tradução. Para obter mais informações, consulte [referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Antes de continuarmos, vamos dar uma olhada numa saída de exemplo para uma chamada para o recurso de idiomas:

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

A partir desta saída, pode extrair o código de idioma e o `name` de um idioma específico. A nossa aplicação utiliza Newtonsoft para anular a serialização do objeto JSON ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Escolha de onde paramos na última secção, vamos adicionar um método para obter os idiomas com suporte à nossa aplicação.

1. No Visual Studio, abra o separador `MainWindow.xaml.cs`.
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

O `GetLanguagesForTranslate()` método cria um pedido HTTP GET e utiliza o `scope=translation` parâmetro de cadeia de caracteres de consulta é utilizado para limitar o âmbito do pedido para idiomas suportados para a tradução. O cabeçalho `Accept-Language` com o valor `en` é adicionado para que os idiomas suportados sejam devolvidos em inglês.

A resposta JSON é analisada e convertida num dicionário. Em seguida, os códigos de idioma são adicionados para o `languageCodes` variável de membro. Os pares de chave/valor que contêm os códigos de idioma e os nomes amigáveis de idiomas são colocados num ciclo e adicionados à variável de membro `languageCodesAndTitles`. Os menus de lista pendente na forma apresentam os nomes amigáveis, mas os códigos são necessários para a tradução do pedido.

## <a name="populate-language-drop-down-menus"></a>Popular menus de lista pendente de idiomas

A interface do usuário é definida usando o XAML, então não precisará fazer muita coisa para configurá-lo além da chamada `InitializeComponent()`. A única coisa que precisa fazer é adicionar os nomes amigáveis de idiomas para o **traduzir partir** e **traduzir para** menus de lista pendente, isso é feito com o `PopulateLanguageMenus()` método.

1. No Visual Studio, abra o separador `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto abaixo o `GetLanguagesForTranslate()` método:
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

Este método itera o `languageCodesAndTitles` dicionário e adiciona cada chave para ambos os menus. Depois dos menus são preenchidos, o padrão de e para os idiomas são definidas como **Detect** e **inglês** , respetivamente.

> [!TIP]
> Sem uma seleção predefinida para os menus, o utilizador pode clicar em **Traduzir**, sem primeiro escolher um idioma "para" ou "de". As predefinições eliminam a necessidade de lidar com este problema.

Agora que `MainWindow` foi inicializado e a interface do usuário criada, este código não será executado até que o **Translate** botão é clicado.

## <a name="detect-language-of-source-text"></a>Detetar o idioma de texto de origem

Agora, vamos criar o método para detetar o idioma do texto de origem (texto inserido na nossa área de texto) com a API de texto do Translator. O valor devolvido por este pedido será utilizado no pedido tradução mais tarde.

1. No Visual Studio, abra o separador `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto abaixo o `PopulateLanguageMenus()` método:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "app/json; charset=utf-8";
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

Este método cria um HTTP `POST` pedido para o recurso de deteção. Demora um único argumento `text`, que é transmitido como o corpo do pedido. Mais tarde, iremos quando criamos o nosso pedido de tradução, o texto introduzido na nossa interface do Usuário serão passados para esse método de deteção de idioma.

Além disso, esse método avalia a pontuação de confiança da resposta. Se a pontuação for superior a `0.5`, o idioma detetado é apresentado na nossa interface de usuário.

## <a name="spell-check-the-source-text"></a>O texto de origem de verificação ortográfica

Agora, vamos criar um método para descrever verifique nosso texto de origem com a API de verificação de ortografia do Bing. Isto garante que vamos entrar em contacto traduções precisas da API de texto do Translator. As correções necessárias para o texto de origem são transferidas em nossa conversão do pedido quando o **Translate** botão é clicado.

1. No Visual Studio, abra o separador `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto abaixo o `DetectLanguage()` método:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "app/x-www-form-urlencoded"; // doesn't work without this

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

## <a name="translate-text-on-click"></a>Traduzir texto no, clique em

A última coisa que precisamos fazer é criar um método que é invocado quando o **Translate** clicar no botão na nossa interface de usuário.

1. No Visual Studio, abra o separador `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto abaixo o `CorrectSpelling()` método e guarde:  
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

A primeira etapa é obter o "de" e "to" idiomas e o texto que o usuário inserido no nosso formulário. Se o idioma de origem está definido como **Detect**, `DetectLanguage()` é chamado para determinar o idioma do texto de origem. O texto poderá estar num idioma que não suporta a API do Translator. Nesse caso, exiba uma mensagem a informar o usuário e retornar sem tradução do texto.

Se o idioma de origem é inglês (seja especificado ou detetado), verifique a ortografia de texto com `CorrectSpelling()` e aplique as correções precisas. O texto corrigido é adicionado novamente para a área de texto para que o utilizador verá que uma correção foi efetuada.

O código para traduzir texto deve parecer familiar: Compile o URI, criar um pedido, enviá-lo e analisar a resposta. A matriz JSON pode conter mais de um objeto para a tradução, no entanto, a nossa aplicação exige apenas um.

Depois de um pedido com êxito, `TranslatedTextLabel.Content` é substituído pelo `translation`, que atualiza a interface do usuário para exibir o texto traduzido.

## <a name="run-your-wpf-app"></a>Executar a sua aplicação WPF

É isso, tem um aplicativo de tradução de trabalho criado com o WPF. Para executar a aplicação, clique nas **iniciar** botão no Visual Studio.

## <a name="source-code"></a>Código de origem

Código-fonte para este projeto está disponível no GitHub.

* [Explore o código-fonte](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API de Texto do Microsoft Translator](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
