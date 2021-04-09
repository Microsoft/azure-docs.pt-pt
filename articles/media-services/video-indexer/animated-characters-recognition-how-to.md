---
title: Deteção de caracteres animados com Índice de Vídeo como
titleSuffix: Azure Media Services
description: Isto demonstra como usar a deteção de caracteres animados com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 29dbdb896d1a6063fec277afa33327c84cb2f0cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880932"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Utilize a deteção de caracteres animados (pré-visualização) com portal e API 

O Azure Media Services Video Indexer suporta a deteção, agrupamento e reconhecimento de personagens em conteúdo animado, esta funcionalidade está disponível através do portal Azure e através da API. Reveja este tópico [de visão](animated-characters-recognition.md) geral.

Este artigo demonstra como usar a deteção de caracteres animados com o portal Azure e a API do Indexante de Vídeo.

## <a name="use-the-animated-character-detection-with-portal"></a>Use a deteção de caracteres animados com portal 

Nas contas de teste, a integração Custom Vision é gerida pelo Video Indexer, podendo começar a criar e usar o modelo de caracteres animados. Se utilizar a conta de teste, pode saltar a seguinte secção ("Conecte a sua conta Visão Personalizada").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Conecte a sua conta De Visão Personalizada (apenas contas pagas)

Se possuir uma conta paga por Video Indexer, tem de ligar primeiro uma conta De Visão Personalizada. Se já não tem uma conta Visão Personalizada, por favor crie uma. Para mais informações, consulte [a Visão Personalizada.](../../cognitive-services/custom-vision-service/overview.md)

> [!NOTE]
> Ambas as contas têm de estar na mesma região. A integração da Visão Personalizada não é atualmente apoiada na região japonesa.

As contas pagas que tenham acesso à sua conta De Visão Personalizada podem ver os modelos e imagens marcadas lá. Saiba mais sobre [como melhorar o seu classificador em Visão Personalizada.](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) 

Note que a formação do modelo deve ser feita apenas através do Video Indexer, e não através do site Da Visão Personalizada. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Conecte uma conta de Visão Personalizada com a API 

Siga estes passos para ligar a conta De Visão Personalizada ao Indexador de Vídeo ou para alterar a conta Visão Personalizada que está atualmente ligada ao Indexador de Vídeo:

