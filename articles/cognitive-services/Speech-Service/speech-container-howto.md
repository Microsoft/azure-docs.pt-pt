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
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: c8efc416555996ef9a1abd79ec5aafcb65186d6f
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316668"
---
# <a name="install-and-run-speech-service-containers"></a>Instalar e executar contêineres de serviço de fala

Os contêineres de fala permitem que os clientes criem uma arquitetura de aplicativo de fala otimizada para aproveitar os recursos robustos de nuvem e a localidade de borda. 

Os dois contêineres de fala são **conversão de fala em texto** e **texto em fala**. 

|Função|Funcionalidades|Mais Recente|
|-|-|--|
|Conversão de voz em texto| <li>Transcreve as gravações contínuas de fala em tempo real ou de áudio em lotes em texto com resultados intermediários.|1.2.0|
|Texto em Voz| <li>Converte texto em voz com som natural. com a entrada de texto sem formatação ou a linguagem de marcação de síntese de fala (SSML). |1.2.0|

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar contêineres de fala:

|Requerido|Objetivo|
|--|--|
|Mecanismo do Docker| Você precisa do mecanismo do Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [MacOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/)e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | Você deve ter uma compreensão básica dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como o conhecimento de comandos `docker` básicos.| 
|Recurso de fala |Para usar esses contêineres, você deve ter:<br><br>Um recurso de _fala_ do Azure para obter a chave de API e o URI de ponto de extremidade associados. Ambos os valores estão disponíveis nas páginas visão geral de **fala** e chaves de portal do Azure. Eles são necessários para iniciar o contêiner.<br><br>**{API_KEY}** : Uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}** : O ponto de extremidade conforme fornecido na página **visão geral**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registro de contêiner

