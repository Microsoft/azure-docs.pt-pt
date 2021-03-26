---
title: O que são os Serviços Cognitivos Azure?
titleSuffix: Azure Cognitive Services
description: Os Serviços Cognitivos tornam a IA acessível a todos os desenvolvedores sem necessitar de aprendizagem automática e conhecimentos de ciência de dados. Basta fazer uma chamada da API a partir da sua aplicação para adicionar a capacidade de ver (pesquisa e reconhecimento de imagem avançada), ouvir, falar, pesquisar e tomar decisões nas suas apps.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: serviços cognitivos, inteligência cognitiva, soluções cognitivas, serviços ai, compreensão cognitiva, características cognitivas
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 71f8635d1cd96a6436cfc902622bf18bc608a143
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867190"
---
# <a name="what-are-azure-cognitive-services"></a>O que são os Serviços Cognitivos Azure?

Os Azure Cognitive Services são serviços baseados na nuvem com APIs REST e SDKs da biblioteca de clientes disponíveis para ajudá-lo a construir inteligência cognitiva nas suas aplicações. Pode adicionar funcionalidades cognitivas às suas aplicações sem ter inteligência artificial (IA) ou competências em ciência de dados. Os Serviços Cognitivos Azure compreendem vários serviços de IA que lhe permitem construir soluções cognitivas que possam ver, ouvir, falar, compreender e até tomar decisões.

## <a name="categories-of-cognitive-services"></a>Categorias de Serviços Cognitivos

O catálogo de serviços cognitivos que proporcionam compreensão cognitiva são categorizados em cinco pilares principais:

* Visão
* Voz
* Idioma
* Decisão
* Pesquisar

As seguintes secções deste artigo fornecem uma lista de serviços que fazem parte destes cinco pilares.

## <a name="vision-apis"></a>APIs de Visão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Imagem Digitalizada](./computer-vision/index.yml "Imagem Digitalizada")|O serviço De Visão Computacional fornece-lhe acesso a algoritmos cognitivos avançados para processar imagens e devolver informações. Consulte [o arranque rápido da Visão de Computador](./computer-vision/quickstarts-sdk/client-library.md) para começar com o serviço.|
|[Serviço de Imagem Personalizada](./custom-vision-service/index.yml "Serviço de Imagem Personalizada")|O Serviço de Visão Personalizada permite-lhe construir, implementar e melhorar os seus próprios classificadores de imagem. Um classificador de imagem é um serviço de IA que aplica rótulos a imagens, com base nas suas características visuais. |
|[Face](./face/index.yml "Rostos")| O serviço Face fornece acesso a algoritmos faciais avançados, permitindo a deteção e reconhecimento de atributos faciais. Consulte [o "Face quickstart"](./face/quickstarts/client-libraries.md) para começar com o serviço.|
|[Reconhecedor de Formato](./form-recognizer/index.yml "Reconhecedor de Formato")|O Reconhecimento de Formulários identifica e extrai pares de valores-chave e dados de tabela a partir de documentos de formulário; em seguida, produz dados estruturados, incluindo as relações no ficheiro original. Consulte [o arranque rápido do Form Recogniser](./form-recognizer/quickstarts/client-library.md) para começar.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|O Indexer de Vídeo permite-lhe extrair informações do seu vídeo. Consulte [o video indexer quickstart](/media-services/video-indexer/video-indexer-get-started.md) para começar.|

## <a name="speech-apis"></a>APIs de fala

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Serviço de fala](./speech-service/index.yml "Serviço de voz")|O serviço de fala adiciona funcionalidades ativadas à fala às aplicações. O serviço de fala inclui várias capacidades como discurso-a-texto, texto-a-discurso, tradução da fala, e muito mais.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>APIs linguísticos

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Compreensão linguística LUIS](./luis/index.yml "Compreensão de Idiomas")|Language Understanding (LUIS) é um serviço de IA de conversação baseado na nuvem que aplica inteligência personalizada de aprendizagem automática ao texto de linguagem natural e conversacional de um utilizador para prever o significado geral, e retirar informações relevantes e detalhadas. [Consulte o RAPIDSTART LUIS](./luis/get-started-portal-build-app.md) para começar com o serviço.|
|[Criador de FAQ](./qnamaker/index.yml "QnA Maker")|O QnA Maker permite-lhe construir um serviço de perguntas e respostas a partir do seu conteúdo semi-estruturado. [Consulte o quickstart do QnA Maker](./qnamaker/quickstarts/create-publish-knowledge-base.md) para começar com o serviço.|
|[Análise de Texto](./text-analytics/index.yml "Análise de Texto")| Text Analytics fornece processamento de linguagem natural sobre texto cru para análise de sentimento, extração de frases-chave e deteção de linguagem. Consulte [o Text Analytics para](./text-analytics/quickstarts/client-libraries-rest-api.md) começar com o serviço.|
|[Tradutor](./translator/index.yml "Tradutor")|O tradutor fornece tradução de texto baseada em máquina em quase tempo real.|
| [Leitura Avançada](./immersive-reader/index.yml "Leitura Avançada") | O Leitor Imersivo adiciona capacidades de leitura e compreensão do ecrã às suas aplicações. Consulte [o Quickstart do Leitor Imersivo](./immersive-reader/quickstarts/client-libraries.md) para começar com o serviço. |

