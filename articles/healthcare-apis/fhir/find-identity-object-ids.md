---
title: Encontre iDs de objeto de identidade para autenticação - Azure API para FHIR
description: Este artigo explica como localizar os IDs de objeto de identidade necessários para configurar a autenticação para AZure API para FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 706d7e081743f2bab1f593e00dc792f218a000ea
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019600"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Encontre iDs de objeto de identidade para configuração de autenticação

Neste artigo, você vai aprender como encontrar iDs de objeto de identidade necessários ao configurar a API Azure para fHIR para [usar um inquilino de Diretório Ativo externo ou secundário](configure-local-rbac.md) para plano de dados.

## <a name="find-user-object-id"></a>Encontre iD de objeto de utilizador

Se tiver um utilizador com o nome de `myuser@contoso.com` utilizador, pode localizar os utilizadores `ObjectId` utilizando o seguinte comando PowerShell:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

ou pode utilizar o Azure CLI:

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>Encontre iD de objeto principal de serviço

Suponha que registou uma [aplicação](register-service-azure-ad-client-app.md) de cliente de serviço e que gostaria de permitir que este cliente de serviço aceda à AZure API para FHIR, pode encontrar o ID do objeto para o principal de serviço do cliente com o seguinte comando PowerShell:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

onde `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` está o ID de aplicação do cliente de serviço. Em alternativa, pode utilizar o `DisplayName` cliente de serviço:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Se estiver a utilizar o Azure CLI, pode utilizar:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>Encontre um ID de objeto de grupo de segurança

Se quiser localizar o ID do objeto de um grupo de segurança, pode utilizar o seguinte comando PowerShell:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Onde `mygroup` está o nome do grupo em que está interessado.

Se estiver a utilizar o Azure CLI, pode utilizar:

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu como encontrar iDs de objeto de identidade necessários para configurar a API Azure para fHIR para usar um inquilino externo ou secundário do Azure Ative Directory. Leia em seguida como usar os IDs do objeto para configurar as definições locais de RBAC:
 
>[!div class="nextstepaction"]
>[Configurar as configurações locais do RBAC](configure-local-rbac.md)
