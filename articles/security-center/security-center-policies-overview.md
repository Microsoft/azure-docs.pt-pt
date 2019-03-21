---
title: Definições do Centro de segurança do Azure | Documentos da Microsoft
description: Configure as definições do Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 818b3e3b464ecbe8d55367ab5318436a14567263
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108768"
---
# <a name="security-center-settings"></a>Definições do Centro de Segurança
Este artigo fornece uma descrição geral das definições no Centro de segurança.

As seguintes definições podem ser contatadas em política de segurança:

- **Recolha de dados**: Determina o aprovisionamento do agente e [recolha de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) definições.
- **Política de segurança**: Determina que controla o Centro de segurança monitoriza e recomenda. Pode editar a [política de segurança](tutorial-security-policy.md) no Centro de segurança. Também pode utilizar [do Azure Policy](tutorial-security-policy.md) para criar definições novas, definir políticas adicionais e atribuir políticas a vários grupos de gestão. 
- **Notificações por e-mail**: Determina os contactos de segurança, e [notificação por e-mail](security-center-provide-security-contact-details.md) definições.
- **Escalão de preço**: Define o gratuito ou standard [seleção de preço](security-center-pricing.md). O escalão que escolhe determina as funcionalidades do Centro de Segurança que estão disponíveis para recursos no âmbito. Pode especificar um escalão para subscrições, grupos de recursos e áreas de trabalho.

> [!NOTE]
> Pode definir tudo isso por subscrição. Para áreas de trabalho, pode definir apenas a recolha de dados e o escalão de preço. Para grupos de recursos, pode definir apenas escalão de preço.
>


## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?
Centro de segurança utiliza baseada em funções controlo de acesso (RBAC), que fornece funções incorporadas que podem ser atribuídas a utilizadores, grupos e serviços no Azure. Quando os utilizadores abrem o Centro de segurança, veem apenas informações relacionadas com a recursos que aos quais têm acesso. O que significa que os utilizadores são atribuídos a função de *proprietário*, *contribuinte*, ou *leitor* para o subscrição ou grupo de recursos que um recurso pertence. Além destas funções, há duas funções específicas do Centro de Segurança:

- **Leitor de segurança**: Tem a ver direitos ao centro de segurança, que incluem recomendações, alertas, políticas e estado de funcionamento, mas eles não podem fazer alterações.
- **Administrador de segurança**: Têm os mesmos direitos de exibição como *leitor de segurança*, e também pode atualizar a política de segurança e ignorar recomendações e alertas.


## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu sobre as políticas de segurança no Centro de segurança do Azure. Para saber mais sobre o Centro de segurança do Azure, veja os artigos seguintes:

* [Definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md): Saiba como configurar políticas de segurança para as suas subscrições do Azure e grupos de recursos.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md): Saiba como as recomendações do Centro de segurança ajudá-lo a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md): Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros no Centro de segurança do Azure](security-center-partner-solutions.md): Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Segurança de dados do Centro de segurança do Azure](security-center-data-security.md): Saiba como o Centro de segurança gere e protege os dados.
* [FAQ do Centro de segurança do Azure](security-center-faq.md): Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Obtenha as notícias mais recentes de segurança do Azure e as informações.
