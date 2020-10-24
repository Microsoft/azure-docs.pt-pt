---
title: 'Tutorial: Imagens moderadas de produtos de e-commerce - Moderador de Conteúdo'
titleSuffix: Azure Cognitive Services
description: Este tutorial mostra como configurar uma aplicação para analisar e classificar imagens de produtos com etiquetas especificadas (usando Azure Computer Vision e Custom Vision). Marque imagens censuráveis para serem revistas mais aprofundadamente (utilizando o Moderador de Conteúdo Azure).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 9aae410d320713650704e175006a6593b30f52a7
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92504161"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Tutorial: Imagens moderadas de produtos de e-commerce com Moderador de Conteúdo Azure

Neste tutorial, você aprenderá a usar os Serviços Cognitivos Azure, incluindo o Moderador de Conteúdo, para classificar e moderar imagens de produtos para um cenário de e-commerce. Você usará visão de computador e visão personalizada para aplicar tags (etiquetas) em imagens, e então você vai criar uma revisão de equipe, que combina as tecnologias baseadas em aprendizagem automática do Content Moderador com equipas de revisão humana para fornecer um sistema de moderação inteligente.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Inscreva-se no Moderador de Conteúdo e crie uma equipa de revisão.
> * Utilize a API de imagem do Content Moderator para analisar potenciais conteúdos para adultos e picantes.
> * Utilize o serviço de Visão de Computador para verificar se existem conteúdos de celebridades (ou outras tags detetáveis por Computador-Vision).
> * Utilize o serviço Visão Personalizada para verificar a presença de bandeiras, brinquedos e canetas (ou outras etiquetas personalizadas).
> * Apresente os resultados combinados da digitalização para revisão humana e tomada de decisão final.

