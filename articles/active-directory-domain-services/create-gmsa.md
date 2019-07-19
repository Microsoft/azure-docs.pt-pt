---
title: 'Azure Active Directory Domain Services: Criar uma conta de serviço gerenciado de grupo | Microsoft Docs'
description: Saiba como criar uma conta de serviço gerenciado de grupo (gMSA) para uso com Azure Active Directory Domain Services domínios gerenciados
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 404160c895a8d0a72921fe202adba82c3d069aaf
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234122"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Criar uma conta de serviço gerenciado de grupo (gMSA) em um domínio gerenciado Azure AD Domain Services
Este artigo mostra como criar contas de serviço gerenciado em um Azure AD Domain Services domínio gerenciado.

## <a name="managed-service-accounts"></a>Contas de serviço gerenciado
Uma conta de serviço gerenciado autônomo (sMSA) é uma conta de domínio gerenciado cuja senha é gerenciada automaticamente. Ele simplifica o gerenciamento de SPN (nome da entidade de serviço) e permite o gerenciamento delegado para outros administradores. Esse tipo de MSA (conta de serviço gerenciado) foi introduzido no Windows Server 2008 R2 e no Windows 7.

A conta de serviço gerenciado por grupo (gMSA) fornece os mesmos benefícios para muitos servidores no domínio. Todas as instâncias de um serviço hospedados em um farm de servidores precisam usar a mesma entidade de serviço para que os protocolos de autenticação mútua funcionem. Quando um gMSA é usado como entidade de serviço, o sistema operacional Windows gerencia a senha da conta em vez de depender do administrador.

**Mais informações:**
- [Visão geral de contas de serviço gerenciado de grupo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Introdução às contas de serviço gerenciado de grupo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Usando contas de serviço nos serviços de domínio do Azure AD
Azure AD Domain Services domínios gerenciados são bloqueados e gerenciados pela Microsoft. Há algumas considerações importantes ao usar contas de serviço com Azure AD Domain Services.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Criar contas de serviço em unidades organizacionais (UO) personalizadas no domínio gerenciado
Você não pode criar uma conta de serviço nas unidades organizacionais ' AADDC users ' ou ' AADDC Computers ' internas. [Crie uma UO personalizada](create-ou.md) em seu domínio gerenciado e, em seguida, crie contas de serviço dentro dessa UO personalizada.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>A chave raiz dos serviços de distribuição de chaves (KDS) já foi criada previamente
A chave raiz dos KDS (serviços de distribuição de chaves) é criada previamente em um domínio gerenciado Azure AD Domain Services. Você não precisa criar uma chave raiz KDS e não tem privilégios para fazer isso. Você também não pode exibir a chave raiz KDS no domínio gerenciado.

## <a name="sample---create-a-gmsa-using-powershell"></a>Exemplo-criar um gMSA usando o PowerShell
O exemplo a seguir mostra como criar uma UO personalizada usando o PowerShell. Em seguida, você pode criar um gMSA dentro dessa UO usando ```-Path``` o parâmetro para especificar a UO.

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

**Documentação do cmdlet do PowerShell:**
- [Cmdlet New-ADOrganizationalUnit](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Cmdlet New-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Passos Seguintes
- [Criar uma UO personalizada em um domínio gerenciado](create-ou.md)
- [Visão geral de contas de serviço gerenciado de grupo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Introdução às contas de serviço gerenciado de grupo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
