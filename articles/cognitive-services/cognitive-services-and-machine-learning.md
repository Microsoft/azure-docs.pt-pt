---
title: Serviços Cognitivos e Aprendizagem Automática
titleSuffix: Azure Cognitive Services
description: Saiba onde se enquadram os Serviços Cognitivos do Azure entre as outras ofertas do Azure para aprendizagem automática.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 4ab32cd37d732cd9f177363da547edd22bd5af77
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349270"
---
# <a name="cognitive-services-and-machine-learning"></a>Serviços Cognitivos e aprendizagem automática

Os Serviços Cognitivos fornecem capacidades de aprendizagem automática para resolver problemas gerais, como analisar texto para sentimento emocional ou analisar imagens para reconhecer objetos ou rostos. Não precisa de conhecimentos de machine learning ou ciência de dados para utilizar estes serviços. 

[Os Serviços Cognitivos](./what-are-cognitive-services.md) são um grupo de serviços, cada um apoiando diferentes capacidades de previsão generalizadas. Os serviços estão divididos em diferentes categorias para ajudá-lo a encontrar o serviço certo. 

|Categoria de serviço|Objetivo|
|--|--|
|[Decisão](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Crie aplicações que obtenham recomendações para assegurar uma tomada de decisões informada e eficiente.|
|[Idioma](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Permita que as suas aplicações processem linguagem natural através de scripts pré-criados, avalie sentimentos e saiba como reconhecer aquilo que os utilizadores pretendem.|
|[Pesquisa](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Adicione APIs de Pesquisa do Bing às suas aplicações e explore a capacidade de combinar inúmeras páginas Web, imagens, vídeos e notícias numa única chamada à API.|
|[Voz](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Converta voz em texto e texto em voz natural. Traduza de um idioma para outro e ative o reconhecimento e a verificação de orador.|
|[Visão](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Reconheça, identifique, legende, indexe e modere as suas imagens, vídeos e conteúdo com tinta digital.|
||||

Utilize serviços cognitivos quando:

* Pode usar uma solução generalizada.
* Solução de acesso a partir de uma programação REST API ou SDK. 

Utilize outra solução de aprendizagem automática quando:

* Precisa escolher o algoritmo e precisa treinar em dados muito específicos.

## <a name="what-is-machine-learning"></a>O que é machine learning?

Machine learning é um conceito em que se reúnem dados e um algoritmo para resolver uma necessidade específica. Uma vez treinados os dados e o algoritmo, a saída é um modelo que pode ser novamente utilizado com dados diferentes. O modelo treinado fornece insights com base nos novos dados. 

O processo de construção de um sistema de aprendizagem automática requer algum conhecimento de aprendizagem automática ou ciência de dados.

O machine learning é fornecido utilizando [produtos e serviços de Aprendizagem automática Azure (AML).](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context)

## <a name="what-is-a-cognitive-service"></a>O que é um Serviço Cognitivo?

Um Serviço Cognitivo fornece parte ou todos os componentes numa solução de machine learning: dados, algoritmo e modelo treinado. Estes serviços destinam-se a exigir conhecimento geral sobre os seus dados sem necessitar de experiência com machine learning ou data science. Estes serviços fornecem tanto API(s) rest e SDKs baseados em linguagem. Como resultado, é necessário ter conhecimentos linguísticos de programação para utilizar os serviços.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Como é que os Serviços Cognitivos e a Aprendizagem Automática Azure (AML) são semelhantes?

Ambos têm o objetivo final de aplicar inteligência artificial (IA) para melhorar as operações comerciais, embora a forma como cada um fornece isso nas respetivas ofertas seja diferente. 

Geralmente, o público é diferente:

* Os Serviços Cognitivos são para desenvolvedores sem experiência de aprendizagem automática.
* A Azure Machine Learning é adaptada para cientistas de dados. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Como é que um Serviço Cognitivo é diferente do machine learning?

Um Serviço Cognitivo fornece um modelo treinado para si. Isto reúne dados e um algoritmo, disponível a partir de um REST API(s) ou SDK. Pode implementar este serviço dentro de minutos, dependendo do seu cenário.  Um Serviço Cognitivo fornece respostas para problemas gerais, tais como frases-chave em texto ou identificação de item em imagens. 

O machine learning é um processo que geralmente requer um período de tempo mais longo para implementar com sucesso. Este tempo é gasto na recolha de dados, limpeza, transformação, seleção de algoritmos, formação de modelos e implementação para chegar ao mesmo nível de funcionalidade fornecido por um Serviço Cognitivo. Com machine learning, é possível fornecer respostas a problemas altamente especializados e/ou específicos. Os problemas de aprendizagem automática requerem uma familiaridade com o assunto específico e os dados do problema em questão, bem como a experiência em ciência dos dados.

## <a name="what-kind-of-data-do-you-have"></a>Que tipo de dados tem?

Os Serviços Cognitivos, como um grupo de serviços, não podem exigir nenhum, alguns ou todos os dados personalizados para o modelo treinado. 

### <a name="no-additional-training-data-required"></a>Não são necessários dados adicionais de formação

Os serviços que fornecem um modelo totalmente treinado podem ser tratados como uma _caixa opaca._ Não precisas de saber como funcionam ou que dados foram usados para os treinar. Leva os seus dados a um modelo totalmente treinado para obter uma previsão. 

### <a name="some-or-all-training-data-required"></a>Alguns ou todos os dados de formação necessários

Alguns serviços permitem-lhe trazer os seus próprios dados e, em seguida, treinar um modelo. Isto permite-lhe estender o modelo usando os dados e algoritmos do Serviço com os seus próprios dados. A saída corresponde às suas necessidades. Quando traz os seus próprios dados, poderá ter de marcar os dados de uma forma específica ao serviço. Por exemplo, se estiver a treinar um modelo para identificar flores, pode fornecer um catálogo de imagens de flores juntamente com a localização da flor em cada imagem para treinar o modelo. 

Um serviço pode _permitir-lhe_ fornecer dados para melhorar os seus próprios dados. Um serviço pode _exigir_ que forneça dados. 

### <a name="real-time-or-near-real-time-data-required"></a>Dados em tempo real ou perto de tempo real necessários

Um serviço pode precisar de dados em tempo real ou em tempo real para construir um modelo eficaz. Estes serviços processam quantidades significativas de dados de modelos. 

## <a name="service-requirements-for-the-data-model"></a>Requisitos de serviço para o modelo de dados

Os dados a seguir categorizam cada serviço pelo tipo de dados que permite ou requer.

|Serviço Cognitivo|Não são necessários dados de formação|Fornece alguns ou todos os dados de treino|Recolha de dados em tempo real ou próximo em tempo real|
|--|--|--|--|
|[Detetor de Anomalias](./Anomaly-Detector/overview.md)|x|x|x|
|Pesquisa do Bing |x|||
|[Imagem Digitalizada](./computer-vision/overview.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Visão Personalizada](./custom-vision-service/overview.md)||x||
|[Face](./Face/Overview.md)|x|x||
|[Reconhecedor de Formato](./form-recognizer/overview.md)||x||
|[Leitura Avançada](./immersive-reader/overview.md)|x|||
|[Reconhecedor de Tinta Digital](./Ink-recognizer/overview.md)|x|x||
|[Compreensão de Idiomas (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizador](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[Criador de FAQ](./QnAMaker/Overview/overview.md)||x||
|[Orador Reconhecedor](./speech-service/speaker-recognition-overview.md)||x||
|[Discurso Texto-a-discurso (TTS)](speech-service/text-to-speech.md)|x|x||
|[Discurso Discurso-a-texto (STT)](speech-service/speech-to-text.md)|x|x||
|[Tradução de Voz](speech-service/speech-translation.md)|x|||
|[Análise de Texto](./text-analytics/overview.md)|x|||
|[Tradutor](./translator/translator-info-overview.md)|x|||
|[Tradutor - tradutor personalizado](./translator/custom-translator/overview.md)||x||

*O personalizar só precisa de dados de formação recolhidos pelo serviço (uma vez que funciona em tempo real) para avaliar a sua política e dados. O personalização não precisa de grandes conjuntos de dados históricos para treinos frontais ou de lote. 

## <a name="where-can-you-use-cognitive-services"></a>Onde pode usar os Serviços Cognitivos?
 
Os serviços são utilizados em qualquer aplicação que possa fazer chamadas REST API ou SDK. Exemplos de aplicações incluem web sites, bots, realidade virtual ou mista, desktop e aplicações móveis. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Como é que a Azure Cognitive Search está relacionada com os Serviços Cognitivos?

[Azure Cognitive Search](../search/search-what-is-azure-search.md) é um serviço de pesquisa em nuvem separado que utiliza opcionalmente serviços cognitivos para adicionar imagem e processamento de linguagem natural à indexação de cargas de trabalho. Os Serviços Cognitivos são expostos na Azure Cognitive Search através [de habilidades incorporadas](../search/cognitive-search-predefined-skills.md) que envolvem APIs individuais. Você pode usar um recurso gratuito para caminhadas, mas planeia criar e anexar um [recurso faturado](../search/cognitive-search-attach-cognitive-services.md) para volumes maiores.

## <a name="how-can-you-use-cognitive-services"></a>Como pode usar os Serviços Cognitivos?

Cada serviço fornece informações sobre os seus dados. Pode combinar serviços em conjunto com soluções em cadeia, como converter a fala (áudio) em texto, traduzir o texto em muitas línguas, e depois usar as línguas traduzidas para obter respostas de uma base de conhecimento. Embora os Serviços Cognitivos possam ser usados para criar soluções inteligentes por si só, também podem ser combinados com projetos tradicionais de aprendizagem automática para complementar modelos ou acelerar o processo de desenvolvimento. 

Serviços Cognitivos que fornecem modelos exportados para outras ferramentas de aprendizagem automática:

|Serviço Cognitivo|Informação do modelo|
|--|--|
|[Visão Personalizada](./custom-vision-service/overview.md)|[Exportação](./Custom-Vision-Service/export-model-python.md) para Tensorflow para Android, CoreML para iOS11, ONNX para Windows ML|

## <a name="learn-more"></a>Saiba mais

* [Guia de Arquitetura - Quais são os produtos de aprendizagem automática na Microsoft?](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Machine learning - Introdução à aprendizagem profunda vs. aprendizagem automática](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Passos seguintes

* Crie a sua conta de Serviço Cognitivo no [portal Azure](cognitive-services-apis-create-account.md) ou com [o Azure CLI](./cognitive-services-apis-create-account-cli.md).
* Aprenda a [autenticar](authentication.md) um Serviço Cognitivo.
* Utilize [o registo de diagnóstico](diagnostic-logging.md) para identificação e depuragem de problemas. 
* Implementar um Serviço Cognitivo num [recipiente](cognitive-services-container-support.md)Docker.
* Mantenha-se atualizado com [as atualizações de serviço](https://azure.microsoft.com/updates/?product=cognitive-services).