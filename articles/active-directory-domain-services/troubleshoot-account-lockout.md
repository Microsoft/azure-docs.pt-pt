---
title: Bloqueio de conta de resolução de problemas nos Serviços de Domínio da AD azure [ Microsoft Docs
description: Aprenda a resolver problemas comuns que fazem com que as contas dos utilizadores sejam bloqueadas nos Serviços de Domínio do Diretório Ativo azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/06/2020
ms.author: iainfou
ms.openlocfilehash: 7d2e22804c06f589c7990bf8f19319b897363a93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743447"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Problemas de bloqueio de conta com um domínio gerido pelos Serviços de Domínio Da Azure AD

Para evitar repetidas tentativas de entrada maliciosas, o Azure AD DS bloqueia contas após um limiar definido. Este bloqueio de conta também pode acontecer por acidente sem um incidente de ataque de sinal. Por exemplo, se um utilizador introduzir repetidamente a palavra-passe errada ou um serviço tentar usar uma senha antiga, a conta fica bloqueada.

Este artigo de resolução de problemas descreve por que razão os bloqueios de conta acontecem e como pode configurar o comportamento e como rever as auditorias de segurança para resolver eventos de bloqueio.

## <a name="what-is-an-account-lockout"></a>O que é um bloqueio de conta?

Uma conta de utilizador em Azure AD DS é bloqueada quando um limiar definido para tentativas de entrada mal sucedidas foi cumprido. Este comportamento de bloqueio de conta foi concebido para protegê-lo de tentativas repetidas de sinal de força bruta que podem indicar um ataque digital automatizado.

**Por predefinição, se houver 5 tentativas de senha em 2 minutos, a conta fica bloqueada durante 30 minutos.**

Os limiares de bloqueio da conta predefinida são configurados utilizando a política de senha sinuosa. Se tiver um conjunto específico de requisitos, pode anular estes limiares de bloqueio de conta por defeito. No entanto, não é aconselhável aumentar os limites de limiar para tentar reduzir os bloqueios da conta de números. Problemas atiram a fonte do comportamento de bloqueio da conta primeiro.

### <a name="fine-grained-password-policy"></a>Política de senha de grãos finos

As políticas de senha de grão fino (FGPPs) permitem aplicar restrições específicas para as políticas de password e bloqueio de conta a diferentes utilizadores num domínio. A FGPP apenas afeta os utilizadores dentro de um domínio gerido pelo Azure AD DS. Os utilizadores de cloud e utilizadores de domínio sincronizados no domínio gerido pelo Azure AD DS a partir do Azure AD só são afetados pelas políticas de password dentro do Azure AD DS. As suas contas em Azure AD ou um diretório no local não são impactadas.

As políticas são distribuídas através da associação de grupos no domínio gerido pelo Azure AD DS, e quaisquer alterações que faça são aplicadas no próximo início de sessão do utilizador. Mudar a apólice não desbloqueia uma conta de utilizador que já está bloqueada.

Para obter mais informações sobre as políticas de senha de grão fino, e as diferenças entre os utilizadores criados diretamente em Azure AD DS versus sincronizado a partir de Azure AD, consulte as políticas de password e bloqueio de [conta configuradas.][configure-fgpp]

## <a name="common-account-lockout-reasons"></a>Razões comuns de bloqueio da conta

As razões mais comuns para uma conta ser bloqueada, sem qualquer intenção ou fatores maliciosos, incluem os seguintes cenários:

* **O utilizador fechou-se.**
    * Após uma recente alteração de palavra-passe, o utilizador continuou a utilizar uma senha anterior? A política de bloqueio de conta predefinida de 5 tentativas falhadas em 2 minutos pode ser causada pelo utilizador, inadvertidamente, a reexperimentar uma senha antiga.
* **Há uma aplicação ou serviço que tem uma senha antiga.**
    * Se uma conta for utilizada por aplicações ou serviços, esses recursos podem tentar iniciar sessão repetidamente utilizando uma senha antiga. Este comportamento faz com que a conta seja bloqueada.
    * Tente minimizar o uso da conta em várias aplicações ou serviços diferentes e registar onde as credenciais são usadas. Se uma palavra-passe da conta for alterada, atualize as aplicações ou serviços associados em conformidade.
* **A palavra-passe foi alterada num ambiente diferente e a nova senha ainda não se sincronizou.**
    * Se uma palavra-passe de conta for alterada fora do Azure AD DS, como num ambiente ad dS on-prem, pode levar alguns minutos para que a alteração da palavra-passe se sincronize através do Azure AD DS e em Azure AD DS.
    * Um utilizador que tenta iniciar sessão num recurso através do Azure AD DS antes de o processo de sincronização de palavras-passe ter concluído faz com que a sua conta seja bloqueada.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Bloqueios de conta de resolução de problemas com auditorias de segurança

Para resolver problemas quando ocorrerem eventos de bloqueio de conta e de onde vêm, ative auditorias de [segurança para O DS Azure AD][security-audit-events]. Os eventos de auditoria só são capturados a partir do momento em que ativar a funcionalidade. Idealmente, deve ativar auditorias de segurança *antes* que haja um problema de bloqueio de conta. Se uma conta de utilizador tiver problemas de bloqueio repetidamente, pode ativar auditorias de segurança prontas para a próxima vez que a situação ocorrer.

Uma vez ativadas as auditorias de segurança, as seguintes consultas de amostra mostram-lhe como rever *eventos*de bloqueio de conta, código *4740*.

Veja todos os eventos de bloqueio da conta nos últimos sete dias:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Veja todos os eventos de bloqueio da conta nos últimos sete dias para a conta chamada *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Veja todos os eventos de bloqueio de conta entre 26 de junho de 2019 às 9h00. e 1 de julho de 2019 meia-noite, ordenada ascendente pela data e hora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas de senha de grão fino para ajustar os limiares de bloqueio da conta, consulte as políticas de bloqueio de [palavras-passe e de conta Configurar][configure-fgpp].

Se ainda tiver problemas em juntar o seu VM ao domínio gerido pelo Azure AD DS, encontre ajuda e abra um bilhete de apoio para o [Azure Ative Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
