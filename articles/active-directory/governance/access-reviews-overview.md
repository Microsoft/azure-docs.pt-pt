---
title: O que são as revisões de acesso? -Azure Active Directory | Microsoft Docs
description: Usando as revisões de Azure Active Directory acesso, você pode controlar a associação de grupo e o acesso ao aplicativo para atender às iniciativas de governança, gerenciamento de riscos e conformidade em sua organização.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/05/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65def17622f026aa4869a4c60e7cb5146d56c5b0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389531"
---
# <a name="what-are-azure-ad-access-reviews"></a>O que são as revisões de acesso do Azure AD?

As revisões de acesso do Azure Active Directory (Azure AD) permitem que as organizações gerenciem com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas as pessoas certas tenham acesso contínuo.

Veja um vídeo que fornece uma visão geral rápida das revisões de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Por que as revisões de acesso são importantes?

O Azure AD permite que você colabore internamente em sua organização e com usuários de organizações externas, como parceiros. Os usuários podem ingressar em grupos, convidar convidados, conectar-se a aplicativos de nuvem e trabalhar remotamente de seus dispositivos pessoais ou de trabalho. A conveniência de aproveitar o poder do autoatendimento levou a uma necessidade de melhores recursos de gerenciamento de acesso.

- À medida que novos funcionários se unem, como você garante que eles tenham o acesso certo para serem produtivos?
- À medida que as pessoas movem equipes ou deixam a empresa, como você garante que o acesso antigo seja removido, especialmente quando ele envolver convidados?
- Direitos de acesso excessivos podem levar a descobertas de auditoria e comprometimentos conforme indicam a falta de controle sobre o acesso.
- Você precisa envolver proativamente os proprietários dos recursos para garantir que eles analisem regularmente quem tem acesso aos seus recursos.

## <a name="when-to-use-access-reviews"></a>Quando usar as revisões de acesso?

