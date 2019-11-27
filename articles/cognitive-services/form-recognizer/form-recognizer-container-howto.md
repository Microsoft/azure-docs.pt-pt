---
title: Como instalar e executar o contêiner para o reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Este artigo explicará como usar o contêiner do reconhecedor do formulário de serviços cognitivas do Azure para analisar dados de formulário e tabela.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 21582a5a17a3c6f67182173bfe08d80c48765f7d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325846"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Instalar e executar contêineres do reconhecedor de formulário (versão prévia)

O reconhecedor de formulários do Azure aplica a tecnologia de aprendizado de máquina para identificar e extrair pares de chave-valor e tabelas de formulários. Ele associa valores e entradas de tabela com os pares chave-valor e, em seguida, gera dados estruturados que incluem as relações no arquivo original. 

Para reduzir a complexidade e integrar facilmente um modelo de reconhecedor de formulário personalizado ao processo de automação do fluxo de trabalho ou a outro aplicativo, você pode chamar o modelo usando uma API REST simples. Somente cinco documentos de formulário (ou um formulário vazio e dois formulários preenchidos) são necessários, para que você possa obter resultados de forma rápida, precisa e adaptada ao seu conteúdo específico. Nenhuma intervenção manual intensa ou ampla experiência em ciência de dados é necessária. E não requer a rotulação de dados nem a anotação de dados.

|Função|Funcionalidades|
|-|-|
|Reconhecedor de Formato| <li>Processa arquivos PDF, PNG e JPG<li>Treina modelos personalizados com um mínimo de cinco formas do mesmo layout <li>Extrai pares de chave-valor e informações de tabela <li>Usa o recurso de Reconhecimento de Texto API da Pesquisa Visual Computacional de serviços cognitivas do Azure para detectar e extrair texto impresso de imagens dentro de formulários<li>Não requer anotação ou rotulagem|

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar contêineres do reconhecedor de formulário, você deve atender aos seguintes pré-requisitos:

|Necessário|Objetivo|
|--|--|
|Mecanismo do Docker| Você precisa do mecanismo do Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [MacOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/)e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter uma introdução sobre o Docker e noções básicas de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> No Windows, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | Você deve ter uma compreensão básica dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner e conhecimento dos comandos básicos de `docker`.|
|A CLI do Azure| Instale o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) em seu host.|
|API da Pesquisa Visual Computacional recurso| Para processar documentos e imagens digitalizadas, você precisa de um recurso Pesquisa Visual Computacional. Você pode acessar o recurso de Reconhecimento de Texto como um recurso do Azure (a API REST ou o SDK) ou um [contêiner](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) *cognitiva-Services-Recognize-Text* . As tarifas de cobrança usuais se aplicam. <br><br>Transmita a chave de API e os pontos de extremidade para seu recurso de Pesquisa Visual Computacional (contêiner de nuvem do Azure ou serviços cognitivas). Use essa chave de API e o ponto de extremidade como **{COMPUTER_VISION_API_KEY}** e **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> Se você usar o contêiner *cognitiva-Services-Recognize-Text* , verifique se:<br><br>Sua chave de Pesquisa Visual Computacional para o contêiner do reconhecedor de formulário é a chave especificada no comando Pesquisa Visual Computacional `docker run` para o contêiner *cognitiva-Services-Recognize-Text* .<br>O ponto de extremidade de cobrança é o ponto de extremidade do contêiner (por exemplo, `http://localhost:5000`). Se você usar o contêiner de Pesquisa Visual Computacional e o contêiner do reconhecedor de formulário juntos no mesmo host, eles não poderão ser iniciados com a porta padrão de *5000*. |
|Recurso de reconhecimento de formulário |Para usar esses contêineres, você deve ter:<br><br>Um recurso do **reconhecedor** do Azure Form para obter a chave de API e o URI de ponto de extremidade associados. Ambos os valores estão disponíveis nas páginas visão geral e chaves do **reconhecedor** do portal do Azure Form e os dois valores são necessários para iniciar o contêiner.<br><br>**{FORM_RECOGNIZER_API_KEY}** : uma das duas chaves de recurso disponíveis na página chaves<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : o ponto de extremidade conforme fornecido na página Visão geral|

## <a name="gathering-required-parameters"></a>Coletando parâmetros necessários

Há três parâmetros principais para todos os contêineres de serviços cognitivas que são necessários. O contrato de licença de usuário final (EULA) deve estar presente com um valor de `accept`. Além disso, uma URL de ponto de extremidade e uma chave de API são necessárias.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>URI do ponto de extremidade `{COMPUTER_VISION_ENDPOINT_URI}` e `{FORM_RECOGNIZER_ENDPOINT_URI}`

O valor do URI do **ponto de extremidade** está disponível na página de *visão geral* portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *visão geral* , passe o mouse sobre o ponto de <span class="docon docon-edit-copy x-hidden-focus"></span> extremidade e um ícone de `Copy to clipboard` aparecerá. Copie e use onde for necessário.

