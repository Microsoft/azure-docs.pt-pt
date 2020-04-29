---
title: Deteção de caracteres animados com Indexer de vídeo
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar a deteção de caracteres animados com o Indexer de Vídeo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76989914"
---
# <a name="animated-character-detection-preview"></a>Deteção de caracteres animados (pré-visualização)

O Azure Media Services Video Indexer suporta a deteção, agrupamento e reconhecimento de personagens em conteúdos animados através da integração com [a visão personalizada dos Serviços Cognitivos.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Esta funcionalidade está disponível tanto através do portal como através da API.

Depois de carregar um vídeo animado com um modelo de animação específico, o Video Indexer extrai quadros de chaves, deteta personagens animados nestes quadros, grupos de caracteres semelhantes e escolhe a melhor amostra. Em seguida, envia os caracteres agrupados para a Custom Vision que identifica personagens com base nos modelos em que foi treinado. 

Antes de começar a treinar o seu modelo, os caracteres são detetados sem nome. À medida que adiciona nomes e treina o modelo, o Indexer de Vídeo reconhecerá os caracteres e nomeá-los-á em conformidade.

## <a name="flow-diagram"></a>Diagrama de fluxo

O diagrama seguinte demonstra o fluxo do processo de deteção de caracteres animados.

![Diagrama de fluxo](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Contas

Dependendo de um tipo da sua conta de Indexer de Vídeo, diferentes conjuntos de funcionalidades estão disponíveis. Para obter informações sobre como ligar a sua conta ao Azure, consulte [Criar uma conta de Indexer](connect-to-azure.md)de Vídeo ligada ao Azure .

* Conta de teste: O Indexante de Vídeo usa uma conta de Visão Personalizada interna para criar modelo e conectá-lo à sua conta de Indexer de Vídeo. 
* Conta paga: liga a sua conta Custom Vision à sua conta De Indexer de Vídeo (se ainda não tiver uma, tem de criar uma conta primeiro).

### <a name="trial-vs-paid"></a>Julgamento vs. pago

|Funcionalidade|Avaliação|Pago|
|---|---|---|
|Conta Visão Personalizada|Gerido nos bastidores pelo Video Indexer. |A sua conta Custom Vision está ligada ao Indexer de Vídeo.|
|Número de modelos de animação|Um|Até 100 modelos por conta (limitação de Visão Personalizada).|
|Preparar o modelo|O Indexer de vídeo treina o modelo para novos personagens exemplos adicionais de caracteres existentes.|O dono da conta treina o modelo quando está pronto para fazer alterações.|
|Opções avançadas em Visão Personalizada|Sem acesso ao portal Visão Personalizada.|Você pode ajustar os modelos no portal Visão Personalizada.|

## <a name="use-the-animated-character-detection-with-portal"></a>Use a deteção de caracteres animados com portal 

Esta secção descreve os passos que precisa de tomar para começar a usar o modelo de deteção de caracteres animados. 

Uma vez que nas contas de teste a integração da Visão Personalizada é gerida pelo Indexer de Vídeo, pode começar a criar e usar o modelo de caracteres animados e saltar a seguinte secção ("Connect your Custom Vision account").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Ligue a sua conta Visão Personalizada (apenas contas pagas)

Se possuir uma conta paga pelo Indexer de Vídeo, tem de ligar primeiro uma conta Custom Vision. Se ainda não tem uma conta Custom Vision, por favor crie uma. Para mais informações, consulte [Visão Personalizada](../../cognitive-services/custom-vision-service/home.md).

> [!NOTE]
> Ambas as contas têm de estar na mesma região. A integração da Visão Personalizada não é atualmente apoiada na região japonesa.

#### <a name="connect-a-custom-vision-account-with-api"></a>Ligue uma conta Visão Personalizada com API 

Siga estes passos para ligar a sua conta De Visão Personalizada ao Indexante de Vídeo, ou para alterar a conta Visão Personalizada que está atualmente ligada ao Indexante de Vídeo:

1. Navegue para [www.customvision.ai](https://www.customvision.ai) e faça login.
1. Copiar as seguintes teclas: 

    * Chave de formação (para o recurso de formação)
    * Chave de previsão (para o recurso de previsão)
    * Ponto Final 
    * ID de recurso de previsão
    
    > [!NOTE]
    > Para fornecer todas as chaves precisa de ter dois recursos separados na Visão Personalizada, um para treino e outro para previsão.
1. Navegue e inscreva-se no [Indexer](https://vi.microsoft.com/)de Vídeo .
1. Clique no ponto de interrogação no canto superior direito da página e escolha **referência API**.
1. Certifique-se de que é subscrito à API Management clicando no separador **Produtos.** Se tiver uma API ligada, pode continuar até ao próximo passo, caso contrário, subscrever. 
1. No portal do desenvolvedor, clique na Referência Completa da **API** e navegue para **operações.**  
1. Selecione **Ligar A Conta de Visão Personalizada (PREVIEW)** e clique em **experimentá-la**.
1. Preencha os campos necessários, bem como o sinal de acesso e clique **em Enviar**. 

    Para mais informações sobre como obter o sinal de acesso do Indexer de Vídeo vá ao portal do [desenvolvedor](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?), e consulte a [documentação relevante](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Uma vez que a chamada retorne 200 ok resposta, a sua conta está conectada.
1. Para verificar a sua ligação navegando no portal [Do Indexante](https://vi.microsoft.com/)de Vídeo):
1. Clique no botão de **personalização** do modelo Conteúdo no canto superior direito.
1. Vá ao separador de **personagens animados.**
1. Assim que clicar em Gerir modelos em Visão Personalizada"**, será transferido para a conta Custom Vision que acabou de ligar.

> [!NOTE]
> Atualmente, apenas os modelos que foram criados via Indexer de Vídeo são suportados. Os modelos criados através da Custom Vision não estarão disponíveis. Além disso, a melhor prática é editar modelos que foram criados através do Indexer de Vídeo apenas através da plataforma Indexer de Vídeo, uma vez que as alterações efetuadas através da Visão Personalizada podem causar resultados não intencionais.

### <a name="create-an-animated-characters-model"></a>Criar um modelo de personagens animados

1. Aceda ao site do [Video Indexer](https://vi.microsoft.com/) e inicie sessão.
1. Clique no botão de personalização do modelo de conteúdo no canto superior direito da página.

    ![Personalização do modelo de conteúdo](./media/animated-characters-recognition/content-model-customization.png)
1. Vá ao separador **de caracteres animados** na secção de personalização do modelo.
1. Clique no **modelo Adicionar**.
1. Nomeie o seu modelo e clique em introduzir para guardar o nome.

> [!NOTE]
> A melhor prática é ter um modelo de visão personalizado para cada série animada. 

### <a name="index-a-video-with-an-animated-model"></a>Indexe um vídeo com um modelo animado

1. Clique no botão **Upload** a partir do menu superior.
1. Escolha um vídeo para carregar (a partir de um ficheiro ou url).
1. Clique em **opções avançadas**.
1. Em **pessoas / personagens animados** escolha **modelos de animação**.
1. Se tiver um modelo, será escolhido automaticamente, e se tiver vários modelos, pode escolher o relevante fora do menu suspenso.
1. Clique no upload.
1. Assim que o vídeo estiver indexado, verá si os caracteres detetados na secção de **caracteres animados** no painel **Insights.**

> [!NOTE] 
> Antes de marcar e treinar o modelo, todos os personagens animados serão nomeados "Unknown #X". Depois de treinar o modelo, também serão reconhecidos.

### <a name="customize-the-animated-characters-models"></a>Personalize os modelos de personagens animados

1. Etiqueta e treina o modelo.

    1. Marque o carácter detetado editando o seu nome. Uma vez que um personagem é treinado para o modelo, será reconhecido o próximo vídeo indexado com esse modelo. 
    1. Para marcar um personagem animado no seu vídeo, vá ao separador **Insights** e clique no botão **Editar** no canto superior direito da janela.
    1. No painel **Insights,** clique em qualquer um dos personagens animados detetados e mude os seus nomes de "Unknown #X" (ou o nome que foi previamente atribuído ao personagem).
    1. Depois de digitar o novo nome, clique no ícone de verificação ao lado do novo nome. Isto guarda o novo nome no modelo em Video Indexer.
    1. Depois de terminar de editar todos os nomes que quiser, precisa treinar o modelo.

        Abra a página de personalização e clique no separador **de caracteres animados** e, em seguida, clique no botão **Train** para treinar o seu modelo.
         
        Se tiver uma conta paga, pode clicar **nos modelos Manage no** link Visão do Cliente (como mostrado abaixo). Em seguida, será encaminhado para a página do modelo em **Visão Personalizada.**
 
        ![Personalização do modelo de conteúdo](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Uma vez treinado, qualquer vídeo que será indexado ou reindexado com esse modelo reconhecerá os caracteres treinados. 
    Contas pagas que tenham acesso à sua conta Custom Vision podem ver os modelos e imagens marcadas lá. Saiba mais sobre [melhorar o seu classificador em Visão Personalizada.](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)

1. Apague um personagem animado.

    1. Para eliminar um personagem animado nas suas ideias de vídeo, vá ao separador **Insights** e clique no botão **Editar** no canto superior direito da janela.
    1. Escolha o personagem animado e, em seguida, clique no botão **Apagar** em seu nome.

    > [!NOTE]
    > Isto eliminará a informação deste vídeo, mas não afetará o modelo.

1. Apagar um modelo.

    1. Clique no botão de **personalização** do modelo Conteúdo no menu superior e vá ao separador **de caracteres animados.**
    1. Clique no ícone de elipse à direita do modelo que pretende eliminar e, em seguida, no botão de apagar.
    
    * Conta paga: o modelo será desligado do Indexer de Vídeo e não poderá reconectá-lo.
    * Conta experimental: o modelo também será eliminado da visão aduaneira. 
    
        > [!NOTE]
        > Numa conta experimental, só tens um modelo que podes usar. Depois de apagar, não pode treinar outros modelos.

## <a name="use-the-animated-character-detection-with-api"></a>Utilize a deteção de caracteres animados com API 

1. Ligue uma conta Visão Personalizada.

    Se possuir uma conta paga pelo Indexer de Vídeo, tem de ligar primeiro uma conta Custom Vision. <br/>
    Se ainda não tem uma conta Custom Vision, por favor crie uma. Para mais informações, consulte [Visão Personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [Ligue a sua conta Visão Personalizada utilizando API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Crie um modelo de personagens animados.

    Utilize o [modelo de animação API.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)
1. Indexar ou reindexar um vídeo.

    Utilize a [API de reindexação.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 
1. Personalize os modelos de personagens animados.

    Utilize o modelo de [animação do comboio](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API.

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

* Atualmente, a capacidade de "identificação de animação" não é suportada na região leste-Asiática.
* Os caracteres que parecem ser pequenos ou distantes no vídeo podem não ser devidamente identificados se a qualidade do vídeo for fraca.
* A recomendação é usar um modelo por conjunto de personagens animados (por exemplo, por exemplo, por uma série animada).

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)
