---
title: Como instalar e executar contêineres-Pesquisa Visual Computacional
titlesuffix: Azure Cognitive Services
description: Como transferir, instalar e executar contentores para imagem digitalizada neste tutorial passo a passo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: efde223061a873a57595bc4a577b7de55b1d8a46
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321470"
---
# <a name="install-and-run-recognize-text-containers"></a>Instalar e executar contêineres de Reconhecimento de Texto

A parte de reconhecer texto de imagem digitalizada também está disponível como um contentor do Docker. Permite-lhe detetar e extrair texto impresso de imagens de vários objetos com diferentes superfícies e fundos, como recibos, pôsteres e cartões de visita.  
> [!IMPORTANT]
> O contentor de reconhecer texto atualmente funciona apenas com o inglês.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar contêineres de Reconhecimento de Texto:

|Necessário|Objetivo|
|--|--|
|Mecanismo do Docker| Você precisa do mecanismo do Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [MacOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/)e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | Você deve ter uma compreensão básica dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como o conhecimento de comandos `docker` básicos.| 
|Pesquisa Visual Computacional recurso |Para usar o contêiner, você deve ter:<br><br>Um recurso de **Pesquisa Visual computacional** do Azure e a chave de API associada do URI do ponto de extremidade. Ambos os valores estão disponíveis nas páginas visão geral e chaves para o recurso e são necessários para iniciar o contêiner.<br><br>**{API_KEY}** : Uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}** : O ponto de extremidade conforme fornecido na página **visão geral**|

## <a name="request-access-to-the-private-container-registry"></a>Pedir acesso para o registo de contentor privado

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela a seguir descreve os núcleos de CPU mínimos e recomendados e a memória a ser alocada para cada contêiner de Reconhecimento de Texto.

| Contentor | Mínimo | Recomendado |TPS<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|Reconhecimento de Texto|1 núcleo, 8 GB de memória, 0,5 TPS|2 núcleos, 8 GB de memória, 1 TPS|0,5, 1|

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* TPS-transações por segundo

O núcleo e a `--cpus` memória correspondem às configurações e `--memory` , que são `docker run` usadas como parte do comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com`docker pull`

As imagens de contêiner para Reconhecimento de Texto estão disponíveis. 

| Contentor | Repositório |
|-----------|------------|
|Reconhecimento de Texto | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner.


### <a name="docker-pull-for-the-recognize-text-container"></a>Pull do Docker para o contêiner de Reconhecimento de Texto

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run)com as configurações de cobrança necessárias. Mais [exemplos](computer-vision-resource-container-config.md) do `docker run` comando estão disponíveis. 
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Execute o contêiner com`docker run`

Use o comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. O comando usa os seguintes parâmetros:

| Marcador de posição | Value |
|-------------|-------|
|{API_KEY} | Essa chave é usada para iniciar o contêiner e está disponível na página chaves do `Cognitive Services` Azure.  |
|{ENDPOINT_URI} | O valor do URI do ponto de extremidade de cobrança. O exemplo é:`https://westus.api.cognitive.microsoft.com/vision/v2.0`|

Você precisa adicionar o `vision/v2.0` roteamento ao URI do ponto de extremidade, conforme mostrado no exemplo de BILLING_ENDPOINT_URI a seguir.

Substitua esses parâmetros pelos seus próprios valores no comando de exemplo `docker run` a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um contêiner de reconhecimento da imagem de contêiner
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host. 

Mais [exemplos](./computer-vision-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis. 

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST. 

Use o host, `http://localhost:5000`, para APIs de contêiner.

### <a name="asynchronous-text-recognition"></a>Reconhecimento de texto assíncrona

Pode utilizar o `POST /vision/v2.0/recognizeText` e `GET /vision/v2.0/textOperations/*{id}*` operações em conjunto, de forma assíncrona reconhecer texto impresso numa imagem, semelhante a como o serviço de visão do computador utiliza as operações REST correspondentes. O contentor de reconhecer texto reconhece apenas texto impresso, texto manuscrito não, neste momento, pelo que a `mode` parâmetro normalmente especificado para a operação de serviço de visão do computador é ignorada pelo contentor de reconhecer texto.

### <a name="synchronous-text-recognition"></a>Reconhecimento de texto síncrona

Pode utilizar o `POST /vision/v2.0/recognizeTextDirect` operação de forma síncrona reconhecer texto impresso numa imagem. Como essa operação é síncrona, o corpo da solicitação para essa operação é o mesmo que `POST /vision/v2.0/recognizeText` a operação, mas o corpo da resposta para essa operação é o mesmo que o retornado `GET /vision/v2.0/textOperations/*{id}*` pela operação.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se você executar o contêiner com uma [montagem](./computer-vision-resource-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem ao iniciar ou executar o contêiner. 


## <a name="billing"></a>Faturação

Os contêineres de Reconhecimento de Texto enviam informações de cobrança para o Azure, usando um recurso de _reconhecimento de texto_ em sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar Reconhecimento de Texto contêineres. Em resumo:

* Reconhecimento de Texto fornece um contêiner do Linux para o Docker, encapsulando o texto de reconhecimento.
* Imagens de contentor são transferidas a partir do registo de contentor do Microsoft (MCR) no Azure.
* Executam imagens de contentor no Docker.
* Você pode usar a API REST ou o SDK para chamar operações em contêineres Reconhecimento de Texto especificando o URI do host do contêiner.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Os contêineres de serviços cognitivas não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Revisão [configurar contentores](computer-vision-resource-container-config.md) para definições de configuração
* Revisão [descrição geral de imagem digitalizada](Home.md) para saber mais sobre o reconhecimento de texto manuscrito e impresso  
* Consulte a [API de imagem digitalizada](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para obter detalhes sobre os métodos suportados pelo contentor.
* Consulte a [perguntas mais frequentes (FAQ) do sobre](FAQ.md) para resolver problemas relacionados com a funcionalidade de imagem digitalizada.
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)
