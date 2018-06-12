---
title: Conector de SMTP no Azure Logic Apps | Microsoft Docs
description: Crie aplicações lógicas com o App service do Azure. Ligar ao SMTP para enviar correio eletrónico.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 516110abc1786d99bc719d47d61475cdc2ebcc4b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296072"
---
# <a name="get-started-with-the-smtp-connector"></a>Começar a utilizar o conector de SMTP
Ligar ao SMTP para enviar correio eletrónico.

Para utilizar [qualquer conector](apis-list.md), terá primeiro de criar uma aplicação lógica. Pode começar a utilizar pelo [criar uma aplicação lógica agora](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-smtp"></a>Ligar ao SMTP
Antes da aplicação lógica pode aceder a qualquer serviço, terá primeiro de criar um *ligação* ao serviço. A [ligação](connectors-overview.md) fornece conectividade entre uma aplicação lógica e outro serviço. Por exemplo, para ligar a SMTP, terá primeiro um SMTP *ligação*. Para criar uma ligação, introduza as credenciais que normalmente utiliza para aceder ao serviço que ligar. Por isso, no exemplo SMTP, introduza as credenciais para o nome da ligação, endereço do servidor SMTP e informações de início de sessão de utilizador para criar a ligação ao SMTP.  

### <a name="create-a-connection-to-smtp"></a>Criar uma ligação a SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Utilizar um acionador de SMTP
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre acionadores](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Neste exemplo, o SMTP não tem um acionador próprios. Por isso, utilize o **Salesforce - quando é criado um objecto** acionador. Este acionador ativa quando é criado um novo objeto no Salesforce. Neste exemplo, este tem configurá-lo para que sempre que é criado um novo fabrico no Salesforce, um *enviar correio eletrónico* ação ocorre através do conector de SMTP com uma notificação de fabrico de novo a ser criada.

1. Introduza *salesforce* na caixa de pesquisa no designer de aplicações lógicas, em seguida, selecione o **Salesforce - quando é criado um objecto** acionador.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. O **quando é criado um objecto** controlo é apresentado.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Selecione o **tipo de objeto** , em seguida, selecione *levar* da lista de objetos. Neste passo, está a criar um acionador que notifica a sua aplicação lógica sempre que é criado um novo fabrico no Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. O acionador foi criado.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Utilizar uma ação de SMTP
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre as ações](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Agora que foi adicionado o acionador, utilize os seguintes passos para adicionar uma ação de SMTP que ocorre quando é criado um novo fabrico no Salesforce.

1. Selecione **+ novo passo** para adicionar a ação que gostaria de tomar quando é criada uma antecedência de novo.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Selecione **adicionar uma ação**. Este é aberta a caixa de pesquisa, onde pode procurar qualquer ação pretende efetuar.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Introduza *smtp* para procurar ações relacionadas com o SMTP.  
4. Selecione **SMTP - enviar correio eletrónico** como a ação a tomar quando é criada a antecedência de novo. É aberto o bloco de controlo de ação. Terá de estabelecer a ligação de smtp no bloco de estruturador se não o fez, anteriormente.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Introduza as informações do e-mail pretendido no **SMTP - enviar correio eletrónico** bloco.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Guarde o trabalho para ativar o fluxo de trabalho.  

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/smtpconnector/).

## <a name="more-connectors"></a>Mais conectores
Volte à [lista APIs](apis-list.md).