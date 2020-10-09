---
title: Recursos suportados para o Azure Red Hat OpenShift 3.11
description: Compreenda quais as regiões Azure e tamanhos de máquinas virtuais suportados pelo Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 38203cede755d776ba9142ad16e1bea1fd1637a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82203680"
---
# <a name="azure-red-hat-openshift-resources"></a>Recursos abertos do chapéu vermelho Azure

Este tópico lista as regiões Azure e tamanhos de máquinas virtuais suportados pelo serviço Microsoft Azure Red Hat OpenShift 3.11.

## <a name="azure-regions"></a>Regiões do Azure

Consulte [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) para obter uma lista atual de regiões onde pode implantar clusters Azure Red Hat OpenShift.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais

Aqui estão os tamanhos de máquina virtual suportados que pode especificar para os nós de computação no seu cluster Azure Red Hat OpenShift.

> [!Important]
> Cada VM tem um número diferente de unidades que podem ser anexadas. Isto pode não ser tão imediatamente claro como o tamanho da memória ou do CPU.
> Nem todos os tamanhos de VM estão disponíveis em todas as regiões. Mesmo que a API suporte o tamanho especificado, poderá obter um erro se o tamanho não estiver disponível na região que especifica.
> Consulte [a lista atual de tamanhos de VM suportados por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter mais informações.

## <a name="compute-node-sizes"></a>Tamanhos dos nódoas de computação

Os seguintes tamanhos de nó computacional são suportados pela AZure Red Hat OpenShift REST API:

|Tamanho|vCPU|RAM|
|-|-|-|
|D4s padrão v3|4|16 GB|
|D8s padrão v3|8|32 GB|
|D16s padrão v3|16|64 GB|
|D32s v3 padrão|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|F8s padrão v2|8|16 GB|
|Padrão F16s v2|16|32 GB|
|F32s padrão v2|32|64 GB|

## <a name="master-node-sizes"></a>Tamanhos do nó principal

Os seguintes tamanhos de nó master/infraestrutura são suportados pela AZure Red Hat OpenShift REST API:

|Tamanho|vCPU|RAM|
|-|-|-|
|D4s padrão v3|4|16 GB|
|D8s padrão v3|8|32 GB|
|D16s padrão v3|16|64 GB|
|D32s v3 padrão|32|128 GB|

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial [de cluster Create azure Red Hat OpenShift.](tutorial-create-cluster.md)
