---
title: 'Tutorial: Moderar as imagens de produto de comércio eletrônico - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Configurar uma aplicação para analisar e classificar as imagens dos produtos com etiquetas especificadas (através do Azure de imagem digitalizada e de visão personalizada) e imagens objetáveis da etiqueta ser revisto (com o Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 5c4d2320ffd54054eb8a5bb26ef14c8e99dabb33
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855959"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Tutorial: Imagens de produto de comércio eletrônico moderado Azure Content moderator

Neste tutorial, irá aprender como utilizar os serviços cognitivos do Azure, incluindo o Content Moderator, para classificar com eficiência e imagens dos produtos moderada para um cenário de comércio eletrônico. Vai utilizar visão do computador e de visão personalizada para aplicar as várias etiquetas (etiquetas) para imagens e, em seguida, irá criar uma revisão de equipe, que combina as tecnologias de baseados em machine-learning do Content Moderator com as equipes de revisão humana para fornecer uma moderação inteligente System.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Inscreva-se o Content Moderator e criar uma equipa de revisão.
> * Utilize a API de imagem do Content Moderator para analisar potenciais conteúdos para adultos e picantes.
> * Utilize o serviço de visão do computador para verificar a existência de conteúdo de celebridade (ou outras marcas de computador-visão-detetável).
> * Utilize o serviço de visão personalizada para analisar a presença de sinalizadores, toys e canetas (ou outras etiquetas personalizadas).
> * Apresente os resultados da análise combinado de revisão humana e a tomada de decisão final.

O código de exemplo completo está disponível na [exemplos de comércio eletrónico moderação do catálogo de](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) repositório no GitHub.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- A chave de uma subscrição do Content Moderator. Siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço do Content Moderator e obtenha a chave.
- Uma chave de assinatura de imagem digitalizada (mesmo instruções, conforme apresentado acima).
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
- Um conjunto de imagens para cada etiqueta do que o classificador de visão personalizada irá utilizar (neste caso toys, canetas e nos sinalizadores).

## <a name="create-a-review-team"></a>Criar uma equipa de revisão

