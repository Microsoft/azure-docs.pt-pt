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
ms.date: 10/22/2020
ms.author: aahi
keywords: no local, Docker, contentor, Kubernetes
ms.openlocfilehash: e6a01192068617dcdb52e6160ffcf633d099bdf1
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677438"
---
# <a name="azure-cognitive-services-containers"></a>Contentores dos Serviços Cognitivos Azure

> [!WARNING]
> A 11 de junho de 2020, a Microsoft anunciou que não irá vender tecnologia de reconhecimento facial a esquadras da polícia nos Estados Unidos até à implementação de uma regulamentação rígida assente nos direitos humanos. Como tal, os clientes não podem utilizar funcionalidades ou funcionalidades de reconhecimento facial incluídas nos Serviços Azure, como o Face ou o Video Indexer, se um cliente estiver, ou estiver a permitir o uso de tais serviços por ou para, um departamento de polícia nos Estados Unidos.

A Azure Cognitive Services fornece [vários recipientes Docker](https://www.docker.com/what-container) que permitem usar as mesmas APIs que estão disponíveis em Azure, no local. A utilização destes recipientes confere-lhe a flexibilidade para aproximar os Serviços Cognitivos dos seus dados por razões de conformidade, segurança ou outras razões operacionais. 

O suporte ao contentor está atualmente disponível para um subconjunto de Serviços Cognitivos Azure, incluindo partes de:

> [!div class="checklist"]
> * [Detetor de Anomalias][ad-containers]
> * [Ler OCR (Reconhecimento de Caracteres Óticos) ][cv-containers]
> * [Análise espacial][spa-containers]
> * [Face][fa-containers]
> * [Reconhecedor de Formato][fr-containers]
> * [Compreensão de Idiomas (LUIS)][lu-containers]
> * [API de Serviço de Voz][sp-containers]
> * [Análise de Texto][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

A containerização é uma abordagem à distribuição de software na qual uma aplicação ou serviço, incluindo as suas dependências & configuração, é embalado em conjunto como uma imagem de recipiente. Com pouca ou nenhuma modificação, uma imagem do recipiente pode ser implantada num hospedeiro de contentores. Os contentores são isolados uns dos outros e o sistema operativo subjacente, com uma pegada menor do que uma máquina virtual. Os recipientes podem ser instantâneos a partir de imagens de contentores para tarefas de curto prazo e removidos quando já não são necessários.

Os recursos dos Serviços Cognitivos estão disponíveis no [Microsoft Azure.](https://azure.microsoft.com) Inscreva-se no [portal Azure](https://portal.azure.com/) para criar e explorar recursos Azure para estes serviços.

## <a name="features-and-benefits"></a>Características e benefícios

- **Infraestrutura imutável** : Permitir que as equipas de DevOps aproveitem um conjunto consistente e fiável de parâmetros conhecidos do sistema, ao mesmo tempo que podem adaptar-se à mudança. Os recipientes proporcionam a flexibilidade para girar dentro de um ecossistema previsível e evitar a deriva de configuração.
- **Controlo sobre os dados** : Escolha onde os seus dados são tratados pelos Serviços Cognitivos. Isto pode ser essencial se não puder enviar dados para a nuvem, mas precisa de acesso a APIs de Serviços Cognitivos. Apoiar a consistência em ambientes híbridos – através de dados, gestão, identidade e segurança.
- **Controlo sobre atualizações de modelos** : Flexibilidade na versão e atualização dos modelos implementados nas suas soluções.
- **Arquitetura portátil** : Permite a criação de uma arquitetura de aplicações portáteis que possa ser implantada em Azure, no local e na borda. Os contentores podem ser implantados diretamente no [Serviço Azure Kubernetes](../aks/index.yml), [Instâncias de Contentores Azure](../container-instances/index.yml)ou num cluster [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](/azure-stack/operator). Para obter mais informações, consulte [Implementar Kubernetes para Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta produção / baixa latência** : Forneça aos clientes a capacidade de escalar para elevados requisitos de produção e baixa latência, permitindo que os Serviços Cognitivos corram fisicamente perto da sua lógica e dados de aplicação. Os contentores não fazem limites às transações por segundo (TPS) e podem ser feitos para escalar tanto para cima como para fora para lidar com a procura se fornecer os recursos de hardware necessários.
- **Escalabilidade** : Com a crescente popularidade do software de containerização e orquestração de contentores, como Kubernetes; a escalabilidade está na vanguarda dos avanços tecnológicos. Com base numa fundação de cluster escalável, o desenvolvimento de aplicações atende à alta disponibilidade.

## <a name="containers-in-azure-cognitive-services"></a>Contentores nos Serviços Cognitivos Azure

Os recipientes Azure Cognitive Services fornecem o seguinte conjunto de recipientes Docker, cada um dos quais contém um subconjunto de funcionalidades de serviços nos Serviços Cognitivos Azure:

| Serviço | Nível de Preços Suportados | Contentor | Descrição |
|--|--|--|--|
| [Detetor de anomalias][ad-containers] | F0, S0 | **Detetor de Anomalias** [(imagem)](https://hub.docker.com/_/azure-cognitive-services-decision-anomaly-detector)  | A API do Detetor de Anomalias permite-lhe monitorizar e detetar anomalias nos dados da série de tempo com aprendizagem automática.<br>[Pedir acesso][request-access] |
| [Imagem Digitalizada][cv-containers] | F0, S1 | **Ler** OCR [(imagem)](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read) | O recipiente Read OCR permite extrair texto impresso e manuscrito de imagens e documentos com suporte para formatos de ficheiro JPEG, PNG, BMP, PDF e TIFF. Para mais informações, consulte a documentação da [API de leitura.](./computer-vision/concept-recognizing-text.md)<br>[Pedir acesso][request-access] |
| [Face][fa-containers] | F0, S0 | **Face** | Deteta rostos humanos em imagens e identifica atributos, incluindo marcos faciais (como narizes e olhos), sexo, idade e outras características faciais previstas pela máquina. Além da deteção, o Face pode verificar se duas faces na mesma imagem ou imagens diferentes são as mesmas usando uma pontuação de confiança, ou comparar rostos com uma base de dados para ver se já existe um rosto semelhante ou idêntico. Também pode organizar rostos semelhantes em grupos, usando traços visuais partilhados. |
| [Reconhecedor de formulários][fr-containers] | F0, S0 | **Reconhecedor de Formato** | A Form Understanding aplica tecnologia de aprendizagem automática para identificar e extrair pares e tabelas de valor-chave a partir de formulários. |
| [LUIS][lu-containers] | F0, S0 | **LUIS** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409) | Carrega um modelo de Compreensão de Linguagem treinado ou publicado, também conhecido como app LUIS, num recipiente de estivadores e fornece acesso às previsões de consulta dos pontos finais da API do contentor. Pode recolher registos de consultas a partir do contentor e enviá-los de volta para o [portal LUIS](https://www.luis.ai) para melhorar a precisão de previsão da aplicação. |
| [API de Serviço de Voz][sp-containers-stt] | F0, S0 | **Discurso-a-texto** [(imagem)](https://hub.docker.com/_/azure-cognitive-services-speechservices-speech-to-text) | Transcreve voz em tempo real contínua para texto. |
| [API de Serviço de Voz][sp-containers-cstt] | F0, S0 | **Discurso-a-texto personalizado** [(imagem)](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-speech-to-text) | Transcreve a fala contínua em tempo real em texto usando um modelo personalizado. |
| [API de Serviço de Voz][sp-containers-tts] | F0, S0 | **Texto-a-discurso** [(imagem)](https://hub.docker.com/_/azure-cognitive-services-speechservices-text-to-speech) | Converte texto em voz com som natural. |
| [API de Serviço de Voz][sp-containers-ctts] | F0, S0 | **Texto-a-discurso personalizado** [(imagem)](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-text-to-speech) | Converte o texto em discurso sonoro natural usando um modelo personalizado. |
| [API de Serviço de Voz][sp-containers-ntts] | F0, S0 | **Texto-a-discurso neural** [(imagem)](https://hub.docker.com/_/azure-cognitive-services-speechservices-neural-text-to-speech) | Converte o texto em discurso sonoro natural usando a tecnologia de rede neural profunda, permitindo um discurso mais natural sintetizado. |
| [Análise de Texto][ta-containers-keyphrase] | F0, S | **Extração de frases-chave** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409) | Extrai frases-chave para identificar os principais pontos. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”. |
| [Análise de Texto][ta-containers-language] | F0, S | **Deteção de Idiomas** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409) | Para até 120 línguas, deteta em que língua o texto de entrada está escrito e reporta um código linguístico único para cada documento submetido no pedido. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação. |
| [Análise de Texto][ta-containers-sentiment] | F0, S | **Análise de Sentimento v3** [(imagem)](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409) | Analisa texto cru para pistas sobre sentimento positivo ou negativo. Esta versão da análise de sentimento devolve rótulos de sentimento (por *exemplo, positivos* ou *negativos)* para cada documento e frase dentro dele. |
| [Análise de Texto][ta-containers-health] | F0, S | **Análise de texto para a saúde** | Extrair e rotular informações médicas de texto clínico não estruturado. |
| [Análise Espacial][spa-containers] | S0 | **Análise espacial** | Analisa o streaming de vídeo em tempo real para entender as relações espaciais entre as pessoas, o seu movimento e interações com objetos em ambientes físicos. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, alguns recipientes são suportados em Serviços Cognitivos [**All-In-One oferecendo**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) chaves de recursos. Pode criar um único recurso De Serviços Cognitivos All-In-One e utilizar a mesma chave de faturação através de serviços suportados para os seguintes serviços:

* Imagem Digitalizada
* Face
* LUIS
* Análise de Texto

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade de contentores nos Serviços Cognitivos Azure

Os contentores Azure Cognitive Services estão disponíveis publicamente através da sua subscrição Azure, e as imagens do contentor Docker podem ser retiradas do Registo de Contentores da Microsoft ou do Docker Hub. Pode utilizar o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem do contentor do registo apropriado.

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Pré-requisitos

Deve satisfazer os seguintes pré-requisitos antes de utilizar os recipientes dos Serviços Cognitivos Azure:

**Docker Engine** : Deve ter o Motor Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente Docker no [macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)e [Windows.](https://docs.docker.com/docker-for-windows/) No Windows, o Docker tem de ser configurado para suportar recipientes Linux. Os contentores docker também podem ser implantados diretamente para o [Serviço Azure Kubernetes](../aks/index.yml) ou [instâncias de contentores Azure](../container-instances/index.yml).

O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure.

**Familiaridade com o Microsoft Container Registry e Docker** : Deve ter uma compreensão básica tanto dos conceitos microsoft container registry como docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de `docker` comandos básicos.

Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).

Os recipientes individuais também podem ter os seus próprios requisitos, incluindo os requisitos de atribuição de servidores e memória.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

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
[spa-containers]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container
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
