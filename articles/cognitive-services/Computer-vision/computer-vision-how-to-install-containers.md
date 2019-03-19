---
title: Como instalar e executar contentores - de imagem digitalizada
titlesuffix: Azure Cognitive Services
description: Como transferir, instalar e executar contentores para imagem digitalizada neste tutorial passo a passo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 02/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1b26f0833dec95d886a56dde4526d02f697926ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004410"
---
# <a name="install-and-run-recognize-text-containers"></a>Instalar e executar contentores de reconhecer texto

A parte de reconhecer texto de imagem digitalizada também está disponível como um contentor do Docker. Permite-lhe detetar e extrair texto impresso de imagens de vários objetos com diferentes superfícies e fundos, como recibos, pôsteres e cartões de visita.  
> [!IMPORTANT]
> O contentor de reconhecer texto atualmente funciona apenas com o inglês.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores de reconhecer texto:

|Necessário|Objetivo|
|--|--|
|Motor do docker| É necessário o motor do Docker instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, Docker também tem de ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | Deve ter uma noção básica dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor, bem como dados de conhecimento do basic `docker` comandos.| 
|Reconhecer o recurso de texto |Para utilizar o contentor, tem de ter:<br><br>R [ _reconhecer texto_ ](vision-api-how-to-topics/howtosubscribe.md) recursos do Azure para obter a chave de faturação associada e a faturação URI do ponto final. Ambos os valores estão disponíveis nas páginas de descrição geral de texto reconhecer e chaves do portal do Azure e são necessários para iniciar o contentor.<br><br>**{BILLING_KEY}** : chave de recurso<br><br>**{BILLING_ENDPOINT_URI}** : exemplo URI do ponto final é: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>Pedir acesso para o registo de contentor privado

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os mínimos e recomendados núcleos de CPU e memória para atribuir cada contentor de reconhecer texto.

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
|Reconhecer texto|1 núcleo, 8 GB de memória, 0,5 TPS|2 núcleos, 8 GB de memória, 1 TPS|

Cada principal tem de ser, pelo menos, de 2,6 GHz (gigahertz) ou mais rápido.

Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.


## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contentor com o `docker pull`

Imagens de contentor para reconhecer texto estão disponíveis. 

| Contentor | Repositório |
|-----------|------------|
|Reconhecer texto | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Utilize o [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) comando para transferir uma imagem de contentor.


### <a name="docker-pull-for-the-recognize-text-container"></a>Solicitação de docker para o contentor de reconhecer texto

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como utilizar o contentor

Assim que o contentor estiver no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

1. [Execute o contentor](#run-the-container-with-docker-run), o necessário com as definições de faturação. Obter mais [exemplos](computer-vision-resource-container-config.md) do `docker run` comandos estão disponíveis. 
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Execute o contentor com `docker run`

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar o contentor. O comando utiliza os seguintes parâmetros:

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor e está disponível na página de chaves de texto de reconhecer o portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O ponto final faturação valor do URI.|

Substitua estes parâmetros pelos seus próprios valores no seguinte exemplo `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Este comando:

* Execute um contentor de reconhecer a partir da imagem de contentor
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião. 

Obter mais [exemplos](./computer-vision-resource-container-config.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contêiner fornece o ponto final de predição de consulta baseado em REST APIs. 

Usar o host, `https://localhost:5000`, para o contentor APIs.

### <a name="asynchronous-text-recognition"></a>Reconhecimento de texto assíncrona

Pode utilizar o `POST /vision/v2.0/recognizeText` e `GET /vision/v2.0/textOperations/*{id}*` operações em conjunto, de forma assíncrona reconhecer texto impresso numa imagem, semelhante a como o serviço de visão do computador utiliza as operações REST correspondentes. O contentor de reconhecer texto reconhece apenas texto impresso, texto manuscrito não, neste momento, pelo que a `mode` parâmetro normalmente especificado para a operação de serviço de visão do computador é ignorada pelo contentor de reconhecer texto.

### <a name="synchronous-text-recognition"></a>Reconhecimento de texto síncrona

Pode utilizar o `POST /vision/v2.0/recognizeTextDirect` operação de forma síncrona reconhecer texto impresso numa imagem. Uma vez que esta operação é síncrona, o corpo do pedido para esta operação é o mesmo que para o `POST /vision/v2.0/recognizeText` operação, mas a resposta body para esta operação é o mesmo que devolvido pelo `GET /vision/v2.0/textOperations/*{id}*` operação.

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o contentor com uma saída [montar](./computer-vision-resource-container-config.md#mount-settings) e registo ativado, o contentor gera os ficheiros de registo que são úteis para resolver os problemas que ocorrem ao iniciar ou executar o contentor. 

## <a name="containers-api-documentation"></a>Documentação da API do contentor

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Faturação

O envio de contentores de reconhecer texto cobrança informações para o Azure, utilizando um _reconhecer texto_ recursos na sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](./computer-vision-resource-container-config.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e reconhecer texto contentores em execução. Em resumo:

* Reconhecer texto fornece um contentor do Linux para o Docker, encapsulando reconhecer texto.
* Imagens de contentor são transferidas a partir do registo de contentor do Microsoft (MCR) no Azure.
* Executam imagens de contentor no Docker.
* Pode utilizar a REST API ou o SDK para chamar operações em contentores de reconhecer texto ao especificar o URI do contentor do anfitrião.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Revisão [configurar contentores](computer-vision-resource-container-config.md) para definições de configuração
* Revisão [descrição geral de imagem digitalizada](Home.md) para saber mais sobre o reconhecimento de texto manuscrito e impresso  
* Consulte a [API de imagem digitalizada](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para obter detalhes sobre os métodos suportados pelo contentor.
* Consulte a [perguntas mais frequentes (FAQ) do sobre](FAQ.md) para resolver problemas relacionados com a funcionalidade de imagem digitalizada.
* Utilizar mais [contentores de serviços cognitivos](../cognitive-services-container-support.md)