Consulte a [Experimente o Content Moderator na web](quick-start.md) início rápido para obter instruções sobre como inscrever-se a [ferramenta rever conteúdo do moderador](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipa de revisão. Anote o **ID da equipa** valor na **credenciais** página.

## <a name="create-custom-moderation-tags"></a>Criar etiquetas personalizadas de moderação

Em seguida, criar etiquetas personalizadas na ferramenta de revisão (consulte a [etiquetas](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) artigo se precisar de ajuda com este processo). Neste caso, iremos adicionar as seguintes tags: **celebridade**, **EUA**, **sinalizador**, **brinquedo**, e **caneta**. Tenha em atenção que todas as etiquetas não precisam ser detetável categorias na visão do computador (como **celebridade**); pode adicionar suas próprias etiquetas personalizadas, desde que treinar o classificador de visão personalizada para detetá-los mais tarde.

![Configurar etiquetas personalizadas](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio

1. No Visual Studio, abra a caixa de diálogo novo projeto. Expanda **instalada**, em seguida, **Visual C#** , em seguida, selecione **aplicação de consola (.NET Framework)**.
1. Dê o nome **EcommerceModeration**, em seguida, clique em **OK**.
1. Se estiver a adicionar este projeto para uma solução existente, selecione esse projeto como o projeto de arranque único.

Este tutorial irá realçar o código que é fundamental para o projeto, mas não abrange todas as linhas de código necessário. Copiar todo o conteúdo de _Program.cs_ do projeto de exemplo ([exemplos de comércio eletrónico moderação do catálogo de](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) para o _Program.cs_ ficheiro do seu novo projeto. Em seguida, percorra as secções seguintes para saber mais sobre como funciona o projeto e como usá-lo por conta própria.

## <a name="define-api-keys-and-endpoints"></a>Definir chaves de API e os pontos finais

Conforme mencionado acima, este tutorial utiliza três serviços cognitivos; Por conseguinte, requer três chaves correspondentes e os pontos finais da API. Consulte os seguintes campos no **programa** classe: 

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Terá de atualizar o `___Key` campos com os valores das suas chaves de subscrição (obterá o `CustomVisionKey` mais tarde), e poderá ter de alterar o `___Uri` campos para que eles contêm os identificadores de região correto. Preencha os `YOURTEAMID` faz parte do `ReviewUri` campo com o ID da equipa de revisão que criou anteriormente. Irá preencher a parte final do `CustomVisionUri` campo mais tarde.

## <a name="primary-method-calls"></a>Chamadas de método principal

Consulte o seguinte código a **Main** método, que faz um loop através de uma lista de URLs de imagens. Ele analisa cada imagem com os três serviços diferentes, que regista as etiquetas aplicadas na **ReviewTags** de matriz e, em seguida, cria uma revisão para moderadores humanos (envia as imagens para a ferramenta de revisão de moderador de conteúdo). Irá explorar esses métodos nas seções a seguir. Tenha em atenção que aqui, se desejar, pode controlar quais imagens são enviadas para rever, utilizando o **ReviewTags** matriz numa instrução condicional para verificar as etiquetas foram aplicadas.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Método EvaluateAdultRacy

Consulte a **EvaluateAdultRacy** método na **programa** classe. Esse método obtém um URL de imagem e uma matriz de pares chave-valor, como parâmetros. Ele chama a API de imagem do Content Moderator (usando REST) para obter as pontuações adulto e Racy da imagem. Se a classificação por qualquer for superior a 0.4 (o intervalo é de 0 a 1), ele define o valor correspondente na **ReviewTags** matriz para **verdadeiro**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecustomvisiontags-method"></a>Método EvaluateCustomVisionTags

O próximo método assume um URL de imagem e suas informações de subscrição de imagem digitalizada e analisa na imagem para a presença de celebridades. Se forem encontradas um ou mais celebridades, ele define o valor correspondente na **ReviewTags** matriz para **verdadeiro**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Método EvaluateCustomVisionTags

Em seguida, veja a **EvaluateCustomVisionTags** método, que classifica os produtos reais&mdash;neste caso sinaliza toys e canetas. Siga as instruções no [como criar um classificador](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) guia para criar o seu classificador de imagem personalizada para detetar a presença de sinalizadores, toys e canetas (ou qualquer coisa que escolheu como as suas etiquetas personalizadas) em imagens.

![Página de web de visão personalizada com imagens de formação de canetas, toys e sinalizadores](images/tutorial-ecommerce-custom-vision.PNG)

Depois de ter preparado o seu classificador, obter a chave de predição e o URL de ponto final de previsão (consulte [obter a chave de URL e previsão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) se precisar de ajuda a obtê-los) e atribuir estes valores para sua `CustomVisionKey` e `CustomVisionUri` campos , respectivamente. O método utiliza estes valores para consultar o classificador. Se o classificador de encontrar um ou mais das etiquetas personalizadas na imagem, este método define o valor ou valores correspondente na **ReviewTags** matriz para **verdadeiro**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Criar as revisões de ferramenta de revisão

Nas secções anteriores foram-lhe apresentados os métodos que analisam as imagens recebidas de forma a encontrar conteúdos para adultos (Content Moderator), celebridades (Imagem Digitalizada) e vários outros objetos (Visão Personalizada). Em seguida, vamos mostrar o método **CreateReview**, que carrega as imagens, com todas as respetivas etiquetas aplicadas (transmitidas como _Metadata_), para a ferramenta Revisão do Content Moderator para que possam estar disponíveis para revisão humana. 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

As imagens serão apresentados no separador de revisão do [ferramenta de revisão de moderador de conteúdo](https://contentmoderator.cognitive.microsoft.com/).

![Captura de ecrã da ferramenta de revisão de moderador de conteúdo com várias imagens e respetivas etiquetas realçadas](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Submeter uma lista de imagens de teste

Como pode ver na **Main** método, este programa procura um diretório de "C:Test" com um _Urls.txt_ ficheiro que contém uma lista de Urls de imagem. Criar o ficheiro e diretório, ou altere o caminho para apontar para o ficheiro de texto e, preencha este ficheiro com os URLs de imagens que pretende testar.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Execute o programa

Se seguiu todos os passos acima, o programa deve processar cada imagem (consulta em todos os três serviços para as respetivas etiquetas relevantes) e, em seguida, carregue as imagens com informações da etiqueta para a ferramenta de revisão de moderador de conteúdo.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um programa para analisar imagens dos produtos com o objetivo de marcá-los por tipo de produto e permitindo que uma equipa de revisão tomar decisões informadas sobre a moderação de conteúdos. Em seguida, saiba mais sobre os detalhes de moderação de imagens.

> [!div class="nextstepaction"]
> [Imagens de revisão moderado](./review-tool-user-guide/review-moderated-images.md)
