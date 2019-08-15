---
title: Usuários sinalizados para risco no portal de Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1412cacd453de01f1937fbcf5e712afdac88def
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989703"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Remediar utilizadores sinalizados para risco no portal do Azure Active Directory

Com os relatórios de segurança no Azure Active Directory (Azure AD), você pode medir a probabilidade de contas de usuário comprometidas em seu ambiente. Um usuário sinalizado como risco é um indicador de uma conta de usuário que pode ter sido comprometida.

A Microsoft está empenhada em ajudar a manter os seus ambientes seguros. Como parte desse compromisso, a Microsoft monitoriza continuamente atividades que são invulgares ou que são consistentes com padrões de ataques conhecidos. 

Se atividades incomuns que podem indicar acesso não autorizado a algumas das contas de seus usuários forem detectadas, você receberá notificações permitindo que você tome uma providência. Isso não significa que os próprios sistemas da Microsoft foram comprometidos.

## <a name="access-the-users-flagged-for-risk-report"></a>Aceda ao relatório de utilizadores sinalizados para risco

Você pode revisar os usuários sinalizados por risco por meio do [relatório usuários em risco](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) no portal do Azure. Se você não tiver o Azure AD, poderá se inscrever gratuitamente em [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD). 

No relatório usuários sinalizados para risco, você pode executar as seguintes ações para cada usuário:

- Gerar palavra-passe temporária
- Exigir que o utilizador reponha a palavra-passe dele em segurança na próxima vez que iniciar sessão
- Dispense o risco de utilizador sem tomar qualquer ação de remediação.

Para obter mais informações, consulte [usuários sinalizados para o relatório de segurança de risco](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Subscrição do Azure AD para os clientes do Office 365

Você também pode usar suas credenciais do Office 365 para acessar o **centro de administração do Azure**. Depois de ativar o acesso ao Azure AD, é redirecionado para o portal do Azure AD. Ao nível da subscrição básica, a quantidade de detalhes indicados nos relatórios é limitada. Estão disponíveis mais dados e análises para os subscritores Premium do Azure.

Para acessar os **usuários sinalizados para** relatórios de risco no centro de administração Microsoft 365:

1.  No menu de navegação no lado esquerdo, selecione **centros de administração**. 
2.  Selecione **Azure ad**.
3.  Inicie sessão no **Centro de administração do Azure Active Directory**.
4.  Se uma faixa for exibida na parte superior da página que diz **verificar o novo portal**, selecione o link.
4.  No menu de navegação no lado esquerdo, selecione **Azure Active Directory**. 
5.  No painel de navegação, selecione **usuários sinalizados para risco** na seção **segurança** .

## <a name="remediation-actions"></a>Ações de remediação

Execute as ações seguintes para ajudar a retificar as contas afetadas e a proteger o seu ambiente:

1.  [Valide as informações corretas](https://aka.ms/MFAValid) para a autenticação multifator e a redefinição de senha de autoatendimento. 
2.  [Habilite a autenticação](https://aka.ms/MFAuth) multifator para todos os usuários. 
3.  Use esse [script de correção](https://aka.ms/remediate) para cada conta afetada, para executar automaticamente as seguintes etapas: 

    a. Redefina a senha para proteger a conta e eliminar as sessões ativas.

    b. Remover delegados de caixas de correio.

    c. Desativar as regras de reencaminhamento de correio para domínios externos.

    d. Remover a propriedade de reencaminhamento de correio global na caixa de correio.

    e. Ativar a MFA na conta do utilizador.

    f. Definir uma complexidade de palavra-passe na conta alta.

    g. Ativar a auditoria da caixa de correio.

    h. Produzir um log de auditoria para que o administrador examine.

4. Investigar o inquilino do Office 365 e outras infraestruturas de TI, incluindo uma análise de todas as definições do inquilino, das contas de utilizador e das definições de configuração por utilizador, para possível modificação. Verificar indicadores de métodos de persistência, bem como indicadores de que um intruso poderá ter tirado partido de uma presença inicial para obter credenciais de VPN ou acesso a outros recursos da organização. 

5.  Como parte de sua investigação, considere se você deve notificar as autoridades governamentais, incluindo a imposição da lei.

Além disso, deve:

- Leia e implemente essas [diretrizes sobre como lidar com atividades incomuns](https://aka.ms/fixaccount). 
- [Habilite o pipeline de auditoria](https://aka.ms/improvesecurity) para ajudá-lo a analisar a atividade em seu locatário. Depois de concluído, o repositório de auditoria começa a popular com os logs de atividade. Neste ponto, você também pode aproveitar o [recurso de pesquisa e investigação do centro de conformidade e segurança](https://aka.ms/sccsearch). 
- Use esse [script para habilitar a auditoria de caixa de correio](https://aka.ms/mailboxaudit1) para todas as suas contas. 
- Reveja as permissões de delegado e as regras de reencaminhamento de todas as caixas de correio de correio. Pode utilizar este [script do PowerShell](https://aka.ms/delegateforwardrules) para realizar esta tarefa. 

## <a name="next-steps"></a>Passos seguintes

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
* [Usuários sinalizados para risco](concept-user-at-risk.md)
