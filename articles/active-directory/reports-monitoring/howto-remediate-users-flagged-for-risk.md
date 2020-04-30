---
title: Utilizadores sinalizados para risco no portal de Diretório Sinuoso Azure / Microsoft Docs
description: Saiba mais sobre os utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea1360499c4f4f9b2f6cfa07e81d8f692d0353d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100792"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Remediar utilizadores sinalizados para risco no portal do Azure Active Directory

Com os relatórios de segurança no Diretório Ativo Azure (Azure AD), pode avaliar a probabilidade de contas de utilizadores comprometidas no seu ambiente. Os utilizadores sinalizados para risco são um indicador de uma conta de utilizador que pode ter sido comprometida.

A Microsoft está empenhada em ajudar a manter os seus ambientes seguros. Como parte desse compromisso, a Microsoft monitoriza continuamente atividades que são invulgares ou que são consistentes com padrões de ataques conhecidos. 

Se forem detetadas atividades incomuns que indiquem o acesso não autorizado a algumas das contas dos seus utilizadores, recebe notificações que lhe permitem tomar medidas. Isto não significa que os próprios sistemas da Microsoft tenham sido comprometidos.

## <a name="access-the-users-flagged-for-risk-report"></a>Aceda ao relatório de utilizadores sinalizados para risco

Pode rever os utilizadores sinalizados para riscos através do relatório de risco dos [utilizadores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) no portal Azure. Se não tiver a AD Azure, pode inscrever-se gratuitamente em [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD). 

Dos utilizadores sinalizados para o relatório de risco, pode tomar as seguintes ações para cada utilizador:

- Gerar uma palavra-passe temporária
- Exigir que o utilizador reponha a palavra-passe dele em segurança na próxima vez que iniciar sessão
- Dispense o risco de utilizador sem tomar qualquer ação de remediação.

Para obter mais informações, consulte [utilizadores sinalizados para relatório](concept-user-at-risk.md)de segurança de risco .

### <a name="azure-ad-subscription-for-office-365-customers"></a>Subscrição do Azure AD para os clientes do Office 365

Também pode utilizar as suas credenciais office 365 para aceder ao **Centro de Administração Azure**. Depois de ativar o acesso ao Azure AD, é redirecionado para o portal do Azure AD. Ao nível da subscrição básica, a quantidade de detalhes indicados nos relatórios é limitada. Estão disponíveis mais dados e análises para os subscritores Premium do Azure.

Para aceder aos **Utilizadores sinalizados para** relatórios de risco no centro de administração da Microsoft 365:

1.  A partir do menu de navegação no lado esquerdo, selecione **centros de administração**. 
2.  Selecione **Azure AD**.
3.  Inicie sessão no **Centro de administração do Azure Active Directory**.
4.  Se um banner for exibido na parte superior da página que diz **Confira o novo portal,** selecione o link.
4.  No menu de navegação do lado esquerdo, selecione **Azure Ative Directory**. 
5.  No painel de navegação, selecione **Utilizadores sinalizados para risco** a partir da secção de **Segurança.**

## <a name="remediation-actions"></a>Ações de remediação

Execute as ações seguintes para ajudar a retificar as contas afetadas e a proteger o seu ambiente:

1.  [Valide informações corretas](https://aka.ms/MFAValid) para a autenticação de vários fatores e reset de palavra-passe self-service. 
2.  Ativar a [autenticação de vários fatores](https://aka.ms/MFAuth) para todos os utilizadores. 
3.  Utilize este script de [reparação](https://aka.ms/remediate) para cada conta impactada, para executar automaticamente os seguintes passos: 

    a. Redefinir a palavra-passe para proteger a conta e matar sessões ativas.

    b. Remover delegados de caixas de correio.

    c. Desativar as regras de reencaminhamento de correio para domínios externos.

    d. Remover a propriedade de reencaminhamento de correio global na caixa de correio.

    e. Ativar a MFA na conta do utilizador.

    f. Definir uma complexidade de palavra-passe na conta alta.

    g. Ativar a auditoria da caixa de correio.

    h. Elabore um registo de auditoria para o administrador rever.

4. Investigar o inquilino do Office 365 e outras infraestruturas de TI, incluindo uma análise de todas as definições do inquilino, das contas de utilizador e das definições de configuração por utilizador, para possível modificação. Verificar indicadores de métodos de persistência, bem como indicadores de que um intruso poderá ter tirado partido de uma presença inicial para obter credenciais de VPN ou acesso a outros recursos da organização. 

5.  Como parte da sua investigação, considere se deve notificar as autoridades governamentais, incluindo as forças policiais.

Além disso, deve:

- Leia e implemente esta [orientação sobre o endereço de atividades incomuns](https://aka.ms/fixaccount). 
- [Ative o pipeline de auditoria](https://aka.ms/improvesecurity) para ajudá-lo a analisar a atividade no seu inquilino. Uma vez concluída, a sua loja de auditoria começa a povoar com registos de atividade. Neste momento, também pode aproveitar o recurso de [busca e investigação do Centro](https://aka.ms/sccsearch)de Segurança e Compliance. 
- Reveja as permissões de delegado e as regras de reencaminhamento de todas as caixas de correio de correio. Pode utilizar este [script do PowerShell](https://aka.ms/delegateforwardrules) para realizar esta tarefa. 

## <a name="next-steps"></a>Passos seguintes

* [Proteção de Identidade do Diretório Ativo Azure](../active-directory-identityprotection.md)
* [Utilizadores marcados em risco](concept-user-at-risk.md)
