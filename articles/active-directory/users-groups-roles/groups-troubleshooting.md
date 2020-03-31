---
title: Corrigir problemas com membros dinâmicos do grupo - Azure AD / Microsoft Docs
description: Dicas de resolução de problemas para a adesão ao grupo dinâmico no Diretório Ativo do Azure
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026547"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Problemas de resolução e resolução de problemas de grupos

## <a name="troubleshooting-group-creation-issues"></a>Problemas de criação de grupos

**Desativei a criação de grupos de segurança no portal Azure, mas os grupos ainda podem ser criados através** da Powershell O **Utilizador pode criar grupos de segurança em portais Azure** que se ajustam no portal Azure controla se os utilizadores não administradores podem ou não criar grupos de segurança no painel de acesso ou no portal Azure. Não controla a criação de grupos de segurança através da Powershell.

Para desativar a criação de grupo para utilizadores não administradores na Powershell:
1. Verifique se os utilizadores não administradores estão autorizados a criar grupos:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Se regressar, `UsersPermissionToCreateGroupsEnabled : True`os utilizadores não administradores podem criar grupos. Para desativar esta funcionalidade:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Recebi um erro de grupos max ao tentar criar um Grupo Dinâmico em Powershell**<br/>
Se receber uma mensagem no Powershell indicando que as políticas dinâmicas do grupo max permitiram a contagem de _grupos alcançada,_ isto significa que atingiu o limite máximo para grupos Dinâmicos no seu inquilino. O número máximo de grupos Dinâmicos por inquilino é de 5.000.

Para criar novos grupos Dinâmicos, primeiro terá de eliminar alguns grupos Dinâmicos existentes. Não há como aumentar o limite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Resolver problemas de associações dinâmicas a grupos

**Configurei uma regra sobre um grupo, mas nenhum membro é atualizado no grupo**<br/>
1. Verifique os valores dos atributos do utilizador ou do dispositivo na regra. Certifique-se de que há utilizadores que satisfaçam a regra. Para os dispositivos, verifique as propriedades do dispositivo para garantir que quaisquer atributos sincronizados contenham os valores esperados.<br/>
2. Verifique o estado de processamento da adesão para confirmar se está completo. Pode verificar o estado de processamento de [membros](groups-create-rule.md#check-processing-status-for-a-rule) e a última data atualizada na página **'Visão Geral** do grupo'.

Se tudo estiver bem, por favor, dê algum tempo para o grupo povoar. Dependendo do tamanho do seu inquilino, o grupo pode demorar até 24 horas a ser preenchido pela primeira vez, ou após uma alteração de regra.

**Configurei uma regra, mas agora os membros existentes da regra são removidos**<br/>Este comportamento está previsto. Os membros existentes do grupo são removidos quando uma regra é ativada ou alterada. Os utilizadores devolvidos da avaliação da regra são adicionados como membros ao grupo.

**Não vejo mudanças de filiação instantaneamente quando adiciono ou mudo uma regra, por que não?**<br/>A avaliação dedicada da adesão é feita periodicamente num processo de fundo assíncrono. O tempo que o processo demora é determinado pelo número de utilizadores no seu diretório e pelo tamanho do grupo criado como resultado da regra. Normalmente, os diretórios com um pequeno número de utilizadores verão a adesão ao grupo mudar em menos de alguns minutos. Os diretórios com um grande número de utilizadores podem demorar 30 minutos ou mais para povoar.

**Como posso forçar o grupo a ser processado agora?**<br/>
Atualmente, não há forma de ativar automaticamente o grupo a ser processado a pedido. No entanto, pode desencadear manualmente o reprocessamento atualizando a regra de adesão para adicionar um espaço branco no final.  

**Encontrei um erro de processamento de regras.**<br/>A tabela que se segue enumera erros de regra de adesão dinâmicos comuns e como corrigi-los.

| Erro de parser de regras | Utilização de erros | Utilização corrigida |
| --- | --- | --- |
| Erro: Atributo não suportado. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Certifique-se de que o atributo está na [lista de propriedades suportadas](groups-dynamic-membership.md#supported-properties). |
| Erro: O operador não é suportado no atributo. |(user.accountEnabled -contém verdadeiro) |(user.accountEnabled -eq true)<br/><br/>O operador utilizado não é suportado para o tipo de propriedade (neste exemplo, - contém não pode ser utilizado em boolean do tipo). Utilize os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação de consulta. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match@domain.ext"* ") | 1. Operador desaparecido. Utilização - e ou -ou dois se juntam predicados<br>(user.department -eq "Sales") -ou (user.department -eq "Marketing")<br>2. Erro na expressão regular utilizado com -match<br>(user.userPrincipalName -match ".*@domain.ext")<br>ou alternativamente: (user.userPrincipalName@domain.ext-match " $") |

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)