## <a name="decision-apis"></a>APIs de decisão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Detetor de Anomalias](./anomaly-detector/index.yml "Detetor de Anomalias") |O Detetor de Anomalias permite-lhe monitorizar e detetar anomalias nos dados da série de tempo. Veja [o quickstart do Detetor de Anomalias](./anomaly-detector/quickstarts/client-libraries.md) para começar com o serviço|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|O Moderador de Conteúdo fornece monitorização para possíveis conteúdos ofensivos, indesejáveis e arriscados. Consulte [o 'Arranque' do Moderador de Conteúdo](./content-moderator/client-libraries.md) para começar com o serviço.|
|[Conselheiro de Métricas](./metrics-advisor/index.yml) (Pré-visualização) | O Metrics Advisor fornece uma deteção de anomalias personalizáveis em dados de séries de tempo multivariáveis e um portal web totalmente apresentado para ajudá-lo a usar o serviço. Consulte [o Quickstart Metrics Advisor](./metrics-advisor/quickstarts/rest-api-and-client-library.md) para começar com o serviço. |
|[Personalizador](./personalizer/index.yml "Personalizador")|O personalização permite-lhe escolher a melhor experiência para mostrar aos seus utilizadores, aprendendo com o seu comportamento em tempo real. Consulte [o Arranque Rápido Personalizer](./personalizer/quickstart-personalizer-sdk.md) para começar com o serviço.|

## <a name="search-apis"></a>APIs de pesquisa

> [!NOTE]
> À procura [de Azure Cognitive Search?](../search/index.yml) Embora utilize serviços cognitivos para algumas tarefas, é uma tecnologia de pesquisa diferente que suporta outros cenários.

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Pesquisa do Bing Notícias](/azure/cognitive-services/bing-news-search/ "Pesquisa do Bing Notícias")|Bing News Search devolve uma lista de artigos noticiosos determinados como relevantes para a consulta do utilizador.|
|[Pesquisa de Vídeos do Bing](/azure/cognitive-services/Bing-Video-Search/ "Pesquisa de Vídeos do Bing")|Bing Video Search devolve uma lista de vídeos determinados a ser relevantes para a consulta do utilizador.|
|[Pesquisa na Web do Bing](./bing-web-search/index.yml "Pesquisa na Web do Bing")|Bing Web Search devolve uma lista de resultados de pesquisa determinados como relevantes para a consulta do utilizador.|
|[Sugestão Automática do Bing](/azure/cognitive-services/Bing-Autosuggest "Sugestão Automática do Bing")|Bing Autosuggest permite-lhe enviar um termo de consulta parcial de pesquisa para Bing e obter de volta uma lista de consultas sugeridas.|
|[Pesquisa Personalizada do Bing](/azure/cognitive-services/bing-custom-search "Pesquisa Personalizada do Bing")|Bing Custom Search permite-lhe criar experiências de pesquisa personalizadas para tópicos que lhe interessam.|
|[Pesquisa de Entidades do Bing](/azure/cognitive-services/bing-entities-search/ "Pesquisa de Entidades do Bing")|Bing Entidade Pesquisa devolve informações sobre entidades que Bing determina serem relevantes para a consulta de um utilizador.|
|[Pesquisa de Imagens do Bing](/azure/cognitive-services/bing-image-search "Pesquisa de Imagens do Bing")|Bing Image Search devolve um ecrã de imagens determinadas como relevantes para a consulta do utilizador.|
|[Pesquisa Visual do Bing](/azure/cognitive-services/bing-visual-search "Pesquisa Visual do Bing")|Bing Visual Search fornece insights sobre uma imagem como imagens visualmente semelhantes, fontes de compras para produtos encontrados na imagem e pesquisas relacionadas.|
|[Pesquisa de Negócios Locais do Bing](/azure/cognitive-services/bing-local-business-search/ "Pesquisa de Negócios Locais do Bing")| A API de Pesquisa de Negócios Locais permite que as suas aplicações encontrem informações de contacto e localização sobre empresas locais com base em consultas de pesquisa.|
|[Verificação Ortográfica do Bing](/azure/cognitive-services/bing-spell-check/ "Verificação Ortográfica do Bing")|Bing Spell Check permite-lhe realizar gramática contextual e verificação ortográfica.|

