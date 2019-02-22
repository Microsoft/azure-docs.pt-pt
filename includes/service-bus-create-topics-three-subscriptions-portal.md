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
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588675"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Criar um tópico com o portal do Azure
1. Sobre o **espaço de nomes do Service Bus** página, selecione **tópicos** no menu da esquerda.
2. Selecione **+ tópico** na barra de ferramentas. 
4. Introduza um **nome** para o tópico. Deixe as outras opções com os valores predefinidos.
5. Selecione **Criar**.

    ![Criar tópico](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Criar subscrições para o tópico
1. Selecione o **tópico** que criou na secção anterior. 
    
    ![Selecione o tópico](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Sobre o **tópico do Service Bus** página, selecione **subscrições** no menu à esquerda e, em seguida, selecione **+ subscrição** na barra de ferramentas. 
    
    ![Adicionar botão de subscrição](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Sobre o **criar subscrição** página, introduza **S1** para **nome** da subscrição e, em seguida, selecione **criar**. 

    ![Criar a página de subscrição](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Repita o passo anterior duas vezes para criar subscrições com o nome **S2** e **S3**.