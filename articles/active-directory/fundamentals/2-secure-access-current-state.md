---
title: Descubra o estado atual da colaboração externa com o Azure Ative Directory
description: Aprenda métodos para descobrir o estado atual da sua colaboração.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 302e4becd337c8e7b0b425a52ed46d562db5bae4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725332"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Descubra o estado atual da colaboração externa na sua organização 

Antes de descobrir o estado atual da sua colaboração externa, deverá [determinar a sua postura de segurança desejada.](1-secure-access-posture.md) Considerará as necessidades da sua organização para o controlo centralizado vs. delegado, e quaisquer metas relevantes de governação, regulação e conformidade. 

Os indivíduos da sua organização provavelmente já estão a colaborar com utilizadores de outras organizações. A colaboração pode ser através de funcionalidades em aplicações de produtividade como a Microsoft 365, por e-mail ou partilhando recursos com utilizadores externos. Os pilares do seu plano de governação formar-se-ão à medida que descobrirem 
*   os utilizadores que iniciam a colaboração externa.
*   os utilizadores e organizações externos com quem está a colaborar.
*   o acesso concedido a utilizadores externos.


## <a name="users-initiating-external-collaboration"></a>Utilizadores que iniciam colaboração externa

Os utilizadores que iniciam a colaboração externa melhor entendem as aplicações mais relevantes para a colaboração externa, e quando esse acesso deve terminar. Compreender estes utilizadores pode ajudá-lo a determinar quem deve ser delegado a convidar utilizadores externos, criar pacotes de acesso e completar revisões de acesso.

Para encontrar utilizadores que estão atualmente a colaborar, reveja o registo de auditoria da [Microsoft 365 para atividades de partilha e acesso.](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance?view=o365-worldwide#sharing-and-access-request-activities) Também pode rever o registo de auditoria da [Azure AD para obter mais informações sobre quem convidou](../external-identities/auditing-and-reporting.md) utilizadores B2B para o seu diretório.

## <a name="find-current-collaboration-partners"></a>Encontre os atuais parceiros de colaboração

Os utilizadores externos podem ser [utilizadores Azure AD B2B](../external-identities/what-is-b2b.md) (preferíveis) com credenciais geridas por parceiros ou utilizadores externos com credenciais alojadas localmente. Estes utilizadores são tipicamente (mas nem sempre) marcados com um UserType de Guest. Pode enumerar utilizadores convidados através do [Microsoft Graph API,](/graph/api/user-list?tabs=http&view=graph-rest-1.0) [PowerShell](/graph/api/user-list?tabs=http&view=graph-rest-1.0)ou do [portal Azure](../enterprise-users/users-bulk-download.md).

### <a name="use-email-domains-and-companyname-property"></a>Use domínios de e-mail e propriedade de nome de empresa

As organizações externas podem ser determinadas pelos nomes de domínio dos endereços de e-mail externos do utilizador. Se os fornecedores de identidade de consumo, como o Google, forem apoiados, isso pode não ser possível. Neste caso, recomendamos que escreva o atributo nome da empresa para identificar claramente a organização externa do utilizador.

### <a name="use-allow-or-deny-lists"></a>Utilizar listas de permitir ou negar

Considere se a sua organização quer permitir a colaboração apenas com organizações específicas. Em alternativa, considere se a sua organização quer bloquear a colaboração com organizações específicas.  Ao nível dos inquilinos, existe uma [lista de permitis ou de negação,](../external-identities/allow-deny-list.md)que pode ser usada para controlar os convites e resgates B2B globais, independentemente da fonte (por exemplo, Equipas, SharePoint e Portal Azure).
Se estiver a utilizar a gestão de direitos, também pode estender os pacotes de acesso a um subconjunto dos seus parceiros, utilizando a definição de organizações conectadas específicas, conforme mostrado abaixo.


![Screenshot de permitir a lista de negação na criação de um novo pacote de acesso.](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>Encontrar acesso a utilizadores externos

Uma vez que tenha um inventário de utilizadores e organizações externos, pode determinar o acesso concedido a estes utilizadores usando a API do Microsoft Graph para determinar a [adesão ao grupo](/graph/api/resources/groups-overview?view=graph-rest-1.0) AD do Azure ou [a atribuição de aplicações diretas](/graph/api/resources/approleassignment?view=graph-rest-1.0) em Azure AD.


### <a name="enumerate-application-specific-permissions"></a>Enumerar permissões específicas da aplicação

Também poderá realizar a enumeração de permissão específica para aplicações. Por exemplo, pode gerar programáticamente um relatório de permissão para o SharePoint Online utilizando [este script](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64).

Investigue especificamente o acesso a todas as suas aplicações sensíveis ao negócio e críticas ao negócio para que esteja plenamente ciente de qualquer acesso externo.

### <a name="detect-ad-hoc-sharing"></a>Detetar partilha de Ad Hoc
Se os seus planos de e-mail e rede o permitirem, pode investigar o conteúdo partilhado através de e-mail ou através de software não autorizado como aplicações de serviço (SaaS). [A Microsoft 365 Data Loss Protection](/microsoft-365/compliance/data-loss-prevention-policies?view=o365-worldwide) ajuda-o a identificar, prevenir e monitorizar a partilha acidental de informações sensíveis através da sua infraestrutura Microsoft 365. [O Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) pode ajudá-lo a identificar o uso de aplicações SaaS não autorizadas no seu ambiente.

## <a name="next-steps"></a>Próximos passos

Consulte os seguintes artigos sobre a garantia do acesso externo aos recursos. Recomendamos que tome as ações na ordem listada.

1. [Determine a sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descubra o seu estado atual](2-secure-access-current-state.md) (Está aqui.)

3. [Criar um plano de governação](3-secure-access-plan.md)

4. [Utilize grupos para segurança](4-secure-access-groups.md)

5. [Transição para Azure AD B2B](5-secure-access-b2b.md)

6. [Acesso seguro com Gestão de Direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com etiquetas de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Acesso seguro a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)