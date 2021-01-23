---
title: Tamanhos disponíveis para Serviços Azure Cloud (suporte alargado)
description: Tamanhos disponíveis para implementações de Azure Cloud Services (suporte alargado)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744654"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Tamanhos disponíveis para Serviços Azure Cloud (suporte alargado)

Este artigo descreve os tamanhos de máquinas virtuais disponíveis para os casos de Serviços cloud (suporte alargado).   

| Família de SKU |  ACU/ Núcleo | 
|---|---|
| [A5-7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[D](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160 - 190* |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160 - 190* |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160 - 190*
|[G](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180-240* |
|[H](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 - 300* | 

>[!NOTE]
> As ACUs marcadas com um * utilizam a tecnologia Intel® Turbo para aumentar a frequência de CPU e proporcionar um aumento do desempenho. A quantidade do aumento pode variar consoante o tamanho da VM, a carga de trabalho e outras cargas de trabalho em execução no mesmo anfitrião.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Configure tamanhos para serviços em nuvem (suporte alargado)

Pode especificar o tamanho da máquina virtual de uma instância de função como parte do modelo de serviço no ficheiro de definição de serviço. O tamanho da função determina o número de núcleos de CPU, capacidade de memória e o tamanho do sistema de ficheiros local.

Por exemplo, definir o tamanho da instância de função web `Standard_D2` para: 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Alterar o tamanho de um papel existente

Para alterar o tamanho de uma função existente, altere o tamanho da máquina virtual no ficheiro de definição de serviço (csdef), reembale o seu Serviço cloud e reimplante-o. 

## <a name="get-a-list-of-available-sizes"></a>Obtenha uma lista de tamanhos disponíveis 

Para obter uma lista de tamanhos disponíveis consulte [Resource Skus - List](https://docs.microsoft.com/rest/api/compute/resourceskus/list) e aplique os seguintes filtros:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Próximos passos 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
