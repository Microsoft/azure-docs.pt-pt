---
title: Forneça detalhes de contato de segurança no Centro de Segurança Azure ! Microsoft Docs
description: Este documento mostra-lhe como fornecer dados de contacto de segurança no Centro de Segurança Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 9fbd63e1b46b837350be720fadf68777927f9bff
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904764"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>Configurar notificações de email para alertas de segurança 

Para garantir que as pessoas certas na sua organização são notificadas sobre alertas de segurança no seu ambiente, insira os seus endereços de e-mail na página **de definições de notificações de email.**

Ao configurar as suas notificações, pode configurar os e-mails a enviar a indivíduos específicos ou a qualquer pessoa com um papel Azure específico para uma subscrição. 

Para evitar cansaço de alerta, o Centro de Segurança limita o volume de mensagens de saída. Para cada subscrição, o Centro de Segurança envia:

- um máximo de **quatro** e-mails por dia para alertas **de alta gravidade**
- um máximo de **dois** e-mails por dia para alertas **de gravidade média**
- um máximo de **um** e-mail por dia para alertas **de baixa gravidade**


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Configurar os detalhes do contacto que receberá e-mails sobre alertas de segurança." :::

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|Gratuito|
|Funções e permissões necessárias:|**Administrador de Segurança**<br>**Proprietário de Assinatura** |
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) Gov dos EUA (parcial)<br>![No](./media/icons/no-icon.png) China Gov, Outro Gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>Configurar notificações de email para alertas <a name="email"></a>

Pode enviar notificações por e-mail a indivíduos ou a todos os utilizadores com funções específicas do Azure.

1. A partir da área de **definições** de & de preços do Security Center, a subscrição relevante e as **notificações de e-mail selecionadas**.

1. Defina os destinatários para as suas notificações:

    - Na lista de abandono, selecione entre as funções disponíveis.
    - E/ou insira endereços de e-mail específicos separados por vírgulas. Não há limite para o número de endereços de e-mail que pode introduzir.

1. Para aplicar as informações de contacto de segurança à sua subscrição, **selecione Guardar**.


## <a name="see-also"></a>Ver também
Para saber mais sobre alertas de segurança, consulte o seguinte:

* [Alertas de segurança - um guia de referência](alerts-reference.md) -- Conheça os alertas de segurança que pode ver no módulo de Proteção de Ameaças do Centro de Segurança Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança Azure](security-center-managing-and-responding-alerts.md) -- Aprender a gerir e responder a alertas de segurança
* [Automatização do fluxo de trabalho](workflow-automation.md) -- Automatizar respostas a alertas com lógica de notificação personalizada