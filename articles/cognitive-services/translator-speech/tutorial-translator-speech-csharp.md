---
title: 'Tutorial: API de voz do tradutorC#'
titleSuffix: Azure Cognitive Services
description: Utilize a API de Voz do Microsoft Translator para traduzir textos em tempo real.
services: cognitive-services
author: v-jerkin
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a3ed13cfe764c4f94dfa50fd096cfc7a8ac7656d
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673756"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Tutorial: Aplicativo de voz de tradutor noC#

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Este tutorial é uma apresentação de uma ferramenta de tradução de voz interativa que utiliza a API de Voz do Microsoft Translator, que faz parte dos Serviços Cognitivos do Azure. Vai aprender a:

> [!div class="checklist"]
> * Pedir uma lista dos idiomas que o serviço suporta
> * Capturar áudio e transmiti-lo para o serviço
> * Receber e apresentar traduções de voz em texto
> * Opcionalmente, reproduzir uma versão falada (conversão de texto em voz) da tradução

Está [disponível no GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator) um ficheiro de solução do Visual Studio para esta aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, precisa de qualquer edição do Visual Studio 2017, incluindo a edição Visual Studio Community.

A solução do Visual Studio também cria um instalador para a aplicação. Precisa do [WiX Toolset](http://wixtoolset.org/) e da [Extensão WiX Toolset Visual Studio](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) para suportar esta funcionalidade.

Também precisa de uma chave de subscrição para o serviço Tradução de Voz, que pode obter no dashboard do Microsoft Azure. Está disponível um escalão de preço gratuito que lhe permite traduzir até dez horas de voz por mês sem custos. Este escalão é suficiente para o tutorial.

Também é necessária a [biblioteca JSON.Net](https://www.newtonsoft.com/json) de terceiros (da Newtonsoft). Esta assemblagem é instalada automaticamente pelo NuGet se ambas as caixas de verificação de Restauro de Pacote estiverem selecionadas nas opções do Visual Studio.

## <a name="trying-the-translation-app"></a>Experimentar a aplicação de tradução

Depois de abrir a solução Tradutor de Voz (`SpeechTranslator.sln`) no Visual Studio, prima F5 para compilar e iniciar a aplicação.  É apresentada a janela principal do programa.

![[Janela principal da Tradução de Voz]](media/speech-translator-main-window.png)

Na primeira execução, escolha **Account Settings** (Definições da Conta), no menu **Settings** (Definições), para abrir a janela mostrada aqui.

![[Janela principal da Tradução de Voz]](media/speech-translator-settings-window.png)

Cole a sua chave de subscrição da Tradução de Voz nesta janela e clique em **Save** (Guardar). A chave é guardada entre as execuções.

Novamente na janela principal, escolha os dispositivos de entrada e saída de áudio que pretende utilizar e os idiomas De e Para. Se quiser ouvir o áudio da tradução, confirme que a opção **TTS** (conversão de texto em voz) está assinalada. Se quiser ver traduções parciais especulativas enquanto fala, ative a opção **Partial Results** (Resultados Parciais).

Por fim, clique em **Start** (Iniciar) para começar a tradução. Diga qualquer coisa que pretenda traduzir e o texto reconhecido e a tradução são apresentados na janela. Se tiver ativado a opção TTS, também ouve a tradução.

## <a name="obtaining-supported-languages"></a>Obter os idiomas suportados

Nesta fase, o serviço Tradução de Voz suporta mais de cinco dezenas de idiomas para tradução de texto. Relativamente à tradução de voz, é suportado um número mais reduzido de idiomas. Esses idiomas precisam de suporte para transcrição (reconhecimento de voz) e, relativamente à saída de texto em voz, de síntese.

Por outras palavras, na tradução de voz, o idioma de partida tem de ser um idioma que suporte transcrição. O idioma de chegada pode ser um dos idiomas que suportem a tradução de texto, partindo do princípio de que pretende obter um resultado em texto. Se preferir receber uma saída de voz, pode traduzir apenas para um idioma que suporte a conversão de texto em voz.

A Microsoft poderá adicionar suporte para idiomas novos periodicamente. Por esse motivo, não deve codificar o conhecimento dos idiomas suportados na sua aplicação. Em vez disso, a API de Voz do Microsoft Translator proporciona o ponto final Idiomas que lhe permite obter os idiomas suportados no runtime. Pode optar por receber uma ou mais listas de idiomas:

| | |
|-|-|
|`speech`|Os idiomas suportados para a transcrição de voz. Podem ser idiomas de partida para a tradução de voz.|
|`text`|Os idiomas suportados para tradução de texto para texto. Podem ser idiomas de chegada para a tradução de voz quando é utilizada a saída de texto.|
|`tts`|As vozes suportadas para síntese de voz, cada qual associada a um determinado idioma. Podem ser idiomas de chegada para a tradução de voz quando é utilizada a conversão de texto em voz. Um determinado idioma poderá ser suportado por mais de uma voz.|

O ponto final Idiomas não requer uma chave de subscrição e a utilização do mesmo não é contabilizada para a sua quota. O URI do ponto final é `https://dev.microsofttranslator.com/languages` e os resultados do mesmo são devolvidos em formato JSON.

O método `UpdateLanguageSettingsAsync()` em `MainWindow.xaml.cs`, mostrado aqui, chama o ponto final Idiomas para obter a lista de idiomas suportados.

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Este método começa por construir um pedido HTTP para o ponto final Idiomas, a pedir as três listas de idiomas (`text`, `speech` e `tts`).

O ponto final utiliza o cabeçalho `Accept-Languages` do pedido para determinar o idioma no qual os nomes dos idiomas são apresentados. Por exemplo, o idioma que os falantes de inglês conhecem como “alemão” chama-se “deutsch” em alemão e “alemán” em espanhol. A lista de idiomas reflete essas diferenças. É utilizado neste cabeçalho o idioma predefinido do sistema.

Após o pedido ser enviado e a resposta JSON recebida, a resposta é separada em estruturas de dados internas. Essas estruturas são, depois, utilizadas para construir os menus Idioma de Partida e Idioma de Chegada.

Uma vez que as vozes disponíveis dependem do Idioma de Chegada que o utilizador escolhe, ainda não é possível configurar o menu Voz. Em vez disso, as vozes disponíveis para cada idioma são armazenadas, para utilização posterior. Mais tarde, o processador `ToLanguage_SelectionChanged` (no mesmo ficheiro de origem) atualiza o menu Voz ao chamar `UpdateVoiceComboBox()` quando o utilizador escolhe o Idioma de Chegada.

Se o utilizador ainda não tiver executado a aplicação antes, é escolhido um Idioma de Chegada aleatoriamente, só por diversão. (As definições do menu são armazenadas entre sessões.)

## <a name="authenticating-requests"></a>Autenticar pedidos

Para se autenticar no serviço Tradução de Voz da Microsoft, tem de enviar a sua chave de subscrição do Azure no cabeçalho como o valor de `Ocp-Apim-Subscription-Key` no pedido de ligação.

## <a name="translation-overview"></a>Descrição geral da tradução

A API de Voz do Microsoft Translate (ponto final WebSockets `wss://dev.microsofttranslator.com/speech/translate`) aceita a tradução de áudio no formato WAVE monofónico, de 16 kHz e 16 bits assinado. O serviço devolve uma ou mais respostas JSON que contêm o texto reconhecido e o texto traduzido. Se tiver sido pedida conversão de texto em voz, é enviado um ficheiro de áudio.

Para escolher a origem do áudio, o utilizador utiliza o menu Entrada de Microfone/Ficheiro. O áudio pode vir de um dispositivo de áudio (como um microfone) ou de um ficheiro `.WAV`.

Quando o utilizador clica no botão Iniciar, o método `StartListening_Click` é invocado. Por sua vez, este processador de eventos chama `Connect()` para dar início ao processo de envio do áudio para o ponto final da API do serviço. O método `Connect()` realiza as seguintes tarefas:


> [!div class="checklist"]
> * Obter as definições do utilizador a partir da janela principal e validá-las
> * Inicializar os fluxos de entrada e saída de áudio
> * Chamar `ConnectAsync()` para processar o resto do trabalho

`ConnectAsync()`, por seu turno, processa as seguintes tarefas:

> [!div class="checklist"]
> * Autenticar com a Chave de Subscrição do Azure no cabeçalho `Ocp-Apim-Subscription-Key`
> * Criar uma instância `SpeechClient` (encontrada em `SpeechClient.cs`) para comunicar com o serviço
> * Inicializar as instâncias `TextMessageDecoder` e `BinaryMessageDecoder` (veja `SpeechResponseDecoder.cs`) para processar as respostas
> * Enviar o áudio através da instância `SpeechClient` para o serviço Tradução de Voz
> * Receber e processar os resultados da tradução

`SpeechClient` tem menos responsabilidades:

> [!div class="checklist"]
> * Estabelecer uma ligação WebSocket ao serviço Tradução de Voz
> * Enviar os dados de áudio e receber respostas através do socket

## <a name="a-closer-look"></a>Uma visão mais detalhada

Nesta fase, deverá ser claro que partes da aplicação funcionam em conjunto para realizar o pedido de tradução. Vamos ver algum código, com especial atenção às partes relevantes.

Segue-se uma versão parcial de `Connect()` que mostra a configuração dos fluxos de áudio:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };

    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Uma parte substancial de `Connect()` envolve a criação de uma instância `SpeechClientOptions` (veja `SpeechClientOptions.cs`), onde armazenar as opções da tradução. As opções incluem as informações necessárias para ligar ao serviço (como a chave de autenticação e o nome de anfitrião) e as funcionalidades utilizadas na tradução. Os campos aqui mapeiam para os campos do cabeçalho e os parâmetros HTTP que a [API de Voz do Microsoft Translator](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference) expõe.

`Connect()` também cria e inicializa o dispositivo de entrada de áudio (variável `sampleProvider`) que funciona como a origem da voz que vai ser traduzida. Esse dispositivo é um dispositivo de entrada de hardware, como um microfone, ou um ficheiro que contém dados de áudio WAVE.

Segue-se o método `ConnectAsync()` que instancia a classe `speechClient` e prepara funções anónimas para processar o texto e as respostas binárias do serviço.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);

    TextMessageDecoder textDecoder;

    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);

    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected.
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

Após a autenticação, o método cria a instância `SpeechClient`. A classe `SpeechClient` (em `SpeechClient.cs`) invoca os processadores de eventos após a receção dos dados binários e de texto. Se a ligação falhar ou se desligar, são invocados mais cabeçalhos.

Os dados binários são o áudio (saída de conversão de texto em voz) que o serviço envia quando o TTS está ativado. Os dados de texto são uma tradução parcial ou completa do texto falado. Assim, após a instanciação, o método prepara funções para processar essas mensagens - o áudio, ao armazená-lo para ser reproduzido mais tarde, e o texto, ao apresentá-lo na janela.

## <a name="next-steps"></a>Passos Seguintes

Este exemplo de código é uma aplicação com muita funcionalidades que demonstra a utilização da API de Voz do Microsoft Translator. Assim, é necessário compreender um número considerável de coisas. Percorreu as mais importantes. Quanto às restantes, pode ser instrutivo definir alguns pontos de interrupção no Visual Studio e seguir o processo de tradução. Quando compreender a aplicação de exemplo, estará preparado para utilizar o serviço Tradução de Voz nas suas aplicações.

> [!div class="nextstepaction"]
> [Microsoft Translator Speech API reference](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference) (Referência da API de Voz do Microsoft Translator)
