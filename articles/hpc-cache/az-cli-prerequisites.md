---
title: Pré-requisitos do Azure CLI para a cache Azure HPC
description: Configurar passos antes de poder utilizar o Azure CLI para criar ou modificar uma Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94654461"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurar a CLI do Azure para o Azure HPC Cache

Siga estes passos para preparar o seu ambiente antes de utilizar o Azure CLI para criar ou gerir uma Cache Azure HPC.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - A azure HPC Cache requer a versão 2.7 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="set-default-resource-group-optional"></a>Definir grupo de recursos predefinidos (opcional)

A maioria dos comandos hpc-cache requerem que você passe o grupo de recursos da cache. Pode definir o grupo de recursos predefinidos utilizando [a configuração az](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Passos seguintes

Depois de instalar a extensão Azure CLI e iniciar sessão, pode utilizar o Azure CLI para criar e gerir os sistemas de cache Azure HPC.

* [Criar uma Cache Azure HPC](hpc-cache-create.md)
* [Documentação Azure CLI hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache)
