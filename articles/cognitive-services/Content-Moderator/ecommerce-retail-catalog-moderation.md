---
title: 'Tutorial: Imagens moderadas de produtos de e-commerce - Moderador de Conteúdo'
titleSuffix: Azure Cognitive Services
description: Este tutorial mostra como configurar uma aplicação para analisar e classificar imagens de produtos com etiquetas especificadas (utilizando a Visão Computacional azure e visão personalizada). Marque imagens censuráveis para serem revistas (utilizando o Moderador de Conteúdo Azure).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5e74eda9e30c536c0eba4e847019344c87e10cce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774343"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Tutorial: Imagens moderadas de produtos de e-commerce com Moderador de Conteúdo Azure

Neste tutorial, você aprenderá a usar serviços cognitivos Azure, incluindo Moderador de Conteúdo, para classificar e moderar imagens de produtos para um cenário de e-commerce. Você usará visão computorizada e visão personalizada para aplicar tags (etiquetas) em imagens, e depois criará uma revisão de equipe, que combina tecnologias baseadas em machine-learning do Content Moderador com equipas de revisão humana para fornecer um sistema de moderação inteligente.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Inscreva-se no Content Moderator e crie uma equipa de revisão.
> * Utilize a API de imagem do Content Moderator para analisar potenciais conteúdos para adultos e picantes.
> * Utilize o serviço Computer Vision para procurar conteúdo de celebridades (ou outras tags detetáveis por computador-visão).
> * Utilize o serviço Custom Vision para procurar a presença de bandeiras, brinquedos e canetas (ou outras etiquetas personalizadas).
> * Apresentar os resultados combinados da digitalização para revisão humana e tomada de decisão final.

