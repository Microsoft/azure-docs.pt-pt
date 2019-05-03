---
title: Instalar e executar a opção contentor - reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar o contentor do reconhecedor de formulário para analisar dados de formulário e de tabela.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 5d4374b329049e2e55966a28567c5232be77abda
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027069"
---
# <a name="install-and-run-form-recognizer-containers"></a>Instalar e executar contentores do reconhecedor de formulário
Formulário reconhecedor aplica-se a tecnologia de aprendizagem automática para identificar e extrair pares chave-valor e tabelas de formulários. Ele associa valores e as entradas da tabela aos mesmos e, em seguida, produz dados estruturados, que inclui as relações no arquivo original. Pode chamar seu modelo de formulário reconhecedor personalizado com uma API REST simples para reduzir a complexidade e integrá-los facilmente seu processo de automatização de fluxo de trabalho ou outra aplicação. Apenas cinco documentos (ou um formulário vazio) é necessários, pelo que pode obter resultados rapidamente, com precisão e adaptado aos seus conteúdos específicos, sem muita intervenção manual ou conhecimentos de ciência de dados extensa. Não requer a etiquetagem de dados ou de anotação de dados.

|Função|Funcionalidades|
|-|-|
|Reconhecedor de Formato| <li>Ficheiros de processos do tipo de PDF, PNG e JPG.<li>Prepara modelos personalizados com formulários mínimo de 5 do layout do mesmo. <li>Extrai os pares chave-valor e informações da tabela. <li>Utiliza cognitivos RecognizeText de API de imagem digitalizada do serviço de computador para detetar e extrair texto impresso de imagens dentro de formulários.<li>Não necessita de anotação ou etiquetagem.|

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores do reconhecedor de formulário:

