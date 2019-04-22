---
title: Instalar, executar contentores
titlesuffix: Face - Azure Cognitive Services
description: Como transferir, instalar e executar contentores para rosto neste tutorial passo a passo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 5028a05fe74f1d19ed5e43ac797df87bbe3382e8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680728"
---
# <a name="install-and-run-face-containers"></a>Instalar e executar contentores de rostos

Face fornece um contentor de Linux padronizado para o Docker, rosto, que Deteta rostos humanos em imagens e identifica os atributos, incluindo pontos de referência do rosto (como noses e olhos), sexo, idade e outras funcionalidades faciais prever a máquina com o nome. Além de deteção, Face pode verificar se dois rostos na mesma imagem ou imagens diferentes são o mesmo com uma pontuação de confiança, ou comparam rostos em relação a uma base de dados para ver se um aspeto semelhante ou idêntica face já existe. Ele também pode organizar o rostos semelhantes em grupos, com as características de visual partilhadas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores da Face API:

|Necessário|Objetivo|
|--|--|
|Motor do docker| É necessário o motor do Docker instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, Docker também tem de ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | Deve ter uma noção básica dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor, bem como dados de conhecimento do basic `docker` comandos.| 
|Azure `Cognitive Services` recursos |Para utilizar o contentor, tem de ter:<br><br>R _dos serviços cognitivos_ o ponto final de faturação URI da chave de recursos do Azure e a faturação associada. Ambos os valores estão disponíveis nas páginas de descrição geral e as chaves para o recurso e são necessários para iniciar o contentor. Tem de adicionar o `face/v1.0` encaminhamento para o URI do ponto de extremidade, conforme mostrado no exemplo a seguir BILLING_ENDPOINT_URI. <br><br>**{BILLING_KEY}** : chave de recurso<br><br>**{BILLING_ENDPOINT_URI}** : exemplo URI do ponto final é: `https://westus.api.cognitive.microsoft.com/face/v1.0`|


## <a name="request-access-to-the-private-container-registry"></a>Pedir acesso para o registo de contentor privado

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os mínimos e recomendados núcleos de CPU e memória para atribuir cada contentor da Face API.

| Contentor | Mínimo | Recomendado | TPS<br>(No mínimo, máximo)|
|-----------|---------|-------------|--|
|Rostos | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |10, 20|

* Cada principal tem de ser, pelo menos, de 2,6 GHz (gigahertz) ou mais rápido.
* TPS - transações por segundo

Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contentor com o `docker pull`

Imagens de contentor para a API de rostos estão disponíveis. 

| Contentor | Repositório |
|-----------|------------|
| Rostos | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Solicitação de docker para o contentor de rostos

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="how-to-use-the-container"></a>Como utilizar o contentor

Assim que o contentor estiver no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

1. [Execute o contentor](#run-the-container-with-docker-run), o necessário com as definições de faturação. Obter mais [exemplos](./face-resource-container-config.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Execute o contentor com `docker run`

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar qualquer um dos três contentores. O comando utiliza os seguintes parâmetros:

| Marcador de posição | Valor |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor e está disponível no Azure `Cognitive Services` página chaves.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível no Azure `Cognitive Services` página de descrição geral. Exemplo é: `https://westus.api.cognitive.microsoft.com/face/v1.0`|

Tem de adicionar o `face/v1.0` encaminhamento para o URI do ponto de extremidade, conforme mostrado no exemplo anterior BILLING_ENDPOINT_URI. 

Substitua estes parâmetros pelos seus próprios valores no seguinte exemplo `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Este comando:

* Execute um contentor de face a partir da imagem de contentor
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião. 

Obter mais [exemplos](./face-resource-container-config.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contêiner fornece o ponto final de predição de consulta baseado em REST APIs. 

Usar o host, `https://localhost:5000`, para o contentor APIs.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o contentor com uma saída [montar](./face-resource-container-config.md#mount-settings) e registo ativado, o contentor gera os ficheiros de registo que são úteis para resolver os problemas que ocorrem ao iniciar ou executar o contentor. 


## <a name="billing"></a>Faturação

O envio de contentores da Face API cobrança informações para o Azure, utilizando um _a API Face_ recursos na sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](./face-resource-container-config.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e a API Face contentores em execução. Em resumo:

* Face API fornece três contentores do Linux para o Docker, encapsulando a extração de expressões-chave, deteção de idioma e análise de sentimentos.
* Imagens de contentor são transferidas a partir do registo de contentor do Microsoft (MCR) no Azure.
* Executam imagens de contentor no Docker.
* Pode utilizar a REST API ou o SDK para chamar operações em contentores de Face API ao especificar o URI do contentor do anfitrião.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Revisão [configurar contentores](face-resource-container-config.md) para definições de configuração
* Revisão [descrição geral de Face](Overview.md) para saber mais sobre a detetar e identificar rostos  
* Consulte a [a API Face](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter detalhes sobre os métodos suportados pelo contentor.
* Consulte a [perguntas mais frequentes (FAQ) do sobre](FAQ.md) para resolver problemas relacionados com a funcionalidade de rostos.
* Utilizar mais [contentores de serviços cognitivos](../cognitive-services-container-support.md)
