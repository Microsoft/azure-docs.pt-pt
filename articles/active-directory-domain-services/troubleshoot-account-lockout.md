---
title: Bloqueio de conta de resolução de problemas nos Serviços de Domínio Azure AD Microsoft Docs
description: Saiba como resolver problemas comuns que fazem com que as contas dos utilizadores sejam bloqueadas nos Serviços de Domínio do Diretório Ativo do Azure.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 340811ee1c518cdccb5bbb0ae9b5f215f5564cfa
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967618"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Problemas de bloqueio de conta de resolução de problemas com um domínio gerido por serviços de domínio do diretório ativo Azure

Para evitar repetidas tentativas de entrada maliciosas, um Azure Ative Directory Domain Services (Azure AD DS) geriu contas de bloqueios de domínio após um limiar definido. Este bloqueio de conta também pode acontecer por acidente sem um incidente de ataque de sinal. Por exemplo, se um utilizador introduzir repetidamente a palavra-passe errada ou se um serviço tentar utilizar uma senha antiga, a conta fica bloqueada.

Este artigo de resolução de problemas descreve por que os bloqueios de contas acontecem e como você pode configurar o comportamento, e como rever auditorias de segurança para resolver eventos de bloqueio de problemas.

## <a name="what-is-an-account-lockout"></a>O que é um bloqueio de conta?

Uma conta de utilizador num domínio gerido Azure AD DS é bloqueada quando um limiar definido para tentativas de entrada mal sucedidas foi cumprido. Este comportamento de bloqueio de conta foi concebido para protegê-lo de tentativas de inscrição repetidas de força bruta que podem indicar um ataque digital automatizado.

**Por padrão, se houver 5 tentativas de senha estragada em 2 minutos, a conta fica bloqueada durante 30 minutos.**

Os limiares de bloqueio da conta predefinidos são configurados utilizando a política de senha de grão fino. Se tiver um conjunto específico de requisitos, pode anular estes limiares de bloqueio de conta predefinidos. No entanto, não é recomendado aumentar os limites de limiar para tentar reduzir os bloqueios de conta de número. Resolver problemas na origem do comportamento de bloqueio de conta primeiro.

### <a name="fine-grained-password-policy"></a>Política de senha de grão fino

As políticas de palavras-passe de grão fino (FGPPs) permitem aplicar restrições específicas para políticas de bloqueio de passwords e conta a diferentes utilizadores num domínio. O FGPP apenas afeta os utilizadores dentro de um domínio gerido. Os utilizadores de nuvem e utilizadores de domínio sincronizados no domínio gerido a partir do Azure AD são apenas afetados pelas políticas de palavra-passe dentro do domínio gerido. As suas contas no Azure AD ou num diretório no local não são impactadas.

As políticas são distribuídas através da associação de grupo no domínio gerido, e quaisquer alterações que escote são aplicadas no próximo início de sôr.000. Mudar a política não desbloqueia uma conta de utilizador que já está bloqueada.

Para obter mais informações sobre as políticas de palavras-passe de grãos finos e as diferenças entre utilizadores criados diretamente em Azure AD DS versus sincronizado a partir de Azure AD, consulte as políticas de bloqueio de [senha e conta de configuração.][configure-fgpp]

## <a name="common-account-lockout-reasons"></a>Razões comuns de bloqueio de contas

As razões mais comuns para que uma conta seja bloqueada, sem qualquer intenção ou fatores maliciosos, incluem os seguintes cenários:

* **O utilizador trancou-se lá fora.**
    * Após uma recente alteração da palavra-passe, o utilizador continuou a utilizar uma palavra-passe anterior? A política de bloqueio de conta padrão de cinco tentativas falhadas em 2 minutos pode ser causada pelo utilizador, inadvertidamente, redando uma senha antiga.
* **Há uma aplicação ou serviço que tem uma senha antiga.**
    * Se uma conta for utilizada por aplicações ou serviços, esses recursos podem tentar repetidamente iniciar sômposições através de uma senha antiga. Este comportamento faz com que a conta seja bloqueada.
    * Tente minimizar o uso da conta em várias aplicações ou serviços diferentes e registar onde as credenciais são usadas. Se uma palavra-passe de conta for alterada, atualize as aplicações ou serviços associados em conformidade.
* **A palavra-passe foi alterada num ambiente diferente e a nova palavra-passe ainda não foi sincronizada.**
    * Se uma palavra-passe de conta for alterada fora do domínio gerido, como num ambiente DS AD pré-pré-m, pode levar alguns minutos para a alteração da palavra-passe sincronizar através do Azure AD e entrar no domínio gerido.
    * Um utilizador que tente iniciar súm numa fonte no domínio gerido antes de o processo de sincronização da palavra-passe ter sido concluído faz com que a sua conta seja bloqueada.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Bloqueios de conta de resolução de problemas com auditorias de segurança

Para resolver problemas quando ocorrerem eventos de bloqueio de conta e de onde vêm, ative as auditorias de [segurança para a Azure AD DS][security-audit-events]. Os eventos de auditoria só são capturados a partir do momento em que ativar a funcionalidade. Idealmente, deve permitir auditorias de segurança *antes* de haver um problema de bloqueio de conta para resolver problemas. Se uma conta de utilizador tiver problemas de bloqueio repetidamente, pode ativar as auditorias de segurança prontas para a próxima vez que a situação ocorrer.

Depois de ter ativado as auditorias de segurança, as seguintes consultas de amostra mostram-lhe como rever *eventos de bloqueio de conta*, código *4740*.

Veja todos os eventos de bloqueio de conta nos últimos sete dias:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Veja todos os eventos de bloqueio de conta nos últimos sete dias para a conta chamada *Driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Veja todos os eventos de bloqueio de conta entre 26 de junho de 2020 às 9:00. e 1 de julho de 2020 meia-noite, classificado subindo pela data e hora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre políticas de senha de grão fino para ajustar os limiares de bloqueio de conta, consulte [as políticas de bloqueio de senha e de bloqueio de conta][configure-fgpp].

Se ainda tiver problemas em juntar o seu VM ao domínio gerido, [encontre ajuda e abra um bilhete de apoio para o Azure Ative Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
