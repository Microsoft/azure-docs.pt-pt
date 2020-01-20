---
title: Recursos com suporte para o Azure Red Hat OpenShift
description: Entenda quais regiões do Azure e tamanhos de máquina virtual têm suporte Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274909"
---
# <a name="azure-red-hat-openshift-resources"></a>Recursos do Azure Red Hat OpenShift

Este tópico lista as regiões do Azure e os tamanhos de máquina virtual com suporte pelo Microsoft Azure serviço Red Hat OpenShift.

## <a name="azure-regions"></a>Regiões do Azure

Consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) para obter uma lista atual de regiões em que você pode implantar clusters do Azure Red Hat OpenShift.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais

Aqui estão os tamanhos de máquina virtual com suporte que você pode especificar para os nós de computação em seu cluster do Azure Red Hat OpenShift.

> [!Important]
> Cada VM tem um número diferente de unidades que podem ser anexadas. Isso pode não estar tão claro quanto o tamanho da memória ou da CPU.
> Nem todos os tamanhos de VM estão disponíveis em todas as regiões. Mesmo que a API ofereça suporte ao tamanho especificado, você poderá receber um erro se o tamanho não estiver disponível na região que você especificar.
> Consulte a [lista atual de tamanhos de VM com suporte por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter mais informações.

## <a name="compute-node-sizes"></a>Tamanhos de nó de computação

Os seguintes tamanhos de nó de computação têm suporte da API REST do Azure Red Hat OpenShift:

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

## <a name="master-node-sizes"></a>Tamanhos de nó mestre

Os seguintes tamanhos de nó mestre/infraestrutura têm suporte da API REST do Azure Red Hat OpenShift:

|Tamanho|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .
