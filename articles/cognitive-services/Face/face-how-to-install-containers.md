---
title: Instalar e executar contentores - Face
titleSuffix: Azure Cognitive Services
description: Este artigo mostra-lhe como descarregar, instalar e executar recipientes para face neste tutorial de walkthrough.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bd1449501cdc9483621a5408a3a4926afe90212f
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702144"
---
# <a name="install-and-run-face-containers-preview"></a>Instalar e executar recipientes face (Pré-visualização)

O Azure Cognitive Services Face fornece um recipiente linux padronizado para o Docker que deteta rostos humanos em imagens. Também identifica atributos, que incluem marcos faciais como narizes e olhos, sexo, idade e outras características faciais previstas para máquinas. Além da deteção, o Face pode verificar se duas faces na mesma imagem ou imagens diferentes são as mesmas usando uma pontuação de confiança. O rosto também pode comparar rostos com uma base de dados para ver se um rosto semelhante ou idêntico já existe. Também pode organizar rostos semelhantes em grupos usando traços visuais partilhados.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Deve cumprir os seguintes pré-requisitos antes de utilizar os recipientes de serviço Face.

|Necessário|Objetivo|
|--|--|
|Motor do Docker| O Motor Docker deve ser instalado num [computador de acolhimento](#the-host-computer). O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os recipientes se conectem e enviem dados de faturação para o Azure. <br><br> No Windows, o Docker também deve ser configurado para suportar os recipientes Linux.<br><br>|
|Familiaridade com Docker | Você precisa de uma compreensão básica dos conceitos docker, tais como registos, repositórios, contentores e imagens de contentores. Também precisa de conhecimento de `docker` comandos básicos.| 
|Recurso facial |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure **Face** e a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas **Overview** e **Keys** para o recurso. São obrigados a ligar o contentor.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final fornecido na página **'Visão Geral'**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registo de contentores privados

Preencha e submeta o formulário de [pedido](https://aka.ms/VisionContainersPreview) para solicitar o acesso ao recipiente. 

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações de contentores

A tabela seguinte descreve os núcleos e memória cpu mínimos e recomendados para alocar para cada recipiente de serviço Face.

| Contentor | Mínimo | Recomendado | Transações por segundo<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|Rostos | 1 núcleo, 2-GB de memória | 1 núcleo, 4-GB de memória |10, 20|

* Cada núcleo deve ter pelo menos 2,6 GHz ou mais rápido.
* Transações por segundo (TPS).

O núcleo e a memória correspondem às `--cpus` definições e `--memory` definições, que são usadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com puxar o estivador

Estão disponíveis imagens de contentores para o serviço Face. 

| Contentor | Repositório |
|-----------|------------|
| Rostos | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker puxar para o recipiente Face

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Use o recipiente

Depois de o recipiente estar no [computador de acolhimento,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente](#run-the-container-with-docker-run) com as definições de faturação necessárias. Mais [exemplos](./face-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis. 
1. [Consulta do ponto final da previsão do recipiente](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Corra o recipiente com estivador run

Use o comando de execução de [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o recipiente. Consulte a recolha de [parâmetros necessários](#gathering-required-parameters) para obter os `{ENDPOINT_URI}` valores e `{API_KEY}` valores necessários.

[Exemplos](face-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente facial a partir da imagem do recipiente.
* Atribui um núcleo cpu e 4 GB de memória.
* Expõe a porta TCP 5000 e atribui um pseudo TTY para o recipiente.
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro. 

Mais [exemplos](./face-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis. 

> [!IMPORTANT]
> E `Eula` `Billing` as `ApiKey` opções devem ser especificadas para executar o recipiente ou o recipiente não arranca. Para mais informações, consulte [billing.](#billing)

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Consulta do ponto final da previsão do recipiente

O recipiente fornece APIs finais de previsão de consulta baseadas em REST. 

Utilize o `http://localhost:5000` hospedeiro, para apis de contentor.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](./face-resource-container-config.md#mount-settings) de saída e o registo estiver ativado, o recipiente gera ficheiros de registo que são úteis para resolver problemas que acontecem durante o arranque ou execução do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os recipientes de serviço Face enviam informações de faturação para o Azure utilizando um recurso Face na sua conta Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para mais informações sobre estas opções, consulte [os recipientes Configur.](./face-resource-container-config.md)

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para como descarregar, instalar e executar recipientes de serviço Face. Em resumo:

* As imagens do contentor são descarregadas do Registo de Contentores Azure.
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST ou o SDK para ligar para as operações em recipientes de serviço Face, especificando o uri hospedeiro do contentor.
* Deve especificar a informação de faturação quando instantaneamente um recipiente.

> [!IMPORTANT]
> Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem serem ligados ao Azure para medição. Os clientes devem permitir que os recipientes comuniquem sempre informações de faturação com o serviço de medição. Os recipientes dos Serviços Cognitivos não enviam dados dos clientes, como a imagem ou texto que está a ser analisado, para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Para configurações de configuração, consulte [os recipientes de configuração](face-resource-container-config.md).
* Para saber mais sobre como detetar e identificar rostos, consulte a visão geral do [Rosto.](Overview.md)
* Para obter informações sobre os métodos suportados pelo recipiente, consulte a [API facial](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Para utilizar mais recipientes de Serviços Cognitivos, consulte [os recipientes dos Serviços Cognitivos.](../cognitive-services-container-support.md)
