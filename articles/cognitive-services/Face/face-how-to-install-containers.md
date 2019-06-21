---
title: Instalar e executar contentores
titlesuffix: Face - Azure Cognitive Services
description: Transferir, instalar e executar contentores para rosto neste tutorial passo a passo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: beb9818be05fbb9a9e9c958dccb2e375f7685bd0
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272798"
---
# <a name="install-and-run-face-containers"></a>Instalar e executar contentores de rostos

Face de serviços cognitivos do Azure fornece um contentor de Linux padronizado para Docker que Deteta rostos humanos em imagens. Ele também identifica atributos, que incluem pontos de referência do rosto como noses e olhos, sexo, idade e outras funcionalidades faciais prevista de máquina. Além de deteção de rostos podem verificar se dois rostos na mesma imagem ou imagens diferentes são o mesmo com uma pontuação de confiança. Face também pode comparar rostos em relação a uma base de dados para ver se já existe um rostos de aspeto semelhante ou idêntico. Ele também pode organizar rostos semelhantes em grupos, utilizando as características de visual partilhadas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar os contentores de Face API.

|Necessário|Objetivo|
|--|--|
|Motor do docker| O motor do Docker tem de ser instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> No Windows, Docker também deve ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | É necessário um conhecimento básico dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor. Também tem de ter conhecimentos basic `docker` comandos.| 
|Azure `Cognitive Services` recursos |Para utilizar o contentor, tem de ter:<br><br>Um recurso de serviços cognitivos do Azure e a chave associada de faturação e o ponto final do URI de faturação. Ambos os valores estão disponíveis na **descrição geral** e **chaves** páginas para o recurso. -Lhes pedido que iniciar o contentor. Adicionar o `face/v1.0` encaminhamento para o ponto final do URI, conforme mostrado no exemplo a seguir BILLING_ENDPOINT_URI: <br><br>**{BILLING_KEY}** : chave de recurso<br><br>**{BILLING_ENDPOINT_URI}** : exemplo URI do ponto final é `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>Pedir acesso para o registo de contentor privado

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os mínimos e recomendados núcleos de CPU e memória para atribuir cada contentor da Face API.

| Contentor | Mínimo | Recomendado | Transações por segundo<br>(No mínimo, máximo)|
|-----------|---------|-------------|--|
|Rostos | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |10, 20|

* Cada principal tem de ser, pelo menos, de 2,6 GHz ou mais rápido.
* Transações por segundo (TPS).

Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contentor com o pull do docker

Imagens de contentor para a API Face estão disponíveis. 

| Contentor | Repositório |
|-----------|------------|
| Rostos | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Solicitação de docker para o contentor de rostos

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Utilizar o contentor

Depois do contentor está no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

1. [Execute o contentor](#run-the-container-with-docker-run) necessários com as definições de faturação. Obter mais [exemplos](./face-resource-container-config.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Execute o contentor com o docker, execute

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar qualquer um dos três contentores. O comando utiliza os seguintes parâmetros.

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor e está disponível no Azure `Cognitive Services` **chaves** página. |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível no Azure `Cognitive Services` **descrição geral** página. Um exemplo é `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Adicionar o `face/v1.0` encaminhamento para o ponto final do URI, conforme mostrado no exemplo anterior BILLING_ENDPOINT_URI. 

Substitua estes parâmetros pelos seus próprios valores na seguinte `docker run` exemplo de comando:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Este comando:

* Execute um contentor de face a partir da imagem de contentor.
* Aloca um núcleo de CPU e 4 GB de memória.
* Expõe a porta TCP 5000 e aloca um pseudo TTY para o contentor.
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião. 

Obter mais [exemplos](./face-resource-container-config.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor ou não inicia o contentor. Para obter mais informações, consulte [faturação](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contêiner fornece o ponto final de predição de consulta baseado em REST APIs. 

Usar o host, `https://localhost:5000`, para o contentor APIs.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o contentor com uma saída [montar](./face-resource-container-config.md#mount-settings) e o registo está ativado, o contentor gera os ficheiros de registo que são úteis para resolver os problemas que ocorrem enquanto iniciar ou executar o contentor. 


## <a name="billing"></a>Faturação

Os contentores de API de rostos enviar informações de faturação para o Azure com um recurso de API de rostos na sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para saber como transferir, instalar e executar contentores da Face API. Em resumo:

* A API Face fornece três contentores de Linux do Docker que fornecem a extração de expressões-chave, deteção de idioma e análise de sentimentos.
* Imagens de contentor são transferidas a partir do Azure Container Registry.
* Executam imagens de contentor no Docker.
* Pode utilizar a API REST ou o SDK para chamar operações em contentores de Face API ao especificar o URI do contentor do anfitrião.
* Tem de especificar informações de faturação quando criar uma instância de um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estiverem licenciados para executar sem a ser ligado ao Azure para medição. Os clientes tem de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviam dados de cliente, como a imagem ou texto que está a ser analisado, para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Para definições de configuração, consulte [configurar contentores](face-resource-container-config.md).
* Para saber mais sobre como detetar e identificar rostos, veja [descrição geral de Face](Overview.md).
* Para obter informações sobre os métodos suportados pelo contêiner, consulte a [a API Face](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Para utilizar mais contentores de serviços cognitivos, veja [contentores de serviços cognitivos](../cognitive-services-container-support.md).
