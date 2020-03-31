---
title: Configuração pós-implantação utilizando extensões
description: Aprenda a utilizar extensões de modelo do Gestor de Recursos Azure para fornecer configurações pós-implementação.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023502"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fornecer configurações de pós-implementação utilizando extensões

Extensões de modelo saem pequenas aplicações que fornecem tarefas de configuração pós-implantação e automação em recursos Azure. A mais popular são as extensões de máquinas virtuais. Consulte [extensões e funcionalidades da máquina Virtual para Windows](../../virtual-machines/extensions/features-windows.md), e [extensões e funcionalidades de máquinavirtual para Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensões

As extensões existentes são:

- [Microsoft.Compute/virtualMachines/extensões](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensões](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Clusters/extensões Microsoft.HDInsight](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Servidores/bases de dados/extensões Microsoft.Sql](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/extensões de sites](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Para saber as extensões disponíveis, navegue para a referência do [modelo](https://docs.microsoft.com/azure/templates/). Em **Filtro por título,** **introduza a extensão**.

Para aprender a usar estas extensões, consulte:

- [Tutorial: Implementar extensões de máquinavirtual com modelos](template-tutorial-deploy-vm-extensions.md)de Gestor de Recursos Azure .
- [Tutorial: Importar ficheiros SQL BACPAC com modelos do Azure Resource Manager](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implementar extensões de máquina virtual com modelos do Azure Resource Manager](template-tutorial-deploy-vm-extensions.md)