|Necessário|Objetivo|
|--|--|
|Motor do docker| É necessário o motor do Docker instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, Docker também tem de ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | Deve ter uma noção básica dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor, bem como dados de conhecimento do basic `docker` comandos.|
|CLI do Azure| Tem de instalar o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) no seu anfitrião.|
|Recurso de API de imagem digitalizada do computador| Para poder processar documentos digitalizados e imagens, uma **recurso de imagem digitalizada** é necessária. Pode aceder a **reconhecer texto** funcionalidade como a um recurso do Azure (REST API ou SDK) ou como um `cognitive-services-recognize-text` contentor. As tarifas habituais de faturas aplicam-se. <br><br>Deve passar a chave e o ponto final de faturação para o seu recurso de imagem digitalizada específico (contentor de serviços cognitivos ou de cloud do Azure). Utilize esta chave e o ponto final de faturação como {COMPUTER_VISION_API_KEY} e {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Se utilizar o  **`cognitive-services-recognize-text` contentor**, certifique-se:<br><br>* A chave de imagem digitalizada do contentor do reconhecedor de formulário é a chave especificada na imagem digitalizada `docker run` comando para o `cognitive-services-recognize-text` contentor.<br>* O ponto final de faturação é o ponto de extremidade do contentor, tal como `https://localhost:5000`. Se utilizar contentores a visão do computador e o reconhecedor de formulário em conjunto no mesmo anfitrião, eles não podem ambos ser iniciados com a porta predefinida `5000`.  |  
|Recursos do reconhecedor de formulário |Para utilizar estes contentores, tem de ter:<br><br>R _reconhecedor de formulário_ recursos do Azure para obter a chave de faturação associada e a faturação URI do ponto final. Ambos os valores estão disponíveis no portal do Azure **reconhecedor de formulário** páginas de descrição geral e as chaves e são necessários para iniciar o contentor.<br><br>**{BILLING_KEY}** : chave de recurso<br><br>**{BILLING_ENDPOINT_URI}** : exemplo URI do ponto final é: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Pedir acesso ao registo de contentor

Primeiro tem de concluir e submeter o [formulário de cognitivos pedido de contentores do reconhecedor de formulário de serviços](https://aka.ms/FormRecognizerRequestAccess) para pedir acesso ao contentor. Isto irá também inscrever para imagem digitalizada. Não é necessário para se inscrever para o formulário de pedido de imagem digitalizada separadamente. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os mínimos e recomendados núcleos de CPU e memória para atribuir cada contentor do reconhecedor de formulário.

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 núcleos, 4 GB de memória | 4 núcleos, 8 GB de memória |

* Cada principal tem de ser, pelo menos, de 2,6 GHz (gigahertz) ou mais rápido.
* TPS - transações por segundo

Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.

> [!Note]
> Os valores mínimos e recomendados baseiam-se fora dos limites do Docker e *não* recursos da máquina host.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contentor com o `docker pull`

Imagens de contentor para o formulário reconhecedor estão disponíveis.

| Contentor | Repositório |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Solicitação de docker para o contentor do reconhecedor de formulário

#### <a name="form-recognizer"></a>Reconhecedor de Formato

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Como utilizar o contentor

Assim que o contentor estiver no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

1. [Execute o contentor](#run-the-container-with-docker-run), com as definições de faturas necessárias mas não utilizadas. Obter mais [exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do `docker run` comandos estão disponíveis.
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Execute o contentor com `docker run`

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar qualquer um dos três contentores. O comando utiliza os seguintes parâmetros:

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor e está disponível na página de chaves do reconhecedor de formulário do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível na página de descrição geral do reconhecedor de formulário do portal do Azure.|
|{COMPUTER_VISION_API_KEY}| A chave está disponível na página de chaves de API de visão do computador do portal do Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|O ponto final de faturação. Se estiver a utilizar um recurso de imagem digitalizada com base na cloud, o valor do URI está disponível na página de descrição geral da API de visão de computador do portal do Azure. Se estiver a utilizar um `cognitive-services-recognize-text` contentor, utilize o URL de ponto final de faturação é passado para o contentor no `docker run` comando.|

Substitua estes parâmetros pelos seus próprios valores no seguinte exemplo `docker run` comando.

### <a name="form-recognizer"></a>Reconhecedor de Formato

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Este comando:

* Execute um contentor de reconhecedor de formulário a partir da imagem de contentor
* Aloca 2 núcleos de CPU e 8 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião.
* Monta uma /input e um volume /output para o contentor

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Executar contentores separados como separado docker, execute comandos

A combinação de formulário reconhecedor e texto reconhecedor alojada localmente no mesmo anfitrião, de seguir dois comandos da CLI do Docker de exemplo.

Execute o primeiro contentor na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
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

Para a combinação de formulário reconhecedor e texto reconhecedor alojada localmente no mesmo anfitrião, segue um exemplo de ficheiro YAML de composição do Docker. O reconhecedor de texto `{COMPUTER_VISION_API_KEY}` tem de ser o mesmo para ambos os `formrecognizer` e `ocr` contentores. O `{COMPUTER_VISION_ENDPOINT_URI}` só é utilizada no `ocr` contentor uma vez que o `formrecognizer` contentor utiliza o `ocr` nome e a porta. 

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
> O `Eula`, `Billing`, e `ApiKey` , bem como `FormRecognizer:ComputerVisionApiKey` e `FormRecognizer:ComputerVisionEndpointUri` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

|Contentor|Ponto Final|
|--|--|
|formulário reconhecedor|http://localhost:5000


### <a name="form-recognizer"></a>Reconhecedor de Formato

O contêiner fornece APIs, que são acedidas através de ponto final de consulta baseada em websocket [documentação do SDK dos serviços de formulário reconhecedor](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Por predefinição, o SDK do reconhecedor de formulário utiliza os serviços online. Para usar o contêiner, terá de alterar o método de inicialização. Veja os exemplos abaixo.

#### <a name="for-c"></a>ParaC#

Deixa de utilizar esta chamada de inicialização da cloud do Azure:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

para esta chamada usando o ponto final do contentor:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Para Python

Deixa de utilizar esta chamada de inicialização da cloud do Azure

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

para esta chamada usando o ponto final do contentor:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Reconhecedor de Formato

O contêiner fornece o ponto final REST APIs, que pode ser encontradas [aqui](https://docs.microsoft.com/azure/cognitive-services/formrecognizer-service/rest-apis#formrecognier-api) e pode ver exemplos [aqui](https://azure.microsoft.com/resources/samples/cognitive-formrecognizer).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Ao executar o contentor, o contentor utiliza **stdout** e **stderr** às informações de saída que é útil para resolver os problemas que ocorrem ao iniciar ou executar o contentor.

## <a name="billing"></a>Faturação

O envio de contentores do reconhecedor de formulário cobrança informações para o Azure, utilizando um _reconhecedor de formulário_ recursos na sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](form-recognizer-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e executar contentores do reconhecedor de formulário. Em resumo:

* Formulário reconhecedor fornece um contentor do Linux para o Docker.
* Imagens de contentor são transferidas a partir do registo de contentor privado no Azure.
* Executam imagens de contentor no Docker.
* Pode utilizar a REST API ou o SDK para chamar operações no contentor do reconhecedor de formulário, especificando o URI do contentor do anfitrião.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
>  Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Revisão [configurar contentores](form-recognizer-container-configuration.md) para definições de configuração
* Utilizar mais [contentores de serviços cognitivos](../cognitive-services-container-support.md)
