---
title: Use identidades geridas para recursos em Azure Cloud Shell
description: Código autenticado com MSI em Casca de Nuvem Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72328720"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Use identidades geridas para recursos Azure em Azure Cloud Shell

A Azure Cloud Shell apoia a autorização com identidades geridas para os recursos Azure. Utilize isto para recuperar fichas de acesso para comunicar com segurança com os serviços Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Acerca das identidades geridas para os recursos do Azure
Um desafio comum ao construir aplicações em nuvem é como gerir de forma segura as credenciais que precisam de estar no seu código para autenticar serviços na nuvem. Na Cloud Shell poderá supor a recuperação do Cofre chave para uma credencial que um script possa necessitar.

Identidades geridas para recursos Azure torna a resolução deste problema mais simples, conferindo aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.

## <a name="acquire-access-token-in-cloud-shell"></a>Adquirir ficha de acesso na Cloud Shell

Execute os seguintes comandos para definir o seu sinal `access_token`de acesso MSI como uma variável ambiental, .
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Expiração do token de manuseamento

Os caches do subsistema MSI locais. Portanto, pode chamá-lo sempre que quiser, e uma chamada no cabo para os resultados da AD Azure apenas se:
- uma falta cache ocorre devido a nenhum símbolo na cache
- o símbolo está expirado

Se enfiar o símbolo no seu código, deve estar preparado para lidar com cenários em que o recurso indique que o token está expirado.

Para lidar com erros simbólicos, visite a [página mSI sobre fichas de acesso MSI de caracóis](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Aquisição de fichas de acesso a MSI VMs](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
