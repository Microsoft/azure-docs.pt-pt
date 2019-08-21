---
title: Atualizar o serviço de QnA Maker-QnA Maker
titleSuffix: Azure Cognitive Services
description: Compartilhe ou atualize seus serviços de QnA Maker para gerenciar melhor os recursos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: df4aa2d6a3c4690fb1fc38b0f4f7d49afccdd657
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640474"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Compartilhar ou atualizar seu serviço de QnA Maker
Compartilhe ou atualize seus serviços de QnA Maker para gerenciar melhor os recursos. 

Você pode optar por atualizar componentes individuais da pilha de QnA Maker após a criação inicial. Consulte os detalhes dos componentes dependentes e a seleção de SKU [aqui](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Compartilhar serviços existentes com o QnA Maker

QnA Maker cria vários recursos do Azure. Para reduzir o gerenciamento e beneficiar-se do compartilhamento de custos, use a tabela a seguir para entender o que você pode e não consegue compartilhar:

|Serviço|Partilhar|
|--|--|
|Serviços Cognitivos|X|
|Plano do serviço de aplicações|✔|
|Serviço de aplicações|X|
|Application Insights|✔|
|Serviço de pesquisa|✔|

## <a name="upgrade-qna-maker-management-sku"></a>Atualizar SKU de gerenciamento de QnA Maker

Quando precisar ter mais perguntas e respostas em sua base de dados de conhecimento, além da sua camada atual, atualize seu tipo de preço do QnA Maker Service. 

Para atualizar o SKU de gerenciamento de QnA Maker:

1. Vá para o recurso de QnA Maker no portal do Azure e selecione **tipo de preço**.

    ![QnA Maker recurso](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Escolha a SKU apropriada e pressione **selecionar**.

    ![Preços de QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Atualizar serviço de aplicativo

 Quando sua base de dados de conhecimento precisar atender a mais solicitações de seu aplicativo cliente, atualize seu tipo de preço do serviço de aplicativo.

Você pode [escalar](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou reduzir verticalmente o serviço de aplicativo.

1. Vá para o recurso serviço de aplicativo no portal do Azure e selecione as opções **escalar** vertical ou reduzir conforme necessário.

    ![Escala do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Atualizar Azure Search serviço

Quando você planeja ter muitas bases de dados de conhecimento, atualize seu tipo de preço do Azure Search Service. 

No momento, não é possível executar uma atualização in-loco do SKU do Azure Search. No entanto, você pode criar um novo recurso de Azure Search com a SKU desejada, restaurar os dados para o novo recurso e vinculá-los ao QnA Maker Stack.

1. Crie um novo recurso de Azure Search no portal do Azure e escolha a SKU desejada.

    ![QnA Maker recurso do Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Restaure os índices do seu recurso original do Azure Search para o novo. Consulte o código de exemplo de restauração de backup [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Depois que os dados forem restaurados, vá para o novo recurso do Azure Search, selecione **chaves**e anote o **nome** e a **chave de administração**.

    ![QnA Maker chaves do Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Para vincular o novo recurso de Azure Search à pilha de QnA Maker, vá para o serviço de aplicativo QnA Maker.

    ![QnA Maker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Selecione **configurações do aplicativo** e substitua os campos **AzureSearchName** e **AzureSearchAdminKey** da etapa 3.

    ![QnA Maker configuração de appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Reinicie o serviço de aplicações.

    ![Reinicialização de QnA Maker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Usar API de QnA Maker](../Quickstarts/csharp.md)
