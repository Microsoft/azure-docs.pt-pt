---
title: Gerenciamento entre locatários na central de segurança do Azure | Microsoft Docs
description: " Saiba como ativar a recolha de dados no Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b8d27178a75cb39c7d7769f2db6a193292a3f9f1
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782551"
---
# <a name="cross-tenant-management-in-security-center"></a>Gerenciamento entre locatários na central de segurança

O gerenciamento entre locatários permite exibir e gerenciar a postura de segurança de vários locatários na central de segurança, aproveitando o [Gerenciamento de recursos delegados do Azure](../lighthouse/concepts/azure-delegated-resource-management.md) . Gerencie vários locatários com eficiência, de uma única exibição, sem precisar entrar no diretório de cada locatário.

- Os provedores de serviços podem gerenciar a postura de segurança dos recursos, para vários clientes, de dentro de seu próprio locatário.

- As equipes de segurança de organizações com vários locatários podem exibir e gerenciar sua postura de segurança em um único local

  ![Gerenciamento de locatários cruzados](./media/security-center-cross-tenant-management/cross-tenant-security-center.png)

## <a name="set-up-cross-tenant-management"></a>Configurar o gerenciamento de locatários cruzados

Configure o gerenciamento de locatários cruzados delegando acesso a recursos de locatários gerenciados para seu próprio locatário usando o [Gerenciamento de recursos delegado do Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

> [!NOTE]
> O gerenciamento de recursos delegado do Azure é um dos principais componentes do Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Como o gerenciamento entre locatários funciona na central de segurança

Você pode revisar e gerenciar assinaturas em vários locatários da mesma maneira que gerencia várias assinaturas em um único locatário.

Na barra de menus superior, clique no ícone de filtro e selecione as assinaturas, no diretório de cada locatário, que você gostaria de exibir.

  ![Filtrar locatários](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

As exibições e ações são basicamente as mesmas. Eis alguns exemplos:

- **Gerenciar políticas de segurança**: De uma exibição, gerencie a postura de segurança de muitos recursos com [políticas](tutorial-security-policy.md), execute ações com recomendações de segurança e colete e gerencie dados relacionados à segurança. Por exemplo, na imagem a seguir, há quatro diretórios de locatário diferentes (Doug. Lora,, Microsoft, SSO, Wilde Company) e cada locatário tem assinaturas. As assinaturas selecionadas dos diferentes locatários estão listadas nesta lista de gerenciamento de política.

     ![Gerenciamento de políticas entre locatários](./media/security-center-cross-tenant-management/cross-tenant-policy.png)

- **Aprimore a pontuação segura e a postura de conformidade**: A visibilidade entre locatários permite que você veja a postura geral de segurança de todos os seus locatários e onde e como melhorar melhor a [Pontuação segura](security-center-secure-score.md) e a [postura de conformidade](security-center-compliance-dashboard.md) para cada um deles, como visto na imagem a seguir.

     ![Classificação de segurança](./media/security-center-cross-tenant-management/cross-tenant-secure-score.png)

- **Corrigir recomendações**: Monitore e corrija uma [recomendação](security-center-recommendations.md) para muitos recursos de vários locatários de uma só vez (como visto na imagem abaixo). Você pode então lidar imediatamente com as vulnerabilidades que apresentam o maior risco em todos os locatários.

  ![Gerenciamento de recomendações entre locatários](./media/security-center-cross-tenant-management/cross-tenant-recommendation.png)

- **Gerenciar alertas**: Detectar [alertas](security-center-alerts-overview.md) em vários locatários diferentes. Execute ações em recursos que estão fora de conformidade com etapas de [correção](security-center-managing-and-responding-alerts.md)acionáveis.

- **Gerencie recursos avançados de defesa em nuvem e muito mais**: Gerencie os vários serviços de detecção e proteção de ameaças, como [acesso à VM just-in-time (JIT)](security-center-just-in-time.md), [proteção de rede adaptável](security-center-adaptive-network-hardening.md), [controles de aplicativo adaptáveis](security-center-adaptive-application.md)e muito mais.

## <a name="next-steps"></a>Passos seguintes
Este artigo explica como o gerenciamento entre locatários funciona na central de segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Fortaleça sua postura de segurança com a central de segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.
* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
