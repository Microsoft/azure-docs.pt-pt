---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como contentores do Docker podem obter o mais perto dos serviços cognitivos aos seus dados.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: a60013bce63ed234e15dfddb13c07fbdc33a4073
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339628"
---
# <a name="container-support-in-azure-cognitive-services"></a>Suporte para contentores nos serviços cognitivos do Azure

Suporte para contentores nos serviços cognitivos do Azure permite aos desenvolvedores usar as mesmas APIs avançadas estão disponíveis no Azure e permite flexibilidade no onde implementar e hospedar os serviços que acompanham [contentores do Docker](https://www.docker.com/what-container). Suporte do contentor está atualmente disponível em pré-visualização para um subconjunto de serviços cognitivos do Azure, incluindo partes da [de imagem digitalizada](Computer-vision/Home.md), [Face](Face/Overview.md), [análise de texto](text-analytics/overview.md)e [ Compreensão de idiomas](LUIS/luis-container-howto.md) (LUIS).

Contentorização é uma abordagem para distribuição de software em que uma aplicação ou serviço, incluindo suas dependências e a configuração, é empacotado em conjunto como uma imagem de contentor. Com poucas ou nenhuma modificação, uma imagem de contentor pode ser implementada num anfitrião de contentor. Os contentores são isolados umas das outras e o sistema operacional subjacente, com requisitos de espaço mais pequenos do que uma máquina virtual. Contentores podem ser instanciados a partir de imagens de contentor para tarefas de curta duração e removidos quando já não for necessário.

O vídeo seguinte demonstra como utilizar um contentor de serviços cognitivos.

[![Demonstração de contentor para os serviços cognitivos](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

O [de imagem digitalizada](Computer-vision/Home.md), [Face](Face/Overview.md), [análise de texto](text-analytics/overview.md), e [compreensão de idiomas (LUIS)](LUIS/what-is-luis.md) serviços estão disponíveis na [Microsoft Azure](https://azure.microsoft.com). Inicie sessão para o [portal do Azure](https://portal.azure.com/) para criar e explore os recursos do Azure para estes serviços.

## <a name="features-and-benefits"></a>Funcionalidades e benefícios

- **Controlo sobre os dados**: Permita aos clientes escolherem onde estes serviços cognitivos processa os dados. Isto é essencial para os clientes que não é possível enviar dados para a cloud, mas precisam de acesso à tecnologia de serviços cognitivos. Suporte consistência em ambientes híbridos – em dados, gestão, identidade e segurança.
- **Controle sobre atualizações do modelo**: Fornece aos clientes flexibilidade no controlo de versões e a atualização dos modelos implementados em suas soluções.
- **Arquitetura portátil**: Ative a criação de uma arquitetura de aplicativos portáteis que pode ser implementada no Azure, no local e o limite. Contentores podem ser implementados diretamente à [serviço Kubernetes do Azure](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), ou uma [Kubernetes](https://kubernetes.io/) cluster implementado para [Azure Pilha](../azure-stack/index.yml). Para obter mais informações, consulte [implementar o Kubernetes no Azure Stack](../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).
- **Alto débito / baixa latência**: Fornece aos clientes a capacidade de dimensionar para um débito elevado e requisitos de baixa latência, permitindo que os serviços cognitivos executar fisicamente perto dos seus dados e lógica do aplicativo. Os contentores não limite de transações por segundo (TPS) e podem ser feitos para aumentar verticalmente e horizontalmente para lidar com a pedido, se fornecer os recursos de hardware necessários. 


## <a name="containers-in-azure-cognitive-services"></a>Contentores nos serviços cognitivos do Azure

Contentores de serviços cognitivos do Azure fornecem o seguinte conjunto de contentores do Docker, cada um deles contendo um subconjunto da funcionalidade dos serviços nos serviços cognitivos do Azure:

| Serviço | Contentor| Descrição |
|---------|----------|-------------|
|[Imagem Digitalizada](Computer-vision/computer-vision-how-to-install-containers.md) |**Reconhecer texto** |Extrai texto impresso partir de imagens de vários objetos com diferentes superfícies e fundos, como recibos, pôsteres e cartões de visita.<br/><br/>**Importante:** O contentor de reconhecer texto atualmente funciona apenas com o inglês.<br>[Pedir acesso](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Face](Face/face-how-to-install-containers.md) |**Face** |Deteta rostos humanos em imagens e identifica os atributos, incluindo pontos de referência do rosto (como noses e olhos), sexo, idade e outras funcionalidades faciais prevista de máquina. Além de deteção, Face pode verificar se dois rostos na mesma imagem ou imagens diferentes são o mesmo com uma pontuação de confiança, ou comparam rostos em relação a uma base de dados para ver se um aspeto semelhante ou idêntica face já existe. Ele também pode organizar o rostos semelhantes em grupos, com as características de visual partilhadas.<br>[Pedir acesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[LUIS](LUIS/luis-container-howto.md) |**LUIS** ([imagem](https://go.microsoft.com/fwlink/?linkid=2043204))|Carrega um modelo de compreensão de idiomas treinado ou publicado, também conhecido como uma aplicação LUIS, para um contentor do docker e fornece acesso para as previsões de consulta a partir de pontos finais de API do contentor. Pode recolher registos de consulta do contêiner e carregar esses anterior para o [portal de LUIS](https://www.luis.ai) para melhorar a exatidão da previsão da aplicação.|
|[Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |**Extração de expressões chave** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018757)) |Extrai as expressões-chave para identificar os pontos principais. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”. |
|[Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Deteção de idioma** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018759)) |Para até 120 idiomas, Deteta que o texto de entrada é escrito em idioma e o relatório um código de idioma único para cada documento enviado no pedido. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação. |
|[Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Análise de sentimentos** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018654)) |Analisa o texto não processado para dicas sobre o sentimento positivo ou negativo. Esta API devolve uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é o mais positivo. Os modelos de análise previamente são treinados com um corpo extenso de tecnologias de texto e de linguagem natural da Microsoft. Para os [idiomas selecionados](./text-analytics/language-support.md), a API pode analisar e classificar qualquer texto não processado que fornecer, devolvendo diretamente os resultados à aplicação de chamada. |

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade de contentor nos serviços cognitivos do Azure

Contentores de serviços cognitivos do Azure estão publicamente disponíveis através da sua subscrição do Azure e imagens de contentor do Docker podem ser extraídas do registo de contentor da Microsoft ou o Docker Hub. Pode utilizar o [solicitação docker](https://docs.docker.com/engine/reference/commandline/pull/) comando para transferir uma imagem de contentor do registo adequado.

> [!IMPORTANT]
> Atualmente, tem de concluir um processo de inscrição para aceder a [Face](Face/face-how-to-install-containers.md) e [reconhecer texto](Computer-vision/computer-vision-how-to-install-containers.md) contentores, em que preencha e submeta um questionário com perguntas sobre si, sua empresa e o caso de utilização para o qual pretende implementar os contentores. Assim que estiver concedido o acesso e fornecidas credenciais, em seguida, pode tirar as imagens de contentor para os contentores de rostos e reconhecer texto de registo de contentor privado alojado pelo Azure Container Registry.

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores de serviços cognitivos do Azure:

**Motor do docker**: Tem de ter o motor do Docker instalado localmente. Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker tem de ser configurado para dar suporte a contentores do Linux. Também podem ser implementados diretamente para contentores do docker [do Azure Kubernetes Service](../aks/index.yml) ou [Azure Container Instances](../container-instances/index.yml).

Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure.

**Familiaridade com o registo de contentor da Microsoft e a Docker**: Deve ter um conhecimento básico dos conceitos de registo de contentor da Microsoft e a Docker, como registos, repositórios, contentores e imagens de contentor, bem como dados de conhecimento do basic `docker` comandos.

Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).

Contentores individuais podem ter seus próprios requisitos, além disso, incluindo requisitos de alocação de memória e de servidor.

## <a name="developer-samples"></a>Exemplos de programador

Exemplos de programador estão disponíveis em nosso [repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Passos Seguintes

Instalar e explorar a funcionalidade fornecida pelo contentores nos serviços cognitivos do Azure:

* [Instalar e utilizar contentores de imagem digitalizada](Computer-vision/computer-vision-how-to-install-containers.md)
* [Instalar e utilizar contentores de rostos](Face/face-how-to-install-containers.md)
* [Instalar e utilizar contentores de análise de texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md)
* [Instalar e utilizar contentores de compreensão de idiomas (LUIS)](LUIS/luis-container-howto.md)
