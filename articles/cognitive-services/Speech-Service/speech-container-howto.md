---
title: Instalar os contentores de voz
titleSuffix: Azure Cognitive Services
description: Instalar e executar contentores de voz. Conversão de voz em texto converte os fluxos de áudio em texto em tempo real que seus aplicativos, ferramentas ou dispositivos podem consumir ou apresentar. Voz converte a entrada de texto em fala sintetizada de semelhante à humana.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 6df40909e2072ac2126344ba5b696c9e520e9955
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434153"
---
# <a name="install-and-run-speech-service-containers"></a>Instalar e executar contentores do serviço de voz

Contentores de voz permitem aos clientes criar uma arquitetura de aplicativos de fala que está otimizada para tirar partido das capacidades de cloud robustas e Localidade de borda. 

São os contentores de dois voz **voz em texto** e **voz**. 

|Função|Funcionalidades|mais recente|
|-|-|--|
|Conversão de voz em texto| <li>Converte contínuas em tempo real de voz ou lote gravações de áudio em texto com os resultados intermediários.|1.1.2|
|Texto em Voz| <li>Converte texto em voz com som natural. com a introdução de texto sem formatação ou Speech Synthesis Markup Language (SSML). |1.1.0|

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores de voz:

|Necessário|Objetivo|
|--|--|
|Motor do docker| É necessário o motor do Docker instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, Docker também tem de ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | Deve ter uma noção básica dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor, bem como dados de conhecimento do basic `docker` comandos.| 
|Recursos de voz |Para utilizar estes contentores, tem de ter:<br><br>R _voz_ recursos do Azure para obter a chave de faturação associada e a faturação URI do ponto final. Ambos os valores estão disponíveis no portal do Azure **voz** páginas de descrição geral e as chaves e são necessários para iniciar o contentor.<br><br>**{BILLING_KEY}** : chave de recurso<br><br>**{BILLING_ENDPOINT_URI}** : exemplo URI do ponto final é: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>Pedir acesso ao registo de contentor