## <a name="get-started-with-cognitive-services"></a>Introdução aos Serviços Cognitivos

Comece por criar um recurso de Serviços Cognitivos com quickstarts práticos utilizando os seguintes métodos:

* [Portal do Azure](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Portal do Azure")
* [CLI do Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "CLI do Azure")
* [Bibliotecas de clientes Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programação-linguagem-csharp")
* [Modelos do Azure Resource Manager (ARM)](./create-account-resource-manager-template.md?tabs=portal "Modelos do Azure Resource Manager (ARM)")

## <a name="using-cognitive-services-in-different-development-environments"></a>Utilização de Serviços Cognitivos em diferentes ambientes de desenvolvimento

Com a Azure e serviços cognitivos, você tem acesso a várias opções de desenvolvimento, tais como:

* Ferramentas de automação e integração como Apps Lógicas e Power Automamate.
* Opções de implementação como as Funções Azure e o Serviço de Aplicações. 
* Serviços Cognitivos Docker contentores para acesso seguro.
* Ferramentas como Apache Spark, Azure Databricks, Azure Synapse Analytics e Azure Kubernetes Service para cenários de Big Data. 

Para saber mais, consulte [as opções de desenvolvimento dos Serviços Cognitivos.](./cognitive-services-development-options.md)

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Utilização de Serviços Cognitivos de forma segura

A Azure Cognitive Services fornece um modelo de segurança em camadas, incluindo [a autenticação](authentication.md "autenticação") através de credenciais de Diretório Ativo Azure, uma chave de recursos válida, e [Redes Virtuais Azure](cognitive-services-virtual-networks.md "Redes Virtuais do Azure").

## <a name="containers-for-cognitive-services"></a>Contentores para os Serviços Cognitivos

 A Azure Cognitive Services fornece vários recipientes Docker que permitem usar as mesmas APIs que estão disponíveis em Azure, no local. A utilização destes recipientes confere-lhe a flexibilidade para aproximar os Serviços Cognitivos dos seus dados por razões de conformidade, segurança ou outras razões operacionais. Saiba mais sobre [os Contentores de Serviços Cognitivos.](cognitive-services-container-support.md "Contentores dos Serviços Cognitivos")

## <a name="regional-availability"></a>Disponibilidade regional

As APIs em Serviços Cognitivos estão hospedadas numa rede crescente de centros de dados geridos pela Microsoft. Pode encontrar a disponibilidade regional para cada API na [lista da região de Azure.](https://azure.microsoft.com/regions "Lista da região de Azure")

À procura de uma região que ainda não apoiamos? Informe-nos ao apresentar um pedido de funcionalidade no nosso [fórum UserVoice](https://cognitive.uservoice.com/ "Fórum do UserVoice").

## <a name="supported-cultural-languages"></a>Línguas culturais apoiadas

Os Serviços Cognitivos apoiam uma vasta gama de línguas culturais ao nível do serviço. Pode encontrar a disponibilidade de idiomas para cada API na [lista de línguas suportadas.](language-support.md "lista de línguas apoiadas")

## <a name="certifications-and-compliance"></a>Certificações e conformidade

Os Serviços Cognitivos foram premiados com certificações como CSA STAR Certification, FedRAMP Moderate e HIPAA BAA. Você pode [baixar](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "transferir") certificações para suas próprias auditorias e avaliações de segurança.

Para compreender a privacidade e a gestão de dados, vá ao [Trust Center.](https://servicetrust.microsoft.com/ "Centro de Confiança")

## <a name="support"></a>Suporte

Os Serviços Cognitivos oferecem várias opções de suporte para ajudá-lo a avançar com a criação de aplicações inteligentes. Os Serviços Cognitivos também têm uma forte comunidade de desenvolvedores que podem ajudar a responder às suas questões específicas. Para obter uma lista completa de opções disponíveis para si, consulte as opções de [suporte dos Serviços Cognitivos e opções de ajuda.](cognitive-services-support-options.md "Serviços Cognitivos apoiam e ajudam opções")

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta dos Serviços Cognitivos](cognitive-services-apis-create-account.md "Criar uma conta dos Serviços Cognitivos")
* [Quais as novidades nos docs dos Serviços Cognitivos](whats-new-docs.md "Quais as novidades nos docs dos Serviços Cognitivos")
* [Planear e gerir os custos dos Serviços Cognitivos](plan-manage-costs.md)
