---
title: Garantir contas de serviço baseadas no utilizador | Diretório Ativo Azure
description: Um guia para garantir contas de utilizadores no local.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417465"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Assegurar contas de serviço baseadas no utilizador no Ative Directory

As contas de utilizadores no local são a abordagem tradicional para garantir serviços em execução no Windows. Utilize estas contas como último recurso quando as contas de serviço geridas globalmente (gMSAs) e as contas de serviço geridas autónomas (SMSAs) não são suportadas pelo seu serviço. Consulte a visão geral das contas do serviço no local para obter informações sobre a seleção do melhor tipo de conta a utilizar. Investigue também se pode mover o seu serviço para usar uma conta de serviço Azure como uma identidade gerida ou um princípio de serviço. 

As contas de utilizadores no local podem ser criadas para fornecer um contexto de segurança para os serviços e privilégios concedidos para que os serviços acedam aos recursos locais e de rede. Requerem uma gestão manual de passwords como qualquer outra conta de utilizador ative (AD). Os administradores de serviços e de domínio são obrigados a observar processos fortes de gestão de passwords para manter estas contas seguras.

Quando utilizar uma conta de utilizador como conta de serviço, utilize-a apenas para um único serviço. Nomeie-o de uma forma que deixa claro que se trata de uma conta de serviço e para qual serviço. 

## <a name="benefits-and-challenges"></a>Benefícios e desafios

Benefícios

As contas de utilizador no local são o tipo de conta mais versátil para utilização com serviços. As contas de utilizador utilizadas como contas de serviço podem ser controladas por todas as políticas que regem as contas normais dos utilizadores. Dito isto, usa-os apenas se não puderes usar um MSA. Avalie também se uma conta de computador é uma opção melhor. 

Desafios com contas de utilizadores no local

Os seguintes desafios estão associados à utilização de contas de utilizadores no local.

| Desafios| Mitigações |
| - | - |
| A gestão da palavra-passe é um processo manual que pode levar a uma segurança e tempo de inatividade mais fracos.| Certifique-se de que a complexidade da palavra-passe e a mudança de palavra-passe são regidas por um processo robusto que garante atualizações regulares com senha forte. <br> Coordene a alteração da palavra-passe com uma atualização de palavra-passe no serviço, o que resultará em tempo de inatividade do serviço. |
| Identificar contas de utilizadores no local que atuam como contas de serviço pode ser difícil.| Documente e mantenha registos de contas de serviço implementadas no seu ambiente. <br> Rastreie o nome da conta e os recursos aos quais lhes é atribuído acesso. <br> Considere adicionar um prefixo de svc_ a todas as contas de utilizador utilizadas como contas de serviço. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Localizar contas de utilizadores no local utilizadas como contas de serviço

As contas de utilizadores no local são como qualquer outra conta de utilizador de AD. Consequentemente, pode ser difícil encontrar estas contas, uma vez que não existe um único atributo de uma conta de utilizador que a identifique como uma conta de serviço. 

Recomendamos que crie uma convenção de nomeação facilmente identificável para qualquer conta de utilizador utilizada como conta de serviço.

Por exemplo, adicione "service-" como prefixo e nomeie o serviço: "service-HRDataConnector".

Pode utilizar alguns dos indicadores abaixo para encontrar estas contas de serviço, no entanto, isso pode não encontrar todas essas contas.

* Contas fidedignas para a delegação.

* Contas com nomes principais do serviço.

* Contas cuja senha está definida para nunca expirar.

Pode executar os seguintes comandos PowerShell para encontrar as contas de utilizador no local criadas para serviços.

### <a name="find-accounts-trusted-for-delegation"></a>Encontrar contas fidedignas para delegação

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>Encontre contas com nomes de princípios de serviço

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Encontre contas com senhas definidas para nunca expirar

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


Também pode auditar o acesso a recursos sensíveis e arquivar registos de auditoria a um sistema de informação de segurança e gestão de eventos (SIEM). Utilizando sistemas como a Azure Log Analytics ou Azure Sentinel, pode pesquisar e analisar e analisar contas de serviço.

## <a name="assess-security-of-on-premises-user-accounts"></a>Avaliar a segurança das contas de utilizadores no local

Avalie a segurança das suas contas de utilizador no local que estão a ser utilizadas como contas de serviço utilizando os seguintes critérios:

* Qual é a política de gestão de passwords?

* A conta é membro de algum grupo privilegiado?

* A conta tem acesso de leitura/escrita a recursos importantes?

### <a name="mitigate-potential-security-issues"></a>Mitigar potenciais problemas de segurança

A tabela a seguir apresenta potenciais problemas de segurança e atenuações correspondentes para contas de utilizadores no local.

| Problemas de segurança| Mitigações |
| - | - |
| Gestão de palavras-passe|* Certifique-se de que a complexidade da palavra-passe e a mudança de palavra-passe são regidas por um processo robusto que garante atualizações regulares com requisitos de senha fortes. <br> * Coordene a alteração da palavra-passe com uma atualização de palavra-passe para minimizar o tempo de inatividade do serviço. |
| A conta é membro de grupos privilegiados.| Rever membros do grupo. Retire a conta de grupos privilegiados. Conceder à conta apenas os direitos e permissões que necessita para executar o seu serviço (consulte o fornecedor de serviços). Por exemplo, pode ser capaz de negar o acesso local ou negar o início de sôm. |
| A conta tem acesso de leitura/escrita a recursos sensíveis.| Auditoria acesso a recursos sensíveis. Arquivar registos de auditoria a um SIEM (Azure Log Analytics ou Azure Sentinel) para análise. Remediar permissões de recursos se for detetado um nível de acesso indesejável. |


## <a name="move-to-more-secure-account-types"></a>Mover-se para tipos de conta mais seguros

A Microsoft não recomenda que os clientes utilizem as contas de utilizadores no local como contas de serviço. Para qualquer serviço que utilize este tipo de conta, avalie se pode ser configurado para utilizar um gMSA ou um SMSA.

Além disso, avalie se o próprio serviço poderia ser transferido para a Azure para que os tipos de conta de serviço mais seguros pudessem ser usados. 

## <a name="next-steps"></a>Passos seguintes
Ver os seguintes artigos sobre a garantia de contas de serviço

* [Introdução às contas de serviço no local](service-accounts-on-premises.md)

* [Contas de serviço geridas pelo grupo seguro](service-accounts-group-managed.md)

* [Contas de serviço geridas autónomas seguras](service-accounts-standalone-managed.md)

* [Contas de computador seguras](service-accounts-computer.md)

* [Proteger contas de utilizador](service-accounts-user-on-premises.md)

* [Governar contas de serviço no local](service-accounts-govern-on-premises.md)

 