Você deve primeiro concluir e enviar o [formulário de solicitação de contêineres de fala dos serviços cognitivas](https://aka.ms/speechcontainerspreview/) para solicitar acesso ao contêiner. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte à extensão de vetor avançado

O **host** é o computador que executa o contêiner do Docker. O host deve dar suporte a [extensões de vetor avançadas](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Você pode verificar esse suporte em hosts Linux com o seguinte comando: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela a seguir descreve os núcleos de CPU mínimos e recomendados e a memória a ser alocada para cada contêiner de fala.

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 núcleos<br>memória de 2 GB  | 4 núcleos<br>4 GB de memória  |
|cognitiva – serviços-conversão de texto em fala | 1 núcleo, 0,5 GB de memória| 2 núcleos, 1 GB de memória |

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a `--cpus` memória correspondem às configurações e `--memory` , que são `docker run` usadas como parte do comando.

**Observação**; O mínimo e recomendado são baseados nos limites do Docker, *não* nos recursos da máquina host. Por exemplo, os contêineres de conversão de texto na memória mapeiam partes de um modelo de linguagem grande e é recomendável que todo o arquivo caiba na memória, que é de 4-6 GB adicionais. Além disso, a primeira execução de qualquer um dos contêineres pode levar mais tempo, pois os modelos estão sendo paginados na memória.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com`docker pull`

As imagens de contêiner para fala estão disponíveis.

| Contentor | Repositório |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitiva – serviços-conversão de texto em fala | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>A localidade do idioma está na marca do contêiner

A `latest` marca extrai a localidade `en-us` e `jessarus` a voz.

#### <a name="speech-to-text-locales"></a>As localidades de fala para texto

Todas as marcas, exceto `latest` no, estão no seguinte formato, em `<culture>` que o indica o contêiner de localidade:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

A marca a seguir é um exemplo do formato:

```
1.2.0-amd64-en-us-preview
```

A tabela a seguir lista as localidades com suporte para a **conversão de fala em texto** na versão 1.2.0 do contêiner:

|Localidade do idioma|Tags|
|--|--|
|Chinês|`zh-cn`|
|Português |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Francês |`fr-ca`<br>`fr-fr`|
|Alemão|`de-de`|
|Italiano|`it-it`|
|Japonês|`ja-jp`|
|Coreano|`ko-kr`|
|Português|`pt-br`|
|Espanhol|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>Texto para localidades de fala

Todas as marcas, exceto `latest` para estão no seguinte formato, em que `<culture>` o `<voice>` indica a localidade e indica a voz do contêiner:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

A marca a seguir é um exemplo do formato:

```
1.2.0-amd64-en-us-jessarus-preview
```

A tabela a seguir lista as localidades com suporte para **conversão de texto em fala** na versão 1.2.0 do contêiner:

|Localidade do idioma|Tags|Vozes com suporte|
|--|--|--|
|Chinês|`zh-cn`|huihuirus<br>kangkang-Apollo<br>yaoyao-Apollo|
|Português |`en-au`|catherine<br>hayleyrus|
|Português |`en-gb`|George-Apollo<br>hazelrus<br>Susan-Apollo|
|Português |`en-in`|Heera-Apollo<br>priyarus<br>Ravi-Apollo<br>|
|Português |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Francês|`fr-ca`|Carolina<br>harmonierus|
|Francês|`fr-fr`|hortenserus<br>Julie-Apollo<br>Paul-Apollo|
|Alemão|`de-de`|hedda<br>heddarus<br>Stefan-Apollo|
|Italiano|`it-it`|Cosimo-Apollo<br>luciarus|
|Japonês|`ja-jp`|Ayumi-Apollo<br>harukarus<br>Ichiro-Apollo|
|Coreano|`ko-kr`|heamirus|
|Português|`pt-br`|Daniel-Apollo<br>heloisarus|
|Espanhol|`es-es`|elenarus<br>Laura-Apollo<br>Pablo-Apollo<br>|
|Espanhol|`es-mx`|hildarus<br>Ricardo-Apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Pull do Docker para os contêineres de fala

#### <a name="speech-to-text"></a>Conversão de voz em texto

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Conversão de texto em voz

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run)com as configurações de cobrança necessárias. Mais [exemplos](speech-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Execute o contêiner com`docker run`

Use o comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{ENDPOINT_URI}` valores `{API_KEY}` e.

[Exemplos](speech-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

> [!NOTE]
> **Durante a visualização**, as configurações de cobrança devem ser válidas para iniciar o contêiner, mas você não é cobrado pelo uso.

### <a name="text-to-speech"></a>Conversão de texto em voz

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>Conversão de voz em texto

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um contêiner de fala da imagem de contêiner
* Aloca 2 núcleos de CPU e 2 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host.

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

|Contentor|Ponto Final|
|--|--|
|Conversão de voz em texto|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Conversão de texto em voz|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Conversão de voz em texto

O contêiner fornece APIs de ponto de extremidade de consulta baseadas no WebSocket, que são acessadas por meio do [SDK de fala](index.yml).

Por padrão, o SDK de fala usa os serviços de fala online. Para usar o contêiner, você precisa alterar o método de inicialização. Consulte os exemplos abaixo.

#### <a name="for-c"></a>FinsC#

Alterar do uso desta chamada de inicialização do Azure-cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

para essa chamada usando o ponto de extremidade do contêiner:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Para Python

Alterar do uso desta chamada de inicialização do Azure-cloud

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

para essa chamada usando o ponto de extremidade do contêiner:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Conversão de texto em voz

O contêiner fornece APIs de ponto de extremidade REST que podem ser encontradas [aqui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) e exemplos podem ser encontrados [aqui](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se você executar o contêiner com uma [montagem](speech-container-configuration.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem ao iniciar ou executar o contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os contêineres de fala enviam informações de cobrança para o Azure, usando um recurso de _fala_ em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar contêineres de fala. Em resumo:

* A fala fornece dois contêineres do Linux para o Docker, encapsulando a fala em texto e texto em fala.
* As imagens de contêiner são baixadas do registro de contêiner privado no Azure.
* Executam imagens de contentor no Docker.
* Você pode usar a API REST ou o SDK para chamar operações em contêineres de fala especificando o URI do host do contêiner.
* Você precisa fornecer informações de cobrança ao criar uma instância de um contêiner.

> [!IMPORTANT]
>  Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Revisão [configurar contentores](speech-container-configuration.md) para definições de configuração
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)
