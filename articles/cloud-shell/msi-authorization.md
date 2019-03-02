---
title: Utilizar identidades geridas para recursos do Azure no Azure Cloud Shell | Documentos da Microsoft
description: Autenticar o código com o MSI no Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: damaerte
ms.openlocfilehash: 7cadaaf67f9c6923ee9e9eb2596941aa8e1f0c9b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244015"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Utilizar identidades geridas para recursos do Azure no Azure Cloud Shell

O Azure Cloud Shell oferece suporte a autorização com identidades geridas para recursos do Azure. Utilize esta opção para obtenção de tokens de acesso para comunicar de forma segura com os serviços do Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Acerca das identidades geridas para os recursos do Azure
Um desafio comum quando criar aplicações na cloud é a forma de gerir de forma segura as credenciais que precisam de estar no seu código para autenticar a serviços em nuvem. No Cloud Shell pode tem de autenticar a obtenção do Key Vault para uma credencial que poderão necessitar de um script.

Identidades geridas para recursos do Azure torna a resolver esse problema mais simples, fornecendo serviços do Azure uma identidade gerida automaticamente no Azure Active Directory (Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.

## <a name="acquire-access-token-in-cloud-shell"></a>Adquirir token de acesso no Cloud Shell

Execute os seguintes comandos para definir o token de acesso MSI como uma variável de ambiente, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Manipulação de expiração do token

O subsistema MSI local armazena em cache tokens. Portanto, pode chamá-lo sempre que quiser, e resulta de uma chamada de on-the-wire para o Azure AD apenas se a:
- uma falha de acerto na cache ocorre devido a nenhum token em cache
- o token expirou

Se colocar em cache o token no seu código, deve estar preparado para lidar com cenários em que o recurso indica que o token expirou.

Para lidar com erros de token, visite o [página MSI sobre curling tokens de acesso MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Aquisição de tokens de acesso de VMs de MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
