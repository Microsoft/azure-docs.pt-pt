---
title: Corrigir problemas com membros dinâmicos do grupo - Azure AD / Microsoft Docs
description: Dicas de resolução de problemas para a adesão dinâmica do grupo no Azure Ative Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8730ac8aa6a6056db67613f2ac8decf11740c467
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376693"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Resolução de problemas e resolver problemas de grupos

## <a name="troubleshooting-group-creation-issues"></a>Problemas de criação de grupos de resolução de problemas

**Desativado a criação de grupos de segurança no portal Azure, mas os grupos ainda podem ser criados através do Powershell** O Utilizador pode criar grupos de segurança na definição **de portais Azure** no portal Azure, se os utilizadores não administrados podem ou não criar grupos de segurança no painel De acesso ou no portal Azure. Não controla a criação de grupos de segurança via Powershell.

Para desativar a criação de grupos para utilizadores não administrativos em Powershell:
1. Verifique se os utilizadores não administrativos podem criar grupos:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Se retornar, `UsersPermissionToCreateGroupsEnabled : True` os utilizadores não administrativos podem criar grupos. Para desativar esta funcionalidade:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Recebi um erro de grupos máximos ao tentar criar um Grupo Dinâmico em Powershell**<br/>
Se receber uma mensagem em Powershell indicando _as políticas de grupo dinâmicas que o max permitiu que os grupos contassem,_ isto significa que atingiu o limite máximo para grupos Dinâmicos na sua organização. O número máximo de grupos dinâmicos por organização é de 5.000.

Para criar novos grupos Dinâmicos, primeiro terá de eliminar alguns grupos Dinâmicos existentes. Não há como aumentar o limite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Resolver problemas de associações dinâmicas a grupos

**Eu configurei uma regra sobre um grupo, mas nenhum membro é atualizado no grupo**<br/>
1. Verifique os valores dos atributos do utilizador ou do dispositivo na regra. Certifique-se de que existem utilizadores que satisfazem a regra. Para os dispositivos, verifique as propriedades do dispositivo para garantir que quaisquer atributos sincronizados contenham os valores esperados.<br/>
2. Verifique o estado de processamento da adesão para confirmar se está completo. Pode verificar o estado de processamento da [adesão](groups-create-rule.md#check-processing-status-for-a-rule) e a última data atualizada na página **'Visão Geral'** do grupo.

Se tudo parece bem, por favor, permita que o grupo povoe. Dependendo do tamanho da sua organização Azure AD, o grupo pode demorar até 24 horas para povoar pela primeira vez ou após uma mudança de regras.

**Eu en configurava uma regra, mas agora os membros existentes da regra são removidos**<br/>Este comportamento está previsto. Os membros existentes do grupo são removidos quando uma regra é ativada ou alterada. Os utilizadores devolvidos da avaliação da regra são adicionados como membros do grupo.

**Não vejo mudanças de filiação instantaneamente quando adiciono ou mudo uma regra, porque não?**<br/>A avaliação dedicada da adesão é feita periodicamente num processo de fundo assíncronos. O tempo que o processo demora é determinado pelo número de utilizadores no seu diretório e pelo tamanho do grupo criado como resultado da regra. Normalmente, os diretórios com um pequeno número de utilizadores verão as mudanças de membro do grupo em menos de alguns minutos. Os diretórios com um grande número de utilizadores podem demorar 30 minutos ou mais a preencher.

**Como posso forçar o grupo a ser processado agora?**<br/>
Atualmente, não há forma de desencadear automaticamente o grupo a ser processado a pedido. No entanto, pode ativar manualmente o reprocessamento atualizando a regra de adesão para adicionar um espaço em branco no final.  

**Encontrei um erro de processamento de regras**<br/>A tabela que se segue lista erros comuns de regras de adesão dinâmica e como corrigi-los.

| Erro do parser de regras | Utilização de erros | Utilização corrigida |
| --- | --- | --- |
| Erro: Atributo não suportado. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Certifique-se de que o atributo está na [lista de propriedades suportadas.](groups-dynamic-membership.md#supported-properties) |
| Erro: O operador não é suportado no atributo. |(user.accountEnabled -contém verdadeiro) |(user.accountEnabled -eq true)<br/><br/>O operador utilizado não é suportado para o tipo de propriedade (neste exemplo, -contém não pode ser usado no tipo boolean). Utilize os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação de consultas. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "* @domain.ext ") | 1. Operador desaparecido. Use -e ou -ou -ou dois se juntem a predicados<br>(user.department -eq "Sales") -ou (user.department -eq "Marketing")<br>2. Erro na expressão regular utilizado com -match<br>(user.userPrincipalName -match ".* @domain.ext ")<br>ou alternativamente: (user.userPrincipalName -match " @domain.ext $") |

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)