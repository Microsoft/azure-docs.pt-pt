---
title: Utentes sinalizados por risco no portal Azure Ative Directory Microsoft Docs
description: Saiba mais sobre os utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23bd7a26bc6215f50a2860ab4aac84f1b46301f5
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92308947"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Remediar utilizadores sinalizados para risco no portal do Azure Active Directory

Com os relatórios de segurança no Azure Ative Directory (Azure AD), pode avaliar a probabilidade de contas de utilizador comprometidas no seu ambiente. Os utilizadores sinalizados para risco são um indicador de uma conta de utilizador que pode ter sido comprometida.

A Microsoft está empenhada em ajudar a manter os seus ambientes seguros. Como parte desse compromisso, a Microsoft monitoriza continuamente atividades que são invulgares ou que são consistentes com padrões de ataques conhecidos. 

Se forem detetadas atividades incomuns que possam indicar acesso não autorizado a algumas das contas dos seus utilizadores, recebe notificações que lhe permitem tomar medidas. Isto não significa que os próprios sistemas da Microsoft tenham sido comprometidos.

## <a name="access-the-users-flagged-for-risk-report"></a>Aceda ao relatório de utilizadores sinalizados para risco

Pode rever os utilizadores sinalizados para o risco através dos [utilizadores em relatório](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) de risco no portal Azure. Se não tiver Azure AD, pode inscrever-se gratuitamente em [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) . 

A partir dos utilizadores sinalizados para relatório de risco, pode tomar as seguintes ações para cada utilizador:

- Gerar uma palavra-passe temporária
- Exigir que o utilizador reponha a palavra-passe dele em segurança na próxima vez que iniciar sessão
- Dispense o risco de utilizador sem tomar qualquer ação de remediação.

Para obter mais informações, consulte [os Utilizadores sinalizados para o relatório de segurança do risco](../identity-protection/overview-identity-protection.md).

### <a name="azure-ad-subscription-for-microsoft-365-customers"></a>Subscrição AD AD da Azure para clientes Microsoft 365

Também pode utilizar as suas credenciais Microsoft 365 para aceder ao **Azure Admin Center**. Depois de ativar o acesso ao Azure AD, é redirecionado para o portal do Azure AD. Ao nível da subscrição básica, a quantidade de detalhes indicados nos relatórios é limitada. Estão disponíveis mais dados e análises para os subscritores Premium do Azure.

Para aceder aos **Utilizadores sinalizados para** relatórios de risco no centro de administração microsoft 365:

1.  A partir do menu de navegação do lado esquerdo, selecione **centros de administração**. 
2.  Selecione **Azure Ad**.
3.  Inicie sessão no **Centro de administração do Azure Active Directory**.
4.  Se for apresentado um banner na parte superior da página que diz **Check out the new portal**, selecione o link.
4.  No menu de navegação do lado esquerdo, selecione **Azure Ative Directory**. 
5.  No painel de navegação, selecione **Utilizadores sinalizados para o risco** a partir da secção **de Segurança.**

## <a name="remediation-actions"></a>Ações de remediação

Execute as ações seguintes para ajudar a retificar as contas afetadas e a proteger o seu ambiente:

1.  [Validar as informações corretas](https://aka.ms/MFAValid) para a autenticação de vários fatores e para o reset da palavra-passe de autosserviço. 
2.  [Ativar a autenticação de vários fatores](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication) para todos os utilizadores. 
3.  Utilize este [roteiro de remediação](https://aka.ms/remediate) para cada conta impactada, para executar automaticamente os seguintes passos: 

    a. Reinicie a palavra-passe para proteger a conta e eliminar sessões ativas.

    b. Remover delegados de caixas de correio.

    c. Desativar as regras de reencaminhamento de correio para domínios externos.

    d. Remover a propriedade de reencaminhamento de correio global na caixa de correio.

    e. Ativar a MFA na conta do utilizador.

    f. Definir uma complexidade de palavra-passe na conta alta.

    exemplo, Ativar a auditoria da caixa de correio.

    h. Produzir um registo de auditoria para o administrador rever.

4. Investigue o seu inquilino Microsoft 365 e outras infraestruturas de TI, incluindo uma revisão de todas as definições de inquilinos, contas de utilizador e as definições de configuração por utilizador para possível modificação. Verificar indicadores de métodos de persistência, bem como indicadores de que um intruso poderá ter tirado partido de uma presença inicial para obter credenciais de VPN ou acesso a outros recursos da organização. 

5.  Como parte da sua investigação, considere se deve notificar as autoridades governamentais, incluindo as autoridades policiais.

Além disso, deve:

- Leia e implemente esta [orientação sobre a abordagem de atividades incomuns.](/archive/blogs/office365security/how-to-fix-a-compromised-hacked-microsoft-office-365-account) 
- [Habilitar o pipeline de auditoria](/archive/blogs/office365security/using-office-365-activity-data-to-improve-your-cybersecurity-stance-and-capability) para ajudá-lo a analisar a atividade no seu inquilino. Uma vez concluída, a sua loja de auditoria começa a encher-se de registos de atividades. Neste momento, você também pode aproveitar o [recurso de pesquisa e investigação do Centro de Segurança e Conformidade.](https://aka.ms/sccsearch) 
- Reveja as permissões de delegado e as regras de reencaminhamento de todas as caixas de correio de correio. Pode utilizar este [script do PowerShell](https://aka.ms/delegateforwardrules) para realizar esta tarefa. 

## <a name="next-steps"></a>Passos seguintes

* [Proteção de Identidade do Diretório Ativo Azure](../identity-protection/overview-identity-protection.md)
* [Utilizadores marcados em risco](../identity-protection/overview-identity-protection.md)