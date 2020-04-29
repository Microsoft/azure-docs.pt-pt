---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67184495"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Criar um tópico com o portal do Azure
1. Na página **Service Bus Namespace,** selecione **Tópicos** no menu esquerdo.
2. Selecione **+ Tópico** na barra de ferramentas. 
4. Insira um **nome** para o tópico. Deixe as outras opções com os valores predefinidos.
5. Selecione **Criar**.

    ![Criar tópico](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Criar subscrições para o tópico
1. Selecione o **tópico** que criou na secção anterior. 
    
    ![Selecione tópico](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Na página Tópico de **Ônibus de Serviço,** selecione **Subscrições** do menu esquerdo e, em seguida, selecione **+ Subscrição** na barra de ferramentas. 
    
    ![Adicionar botão de subscrição](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Na página **de subscrição Criar,** introduza **S1** para **nome** para a subscrição e, em seguida, selecione **Criar**. 

    ![Criar página de subscrição](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Repita o passo anterior duas vezes para criar subscrições chamadas **S2** e **S3**.