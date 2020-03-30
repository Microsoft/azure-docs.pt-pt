---
title: Ver serviço principal de uma identidade gerida - Azure CLI - Azure AD
description: Instruções passo a passo para visualizar o diretor de serviço de uma identidade gerida utilizando o Azure CLI.
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
ms.openlocfilehash: 6fedef003c6d4143d3ad1814081d55b61d5ee020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298704"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Ver o principal de serviço de uma identidade gerida usando o Azure CLI

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação da AD Azure sem ter credenciais no seu código. 

Neste artigo, aprende-se a ver o principal de serviço de uma identidade gerida utilizando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tem uma conta Azure, [inscreva-se para uma conta gratuita.](https://azure.microsoft.com/free/)
- Ativar [o sistema de identidade atribuída numa máquina](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) virtual ou [aplicação](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Para executar os exemplos do script CLI, tem três opções:
    - Utilize a Casca de [Nuvem Azure](../../cloud-shell/overview.md) a partir do portal Azure (ver secção seguinte).
    - Utilize a casca de nuvem azure incorporada através do botão "Try It", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir utilizar uma consola CLI local e iniciar sessão no Azure usando`az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Ver o diretor de serviço

Este comando seguinte demonstra como visualizar o diretor de serviço de um VM ou aplicação com identidade gerida ativada. Substitua-os `<VM or application name>` com os seus próprios valores. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão dos principais de serviço sia, utilizando o Azure CLI, consulte [az ad sp](/cli/azure/ad/sp).


