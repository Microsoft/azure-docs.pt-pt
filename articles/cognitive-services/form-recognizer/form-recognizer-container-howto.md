---
title: Como instalar e executar o contentor para o reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar o contentor do Reconhecedor de Formato para analisar dados de formulário e tabela.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: a251e97d671c4aad0aebb1d6c3349cdc09444308
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718480"
---
# <a name="install-and-run-form-recognizer-containers"></a>Instalar e executar contentores do reconhecedor de formulário

Reconhecedor de formulário do Azure aplica-se a tecnologia de aprendizagem automática para identificar e extrair pares chave-valor e tabelas de formulários. Ele associa as entradas da tabela e valores com os pares chave-valor e, em seguida, produz dados estruturados, que inclui as relações no arquivo original. 

Para reduzir a complexidade e integrar facilmente um modelo de formulário reconhecedor personalizado ao seu processo de automatização de fluxo de trabalho ou de outro aplicativo, pode chamar o modelo com uma API REST simples. Apenas cinco de formulário de documentos (ou um formulário vazio e dois formulários preenchidos) são necessários, pelo que pode obter resultados rapidamente, com precisão e adaptados ao seu conteúdo específico. Sem muita intervenção manual ou a experiência de ciência de dados extensa é necessária. E ele não requer a etiquetagem de dados ou de anotação de dados.

|Função|Funcionalidades|
|-|-|
|Reconhecedor de Formato| <li>Processa os ficheiros PDF, PNG e JPG<li>Prepara modelos personalizados com um mínimo de 5 formas do mesmo layout <li>Extrai os pares chave-valor e informações da tabela <li>Utiliza a funcionalidade do Azure Cognitive Services computador visão API reconhecer texto para detetar e extrair texto impresso de imagens dentro de formulários<li>Não necessita de anotação ou etiquetagem|

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de utilizar contentores do reconhecedor de formulário, tem de cumprir os seguintes pré-requisitos:

