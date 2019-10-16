---
title: Usar identidades gerenciadas para recursos no Azure Cloud Shell
description: Autenticar código com MSI no Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: a5d49a16324a5a97f4a0507f9abf47ea602ea072
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72328720"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Usar identidades gerenciadas para recursos do Azure no Azure Cloud Shell

Azure Cloud Shell dá suporte à autorização com identidades gerenciadas para recursos do Azure. Utilize isso para recuperar Tokens de acesso para se comunicar com segurança com os serviços do Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Acerca das identidades geridas para os recursos do Azure
Um desafio comum ao criar aplicativos de nuvem é como gerenciar com segurança as credenciais que precisam estar em seu código para autenticação em serviços de nuvem. Em Cloud Shell talvez seja necessário autenticar a recuperação de Key Vault para uma credencial que um script possa precisar.

Identidades gerenciadas para recursos do Azure facilitam a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.

## <a name="acquire-access-token-in-cloud-shell"></a>Adquirir token de acesso no Cloud Shell

Execute os comandos a seguir para definir seu token de acesso MSI como uma variável de ambiente, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Tratamento da expiração do token

O subsistema MSI local armazena em cache os tokens. Portanto, você pode chamá-lo sempre que desejar e uma chamada durante a transmissão para os resultados do AD do Azure somente se:
- um erro de cache ocorre devido a nenhum token no cache
- o token expirou

Se você armazenar em cache o token em seu código, deverá estar preparado para lidar com cenários em que o recurso indica que o token expirou.

Para lidar com erros de token, visite a [página MSI sobre como enrolar tokens de acesso MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Adquirindo tokens de acesso de VMs do MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
