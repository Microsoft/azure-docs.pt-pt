---
title: Utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fc2aca2b785329b6faebd37c94493269d65c22b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182987"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Remediar utilizadores sinalizados para risco no portal do Azure Active Directory

Com os relatórios de segurança no Azure Active Directory (Azure AD), pode avaliar a probabilidade de contas de utilizador comprometidas no seu ambiente. Um utilizador sinalizado para risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

A Microsoft está empenhada em ajudar a manter os seus ambientes seguros. Como parte desse compromisso, a Microsoft monitoriza continuamente atividades que são invulgares ou que são consistentes com padrões de ataques conhecidos. 

Se forem detetadas atividades invulgares que possam indicar acesso não autorizado a algumas das suas contas de utilizador, receberá notificações que lhe permitem tomar medidas. Isso não significa que os sistemas da Microsoft tem sido comprometidos.

## <a name="access-the-users-flagged-for-risk-report"></a>Aceda ao relatório de utilizadores sinalizados para risco

Pode rever os utilizadores sinalizados para risco através da [os utilizadores no relatório de risco](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) no portal do Azure. Se não tiver o Azure AD, pode inscrever-se gratuitamente em [ https://aka.ms/AccessAAD ](https://aka.ms/AccessAAD). 

Dos utilizadores sinalizados no relatório de risco, pode efetuar as seguintes ações para cada utilizador:

- Gerar uma palavra-passe temporária
- Exigir que o utilizador reponha a palavra-passe dele em segurança na próxima vez que iniciar sessão
- Dispense o risco de utilizador sem tomar qualquer ação de remediação.

Para obter mais informações, consulte [utilizadores sinalizados no relatório de segurança de risco de](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Subscrição do Azure AD para os clientes do Office 365

Também pode utilizar as suas credenciais do Office 365 para aceder a **Centro de administração do Azure**. Depois de ativar o acesso ao Azure AD, é redirecionado para o portal do Azure AD. Ao nível da subscrição básica, a quantidade de detalhes indicados nos relatórios é limitada. Estão disponíveis mais dados e análises para os subscritores Premium do Azure.

Para aceder a **utilizadores sinalizados para risco** relatórios no Centro de administração do Microsoft 365:

1.  A partir do menu de navegação no lado esquerdo, selecione **do Centro de administração**. 
2.  Selecione **do Azure AD**.
3.  Inicie sessão no **Centro de administração do Azure Active Directory**.
4.  Se é apresentada uma faixa na parte superior da página que diz **confira o novo portal**, selecione a ligação.
4.  No menu de navegação no lado esquerdo, selecione **do Azure Active Directory**. 
5.  No painel de navegação, selecione **utilizadores sinalizados para risco** partir do **segurança** secção.

## <a name="remediation-actions"></a>Ações de remediação

Execute as ações seguintes para ajudar a retificar as contas afetadas e a proteger o seu ambiente:

1.  [Validar as informações corretas](https://aka.ms/MFAValid) para autenticação multifator e a palavra-passe self-service repor. 
2.  [Ativar a autenticação multifator](https://aka.ms/MFAuth) para todos os utilizadores. 
3.  Utilize esta opção [script de remediação](https://aka.ms/remediate) para todas as contas afetadas, para realizar automaticamente os seguintes passos: 

    a. Repor palavra-passe para proteger a conta e eliminar sessões ativas.

    b. Remover delegados de caixas de correio.

    c. Desativar as regras de reencaminhamento de correio para domínios externos.

    d. Remover a propriedade de reencaminhamento de correio global na caixa de correio.

    e. Ativar a MFA na conta do utilizador.

    f. Definir uma complexidade de palavra-passe na conta alta.

    g. Ativar a auditoria da caixa de correio.

    h. Produza um log de auditoria para o administrador rever.

4. Investigar o inquilino do Office 365 e outras infraestruturas de TI, incluindo uma análise de todas as definições do inquilino, das contas de utilizador e das definições de configuração por utilizador, para possível modificação. Verificar indicadores de métodos de persistência, bem como indicadores de que um intruso poderá ter tirado partido de uma presença inicial para obter credenciais de VPN ou acesso a outros recursos da organização. 

5.  Como parte da sua investigação, considere se deve notificar as autoridades, incluindo a aplicação da lei.

Além disso, deve:

- Ler e implementar estas [documentação de orientação sobre a abordagem a atividades invulgares](https://aka.ms/fixaccount). 
- [Ativar o pipeline de auditoria](https://aka.ms/improvesecurity) para ajudá-lo a analisar a atividade no seu inquilino. Depois de concluído, o arquivo de auditoria começa a ser preenchido com os registos de atividades. Neste ponto, também pode aproveitar o [recursos search and investigation do Security and Compliance Center](https://aka.ms/sccsearch). 
- Utilize esta opção [script para ativar a auditoria da caixa de correio](https://aka.ms/mailboxaudit1) para todas as suas contas. 
- Reveja as permissões de delegado e as regras de reencaminhamento de todas as caixas de correio de correio. Pode utilizar este [script do PowerShell](https://aka.ms/delegateforwardrules) para realizar esta tarefa. 

## <a name="next-steps"></a>Passos Seguintes

* [O Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
* [Utilizadores sinalizados para risco](concept-user-at-risk.md)
