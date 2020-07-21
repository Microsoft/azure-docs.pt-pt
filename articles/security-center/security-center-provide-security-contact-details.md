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
ms.openlocfilehash: 661d3845365778f7ef23cdd05b81b98c3bf84259
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519291"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>Configurar notificações de email para alertas de segurança 

Para garantir que as pessoas certas na sua organização são notificadas sobre alertas de segurança no seu ambiente, insira os seus endereços de e-mail na página **de definições de notificações de email.**

Ao configurar as suas notificações, pode configurar os e-mails a enviar a indivíduos específicos ou a qualquer pessoa com um papel RBAC específico para uma subscrição. 

Para evitar cansaço de alerta, o Centro de Segurança limita o volume de mensagens de saída. Para cada subscrição, o Centro de Segurança envia:

- um máximo de **quatro** e-mails por dia para alertas **de alta gravidade**
- um máximo de **dois** e-mails por dia para alertas **de gravidade média**
- um máximo de **um** e-mail por dia para alertas **de baixa gravidade**

## <a name="availability"></a>Disponibilidade

- Estado de libertação: **Geralmente disponível**
- Funções necessárias: **Administrador de Segurança** ou Proprietário de **Subscrição** 
- Nuvens: ✔ Nuvens comerciais ✔ Governo dos EUA (parcial) ✘ Nacional/Soberano (China Gov, Outro Gov)


## <a name="set-up-email-notifications-for-alerts"></a>Configurar notificações de email para alertas<a name="email"></a>

Pode enviar notificações por e-mail a indivíduos ou a todos os utilizadores com funções específicas de RBAC.

1. A partir da área de **definições** de & de preços do Security Center, a subscrição relevante e as **notificações de e-mail selecionadas**.

1. Defina os destinatários para as suas notificações:

    - Na lista de abandono, selecione entre as funções disponíveis.
    - E/ou insira endereços de e-mail específicos separados por vírgulas. Não há limite para o número de endereços de e-mail que pode introduzir.

1. Para aplicar as informações de contacto de segurança à sua subscrição, **selecione Guardar**.


## <a name="see-also"></a>Veja também
Para saber mais sobre alertas de segurança, consulte o seguinte:

* [Alertas de segurança - um guia de referência](alerts-reference.md) -- Conheça os alertas de segurança que pode ver no módulo de Proteção de Ameaças do Centro de Segurança Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança Azure](security-center-managing-and-responding-alerts.md) -- Aprender a gerir e responder a alertas de segurança