---
title: Pré-requisitos do Azure CLI para a cache Azure HPC
description: Configurar passos antes de poder utilizar o Azure CLI para criar ou modificar uma Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0b8e1158bc60c4cceea508db988000fe952a90a4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864294"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurar a CLI do Azure para o Azure HPC Cache

Siga estes passos para preparar o seu ambiente antes de utilizar o Azure CLI para criar ou gerir uma Cache Azure HPC.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - A azure HPC Cache requer a versão 2.7 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="set-default-resource-group-optional"></a>Definir grupo de recursos predefinidos (opcional)

A maioria dos comandos hpc-cache requerem que você passe o grupo de recursos da cache. Pode definir o grupo de recursos predefinidos utilizando [a configuração az](/cli/azure/reference-index#az_configure).

## <a name="next-steps"></a>Passos seguintes

Depois de instalar a extensão Azure CLI e iniciar sessão, pode utilizar o Azure CLI para criar e gerir os sistemas de cache Azure HPC.

* [Criar uma Cache Azure HPC](hpc-cache-create.md)
* [Documentação Azure CLI hpc-cache](/cli/azure/hpc-cache)
