---
title: Como ver o principal de serviço de uma identidade gerida com a CLI do Azure
description: Obter instruções passo a passo para visualizar o principal de serviço de uma identidade gerida com a CLI do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f379c78113a4edc1efc288617a8a1c205d03552a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442289"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Ver o principal de serviço de uma identidade gerida com a CLI do Azure

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD sem ter credenciais em seu código. 

Neste artigo, irá aprender a ver o principal de serviço de uma identidade gerida com a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md).
- Se ainda não tiver uma conta do Azure, [Inscreva-se numa conta gratuita](https://azure.microsoft.com/free/).
- Ativar [identidade do sistema atribuído numa máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou [aplicação](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Para executar os exemplos de script da CLI, tem três opções:
    - Uso [Azure Cloud Shell](../../cloud-shell/overview.md) do portal do Azure (consulte a secção seguinte).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instalar a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir utilizar uma consola local da CLI e inicie sessão no Azure com `az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Ver o principal de serviço

Este comando seguinte demonstra como visualizar o principal de serviço de uma VM ou a aplicação com a identidade gerida ativada. Substitua `<VM or application name>` pelos seus próprios valores. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como gerir principais de serviço do Azure AD com a CLI do Azure, consulte [az ad sp](/cli/azure/ad/sp).