Primeiro tem de concluir e submeter o [formulário de pedido de contentores de voz dos serviços cognitivos](https://aka.ms/speechcontainerspreview/) para pedir acesso ao contentor. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte avançado do Vetor de extensão

O **anfitrião** é o computador que executa o contentor do docker. O anfitrião tem de suportar [Advanced extensões de Vetor](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). É possível verificar esse suporte em anfitriões do Linux com o seguinte comando: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os mínimos e recomendados núcleos de CPU e memória para atribuir cada contentor de voz.

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 núcleos<br>2 GB de memória  | 4 núcleos<br>4 GB de memória  |
|cognitive-services-text-to-speech | 1 núcleo, 0,5 GB de memória| 2 núcleos, 1 GB de memória |

* Cada principal tem de ser, pelo menos, de 2,6 GHz (gigahertz) ou mais rápido.

Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.

**Tenha em atenção**; Mínimos e recomendados baseiam-se fora dos limites de Docker *não* recursos da máquina host. Por exemplo, é a partes de mapa de memória de contentores de voz em texto de um modelo de idioma grandes e ele _recomendado_ que todo o arquivo se encaixa na memória, o que é mais 4 a 6 GB. Além disso, a primeira execução de qualquer um dos contentores pode demorar mais, uma vez que os modelos estão a ser paginados na memória.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contentor com o `docker pull`

Imagens de contentor para conversão de voz estão disponíveis.

| Contentor | Repositório |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Região de idioma é na marca de contentor

O `latest` Etiquetar extrai o `en-us` localidade e `jessarus` voz.

#### <a name="speech-to-text-locales"></a>Conversão de voz em localidades de texto

Todas as etiquetas, exceto para `latest` estão no formato seguinte, onde o `<culture>` indica o contentor de localidade:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

A seguinte marca é um exemplo do formato de:

```
1.1.2-amd64-en-us-preview
```

A tabela seguinte lista as regiões suportadas para **voz em texto** no 1.1.2 versão do contentor:

|Região de idioma|Tags|
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

#### <a name="text-to-speech-locales"></a>Localidades de texto em voz

Todas as etiquetas, exceto para `latest` estão no formato seguinte, em que o `<culture>` indica a Localidade e o `<voice>` indica a voz do contentor:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

A seguinte marca é um exemplo do formato de:

```
1.1.0-amd64-en-us-jessarus-preview
```

A tabela seguinte lista as regiões suportadas para **voz** no 1.1.0 versão do contentor:

|Região de idioma|Tags|Vozes suportados|
|--|--|--|
|Chinês|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao apollo|
|Português |`en-au`|catherine<br>hayleyrus|
|Português |`en-gb`|George apollo<br>hazelrus<br>Susan apollo|
|Português |`en-in`|heera apollo<br>priyarus<br>ravi-apollo<br>|
|Português |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Francês|`fr-ca`|caroline<br>harmonierus|
|Francês|`fr-fr`|hortenserus<br>Julie apollo<br>Paul apollo|
|Alemão|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|Italiano|`it-it`|cosimo apollo<br>luciarus|
|Japonês|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Coreano|`ko-kr`|heamirus|
|Português|`pt-br`|Daniel apollo<br>heloisarus|
|Espanhol|`es-es`|elenarus<br>Laura apollo<br>Pablo apollo<br>|
|Espanhol|`es-mx`|hildarus<br>raul apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Solicitação de docker para os contentores de voz

#### <a name="speech-to-text"></a>Conversão de voz em texto

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Conversão de texto em voz

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Como utilizar o contentor

Assim que o contentor estiver no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

1. [Execute o contentor](#run-the-container-with-docker-run), com as definições de faturas necessárias mas não utilizadas. Obter mais [exemplos](speech-container-configuration.md#example-docker-run-commands) do `docker run` comandos estão disponíveis.
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Execute o contentor com `docker run`

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar qualquer um dos três contentores. O comando utiliza os seguintes parâmetros:

**Durante a pré-visualização**, as definições de faturas tem de ser válidas para iniciar o contentor, mas não são faturadas para utilização.

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor e está disponível na página de chaves de voz do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível na página de descrição geral de voz do portal do Azure.|

Substitua estes parâmetros pelos seus próprios valores no seguinte exemplo `docker run` comando.

### <a name="text-to-speech"></a>Conversão de texto em voz

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

### <a name="speech-to-text"></a>Conversão de voz em texto

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Este comando:

* Execute um contentor de voz a partir da imagem de contentor
* Aloca 2 núcleos de CPU e 2 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião.

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

|Contentor|Ponto Final|
|--|--|
|Conversão de voz em texto|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Conversão de texto em voz|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Conversão de voz em texto

O contêiner fornece APIs, que são acedidos através do ponto de extremidade do websocket com base na consulta a [SDK de voz](index.yml).

Por predefinição, o SDK de voz utiliza serviços de voz online. Para usar o contêiner, terá de alterar o método de inicialização. Veja os exemplos abaixo.

#### <a name="for-c"></a>ParaC#

Deixa de utilizar esta chamada de inicialização da cloud do Azure:

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

para esta chamada usando o ponto final do contentor:

```C#
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Para Python

Deixa de utilizar esta chamada de inicialização da cloud do Azure

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

para esta chamada usando o ponto final do contentor:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Conversão de texto em voz

O contêiner fornece o ponto final REST APIs que pode ser encontradas [aqui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) e pode ver exemplos [aqui](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Ao executar o contentor, o contentor utiliza **stdout** e **stderr** às informações de saída que é útil para resolver os problemas que ocorrem ao iniciar ou executar o contentor.

## <a name="billing"></a>Faturação

O envio de contentores de voz cobrança informações para o Azure, utilizando um _voz_ recursos na sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e voz contentores em execução. Em resumo:

* Voz fornece dois contentores do Linux para o Docker, encapsulando a conversão de voz em texto e o texto em voz.
* Imagens de contentor são transferidas a partir do registo de contentor privado no Azure.
* Executam imagens de contentor no Docker.
* Pode utilizar a REST API ou o SDK para chamar operações em contentores de voz, especificando o URI do contentor do anfitrião.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
>  Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Revisão [configurar contentores](speech-container-configuration.md) para definições de configuração
* Utilizar mais [contentores de serviços cognitivos](../cognitive-services-container-support.md)
