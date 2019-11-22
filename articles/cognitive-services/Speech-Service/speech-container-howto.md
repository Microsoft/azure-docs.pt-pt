---
title: Instalar contêineres de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Instale e execute contêineres de fala. A fala a texto transcreve fluxos de áudio para texto em tempo real que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Conversão de texto em fala converte o texto de entrada em fala sintetizada por meio do tipo humano.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: b83ece803a72fe6132c1ba98414e95897a0fcc90
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304874"
---
# <a name="install-and-run-speech-service-containers"></a>Instalar e executar contêineres de serviço de fala

Os contêineres permitem executar algumas das APIs do serviço de fala em seu próprio ambiente. Os contêineres são ótimos para requisitos específicos de segurança e governança de dados. Neste artigo, você aprenderá a baixar, instalar e executar um contêiner de fala.

Os contêineres de fala permitem que os clientes criem uma arquitetura de aplicativo de fala otimizada para recursos de nuvem robustos e localidade de borda. Há quatro contêineres diferentes disponíveis. Os dois contêineres padrão são Text **-to-Text** e **conversão de texto em fala**. Os dois contêineres personalizados são **fala personalizada para texto** e **texto em fala personalizado**.

> [!IMPORTANT]
> Atualmente, todos os contêineres de fala são oferecidos como parte de uma [Visualização pública "restrita"](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Um comunicado será feito quando os contêineres de fala progredirem para disponibilidade geral (GA).

| Função | Funcionalidades | Mais recente |
|--|--|--|
| Conversão de voz em texto | Transcreve as gravações contínuas de fala em tempo real ou de áudio em lotes em texto com resultados intermediários. | 2.0.0 |
| Fala Personalizada para texto | Usar um modelo personalizado do [portal de fala personalizada](https://speech.microsoft.com/customspeech), transcreve gravações contínuas em tempo real ou de áudio em lotes em texto com resultados intermediários. | 2.0.0 |
| Conversão de texto em voz | Converte texto em fala natural-som com entrada de texto sem formatação ou SSML (linguagem de marcação de síntese de fala). | 1.3.0 |
| Conversão de texto em fala personalizada | Usando um modelo personalizado do [portal de voz personalizado](https://aka.ms/custom-voice-portal), o converte o texto em fala de som natural com entrada de texto sem formatação ou SSML (linguagem de marcação de síntese de fala). | 1.3.0 |

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar os contêineres de fala:

| Necessário | Objetivo |
|--|--|
| Mecanismo do Docker | Você precisa do mecanismo do Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [MacOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/)e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br> |
| Familiaridade com o Docker | Você deve ter uma compreensão básica dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como o conhecimento de comandos básicos de `docker`. |
| Recurso de fala | Para usar esses contêineres, você deve ter:<br><br>Um recurso de _fala_ do Azure para obter a chave de API e o URI de ponto de extremidade associados. Ambos os valores estão disponíveis nas páginas visão geral de **fala** e chaves de portal do Azure. Eles são necessários para iniciar o contêiner.<br><br>**{Api_key}** : uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}** : o ponto de extremidade conforme fornecido na página **visão geral** |

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registro de contêiner

Preencha e envie o [formulário de solicitação de contêineres de fala dos serviços cognitivas](https://aka.ms/speechcontainerspreview/) para solicitar acesso ao contêiner. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte à extensão de vetor avançado

O **host** é o computador que executa o contêiner do Docker. O host *deve dar suporte* a [extensões de vetor avançadas](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Você pode verificar o suporte a AVX2 em hosts Linux com o seguinte comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> O computador host é *necessário* para dar suporte a AVX2. O contêiner *não* funcionará corretamente sem o suporte do AVX2.

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada contêiner de fala.

# <a name="speech-to-texttabstt"></a>[Conversão de voz em texto](#tab/stt)

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Conversão de voz em texto | 2 núcleos, 2 GB de memória | 4 núcleos, 4 GB de memória |

# <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Fala Personalizada para texto | 2 núcleos, 2 GB de memória | 4 núcleos, 4 GB de memória |

# <a name="text-to-speechtabtts"></a>[Conversão de texto em voz](#tab/tts)

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Conversão de texto em voz | 1 núcleo, 2 GB de memória | 2 núcleos, 3 GB de memória |

# <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Conversão de texto em fala personalizada | 1 núcleo, 2 GB de memória | 2 núcleos, 3 GB de memória |

***

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a memória correspondem às configurações de `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

> [!NOTE]
> O mínimo e recomendado são baseados nos limites do Docker, *não* nos recursos da máquina host. Por exemplo, os contêineres de conversão de texto na memória mapeiam partes de um modelo de linguagem grande e é *recomendável* que todo o arquivo caiba na memória, que é de 4-6 GB adicionais. Além disso, a primeira execução de qualquer um dos contêineres pode levar mais tempo, pois os modelos estão sendo paginados na memória.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

As imagens de contêiner para fala estão disponíveis no registro de contêiner a seguir.

# <a name="speech-to-texttabstt"></a>[Conversão de voz em texto](#tab/stt)

| Contentor | Repositório |
|-----------|------------|
| Conversão de voz em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

| Contentor | Repositório |
|-----------|------------|
| Fala Personalizada para texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Conversão de texto em voz](#tab/tts)

| Contentor | Repositório |
|-----------|------------|
| Conversão de texto em voz | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

| Contentor | Repositório |
|-----------|------------|
| Conversão de texto em fala personalizada | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Pull do Docker para os contêineres de fala

# <a name="speech-to-texttabstt"></a>[Conversão de voz em texto](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Pull do Docker para o contêiner de conversão de fala em texto

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> A marca de `latest` extrai a localidade `en-US`. Para localidades adicionais, consulte [localidades de fala para texto](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Localidades de conversão de fala em texto

Todas as marcas, com exceção de `latest` estão no seguinte formato e diferenciam maiúsculas de minúsculas:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

A marca a seguir é um exemplo do formato:

```
2.0.0-amd64-en-us-preview
```

Para todas as localidades com suporte do contêiner de **fala a texto** , consulte [marcas de imagem de fala para texto](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Pull do Docker para o contêiner de Fala Personalizada para texto

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> O `locale` e `voice` para contêineres de fala personalizados são determinados pelo modelo personalizado ingerido pelo contêiner.

# <a name="text-to-speechtabtts"></a>[Conversão de texto em voz](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Pull do Docker para o contêiner de conversão de texto em fala

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> A marca de `latest` extrai a localidade `en-US` e `jessarus` voz. Para localidades adicionais, consulte [localidades de conversão de texto em fala](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Localidades de conversão de texto em fala

Todas as marcas, com exceção de `latest` estão no seguinte formato e diferenciam maiúsculas de minúsculas:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

A marca a seguir é um exemplo do formato:

```
1.3.0-amd64-en-us-jessarus-preview
```

Para todas as localidades com suporte e as vozes correspondentes do contêiner de **conversão de texto em fala** , confira [marcas de imagem de texto em fala](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Ao construir um HTTP POST *padrão de conversão de texto em fala* , a mensagem de [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md) requer um elemento `voice` com um atributo `name`. O valor é a localidade de contêiner correspondente e voz, também conhecido como ["nome curto"](language-support.md#standard-voices). Por exemplo, a marca de `latest` teria um nome de voz de `en-US-JessaRUS`.

# <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Pull do Docker para o contêiner personalizado de conversão de texto em fala

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> O `locale` e `voice` para contêineres de fala personalizados são determinados pelo modelo personalizado ingerido pelo contêiner.

***

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run)com as configurações de cobrança necessárias. Mais [exemplos](speech-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Execute o contêiner com `docker run`

Use o comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os valores de `{Endpoint_URI}` e de `{API_Key}`. [Exemplos](speech-container-configuration.md#example-docker-run-commands) adicionais do comando `docker run` também estão disponíveis.

# <a name="speech-to-texttabstt"></a>[Conversão de voz em texto](#tab/stt)

Para executar o contêiner de *conversão de fala em texto* , execute o comando `docker run` a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um contêiner de *conversão de fala em texto* da imagem de contêiner.
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host.

# <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

O contêiner de *fala personalizada para texto* depende de um modelo de fala personalizado. O modelo personalizado deve ter sido [treinado](how-to-custom-speech-train-model.md) usando o [portal de fala personalizado](https://speech.microsoft.com/customspeech). A ID do **modelo** de fala personalizado é necessária para executar o contêiner. Ele pode ser encontrado na página de **treinamento** do portal de fala personalizado. No portal de fala personalizado, navegue até a página de **treinamento** e selecione o modelo.
<br>

![Página de treinamento de fala personalizada](media/custom-speech/custom-speech-model-training.png)

Obtenha a **ID do modelo** para usar como o argumento para o parâmetro `ModelId` do comando `docker run`.
<br>

![Detalhes do modelo de fala personalizado](media/custom-speech/custom-speech-model-details.png)

A tabela a seguir representa os vários parâmetros `docker run` e suas descrições correspondentes:

| Parâmetro | Descrição |
|---------|---------|
| `{VOLUME_MOUNT}` | A montagem de [volume](https://docs.docker.com/storage/volumes/)do computador host, que o Docker usa para manter o modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada no computador host. |
| `{MODEL_ID}` | A **ID do modelo** de fala personalizada da página de **treinamento** do portal de fala personalizado. |
| `{ENDPOINT_URI}` | O ponto de extremidade é necessário para medição e cobrança. Para obter mais informações, consulte [coletando parâmetros necessários](#gathering-required-parameters). |
| `{API_KEY}` | A chave de API é necessária. Para obter mais informações, consulte [coletando parâmetros necessários](#gathering-required-parameters). |

Para executar o contêiner de *fala personalizada para texto* , execute o seguinte comando de `docker run`:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um contêiner de *fala personalizada para texto* a partir da imagem de contêiner.
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Carrega o modelo de *fala personalizada para texto* da montagem de entrada de volume, por exemplo, *C:\CustomSpeech*.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Baixa o modelo de acordo com o `ModelId` (se não for encontrado na montagem do volume).
* Se o modelo personalizado tiver sido baixado anteriormente, o `ModelId` será ignorado.
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host.

# <a name="text-to-speechtabtts"></a>[Conversão de texto em voz](#tab/tts)

Para executar o contêiner de *conversão de texto em fala* , execute o comando `docker run` a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um contêiner de *conversão de texto em fala* da imagem de contêiner.
* Aloca 2 núcleos de CPU e um gigabyte (GB) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host.

# <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

O contêiner de *texto em fala personalizado* depende de um modelo de voz personalizado. O modelo personalizado deve ter sido [treinado](how-to-custom-voice-create-voice.md) usando o [portal de voz personalizado](https://aka.ms/custom-voice-portal). A ID do **modelo** de voz personalizado é necessária para executar o contêiner. Ele pode ser encontrado na página de **treinamento** do portal de voz personalizado. No portal de voz personalizado, navegue até a página de **treinamento** e selecione o modelo.
<br>

![Página de treinamento de voz personalizada](media/custom-voice/custom-voice-model-training.png)

Obtenha a **ID do modelo** para usar como o argumento para o `ModelId` parâmetro do comando de execução do Docker.
<br>

![Detalhes do modelo de voz personalizado](media/custom-voice/custom-voice-model-details.png)

A tabela a seguir representa os vários parâmetros `docker run` e suas descrições correspondentes:

| Parâmetro | Descrição |
|---------|---------|
| `{VOLUME_MOUNT}` | A montagem de [volume](https://docs.docker.com/storage/volumes/)do computador host, que o Docker usa para manter o modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada no computador host. |
| `{MODEL_ID}` | A **ID do modelo** de fala personalizada da página de **treinamento** do portal de voz personalizado. |
| `{ENDPOINT_URI}` | O ponto de extremidade é necessário para medição e cobrança. Para obter mais informações, consulte [coletando parâmetros necessários](#gathering-required-parameters). |
| `{API_KEY}` | A chave de API é necessária. Para obter mais informações, consulte [coletando parâmetros necessários](#gathering-required-parameters). |

Para executar o contêiner de *texto em fala personalizado* , execute o seguinte comando de `docker run`:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um contêiner de *conversão de texto em fala personalizado* da imagem de contêiner.
* Aloca 2 núcleos de CPU e um gigabyte (GB) de memória.
* Carrega o modelo de *conversão de texto em fala personalizado* da montagem de entrada de volume, por exemplo, *C:\CustomVoice*.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Baixa o modelo de acordo com o `ModelId` (se não for encontrado na montagem do volume).
* Se o modelo personalizado tiver sido baixado anteriormente, o `ModelId` será ignorado.
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host.

***

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

| Contentor | Ponto Final | Protocolo |
|--|--|--|
| Conversão de voz em texto | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | Federation |
| Fala Personalizada para texto | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | Federation |
| Conversão de texto em voz | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |
| Conversão de texto em fala personalizada | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |

Para obter mais informações sobre como usar os protocolos do WSS e HTTPS, consulte [segurança do contêiner](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Conversão de texto em fala ou personalizada para fala

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, certifique-se de executar cada contêiner com uma porta exposta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

Você pode ter esse contêiner e um contêiner diferente de serviços cognitivas do Azure em execução no HOST juntos. Você também pode ter vários contêineres do mesmo contêiner de serviços cognitivas em execução.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Ao iniciar ou executar o contêiner, você poderá ter problemas. Use uma [montagem](speech-container-configuration.md#mount-settings) de saída e habilite o registro em log. Isso permitirá que o contêiner gere arquivos de log que são úteis ao solucionar problemas.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os contêineres de fala enviam informações de cobrança para o Azure, usando um recurso de *fala* em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar contêineres de fala. Em resumo:

* A fala fornece quatro contêineres do Linux para o Docker, encapsulando vários recursos:
  * *Conversão de voz em texto*
  * *Fala Personalizada para texto*
  * *Conversão de texto em voz*
  * *Conversão de texto em fala personalizada*
* As imagens de contêiner são baixadas do registro de contêiner no Azure.
* Executam imagens de contentor no Docker.
* Você pode usar a API REST ou o SDK para chamar operações em contêineres de fala especificando o URI do host do contêiner.
* Você precisa fornecer informações de cobrança ao criar uma instância de um contêiner.

> [!IMPORTANT]
>  Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Examinar [definir contêineres](speech-container-configuration.md) para definições de configuração
* Saiba como [usar contêineres de serviço de fala com kubernetes e Helm](speech-container-howto-on-premises.md)
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)
