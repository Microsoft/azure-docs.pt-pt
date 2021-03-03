---
title: Instale e execute os recipientes Docker para a API text Analytics
titleSuffix: Azure Cognitive Services
description: Utilize os recipientes Docker para a API text Analytics para realizar o processamento de linguagem natural, como análise de sentimento, no local.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: aahi
keywords: no local, Docker, contentor, análise de sentimento, processamento de linguagem natural
ms.openlocfilehash: e815ecafe5d00f92a5430fdb71bcf952bc8984c8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736717"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contentores da Análise de Texto

> [!NOTE]
> * O recipiente para análise de sentimento e deteção de linguagem está agora geralmente disponível. O recipiente de extração de frases-chave está disponível como uma pré-visualização pública não acompanhada.
> * A ligação da entidade e o NER não estão atualmente disponíveis como recipiente.
> * O acesso ao Texto Analytics para recipiente de saúde requer um [formulário de pedido](https://aka.ms/csgate). Atualmente, não será cobrado pelo seu uso.
> * As localizações de imagem do contentor podem ter mudado recentemente. Leia este artigo para ver a localização atualizada deste recipiente.

Os contentores permitem-lhe executar as APIs de Análise de Texto no seu próprio ambiente e são ótimos para os requisitos específicos de governação de dados e segurança. Os recipientes Text Analytics fornecem processamento avançado da linguagem natural sobre o texto cru, e incluem três funções principais: análise de sentimento, extração de frases-chave e deteção de linguagem. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

> [!IMPORTANT]
> A conta gratuita está limitada a 5.000 transações por mês e apenas os níveis de preços **Gratuitos** e **Standard** são <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> válidos</a> para contentores. Para obter mais informações sobre as taxas de pedido de transação, consulte [Limites de Dados](../overview.md#data-limits).

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos recipientes text Analytics, você deve ter os ambientes do computador e do recipiente hospedeiro.

## <a name="preparation"></a>Preparação

Deve encontrar os seguintes pré-requisitos antes de utilizar os recipientes text Analytics:

|Necessário|Objetivo|
|--|--|
|Motor do Docker| Precisa do Motor Docker instalado num [computador anfitrião.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica de conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de `docker` comandos básicos.| 
|Recurso de análise de texto |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure [Text Analytics](../../cognitive-services-apis-create-account.md) com o nível de preços gratuito (F0) ou standard [(S).](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Terá de obter a chave API associada e o ponto final URI navegando na página **chave e ponto final** do seu recurso no portal Azure. <br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis. <br><br>**{ENDPOINT_URI}**: O ponto final do seu recurso. |

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações do contentor

O quadro a seguir descreve as especificações mínimas e recomendadas para os recipientes Text Analytics. São necessários pelo menos 2 gigabytes (GB) de memória, e cada núcleo de CPU deve ser de pelo menos 2,6 gigahertz (GHz) ou mais rápido. As transações admissíveis por secção (TPS) também estão listadas.

|  | Especificações mínimas de anfitrião | Especificações recomendadas do anfitrião | TPS mínimos | TPS máximo|
|---|---------|-------------|--|--|
| **Deteção de linguagem, extração de frases-chave**   | 1 núcleo, 2GB de memória | 1 núcleo, 4GB de memória |15 | 30|
| **Análise de Sentimentos**   | 1 núcleo, 2GB de memória | 4 núcleos, memória de 8GB |15 | 30|
| **Análise de texto para a saúde - 1 documento/pedido**   |  4 núcleo, memória de 10GB | 6 núcleo, 12GB de memória |15 | 30|
| **Análise de texto para saúde - 10 documentos/pedido**   |  6 núcleo, 16GB de memória | 8 núcleo, 20GB de memória |15 | 30|

O núcleo e a memória do CPU correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

As imagens do contentor para Análise de Texto estão disponíveis no Registo do Contentor da Microsoft.

# <a name="sentiment-analysis"></a>[Análise de Sentimentos ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de frases-chave (pré-visualização)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Deteção de Idioma](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Análise de texto para saúde (pré-visualização)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>Como usar o recipiente

Uma vez que o recipiente esteja no [computador anfitrião,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de faturação necessárias.
1. [Consultar o ponto final de previsão do recipiente.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com `docker run`

Use o comando de execução do [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar os contentores. O contentor continuará a funcionar até o parar.

> [!IMPORTANT]
> * Os comandos do estivador nas seguintes secções usam o corte `\` traseiro, como um personagem de continuação de linha. Substitua ou remova isto com base nos requisitos do seu sistema operativo anfitrião. 
> * As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](#billing)
> * Os recipientes de análise de sentimento e de deteção de linguagem estão geralmente disponíveis. O recipiente de extração de frase-chave utiliza v2 da API e está em pré-visualização.

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de frases-chave (pré-visualização)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Deteção de Idioma](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Análise de texto para saúde (pré-visualização)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contentor fornece APIs de ponto final de predição de consulta com base em REST.

Utilize o anfitrião, `http://localhost:5000`, para APIs de contentor.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](../text-analytics-resource-container-config.md#mount-settings) de saída e um registo ativado, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou execução do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os recipientes Text Analytics enviam informações de faturação para o Azure, utilizando um recurso _Text Analytics_ na sua conta Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [os recipientes Configure](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar recipientes de Text Analytics. Em resumo:

* A Text Analytics fornece três recipientes Linux para Docker, encapsulando várias capacidades:
   * *Análise de Sentimentos*
   * *Extração de frases-chave (pré-visualização)* 
   * *Deteção de Idioma*
   * *Análise de texto para saúde (pré-visualização)*
* As imagens do contentor são descarregadas a partir do Registo de Contentores da Microsoft (MCR) ou do repositório de contentores de pré-visualização.
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST ou a SDK para ligar para operações em recipientes Text Analytics especificando o hospedeiro URI do recipiente.
* Deve especificar as informações de faturação ao instantaneamente um recipiente.

> [!IMPORTANT]
> Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem estarem ligados ao Azure para a medição. Os clientes precisam de permitir que os contentores comuniquem informações de faturação com o serviço de medição em todos os momentos. Os recipientes de Serviços Cognitivos não enviam dados do cliente (por exemplo, texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Rever recipientes de configuração](../text-analytics-resource-container-config.md) para configurações de configuração
* Consulte [perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados com a funcionalidade.
