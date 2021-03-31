---
title: Alterar o tipo de autenticação de subdomínio utilizando PowerShell e Graph - Azure Ative Directory | Microsoft Docs
description: Alterar as definições de autenticação de subdomínio predefinidos herdadas das definições de domínio raiz no Azure Ative Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734e6824f13e62ad080500eff18c4892e1f76807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95503666"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Alterar o tipo de autenticação subdomínio no Diretório Ativo Azure

Depois de um domínio de raiz ser adicionado ao Azure Ative Directory (Azure AD), todos os subdomínios subsequentes adicionados a essa raiz na sua organização AZure AD herdam automaticamente a definição de autenticação do domínio raiz. No entanto, se pretender gerir as definições de autenticação de domínio de forma independente das definições de domínio raiz, pode agora com a API do Gráfico microsoft. Por exemplo, se tiver um domínio de raiz federado, como contoso.com, este artigo pode ajudá-lo a verificar um subdomínio como child.contoso.com como gerido em vez de federado.

No portal AD Azure, quando o domínio principal é federado e o administrador tenta verificar um subdomínio gerido na página **de nomes de domínio personalizado,** obterá um erro de "Não adicionar domínio" com a razão de "Uma ou mais propriedades contêm valores inválidos". Se tentar adicionar este subdomínio ao centro de administração microsoft 365, receberá um erro semelhante. Para obter mais informações sobre o erro, consulte [Um domínio infantil não herda alterações de domínio dos pais no Office 365, Azure ou Intune](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Como verificar um subdomínio personalizado

Uma vez que os subdomínios herdam o tipo de autenticação do domínio raiz por padrão, deve promover a subdomínio para um domínio raiz em AD Azure usando o Gráfico do Microsoft para que possa definir o tipo de autenticação para o tipo de autenticação para o seu tipo pretendido.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Adicione o subdomínio e veja o seu tipo de autenticação

1. Utilize o PowerShell para adicionar o novo subdomínio, que tem o tipo de autenticação predefinido do seu domínio de raiz. Os centros de administração Azure AD e Microsoft 365 ainda não suportam esta operação.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Utilize [o Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net) para OBTER o domínio. Como o domínio não é um domínio de raiz, herda o tipo de autenticação do domínio raiz. O seu comando e resultados podem parecer os seguintes, usando a sua própria id de inquilino:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Use a Azure AD Graph Explorer API para fazer deste um domínio raiz

Utilize o seguinte comando para promover o subdomínio:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Alterar o tipo de autenticação subdomínio

1. Utilize o seguinte comando para alterar o tipo de autenticação subdomínio:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Verifique via GET em Azure AD Graph Explorer que o tipo de autenticação de subdomínio é agora gerido:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Passos seguintes

- [Adicionar nomes de domínio personalizados](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Gerir nomes de domínio](domains-manage.md)
- [ForceDelete um nome de domínio personalizado com Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)