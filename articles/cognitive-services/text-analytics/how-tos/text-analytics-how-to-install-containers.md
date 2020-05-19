---
title: Instalar e executar contentores - Análise de Texto
titleSuffix: Azure Cognitive Services
description: Como descarregar, instalar e executar recipientes para Text Analytics neste tutorial de walkthrough.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: d22dcf221bef40edb8bb2bd346dd5964000a4a68
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588417"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contentores da Análise de Texto

> [!NOTE]
> * O recipiente para Análise de Sentimentos v3 está agora geralmente disponível. A frase-chave de extração e dedetecção de linguagens está disponível como [pré-visualização pública ungida.](../../cognitive-services-gating-process.md)
> * Entidade seletiva e NER não estão atualmente disponíveis como recipiente.

Os contentores permitem-lhe executar as APIs analíticas do texto no seu próprio ambiente e são ótimos para os seus requisitos específicos de segurança e governança de dados. Os recipientes Text Analytics fornecem processamento avançado de linguagem natural sobre texto cru, e incluem três funções principais: análise de sentimentos, extração de frases-chave e deteção de linguagem. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> A conta gratuita está limitada a 5.000 transações por mês e apenas os níveis de preços **Free** e **Standard** são <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">válidos <span class="docon docon-navigate-external x-hidden-focus"></span> </a> para contentores. Para obter mais informações sobre as taxas de pedido de transação, consulte [Limites de Dados](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos recipientes text Analytics, deve ter o computador de acolhimento e os ambientes de contentores.

## <a name="preparation"></a>Preparação

Deve cumprir os seguintes pré-requisitos antes de utilizar os recipientes Text Analytics:

|Necessário|Objetivo|
|--|--|
|Motor do Docker| Precisa do Motor Docker instalado num [computador de acolhimento.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os recipientes se conectem e enviem dados de faturação para o Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar os recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica dos conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de `docker` comandos básicos.| 
|Recurso de Análise de Texto |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure [Text Analytics](../../cognitive-services-apis-create-account.md) para obter a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas de Visão Geral e Chaves do portal Azure e são necessários para iniciar o recipiente.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final fornecido na página **'Visão Geral'**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações de contentores

O quadro seguinte descreve as especificações mínimas e recomendadas para os recipientes text Analytics. São necessários pelo menos 2 gigabytes (GB) de memória, e cada núcleo cpu deve ser pelo menos 2,6 gigahertz (GHz) ou mais rápido. As transações admissíveis por secção (TPS) também estão listadas.

|  | Especificações mínimas de acolhimento | Especificações recomendadas para o hospedeiro | TPS mínimo | TPS máximo|
|---|---------|-------------|--|--|
| **Deteção de linguagem, extração de frase-chave**   | 1 núcleo, 2GB de memória | 1 núcleo, 4GB de memória |15 | 30|
| **Análise de Sentimento v3**   | 1 núcleo, 2GB de memória | 4 núcleos, 8GB de memória |15 | 30|

O núcleo e a memória do CPU correspondem às `--cpus` definições e `--memory` definições, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com`docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

As imagens do contentor para O Análise de Texto estão disponíveis no Registo de Contentores da Microsoft.

# <a name="sentiment-analysis-v3"></a>[Análise de Sentimento v3](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de frases-chave (pré-visualização)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Deteção de Idiomas (pré-visualização)](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

***

## <a name="how-to-use-the-container"></a>Como utilizar o recipiente

Uma vez que o recipiente esteja no [computador de acolhimento,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de faturação necessárias.
1. [Consulta do ponto final da previsão do recipiente](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com`docker run`

Utilize o comando de execução de [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar os contentores. O recipiente continuará a funcionar até o parar.

Substitua os espaços reservados abaixo por valores próprios:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave para o seu recurso Text Analytics. Pode encontrá-lo na **página chave e ponto final** do seu recurso, no portal Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O ponto final para aceder à API de Análise de Texto. Pode encontrá-lo na **página chave e ponto final** do seu recurso, no portal Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

> [!IMPORTANT]
> * Os comandos do estivador nas seguintes secções usam o corte traseiro, como um carácter de continuação da `\` linha. Substitua ou remova isto com base nos requisitos do sistema operativo do hospedeiro. 
> * O `Eula` `Billing` , e as `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [billing.](#billing)
> * O recipiente v3 de análise de sentimento está agora geralmente disponível, o que devolve [etiquetas](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) de sentimento na resposta. A frase-chave de extração e dedetecção de linguagens utiliza v2 da API e está em pré-visualização.

# <a name="sentiment-analysis-v3"></a>[Análise de Sentimento v3](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de frases-chave (pré-visualização)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Deteção de Idiomas (pré-visualização)](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consulta do ponto final da previsão do recipiente

O recipiente fornece APIs finais de previsão de consulta baseadas em REST.

Utilize o `http://localhost:5000` hospedeiro, para apis de contentor.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](../text-analytics-resource-container-config.md#mount-settings) de saída e uma exploração de madeira ativada, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou funcionamento do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os recipientes Text Analytics enviam informações de faturação para o Azure, utilizando um recurso _Text Analytics_ na sua conta Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para mais informações sobre estas opções, consulte [os recipientes Configur.](../text-analytics-resource-container-config.md)

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar contentores de Text Analytics. Em resumo:

* O Text Analytics fornece três recipientes Linux para o Docker, encapsulando várias capacidades:
   * *Análise de Sentimentos*
   * *Extração de frases-chave (pré-visualização)* 
   * *Deteção de Idiomas (pré-visualização)*
   
* As imagens de contentores são descarregadas a partir do Registo de Contentores da Microsoft (MCR) em Azure.
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST ou SDK para ligar para operações em recipientes de Análise de Texto, especificando o uri hospedeiro do recipiente.
* Deve especificar a informação de faturação ao instantaneamente um recipiente.

> [!IMPORTANT]
> Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem serem ligados ao Azure para medição. Os clientes precisam de permitir que os contentores comuniquem sempre informações de faturação com o serviço de medição. Os recipientes dos Serviços Cognitivos não enviam dados dos clientes (por exemplo, texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Rever [Configure recipientes](../text-analytics-resource-container-config.md) para configurações de configuração
* Consulte [as perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados com a funcionalidade.
