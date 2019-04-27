---
title: Fornecer as configurações de pós-implementação ao utilizar extensões - Azure | Documentos da Microsoft
description: Saiba como utilizar as extensões de modelo do Azure Resource Manager para fornecer configurações de pós-implementação.
services: azure-resource-manager
documentationcenter: na
author: mumian
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: eb46966c3a28b3fa4c2b23668109b7c5d23a609b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390873"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fornecer as configurações de pós-implementação através de extensões

As extensões de modelo são pequenos aplicativos que fornecem configuração pós-implantação e tarefas de automação em recursos do Azure. Mais popular é extensões de máquina virtual. Ver [extensões de Máquina Virtual e funcionalidades para Windows](../virtual-machines/extensions/features-windows.md), e [extensões de Máquina Virtual e funcionalidades para Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensões

As extensões existentes são:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. Compute virtualMachineScaleSets/extensões](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Clusters/extensões da Microsoft](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Servidores/bases de dados/extensões da Microsoft. SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Para obter as extensões disponíveis, navegue para o [referência de modelo](https://docs.microsoft.com/azure/templates/). Na **filtrar por título**, introduza **extensão**.

Para saber como utilizar estas extensões, consulte:

- [Tutorial: Implementar extensões de máquina virtual com modelos Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Tutorial: Importar ficheiros BACPAC do SQL com modelos Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implementar extensões de máquina virtual com modelos Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
