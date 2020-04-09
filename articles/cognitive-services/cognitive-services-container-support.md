---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como os recipientes do Docker podem aproximar os Serviços Cognitivos dos seus dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7a38ec47d416027e8ea3fa772ae01e4f6264197a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876840"
---
# <a name="container-support-in-azure-cognitive-services"></a>Suporte de contentores em Serviços Cognitivos Azure

O suporte de contentores nos Serviços Cognitivos Azure permite que os desenvolvedores utilizem as mesmas APIs ricas que estão disponíveis no Azure, e permite flexibilidade para onde implantar e acolher os serviços que vêm com [contentores Docker.](https://www.docker.com/what-container) O suporte do contentor está atualmente disponível para um subconjunto de Serviços Cognitivos Azure, incluindo partes de:

> [!div class="checklist"]
> * [Detetor de Anomalias][ad-containers]
> * [Imagem Digitalizada][cv-containers]
> * [Rostos][fa-containers]
> * [Reconhecedor de Formato][fr-containers]
> * [Compreensão de Idiomas (LUIS)][lu-containers]
> * [API de Serviço de Voz][sp-containers]
> * [Análise de Texto][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

A containerização é uma abordagem à distribuição de software em que uma aplicação ou serviço, incluindo as suas dependências & configuração, é embalado em conjunto como uma imagem de recipiente. Com pouca ou nenhuma modificação, uma imagem de recipiente pode ser implantada num hospedeiro de contentores. Os contentores são isolados uns dos outros e do sistema operativo subjacente, com uma pegada menor do que uma máquina virtual. Os recipientes podem ser instantâneos a partir de imagens de contentores para tarefas de curto prazo, e removidos quando já não são necessários.

Os recursos dos Serviços Cognitivos estão disponíveis no [Microsoft Azure.](https://azure.microsoft.com) Assine no [portal Azure](https://portal.azure.com/) para criar e explorar os recursos do Azure para estes serviços.

## <a name="features-and-benefits"></a>Características e benefícios

- **Infraestrutura imutável**: Permitir que as equipas de DevOps aproveitem um conjunto consistente e fiável de parâmetros conhecidos do sistema, ao mesmo tempo que conseguem adaptar-se à mudança. Os recipientes proporcionam a flexibilidade para girar dentro de um ecossistema previsível e evitar a deriva de configuração.
- **Controlo sobre os dados**: Permitir que os clientes escolham onde estes Serviços Cognitivos processam os seus dados. Isto é essencial para clientes que não podem enviar dados para a nuvem, mas precisam de acesso à tecnologia de Serviços Cognitivos. Apoiar a consistência em ambientes híbridos – através de dados, gestão, identidade e segurança.
- **Controlo sobre as atualizações**de modelos : Proporcionar aos clientes flexibilidade na versão e atualização dos modelos implementados nas suas soluções.
- **Arquitetura portátil**: Permitir a criação de uma arquitetura de aplicação portátil que pode ser implantada em Azure, no local e na borda. Os contentores podem ser implantados diretamente para o [Serviço Azure Kubernetes,](../aks/index.yml)instâncias de [contentores Azure,](../container-instances/index.yml)ou para um cluster [Kubernetes](https://kubernetes.io/) implantado para [a Stack Azure](/azure-stack/operator). Para mais informações, consulte A utilização de [Kubernetes para O Stack Azure](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta frequência /baixa latência**: Fornecer aos clientes a capacidade de escalar para requisitos de alta frequência e baixa latência, permitindo que os Serviços Cognitivos corram fisicamente perto da sua lógica de aplicação e dados. Os contentores não cobrem transações por segundo (TPS) e podem ser feitos para escalar tanto para cima como para fora para lidar com a procura se fornecer os recursos de hardware necessários.
- **Escalabilidade**: Com a popularidade cada vez maior do software de containerização e orquestração de contentores, como kubernetes; a escalabilidade está na vanguarda dos avanços tecnológicos. Com base numa fundação de cluster escalável, o desenvolvimento de aplicações atende à elevada disponibilidade.

## <a name="containers-in-azure-cognitive-services"></a>Contentores em Serviços Cognitivos Azure

Os recipientes dos Serviços Cognitivos Azure fornecem o seguinte conjunto de recipientes Docker, cada um dos quais contém um subconjunto de funcionalidades dos serviços em Serviços Cognitivos Azure:

| Serviço | Nível de Preços Suportados | Contentor | Descrição |
|---------|----------|----------|-------------|
|[Detetor de anomalias][ad-containers] |F0, S0|**Detetor de anomalias** |A API do Detetor de Anomalias permite-lhe monitorizar e detetar anomalias nos dados da série temporal com machine learning.<br>[Pedir acesso](https://aka.ms/adcontainer)|
|[Imagem Digitalizada][cv-containers] |F0, S1|**Leitura** |Extratos de texto impresso a partir de imagens de vários objetos com diferentes superfícies e fundos, tais como recibos, cartazes e cartões de visita. O recipiente De leitura também deteta *texto manuscrito* em imagens e fornece suporte PDF/TIFF/multi-página.<br/><br/>**Importante:** O recipiente De Leitura funciona atualmente apenas com inglês.|
|[Rostos][fa-containers] |F0, S0|**Rostos** |Deteta rostos humanos em imagens e identifica atributos, incluindo marcos faciais (como narizes e olhos), sexo, idade e outras características faciais previstas por máquinas. Além da deteção, o Face pode verificar se duas faces na mesma imagem ou imagens diferentes são as mesmas usando uma pontuação de confiança, ou comparar rostos com uma base de dados para ver se já existe um rosto semelhante ou idêntico. Também pode organizar rostos semelhantes em grupos, usando traços visuais partilhados.<br>[Pedir acesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Reconhecimento de formulários][fr-containers] |F0, S0|**Reconhecedor de Formato** |A Forma Compreensão aplica tecnologia de machine learning para identificar e extrair pares e tabelas de valor-chave a partir de formulários.<br>[Pedir acesso](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)|Carrega um modelo de Compreensão linguística treinado ou publicado, também conhecido como app LUIS, num recipiente de estivador e dá acesso às previsões de consulta dos pontos finais da API do contentor. Pode recolher registos de consulta do recipiente e carregá-los de volta para o [portal LUIS](https://www.luis.ai) para melhorar a precisão de previsão da aplicação.|
|[API de Serviço de Voz][sp-containers-stt] |F0, S0|**Conversão de voz em texto** |Transcreve voz em tempo real contínua para texto.|
|[API de Serviço de Voz][sp-containers-cstt] |F0, S0|**Discurso personalizado a texto** |Transcreve o discurso contínuo em tempo real em texto usando um modelo personalizado.|
|[API de Serviço de Voz][sp-containers-tts] |F0, S0|**Conversão de texto em voz** |Converte texto em voz com som natural.|
|[API de Serviço de Voz][sp-containers-ctts] |F0, S0|**Texto-a-fala personalizado** |Converte o texto em discurso de som natural usando um modelo personalizado.|
|[Análise de Texto][ta-containers-keyphrase] |F0, S|**Extração de frases-chave** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409) |Extrai frases-chave para identificar os pontos principais. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”. |
|[Análise de Texto][ta-containers-language]|F0, S|**Deteção de Linguagem** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409) |Para até 120 línguas, deteta em que língua o texto de entrada está escrito e reporta um único código linguístico para cada documento apresentado a pedido. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação. |
|[Análise de Texto][ta-containers-sentiment]|F0, S|**Análise de Sentimentos** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409) |Analisa texto cru para pistas sobre sentimentopositivo ou negativo. Esta API devolve uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é o mais positivo. Os modelos de análise são pré-treinados usando um vasto corpo de tecnologias de texto e linguagem natural da Microsoft. Para os [idiomas selecionados](./text-analytics/language-support.md), a API pode analisar e classificar qualquer texto não processado que fornecer, devolvendo diretamente os resultados à aplicação de chamada. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, alguns recipientes são suportados em Serviços Cognitivos [**All-In-One oferecendo**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) chaves de recursos. Você pode criar um único recurso De Serviços Cognitivos All-In-One e usar a mesma chave de faturação em serviços suportados para os seguintes serviços:

* Imagem Digitalizada
* Rostos
* LUIS
* Análise de Texto

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade de contentores em Serviços Cognitivos Azure

Os contentores dos Serviços Cognitivos Azure estão disponíveis publicamente através da sua subscrição Azure, e as imagens de contentores Docker podem ser retiradas do Registo de Contentores da Microsoft ou do Docker Hub. Pode utilizar o comando de puxar pelo [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo apropriado.

> [!IMPORTANT]
> Atualmente, tem de concluir um processo de inscrição para aceder aos seguintes contentores, no qual preenche e submete um questionário com perguntas sobre si, a sua empresa e o caso de utilização para o qual pretende implementar os contentores. Assim que lhe for concedido acesso e credenciais fornecidas, pode então retirar as imagens do contentor de um registo de contentores privados hospedado pelo Registo de Contentores Azure.
> * [Detetor de anomalias](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Rostos](Face/face-how-to-install-containers.md)
> * [Reconhecedor de Formato](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Leitura](computer-vision/computer-vision-how-to-install-containers.md)
> * [Discurso-a-texto e Texto-a-fala](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Pré-requisitos

Deve satisfazer os seguintes pré-requisitos antes de utilizar os recipientes dos Serviços Cognitivos Azure:

**Motor Docker**: Você deve ter o Motor Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente Docker no [macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker deve ser configurado para suportar os recipientes Linux. Os recipientes de estivadores também podem ser implantados diretamente para o [Serviço Azure Kubernetes](../aks/index.yml) ou para as instâncias de [contentores Azure](../container-instances/index.yml).

O Docker deve ser configurado para permitir que os recipientes se conectem e enviem dados de faturação para o Azure.

**Familiaridade com o Microsoft Container Registry e Docker**: Você deve ter uma compreensão básica tanto dos conceitos de Registo de Contentores `docker` da Microsoft como do Docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de comandos básicos.

Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).

Os recipientes individuais também podem ter os seus próprios requisitos, incluindo os requisitos de atribuição de servidores e de memória.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as receitas](containers/container-reuse-recipe.md) de recipientes que pode usar com os Serviços Cognitivos.

Instale e explore a funcionalidade fornecida por contentores em Serviços Cognitivos Azure:

* [Recipientes de detetor de anomalias][ad-containers]
* [Recipientes de Visão Computacional][cv-containers]
* [Recipientes faciais][fa-containers]
* [Recipientes de reconhecimento de formulário][fr-containers]
* [Recipientes de compreensão linguística (LUIS)][lu-containers]
* [Contentores da API do Serviço de Fala][sp-containers]
* [Recipientes de Análise de Texto][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment