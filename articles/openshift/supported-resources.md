---
title: Recursos suportados para a Azure Red Hat OpenShift | Documentos da Microsoft
description: Compreenda as regiões do Azure e tamanhos de máquinas virtuais são suportados pelo Microsoft Azure Red Hat OpenShift.
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/15/2019
ms.openlocfilehash: 5182a5e325bd7883af1a7d102d3e02b277a5089e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788705"
---
# <a name="azure-red-hat-openshift-resources"></a>Recursos do Azure de Red Hat OpenShift

Este tópico lista as regiões do Azure e os tamanhos de máquinas virtuais suportados pelo serviço Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Regiões do Azure

Ver [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) para uma lista atualizada de regiões onde pode implementar Azure Red Hat OpenShift clusters.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais

Aqui estão os tamanhos de máquina virtual suportadas, que pode especificar para os nós de computação no seu cluster do Azure Red Hat OpenShift.

> [!Important]
> Cada VM tem um número diferente de unidades que podem ser anexados. Isso pode não ser imediatamente tão claro como memória ou o tamanho de CPU.
> Nem todos os tamanhos VM estão disponíveis em todas as regiões. Mesmo que a API suporta o tamanho especificado, poderá receber um erro se o tamanho não está disponível na região que especificar.
> Ver [os tamanhos de lista atual de VM suportada por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter mais informações.

## <a name="compute-node-sizes"></a>Tamanhos de nó de computação

Os seguintes tamanhos de nó de computação são compatíveis com a API de REST do Azure Red Hat OpenShift:

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

## <a name="master-node-sizes"></a>Tamanhos de nó principal

O seguinte mestre / tamanhos de nós de infraestrutura são suportados pela API de REST do Azure Red Hat OpenShift:

|Tamanho|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Passos Seguintes

Experimente o [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.