1. Navegue para [www.customvision.ai](https://www.customvision.ai) e faça login.
1. Copiar as chaves para os recursos de Formação e Previsão:

    > [!NOTE]
    > Para fornecer todas as chaves precisa de ter dois recursos separados na Visão Personalizada, um para treinar e outro para previsão.
1. Fornecer outras informações:

    * Ponto final 
    * ID de recurso de previsão
1. Navegue e inscreva-se no [Índice de Vídeo](https://vi.microsoft.com/).
1. Clique no ponto de interrogação no canto superior direito da página e escolha **referência API**.
1. Certifique-se de que está subscrito na Gestão da API clicando no separador **Produtos.** Se tiver uma API ligada, pode continuar até ao passo seguinte, caso contrário, subscreva. 
1. No portal do desenvolvedor, clique na **Referência API Completa** e navegue para **Operações.**  
1. Selecione **Conecte a Conta de Visão Personalizada (PREVIEW)** e clique em **Experimentá-la**.
1. Preencha os campos necessários, bem como o token de acesso e clique em **Enviar.** 

    Para obter mais informações sobre como obter o token de acesso do Indexer de Vídeo aceda ao portal do [desenvolvedor,](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)e consulte a [documentação relevante](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Assim que a chamada devolver 200 OK, a sua conta está ligada.
1. Para verificar a sua ligação navegando no portal [do Indexante de](https://vi.microsoft.com/)Vídeo:
1. Clique no botão de personalização do **modelo contento** no canto superior direito.
1. Vá ao **separador de caracteres animados.**
1. Assim que clicar em Gerir modelos em Visão Personalizada, será transferido para a conta Visão Personalizada que acabou de ligar.

> [!NOTE]
> Atualmente, apenas os modelos que foram criados através do Video Indexer são suportados. Os modelos criados através da Visão Personalizada não estarão disponíveis. Além disso, a melhor prática é editar modelos que foram criados através do Video Indexer apenas através da plataforma Video Indexer, uma vez que as alterações escorraçada através da Visão Personalizada podem causar resultados não intencionais.

### <a name="create-an-animated-characters-model"></a>Crie um modelo de personagens animados

1. Aceda ao site do [Video Indexer](https://vi.microsoft.com/) e inicie sessão.
1. Para personalizar um modelo na sua conta, selecione o botão de **personalização** do modelo Content à esquerda da página.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Personalizar modelo de conteúdo no Video Indexer":::
1. Aceda ao separador **caracteres animados** na secção de personalização do modelo.
1. Clique no **modelo Adicionar**.
1. Nomeie-o modelo e clique em introduzir para guardar o nome.

> [!NOTE]
> A melhor prática é ter um modelo de visão personalizado para cada série animada. 

### <a name="index-a-video-with-an-animated-model"></a>Indexar um vídeo com um modelo animado

Para o treino inicial, faça o upload de pelo menos dois vídeos. Cada um deve ser de preferência superior a 15 minutos, antes de esperar um bom modelo de reconhecimento. Se tiver episódios mais curtos, recomendamos o upload de pelo menos 30 minutos de conteúdo de vídeo antes do treino. Isto permitir-lhe-á fundir grupos que pertencem ao mesmo personagem de diferentes cenas e origens, e, portanto, aumentar a chance de detetar o personagem nos seguintes episódios que indexa. Para treinar um modelo em vários vídeos (episódios) é necessário indexá-los todos com o mesmo modelo de animação. 

1. Clique no botão **Upload.**
1. Escolha um vídeo para carregar (de um ficheiro ou de um URL).
1. Clique em **opções avançadas.**
1. Em **Pessoas / Personagens animados** escolhem **modelos de animação.**
1. Se tiver um modelo, será escolhido automaticamente e, se tiver vários modelos, pode escolher o relevante fora do menu suspenso.
1. Clique no upload.
1. Uma vez indexado o vídeo, verá os caracteres detetados na secção **de caracteres Animados** no painel **Insights.**

Antes de marcar e treinar o modelo, todos os personagens animados serão nomeados "Desconhecidos #X". Depois de treinar o modelo, também serão reconhecidos.

### <a name="customize-the-animated-characters-models"></a>Personalize os modelos de personagens animados

1. Nomeie os caracteres no Indexer de Vídeo.

    1. Após o modelo ter criado o grupo de caracteres, recomenda-se rever estes grupos em Visão Personalizada. 
    1. Para marcar um personagem animado no seu vídeo, vá ao **separador Insights**   e clique no botão **Editar**   no canto superior direito da janela. 
    1. No ****   painel Insights, clique em qualquer um dos caracteres animados detetados e altere os seus nomes de "#X Desconhecida" para um nome temporário (ou o nome que foi previamente atribuído à personagem). 
    1. Depois de escrever o novo nome, clique no ícone de verificação ao lado do novo nome. Isto guarda o novo nome no modelo no Video Indexer. 
1. Apenas contas pagas: Rever os grupos em Visão Personalizada 

    > [!NOTE]
    > As contas pagas que tenham acesso à sua conta De Visão Personalizada podem ver os modelos e imagens marcadas lá. Saiba mais sobre [como melhorar o seu classificador em Visão Personalizada.](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) É importante notar que o treino do modelo deve ser feito apenas através do Video Indexer (como descrito neste topid), e não através do site Da Visão Personalizada. 

    1. Vá à página **Modelos Personalizados** no Índice de Vídeo e escolha o **separador caracteres Animados.** 
    1. Clique no botão Editar para o modelo em que está a trabalhar para o gerir em Visão Personalizada. 
    1. Rever cada grupo de caracteres: 

        * Se o grupo contiver imagens não relacionadas, é aconselhável eliminá-las no website da Visão Personalizada. 
        * Se houver imagens que pertençam a um carácter diferente, altere a etiqueta nestas imagens específicas clicando na imagem, adicionando a etiqueta certa e eliminando a etiqueta errada. 
        * Se o grupo não estiver correto, o que significa que contém principalmente imagens ou imagens não-caracteres de vários caracteres, pode eliminar no site da Visão Personalizada ou em insights de Indexer de Vídeo. 
        * O algoritmo de agrupamento por vezes divide os seus personagens em diferentes grupos. Por isso, recomenda-se dar a todos os grupos que pertencem ao mesmo carácter o mesmo nome (in Video Indexer Insights), o que fará com que todos estes grupos apareçam como no website da Visão Personalizada. 
    1. Uma vez refinado o grupo, certifique-se de que o nome inicial com que o marcou reflete o carácter do grupo. 
1. Preparar o modelo 

    1. Depois de ter terminado de editar todos os nomes que quiser, precisa de treinar o modelo. 
    1. Uma vez que um personagem é treinado no modelo, será reconhecido o próximo vídeo indexado a este modelo. 
    1. Abra a página de personalização e clique no separador **caracteres Animados**   e, em seguida, clique no botão **Train** para treinar o seu modelo. Para manter a ligação entre vídeo 
    
Indexante e o modelo, não treine o modelo no website da Visão Personalizada (as contas pagas têm acesso ao website Da Visão Personalizada), apenas no Índice de Vídeo. Uma vez treinado, qualquer vídeo que seja indexado ou reindexado com este modelo reconhecerá os caracteres treinados. 

## <a name="delete-an-animated-character-and-the-model"></a>Excluir um personagem animado e o modelo

1. Apague um personagem animado.

    1. Para eliminar um personagem animado nas suas introspeções de vídeo, vá ao **separador Insights** e clique no botão **Editar** no canto superior direito da janela.
    1. Escolha o personagem animado e, em seguida, clique no botão **Eliminar** sob o seu nome.

    > [!NOTE]
    > Isto irá eliminar a perceção deste vídeo mas não irá afetar o modelo.
1. Apague um modelo.

    1. Clique no botão **de personalização** do modelo de conteúdo no menu superior e vá ao separador **caracteres Animados.**
    1. Clique no ícone de elipses à direita do modelo que pretende eliminar e, em seguida, no botão eliminar.
    
    * Conta paga: o modelo será desligado do Video Indexer e não poderá reconectá-lo.
    * Conta experimental: o modelo será também eliminado da visão aduaneira. 
    
        > [!NOTE]
        > Numa conta experimental, só tem um modelo que pode usar. Depois de apagar, não pode treinar outros modelos.

## <a name="use-the-animated-character-detection-with-api"></a>Use a deteção de caracteres animados com API 

1. Conecte uma conta Visão Personalizada.

    Se possuir uma conta paga por Video Indexer, tem de ligar primeiro uma conta De Visão Personalizada. <br/>
    Se já não tem uma conta Visão Personalizada, por favor crie uma. Para mais informações, consulte [a Visão Personalizada.](../../cognitive-services/custom-vision-service/overview.md)

    [Ligue a sua conta De Visão Personalizada utilizando a API.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)
1. Crie um modelo de personagens animados.

    Utilize o [modelo de animação create](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) API.
1. Indexar ou re-indexar um vídeo.

    Utilize a API [de re-indexação.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 
1. Personalize os modelos de personagens animados.

    Use o [modelo de animação](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) de comboio API.

### <a name="view-the-output"></a>Ver a saída

Veja os caracteres animados no ficheiro JSON gerado.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Limitações

* Atualmente, a capacidade de "identificação de animação" não é suportada na região East-Asia.
* As personagens que parecem ser pequenas ou distantes no vídeo podem não ser identificadas corretamente se a qualidade do vídeo for fraca.
* A recomendação é usar um modelo por conjunto de caracteres animados (por exemplo, por uma série animada).

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)