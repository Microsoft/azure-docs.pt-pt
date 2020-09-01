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
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: d26f1cdd6184ca10c3a7b9c9a7dc5724cc5e8129
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266311"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Ver o principal de serviço de uma identidade gerida usando O Azure CLI

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD sem ter credenciais no seu código. 

Neste artigo, você aprende a ver o principal de serviço de uma identidade gerida usando Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tem uma conta Azure, [inscreva-se numa conta gratuita.](https://azure.microsoft.com/free/)
- Ativar a identidade atribuída ao sistema numa máquina ou [aplicação](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity) [virtual.](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)
- Para executar os exemplos de script do CLI, tem três opções:
    - Utilize a Casca de [Nuvem Azure](../../cloud-shell/overview.md) a partir do portal Azure (ver secção seguinte).
    - Utilize o Azure Cloud Shell incorporado através do botão "Try It", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do Azure CLI](/cli/azure/install-azure-cli) se preferir utilizar uma consola CLI local e iniciar sação no Azure usando `az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Ver o principal de serviço

Este comando seguinte demonstra como ver o principal de serviço de um VM ou aplicação com identidade gerida habilitada. `<VM or application name>`Substitua-o por seus próprios valores. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão dos principais serviços Azure AD utilizando o Azure CLI, consulte [az ad sp](/cli/azure/ad/sp).