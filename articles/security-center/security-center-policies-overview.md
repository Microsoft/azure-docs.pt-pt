---
title: Configurações da central de segurança do Azure | Microsoft Docs
description: Defina as configurações da central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/03/2018
ms.author: memildin
ms.openlocfilehash: 4a7254d4ac67ee7d1bf203baf5741638dbc8f3dd
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201620"
---
# <a name="security-center-settings"></a>Definições do Centro de Segurança
Este artigo fornece uma visão geral das configurações na central de segurança.

As configurações a seguir podem ser acessadas em política de segurança:

- **Coleta de dados**: Determina as configurações de provisionamento de agente e de [coleta de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) .
- **Política de segurança**: Determina quais controles a central de segurança monitora e recomenda. Você pode editar a [política de segurança](tutorial-security-policy.md) na central de segurança. Você também pode usar [Azure Policy](tutorial-security-policy.md) para criar novas definições, definir políticas adicionais e atribuir políticas entre grupos de gerenciamento. 
- **Notificações por email**: Determina os contatos de segurança e as configurações de [notificação por email](security-center-provide-security-contact-details.md) .
- **Tipo de preço**: Define a seleção de [preços](security-center-pricing.md)gratuita ou padrão. O escalão que escolhe determina as funcionalidades do Centro de Segurança que estão disponíveis para recursos no âmbito. Você pode especificar uma camada para assinaturas e espaços de trabalho.

> [!NOTE]
> Você pode definir todos eles por assinatura. Para espaços de trabalho, você pode definir apenas a coleta de dados e o tipo de preço.
>


## <a name="who-can-edit-security-policies"></a>Quem pode editar políticas de segurança?
A central de segurança usa o RBAC (controle de acesso baseado em função), que fornece funções internas que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando os usuários abrem a central de segurança, eles veem apenas as informações relacionadas aos recursos aos quais eles têm acesso. Isso significa que os usuários recebem a função de *proprietário*, *colaborador*ou *leitor* à assinatura à qual o recurso pertence. Além destas funções, há duas funções específicas do Centro de Segurança:

- **Leitor de segurança**: Ter direitos de exibição para a central de segurança, que inclui recomendações, alertas, política e integridade, mas não podem fazer alterações.
- **Administrador de segurança**: Ter os mesmos direitos de exibição que o *leitor de segurança*, e eles também podem atualizar a política de segurança e ignorar recomendações e alertas.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu sobre as políticas de segurança na central de segurança do Azure. Para saber mais sobre a central de segurança do Azure, consulte os seguintes artigos:

* [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md): Saiba como configurar políticas de segurança para suas assinaturas do Azure.
* [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md): Saiba como as recomendações da central de segurança ajudam a proteger seus recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md): Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerir e responder a alertas de segurança.
* [Monitorando soluções de parceiros com a central de segurança do Azure](security-center-partner-solutions.md): Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Segurança de dados da central de segurança do Azure](security-center-data-security.md): Saiba como a central de segurança gerencia e protege os dados.
* [Perguntas frequentes da central de segurança do Azure](security-center-faq.md): Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Obtenha as últimas notícias e informações de segurança do Azure.
