---
title: Atualizar o serviço QnA Maker - QnA Maker
titleSuffix: Azure Cognitive Services
description: Partilhe ou atualizar os seus serviços do QnA Maker para gerir os recursos melhor.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: tulasim
ms.openlocfilehash: cb38fdcfa8cbbfa0b90141c2de2280ff1ef08c3b
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437992"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Partilhar ou atualizar o serviço QnA Maker
Partilhe ou atualizar os seus serviços do QnA Maker para gerir os recursos melhor. 

Pode optar por atualizar componentes individuais da pilha do QnA Maker após a criação inicial. Ver os detalhes dos componentes dependentes e seleção de SKU [aqui](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Partilhar os serviços existentes com a ferramenta QnA Maker

A ferramenta QnA Maker cria vários recursos do Azure. Para reduzir a gestão e beneficiamos do compartilhamento de custos, utilize a tabela seguinte para compreender o que pode e não é possível partilhar:

|Serviço|Partilhar|
|--|--|
|Serviços Cognitivos|X|
|Plano do serviço de aplicações|✔|
|Serviço de aplicações|X|
|Application Insights|✔|
|Serviço de pesquisa|✔|

## <a name="upgrade-qna-maker-management-sku"></a>Atualizar a ferramenta QnA Maker gestão SKU

Quando tem de ter mais perguntas e respostas na sua base de dados de conhecimento, para além do escalão atual, atualize o serviço QnA Maker escalão de preço. 

Para atualizar o SKU de gerenciamento do QnA Maker:

1. Aceda ao seu recurso do QnA Maker no portal do Azure e selecione **escalão de preço**.

    ![Recursos do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Selecione o SKU de apropriados e prima **selecione**.

    ![Preços do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Atualizar serviço de aplicações

 Quando a sua base de dados de conhecimento precisa ser usado mais pedidos a partir da sua aplicação de cliente, atualize o escalão de preço do serviço de aplicações.

Pode [aumentar verticalmente](https://docs.microsoft.com/azure/app-service/web-sites-scale) ou reduzir verticalmente o serviço de aplicações.

1. Vá para o recurso de serviço de aplicações no portal do Azure e selecione **aumentar verticalmente** ou **reduzir verticalmente** opções conforme necessário.

    ![Dimensionamento do serviço de aplicações de QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Atualizar o serviço Azure Search

Quando planeia ter muitas bases de dados de conhecimento, atualize o serviço de Azure Search escalão de preço. 

Atualmente não é possível executar um ponto de partida no SKU de pesquisa de atualização do Azure. No entanto, pode criar um novo recurso de pesquisa do Azure com o SKU desejado, restaurar os dados para o novo recurso e, em seguida, ligá-lo para a pilha do QnA Maker.

1. Criar um novo recurso de pesquisa do Azure no portal do Azure e selecione o SKU pretendido.

    ![Recurso de pesquisa do Azure do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Restaure os índices do seu recurso de pesquisa do Azure original para a nova. Consulte o código de exemplo de restauro de cópia de segurança [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Assim que os dados são restaurados, aceda ao seu recurso de pesquisa do Azure nova, selecione **chaves**e tome nota da **nome** e o **chave Admin**.

    ![Chaves de pesquisa do Azure do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Para ligar o novo recurso de pesquisa do Azure para a pilha do QnA Maker, vá para o serviço de aplicações do QnA Maker.

    ![Serviço de aplicações do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Selecione **as configurações do aplicativo** e substitua o **AzureSearchName** e **AzureSearchAdminKey** campos do passo 3.

    ![Definição de serviço de aplicações do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Reinicie o serviço de aplicações.

    ![Reiniciar o serviço de aplicações do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Utilizar a API do QnA Maker](../Quickstarts/csharp.md)
