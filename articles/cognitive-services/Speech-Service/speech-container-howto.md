---
title: Instale e execute os recipientes Docker para as APIs do serviço de fala
titleSuffix: Azure Cognitive Services
description: Utilize os recipientes Docker para o serviço de discurso para realizar reconhecimento de voz, transcrição, geração e mais no local.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: no local, Docker, contentor
ms.openlocfilehash: 7bebaf7558de8ec5c1fcca3c9a4526330da1d695
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575793"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Instale e execute os recipientes Docker para as APIs do serviço de fala 

Os contentores permitem-lhe executar algumas das APIs do Serviço de voz no seu próprio ambiente. Os contentores são ótimos para requisitos específicos de governação de dados e segurança. Neste artigo, ficará a saber como transferir, instalar e executar um contentor de Voz.

Os contentores de Voz permitem que os clientes criem uma arquitetura de aplicação de voz, otimizada para capacidades de cloud robustas e localidades no Edge. Existem vários contentores disponíveis, que usam os mesmos [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) que os Serviços de Fala Azure baseados na nuvem.


> [!IMPORTANT]
> Os seguintes recipientes de discurso estão agora geralmente disponíveis:
> * Padrão Discurso-a-Texto
> * Discurso-a-texto personalizado
> * Texto-a-discurso padrão
> * Texto-a-discurso neural
>
> Os seguintes recipientes de fala estão em pré-visualização fechada.
> * Texto-a-discurso personalizado
> * Deteção de linguagem da fala 
>
> Para utilizar os recipientes de fala deve submeter um pedido on-line e aprová-lo. Consulte a **aprovação do Pedido para executar a** secção do recipiente abaixo para obter mais informações.

