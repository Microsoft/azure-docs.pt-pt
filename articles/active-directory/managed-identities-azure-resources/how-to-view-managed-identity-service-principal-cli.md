---
title: Ver diretor de serviço de uma identidade gerida - Azure CLI - Azure AD
description: Instruções passo a passo para visualização do principal de serviço de uma identidade gerida utilizando O Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c0f167a36ceff6a9b62e2a3ccf24ca6e13e6294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611916"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Ver o principal de serviço de uma identidade gerida usando O Azure CLI

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD sem ter credenciais no seu código. 

Neste artigo, você aprende a ver o principal de serviço de uma identidade gerida usando Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tem uma conta Azure, [inscreva-se numa conta gratuita.](https://azure.microsoft.com/free/)
- Ativar a identidade atribuída ao sistema numa máquina ou [aplicação](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity) [virtual.](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)
- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar scripts localmente instalando a versão mais recente do [Azure CLI,](/cli/azure/install-azure-cli)em seguida, inicie sessão no Azure usando [login az](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição do Azure na qual pretende criar recursos.   

## <a name="view-the-service-principal"></a>Ver o principal de serviço

Este comando seguinte demonstra como ver o principal de serviço de um VM ou aplicação com identidade gerida habilitada. `<Azure resource name>`Substitua-o por seus próprios valores.

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão dos principais serviços Azure AD utilizando o Azure CLI, consulte [az ad sp](/cli/azure/ad/sp).
