---
title: Garantir contas de serviço geridas pelo grupo | Diretório Ativo Azure
description: Um guia para assegurar contas de serviço geridas pelo grupo.
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
ms.openlocfilehash: f6f91adf499c62fef56a30c5f2945ba7a90a0b29
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640064"
---
# <a name="securing-group-managed-service-accounts"></a>Assegurar contas de serviço geridas pelo grupo

As contas de serviço geridas pelo grupo (gMSAs) são contas de domínio geridas que são utilizadas para assegurar serviços. os gMSAs podem funcionar num único servidor, ou numa fazenda de servidores, como sistemas por trás de um Balancer de Carga de Rede (NLB) ou um servidor de Serviços de Informação da Internet (IIS). Uma vez configurados os seus serviços para utilizar um principal gMSA, a gestão de passwords para essa conta é tratada pelo Windows.

## <a name="benefits-of-using-gmsas"></a>Benefícios da utilização de gMSAs

os gMSAs oferecem uma única solução de identidade com maior segurança, reduzindo ao mesmo tempo as despesas administrativas:

* **Definição de palavras-passe fortes**. gMSAs usam 240 byte palavras-passe complexas geradas aleatoriamente. A complexidade e o comprimento das palavras-passe gMSA minimizam a probabilidade de um serviço ser comprometido por força bruta ou ataques de dicionário.

* **Senhas de ciclismo regularmente.** gMSAs transferem a gestão de passwords para o Windows, que altera a palavra-passe a cada 30 dias. Os administradores de serviços e de domínio já não precisam de agendar alterações de palavras-passe ou de gerir falhas de serviço para manter as contas de serviço seguras. 

* **Suportando a implementação para as explorações de servidores.** A capacidade de implantar gMSAs em vários servidores permite o suporte de soluções equilibradas de carga onde vários anfitriões executam o mesmo serviço. 

* **Suportando a gestão simplificada do Nome Principal do Servidor (SPN).** Pode configurar o SPN utilizando o PowerShell no momento da criação de conta. Além disso, os serviços que suportam registos SPN automáticos podem fazê-lo contra o gMSA, desde que as permissões gMSA sejam corretamente definidas. 

## <a name="when-to-use-gmsas"></a>Quando usar gMSAs

Utilize os gMSAs como o tipo de conta preferido para serviços no local, a menos que um serviço, como o Failover Clustering, não o suporte.

> [!IMPORTANT]
> Deve testar o seu serviço com gMSAs antes da sua colocação em produção. Para tal, crie um ambiente de teste e garanta que a aplicação pode utilizar o gMSA e aceder aos recursos a que necessita para aceder. Para obter mais informações, consulte [Suporte para contas de serviço geridas pelo grupo.](/system-center/scom/support-group-managed-service-accounts)


Se um serviço não suportar a utilização de gMSAs, a sua próxima melhor opção é utilizar uma conta de serviço gerida autónoma (sMSA). os sMSAs fornecem a mesma funcionalidade que um gMSA, mas destinam-se a ser implantados apenas num único servidor.

Se não puder utilizar um gMSA ou sMSA é suportado pelo seu serviço, então o serviço deve ser configurado para funcionar como uma conta de utilizador padrão. Os administradores de serviço e de domínio são obrigados a observar processos fortes de gestão de passwords para manter a conta segura.

## <a name="assess-the-security-posture-of-gmsas"></a>Avaliar a postura de segurança dos GMSAs

os gMSAs são inerentemente mais seguros do que as contas padrão do utilizador, que requerem uma gestão contínua da palavra-passe. No entanto, é importante considerar o âmbito de acesso dos gMSAs à medida que olha para a sua postura de segurança geral.

A tabela a seguir mostra potenciais problemas de segurança e mitigações para a utilização de gMSAs.

| Problemas de segurança| Mitigações |
| - | - |
| GMSA é um membro de grupos privilegiados. | Reveja os seus membros do grupo. Para tal, pode criar um script PowerShell para enumerar todos os membros do grupo e, em seguida, filtrar um ficheiro CSV resultante pelos nomes dos seus ficheiros gMSA. <br>Retire o gMSA de grupos privilegiados.<br> Conceda à gMSA apenas os direitos e permissões que necessita para executar o seu serviço (consulte o seu fornecedor de serviços). 
| gMSA tem acesso de leitura/escrita a recursos sensíveis. | Auditoria acesso a recursos sensíveis. Arquivar registos de auditoria a um SIEM, por exemplo Azure Log Analytics ou Azure Sentinel, para análise. Remova permissões de recursos desnecessárias se for detetado um nível de acesso indesejável. |


## <a name="find-gmsas"></a>Encontre gMSAs

A sua organização pode já ter gMSAs criados. Executar o seguinte cmdlet PowerShell para recuperar estas contas:

Para funcionar eficazmente, os gMSAs devem estar na unidade organizacional de Contas de Serviço Gerido (OU).

  
![Imagem de tela da conta de serviço gerida OU.](./media/securing-service-accounts/secure-gmsa-image-1.png)

Para encontrar MSAs de serviço que podem não estar lá, consulte os seguintes comandos.

**Para encontrar todas as contas de serviço, incluindo gMSAs e sMSAs:**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount"}
```

## <a name="manage-gmsas"></a>Gerir gMSAs

Pode utilizar os seguintes cmdlets Ative Directory PowerShell para gerir gMSAs:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Começando pelo Windows Server 2012, os cmdlets *-ADServiceAccount funcionam com gMSAs por padrão. Para obter mais informações sobre a utilização dos cmdlets acima, consulte [**Começar com contas de serviço geridas pelo grupo.**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

## <a name="move-to-a-gmsa"></a>Mude-se para um gMSA
gMSAs são o tipo de serviço mais seguro para as necessidades no local. Se pode mudar para um, deve. Além disso, considere transferir os seus serviços para a Azure e as suas contas de serviço para o Azure Ative.

1.  Certifique-se de que a [chave raiz KDS está implantada na floresta.](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key) Trata-se de uma operação única.

2. [Criar um novo gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Instale o novo gMSA em cada anfitrião que executa o serviço.
   > [!NOTE] 
   > Para obter mais informações sobre a criação e instalação de gMSA num anfitrião, antes de configurar o seu serviço para utilizar o gMSA, consulte Começar com Contas de [Serviço Geridas pelo Grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))

 
4. Altere a sua identidade de serviço para gMSA e especifique uma palavra-passe em branco.

5. Valide que o seu serviço está a funcionar sob a nova identidade gMSA.

6. Apague a identidade da conta de serviço antiga.

 

## <a name="next-steps"></a>Passos seguintes
Ver os seguintes artigos sobre a garantia de contas de serviço

* [Introdução às contas de serviço no local](service-accounts-on-premises.md)

* [Contas de serviço geridas pelo grupo seguro](service-accounts-group-managed.md)

* [Contas de serviço geridas autónomas seguras](service-accounts-standalone-managed.md)

* [Contas de computador seguras](service-accounts-computer.md)

* [Proteger contas de utilizador](service-accounts-user-on-premises.md)

* [Governar contas de serviço no local](service-accounts-govern-on-premises.md)