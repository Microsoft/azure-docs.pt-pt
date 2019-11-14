---
title: Corrigir problemas com associações de grupo dinâmicos – Azure AD | Microsoft Docs
description: Dicas de solução de problemas para associação de grupo dinâmico no Azure Active Directory
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026547"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Solucionar problemas e resolver questões de grupos

## <a name="troubleshooting-group-creation-issues"></a>Solucionando problemas de criação de grupo

**Desabilitei a criação do grupo de segurança na portal do Azure mas os grupos ainda podem ser criados por meio do PowerShell** O **usuário pode criar grupos de segurança na configuração de portais do Azure** no portal do Azure controla se os usuários não administradores podem ou não criar grupos de segurança no painel de acesso ou no portal do Azure. Ele não controla a criação do grupo de segurança por meio do PowerShell.

Para desabilitar a criação de grupo para usuários não administradores no PowerShell:
1. Verifique se os usuários não administradores têm permissão para criar grupos:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Se ele retornar `UsersPermissionToCreateGroupsEnabled : True`, os usuários não administradores poderão criar grupos. Para desabilitar esse recurso:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Recebi um erro de máximo de grupos permitidos ao tentar criar um grupo dinâmico no PowerShell**<br/>
Se você receber uma mensagem no PowerShell indicando que a _contagem máxima de grupos permitidos das diretivas de grupo dinâmicos foi atingida_, isso significa que você atingiu o limite máximo de grupos dinâmicos em seu locatário. O número máximo de grupos dinâmicos por locatário é 5.000.

Para criar novos grupos dinâmicos, primeiro você precisará excluir alguns grupos dinâmicos existentes. Não há como aumentar o limite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Resolver problemas de associações dinâmicas a grupos

**Configurei uma regra em um grupo, mas nenhuma associação foi atualizada no grupo**<br/>
1. Verifique os valores dos atributos de usuário ou dispositivo na regra. Verifique se há usuários que satisfaçam a regra. Para dispositivos, verifique as propriedades do dispositivo para garantir que todos os atributos sincronizados contenham os valores esperados.<br/>
2. Verifique o status de processamento de associação para confirmar se ele está concluído. Você pode verificar o [status de processamento de associação](groups-create-rule.md#check-processing-status-for-a-rule) e a data da última atualização na página **visão geral** do grupo.

Se tudo estiver correto, aguarde algum tempo para que o grupo seja preenchido. Dependendo do tamanho do seu inquilino, o grupo pode demorar até 24 horas a ser preenchido pela primeira vez, ou após uma alteração de regra.

**Configurei uma regra, mas agora os membros existentes da regra foram removidos**<br/>Esse é o comportamento esperado. Os membros existentes do grupo são removidos quando uma regra é habilitada ou alterada. Os usuários retornados da avaliação da regra são adicionados como membros ao grupo.

**Não vejo alterações de associação instantaneamente quando adiciono ou altero uma regra, por que não?**<br/>A avaliação de associação dedicada é feita periodicamente em um processo assíncrono em segundo plano. O tempo que o processo leva é determinado pelo número de usuários em seu diretório e o tamanho do grupo criado como resultado da regra. Normalmente, os diretórios com pequenos números de usuários verão as alterações de associação de grupo em menos de alguns minutos. Os diretórios com um grande número de usuários podem levar 30 minutos ou mais para serem preenchidos.

**Como posso forçar o processamento do grupo agora?**<br/>
Atualmente, não há como disparar automaticamente o grupo a ser processado sob demanda. No entanto, você pode disparar manualmente o reprocessamento atualizando a regra de associação para adicionar um espaço em branco no final.  

**Encontrei um erro de processamento de regra**<br/>A tabela a seguir lista os erros comuns da regra de associação dinâmica e como corrigi-los.

| Erro do analisador de regra | Uso de erro | Uso corrigido |
| --- | --- | --- |
| Erro: atributo sem suporte. |(User. inválidaproperty-EQ "valor") |(User. Department-EQ "valor")<br/><br/>Verifique se o atributo está na [lista de propriedades com suporte](groups-dynamic-membership.md#supported-properties). |
| Erro: não há suporte para o operador no atributo. |(User. accountEnabled-contém true) |(User. accountEnabled-EQ true)<br/><br/>O operador usado não tem suporte para o tipo de propriedade (neste exemplo,-Contains não pode ser usado no tipo booliano). Use os operadores corretos para o tipo de propriedade. |
| Erro: erro de compilação de consulta. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (User. userPrincipalName-Match "*@domain.ext") | 1. operador ausente. Use-and ou-or dois predicados de junção<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. erro na expressão regular usada com-Match<br>(User. userPrincipalName-Match ". *@domain.ext")<br>Alternativamente: (User. userPrincipalName-Match "@domain.ext$") |

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)