---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como contentores do Docker podem obter o mais perto dos serviços cognitivos aos seus dados.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: ae4b4b13e81bc000bc0675c80c09101cc4369bc1
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130134"
---
# <a name="container-support-in-azure-cognitive-services"></a>Suporte para contentores nos serviços cognitivos do Azure

Suporte para contentores nos serviços cognitivos do Azure permite aos desenvolvedores usar as mesmas APIs avançadas estão disponíveis no Azure e permite flexibilidade no onde implementar e hospedar os serviços que acompanham [contentores do Docker](https://www.docker.com/what-container). Atualmente, o suporte a contêiner está disponível em versão prévia para um subconjunto de serviços cognitivas do Azure, incluindo partes do:

* [Detector de anomalias][ad-containers]
* [Imagem Digitalizada][cv-containers]
* [Face][fa-containers]
* [Reconhecedor de formulário][fr-containers]
* [Language Understanding (LUIS)][lu-containers]
* [API de Serviço de Voz][sp-containers]
* [Análise de Texto][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Contentorização é uma abordagem para distribuição de software em que uma aplicação ou serviço, incluindo suas dependências e a configuração, é empacotado em conjunto como uma imagem de contentor. Com poucas ou nenhuma modificação, uma imagem de contentor pode ser implementada num anfitrião de contentor. Os contentores são isolados umas das outras e o sistema operacional subjacente, com requisitos de espaço mais pequenos do que uma máquina virtual. Contentores podem ser instanciados a partir de imagens de contentor para tarefas de curta duração e removidos quando já não for necessário.

Os recursos de serviços cognitivas estão disponíveis em [Microsoft Azure](https://azure.microsoft.com). Inicie sessão para o [portal do Azure](https://portal.azure.com/) para criar e explore os recursos do Azure para estes serviços.

## <a name="features-and-benefits"></a>Funcionalidades e benefícios

- **Controle sobre os dados**: Permitir que os clientes escolham onde esses serviços cognitivas processam seus dados. Isto é essencial para os clientes que não é possível enviar dados para a cloud, mas precisam de acesso à tecnologia de serviços cognitivos. Suporte a consistência em ambientes híbridos – entre dados, gerenciamento, identidade e segurança.
- **Controle sobre atualizações de modelo**: Fornecer aos clientes flexibilidade no controle de versão e atualização de modelos implantados em suas soluções.
- **Arquitetura portátil**: Habilite a criação de uma arquitetura de aplicativo portátil que pode ser implantada no Azure, no local e na borda. Contentores podem ser implementados diretamente à [serviço Kubernetes do Azure](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), ou uma [Kubernetes](https://kubernetes.io/) cluster implementado para [Azure Pilha](/azure-stack/operator). Para obter mais informações, consulte [implementar o Kubernetes no Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta taxa de transferência/baixa latência**: Forneça aos clientes a capacidade de dimensionar os requisitos de alta taxa de transferência e baixa latência, permitindo que os serviços cognitivas sejam executados fisicamente em seus dados e lógica do aplicativo. Os contentores não limite de transações por segundo (TPS) e podem ser feitos para aumentar verticalmente e horizontalmente para lidar com a pedido, se fornecer os recursos de hardware necessários. 

## <a name="containers-in-azure-cognitive-services"></a>Contentores nos serviços cognitivos do Azure

Contentores de serviços cognitivos do Azure fornecem o seguinte conjunto de contentores do Docker, cada um deles contendo um subconjunto da funcionalidade dos serviços nos serviços cognitivos do Azure:

| Serviço | Tipo de preço com suporte | Contentor | Descrição |
|---------|----------|----------|-------------|
|[Detector de anomalias][ad-containers] |F0, S0|**Detector de anomalias** |A API do detector de anomalias permite que você monitore e detecte anormalidades em seus dados de série temporal com o aprendizado de máquina.<br>[Pedir acesso](https://aka.ms/adcontainer)|
|[Imagem Digitalizada][cv-containers] |F0, S1|**Leitura** |Extrai texto impresso partir de imagens de vários objetos com diferentes superfícies e fundos, como recibos, pôsteres e cartões de visita. O contêiner de leitura também detecta *texto manuscrito* em imagens e fornece suporte a PDF/TIFF/várias páginas.<br/><br/>**Importante:** O contentor de reconhecer texto atualmente funciona apenas com o inglês.<br>[Pedir acesso](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Imagem Digitalizada][cv-containers] |F0, S1|**Reconhecer texto** |Extrai texto impresso partir de imagens de vários objetos com diferentes superfícies e fundos, como recibos, pôsteres e cartões de visita.<br/><br/>**Importante:** O contentor de reconhecer texto atualmente funciona apenas com o inglês.<br>[Pedir acesso](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Face][fa-containers] |F0, S0|**Face** |Deteta rostos humanos em imagens e identifica os atributos, incluindo pontos de referência do rosto (como noses e olhos), sexo, idade e outras funcionalidades faciais prevista de máquina. Além de deteção, Face pode verificar se dois rostos na mesma imagem ou imagens diferentes são o mesmo com uma pontuação de confiança, ou comparam rostos em relação a uma base de dados para ver se um aspeto semelhante ou idêntica face já existe. Ele também pode organizar o rostos semelhantes em grupos, com as características de visual partilhadas.<br>[Pedir acesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Reconhecedor de formulário][fr-containers] |F0, S0|**Reconhecedor de formulário** |A compreensão de formulário aplica a tecnologia de aprendizado de máquina para identificar e extrair pares de chave-valor e tabelas de formulários.<br>[Pedir acesso](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([imagem](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Carrega um modelo de compreensão de idiomas treinado ou publicado, também conhecido como uma aplicação LUIS, para um contentor do docker e fornece acesso para as previsões de consulta a partir de pontos finais de API do contentor. Pode recolher registos de consulta do contêiner e carregar esses anterior para o [portal de LUIS](https://www.luis.ai) para melhorar a exatidão da previsão da aplicação.|
|[API de Serviço de Voz][sp-containers] |F0, S0|**Conversão de voz em texto** |Transcreve voz em tempo real contínua para texto.<br>[Pedir acesso](https://aka.ms/speechcontainerspreview/)|
|[API de Serviço de Voz][sp-containers] |F0, S0|**Conversão de texto em voz** |Converte texto em voz com som natural.<br>[Pedir acesso](https://aka.ms/speechcontainerspreview/)|
|[Análise de Texto][ta-containers] |F0, S|**Extração de expressões chave** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extrai as expressões-chave para identificar os pontos principais. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”. |
|[Análise de Texto][ta-containers]|F0, S|**Deteção de idioma** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Para até 120 idiomas, Deteta que o texto de entrada é escrito em idioma e o relatório um código de idioma único para cada documento enviado no pedido. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação. |
|[Análise de Texto][ta-containers]|F0, S|**Análise de sentimentos** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analisa o texto não processado para dicas sobre o sentimento positivo ou negativo. Esta API devolve uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é o mais positivo. Os modelos de análise previamente são treinados com um corpo extenso de tecnologias de texto e de linguagem natural da Microsoft. Para os [idiomas selecionados](./text-analytics/language-support.md), a API pode analisar e classificar qualquer texto não processado que fornecer, devolvendo diretamente os resultados à aplicação de chamada. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, alguns contêineres têm suporte em chaves de recurso de oferta de serviços cognitivas [**All-in-One**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) . Você pode criar um único recurso All-in-One de serviços cognitivas e usar a mesma chave de cobrança entre os serviços com suporte para os seguintes serviços:

* Imagem Digitalizada
* Face
* LUIS
* Análise de Texto

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade de contentor nos serviços cognitivos do Azure

Contentores de serviços cognitivos do Azure estão publicamente disponíveis através da sua subscrição do Azure e imagens de contentor do Docker podem ser extraídas do registo de contentor da Microsoft ou o Docker Hub. Pode utilizar o [solicitação docker](https://docs.docker.com/engine/reference/commandline/pull/) comando para transferir uma imagem de contentor do registo adequado.

> [!IMPORTANT]
> No momento, você deve concluir um processo de inscrição para acessar os seguintes contêineres, nos quais você preenche e envia um questionário com perguntas sobre você, sua empresa e o caso de uso para o qual você deseja implementar os contêineres. Depois de receber o acesso e as credenciais fornecidas, você pode extrair as imagens de contêiner de um registro de contêiner privado hospedado pelo registro de contêiner do Azure.
> * [Detector de anomalias](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Face](Face/face-how-to-install-containers.md)
> * [Reconhecedor de formulário](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Reconhecer texto](Computer-vision/computer-vision-how-to-install-containers.md)
> * [Conversão de fala em texto e conversão de texto em fala](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores de serviços cognitivos do Azure:

**Mecanismo do Docker**: Você deve ter o mecanismo do Docker instalado localmente. Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker tem de ser configurado para dar suporte a contentores do Linux. Também podem ser implementados diretamente para contentores do docker [do Azure Kubernetes Service](../aks/index.yml) ou [Azure Container Instances](../container-instances/index.yml).

Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure.

**Familiaridade com o registro de contêiner da Microsoft e o Docker**: Você deve ter uma compreensão básica do registro de contêiner da Microsoft e dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como o conhecimento `docker` de comandos básicos.

Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).

Contentores individuais podem ter seus próprios requisitos, além disso, incluindo requisitos de alocação de memória e de servidor.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as [receitas de contêiner](containers/container-reuse-recipe.md) que você pode usar com os serviços cognitivas.

Instalar e explorar a funcionalidade fornecida pelo contentores nos serviços cognitivos do Azure:

* [Contêineres de detector de anomalias][ad-containers]
* [Contêineres de Pesquisa Visual Computacional][cv-containers]
* [Contêineres de face][fa-containers]
* [Contêineres do reconhecedor de formulário][fr-containers]
* [Contêineres de Reconhecimento vocal (LUIS)][lu-containers]
* [Contêineres da API do serviço de fala][sp-containers]
* [Contêineres de Análise de Texto][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md