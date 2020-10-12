---
title: Deteção de caracteres animados com Indexador de Vídeo
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar a deteção de caracteres animados com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: 3b449f68b25fce19dc034d2a0db0ae5ce8a28ec7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047493"
---
# <a name="animated-character-detection-preview"></a>Deteção de carateres animados (pré-visualização)

O Azure Media Services Video Indexer suporta a deteção, agrupamento e reconhecimento de personagens em conteúdo animado através da integração com [a visão personalizada dos Serviços Cognitivos.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Esta funcionalidade encontra-se disponível tanto através do portal como através da API.

Depois de carregar um vídeo animado com um modelo de animação específico, o Video Indexer extrai quadros-chave, deteta personagens animados nestes quadros, agruma personagem semelhante e escolhe a melhor amostra. Em seguida, envia os caracteres agrupados para a Visão Personalizada que identifica caracteres com base nos modelos em que foi treinado. 

Antes de começar a treinar o seu modelo, os caracteres são detetados sem nome. À medida que adiciona nomes e treina o modelo, o Video Indexer reconhecerá os caracteres e nomeá-los-á em conformidade.

## <a name="flow-diagram"></a>Diagrama de fluxo

O diagrama seguinte demonstra o fluxo do processo de deteção de caracteres animados.

![Diagrama de fluxo](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Contas

Dependendo de um tipo da sua conta de Indexer de Vídeo, estão disponíveis diferentes conjuntos de funcionalidades. Para obter informações sobre como ligar a sua conta ao Azure, consulte [Criar uma conta de Indexer de Vídeo ligada ao Azure](connect-to-azure.md).

* Conta de ensaio: O Indexante de Vídeo utiliza uma conta interna de Visão Personalizada para criar um modelo e conectá-lo à sua conta de Indexer de Vídeo. 
* Conta paga: liga a sua conta De Visão Personalizada à sua conta de Indexer de Vídeo (se ainda não tiver uma, tem de criar uma conta primeiro).

### <a name="trial-vs-paid"></a>Julgamento vs. pago

|Funcionalidade|Avaliação|Pago|
|---|---|---|
|Conta Visão Personalizada|Gerido nos bastidores por Video Indexer. |A sua conta De Visão Personalizada está ligada ao Indexador de Vídeo.|
|Número de modelos de animação|Um|Até 100 modelos por conta (limitação de Visão Personalizada).|
|Preparar o modelo|O Video Indexer treina o modelo para novos personagens exemplos adicionais de caracteres existentes.|O proprietário da conta treina o modelo quando está pronto para fazer alterações.|
|Opções avançadas em Visão Personalizada|Sem acesso ao portal Visão Personalizada.|Pode ajustar os modelos sozinho no portal Visão Personalizada.|

## <a name="use-the-animated-character-detection-with-portal"></a>Use a deteção de caracteres animados com portal 

Esta secção descreve os passos que precisa de tomar para começar a usar o modelo de deteção de caracteres animados. 

Uma vez que nas contas de teste a integração Custom Vision é gerida pelo Video Indexer, pode começar a criar e usar o modelo de caracteres animados e saltar a seguinte secção ("Conecte a sua conta De Visão Personalizada").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Conecte a sua conta De Visão Personalizada (apenas contas pagas)

Se possuir uma conta paga por Video Indexer, tem de ligar primeiro uma conta De Visão Personalizada. Se já não tem uma conta Visão Personalizada, por favor crie uma. Para mais informações, consulte [a Visão Personalizada.](../../cognitive-services/custom-vision-service/home.md)

> [!NOTE]
> Ambas as contas têm de estar na mesma região. A integração da Visão Personalizada não é atualmente apoiada na região japonesa.

#### <a name="connect-a-custom-vision-account-with-api"></a>Conecte uma conta de Visão Personalizada com a API 

Siga estes passos para ligar a conta De Visão Personalizada ao Indexador de Vídeo ou para alterar a conta Visão Personalizada que está atualmente ligada ao Indexador de Vídeo:

1. Navegue para [www.customvision.ai](https://www.customvision.ai) e faça login.
1. Copiar as seguintes teclas: 

    * Chave de formação (para o recurso de formação)
    * Chave de previsão (para o recurso de previsão)
    * Ponto final 
    * ID de recurso de previsão
    
    > [!NOTE]
    > Para fornecer todas as chaves precisa de ter dois recursos separados na Visão Personalizada, um para treinar e outro para previsão.
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
1. Assim que clicar em Gerir modelos em Visão Personalizada"***, será transferido para a conta Visão Personalizada que acabou de ligar.

> [!NOTE]
> Atualmente, apenas os modelos que foram criados através do Video Indexer são suportados. Os modelos criados através da Visão Personalizada não estarão disponíveis. Além disso, a melhor prática é editar modelos que foram criados através do Video Indexer apenas através da plataforma Video Indexer, uma vez que as alterações escorraçada através da Visão Personalizada podem causar resultados não intencionais.

### <a name="create-an-animated-characters-model"></a>Crie um modelo de personagens animados

1. Aceda ao site do [Video Indexer](https://vi.microsoft.com/) e inicie sessão.
1. Clique no botão de personalização do modelo de conteúdo no canto superior direito da página.

    ![Personalização do modelo de conteúdo](./media/animated-characters-recognition/content-model-customization.png)
1. Aceda ao separador **caracteres animados** na secção de personalização do modelo.
1. Clique no **modelo Adicionar**.
1. Nomeie-o modelo e clique em introduzir para guardar o nome.

> [!NOTE]
> A melhor prática é ter um modelo de visão personalizado para cada série animada. 

### <a name="index-a-video-with-an-animated-model"></a>Indexar um vídeo com um modelo animado

1. Clique no botão **Upload** a partir do menu superior.
1. Escolha um vídeo para carregar (de um ficheiro ou de um URL).
1. Clique em **opções avançadas.**
1. Em **Pessoas / Personagens animados** escolhem **modelos de animação.**
1. Se tiver um modelo, será escolhido automaticamente e, se tiver vários modelos, pode escolher o relevante fora do menu suspenso.
1. Clique no upload.
1. Uma vez indexado o vídeo, verá os caracteres detetados na secção **de caracteres Animados** no painel **Insights.**

> [!NOTE] 
> Antes de marcar e treinar o modelo, todos os personagens animados serão nomeados "Desconhecidos #X". Depois de treinar o modelo, também serão reconhecidos.

### <a name="customize-the-animated-characters-models"></a>Personalize os modelos de personagens animados

1. Etiqueta e treina o modelo.

    1. Marque o carácter detetado editando o seu nome. Uma vez que um personagem é treinado no modelo, será reconhecido o próximo vídeo indexado a este modelo. 
    1. Para marcar um personagem animado no seu vídeo, vá ao **separador Insights** e clique no botão **Editar** no canto superior direito da janela.
    1. No painel **Insights,** clique em qualquer um dos caracteres animados detetados e altere os seus nomes de "Unknown #X" (ou o nome que foi previamente atribuído à personagem).
    1. Depois de escrever o novo nome, clique no ícone de verificação ao lado do novo nome. Isto guarda o novo nome no modelo no Video Indexer.
    1. Depois de ter terminado de editar todos os nomes que quiser, precisa de treinar o modelo.

        Abra a página de personalização e clique no separador **caracteres Animados** e, em seguida, clique no botão **Train** para treinar o seu modelo.
         
        Se tiver uma conta paga, pode clicar **nos modelos 'Gerir' no** link Visão do Cliente (como mostrado abaixo). Em seguida, será reencaminhado para a página do modelo em **Visão Personalizada**.
 
        ![Personalização do modelo de conteúdo](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Uma vez treinado, qualquer vídeo que seja indexado ou reindexado com este modelo reconhecerá os caracteres treinados. 
    As contas pagas que tenham acesso à sua conta De Visão Personalizada podem ver os modelos e imagens marcadas lá. Saiba mais sobre [como melhorar o seu classificador em Visão Personalizada.](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)

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
    Se já não tem uma conta Visão Personalizada, por favor crie uma. Para mais informações, consulte [a Visão Personalizada.](../../cognitive-services/custom-vision-service/home.md)

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
