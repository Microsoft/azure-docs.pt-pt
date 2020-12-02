---
title: Configure notificações de e-mail para alertas do Centro de Segurança Azure
description: Saiba como afinar os tipos de e-mails enviados pelo Azure Security Center para alertas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2020
ms.author: memildin
ms.openlocfilehash: 85dffd4d96a78bab9dd890d9ad37572f3e524f06
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487937"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Configure notificações de email para alertas de segurança 

Os alertas de segurança precisam de chegar às pessoas certas na sua organização. Por padrão, o Security Center envia e-mails aos proprietários de subscrições sempre que um alerta de alta gravidade é desencadeado para a sua subscrição. Esta página explica como personalizar estas notificações.

Para definir as suas preferências por e-mails de notificação, a página de **notificações** de email do Azure Security Center permite-lhe escolher:

- **_que_ deve ser notificado** - Os e-mails podem ser enviados para selecionar indivíduos ou para qualquer pessoa com uma função Azure especificada para uma subscrição. 
- **_sobre o que_ devem ser notificados** - Modifique os níveis de gravidade para os quais o Centro de Segurança deve enviar notificações.

Para evitar cansaço de alerta, o Centro de Segurança limita o volume de mensagens de saída. Para cada subscrição, o Centro de Segurança envia:

- um máximo de um e-mail por **6 horas** (4 e-mails por dia) para alertas **de alta gravidade**
- um máximo de um e-mail por **12 horas** (2 e-mails por dia) para alertas **de gravidade média**
- um máximo de um e-mail por **24 horas** para alertas **de baixa gravidade**

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Configurar os detalhes do contacto que receberá e-mails sobre alertas de segurança." :::
 
## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|Gratuito|
|Funções e permissões necessárias:|**Administrador de Segurança**<br>**Proprietário de Assinatura** |
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||


## <a name="customize-the-security-alerts-email-notifications"></a>Personalize as notificações de email alertas de segurança<a name="email"></a>

Pode enviar notificações por e-mail a indivíduos ou a todos os utilizadores com funções específicas do Azure.

1. A partir da área de **definições** de preços & do Centro de Segurança, selecione a subscrição relevante e selecione **notificações de e-mail**.

1. Defina os destinatários das suas notificações com uma ou ambas as opções:

    - Na lista de abandono, selecione entre as funções disponíveis.
    - Introduza endereços de e-mail específicos separados por vírgulas. Não há limite para o número de endereços de e-mail que pode introduzir.

1. Para aplicar as informações de contacto de segurança à sua subscrição, **selecione Guardar**.


## <a name="see-also"></a>Ver também
Para saber mais sobre alertas de segurança, consulte as seguintes páginas:

- [Alertas de segurança - um guia](alerts-reference.md)de referência --Saiba mais sobre os alertas de segurança que pode ver no módulo de Proteção de Ameaças do Centro de Segurança Azure
- [Gerir e responder a alertas de segurança no Azure Security Center](security-center-managing-and-responding-alerts.md)--Saiba como gerir e responder a alertas de segurança
- [Automatização do fluxo de trabalho](workflow-automation.md)--Automatizar respostas a alertas com lógica de notificação personalizada