- **Muitos usuários em funções privilegiadas:** É uma boa ideia verificar quantos usuários têm acesso administrativo, quantos deles são administradores globais e se há algum convidado ou parceiro que não tenha sido removido depois de ser atribuído a uma tarefa de administração. Você pode recertificar os usuários de atribuição de função nas [funções do Azure ad](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , como administradores globais, ou [funções de recursos do Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , como administrador de acesso do usuário na experiência do [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **Quando a automação é inviável:** Você pode criar regras para associação dinâmica em grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estiverem no Azure AD ou se os usuários ainda precisarem de acesso depois de deixar o grupo para treinar sua substituição? Em seguida, você pode criar uma análise nesse grupo para garantir que aqueles que ainda precisam de acesso tenham acesso contínuo.
- **Quando um grupo é usado para uma nova finalidade:** Se você tiver um grupo que será sincronizado com o Azure AD, ou se você planeja habilitar o Salesforce do aplicativo para todos no grupo de equipe de vendas, seria útil pedir ao proprietário do grupo para examinar a associação de grupo antes de o grupo ser usado em um risco diferente nteúdo.
- **Acesso a dados críticos para os negócios:** para determinados recursos, pode ser necessário pedir às pessoas fora dele para sair regularmente e dar uma justificativa sobre por que eles precisam de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma política:** Em um mundo ideal, todos os usuários seguirão as políticas de acesso para proteger o acesso aos recursos da sua organização. No entanto, às vezes há casos comerciais que exigem que você faça exceções. Como administrador de ti, você pode gerenciar essa tarefa, evitar a supervisão de exceções de política e fornecer aos auditores uma prova de que essas exceções são revisadas regularmente.
- **Peça aos proprietários do grupo para confirmar que eles ainda precisam de convidados em seus grupos:** O acesso do funcionário pode ser automatizado com algum IAM local, mas não convidados. Se um grupo dá acesso aos convidados ao conteúdo de negócios confidenciais, é responsabilidade do proprietário do grupo confirmar se os convidados ainda têm uma necessidade comercial legítima de acesso.
- **Revisar recorrer periodicamente:** Você pode configurar revisões de acesso recorrentes de usuários em conjunto frequências, como semanal, mensal, trimestral ou anual, e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar o acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

## <a name="where-do-you-create-reviews"></a>Onde você cria análises?

Dependendo do que você deseja examinar, você criará sua análise de acesso nas revisões de acesso do Azure AD, nos aplicativos do Azure AD Enterprise (em versão prévia) ou no Azure AD PIM.

| Direitos de acesso de usuários | Os revisores podem ser | Revisão criada em | Experiência do revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros do grupo do Office | Revisores especificados</br>Proprietários do grupo</br>Análise automática | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a um aplicativo conectado | Revisores especificados</br>Análise automática | Revisões de acesso do Azure AD</br>Aplicativos do Azure AD Enterprise (em versão prévia) | Painel de acesso |
| Função do Azure AD | Revisores especificados</br>Análise automática | [PIM do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |
| Função de recurso do Azure | Revisores especificados</br>Análise automática | [PIM do Azure AD](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |


## <a name="onboard-access-reviews"></a>Revisões de acesso integrado

Para carregar as revisões de acesso, siga estas etapas.

1. Como administrador global ou administrador de usuários, entre no [portal do Azure](https://portal.azure.com) em que você deseja usar as revisões de acesso.

1. No painel de navegação esquerdo, clique em **Azure Active Directory**.

1. No menu à esquerda, clique em **governança de identidade**.

1. Clique em **revisões de acesso**.
 
    ![Página inicial de revisões de acesso](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. Na página, clique no botão **integrado agora** .
    
      ![Revisões de acesso integradas](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>Saiba mais sobre as revisões de acesso

Para saber mais sobre como criar e executar revisões de acesso, Assista a esta breve demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se você estiver pronto para implantar revisões de acesso em sua organização, siga estas etapas no vídeo para carregar, treinar seus administradores e criar sua primeira análise de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="which-users-must-have-licenses"></a>Quais usuários devem ter licenças?

Cada usuário que interage com as revisões de acesso deve ter uma licença paga de Azure AD Premium P2. Alguns exemplos:

- Administradores que criam uma revisão de acesso
- Proprietários de grupo que executam uma revisão de acesso
- Usuários atribuídos como revisores
- Usuários que executam uma autoanálise

Você também pode pedir que os usuários convidados revisem seu próprio acesso. Para cada licença paga do Azure AD Premium P2 que você atribui a um dos usuários da sua organização, você pode usar o B2B (Business-to-Business) do Azure AD para convidar até cinco usuários convidados sob a concessão do usuário externo. Esses usuários convidados também podem usar Azure AD Premium recursos P2. Para obter mais informações, consulte [diretrizes de licenciamento da colaboração B2B do Azure ad](../b2b/licensing-guidance.md).

Aqui estão alguns cenários de exemplo para ajudá-lo a determinar o número de licenças que você deve ter.

| Cenário | Cálculo | Número necessário de licenças |
| --- | --- | --- |
| Um administrador cria uma revisão de acesso do grupo A com 500 usuários. Atribui 3 proprietários de grupo como revisores. | 1 licença para o administrador + 3 licenças para cada proprietário do grupo como revisores. | 4 |
| Um administrador cria uma revisão de acesso do grupo A com 500 usuários. O torna uma análise automática. | 1 licença para as licenças de administrador + 500 para cada usuário como revisores automáticos. | 501 |
| Um administrador cria uma revisão de acesso do grupo B com 5 usuários e 25 usuários convidados. O torna uma análise automática. | 1 licença para o administrador + 5 licenças para cada usuário como revisores automáticos.<br/>(os usuários convidados são abordados na proporção 1:5 necessária) | 6 |
| Um administrador cria uma revisão de acesso do grupo C com 5 usuários e 108 usuários convidados. O torna uma análise automática. | 1 licença para o administrador + 5 licenças para cada usuário como autoviewers + 16 licenças adicionais para cobrir todos os usuários convidados 108 na proporção 1:5 necessária.<br/>1 + 5 = 6 licenças, que abrangem 5 @ no__t-06 = 30 usuários convidados. Para os restantes (108-5 @ no__t-06) = 78 usuários convidados, 78/5 = 16 licenças adicionais são necessárias. Portanto, no total, são necessárias 6 + 16 = 22 licenças. | 22 |

Para obter informações sobre como atribuir licenças aos seus usos, consulte [atribuir ou remover licenças usando o portal de Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