![Coletar o URI do ponto de extremidade para uso posterior](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Chaves `{COMPUTER_VISION_API_KEY}` e `{FORM_RECOGNIZER_API_KEY}`

Essa chave é usada para iniciar o contêiner e está disponível na página chaves do portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *chaves* e clique no ícone de `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> .

![Obter uma das duas chaves para uso posterior](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API de serviço cognitiva. Não compartilhe suas chaves. Armazene-os com segurança, por exemplo, usando Azure Key Vault. Também recomendamos regenerar essas chaves regularmente. Apenas uma chave é necessária para fazer uma chamada à API. Ao regenerar a primeira chave, você pode usar a segunda chave para obter acesso contínuo ao serviço.

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registro de contêiner

Você deve primeiro concluir e enviar o formulário de [solicitação de acesso aos contêineres do reconhecedor de formulários de serviços de cognitiva](https://aka.ms/FormRecognizerRequestAccess) para solicitar acesso ao contêiner. Isso também se inscreve para Pesquisa Visual Computacional. Você não precisa se inscrever para o formulário de solicitação de Pesquisa Visual Computacional separadamente. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

Os núcleos de CPU mínimos e recomendados e a memória para alocar para cada contêiner do reconhecedor de formulário são descritos na tabela a seguir:

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Reconhecedor de Formato | 2 núcleos, 4 GB de memória | 4 núcleos, 8 GB de memória |
| Reconhecimento de Texto | 1 núcleo, 8 GB de memória | 2 núcleos, 8 GB de memória |

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* O núcleo e a memória correspondem às configurações de `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

> [!Note]
> Os valores mínimos e recomendados se baseiam nos limites do Docker e *não* nos recursos da máquina host.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Obter as imagens de contêiner com o comando docker pull

As imagens de contêiner para as ofertas de **reconhecedor de formulário** e **reconhecimento de texto** estão disponíveis no seguinte registro de contêiner:

| Contentor | Nome da imagem totalmente qualificado |
|-----------|------------|
| Reconhecedor de Formato | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Reconhecimento de Texto | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Você precisará de ambos os contêineres. Observe que o contêiner de **texto do reconhecedor** é [detalhado fora deste artigo.](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Pull do Docker para o contêiner do reconhecedor de formulário

#### <a name="form-recognizer"></a>Reconhecedor de Formato

Para obter o contêiner do reconhecedor de formulário, use o seguinte comando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Pull do Docker para o contêiner de Reconhecimento de Texto

#### <a name="recognize-text"></a>Reconhecimento de Texto

Para obter o contêiner de Reconhecimento de Texto, use o seguinte comando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-by-using-the-docker-run-command)com as configurações de cobrança necessárias. Mais [exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Executar o contêiner usando o comando Docker Run

Use o comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{COMPUTER_VISION_ENDPOINT_URI}`, `{COMPUTER_VISION_API_KEY}`, `{FORM_RECOGNIZER_ENDPOINT_URI}` e valores de `{FORM_RECOGNIZER_API_KEY}`.

[Exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis.

### <a name="form-recognizer"></a>Reconhecedor de Formato

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Este comando:

* Executa um contêiner do reconhecedor de formulário da imagem de contêiner.
* Aloca 2 núcleos de CPU e 8 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host.
* Monta um/Input e um volume/output no contêiner.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Executar contêineres separados como comandos de execução do Docker separados

Para o reconhecedor de formulário e a combinação de reconhecedor de texto hospedado localmente no mesmo host, use os dois comandos CLI de exemplo a seguir:

Execute o primeiro contêiner na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Execute o segundo contêiner na porta 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Cada contêiner subsequente deve estar em uma porta diferente. 

### <a name="run-separate-containers-with-docker-compose"></a>Executar contêineres separados com Docker Compose

Para o reconhecedor de formulário e a combinação de reconhecedor de texto hospedado localmente no mesmo host, consulte o exemplo a seguir Docker Compose arquivo YAML. O `{COMPUTER_VISION_API_KEY}` do reconhecedor de texto deve ser o mesmo para os contêineres `formrecognizer` e `ocr`. O `{COMPUTER_VISION_ENDPOINT_URI}` é usado somente no contêiner de `ocr`, porque o contêiner de `formrecognizer` usa a porta e o nome do `ocr`. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> As `Eula`, `Billing`e `ApiKey`, bem como as opções `FormRecognizer:ComputerVisionApiKey` e `FormRecognizer:ComputerVisionEndpointUri`, devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado. Para obter mais informações, consulte [cobrança](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

|Contentor|Ponto Final|
|--|--|
|formulário-reconhecedor|http://localhost:5000

### <a name="form-recognizer"></a>Reconhecedor de Formato

O contêiner fornece APIs de ponto de extremidade de consulta baseadas no WebSocket, que são acessadas por meio da [documentação do SDK dos serviços do Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Por padrão, o SDK do reconhecedor de formulário usa o serviços online. Para usar o contêiner, você precisa alterar o método de inicialização. Consulte os exemplos abaixo.

#### <a name="for-c"></a>FinsC#

Alterar do uso desta chamada de inicialização do Azure-cloud:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
para essa chamada, que usa o ponto de extremidade do contêiner:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Para Python

Alterar do uso desta chamada de inicialização do Azure-cloud:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

para essa chamada, que usa o ponto de extremidade do contêiner:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Reconhecedor de Formato

O contêiner fornece APIs de ponto de extremidade REST, que podem ser encontradas na página [API do reconhecedor de formulário](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) .


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se você executar o contêiner com uma [montagem](form-recognizer-container-configuration.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem ao iniciar ou executar o contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os contêineres do reconhecedor de formulário enviam informações de cobrança para o Azure usando um recurso de _reconhecimento de formulário_ em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [configurar contêineres](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar contêineres do reconhecedor de formulário. Em resumo:

* O reconhecedor de formulário fornece um contêiner do Linux para o Docker.
* As imagens de contêiner são baixadas do registro de contêiner privado no Azure.
* Executam imagens de contentor no Docker.
* Você pode usar a API REST ou o SDK REST para chamar operações no contêiner do reconhecedor de formulário especificando o URI do host do contêiner.
* Você deve especificar as informações de cobrança ao criar uma instância de um contêiner.

> [!IMPORTANT]
>  Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Os contêineres de serviços cognitivas não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Examine [configurar contêineres](form-recognizer-container-configuration.md) para definições de configuração.
* Use mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md).
