---
title: Segurança de contas informáticas | Diretório Ativo Azure
description: Um guia para garantir contas de computador no local.
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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417514"
---
# <a name="securing-computer-accounts"></a>Segurança de contas de computador

A conta do computador, ou conta LocalSystem, é uma conta incorporada, altamente privilegiada, com acesso a praticamente todos os recursos no computador local. Esta conta não está associada a qualquer conta de utilizador assinada. Serviços funcionando como recursos de rede de acesso local, apresentando as credenciais do computador para servidores remotos. Apresenta credenciais na forma <domain_name>\<computer_name> $. O nome pré-definido de uma conta de computador é NT AUTHORITY\SYSTEM. Pode ser usado para iniciar um serviço e fornecer contexto de segurança para esse serviço.

![[Imagem 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>Benefícios da utilização da conta do computador

A conta do computador fornece os seguintes benefícios.

* **Acesso local sem restrições**: A conta do computador proporciona acesso total aos recursos locais da máquina.

* **Gestão automática da palavra-passe**: A conta do computador remove a necessidade de alterar manualmente as palavras-passe. Em vez disso, esta conta é membro do Ative Directory e a palavra-passe da conta é alterada automaticamente. Elimina também a necessidade de registar o nome principal do serviço para o serviço.

* **Direitos de acesso limitados fora da máquina**: A Lista de Controlo de Acesso predefinido (ACL) em Serviços de Domínio de Diretório Ativo permite o acesso mínimo para contas de computador. Se este serviço fosse pirateado, teria apenas acesso limitado a recursos na sua rede.

## <a name="assess-security-posture-of-computer-accounts"></a>Avaliar a postura de segurança das contas do computador

Potenciais desafios e mitigações associadas ao utilizar contas de computador. 

| Questões| Mitigações |
| - | - |
| As contas do computador estão sujeitas a eliminação e recreação quando o computador sai e volta ao domínio.| Validar a necessidade de adicionar um computador a um grupo de AD e verificar qual a conta de computador adicionada a um grupo usando os scripts de exemplo fornecidos nesta página.| 
| Se adicionar uma conta de computador a um grupo, todos os serviços que executam como Sistema Local nesse computador recebem direitos de acesso do grupo.| Seja seletivo dos membros do grupo da sua conta de computador. Evite fazer contas de computador membros de quaisquer grupos de administrador de domínio porque o serviço associado tem acesso total aos Serviços de Domínio do Diretório Ativo. |
| Incumprimentos de rede impróprios para o Sistema Local| Não assuma que a conta do computador tem o acesso limitado a recursos de rede por defeito. Em vez disso, consulte cuidadosamente os membros do grupo para esta conta. |
| Serviços desconhecidos em funcionamento como Sistema Local| Certifique-se de que todos os serviços que executam sob a conta do Sistema Local são serviços da Microsoft ou serviços fidedignos de terceiros. |


## <a name="find-services-running-under-the-computer-account"></a>Localizar serviços a funcionar sob a conta do computador

Use o seguinte cmdlet PowerShell para encontrar serviços em execução no contexto Do Sistema Local

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Encontre contas de computadores que são membros de um grupo específico**

Utilize o cmdlet PowerShell seguinte para encontrar contas de computador que sejam membros de um grupo específico.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Encontre contas de computadores que são membros de grupos privilegiados**

Utilize o cmdlet PowerShell seguinte para encontrar contas de computador que sejam membros de grupos de administradores de identidade (Administradores de Domínio, Administradores empresariais, administradores)

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Mover-se de contas de computador

> [!IMPORTANT]
> As contas de computador são contas altamente privilegiadas e só devem ser utilizadas quando o seu serviço necessita de acesso ilimitado aos recursos locais na máquina, e não pode utilizar uma conta de serviço gerida (MSA).

* Consulte o seu titular de serviço se o seu serviço pode ser executado usando um MSA, e use uma conta de serviço gerida pelo grupo (gMSA) ou uma conta de serviço gerida autónoma (sMSA) se o seu serviço o suportar.

* Utilize uma conta de utilizador de domínio apenas com os privilégios necessários para executar o seu serviço.

## <a name="next-steps"></a>Passos Seguintes 

Ver os seguintes artigos sobre a garantia de contas de serviço

* [Introdução às contas de serviço no local](service-accounts-on-premises.md)

* [Contas de serviço geridas pelo grupo seguro](service-accounts-group-managed.md)

* [Contas de serviço geridas autónomas seguras](service-accounts-standalone-managed.md)

* [Contas de computador seguras](service-accounts-computer.md)

* [Proteger contas de utilizador](service-accounts-user-on-premises.md)

* [Governar contas de serviço no local](service-accounts-govern-on-premises.md)

 

 
