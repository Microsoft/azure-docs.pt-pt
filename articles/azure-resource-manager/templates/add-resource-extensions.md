---
title: Configuração pós-implantação utilizando extensões
description: Saiba como utilizar as extensões do modelo do Gestor de Recursos Azure (modelo ARM) para fornecer configurações pós-implantação.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: 23d5a9aaa546542218a6f839ab415184ff309928
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934327"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fornecer configurações pós-implantação utilizando extensões

As extensões do modelo Azure Resource Manager (modelo ARM) são pequenas aplicações que fornecem tarefas de configuração e automação pós-implantação nos recursos Azure. A mais popular são as extensões de máquinas virtuais. Consulte [extensões e funcionalidades de máquina virtual para Windows](../../virtual-machines/extensions/features-windows.md)e [extensões e funcionalidades de máquinas virtuais para Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensões

As extensões existentes são:

- [Microsoft.Compute/virtualMachines/extensões](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensões](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensões](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Servidores/bases de dados/extensões microsoft.Sql](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)
- [Microsoft.Web/sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Para saber as extensões disponíveis, navegue na referência do [modelo](/azure/templates/). In **Filter by title**, insira a **extensão**.

Para aprender a usar estas extensões, consulte:

- [Tutorial: Implementar extensões de máquinas virtuais com modelos ARM](template-tutorial-deploy-vm-extensions.md).
- [Tutorial: Importar ficheiros BACPAC SQL com modelos ARM](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implementar extensões de máquinas virtuais com modelos ARM](template-tutorial-deploy-vm-extensions.md)