O código de amostra completo está disponível no repositório de moderação do [catálogo de amostras](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) no GitHub.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- A chave de uma subscrição do Content Moderator. Siga as instruções na [conta Create a Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço moderador de conteúdo e obter a sua chave.
- Uma chave de subscrição da Visão Computacional (as mesmas instruções acima).
- Qualquer edição do [Visual Studio 2015 ou 2017.](https://www.visualstudio.com/downloads/)
- Um conjunto de imagens para cada rótulo que o classificador De Visão Personalizada utilizará (neste caso, brinquedos, canetas e bandeiras dos EUA).

## <a name="create-a-review-team"></a>Criar uma equipa de revisão

Consulte o Moderador de [Conteúdo experimente na web](quick-start.md) quickstart para obter instruções sobre como se inscrever na ferramenta De revisão de moderador de [conteúdo](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipa de revisão. Tome nota do valor de ID da **equipa** na página **credenciais.**

## <a name="create-custom-moderation-tags"></a>Criar etiquetas de moderação personalizadas

Em seguida, crie etiquetas personalizadas na ferramenta Review (consulte o artigo [tags](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) se precisar de ajuda com este processo). Neste caso, adicionaremos as seguintes tags: **celebridade,** **EUA,** **bandeira,** **brinquedo**e **caneta**. Nem todas as etiquetas precisam de ser categorias detetáveis em Visão Computacional (como **celebridade);** pode adicionar as suas próprias etiquetas personalizadas desde que treine o classificador Custom Vision para detetá-las mais tarde.

![Configurar etiquetas personalizadas](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio

1. No Estúdio Visual, abra o diálogo do Novo Projeto. Expandir **Instalado,** em seguida **Visual C#**, em seguida, selecione **app consola (.NET Framework)**.
1. Nomeie a aplicação **EcommerceModeração,** em seguida, clique **OK**.
1. Se está a adicionar este projeto a uma solução existente, selecione este projeto como o projeto de arranque único.

Este tutorial destaca o código que é central para o projeto, mas não cobre todas as linhas de código. Copie o conteúdo completo de _Program.cs_ do projeto da amostra[(Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) para o ficheiro _Program.cs_ do seu novo projeto. Em seguida, passe pelas seguintes secções para saber como funciona o projeto e como usá-lo por si mesmo.

## <a name="define-api-keys-and-endpoints"></a>Defina chaves E pontos finais DaPi

Este tutorial utiliza três serviços cognitivos; por conseguinte, requer três chaves correspondentes e pontos finais da API. Consulte os seguintes campos na aula do **Programa:**

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Terá de atualizar os `___Key` campos com os valores das suas chaves `___Uri` de subscrição e terá de alterar os campos para os URLs de ponto final corretos (receberá a tecla Visão Personalizada e o ponto final mais tarde). Pode encontrar estes valores nos separadores **de arranque rápido** de cada recurso Azure. Preencha `YOURTEAMID` a parte `ReviewUri` do campo com a identificação da equipa de revisão que criou anteriormente. Vais preencher a parte final `CustomVisionUri` do campo mais tarde.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Chamadas de método primário

Consulte o seguinte código no método **Principal,** que passa por uma lista de URLs de imagem. Analisa cada imagem com os três serviços diferentes, regista as tags aplicadas na matriz **ReviewTags** e, em seguida, cria uma revisão para moderadores humanos enviando as imagens para a Ferramenta de Revisão de Moderadores de Conteúdo. Explorará estes métodos nas seguintes secções. Se desejar, pode controlar quais as imagens enviadas para revisão, utilizando a matriz **ReviewTags** numa declaração condicional para verificar quais as etiquetas aplicadas.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Avaliar métodoAdultRacy

Consulte o método **AssessAdultRacy** na aula de **Programa.** Este método requer um URL de imagem e uma variedade de pares de valor-chave como parâmetros. Chama a API de Imagem do Moderador de Conteúdo (usando O REST) para obter as pontuações adulta e picante da imagem. Se a pontuação para qualquer um dos dois for superior a 0,4 (o intervalo é entre 0 e 1), define o valor correspondente na matriz **De Revisão Tags** para **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Avaliar o métodoComputerVisionTags

O próximo método requer um URL de imagem e a informação de subscrição da Computer Vision e analisa a imagem para a presença de celebridades. Se forem encontradas uma ou mais celebridades, define o valor correspondente na matriz **De Revisão Tags** a **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Avaliar métodoCustomVisionTags

Em seguida, consulte o método **AssessCustomVisionTags,** &mdash;que classifica os produtos reais neste caso bandeiras, brinquedos e canetas. Siga as instruções no Como construir um guia [de classificação](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) para construir o seu próprio classificador de imagem personalizado e detetar bandeiras, brinquedos e canetas (ou o que escolher como etiquetas personalizadas) em imagens. Pode utilizar as imagens na pasta de **imagens de amostra** do [repo GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) para treinar rapidamente algumas das categorias neste exemplo.

![Página web da Visão Personalizada com imagens de treino de canetas, brinquedos e bandeiras](images/tutorial-ecommerce-custom-vision.PNG)

Depois de ter treinado o seu classificador, obtenha a chave de previsão e o URL final do ponto de previsão (consulte `CustomVisionUri` a url e a chave de [previsão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) se precisar de ajuda para os recuperar) e atribua estes valores aos seus `CustomVisionKey` campos e campos, respectivamente. O método utiliza estes valores para consultar o classificador. Se o classificador encontrar uma ou mais etiquetas personalizadas na imagem, este método define o valor(s) correspondente na matriz **De Revisão Tags** para **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Criar comentários para ferramenta de revisão

Nas secções anteriores, explorou como a aplicação digitaliza imagens de entrada para conteúdos adultos e picantes (Moderador de Conteúdo), celebridades (Visão computorizada) e vários outros objetos (Visão Personalizada). Em seguida, consulte o método **CreateReview,** que envia as imagens com todas as suas etiquetas aplicadas (transmitidas como _Metadados)_ para a Ferramenta de Revisão de Moderadores de Conteúdo.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

As imagens aparecerão no separador Review da ferramenta De revisão de [moderadores](https://contentmoderator.cognitive.microsoft.com/)de conteúdo .

![Screenshot da ferramenta Content Moderator Review com várias imagens e suas tags em destaque](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Enviar uma lista de imagens de teste

Como pode ver no método **Principal,** este programa procura um diretório "C:Test" com um ficheiro _Urls.txt_ que contém uma lista de Urls de imagem. Crie este ficheiro e diretório, ou altere o caminho para apontar para o seu ficheiro de texto. Em seguida, povoe este ficheiro com os URLs de imagens que gostaria de testar.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Execute o programa

Se seguiu todos os passos acima, o programa deve processar cada imagem (consultando os três serviços para as respetivas etiquetas relevantes) e, em seguida, enviar as imagens com informações de etiqueta para a Ferramenta de Revisão de Moderadores de Conteúdo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura um programa para analisar imagens de produtos, marcá-las por tipo de produto, e permitir que uma equipa de revisão tome decisões informadas sobre a moderação de conteúdo. Em seguida, saiba mais sobre os detalhes da moderação da imagem.

> [!div class="nextstepaction"]
> [Reveja imagens moderadas](./review-tool-user-guide/review-moderated-images.md)
