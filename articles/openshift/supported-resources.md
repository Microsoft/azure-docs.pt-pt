---
title: Recursos suportados para o OpenShift do Chapéu Vermelho Azure
description: Entenda quais as regiões azure e tamanhos de máquinas virtuais suportados pelo Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381431"
---
# <a name="azure-red-hat-openshift-resources"></a>Chapéu Vermelho Azure OpenShift recursos

Este tópico lista as regiões azure e tamanhos de máquinas virtuais suportados pelo serviço Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Regiões do Azure

Consulte [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) para uma lista atual de regiões onde pode implantar clusters OpenShift do Chapéu Vermelho Azure.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais

Aqui estão os tamanhos da máquina virtual suportado que pode especificar para os nós de computação no seu cluster Azure Red Hat OpenShift.

> [!Important]
> Cada VM tem um número diferente de unidades que podem ser anexadas. Isto pode não ser tão imediatamente claro como o tamanho da memória ou do CPU.
> Nem todos os tamanhos vm estão disponíveis em todas as regiões. Mesmo que a API suporte o tamanho que especifica, poderá obter um erro se o tamanho não estiver disponível na região que especifica.
> Consulte a lista atual de tamanhos de [VM suportados por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter mais informações.

## <a name="compute-node-sizes"></a>Tamanhos do nó computacional

Os seguintes tamanhos de nó computacional são suportados pela API OpenShift REST Do chapéu vermelho azure:

|Tamanho|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Tamanhos do nó mestre

Os seguintes tamanhos de nós de mestre/infraestrutura são suportados pela API openshift REST Do chapéu vermelho azure:

|Tamanho|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial de [cluster Create a Azure Red Hat OpenShift.](tutorial-create-cluster.md)
