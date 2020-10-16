---
title: Configuração pós-implantação utilizando extensões
description: Saiba como utilizar as extensões de modelo do Azure Resource Manager para fornecer configurações pós-implantação.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: be55138a2aa6dc0552c7556438ffd43705687c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86055055"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fornecer configurações pós-implantação utilizando extensões

As extensões de modelo são pequenas aplicações que fornecem tarefas de configuração e automação pós-implantação nos recursos Azure. A mais popular são as extensões de máquinas virtuais. Consulte [extensões e funcionalidades de máquina virtual para Windows](../../virtual-machines/extensions/features-windows.md)e [extensões e funcionalidades de máquinas virtuais para Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensões

As extensões existentes são:

- [Microsoft.Compute/virtualMachines/extensões](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensões](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensões](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Servidores/bases de dados/extensões microsoft.Sql](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Para saber as extensões disponíveis, navegue na referência do [modelo](/azure/templates/). In **Filter by title**, insira a **extensão**.

Para aprender a usar estas extensões, consulte:

- [Tutorial: Implementar extensões de máquinas virtuais com modelos do Gestor de Recursos Azure](template-tutorial-deploy-vm-extensions.md).
- [Tutorial: Importar ficheiros SQL BACPAC com modelos do Azure Resource Manager](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implementar extensões de máquina virtual com modelos do Azure Resource Manager](template-tutorial-deploy-vm-extensions.md)
