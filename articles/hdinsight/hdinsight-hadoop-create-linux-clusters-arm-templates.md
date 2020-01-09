---
title: Criar clusters Apache Hadoop usando modelos – Azure HDInsight
description: Saiba como criar clusters para o HDInsight usando modelos do Resource Manager
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: c840cf452f047177c1244caedf09d976f0514961
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435539"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste artigo, você aprende várias maneiras de criar clusters do Azure HDInsight usando modelos de Azure Resource Manager. Para obter mais informações, consulte [implantar um aplicativo com Azure Resource Manager modelo](../azure-resource-manager/resource-group-template-deploy.md). Para saber mais sobre outras ferramentas e recursos de criação de cluster, clique no seletor de guias na parte superior desta página ou consulte [métodos de criação de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma [subscrição do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell e/ou CLI do Azure.

### <a name="resource-manager-templates"></a>Modelos do Gestor de Recursos

Um modelo do Resource Manager facilita a criação dos seguintes recursos para seu aplicativo em uma única operação coordenada:
* Clusters HDInsight e seus recursos dependentes (como a conta de armazenamento padrão).
* Outros recursos (como o banco de dados SQL do Azure para usar o [Apache Sqoop](https://sqoop.apache.org/)).

No modelo, você define os recursos que são necessários para o aplicativo. Você também especifica parâmetros de implantação para valores de entrada para ambientes diferentes. O modelo consiste em JSON e expressões que você usa para construir valores para sua implantação.

Você pode encontrar exemplos de modelo do HDInsight em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Use [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) de plataforma cruzada com a [extensão do Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) ou um editor de texto para salvar o modelo em um arquivo em sua estação de trabalho.

Para obter mais informações sobre modelos do Resource Manager, consulte os seguintes artigos e exemplos:

* [Criar modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Implantar um aplicativo com modelos de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* Referência de modelo [do Microsoft. HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Gerar modelos

O Gerenciador de recursos permite exportar um modelo do Resource Manager de recursos existentes em sua assinatura usando ferramentas diferentes. Pode utilizar esse modelo gerado para saber mais sobre a sintaxe do modelo ou para automatizar a reimplementação da sua solução conforme necessário. Para obter mais informações, consulte [Export templates](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Implantar usando o portal

Você pode implantar um modelo do Resource Manager usando o portal do Azure. Para obter mais informações, consulte [implantar recursos do modelo personalizado](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Implementar com o PowerShell

Você pode implantar um modelo do Resource Manager usando Azure PowerShell. Para obter mais informações, consulte [implantar recursos com modelos do Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [implantar o modelo do Resource Manager privado com o token SAS e Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure

Você pode implantar um modelo do Resource Manager usando CLI do Azure. Para obter mais informações, consulte [implantar recursos com modelos do Resource Manager e CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) e [implantar o modelo do Resource Manager privado com o token SAS e CLI do Azure](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Implantar usando a API REST

Você pode implantar um modelo do Resource Manager usando a API REST. Para obter mais informações, consulte [implantar recursos com modelos do Resource Manager e a API REST do Gerenciador de recursos](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Implementar com o Visual Studio

 Use o Visual Studio para criar um projeto de grupo de recursos e implantá-lo no Azure por meio da interface do usuário. Você seleciona o tipo de recursos a serem incluídos em seu projeto. Esses recursos são adicionados automaticamente ao modelo do Resource Manager. O projeto também fornece um script do PowerShell para implantar o modelo.

Para obter uma introdução ao uso do Visual Studio com grupos de recursos, consulte [criando e implantando grupos de recursos do Azure por meio do Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, confira os seguintes artigos:

* Para obter mais modelos relacionados ao HDInsight, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Para obter um exemplo de implantação de recursos por meio da biblioteca de cliente .NET, consulte [implantar recursos usando bibliotecas do .net e um modelo](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obter um exemplo detalhado de implantação de um aplicativo, consulte [provisionar e implantar microservices previsíveis no Azure](../app-service/deploy-complex-application-predictably.md).
* Para obter orientações sobre a implementação da sua solução em ambientes diferentes, consulte [Ambientes de desenvolvimento e teste no Microsoft Azure](../solution-dev-test-environments.md).
* Para saber mais sobre as seções do modelo de Azure Resource Manager, consulte [criando modelos](../azure-resource-manager/templates/template-syntax.md).
* Para obter uma lista das funções que você pode usar em um modelo de Azure Resource Manager, consulte [funções de modelo](../azure-resource-manager/resource-group-template-functions.md).
