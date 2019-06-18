---
title: 'Azure Active Directory Domain Services: Crie uma conta de serviço geridas de grupo | Documentos da Microsoft'
description: Administrar domínios geridos do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mstephen
ms.openlocfilehash: 0f4e57359797e9c28ea62397e5c92c5fca05c69e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246348"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Criar uma conta de serviço geridas de grupo (gMSA) num domínio gerido do Azure AD Domain Services
Este artigo mostra-lhe como criar contas de serviço geridas num domínio gerido do Azure AD Domain Services.

## <a name="managed-service-accounts"></a>Contas de serviço geridas
Uma conta de serviço gerida autónoma (sMSA) é uma conta de domínio gerido que palavra-passe é gerida automaticamente. Isso simplifica a gestão do serviço (SPN) de nome principal e permite delegada gestão a outros administradores. Este tipo de conta de serviço geridas (MSA) foi introduzido no Windows Server 2008 R2 e Windows 7.

A conta de serviço gerida de grupo (gMSA) fornece os mesmos benefícios para vários servidores no domínio. Todas as instâncias de um serviço alojado num farm de servidores tem de utilizar a mesma entidade de serviço para os protocolos de autenticação mútua para trabalhar. Quando uma gMSA é utilizada como principal de serviço, o sistema operativo Windows gere a senha da conta em vez de depender do administrador.

**Obter mais informações:**
- [Descrição geral das contas de serviço geridas de grupo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Guia de introdução contas de serviço geridas de grupo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Utilizar contas de serviço no Azure AD Domain services
Domínios de geridos do Azure AD Domain Services são bloqueados e gerenciados pela Microsoft. Existem algumas considerações-chave ao utilizar contas de serviço com o Azure AD Domain Services.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Crie contas de serviço no personalizado unidades organizacionais (UO) no domínio gerido
Não é possível criar uma conta de serviço nas unidades organizacionais incorporadas "Utilizadores do aad DC" ou "Computadores do aad DC". [Criar uma UO personalizada](create-ou.md) no seu domínio gerido e, em seguida, crie contas de serviço no UO personalizada.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>A chave de raiz de serviços de distribuição de chave (KDS) já foi previamente criada
A chave de raiz de serviços de distribuição de chave (KDS) é previamente criada num domínio gerido do Azure AD Domain Services. Não precisa de criar uma raiz KDS da chave e não tem privilégios para fazer por isso, qualquer um. Optar por não pode ver a chave de raiz KDS no domínio gerido.

## <a name="sample---create-a-gmsa-using-powershell"></a>Exemplo - criar uma gMSA com o PowerShell
O exemplo a seguir mostra como criar uma UO personalizada com o PowerShell. Em seguida, pode criar uma gMSA no UO utilizando o ```-Path``` parâmetro para especificar a UO.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**Documentação de cmdlets do PowerShell:**
- [New-ADOrganizationalUnit cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Cmdlet de novo-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Passos Seguintes
- [Criar uma UO personalizada num domínio gerido](create-ou.md)
- [Descrição geral das contas de serviço geridas de grupo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Guia de introdução contas de serviço geridas de grupo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