| Contentor | Funcionalidades | Mais Recente |
|--|--|--|
| Conversão de voz em texto | Analisa o sentimento e transcreve gravações contínuas de voz em tempo real ou de lotes com resultados intermédios.  | 2.9.0 |
| Discurso-a-texto personalizado | Utilizando um modelo personalizado do [portal Discurso Personalizado,](https://speech.microsoft.com/customspeech)transcreve gravações contínuas de discursos em tempo real ou de lotes em texto com resultados intermédios. | 2.9.0 |
| Conversão de texto em voz | Converte o texto para discurso sonoro natural com entrada de texto simples ou linguagem de marcação de síntese de fala (SSML). | 1.11.0 |
| Texto-a-discurso personalizado | Utilizando um modelo personalizado do [portal Voz Personalizada,](https://aka.ms/custom-voice-portal)converte o texto em discurso sonoro natural com entrada de texto simples ou linguagem de marcação de síntese de fala (SSML). | 1.11.0 |
| Deteção de linguagem da fala | Detetar a linguagem falada em ficheiros áudio. | 1.0 |
| Texto-a-discurso neural | Converte o texto em discurso sonoro natural usando a tecnologia de rede neural profunda, permitindo um discurso mais natural sintetizado. | 1.3.0 |

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes da utilização dos recipientes de fala:

| Necessário | Objetivo |
|--|--|
| Motor do Docker | Precisa do Motor Docker instalado num [computador anfitrião.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar recipientes Linux.<br><br> |
| Familiaridade com Docker | Você deve ter uma compreensão básica de conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de `docker` comandos básicos. |
| Recurso de fala | Para utilizar estes recipientes, deve ter:<br><br>Um recurso _de Discurso_ Azure para obter a chave API associada e ponto final URI. Ambos os valores estão disponíveis nas páginas Do **Discurso** e Chaves do portal Azure. Ambos são obrigados a iniciar o contentor.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final, conforme fornecido na página **'Vista Geral',** |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte avançado de extensão do vetor

O **hospedeiro** é o computador que gere o contentor do estivador. O anfitrião *deve suportar* [extensões de vetores avançados](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Pode verificar se há suporte AVX2 em anfitriões Linux com o seguinte comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> O computador anfitrião é *necessário* para suportar o AVX2. O recipiente *não funcionará* corretamente sem suporte AVX2.

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações do contentor

O quadro seguinte descreve a alocação mínima e recomendada de recursos para cada recipiente de discurso.

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Conversão de voz em texto | 2 núcleo, memória de 2 GB | 4 núcleo, memória de 4-GB |
| Discurso-a-texto personalizado | 2 núcleo, memória de 2 GB | 4 núcleo, memória de 4-GB |
| Conversão de texto em voz | 1 núcleo, memória de 2 GB | 2 núcleo, memória de 3-GB |
| Texto-a-discurso personalizado | 1 núcleo, memória de 2 GB | 2 núcleo, memória de 3-GB |
| Deteção de linguagem da fala | 1 núcleo, 1-GB de memória | 1 núcleo, 1-GB de memória |
| Texto-a-discurso neural | 6 núcleo, memória de 12 GB | 8 núcleo, memória de 16-GB |

* Cada núcleo deve ser pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a memória correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do `docker run` comando.

> [!NOTE]
> Os mínimos e recomendados baseiam-se fora dos limites do Docker, *não* dos recursos da máquina hospedeira. Por exemplo, os contentores de memória de um grande modelo de linguagem, por exemplo, são *recomendados* que todo o ficheiro se encaixe na memória, que é um adicional de 4-6 GB. Além disso, a primeira execução de qualquer um dos recipientes pode demorar mais tempo, uma vez que os modelos estão a ser colocados na memória.

## <a name="request-approval-to-the-run-the-container"></a>Solicitar aprovação para a execução do contentor

Preencha e envie o [formulário de pedido](https://aka.ms/csgate) para solicitar o acesso ao contentor. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

As imagens do contentor para a fala estão disponíveis no seguinte registo de contentores.

# <a name="speech-to-text"></a>[Conversão de voz em texto](#tab/stt)

| Contentor | Repositório |
|-----------|------------|
| Conversão de voz em texto | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Discurso-a-texto personalizado](#tab/cstt)

| Contentor | Repositório |
|-----------|------------|
| Discurso-a-texto personalizado | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Conversão de texto em voz](#tab/tts)

| Contentor | Repositório |
|-----------|------------|
| Conversão de texto em voz | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Texto-a-discurso neural](#tab/ntts)

| Contentor | Repositório |
|-----------|------------|
| Texto-a-discurso neural | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Texto-a-discurso personalizado](#tab/ctts)

| Contentor | Repositório |
|-----------|------------|
| Texto-a-discurso personalizado | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Deteção de linguagem da fala](#tab/lid)

> [!TIP]
> Para obter os resultados mais úteis, recomendamos a utilização do recipiente de deteção de linguagem da fala com os recipientes fala-a-texto ou personalizados. 

| Contentor | Repositório |
|-----------|------------|
| Deteção de linguagem da fala | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker puxa para os recipientes da fala

# <a name="speech-to-text"></a>[Conversão de voz em texto](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker puxa para o recipiente discurso-a-texto

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo do Microsoft Container.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> A `latest` etiqueta puxa o `en-US` local. Para locais adicionais consulte [locais de voz a texto.](#speech-to-text-locales)

#### <a name="speech-to-text-locales"></a>Locais de fala para texto

Todas as tags, com exceção `latest` das marcas, são no seguinte formato e são sensíveis a casos:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

A seguinte etiqueta é um exemplo do formato:

```
2.6.0-amd64-en-us
```

Para todos os locais suportados do recipiente **discurso-a-texto,** consulte [as etiquetas de imagem discurso-a-texto](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Discurso-a-texto personalizado](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker puxa para o recipiente personalizado discurso-a-texto

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo do Microsoft Container.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> Os `locale` `voice` recipientes e para os recipientes de fala personalizados são determinados pelo modelo personalizado ingerido pelo recipiente.

# <a name="text-to-speech"></a>[Conversão de texto em voz](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker puxa para o recipiente texto-a-fala

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo do Microsoft Container.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> A `latest` etiqueta puxa o local e a `en-US` `ariarus` voz. Para locais adicionais consulte [locais de texto para falar.](#text-to-speech-locales)

#### <a name="text-to-speech-locales"></a>Locais de texto para falar

Todas as tags, com exceção `latest` das marcas, são no seguinte formato e são sensíveis a casos:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

A seguinte etiqueta é um exemplo do formato:

```
1.8.0-amd64-en-us-ariarus
```

Para todos os locais apoiados e vozes correspondentes do recipiente **texto-a-voz,** consulte [as etiquetas de imagem text-to-speech](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Ao construir um HTTP POST *DE TEXTO-A-Discurso,* a mensagem de síntese de [síntese de discurso (SSML)](speech-synthesis-markup.md) requer um `voice` elemento com um `name` atributo. O valor é o local e voz correspondentes do contentor, também conhecido como ["nome curto".](language-support.md#standard-voices) Por exemplo, a `latest` etiqueta teria um nome de voz de `en-US-AriaRUS` .

# <a name="neural-text-to-speech"></a>[Texto-a-discurso neural](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Docker puxa para o recipiente texto-a-fala neural

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo do Microsoft Container.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> A `latest` etiqueta puxa o local e a `en-US` `arianeural` voz. Para locais adicionais consulte [locais de texto-a-voz neural.](#neural-text-to-speech-locales)

#### <a name="neural-text-to-speech-locales"></a>Locais de texto-a-discurso neural

Todas as tags, com exceção `latest` das marcas, são no seguinte formato e são sensíveis a casos:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

A seguinte etiqueta é um exemplo do formato:

```
1.3.0-amd64-en-us-arianeural
```

Para todos os locais apoiados e vozes correspondentes do recipiente **neural texto-a-voz,** consulte [as etiquetas de imagem Neural Text-to-speech](../containers/container-image-tags.md#neural-text-to-speech).

> [!IMPORTANT]
> Ao construir um *texto neural para falar* HTTP POST, a mensagem de síntese de [síntese de discurso (SSML)](speech-synthesis-markup.md) requer um `voice` elemento com um `name` atributo. O valor é o local e voz correspondentes do contentor, também conhecido como ["nome curto".](language-support.md#neural-voices) Por exemplo, a `latest` etiqueta teria um nome de voz de `en-US-AriaNeural` .

# <a name="custom-text-to-speech"></a>[Texto-a-discurso personalizado](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker puxa para o recipiente text-to-speech personalizado

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo do Microsoft Container.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> Os `locale` `voice` recipientes e para os recipientes de fala personalizados são determinados pelo modelo personalizado ingerido pelo recipiente.

# <a name="speech-language-detection"></a>[Deteção de linguagem da fala](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Docker puxa para o recipiente de deteção de linguagem da fala

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo do Microsoft Container.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>Como usar o recipiente

Uma vez que o recipiente esteja no [computador anfitrião,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de faturação necessárias. Mais [exemplos](speech-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.
1. [Consultar o ponto final de previsão do recipiente.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com `docker run`

Use o comando de execução do [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o contentor. Consulte a [recolha dos parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{Endpoint_URI}` valores e `{API_Key}` valores. Estão também disponíveis [exemplos](speech-container-configuration.md#example-docker-run-commands) adicionais do `docker run` comando.

# <a name="speech-to-text"></a>[Conversão de voz em texto](#tab/stt)

Para executar o recipiente *padrão discurso-texto,* execute o seguinte `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente *discurso-texto* a partir da imagem do recipiente.
* Atribui 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

> [!NOTE]
> Os recipientes suportam a entrada de áudio comprimido para o Speech SDK utilizando o GStreamer.
> Para instalar o GStreamer num recipiente, siga as instruções do Linux para GStreamer in [Use codec comprimida com o SDK de fala](how-to-use-codec-compressed-audio-input-streams.md).

#### <a name="diarization-on-the-speech-to-text-output"></a>Diarização na saída do discurso para o texto
A diarização é ativada por predefinição. para obter a diarização na sua resposta, use `diarize_speech_config.set_service_property` .

1. Desa estação o formato de saída da frase `Detailed` para .
2. Defina o modo de diarização. Os modos suportados são `Identity` e `Anonymous` .
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> O modo "Identidade" volta `"SpeakerId": "Customer"` ou `"SpeakerId": "Agent"` .
> Modo "anónimo" retorna `"SpeakerId": "Speaker 1"` ou `"SpeakerId": "Speaker 2"`


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analisar o sentimento sobre a saída fala-a-texto 
A partir de v2.6.0 do recipiente de fala para texto, deve utilizar o ponto final textanalytics 3.0 API em vez do de pré-visualização. Por exemplo
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> A API de Análise de Texto `v3.0` não é compatível com Text Analytics `v3.0-preview.1` . Para obter o suporte mais recente do recurso de sentimento, utilize `v2.6.0` a imagem do recipiente de porta-a-texto e a Análise de Texto `v3.0` .

A partir de v2.2.0 do recipiente de fala-a-texto, pode chamar a análise de [sentimento v3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) na saída. Para chamar a análise de sentimento, você precisará de um ponto final de recursos API API de texto. Por exemplo: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Se estiver a aceder a um ponto final de análise de texto na nuvem, precisará de uma chave. Se estiver a executar Text Analytics localmente, poderá não precisar de o fornecer.

A chave e o ponto final são passados para o recipiente da Fala como argumentos, como no exemplo seguinte.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Este comando:

* Executa os mesmos passos que o comando acima.
* Armazena um ponto final e chave da API de Análise de Texto, para o envio de pedidos de análise de sentimento. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>Fraseista v2 na saída fala-a-texto 

A partir de v2.6.0 do recipiente discurso-a-texto, você pode obter a saída com as suas próprias frases - ou toda a frase, ou frases no meio. Por *exemplo, o homem alto* na seguinte frase:

* "Esta é uma frase **o homem alto** esta é outra frase."

Para configurar uma lista de frases, é necessário adicionar as suas próprias frases quando fizer a chamada. Por exemplo:

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)

```

Se tiver várias frases a adicionar, chame `.addPhrase()` cada frase para adicioná-la à lista de frases. 


# <a name="custom-speech-to-text"></a>[Discurso-a-texto personalizado](#tab/cstt)

O recipiente *personalizado discurso-a-texto baseia-se* num modelo de fala personalizado. O modelo personalizado tem de ter sido [treinado](how-to-custom-speech-train-model.md) utilizando o [portal de fala personalizado.](https://speech.microsoft.com/customspeech)

O **ID do modelo de** fala personalizado é necessário para executar o recipiente. Pode ser encontrado na página **de Formação** do portal de fala personalizado. A partir do portal de fala personalizado, navegue até à página **Treino** e selecione o modelo.
<br>

![Página de treinamento de fala personalizada](media/custom-speech/custom-speech-model-training.png)

Obtenha o **ID do modelo** para usar como argumento para o `ModelId` parâmetro do `docker run` comando.
<br>

![Detalhes personalizados do modelo de discurso](media/custom-speech/custom-speech-model-details.png)

O quadro a seguir representa os `docker run` vários parâmetros e as respetivas descrições:

| Parâmetro | Descrição |
|---------|---------|
| `{VOLUME_MOUNT}` | O suporte de [volume do](https://docs.docker.com/storage/volumes/)computador do anfitrião, que o estivador usa para persistir no modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada na máquina hospedeira. |
| `{MODEL_ID}` | O **ID do Modelo de** Discurso Personalizado da página **de Formação** do portal de fala personalizado. |
| `{ENDPOINT_URI}` | O ponto final é necessário para a medição e faturação. Para obter mais informações, consulte [a recolha dos parâmetros necessários.](#gathering-required-parameters) |
| `{API_KEY}` | A chave API é necessária. Para obter mais informações, consulte [a recolha dos parâmetros necessários.](#gathering-required-parameters) |

Para executar o recipiente *discurso-texto personalizado,* execute o seguinte `docker run` comando:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente *personalizado de discurso a texto* a partir da imagem do recipiente.
* Atribui 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Carrega o modelo *Personalizado Discurso-a-Texto* a partir do suporte de entrada de volume, por exemplo *C:\CustomSpeech*.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Descarrega o modelo dado o `ModelId` (se não for encontrado no suporte de volume).
* Se o modelo personalizado foi previamente descarregado, o `ModelId` é ignorado.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Descarregamento do modelo base no recipiente personalizado discurso-a-texto  
A partir de v2.6.0 do recipiente personalizado discurso-a-texto, pode obter as informações do modelo base disponíveis utilizando a opção `BaseModelLocale=<locale>` . Esta opção irá dar-lhe uma lista de modelos base disponíveis nesse local sob a sua conta de faturação. Por exemplo:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente *personalizado de discurso a texto* a partir da imagem do recipiente.
* Verifique e devolva os modelos base disponíveis do local alvo.

A saída dá-lhe uma lista de modelos base com a informação local, id de modelo e hora de data de criação. Pode utilizar o id do modelo para descarregar e utilizar o modelo base específico que prefere. Por exemplo:
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Pronúncia personalizada no recipiente personalizado discurso-a-texto 
A partir de v2.5.0 do recipiente personalizado discurso-a-texto, você pode obter o resultado da pronúncia personalizada na saída. Tudo o que precisa fazer é ter as suas próprias regras de pronúncia personalizadas definidas no seu modelo personalizado e montar o modelo para um recipiente personalizado de discurso a texto.


# <a name="text-to-speech"></a>[Conversão de texto em voz](#tab/tts)

Para executar o recipiente *Texto-a-fala* Padrão, execute o seguinte `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente *texto-a-fala* padrão a partir da imagem do recipiente.
* Atribui 1 núcleo cpu e 2 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

# <a name="neural-text-to-speech"></a>[Texto-a-discurso neural](#tab/ntts)

Para executar o recipiente *Texto-a-fala Neural,* execute o seguinte `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente *neural texto-a-fala* a partir da imagem do recipiente.
* Atribui 6 núcleos de CPU e 12 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

# <a name="custom-text-to-speech"></a>[Texto-a-discurso personalizado](#tab/ctts)

O recipiente *text-to-speech personalizado baseia-se* num modelo de voz personalizado. O modelo personalizado tem de ter sido [treinado](how-to-custom-voice-create-voice.md) utilizando o [portal de voz personalizado.](https://aka.ms/custom-voice-portal) O **ID do modelo de** voz personalizado é necessário para executar o recipiente. Pode ser encontrado na página **de Treino** do portal de voz personalizado. A partir do portal de voz personalizado, navegue até à página **Treino** e selecione o modelo.
<br>

![Página de treinamento de voz personalizada](media/custom-voice/custom-voice-model-training.png)

Obtenha o **ID do modelo** para usar como argumento para o `ModelId` parâmetro do comando de execução do estivador.
<br>

![Detalhes personalizados do modelo de voz](media/custom-voice/custom-voice-model-details.png)

O quadro a seguir representa os `docker run` vários parâmetros e as respetivas descrições:

| Parâmetro | Descrição |
|---------|---------|
| `{VOLUME_MOUNT}` | O suporte de [volume do](https://docs.docker.com/storage/volumes/)computador do anfitrião, que o estivador usa para persistir no modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada na máquina hospedeira. |
| `{MODEL_ID}` | O **ID do Modelo de** Discurso Personalizado a partir da página de **Treino** do portal de voz personalizado. |
| `{ENDPOINT_URI}` | O ponto final é necessário para a medição e faturação. Para obter mais informações, consulte [a recolha dos parâmetros necessários.](#gathering-required-parameters) |
| `{API_KEY}` | A chave API é necessária. Para obter mais informações, consulte [a recolha dos parâmetros necessários.](#gathering-required-parameters) |

Para executar o recipiente *text-to-speech personalizado,* execute o seguinte `docker run` comando:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente *de texto para a fala personalizado* a partir da imagem do recipiente.
* Atribui 1 núcleo cpu e 2 gigabytes (GB) de memória.
* Carrega o modelo *de texto para voz personalizado* a partir do suporte de entrada de volume, por exemplo *C:\CustomVoice*.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Descarrega o modelo dado o `ModelId` (se não for encontrado no suporte de volume).
* Se o modelo personalizado foi previamente descarregado, o `ModelId` é ignorado.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

# <a name="speech-language-detection"></a>[Deteção de linguagem da fala](#tab/lid)

Para executar o recipiente *de deteção de linguagem* da fala, execute o seguinte `docker run` comando.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando: 

* Executa um recipiente de deteção de linguagem da fala a partir da imagem do recipiente. Atualmente não será cobrado por executar esta imagem.
* Atribui 1 núcleos de CPU e 1 gigabyte (GB) de memória.
* Expõe a porta TCP 5003 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

Se enviar apenas pedidos de Deteção de Linguagem da Fala, terá de definir o valor do cliente da Fala `phraseDetection` para `None` .  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Se quiser executar este recipiente com o recipiente de fala para texto, pode utilizar esta [imagem do Docker](https://hub.docker.com/r/antsu/on-prem-client). Depois de iniciar ambos os contentores, utilize este comando Docker Run para executar `speech-to-text-with-languagedetection-client` .

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Aumentar o número de chamadas simultâneas pode ter impacto na fiabilidade e latência. Para a deteção de idiomas, recomendamos um máximo de 4 chamadas simultâneas usando 1 CPU com e 1GB de memória. Para anfitriões com 2 CPUs e 2GB de memória, recomendamos um máximo de 6 chamadas simultâneas.

***

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](#billing)

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

> [!NOTE]
> Utilize um número de porta único se estiver a executar vários contentores.

| Contentores | URL anfitrião SDK | Protocolo |
|--|--|--|
| Discurso-a-texto padrão e discurso-a-texto personalizado | `ws://localhost:5000` | WS |
| Texto-a-discurso (incluindo Standard, Custom e Neural), deteção de linguagem da fala | `http://localhost:5000` | HTTP |

Para obter mais informações sobre a utilização dos protocolos WSS e HTTPS, consulte a [segurança do contentor.](../cognitive-services-container-support.md#azure-cognitive-services-container-security)

### <a name="speech-to-text-standard-and-custom"></a>Discurso-a-texto (Padrão e Personalizado)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analisar sentimento

Se forneceu as suas credenciais de API de Análise de Texto [ao recipiente,](#analyze-sentiment-on-the-speech-to-text-output)pode utilizar o Speech SDK para enviar pedidos de reconhecimento de voz com análise de sentimento. Pode configurar as respostas da API para utilizar um formato *simples* ou *detalhado.*
> [!NOTE]
> v1.13 do Serviço de Fala Python SDK tem um problema identificado com a análise do sentimento. Por favor, use v1.12.x ou mais cedo se estiver a usar a análise de sentimento no Serviço de Fala Python SDK.

# <a name="simple-format"></a>[Formato simples](#tab/simple-format)

Para configurar o cliente Discurso para utilizar um formato simples, adicione `"Sentiment"` como um valor para `Simple.Extensions` . Se quiser escolher uma versão específica do modelo Text Analytics, `'latest'` substitua-a na configuração da `speechcontext-phraseDetection.sentimentAnalysis.modelversion` propriedade.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` irá devolver o resultado do sentimento na camada raiz da resposta.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Formato detalhado](#tab/detailed-format)

Para configurar o cliente Do Discurso para usar um formato detalhado, adicione `"Sentiment"` como um valor para , ou `Detailed.Extensions` `Detailed.Options` ambos. Se quiser escolher uma versão específica do modelo Text Analytics, `'latest'` substitua-a na configuração da `speechcontext-phraseDetection.sentimentAnalysis.modelversion` propriedade.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` fornece o resultado do sentimento na camada raiz da resposta. `Detailed.Options` fornece o resultado na `NBest` camada da resposta. Podem ser usados separadamente ou em conjunto.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Se quiser desativar completamente a análise de sentimentos, adicione um `false` valor a `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Texto-a-discurso (Padrão, Neural e Personalizado)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários recipientes no mesmo hospedeiro

Se pretender executar vários recipientes com portas expostas, certifique-se de que funciona cada recipiente com uma porta exposta diferente. Por exemplo, executar o primeiro contentor no porto 5000 e o segundo contentor no porto 5001.

Você pode ter este recipiente e um recipiente diferente Azure Cognitive Services funcionando no HOST juntos. Também pode ter vários contentores do mesmo recipiente dos Serviços Cognitivos em funcionamento.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Ao iniciar ou executar o recipiente, poderá sentir problemas. Utilize um [suporte de](speech-container-configuration.md#mount-settings) saída e ative o registo. Ao fazê-lo, permitirá que o recipiente gere ficheiros de registo que sejam úteis quando se trata de problemas de resolução de problemas.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os recipientes de Discurso enviam informações de faturação para a Azure, utilizando um recurso *de Discurso* na sua conta Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [os recipientes Configure](speech-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar recipientes de discurso. Em resumo:

* A fala fornece quatro recipientes Linux para Docker, encapsulando várias capacidades:
  * *Conversão de voz em texto*
  * *Discurso-a-texto personalizado*
  * *Conversão de texto em voz*
  * *Texto-a-discurso personalizado*
  * *Texto-a-discurso neural*
  * *Deteção de linguagem da fala*
* As imagens do contentor são descarregadas do registo do contentor em Azure.
* Imagens de contentores correm em Docker.
* Quer utilize a API REST (texto-a-discurso apenas) ou o SDK (Discurso-a-texto ou Texto-a-fala) especifica o URI do anfitrião do recipiente. 
* É-lhe exigido que forneça informações de faturação ao instantaneamente um contentor.

> [!IMPORTANT]
>  Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem estarem ligados ao Azure para a medição. Os clientes precisam de permitir que os contentores comuniquem informações de faturação com o serviço de medição em todos os momentos. Os recipientes de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Reveja os recipientes de configuração](speech-container-configuration.md) para configurações de configuração
* Saiba como [usar recipientes de serviço de fala com Kubernetes e Helm](speech-container-howto-on-premises.md)
* Use mais [recipientes de Serviços Cognitivos](../cognitive-services-container-support.md)