|Necessário|Objetivo|
|--|--|
|Motor do docker| É necessário o motor do Docker instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> No Windows, Docker também tem de ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | Deve ter uma noção básica dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor e dados de conhecimento do basic `docker` comandos.|
|A CLI do Azure| Instalar o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) no seu anfitrião.|
|Recurso de API de imagem digitalizada do computador| Para processar documentos digitalizados e imagens, precisa de um recurso de imagem digitalizada. Pode acessar a funcionalidade de reconhecer texto como a um recurso do Azure (da REST API ou SDK) ou uma *cognitive services-reconhecer-texto* [contentor](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). As tarifas habituais de faturas aplicam-se. <br><br>Introduzir a chave e os pontos finais de faturas para o recurso de imagem digitalizada (em nuvem do Azure ou o contentor de serviços cognitivos). Utilize esta chave e o ponto final de faturação como {COMPUTER_VISION_API_KEY} e {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Se utilizar o *cognitive services-reconhecer-texto* contentor, certifique-se de que:<br><br>A chave de imagem digitalizada do contentor do reconhecedor de formulário é a chave especificada na imagem digitalizada `docker run` comando para o *cognitive services-reconhecer-texto* contentor.<br>O ponto final de faturação é o ponto de extremidade do contentor (por exemplo, `https://localhost:5000`). Se utilizar o contentor de imagem digitalizada e o contentor do reconhecedor de formulário em conjunto no mesmo anfitrião, eles não podem ambos ser iniciados com a porta predefinida *5000*.  |  
|Recursos do reconhecedor de formulário |Para utilizar estes contentores, tem de ter:<br><br>R _reconhecedor de formulário_ recursos do Azure para obter a chave de faturação associada e a faturação URI do ponto final. Ambos os valores estão disponíveis no portal do Azure **descrição geral do formulário reconhecedor** e **chaves de descrição geral do formulário reconhecedor** páginas e os dois valores são necessários para iniciar o contentor.<br><br>**{BILLING_KEY}** : chave de recurso<br><br>**{BILLING_ENDPOINT_URI}** : exemplo URI do ponto final é `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Pedir acesso ao registo de contentor

Primeiro tem de concluir e submeter o [formulário de pedido de acesso a contentores do reconhecedor de formulário de serviços cognitivos](https://aka.ms/FormRecognizerRequestAccess) para pedir acesso ao contentor. Se o fizer, também assina tendo em vista o de imagem digitalizada. Não precisa para se inscrever para o formulário de pedido de imagem digitalizada separadamente. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

O mínimos e recomendados núcleos de CPU e memória para atribuir cada contentor do reconhecedor de formulário são descritas na tabela a seguir:

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 núcleos, 4 GB de memória | 4 núcleos, 8 GB de memória |

* Cada principal tem de ser, pelo menos, de 2,6 GHz (gigahertz) ou mais rápido.
* TPS - transações por segundo
* Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.

> [!Note]
> Os valores mínimos e recomendados baseiam-se sobre os limites do Docker e *não* recursos da máquina host.

## <a name="get-the-container-image-with-the-docker-pull-command"></a>Obter a imagem de contentor com o comando de pull do docker

Imagens de contentor para o formulário reconhecedor estão disponíveis no repositório do seguinte:

| Contentor | Repositório |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Se pretende usar o `cognitive-services-recognize-text` [contentor](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull), em vez do serviço do reconhecedor de formulário, certifique-se de que utiliza o `docker pull` comando com o nome do contentor correto: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Solicitação de docker para o contentor do reconhecedor de formulário

#### <a name="form-recognizer"></a>Reconhecedor de Formato

Para obter o contentor do reconhecedor de formulário, utilize o seguinte comando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Como utilizar o contentor

Depois do contentor está no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

1. [Execute o contentor](#run-the-container-by-using-the-docker-run-command), com as definições de faturas necessárias mas não utilizadas. Obter mais [exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do `docker run` comandos estão disponíveis.
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Execute o contentor com o docker, execute o comando

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar qualquer um dos três contentores. O comando utiliza os seguintes parâmetros:

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor. Está disponível no portal do Azure **chaves do reconhecedor de formulário** página.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível no portal do Azure **descrição geral do reconhecedor de formulário** página.|
|{COMPUTER_VISION_API_KEY}| A chave está disponível no portal do Azure **chaves de API de visão do computador** página.|
|{COMPUTER_VISION_ENDPOINT_URI}|O ponto final de faturação. Se estiver a utilizar um recurso de imagem digitalizada com base na cloud, o valor do URI está disponível no portal do Azure **descrição geral da API de visão de computador** página. Se estiver a utilizar um `cognitive-services-recognize-text` contentor, utilize o URL de ponto final de faturação que é passado para o contentor no `docker run` comando.|

Substitua estes parâmetros pelos seus próprios valores no seguinte exemplo `docker run` comando.

### <a name="form-recognizer"></a>Reconhecedor de Formato

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Este comando:

* Execute um contentor de reconhecedor de formulário a partir da imagem de contentor.
* Aloca 2 núcleos de CPU e 8 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor.
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião.
* Monta uma /input e um volume /output para o contentor.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Executar contentores separados como separado docker, execute comandos

Para a combinação do reconhecedor de formulário e do reconhecedor de texto que está alojada localmente no mesmo anfitrião, utilize os seguintes comandos de Docker CLI de dois exemplo:

Execute o primeiro contentor na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Execute o contentor de segundo na porta 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Cada contentor subseqüente deve ser uma porta diferente. 

### <a name="run-separate-containers-with-docker-compose"></a>Execute contentores separados com o Docker Compose

Para a combinação do reconhecedor de formulário e do reconhecedor de texto que está alojada localmente no mesmo anfitrião, consulte o ficheiro YAML de composição do Docker de exemplo seguinte. O reconhecedor de texto `{COMPUTER_VISION_API_KEY}` tem de ser o mesmo para ambos os `formrecognizer` e `ocr` contentores. O `{COMPUTER_VISION_ENDPOINT_URI}` é utilizado apenas na `ocr` contentor, uma vez que o `formrecognizer` contentor utiliza o `ocr` nome e a porta. 

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
      apikey: {COMPUTER_VISION_API_KEY}  

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
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
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
> O `Eula`, `Billing`, e `ApiKey`, bem como a `FormRecognizer:ComputerVisionApiKey` e `FormRecognizer:ComputerVisionEndpointUri` opções, tem de ser especificado para executar o contentor; caso contrário, não inicia o contentor. Para obter mais informações, consulte [faturação](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

|Contentor|Ponto Final|
|--|--|
|formulário reconhecedor|http://localhost:5000


### <a name="form-recognizer"></a>Reconhecedor de Formato

O contêiner fornece APIs, que pode aceder através do ponto de extremidade do websocket com base em consulta [documentação do SDK dos serviços de formulário reconhecedor](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Por predefinição, o SDK do reconhecedor de formulário utiliza os serviços online. Para usar o contêiner, terá de alterar o método de inicialização. Veja os exemplos abaixo.

#### <a name="for-c"></a>ParaC#

Deixa de utilizar esta chamada de inicialização da cloud do Azure:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

para esta chamada, que utiliza o ponto final do contentor:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Para Python

Deixa de utilizar esta chamada de inicialização da cloud do Azure:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

para esta chamada, que utiliza o ponto final do contentor:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Reconhecedor de Formato

O contêiner fornece o ponto final REST APIs, que pode ser encontrado no [API do reconhecedor de formulário](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) página.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Ao executar o contentor, o contentor utiliza **stdout** e **stderr** às informações de saída que é útil para resolução de problemas que surgem quando inicia ou executar o contentor.

## <a name="billing"></a>Faturação

Os contentores do reconhecedor de formulário enviar informações de faturação para o Azure com um _reconhecedor de formulário_ recursos na sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e executar contentores do reconhecedor de formulário. Em resumo:

* Formulário reconhecedor fornece um contentor do Linux para o Docker.
* Imagens de contentor são transferidas a partir do registo de contentor privado no Azure.
* Executam imagens de contentor no Docker.
* Pode utilizar a API REST ou o SDK de REST para chamar operações no contentor do reconhecedor de formulário, especificando o URI do contentor do anfitrião.
* Tem de especificar as informações de faturas quando criar uma instância de um contentor.

> [!IMPORTANT]
>  Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Revisão [configurar contentores](form-recognizer-container-configuration.md) para definições de configuração.
* Utilizar mais [contentores de serviços cognitivos](../cognitive-services-container-support.md).
