---
title: Corrigir problemas de associação dinâmica para grupos - Azure Active Directory | Documentos da Microsoft
description: Sugestões de resolução de problemas para associação dinâmica para grupos no Azure AD.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0594d99874ea9bb83673013a9a03272edcd8ce0b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897678"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Resolver problemas de grupos

## <a name="troubleshooting-group-creation-issues"></a>Resolução de problemas de criação de grupo
**Eu desativado a criação do grupo de segurança no portal do Azure, mas ainda podem ser criados grupos através do Powershell** a **utilizador pode criar grupos de segurança nos portais do Azure** definição os controlos do portal do Azure ou não não-administrador os utilizadores podem criar grupos de segurança no painel de acesso ou o portal do Azure. Ele não controla a criação do grupo de segurança através do Powershell.

Para desativar a criação do grupo de usuários não-administradores no Powershell:
1. Certifique-se de que os usuários não-administradores têm permissão para criar grupos:
   
   ```
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Se ele retorna `UsersPermissionToCreateGroupsEnabled : True`, em seguida, os usuários não-administradores podem criar grupos. Para desativar esta funcionalidade:
  
   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Recebi um grupos máximo permitido de erro ao tentar criar um grupo dinâmico no Powershell**<br/>
Se receber uma mensagem no Powershell indicando _políticas de grupo dinâmico permitidas máx contagem de grupos atingida_, isso significa que atingiu o limite máximo para grupos dinâmicos no seu inquilino. O número máximo de grupos dinâmicos por inquilino é de 5000.

Para criar novos grupos dinâmicos, terá primeiro de eliminar alguns grupos dinâmicos existentes. Não é possível para aumentar o limite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Resolver problemas de associações dinâmicas a grupos

**Configurei uma regra num grupo, mas nenhuma das associações for atualizadas no grupo**<br/>
1. Verifique se os valores para o utilizador ou atributos de dispositivos na regra. Certifique-se de que existem utilizadores que satisfazem a regra. Para dispositivos, verifique as propriedades do dispositivo para garantir que todos os atributos sincronizados contêm os valores esperados.<br/>
2. Verifique a associação do Estado de processamento para confirmar se foi concluída. Pode verificar o [estado de processamento de associação](groups-create-rule.md#check-processing-status-for-a-rule) e a última data de atualização no **descrição geral** página para o grupo.

Se estiver tudo correto, aguarde algum tempo para o grupo ser preenchido. Dependendo do tamanho do seu inquilino, o grupo pode demorar até 24 horas a ser preenchido pela primeira vez, ou após uma alteração de regra.

**Configurei uma regra, mas agora os membros existentes da regra são removidos**<br/>Este comportamento está previsto. Os membros existentes do grupo são removidos quando uma regra está ativada ou alterada. Os utilizadores devolvidos da versão de avaliação da regra são adicionados como membros ao grupo.

**Eu não vejo a associação é alterado instantaneamente quando adicionar ou alterar uma regra, por que não?**<br/>Avaliação da associação dedicado é feita periodicamente num processo assíncrono em segundo plano. Quanto tempo leva o processo é determinado pelo número de utilizadores no seu diretório e o tamanho do grupo criado como resultado a regra. Normalmente, os diretórios com um pequeno número de usuários verão as alterações de associação de grupo em menos de alguns minutos. Diretórios com um grande número de utilizadores podem demorar 30 minutos ou mais tempo a preencher.

**Como posso forçar o grupo para serem processados agora?**<br/>
Atualmente, não é possível acionar automaticamente o grupo para ser processada a pedido. No entanto, pode acionar manualmente um novo processamento ao atualizar a regra de associação para adicionar um espaço em branco no final.  

**Deparei-me uma regra de erro de processamento**<br/>A tabela seguinte lista erros comuns de regra de associação dinâmica e como corrigi-las.

| Erro da regra de analisador | Utilização de erro | Utilização corrigida |
| --- | --- | --- |
| Erro: Atributo não suportado. |(user.invalidProperty - eq "Value") |(user.department -eq "value")<br/><br/>Certifique-se de que o atributo for a [suportada a lista de propriedades](groups-dynamic-membership.md#supported-properties). |
| Erro: Operador não é suportado no atributo. |(user.accountEnabled-contém o verdadeiro) |(user.accountEnabled - eq verdadeiro)<br/><br/>O operador usado não é suportado para o tipo de propriedade (neste exemplo,-contém não pode ser utilizado no tipo Booleano). Utilize os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação de consulta. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1. Operador em falta. Utilize o - e ou - ou dois associar predicados<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Erro ao corresponder a expressão regular utilizada com -<br>(user.userPrincipalName -match ".*@domain.ext")<br>ou, em alternativa: (user.userPrincipalName-correspondência "@domain.ext$") |

## <a name="next-steps"></a>Passos Seguintes

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
