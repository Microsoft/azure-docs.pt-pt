---
title: Pré-requisitos do Azure CLI para a cache Azure HPC
description: Configurar passos antes de poder utilizar o Azure CLI para criar ou modificar uma Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87099250"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurar a CLI do Azure para o Azure HPC Cache

Siga estes passos para preparar o seu ambiente antes de utilizar o Azure CLI para criar ou gerir uma Cache Azure HPC.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Instalar a CLI do Azure

A azure HPC Cache requer a versão 2.7 ou posterior do Azure CLI. Execute `az --version` para ver que versão e bibliotecas dependentes estão instaladas. Para instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Instale a extensão cache Azure HPC

As funções de Cache Azure HPC não fazem parte da base de código principal, pelo que também deve instalar a referência de extensão. Siga as instruções abaixo.

1. Iniciar sessão

   Inicie sessão utilizando o comando [de login az](/cli/azure/reference-index#az-login) se estiver a utilizar uma versão instalada localmente do CLI.

    ```azurecli
    az login
    ```

    Siga os passos apresentados no seu terminal para concluir o processo de autenticação.

   > [!TIP]
   > Se tiver várias subscrições, tem de escolher uma. Selecione-o quando iniciar uma sessão cloud Shell no Portal Azure ou siga as instruções em [Começar com o Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in) para definir a sua subscrição a partir da linha de comando.

2. Instale a extensão Azure CLI

   As funções de Cache Azure HPC são fornecidas como uma extensão CLI Azure - ainda não faz parte do pacote CLI core. Tem de instalar a referência de extensão antes de a poder utilizar.

   As extensões Azure CLI dão-lhe acesso a comandos experimentais e pré-lançamento. Para saber mais sobre extensões, incluindo atualização e desinstalação, consulte [extensões de utilização com Azure CLI](/cli/azure/azure-cli-extensions-overview).

   Instale a extensão para Azure HPC Cache executando o seguinte comando:

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Definir grupo de recursos predefinidos (opcional)

A maioria dos comandos hpc-cache requerem que você passe o grupo de recursos da cache. Pode definir o grupo de recursos predefinidos utilizando [a configuração az](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Passos seguintes

Depois de instalar a extensão Azure CLI e iniciar sessão, pode utilizar o Azure CLI para criar e gerir os sistemas de cache Azure HPC.

* [Criar uma Cache Azure HPC](hpc-cache-create.md)
* [Documentação Azure CLI hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache)
