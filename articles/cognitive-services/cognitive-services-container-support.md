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
ms.date: 01/10/2020
ms.author: dapine
ms.openlocfilehash: d75962b98543991a065f6b165279215614175925
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390821"
---
# <a name="container-support-in-azure-cognitive-services"></a>Suporte para contentores nos serviços cognitivos do Azure

O suporte de contentores nos Serviços Cognitivos Azure permite que os desenvolvedores utilizem as mesmas APIs ricas que estão disponíveis no Azure, e permite flexibilidade para onde implantar e acolher os serviços que vêm com [contentores Docker.](https://www.docker.com/what-container) O suporte do contentor está atualmente disponível para um subconjunto de Serviços Cognitivos Azure, incluindo partes de:

> [!div class="checklist"]
> * [Detetor de Anomalias][ad-containers]
> * [Imagem Digitalizada][cv-containers]
> * [Rosto][fa-containers]
> * [Reconhecimento de Formulários][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [API de Serviço de Voz][sp-containers]
> * [Análise de Texto][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Contentorização é uma abordagem para distribuição de software em que uma aplicação ou serviço, incluindo suas dependências e a configuração, é empacotado em conjunto como uma imagem de contentor. Com poucas ou nenhuma modificação, uma imagem de contentor pode ser implementada num anfitrião de contentor. Os contentores são isolados umas das outras e o sistema operacional subjacente, com requisitos de espaço mais pequenos do que uma máquina virtual. Contentores podem ser instanciados a partir de imagens de contentor para tarefas de curta duração e removidos quando já não for necessário.

Os recursos dos Serviços Cognitivos estão disponíveis no [Microsoft Azure.](https://azure.microsoft.com) Assine no [portal Azure](https://portal.azure.com/) para criar e explorar os recursos do Azure para estes serviços.

## <a name="features-and-benefits"></a>Funcionalidades e benefícios

- **Infraestrutura imutável**: Permitir que as equipas de DevOps aproveitem um conjunto consistente e fiável de parâmetros conhecidos do sistema, ao mesmo tempo que conseguem adaptar-se à mudança. Os recipientes proporcionam a flexibilidade para girar dentro de um ecossistema previsível e evitar a deriva de configuração.
- **Controlo sobre os dados**: Permitir que os clientes escolham onde estes Serviços Cognitivos processam os seus dados. Isto é essencial para os clientes que não é possível enviar dados para a cloud, mas precisam de acesso à tecnologia de serviços cognitivos. Apoiar a consistência em ambientes híbridos – através de dados, gestão, identidade e segurança.
- **Controlo sobre as atualizações**de modelos : Proporcionar aos clientes flexibilidade na versão e atualização dos modelos implementados nas suas soluções.
- **Arquitetura portátil**: Permitir a criação de uma arquitetura de aplicação portátil que pode ser implantada em Azure, no local e na borda. Os contentores podem ser implantados diretamente para o [Serviço Azure Kubernetes,](../aks/index.yml)instâncias de [contentores Azure,](../container-instances/index.yml)ou para um cluster [Kubernetes](https://kubernetes.io/) implantado para [a Stack Azure](/azure-stack/operator). Para mais informações, consulte A utilização de [Kubernetes para O Stack Azure](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta frequência /baixa latência**: Fornecer aos clientes a capacidade de escalar para requisitos de alta frequência e baixa latência, permitindo que os Serviços Cognitivos corram fisicamente perto da sua lógica de aplicação e dados. Os contentores não limite de transações por segundo (TPS) e podem ser feitos para aumentar verticalmente e horizontalmente para lidar com a pedido, se fornecer os recursos de hardware necessários.
- **Escalabilidade**: Com a popularidade cada vez maior do software de containerização e orquestração de contentores, como kubernetes; a escalabilidade está na vanguarda dos avanços tecnológicos. Com base numa fundação de cluster escalável, o desenvolvimento de aplicações atende à elevada disponibilidade.

## <a name="containers-in-azure-cognitive-services"></a>Contentores nos serviços cognitivos do Azure

Contentores de serviços cognitivos do Azure fornecem o seguinte conjunto de contentores do Docker, cada um deles contendo um subconjunto da funcionalidade dos serviços nos serviços cognitivos do Azure:

| Serviço | Nível de Preços Suportados | Contentor | Descrição |
|---------|----------|----------|-------------|
|[Detetor de anomalias][ad-containers] |F0, S0|**Detetor de anomalias** |A API do Detetor de Anomalias permite-lhe monitorizar e detetar anomalias nos dados da série temporal com machine learning.<br>[Solicitar acesso](https://aka.ms/adcontainer)|
|[Imagem Digitalizada][cv-containers] |F0, S1|**Leitura** |Extrai texto impresso partir de imagens de vários objetos com diferentes superfícies e fundos, como recibos, pôsteres e cartões de visita. O recipiente De leitura também deteta *texto manuscrito* em imagens e fornece suporte PDF/TIFF/multi-página.<br/><br/>**Importante:** O recipiente De Leitura funciona atualmente apenas com inglês.|
|[Rosto][fa-containers] |F0, S0|**Rosto** |Deteta rostos humanos em imagens e identifica os atributos, incluindo pontos de referência do rosto (como noses e olhos), sexo, idade e outras funcionalidades faciais prevista de máquina. Além de deteção, Face pode verificar se dois rostos na mesma imagem ou imagens diferentes são o mesmo com uma pontuação de confiança, ou comparam rostos em relação a uma base de dados para ver se um aspeto semelhante ou idêntica face já existe. Ele também pode organizar o rostos semelhantes em grupos, com as características de visual partilhadas.<br>[Solicitar acesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Reconhecimento de formulários][fr-containers] |F0, S0|**Reconhecimento de Formulários** |A Forma Compreensão aplica tecnologia de machine learning para identificar e extrair pares e tabelas de valor-chave a partir de formulários.<br>[Solicitar acesso](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)|Carrega um modelo de compreensão de idiomas treinado ou publicado, também conhecido como uma aplicação LUIS, para um contentor do docker e fornece acesso para as previsões de consulta a partir de pontos finais de API do contentor. Pode recolher registos de consulta do recipiente e carregá-los de volta para o [portal LUIS](https://www.luis.ai) para melhorar a precisão de previsão da aplicação.|
|[API de Serviço de Voz][sp-containers-stt] |F0, S0|**Conversão de voz em texto** |Transcreve voz em tempo real contínua para texto.|
|[API de Serviço de Voz][sp-containers-cstt] |F0, S0|**Discurso personalizado a texto** |Transcreve o discurso contínuo em tempo real em texto usando um modelo personalizado.|
|[API de Serviço de Voz][sp-containers-tts] |F0, S0|**Conversão de texto em voz** |Converte texto em voz com som natural.|
|[API de Serviço de Voz][sp-containers-ctts] |F0, S0|**Texto-a-fala personalizado** |Converte o texto em discurso de som natural usando um modelo personalizado.|
|[Análise de Texto][ta-containers-keyphrase] |F0, S|**Extração de frases-chave** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409) |Extrai as expressões-chave para identificar os pontos principais. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”. |
|[Análise de Texto][ta-containers-language]|F0, S|**Deteção de Linguagem** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409) |Para até 120 idiomas, Deteta que o texto de entrada é escrito em idioma e o relatório um código de idioma único para cada documento enviado no pedido. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação. |
|[Análise de Texto][ta-containers-sentiment]|F0, S|**Análise de Sentimentos** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409) |Analisa o texto não processado para dicas sobre o sentimento positivo ou negativo. Esta API devolve uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é o mais positivo. Os modelos de análise previamente são treinados com um corpo extenso de tecnologias de texto e de linguagem natural da Microsoft. Para os [idiomas selecionados](./text-analytics/language-support.md), a API pode analisar e classificar qualquer texto não processado que fornecer, devolvendo diretamente os resultados à aplicação de chamada. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, alguns recipientes são suportados em Serviços Cognitivos [**All-In-One oferecendo**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) chaves de recursos. Você pode criar um único recurso De Serviços Cognitivos All-In-One e usar a mesma chave de faturação em serviços suportados para os seguintes serviços:

* Imagem Digitalizada
* Rostos
* LUIS
* Análise de Texto

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade de contentor nos serviços cognitivos do Azure

Contentores de serviços cognitivos do Azure estão publicamente disponíveis através da sua subscrição do Azure e imagens de contentor do Docker podem ser extraídas do registo de contentor da Microsoft ou o Docker Hub. Pode utilizar o comando de puxar pelo [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do registo apropriado.

> [!IMPORTANT]
> Atualmente, tem de concluir um processo de inscrição para aceder aos seguintes contentores, no qual preenche e submete um questionário com perguntas sobre si, a sua empresa e o caso de utilização para o qual pretende implementar os contentores. Assim que lhe for concedido acesso e credenciais fornecidas, pode então retirar as imagens do contentor de um registo de contentores privados hospedado pelo Registo de Contentores Azure.
> * [Detetor de anomalias](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Rosto](Face/face-how-to-install-containers.md)
> * [Reconhecimento de Formulários](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Leitura](computer-vision/computer-vision-how-to-install-containers.md)
> * [Discurso-a-texto e Texto-a-fala](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores de serviços cognitivos do Azure:

**Motor Docker**: Você deve ter o Motor Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente Docker no [macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker tem de ser configurado para dar suporte a contentores do Linux. Os recipientes de estivadores também podem ser implantados diretamente para o [Serviço Azure Kubernetes](../aks/index.yml) ou para as instâncias de [contentores Azure](../container-instances/index.yml).

Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure.

**Familiaridade com o Microsoft Container Registry e Docker**: Você deve ter uma compreensão básica tanto dos conceitos de Registo de Contentores da Microsoft como do Docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de comandos básicos de `docker`.

Para um primer sobre o Docker e o básico do contentor, consulte a visão geral do [Docker.](https://docs.docker.com/engine/docker-overview/)

Contentores individuais podem ter seus próprios requisitos, além disso, incluindo requisitos de alocação de memória e de servidor.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as receitas](containers/container-reuse-recipe.md) de recipientes que pode usar com os Serviços Cognitivos.

Instalar e explorar a funcionalidade fornecida pelo contentores nos serviços cognitivos do Azure:

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