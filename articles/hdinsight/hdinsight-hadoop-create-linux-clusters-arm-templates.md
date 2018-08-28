---
title: Criar clusters do Hadoop com os modelos - Azure HDInsight
description: Saiba como criar clusters de HDInsight utilizando modelos do Resource Manager
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: 9a48b3ff9769e2757ac7930feb14d6537d96fae3
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43109243"
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Criar clusters do Hadoop no HDInsight com modelos do Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste artigo, irá aprender as várias formas de criar clusters do Azure HDInsight com modelos Azure Resource Manager. Para obter mais informações, consulte [implementar uma aplicação com o modelo Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Para saber mais sobre outras funcionalidades e ferramentas de criação do cluster, clique o Seletor de separador na parte superior desta página ou veja [métodos de criação do Cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para seguir as instruções neste artigo, terá de:

* Uma [subscrição do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* CLI do Azure PowerShell e/ou do Azure.

### <a name="resource-manager-templates"></a>Modelos do Resource Manager
Um modelo do Resource Manager torna mais fácil criar o resoruces seguintes para a sua aplicação numa operação única e coordenada:
* Clusters do HDInsight e os recursos dependentes (por exemplo, a conta de armazenamento predefinida)
* Outros recursos (por exemplo, o Azure SQL Database para utilizar o Apache Sqoop)

O modelo, vai definir os recursos que são necessários para a aplicação. Também pode especificar parâmetros de implementação para introduzir os valores para diferentes ambientes. O modelo é constituído por JSON e expressões que utilizar para construir valores para a sua implementação.

Pode encontrar exemplos de modelos de HDInsight no [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Utilizar várias plataformas [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) com o [extensão do Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) ou um editor de texto para guardar o modelo para um ficheiro na sua estação de trabalho. 

Para obter mais informações sobre modelos do Resource Manager, consulte os artigos seguintes:

* [Criar modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Implementar uma aplicação com modelos Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Gerar modelos

O Resource Manager permite-lhe exportar um modelo do Resource Manager a partir de recursos existentes na sua subscrição, utilizando ferramentas diferentes. Pode utilizar esse modelo gerado para saber mais sobre a sintaxe do modelo ou para automatizar a reimplementação da sua solução conforme necessário.

- Portal do Azure: veja [exportar um modelo Azure Resource Manager a partir de recursos existentes](../azure-resource-manager/resource-manager-export-template.md).
- O Azure PowerShell: Veja [modelos de exportar o Azure Resource Manager com o PowerShell](../azure-resource-manager/resource-manager-export-template-powershell.md).
- CLI do Azure: Veja [modelos de exportar o Azure Resource Manager com CLI do Azure](../azure-resource-manager/resource-manager-export-template-cli.md).


## <a name="deploy-using-the-portal"></a>Implementar com o portal

Pode implementar um modelo do Resource Manager com o portal do Azure. Para obter mais informações, consulte [implementar recursos de modelo personalizado](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Implementar com o PowerShell

Pode implementar um modelo do Resource Manager com o Azure PowerShell. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [implementar privado modelo do Resource Manager com o SAS token e o Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-cli"></a>Implementar com a CLI

Pode implementar um modelo do Resource Manager com CLI do Azure. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) e [implementar privado modelo do Resource Manager com CLI do Azure e o SAS token](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Implementar com a API REST
Pode implementar um modelo do Resource Manager com a REST API. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e API de REST do Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Implementar com o Visual Studio
 Utilize o Visual Studio para criar um projeto do grupo de recursos e implementá-la para o Azure através da interface de utilizador. Selecione o tipo de recursos para incluir no seu projeto. Esses recursos são adicionados automaticamente para o modelo do Resource Manager. O projeto também fornece um script do PowerShell para implementar o modelo.

Para obter uma introdução ao uso do Visual Studio com grupos de recursos, consulte [criar e implementar grupos de recursos do Azure através do Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a várias formas de criar um cluster do HDInsight. Para obter mais informações, consulte os artigos seguintes:

* Para obter mais HDInsight modelos relacionados, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Para obter um exemplo de implementação de recursos por meio da biblioteca de cliente .NET, consulte [implementar recursos com bibliotecas .NET e um modelo](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obter um exemplo de implantação de um aplicativo, consulte [aprovisionar e implementar microsserviços de forma previsível no Azure](../app-service/app-service-deploy-complex-application-predictably.md).
* Para obter orientações sobre a implementação da sua solução em ambientes diferentes, consulte [Ambientes de desenvolvimento e teste no Microsoft Azure](../solution-dev-test-environments.md).
* Para saber mais sobre as secções do modelo do Azure Resource Manager, veja [criação de modelos](../azure-resource-manager/resource-group-authoring-templates.md).
* Para obter uma lista das funções que pode usar num modelo Azure Resource Manager, consulte [funções de modelo](../azure-resource-manager/resource-group-template-functions.md).