O código de amostra completo está disponível no repositório de moderação do [catálogo samples eCommerce](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) no GitHub.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- A chave de uma subscrição do Content Moderator. Siga as instruções na [conta Criar um Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de Moderador de Conteúdo e obter a sua chave.
- Uma chave de subscrição de Visão de Computador (as mesmas instruções acima).
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
- Um conjunto de imagens para cada etiqueta que o classificador de Visão Personalizada utilizará (neste caso, brinquedos, canetas e bandeiras dos EUA).

## <a name="create-a-review-team"></a>Criar uma equipa de revisão

Consulte o Moderador de [Conteúdo de Tentativa no início](quick-start.md) da web para obter instruções sobre como se inscrever na ferramenta de Análise de Moderador de [Conteúdo](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipa de revisão. Tome nota do valor de **identificação** da equipa na página **De Credenciais.**

## <a name="create-custom-moderation-tags"></a>Criar etiquetas de moderação personalizadas

Em seguida, crie etiquetas personalizadas na ferramenta 'Rever' (consulte o artigo [Tags](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) se precisar de ajuda neste processo). Neste caso, vamos adicionar as seguintes tags: **celebridade,** **EUA,** **bandeira,** **brinquedo,** e **caneta.** Nem todas as tags precisam de ser detetáveis categorias em Visão Computacional (como **celebridade);** pode adicionar as suas próprias etiquetas personalizadas desde que treine o classificador De Visão Personalizada para detetá-las mais tarde.

![Configurar etiquetas personalizadas](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio

1. No Visual Studio, abra o diálogo do Novo Projeto. Expandir **instalado,** em **seguida, Visual C.**, em seguida, selecione **app consola (.NET Framework)**.
1. Nomeie a aplicação **EcommerceModeration**e, em seguida, clique **em OK**.
1. Se você está adicionando este projeto a uma solução existente, selecione este projeto como o projeto de startup única.

Este tutorial destaca o código que é central para o projeto, mas não cobre todas as linhas de código. Copie todo o conteúdo de _Program.cs_ do projeto de amostra ([Samples eCommerce Catalog Moderation)](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)no arquivo _Program.cs_ do seu novo projeto. Em seguida, passe pelas seguintes secções para saber como o projeto funciona e como usá-lo por si mesmo.

## <a name="define-api-keys-and-endpoints"></a>Definir chaves e pontos finais da API

Este tutorial utiliza três serviços cognitivos; portanto, requer três chaves correspondentes e pontos finais API. Consulte os seguintes campos na classe **Programa:**

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Terá de atualizar os `___Key` campos com os valores das suas teclas de subscrição e precisa de alterar os campos para `___Uri` urls de ponto final correto (terá a tecla Visão Personalizada e o ponto final mais tarde). Pode encontrar estes valores nos separadores **de início rápido** de cada recurso Azure. Preencha a `YOURTEAMID` parte do campo com o `ReviewUri` ID da equipa de revisão que criou anteriormente. Vais preencher a parte final do `CustomVisionUri` campo mais tarde.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Chamadas de método primário

Consulte o seguinte código no método **Principal,** que passa por uma lista de URLs de imagem. Analisa cada imagem com os três serviços diferentes, regista as tags aplicadas na matriz **ReviewTags** e, em seguida, cria uma revisão para moderadores humanos enviando as imagens para a Ferramenta de Revisão do Moderador de Conteúdo. Você explorará estes métodos nas seguintes secções. Se desejar, pode controlar quais as imagens enviadas para rever, utilizando o conjunto **ReviewTags** numa declaração condicional para verificar quais as etiquetas que foram aplicadas.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Avaliar métodoAdultRacy

Consulte o método **AvaliarAdultRacy** na classe **Programa.** Este método requer um URL de imagem e uma matriz de pares de valores-chave como parâmetros. Chama a API de Imagem do Moderador de Conteúdo (usando REST) para obter as pontuações de imagem adulta e picante. Se a pontuação para um deles for superior a 0,4 (o intervalo é entre 0 e 1), define o valor correspondente na matriz **ReviewTags** para **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Avaliar Método DeComputerVisionTags

O método seguinte requer um URL de imagem e a informação de subscrição da Visão de Computador e analisa a imagem para a presença de celebridades. Se uma ou mais celebridades forem encontradas, define o valor correspondente na matriz **ReviewTags** para **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Avaliar MétodoCustomVisionTags

Em seguida, consulte o método **AssessCustomVisionTags,** que classifica os produtos reais &mdash; neste caso bandeiras, brinquedos e canetas. Siga as instruções do Guia de Como construir um guia [de classificação](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) para construir o seu próprio classificador de imagem personalizado e detetar bandeiras, brinquedos e canetas (ou o que escolher como suas etiquetas personalizadas) em imagens. Pode utilizar as imagens na pasta **de imagens de amostras** do [repo GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) para treinar rapidamente algumas das categorias neste exemplo.

![Página web da Visão Personalizada com imagens de treinamento de canetas, brinquedos e bandeiras](images/tutorial-ecommerce-custom-vision.PNG)

Depois de ter treinado o seu classificador, obtenha a chave de previsão e url de ponto final de previsão (consulte [obter o URL e a chave de previsão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) se precisar de ajuda para recuperá-los) e atribua estes valores ao seu e aos seus `CustomVisionKey` `CustomVisionUri` campos, respectivamente. O método utiliza estes valores para consultar o classificador. Se o classificador encontrar uma ou mais etiquetas personalizadas na imagem, este método define o valor ou s correspondente na matriz **reviewTags** para **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Criar comentários para a ferramenta Review

Nas secções anteriores, explorou como a aplicação digitaliza imagens recebidas para conteúdo adulto e picante (Moderador de Conteúdo), celebridades (Visão de Computador) e vários outros objetos (Visão Personalizada). Em seguida, consulte o método **CreateReview,** que faz o upload das imagens com todas as suas tags aplicadas (passadas como _Metadados)_ para a Ferramenta de Revisão do Moderador de Conteúdo.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

As imagens aparecerão no separador 'Análise' da [ferramenta 'Análise de Moderadores de Conteúdo'.](https://contentmoderator.cognitive.microsoft.com/)

![Screenshot da ferramenta de Análise de Moderador de Conteúdo com várias imagens e as suas tags em destaque](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Enviar uma lista de imagens de teste

Como pode ver no método **Principal,** este programa procura um diretório "C:Test" com um ficheiro _Urls.txt_ que contenha uma lista de Urls de imagem. Crie este ficheiro e diretório, ou altere o caminho para apontar para o seu ficheiro de texto. Em seguida, povoar este ficheiro com os URLs de imagens que gostaria de testar.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Execute o programa

Se seguiu todos os passos acima, o programa deve processar cada imagem (consultando os três serviços para as suas etiquetas relevantes) e, em seguida, fazer o upload das imagens com informações de marcação para a Ferramenta de Revisão do Moderador de Conteúdo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, cria-se um programa para analisar as imagens do produto, marcá-las por tipo de produto e permitir que uma equipa de revisão tome decisões informadas sobre a moderação de conteúdos. Em seguida, saiba mais sobre os detalhes da moderação da imagem.

> [!div class="nextstepaction"]
> [Rever imagens moderadas](./review-tool-user-guide/review-moderated-images.md)
