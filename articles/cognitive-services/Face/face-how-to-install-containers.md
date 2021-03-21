---
title: Instale e execute os recipientes Docker para a API face
titleSuffix: Azure Cognitive Services
description: Utilize o recipiente Docker para a API facial para detetar e identificar rostos humanos em imagens.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
keywords: no local, Docker, contentor, identificar
ms.openlocfilehash: 36cbd7bd24304871593b107f9b8ed9be02ce46de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706797"
---
# <a name="install-and-run-face-containers-preview"></a>Instalar e executar recipientes face (pré-visualização)

> [!IMPORTANT]
> Foi atingido o limite de utilizadores do contentor de Face. De momento, não estamos a aceitar novas aplicações para o contentor de Face.

A Azure Cognitive Services Face API fornece um recipiente Linux Docker que deteta e analisa rostos humanos em imagens. Também identifica atributos, que incluem marcos faciais como narizes e olhos, sexo, idade e outras características faciais previstas pela máquina. Além da deteção, o Face pode verificar se duas faces na mesma imagem ou imagens diferentes são as mesmas usando uma pontuação de confiança. Face também pode comparar rostos com uma base de dados para ver se um rosto semelhante ou idêntico já existe. Também pode organizar rostos semelhantes em grupos usando traços visuais partilhados.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Deve encontrar os seguintes pré-requisitos antes de utilizar os recipientes de serviço Face.

|Necessário|Objetivo|
|--|--|
|Motor do Docker| O Motor Docker deve ser instalado num [computador anfitrião](#the-host-computer). O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure. <br><br> No Windows, o Docker também deve ser configurado para suportar recipientes Linux.<br><br>|
|Familiaridade com Docker | Você precisa de uma compreensão básica dos conceitos de Docker, tais como registos, repositórios, contentores e imagens de contentores. Também precisa de conhecimento de `docker` comandos básicos.| 
|Recurso facial |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure **Face** e a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas **'Visão Geral'** e **Chaves** para o recurso. São obrigados a ligar o contentor.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final, conforme fornecido na página **'Vista Geral',**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações do contentor

A tabela seguinte descreve os núcleos e memórias de CPU mínimos e recomendados para alocar para cada recipiente de serviço Face.

| Contentor | Mínimo | Recomendado | Transações por segundo<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|Face | 1 núcleo, memória de 2 GB | 1 núcleo, memória de 4-GB |10, 20|

* Cada núcleo deve ser pelo menos 2,6 GHz ou mais rápido.
* Transações por segundo (TPS).

O núcleo e a memória correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com estivador puxar

Estão disponíveis imagens de contentores para o serviço Face. 

| Contentor | Repositório |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker puxa para o recipiente face

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Use o recipiente

Depois de o recipiente estar no [computador anfitrião,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente](#run-the-container-with-docker-run) com as definições de faturação necessárias. Mais [exemplos](./face-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis. 
1. [Consultar o ponto final de previsão do recipiente.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com estivador correr

Use o comando de execução do [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o contentor. Consulte a [recolha dos parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{ENDPOINT_URI}` valores e `{API_KEY}` valores.

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
* Aloca um núcleo de CPU e 4 GB de memória.
* Expõe a porta TCP 5000 e atribui um pseudo TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião. 

Mais [exemplos](./face-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis. 

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente ou o recipiente não arranca. Para mais informações, consulte [Billing.](#billing)

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contentor fornece APIs de ponto final de predição de consulta com base em REST. 

Utilize o anfitrião, `http://localhost:5000`, para APIs de contentor.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte de](./face-resource-container-config.md#mount-settings) saída e o registo de madeira estiver ativado, o contentor gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou funciona o recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os recipientes de serviço Face enviam informações de faturação para a Azure utilizando um recurso Face na sua conta Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [os recipientes Configure](./face-resource-container-config.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para como descarregar, instalar e executar recipientes de serviço Face. Em resumo:

* As imagens do contentor são descarregadas do Registo do Contentor de Azure.
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST ou o SDK para ligar para as operações em recipientes de serviço Face, especificando o hospedeiro URI do recipiente.
* Tem de especificar as informações de faturação quando instantânear um recipiente.

> [!IMPORTANT]
> Os contentores dos Serviços Cognitivos não estão licenciados para funcionar sem estarem ligados ao Azure para a medição. Os clientes devem permitir que os recipientes comuniquem sempre informações de faturação com o serviço de medição. Os contentores dos Serviços Cognitivos não enviam dados dos clientes, como a imagem ou texto que está a ser analisado, para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Para configurações, consulte [recipientes de configuração](face-resource-container-config.md).
* Para saber mais sobre como detetar e identificar rostos, consulte [a visão geral do Face](Overview.md).
* Para obter informações sobre os métodos suportados pelo recipiente, consulte a [API Face](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Para utilizar mais recipientes de Serviços Cognitivos, consulte [os recipientes dos Serviços Cognitivos.](../cognitive-services-container-support.md)
