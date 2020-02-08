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
ms.date: 12/04/2019
ms.author: dapine
ms.openlocfilehash: ca7e7f7460db82a357ed8aa240467a6894254217
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086988"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Instalar e executar contêineres do serviço de fala (versão prévia)

Os contêineres permitem executar algumas das APIs do serviço de fala em seu próprio ambiente. Os contêineres são ótimos para requisitos específicos de segurança e governança de dados. Neste artigo, você aprenderá a baixar, instalar e executar um contêiner de fala.

Os contêineres de fala permitem que os clientes criem uma arquitetura de aplicativo de fala otimizada para recursos de nuvem robustos e localidade de borda. Há quatro contêineres diferentes disponíveis. Os dois recipientes padrão são **discurso-a-texto** e **texto-a-fala**. Os dois recipientes personalizados são **discurso-a-texto personalizado** e **texto personalizado-a-fala**.

> [!IMPORTANT]
> Todos os contentores da fala são atualmente oferecidos como parte de uma [pré-visualização pública "Gated".](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio) Um comunicado será feito quando os contêineres de fala progredirem para disponibilidade geral (GA).

| Função | Funcionalidades | Últimas |
|--|--|--|
| Conversão de voz em texto | Transcreve as gravações contínuas de fala em tempo real ou de áudio em lotes em texto com resultados intermediários. | 2.0.0 |
| Fala Personalizada para texto | Utilizando um modelo personalizado do [portal Custom Speech,](https://speech.microsoft.com/customspeech)transcreve gravações contínuas de fala em tempo real ou gravações áudio em lote em texto com resultados intermédios. | 2.0.0 |
| Conversão de texto em voz | Converte texto em fala natural-som com entrada de texto sem formatação ou SSML (linguagem de marcação de síntese de fala). | 1.3.0 |
| Conversão de texto em fala personalizada | Utilizando um modelo personalizado do [portal Voz Personalizada,](https://aka.ms/custom-voice-portal)converte texto em discurso de som natural com entrada de texto simples ou linguagem de marcação de síntese da fala (SSML). | 1.3.0 |

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar os contêineres de fala:

| Necessário | Objetivo |
|--|--|
| Motor Docker | Precisa do Motor Docker instalado num [computador de acolhimento.](#the-host-computer) O Docker fornece pacotes que configuram o ambiente Docker no [macOS,](https://docs.docker.com/docker-for-mac/) [Windows](https://docs.docker.com/docker-for-windows/)e [Linux.](https://docs.docker.com/engine/installation/#supported-platforms) Para um primer sobre o Docker e o básico do contentor, consulte a visão geral do [Docker.](https://docs.docker.com/engine/docker-overview/)<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar os recipientes Linux.<br><br> |
| Familiaridade com Docker | Você deve ter uma compreensão básica dos conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de comandos básicos de `docker`. |
| Recurso de fala | Para usar esses contêineres, você deve ter:<br><br>Um recurso Azure _Speech_ para obter a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas de Visão Geral do **Discurso** do portal Azure e de Keys. Eles são necessários para iniciar o contêiner.<br><br>**{API_KEY}** : Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}** : O ponto final fornecido na página **'Visão Geral'** |

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registo de contentores

Preencha e submeta o formulário de pedido de pedidos de pedidos de portadores de [serviços cognitivos](https://aka.ms/speechcontainerspreview/) para solicitar o acesso ao recipiente. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte à extensão de vetor avançado

O **hospedeiro** é o computador que gere o contentor de estivadores. O hospedeiro *deve suportar* [extensões vetoriais avançadas](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Você pode verificar o suporte a AVX2 em hosts Linux com o seguinte comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> O computador hospedeiro é *necessário* para suportar o AVX2. O recipiente *não funcionará* corretamente sem suporte AVX2.

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada contêiner de fala.

# <a name="speech-to-texttabstt"></a>[Conversão de voz em texto](#tab/stt)

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Conversão de voz em texto | 2 núcleos, 2 GB de memória | 4 núcleos, 4 GB de memória |

# <a name="custom-speech-to-texttabcstt"></a>[Discurso personalizado a texto](#tab/cstt)

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Fala Personalizada para texto | 2 núcleos, 2 GB de memória | 4 núcleos, 4 GB de memória |

# <a name="text-to-speechtabtts"></a>[Conversão de texto em voz](#tab/tts)

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Conversão de texto em voz | 1 núcleo, 2 GB de memória | 2 núcleos, 3 GB de memória |

# <a name="custom-text-to-speechtabctts"></a>[Texto-a-fala personalizado](#tab/ctts)

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Conversão de texto em fala personalizada | 1 núcleo, 2 GB de memória | 2 núcleos, 3 GB de memória |

***

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a memória correspondem às definições `--cpus` e `--memory`, que são utilizadas como parte do comando `docker run`.

> [!NOTE]
> O mínimo e recomendado baseiam-se nos limites do Docker, *não* nos recursos da máquina hospedeira. Por exemplo, os recipientes de fala a texto mapeiam partes de um grande modelo de linguagem, e recomenda-se que todo o ficheiro se encaixe na memória, que é um adicional de 4-6 GB. Além disso, a primeira execução de qualquer um dos contêineres pode levar mais tempo, pois os modelos estão sendo paginados na memória.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

As imagens de contêiner para fala estão disponíveis no registro de contêiner a seguir.

# <a name="speech-to-texttabstt"></a>[Conversão de voz em texto](#tab/stt)

| Contentor | Repositório |
|-----------|------------|
| Conversão de voz em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Discurso personalizado a texto](#tab/cstt)

| Contentor | Repositório |
|-----------|------------|
| Fala Personalizada para texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Conversão de texto em voz](#tab/tts)

| Contentor | Repositório |
|-----------|------------|
| Conversão de texto em voz | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Texto-a-fala personalizado](#tab/ctts)

| Contentor | Repositório |
|-----------|------------|
| Conversão de texto em fala personalizada | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Pull do Docker para os contêineres de fala

# <a name="speech-to-texttabstt"></a>[Conversão de voz em texto](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Pull do Docker para o contêiner de conversão de fala em texto

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo de pré-visualização do contentor.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> A etiqueta `latest` puxa o local `en-US`. Para locais adicionais consulte [locais do Discurso ao Texto](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Localidades de conversão de fala em texto

Todas as etiquetas, com exceção `latest` estão no seguinte formato e são sensíveis aos casos:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

A marca a seguir é um exemplo do formato:

```
2.0.0-amd64-en-us-preview
```

Para todos os locais apoiados do recipiente **de discurso a texto,** consulte [as etiquetas](../containers/container-image-tags.md#speech-to-text)de imagem do Discurso ao Texto .

# <a name="custom-speech-to-texttabcstt"></a>[Discurso personalizado a texto](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Pull do Docker para o contêiner de Fala Personalizada para texto

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo de pré-visualização do contentor.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> A `locale` e `voice` para recipientes de discurso personalizados é determinada pelo modelo personalizado ingerido pelo recipiente.

# <a name="text-to-speechtabtts"></a>[Conversão de texto em voz](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Pull do Docker para o contêiner de conversão de texto em fala

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo de pré-visualização do contentor.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> A etiqueta `latest` puxa o `en-US` local e `jessarus` voz. Para locais adicionais consulte [locais de texto a fala](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Localidades de conversão de texto em fala

Todas as etiquetas, com exceção `latest` estão no seguinte formato e são sensíveis aos casos:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

A marca a seguir é um exemplo do formato:

```
1.3.0-amd64-en-us-jessarus-preview
```

Para todos os locais apoiados e vozes correspondentes do recipiente **texto-a-fala,** consulte as etiquetas de [imagem texto-a-fala](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Ao construir um *código de texto-a-fala* HTTP POST, a mensagem de marcação da síntese da [fala (SSML)](speech-synthesis-markup.md) requer um elemento `voice` com um atributo `name`. O valor é o local e a voz correspondentes do contentor, também conhecido como ["nome curto".](language-support.md#standard-voices) Por exemplo, a etiqueta `latest` teria um nome de voz de `en-US-JessaRUS`.

# <a name="custom-text-to-speechtabctts"></a>[Texto-a-fala personalizado](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Pull do Docker para o contêiner personalizado de conversão de texto em fala

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo de pré-visualização do contentor.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> A `locale` e `voice` para recipientes de discurso personalizados é determinada pelo modelo personalizado ingerido pelo recipiente.

***

## <a name="how-to-use-the-container"></a>Como utilizar o recipiente

Uma vez que o recipiente esteja no [computador de acolhimento,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de faturação necessárias. Mais [exemplos](speech-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis.
1. [Consulta do ponto final da previsão do recipiente](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com `docker run`

Use o comando de execução de [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o recipiente. Consulte a recolha de [parâmetros necessários](#gathering-required-parameters) para obter os valores `{Endpoint_URI}` e `{API_Key}`. [Exemplos](speech-container-configuration.md#example-docker-run-commands) adicionais do comando `docker run` também estão disponíveis.

# <a name="speech-to-texttabstt"></a>[Conversão de voz em texto](#tab/stt)

Para executar o recipiente *Discurso-a-texto,* execute o seguinte comando `docker run`.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente *de discurso a texto* a partir da imagem do recipiente.
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro.

# <a name="custom-speech-to-texttabcstt"></a>[Discurso personalizado a texto](#tab/cstt)

O recipiente *discurso-texto personalizado baseia-se* num modelo de fala personalizado. O modelo personalizado tem de ter sido [treinado](how-to-custom-speech-train-model.md) usando o portal de [discurso personalizado.](https://speech.microsoft.com/customspeech) O **ID** modelo de fala personalizado é necessário para executar o recipiente. Pode ser encontrado na página de **Formação** do portal de discurso personalizado. A partir do portal de discurso personalizado, navegue até à página **de Formação** e selecione o modelo.
<br>

![Página de treinamento de fala personalizada](media/custom-speech/custom-speech-model-training.png)

Obtenha o **ID** modelo para usar como argumento para o parâmetro `ModelId` do comando `docker run`.
<br>

![Detalhes do modelo de fala personalizado](media/custom-speech/custom-speech-model-details.png)

O quadro seguinte representa os vários parâmetros `docker run` e respetivas descrições:

| Parâmetro | Descrição |
|---------|---------|
| `{VOLUME_MOUNT}` | A montagem de [volume](https://docs.docker.com/storage/volumes/)de computador hospedeiro, que o estivador usa para persistir o modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada na máquina hospedeira. |
| `{MODEL_ID}` | O **ID** modelo de discurso personalizado da página de **formação** do portal de discurso personalizado. |
| `{ENDPOINT_URI}` | O ponto de extremidade é necessário para medição e cobrança. Para mais informações, consulte a [recolha de parâmetros necessários](#gathering-required-parameters). |
| `{API_KEY}` | A chave de API é necessária. Para mais informações, consulte a [recolha de parâmetros necessários](#gathering-required-parameters). |

Para executar o recipiente *discurso-texto personalizado,* execute o seguinte comando `docker run`:

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

* Executa um recipiente *de discurso a texto personalizado* a partir da imagem do recipiente.
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Carrega o modelo *discurso-texto personalizado* a partir do suporte de entrada de volume, por exemplo *C:\CustomSpeech*.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Descarrega o modelo dado o `ModelId` (se não for encontrado na montagem de volume).
* Se o modelo personalizado foi previamente descarregado, o `ModelId` é ignorado.
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro.

# <a name="text-to-speechtabtts"></a>[Conversão de texto em voz](#tab/tts)

Para executar o recipiente *Texto-a-fala,* execute o seguinte comando `docker run`.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente *de texto a palavra* a partir da imagem do recipiente.
* Aloca 2 núcleos de CPU e um gigabyte (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro.

# <a name="custom-text-to-speechtabctts"></a>[Texto-a-fala personalizado](#tab/ctts)

O recipiente *de texto-a-fala personalizado baseia-se* num modelo de voz personalizado. O modelo personalizado tem de ter sido [treinado](how-to-custom-voice-create-voice.md) utilizando o [portal de voz personalizado.](https://aka.ms/custom-voice-portal) O **ID** modelo de voz personalizado é necessário para executar o recipiente. Pode ser encontrado na página de **Treino** do portal de voz personalizado. A partir do portal de voz personalizado, navegue até à página **de Formação** e selecione o modelo.
<br>

![Página de treinamento de voz personalizada](media/custom-voice/custom-voice-model-training.png)

Obtenha o **ID** modelo para usar como argumento para o parâmetro `ModelId` do comando de execução do estivador.
<br>

![Detalhes do modelo de voz personalizado](media/custom-voice/custom-voice-model-details.png)

O quadro seguinte representa os vários parâmetros `docker run` e respetivas descrições:

| Parâmetro | Descrição |
|---------|---------|
| `{VOLUME_MOUNT}` | A montagem de [volume](https://docs.docker.com/storage/volumes/)de computador hospedeiro, que o estivador usa para persistir o modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada na máquina hospedeira. |
| `{MODEL_ID}` | O ID do **modelo** de discurso personalizado da página de **formação** do portal de voz personalizado. |
| `{ENDPOINT_URI}` | O ponto de extremidade é necessário para medição e cobrança. Para mais informações, consulte a [recolha de parâmetros necessários](#gathering-required-parameters). |
| `{API_KEY}` | A chave de API é necessária. Para mais informações, consulte a [recolha de parâmetros necessários](#gathering-required-parameters). |

Para executar o recipiente *de texto-a-fala personalizado,* execute o seguinte comando `docker run`:

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

* Executa um recipiente *de texto-a-fala personalizado* a partir da imagem do recipiente.
* Aloca 2 núcleos de CPU e um gigabyte (GB) de memória.
* Carrega o modelo *de texto-a-fala personalizado* a partir do suporte de entrada de volume, por exemplo *C:\CustomVoice*.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Descarrega o modelo dado o `ModelId` (se não for encontrado na montagem de volume).
* Se o modelo personalizado foi previamente descarregado, o `ModelId` é ignorado.
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro.

***

> [!IMPORTANT]
> As opções de `Eula`, `Billing`e `ApiKey` devem ser especificadas para funcionar o contentor; caso contrário, o contentor não vai começar.  Para mais informações, consulte [billing.](#billing)

## <a name="query-the-containers-prediction-endpoint"></a>Consulta do ponto final da previsão do recipiente

| Contentores | URL do host do SDK | Protocolo |
|--|--|--|
| Conversão de fala em texto e Fala Personalizada em texto | `ws://localhost:5000` | WS |
| Conversão de texto em fala e Text-to-Speech personalizado | `http://localhost:5000` | HTTP |

Para obter mais informações sobre a utilização dos protocolos WSS e HTTPS, consulte a [segurança dos contentores.](../cognitive-services-container-support.md#azure-cognitive-services-container-security)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Conversão de texto em fala ou personalizada para fala

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, certifique-se de executar cada contêiner com uma porta exposta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

Você pode ter esse contêiner e um contêiner diferente de serviços cognitivas do Azure em execução no HOST juntos. Você também pode ter vários contêineres do mesmo contêiner de serviços cognitivas em execução.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Pare o recipiente

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Ao iniciar ou executar o contêiner, você poderá ter problemas. Utilize um [suporte](speech-container-configuration.md#mount-settings) de saída e ative a exploração madeireira. Isso permitirá que o contêiner gere arquivos de log que são úteis ao solucionar problemas.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os contentores da Fala enviam informações de faturação para o Azure, utilizando um recurso *da Fala* na sua conta Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para mais informações sobre estas opções, consulte [os recipientes Configur.](speech-container-configuration.md)

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar contêineres de fala. Em resumo:

* A fala fornece quatro contêineres do Linux para o Docker, encapsulando vários recursos:
  * *Conversão de voz em texto*
  * *Discurso personalizado a texto*
  * *Conversão de texto em voz*
  * *Texto-a-fala personalizado*
* As imagens de contêiner são baixadas do registro de contêiner no Azure.
* Executam imagens de contentor no Docker.
* Quer utilize a API REST (apenas texto a palavra) ou o SDK (Discurso a texto ou texto-a-fala) especifique o uri hospedeiro do recipiente. 
* Você precisa fornecer informações de cobrança ao criar uma instância de um contêiner.

> [!IMPORTANT]
>  Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Rever [os recipientes](speech-container-configuration.md) de configuração para configurações de configuração
* Saiba como usar recipientes de [serviço da Fala com Kubernetes e Helm](speech-container-howto-on-premises.md)
* Utilize mais [recipientes de Serviços Cognitivos](../cognitive-services-container-support.md)
