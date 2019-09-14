---
title: Detecção de caracteres animados com Video Indexer
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar a detecção de caracteres animados com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/05/2019
ms.author: juliako
ms.openlocfilehash: 8158611678acc23d89bab377c170759c6bf9677f
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962639"
---
# <a name="animated-character-detection-preview"></a>Detecção de caracteres animados (versão prévia)

Os serviços de mídia do Azure Video Indexer dão suporte à detecção, agrupamento e reconhecimento de caracteres em conteúdo animado por meio da integração com a [visão personalizada dos serviços cognitivas](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Essa funcionalidade está disponível por meio do portal e pela API.

Depois de carregar um vídeo animado com um modelo de animação específico, Video Indexer extrai quadros-chave, detecta caracteres animados nesses quadros, agrupa caracteres semelhantes e escolhe o melhor exemplo. Em seguida, ele envia os caracteres agrupados para Visão Personalizada que identificam caracteres com base nos modelos em que foram treinados. 

Antes de começar a treinar seu modelo, os caracteres são detectados namelessly. À medida que você adiciona nomes e treina o modelo, o Video Indexer reconhecerá os caracteres e os nomeará de acordo.

## <a name="flow-diagram"></a>Diagrama de fluxo

O diagrama a seguir demonstra o fluxo do processo de detecção de caracteres animados.

![Diagrama de fluxo](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Contas

Dependendo de um tipo de sua conta de Video Indexer, diferentes conjuntos de recursos estarão disponíveis. Para obter informações sobre como conectar sua conta ao Azure, consulte [criar uma conta de video indexer conectada ao Azure](connect-to-azure.md).

* Conta de avaliação: Video Indexer usa uma conta de Visão Personalizada interna para criar o modelo e conectá-lo à sua conta do Video Indexer. 
* Conta paga: você conecta sua conta do Visão Personalizada à sua conta do Video Indexer (se você ainda não tiver uma, precisará criar uma conta primeiro).

### <a name="trial-vs-paid"></a>Avaliação vs. pagas

|Funcionalidade|Avaliação|Pago|
|---|---|---|
|Conta de Visão Personalizada|Gerenciado por trás dos bastidores por Video Indexer. |Sua conta de Visão Personalizada está conectada ao Video Indexer.|
|Número de modelos de animação|Só|Até 100 modelos por conta (limitação de Visão Personalizada).|
|Treinando o modelo|Video Indexer treina o modelo para obter novos exemplos de caracteres adicionais de caracteres existentes.|O proprietário da conta treina o modelo quando eles estão prontos para fazer alterações.|
|Opções avançadas no Visão Personalizada|Sem acesso ao portal de Visão Personalizada.|Você pode ajustar os modelos por conta própria no portal de Visão Personalizada.|

## <a name="use-the-animated-character-detection-with-portal"></a>Usar a detecção de caracteres animados com o portal 

Esta seção descreve as etapas que você precisa seguir para começar a usar o modelo de detecção de caracteres animados.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Conectar sua conta do Visão Personalizada (somente contas pagas)

Se você possui uma conta paga Video Indexer, você precisa conectar uma conta de Visão Personalizada primeiro. Se você ainda não tem uma conta de Visão Personalizada, crie uma. Para obter mais informações, consulte [visão personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

### <a name="create-an-animated-characters-model"></a>Criar um modelo de caracteres animados

1. Aceda ao site do [Video Indexer](https://vi.microsoft.com/) e inicie sessão.
1. Clique no botão personalização do modelo de conteúdo no canto superior direito da página.

    ![Personalização do modelo de conteúdo](./media/animated-characters-recognition/content-model-customization.png)
1. Vá para a guia **caracteres animados** na seção personalização do modelo.
1. Clique em **Adicionar modelo**.
1. Nomeie-o como modelo e clique em Enter para salvar o nome.

> [!NOTE]
> A prática recomendada é ter um modelo de visão personalizada para cada série animada. 

### <a name="index-a-video-with-an-animated-model"></a>Indexar um vídeo com um modelo animado

1. Clique no botão **carregar** no menu superior.
1. Escolha um vídeo para carregar (de um arquivo ou de uma URL).
1. Clique em **Opções avançadas**.
1. Em **pessoas/caracteres animados** , escolha **modelos de animação**.
1. Se você tiver um modelo, ele será escolhido automaticamente e, se você tiver vários modelos, poderá escolher o que é relevante no menu suspenso.
1. Clique em carregar.
1. Depois que o vídeo for indexado, você verá os caracteres detectados na seção **caracteres animados** no painel **insights** .

> [!NOTE] 
> Antes de marcar e treinar o modelo, todos os caracteres animados serão nomeados "desconhecido #X". Depois de treinar o modelo, eles também serão reconhecidos.

### <a name="customize-the-animated-characters-models"></a>Personalizar os modelos de caracteres animados

1. Marque e treine o modelo.

    1. Marcar o caractere detectado editando seu nome. Depois que um caractere é treinado no modelo, ele será reconhecido no próximo vídeo indexado com esse modelo. 
    1. Para marcar um caractere animado em seu vídeo, vá para a guia **insights** e clique no botão **Editar** no canto superior direito da janela.
    1. No painel **insights** , clique em qualquer um dos caracteres animados detectados e altere seus nomes de "desconhecido #X" (ou o nome que foi atribuído anteriormente ao caractere).
    1. Depois de digitar o novo nome, clique no ícone de verificação ao lado do novo nome. Isso salva o novo nome no modelo em Video Indexer.
    1. Depois de concluir a edição de todos os nomes desejados, você precisará treinar o modelo.

        Abra a página personalização e clique na guia **caracteres animados** . Para o modelo relevante, clique no link **Editar no visão personalizada** . Em seguida, você será encaminhado para a página do modelo em Visão Personalizada. Clique no botão **treinar** para treinar seu modelo. 
    1. Uma vez treinado, qualquer vídeo que será indexado ou reindexado com esse modelo reconhecerá os caracteres treinados. 
    Contas pagas que têm acesso à sua conta de Visão Personalizada podem ver os modelos e imagens marcadas ali. Saiba mais sobre como [melhorar seu classificador em visão personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Excluir um caractere animado.

    1. Para excluir um caractere animado em suas ideias de vídeo, vá para a guia **insights** e clique no botão **Editar** no canto superior direito da janela.
    1. Escolha o caractere animado e, em seguida, clique no botão **excluir** em seu nome.

    > [!NOTE]
    > Isso excluirá o insight deste vídeo, mas não afetará o modelo.

1. Excluir um modelo.

    1. Clique no botão **personalização do modelo de conteúdo** no menu superior e vá para a guia **caracteres animados** .
    1. Clique no ícone de reticências à direita do modelo que você deseja excluir e, em seguida, no botão excluir.
    
    * Conta paga: o modelo será desconectado da Video Indexer e você não poderá reconectá-lo.
    * Conta de avaliação: o modelo também será excluído da visão alfandegária. 
    
        > [!NOTE]
        > Em uma conta de avaliação, você tem apenas um modelo que pode usar. Depois de excluí-lo, você não pode treinar outros modelos.

## <a name="use-the-animated-character-detection-with-api"></a>Usar a detecção de caracteres animados com a API 

1. Conecte uma conta de Visão Personalizada.

    Se você possui uma conta paga Video Indexer, você precisa conectar uma conta de Visão Personalizada primeiro. <br/>
    Se você ainda não tem uma conta de Visão Personalizada, crie uma. Para obter mais informações, consulte [visão personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [Conecte sua conta do visão personalizada usando a API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Crie um modelo de caracteres animados.

    Use a API [criar modelo de animação](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) .
1. Indexar ou reindexar um vídeo.

    Use a API de [reindexação](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) . 
1. Personalize os modelos de caracteres animados.

    Use a API de [modelo de animação Train](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) .

### <a name="view-the-output"></a>Exibir a saída

Consulte os caracteres animados no arquivo JSON gerado.

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

* Atualmente, a funcionalidade de "identificação de animação" não tem suporte na região leste-Ásia.
* Os caracteres que parecem ser pequenos ou distantes no vídeo podem não ser identificados corretamente se a qualidade do vídeo for ruim.
* A recomendação é usar um modelo por conjunto de caracteres animados (por exemplo, por uma série animada).

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)
