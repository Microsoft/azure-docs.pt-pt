---
title: Configuração pós-implantação usando extensões
description: Saiba como usar Azure Resource Manager extensões de modelo para fornecer configurações de pós-implantação.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: c7896efb453cac478202efedb268c4bc838aef3a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150367"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fornecer configurações pós-implantação usando extensões

Extensões de modelo são pequenos aplicativos que fornecem tarefas de configuração e automação de pós-implantação nos recursos do Azure. O mais popular é o de extensões de máquina virtual. Consulte [extensões e recursos de máquina virtual para Windows](../virtual-machines/extensions/features-windows.md), e [extensões de máquina virtual e recursos para Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensões

As extensões existentes são:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [VirtualMachineScaleSets/extensões Microsoft. Compute](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Clusters/extensões do Microsoft. HDInsight](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Servidores/bancos de dados/extensões Microsoft. SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Para descobrir as extensões disponíveis, navegue até a [referência de modelo](https://docs.microsoft.com/azure/templates/). Em **Filtrar por título**, insira a **extensão**.

Para saber como usar essas extensões, consulte:

- [Tutorial: implantar extensões de máquina virtual com modelos de Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Tutorial: importar arquivos BACPAC do SQL com modelos de Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: implantar extensões de máquina virtual com modelos de Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
