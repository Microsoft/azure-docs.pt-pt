---
title: Utilizar recipientes de serviços cognitivos Azure no local
titleSuffix: Azure Cognitive Services
description: Aprenda a usar os recipientes Docker para usar os Serviços Cognitivos no local.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 12/16/2020
ms.author: aahi
keywords: no local, Docker, contentor, Kubernetes
ms.openlocfilehash: 007dfe6d67d504286b9546fe0139055b58dc700f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285608"
---
# <a name="azure-cognitive-services-containers"></a>Contentores dos Serviços Cognitivos do Azure

A Azure Cognitive Services fornece [vários recipientes Docker](https://www.docker.com/what-container) que permitem usar as mesmas APIs que estão disponíveis em Azure, no local. A utilização destes recipientes confere-lhe a flexibilidade para aproximar os Serviços Cognitivos dos seus dados por razões de conformidade, segurança ou outras razões operacionais. O suporte ao contentor está atualmente disponível para um subconjunto de Serviços Cognitivos Azure.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

A containerização é uma abordagem à distribuição de software na qual uma aplicação ou serviço, incluindo as suas dependências & configuração, é embalado em conjunto como uma imagem de recipiente. Com pouca ou nenhuma modificação, uma imagem do recipiente pode ser implantada num hospedeiro de contentores. Os contentores são isolados uns dos outros e o sistema operativo subjacente, com uma pegada menor do que uma máquina virtual. Os recipientes podem ser instantâneos a partir de imagens de contentores para tarefas de curto prazo e removidos quando já não são necessários.

## <a name="features-and-benefits"></a>Características e benefícios

- **Infraestrutura imutável**: Permitir que as equipas de DevOps aproveitem um conjunto consistente e fiável de parâmetros conhecidos do sistema, ao mesmo tempo que podem adaptar-se à mudança. Os recipientes proporcionam a flexibilidade para girar dentro de um ecossistema previsível e evitar a deriva de configuração.
- **Controlo sobre os dados**: Escolha onde os seus dados são tratados pelos Serviços Cognitivos. Isto pode ser essencial se não puder enviar dados para a nuvem, mas precisa de acesso a APIs de Serviços Cognitivos. Apoiar a consistência em ambientes híbridos – através de dados, gestão, identidade e segurança.
- **Controlo sobre atualizações de modelos**: Flexibilidade na versão e atualização dos modelos implementados nas suas soluções.
- **Arquitetura portátil**: Permite a criação de uma arquitetura de aplicações portáteis que possa ser implantada em Azure, no local e na borda. Os contentores podem ser implantados diretamente no [Serviço Azure Kubernetes](../aks/index.yml), [Instâncias de Contentores Azure](../container-instances/index.yml)ou num cluster [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](/azure-stack/operator). Para obter mais informações, consulte [Implementar Kubernetes para Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta produção / baixa latência**: Forneça aos clientes a capacidade de escalar para elevados requisitos de produção e baixa latência, permitindo que os Serviços Cognitivos corram fisicamente perto da sua lógica e dados de aplicação. Os contentores não fazem limites às transações por segundo (TPS) e podem ser feitos para escalar tanto para cima como para fora para lidar com a procura se fornecer os recursos de hardware necessários.
- **Escalabilidade**: Com a crescente popularidade do software de containerização e orquestração de contentores, como Kubernetes; a escalabilidade está na vanguarda dos avanços tecnológicos. Com base numa fundação de cluster escalável, o desenvolvimento de aplicações atende à alta disponibilidade.

## <a name="containers-in-azure-cognitive-services"></a>Contentores nos Serviços Cognitivos Azure

Os recipientes Azure Cognitive Services fornecem o seguinte conjunto de recipientes Docker, cada um dos quais contém um subconjunto de funcionalidades de serviços nos Serviços Cognitivos Azure. Pode encontrar instruções e locais de imagem nas tabelas abaixo. Uma lista de imagens de [contentores](containers/container-image-tags.md) também está disponível.

### <a name="decision-containers"></a>Contentores de decisão

| Serviço |  Contentor | Descrição | Disponibilidade |
|--|--|--|--|
| [Detetor de anomalias][ad-containers] | **Detetor de Anomalias** [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector)  | A API do Detetor de Anomalias permite-lhe monitorizar e detetar anomalias nos dados da série de tempo com aprendizagem automática. | Disponível em Geral |

### <a name="language-containers"></a>Contentores de linguagem

| Serviço |  Contentor | Descrição | Disponibilidade |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409) | Carrega um modelo de Compreensão de Linguagem treinado ou publicado, também conhecido como app LUIS, num recipiente de estivadores e fornece acesso às previsões de consulta dos pontos finais da API do contentor. Pode recolher registos de consultas a partir do contentor e enviá-los de volta para o [portal LUIS](https://www.luis.ai) para melhorar a precisão de previsão da aplicação. | Disponível em Geral |
| [Análise de Texto][ta-containers-keyphrase] | **Extração de frases-chave** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409) | Extrai frases-chave para identificar os principais pontos. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”. | Pré-visualizar |
| [Análise de Texto][ta-containers-language] |  **Deteção de linguagem de texto** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409) | Para até 120 línguas, deteta em que língua o texto de entrada está escrito e reporta um código linguístico único para cada documento submetido no pedido. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação. | Pré-visualizar |
| [Análise de Texto][ta-containers-sentiment] | **Análise de Sentimento v3** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409) | Analisa texto cru para pistas sobre sentimento positivo ou negativo. Esta versão da análise de sentimento devolve rótulos de sentimento (por *exemplo, positivos* ou *negativos)* para cada documento e frase dentro dele. |  Disponível em Geral |
| [Análise de Texto][ta-containers-health] |  **Análise de Texto para a saúde** | Extrair e rotular informações médicas de texto clínico não estruturado. | Pré-visualização fechada. [Solicitar acesso][request-access]. |

