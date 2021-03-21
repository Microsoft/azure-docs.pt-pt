---
title: Utilize identidades geridas para recursos em Azure Cloud Shell
description: Autenticar código com MSI em Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: 0fb19524079f84e92e1ddbc98a61917026492663
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89469903"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Utilize identidades geridas para recursos Azure em Azure Cloud Shell

A Azure Cloud Shell suporta a autorização com identidades geridas para recursos Azure. Utilize-o para obter fichas de acesso para comunicar de forma segura com os serviços da Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Acerca das identidades geridas para os recursos do Azure
Um desafio comum ao construir aplicações em nuvem é como gerir de forma segura as credenciais que precisam estar no seu código para autenticar serviços na nuvem. Na Cloud Shell poderá ser necessário autenticar a recuperação do Key Vault para obter uma credencial que um script possa necessitar.

Identidades geridas para recursos Azure simplificam a resolução deste problema, dando aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.

## <a name="acquire-access-token-in-cloud-shell"></a>Adquirir ficha de acesso em Cloud Shell

Execute os seguintes comandos para definir o seu token de acesso MSI como uma variável ambiental, `access_token` .
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Validade do token de manuseamento

O subsistema local do subsistema MSI caches tokens. Portanto, pode chamá-lo sempre que quiser, e uma chamada no fio para resultados da AD Azure apenas se:
- uma falha cache ocorre devido a nenhum símbolo na cache
- o símbolo expira

Se cache o token no seu código, deve estar preparado para lidar com cenários em que o recurso indique que o token está expirado.

Para lidar com erros simbólicos, visite a [página msi sobre os tokens de acesso MSI](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#error-handling).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre a MSI](../active-directory/managed-identities-azure-resources/overview.md)  
[Aquisição de fichas de acesso a MSI VMs](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)