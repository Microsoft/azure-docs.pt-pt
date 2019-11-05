---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como os contêineres do Docker podem obter serviços cognitivas mais perto de seus dados.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 10/14/2019
ms.author: dapine
ms.openlocfilehash: 926c2c0bbe4fdd24a6a8271b2e01ababcacf77d5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499182"
---
# <a name="container-support-in-azure-cognitive-services"></a>Suporte a contêineres nos serviços cognitivas do Azure

O suporte a contêineres nos serviços cognitivas do Azure permite que os desenvolvedores usem as mesmas APIs avançadas disponíveis no Azure, além de permitir flexibilidade em onde implantar e hospedar os serviços que acompanham os [contêineres do Docker](https://www.docker.com/what-container). Atualmente, o suporte a contêiner está disponível em versão prévia para um subconjunto de serviços cognitivas do Azure, incluindo partes do:

> [!div class="checklist"]
> * [Detector de anomalias][ad-containers]
> * [Imagem Digitalizada][cv-containers]
> * [Sorridente][fa-containers]
> * [Reconhecedor de formulário][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [API de Serviço de Voz][sp-containers]
> * [Análise de Texto][ta-containers]
> * [Tradução de Texto][tt-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

A Containerização é uma abordagem para a distribuição de software na qual um aplicativo ou serviço, incluindo suas dependências & configuração, é empacotado em conjunto como uma imagem de contêiner. Com pouca ou nenhuma modificação, uma imagem de contêiner pode ser implantada em um host de contêiner. Os contêineres são isolados uns dos outros e do sistema operacional subjacente, com uma superfície menor do que uma máquina virtual. Os contêineres podem ser instanciados de imagens de contêiner para tarefas de curto prazo e removidos quando não forem mais necessários.

Os recursos de serviços cognitivas estão disponíveis em [Microsoft Azure](https://azure.microsoft.com). Entre no [portal do Azure](https://portal.azure.com/) para criar e explorar os recursos do Azure para esses serviços.

## <a name="features-and-benefits"></a>Funcionalidades e benefícios

- **Controle sobre os dados**: permite que os clientes escolham onde esses serviços cognitivas processam seus dados. Isso é essencial para os clientes que não podem enviar dados para a nuvem, mas precisam de acesso à tecnologia de serviços cognitivas. Suporte a consistência em ambientes híbridos – entre dados, gerenciamento, identidade e segurança.
- **Controle sobre atualizações de modelo**: forneça aos clientes flexibilidade no controle de versão e atualização de modelos implantados em suas soluções.
- **Arquitetura portátil**: habilite a criação de uma arquitetura de aplicativo portátil que pode ser implantada no Azure, no local e na borda. Os contêineres podem ser implantados diretamente no [serviço kubernetes do Azure](../aks/index.yml), [instâncias de contêiner do Azure](../container-instances/index.yml)ou em um cluster [Kubernetes](https://kubernetes.io/) implantado para [Azure Stack](/azure-stack/operator). Para obter mais informações, consulte [Deploy kubernetes to Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta taxa de transferência/baixa latência**: forneça aos clientes a capacidade de dimensionar para alta taxa de transferência e requisitos de baixa latência, permitindo que os serviços cognitivas sejam executados fisicamente em seus dados e lógica do aplicativo. Os contêineres não limitem as transações por segundo (TPS) e podem ser feitos para escalar verticalmente e para lidar com a demanda se você fornecer os recursos de hardware necessários. 

## <a name="containers-in-azure-cognitive-services"></a>Contêineres nos serviços cognitivas do Azure

Os contêineres de serviços cognitivas do Azure fornecem o seguinte conjunto de contêineres do Docker, cada um contendo um subconjunto de funcionalidades de serviços nos serviços cognitivas do Azure:

| Serviço | Tipo de preço com suporte | Contentor | Descrição |
|---------|----------|----------|-------------|
|[Detetor de anomalias][ad-containers] |F0, S0|**Detector de anomalias** |A API do detector de anomalias permite que você monitore e detecte anormalidades em seus dados de série temporal com o aprendizado de máquina.<br>[Solicitar acesso](https://aka.ms/adcontainer)|
|[Imagem Digitalizada][cv-containers] |F0, S1|**Leitura** |Extrai o texto impresso de imagens de vários objetos com diferentes superfícies e planos de fundo, como recibos, pôsteres e cartões de visita. O contêiner de leitura também detecta *texto manuscrito* em imagens e fornece suporte a PDF/TIFF/várias páginas.<br/><br/>**Importante:** O contêiner de leitura atualmente funciona apenas com o inglês.|
|[Sorridente][fa-containers] |F0, S0|**Sorridente** |Detecta faces humanas em imagens e identifica atributos, incluindo pontos de referência de face (como narizs e olhos), gênero, idade e outros recursos faciais previstos por máquina. Além da detecção, a face pode verificar se duas faces na mesma imagem ou em imagens diferentes são as mesmas usando uma pontuação de confiança ou comparar rostos com um banco de dados para ver se já existe uma face semelhante ou idêntica. Ele também pode organizar faces semelhantes em grupos, usando características visuais compartilhadas.<br>[Solicitar acesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Reconhecedor de formulário][fr-containers] |F0, S0|**Reconhecedor de formulário** |A compreensão de formulário aplica a tecnologia de aprendizado de máquina para identificar e extrair pares de chave-valor e tabelas de formulários.<br>[Solicitar acesso](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**Luis** ([imagem](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Carrega um modelo de Reconhecimento vocal treinado ou publicado, também conhecido como aplicativo LUIS, em um contêiner do Docker e fornece acesso às previsões de consulta dos pontos de extremidade de API do contêiner. Você pode coletar logs de consulta do contêiner e carregá-los de volta para o [portal do Luis](https://www.luis.ai) para melhorar a precisão da previsão do aplicativo.|
|[API de Serviço de Voz][sp-containers-stt] |F0, S0|**Conversão de voz em texto** |Transcreve voz em tempo real contínua para texto.|
|[API de Serviço de Voz][sp-containers-cstt] |F0, S0|**Fala Personalizada para texto** |Transcreve a fala contínua em tempo real em texto usando um modelo personalizado.|
|[API de Serviço de Voz][sp-containers-tts] |F0, S0|**Conversão de texto em voz** |Converte texto em voz com som natural.|
|[API de Serviço de Voz][sp-containers-ctts] |F0, S0|**Conversão de texto em fala personalizada** |Converte o texto em uma fala de som natural usando um modelo personalizado.|
|[Análise de Texto][ta-containers] |F0, S|**Extração de frases-chave** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extrai frases-chave para identificar os pontos principais. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”. |
|[Análise de Texto][ta-containers]|F0, S|**Detecção de idioma** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Para até 120 idiomas, o detecta em qual idioma o texto de entrada é gravado e relata um único código de idioma para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação. |
|[Análise de Texto][ta-containers]|F0, S|**Análise de sentimento** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analisa o texto bruto para obter dicas sobre sentimentos positivos ou negativos. Esta API devolve uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é o mais positivo. Os modelos de análise são previamente treinados usando um corpo extensivo de tecnologias de texto e linguagem natural da Microsoft. Para os [idiomas selecionados](./text-analytics/language-support.md), a API pode analisar e classificar qualquer texto não processado que fornecer, devolvendo diretamente os resultados à aplicação de chamada. |
|[Tradução de Texto][tt-containers]| **N/D** | **Tradução de Texto** | A Tradução de Texto é um serviço de tradução automática com base na cloud que pode utilizar para traduzir texto quase em tempo real através de uma simples chamada à API REST.<br>[Solicitar acesso](https://aka.ms/translatorcontainerform) |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, alguns contêineres têm suporte em chaves de recurso de oferta de serviços cognitivas [**All-in-One**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) . Você pode criar um único recurso All-in-One de serviços cognitivas e usar a mesma chave de cobrança entre os serviços com suporte para os seguintes serviços:

* Imagem Digitalizada
* Rostos
* LUIS
* Análise de Texto

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade do contêiner nos serviços cognitivas do Azure

Os contêineres de serviços cognitivas do Azure estão publicamente disponíveis por meio de sua assinatura do Azure, e as imagens de contêiner do Docker podem ser extraídas do registro de contêiner da Microsoft ou do Hub do Docker. Você pode usar o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro apropriado.

> [!IMPORTANT]
> No momento, você deve concluir um processo de inscrição para acessar os seguintes contêineres, nos quais você preenche e envia um questionário com perguntas sobre você, sua empresa e o caso de uso para o qual você deseja implementar os contêineres. Depois de receber o acesso e as credenciais fornecidas, você pode extrair as imagens de contêiner de um registro de contêiner privado hospedado pelo registro de contêiner do Azure.
> * [Detetor de anomalias](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Sorridente](Face/face-how-to-install-containers.md)
> * [Reconhecedor de formulário](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Leitura](computer-vision/computer-vision-how-to-install-containers.md)
> * [Conversão de fala em texto e conversão de texto em fala](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)
> * [Tradução de Texto](translator/how-to-install-containers.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar os contêineres de serviços cognitivas do Azure:

**Mecanismo do Docker**: você deve ter o mecanismo do Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente do Docker no [MacOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker deve ser configurado para dar suporte a contêineres do Linux. Os contêineres do Docker também podem ser implantados diretamente no [serviço kubernetes do Azure](../aks/index.yml) ou em [instâncias de contêiner do Azure](../container-instances/index.yml).

O Docker deve ser configurado para permitir que os contêineres se conectem e enviem dados de cobrança para o Azure.

**Familiaridade com o registro de contêiner da Microsoft e com o Docker**: você deve ter uma compreensão básica do registro de contêiner da Microsoft e dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como o conhecimento de básico `docker` comandos.

Para obter uma introdução sobre o Docker e noções básicas de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).

Contêineres individuais também podem ter seus próprios requisitos, incluindo requisitos de alocação de servidor e memória.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as [receitas de contêiner](containers/container-reuse-recipe.md) que você pode usar com os serviços cognitivas.

Instale e explore a funcionalidade fornecida por contêineres nos serviços cognitivas do Azure:

* [Contêineres de detector de anomalias][ad-containers]
* [Contêineres de Pesquisa Visual Computacional][cv-containers]
* [Contêineres de face][fa-containers]
* [Contêineres do reconhecedor de formulário][fr-containers]
* [Contêineres de Reconhecimento vocal (LUIS)][lu-containers]
* [Contêineres da API do serviço de fala][sp-containers]
* [Contêineres de Análise de Texto][ta-containers]
* [Contêineres de Tradução de Texto][tt-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
<<<<<<< HEAD
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
=======
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: translator/how-to-install-containers.md
>>>>>>> refs/remotos/MicrosoftDocs/mestre