### <a name="speech-containers"></a>Contentores de voz

> [!NOTE]
> Para utilizar os recipientes de discurso, terá de preencher um [formulário de pedido on-line](https://aka.ms/csgate).

| Serviço |  Contentor | Descrição | Disponibilidade |
|--|--|--|
| [API de Serviço de Voz][sp-containers-stt] |  **Discurso-a-texto** [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text) | Transcreve voz em tempo real contínua para texto. | Disponível em Geral |
| [API de Serviço de Voz][sp-containers-cstt] | **Discurso-a-texto personalizado** [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text) | Transcreve a fala contínua em tempo real em texto usando um modelo personalizado. | Disponível em Geral |
| [API de Serviço de Voz][sp-containers-tts] | **Texto-a-discurso** [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech) | Converte texto em voz com som natural. | Disponível em Geral |
| [API de Serviço de Voz][sp-containers-ctts] | **Texto-a-discurso personalizado** [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech) | Converte o texto em discurso sonoro natural usando um modelo personalizado. | Pré-visualização fechada |
| [API de Serviço de Voz][sp-containers-ntts] | **Texto-a-discurso neural** [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech) | Converte o texto em discurso sonoro natural usando a tecnologia de rede neural profunda, permitindo um discurso mais natural sintetizado. | Disponível em Geral |
| [API de Serviço de Voz][sp-containers-lid] | **Deteção da linguagem da** fala [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection) | Determina a linguagem do áudio falado. | Pré-visualização fechada |

### <a name="vision-containers"></a>Contentores de imagem

> [!WARNING]
> A 11 de junho de 2020, a Microsoft anunciou que não irá vender tecnologia de reconhecimento facial a esquadras da polícia nos Estados Unidos até à implementação de uma regulamentação rígida assente nos direitos humanos. Como tal, os clientes não podem utilizar funcionalidades ou funcionalidades de reconhecimento facial incluídas nos Serviços Azure, como o Face ou o Video Indexer, se um cliente estiver, ou estiver a permitir o uso de tais serviços por ou para, um departamento de polícia nos Estados Unidos.

| Serviço |  Contentor | Descrição | Disponibilidade |
|--|--|--|--|
| [Imagem Digitalizada][cv-containers] | **Ler OCR** [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read) | O recipiente Read OCR permite extrair texto impresso e manuscrito de imagens e documentos com suporte para formatos de ficheiro JPEG, PNG, BMP, PDF e TIFF. Para mais informações, consulte a documentação da [API de leitura.](./computer-vision/overview-ocr.md) | Pré-visualização fechada. [Solicitar acesso][request-access]. |
| [Análise Espacial][spa-containers] | **Análise espacial** [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis) | Analisa o streaming de vídeo em tempo real para entender as relações espaciais entre as pessoas, o seu movimento e interações com objetos em ambientes físicos. | Pré-visualização fechada. [Solicitar acesso][request-access]. |
| [Face][fa-containers] | **Face** | Deteta rostos humanos em imagens e identifica atributos, incluindo marcos faciais (como narizes e olhos), sexo, idade e outras características faciais previstas pela máquina. Além da deteção, o Face pode verificar se duas faces na mesma imagem ou imagens diferentes são as mesmas usando uma pontuação de confiança, ou comparar rostos com uma base de dados para ver se já existe um rosto semelhante ou idêntico. Também pode organizar rostos semelhantes em grupos, usando traços visuais partilhados. | Indisponível |
| [Reconhecedor de formulários][fr-containers] | **Reconhecedor de Formato** | A Form Understanding aplica tecnologia de aprendizagem automática para identificar e extrair pares e tabelas de valor-chave a partir de formulários. | Indisponível | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, alguns contentores são suportados na oferta [de recursos multi-serviços](cognitive-services-apis-create-account.md) dos Serviços Cognitivos. Pode criar um único recurso De Serviços Cognitivos All-In-One e utilizar a mesma chave de faturação através de serviços suportados para os seguintes serviços:

* Imagem Digitalizada
* Face
* LUIS
* Análise de Texto

## <a name="prerequisites"></a>Pré-requisitos

Deve satisfazer os seguintes pré-requisitos antes de utilizar os recipientes dos Serviços Cognitivos Azure:

**Docker Engine**: Deve ter o Motor Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente Docker no [macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)e [Windows.](https://docs.docker.com/docker-for-windows/) No Windows, o Docker tem de ser configurado para suportar recipientes Linux. Os contentores docker também podem ser implantados diretamente para o [Serviço Azure Kubernetes](../aks/index.yml) ou [instâncias de contentores Azure](../container-instances/index.yml).

O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure.

**Familiaridade com o Microsoft Container Registry e Docker**: Deve ter uma compreensão básica tanto dos conceitos microsoft container registry como docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de `docker` comandos básicos.

Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).

Os recipientes individuais também podem ter os seus próprios requisitos, incluindo os requisitos de atribuição de servidores e memória.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Exemplos de programador

As amostras de desenvolvedores estão disponíveis no nosso [repositório GitHub.](https://github.com/Azure-Samples/cognitive-services-containers-samples)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as receitas de recipientes](containers/container-reuse-recipe.md) que pode utilizar com os Serviços Cognitivos.

Instale e explore a funcionalidade fornecida pelos contentores nos Serviços Cognitivos Azure:

* [Recipientes de detetor de anomalias][ad-containers]
* [Recipientes de Visão Computacional][cv-containers]
* [Recipientes faciais][fa-containers]
* [Recipientes de reconhecimento de formulários][fr-containers]
* [Recipientes de Compreensão linguística (LUIS)][lu-containers]
* [Recipientes API do Serviço de Fala][sp-containers]
* [Recipientes de análise de texto][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u