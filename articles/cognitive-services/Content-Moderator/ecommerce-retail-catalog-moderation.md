---
title: 'Tutorial: imagens de produtos de comércio eletrônico moderadas-Content Moderator'
titleSuffix: Azure Cognitive Services
description: Configure um aplicativo para analisar e classificar imagens de produtos com rótulos especificados (usando o Azure Pesquisa Visual Computacional e Visão Personalizada). Marque imagens censuráveis para serem revisadas mais detalhadamente (usando o Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: e3c4f1c641865fa8aa1d01d370063c03bbc0680c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72936034"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Tutorial: imagens de produtos de comércio eletrônico moderadas com o Azure Content Moderator

Neste tutorial, você aprenderá a usar os serviços cognitivas do Azure, incluindo Content Moderator, para classificar e moderar imagens de produtos para um cenário de comércio eletrônico. Você usará Pesquisa Visual Computacional e Visão Personalizada para aplicar marcas (rótulos) a imagens e, em seguida, criará uma análise da equipe, que combina as tecnologias baseadas em aprendizado de máquina de Content Moderator com equipes de revisão humana para fornecer um sistema de moderação inteligente.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Inscreva-se para Content Moderator e crie uma equipe de revisão.
> * Utilize a API de imagem do Content Moderator para analisar potenciais conteúdos para adultos e picantes.
> * Use o serviço de Pesquisa Visual Computacional para verificar o conteúdo do celebridade (ou outras marcas detectáveis por revisão de computador).
> * Use o serviço de Visão Personalizada para verificar a presença de sinalizadores, brinquedos e canetas (ou outras marcas personalizadas).
> * Apresente os resultados de varredura combinados para a revisão humana e a tomada de decisão final.

O código de exemplo completo está disponível no repositório de [moderação de catálogo de exemplos de comércio eletrônico](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) no github.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- A chave de uma subscrição do Content Moderator. Siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço de Content moderator e obter sua chave.
- Uma chave de assinatura Pesquisa Visual Computacional (as mesmas instruções acima).
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
- Um conjunto de imagens para cada rótulo que o classificador de Visão Personalizada usará (neste caso, brinquedos, canetas e sinalizadores dos EUA).

## <a name="create-a-review-team"></a>Criar uma equipe de revisão

Consulte o guia de início rápido do [Content moderator na Web](quick-start.md) para obter instruções sobre como se inscrever na [ferramenta de revisão de Content moderator](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipe de revisão. Anote o valor da **ID da equipe** na página **credenciais** .

## <a name="create-custom-moderation-tags"></a>Criar marcas de moderação personalizadas

Em seguida, crie marcas personalizadas na ferramenta de revisão (consulte o artigo [marcas](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) se precisar de ajuda com esse processo). Nesse caso, adicionaremos as seguintes marcas: **celebridade**, **EUA**, **Flag**, **Toy**e **caneta**. Nem todas as marcas precisam ser categorias detectáveis no Pesquisa Visual Computacional (como **celebridade**); Você pode adicionar suas próprias marcas personalizadas, desde que você treine o classificador Visão Personalizada para detectá-los posteriormente.

![Configurar etiquetas personalizadas](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio

1. No Visual Studio, abra a caixa de diálogo novo projeto. Expanda **instalado**, **Visual C#** e, em seguida, selecione **aplicativo de console (.NET Framework)** .
1. Nomeie o aplicativo **EcommerceModeration**e clique em **OK**.
1. Se você estiver adicionando esse projeto a uma solução existente, selecione este projeto como o único projeto de inicialização.

Este tutorial realça o código central para o projeto, mas ele não abordará todas as linhas de código. Copie todo o conteúdo de _Program.cs_ do projeto de exemplo ([exemplos de moderação do catálogo de comércio eletrônico](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) no arquivo _Program.cs_ do seu novo projeto. Em seguida, percorra as seções a seguir para saber mais sobre como o projeto funciona e como usá-lo por conta própria.

## <a name="define-api-keys-and-endpoints"></a>Definir chaves de API e pontos de extremidade

Este tutorial usa três serviços cognitivas; Portanto, ele requer três chaves e pontos de extremidade de API correspondentes. Consulte os seguintes campos na classe **programa** :

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Você precisará atualizar os campos de `___Key` com os valores de suas chaves de assinatura e alterar os campos de `___Uri` para as URLs de ponto de extremidade corretas (você obterá a chave de Visão Personalizada e o ponto de extremidade posteriormente). Você pode encontrar esses valores nas guias **início rápido** de cada recurso do Azure. Preencha a parte `YOURTEAMID` do campo `ReviewUri` com a ID da equipe de revisão criada anteriormente. Você preencherá a parte final do campo `CustomVisionUri` mais tarde.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Chamadas de método primário

Consulte o código a seguir no método **Main** , que percorre uma lista de URLs de imagem. Ele analisa cada imagem com os três serviços diferentes, registra as marcas aplicadas na matriz **ReviewTags** e, em seguida, cria uma revisão para moderadores humanos enviando as imagens para a ferramenta de revisão de Content Moderator. Você irá explorar esses métodos nas seções a seguir. Se desejar, você pode controlar quais imagens são enviadas para revisão, usando a matriz **ReviewTags** em uma instrução condicional para verificar quais marcas foram aplicadas.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Método EvaluateAdultRacy

Consulte o método **EvaluateAdultRacy** na classe **Program** . Esse método usa uma URL de imagem e uma matriz de pares chave-valor como parâmetros. Ele chama a API de imagem do Content Moderator (usando REST) para obter as pontuações de adulto e erótico da imagem. Se a pontuação de for maior que 0,4 (o intervalo está entre 0 e 1), ele definirá o valor correspondente na matriz **ReviewTags** como **true**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Método EvaluateComputerVisionTags

O próximo método usa uma URL de imagem e suas Pesquisa Visual Computacional informações de assinatura e analisa a imagem para a presença de celebridades. Se um ou mais celebridades forem encontrados, ele definirá o valor correspondente na matriz **ReviewTags** como **true**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Método EvaluateCustomVisionTags

Em seguida, consulte o método **EvaluateCustomVisionTags** , que classifica os produtos reais&mdash;neste caso sinalizadores, brinquedos e canetas. Siga as instruções no [como criar um](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) guia de classificação para criar seu próprio classificador de imagem personalizada e detectar sinalizadores, brinquedos e canetas (ou qualquer coisa que você escolher como marcas personalizadas) em imagens. Você pode usar as imagens na pasta **Sample-images** do [repositório GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) para treinar rapidamente algumas das categorias neste exemplo.

![Visão Personalizada página da Web com imagens de treinamento de canetas, brinquedos e sinalizadores](images/tutorial-ecommerce-custom-vision.PNG)

Depois de ter treinado seu classificador, obtenha a chave de previsão e a URL do ponto de extremidade de previsão (consulte [obter a URL e a chave de previsão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) se precisar de ajuda para recuperá-las) e atribua esses valores aos campos `CustomVisionKey` e `CustomVisionUri`, respectivamente. O método usa esses valores para consultar o classificador. Se o classificador encontrar uma ou mais das marcas personalizadas na imagem, esse método definirá os valores correspondentes na matriz **ReviewTags** como **true**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Criar revisões para a ferramenta de análise

Nas seções anteriores, você explorou como o aplicativo examina as imagens recebidas para conteúdo adulto e erótico (Content Moderator), celebridades (Pesquisa Visual Computacional) e vários outros objetos (Visão Personalizada). Em seguida, consulte o método **createreview** , que carrega as imagens com todas as marcas aplicadas (passadas como _metadados_) para a ferramenta de revisão de Content Moderator.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

As imagens serão exibidas na guia revisão da [ferramenta de revisão de Content moderator](https://contentmoderator.cognitive.microsoft.com/).

![Captura de tela da ferramenta de revisão de Content Moderator com várias imagens e suas marcas realçadas](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Enviar uma lista de imagens de teste

Como você pode ver no método **Main** , esse programa procura um diretório "C:Test" com um arquivo _URLs. txt_ que contém uma lista de URLs de imagem. Crie esse arquivo e diretório ou altere o caminho para apontar para o arquivo de texto. Em seguida, preencha esse arquivo com as URLs de imagens que você gostaria de testar.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Execute o programa

Se você seguiu todas as etapas acima, o programa deve processar cada imagem (consultando todos os três serviços para suas marcas relevantes) e, em seguida, carregar as imagens com informações de marca para a ferramenta de revisão de Content Moderator.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura um programa para analisar imagens de produtos, marcá-las por tipo de produto e permitir que uma equipe de revisão tome decisões informadas sobre a moderação do conteúdo. Em seguida, saiba mais sobre os detalhes da moderação da imagem.

> [!div class="nextstepaction"]
> [Examinar imagens moderadas](./review-tool-user-guide/review-moderated-images.md)
