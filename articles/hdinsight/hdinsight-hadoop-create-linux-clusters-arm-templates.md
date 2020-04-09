---
title: Crie clusters Apache Hadoop usando modelos - Azure HDInsight
description: Aprenda a criar clusters para hDInsight usando modelos de Gestor de Recursos
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 67d5481b82b7469c5ae55704768ce494dbc9dca5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879144"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Crie clusters Apache Hadoop em HDInsight usando modelos de Gestor de Recursos

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste artigo, aprende-se várias formas de criar clusters Azure HDInsight utilizando modelos de [Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md). Para saber mais sobre outras ferramentas e funcionalidades de criação de clusters, clique no seletor de separadores na parte superior desta página. Ver também, Métodos de [criação de Cluster.](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

Um modelo de Gestor de Recursos facilita a criação dos seguintes recursos para a sua aplicação numa única operação coordenada:

* Os clusters HDInsight e os seus recursos dependentes (como a conta de armazenamento por defeito).
* Outros recursos (como a Base de Dados Azure SQL para utilizar [o Apache Sqoop).](https://sqoop.apache.org/)

No modelo, define os recursos necessários para a aplicação. Também especifica parâmetros de implantação para valores de entrada para diferentes ambientes. O modelo consiste em JSON e expressões que utiliza para construir valores para a sua implantação.

Pode encontrar amostras de modelo HDInsight em [modelos de quickstart Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Utilize o [Código](https://code.visualstudio.com/#alt-downloads) do Estúdio Visual transversal com a [extensão do Gestor](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) de Recursos ou um editor de texto para guardar o modelo num ficheiro da sua estação de trabalho.

Para obter mais informações sobre os modelos do Gestor de Recursos, consulte os seguintes artigos e exemplos:

* [Modelos de Gestor de Recursos Do Autor Azure](../azure-resource-manager/templates/template-syntax.md)
* [Implementar uma aplicação com modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md)
* Referência do modelo [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Modelos de quickstart azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Gerar modelos

O Gestor de Recursos permite-lhe exportar um modelo de Gestor de Recursos a partir de recursos existentes na sua subscrição utilizando diferentes ferramentas. Pode utilizar esse modelo gerado para saber mais sobre a sintaxe do modelo ou para automatizar a reimplementação da sua solução conforme necessário. Para mais informações, consulte os modelos de [exportação.](../azure-resource-manager/templates/export-template-portal.md)

## <a name="deploy-using-the-portal"></a>Implementar usando o portal

Pode implementar um modelo de Gestor de Recursos utilizando o portal Azure. Para mais informações, consulte [Implementar recursos a partir de modelo personalizado](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Implementar com o PowerShell

Pode implementar um modelo de Gestor de Recursos utilizando o Azure PowerShell. Para mais informações, consulte [a implantação de recursos com modelos de Gestor de Recursos e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e implante o modelo de Gestor de [Recursos privados com token SAS e Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure

Pode implementar um modelo de Gestor de Recursos utilizando o Azure CLI. Para mais informações, consulte [a implantação de recursos com modelos de Gestor de Recursos e o Azure CLI](../azure-resource-manager/templates/deploy-cli.md) e implante o modelo de Gestor de [Recursos privados com token SAS e Azure CLI](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Implementar utilizando a API REST

Pode implementar um modelo de Gestor de Recursos utilizando a API REST. Para mais informações, consulte API de [Implantação de recursos com modelos](../azure-resource-manager/templates/deploy-rest.md)de Gestor de Recursos e Gestor de Recursos REST API .

## <a name="deploy-with-visual-studio"></a>Implementar com o Visual Studio

 Utilize o Visual Studio para criar um projeto de grupo de recursos e implementá-lo para o Azure através da interface de utilizador. Seleciona o tipo de recursos a incluir no seu projeto. Esses recursos são automaticamente adicionados ao modelo do Gestor de Recursos. O projeto também fornece um script PowerShell para implementar o modelo.

Para uma introdução à utilização do Visual Studio com grupos de recursos, consulte criar e implantar grupos de [recursos Azure através do Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu várias formas de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* Para obter mais modelos relacionados com hDInsight, consulte [os modelos de arranque rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Para um exemplo de implantação de recursos através da biblioteca de clientes .NET, consulte os [recursos de implantação utilizando bibliotecas .NET e um modelo](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para um exemplo aprofundado de implementação de uma aplicação, consulte [a Provision e implemente microserviços previsivelmente em Azure](../app-service/deploy-complex-application-predictably.md).
* Para obter orientações sobre a implementação da sua solução em ambientes diferentes, consulte [Ambientes de desenvolvimento e teste no Microsoft Azure](../solution-dev-test-environments.md).
* Para conhecer as secções do modelo do Gestor de Recursos Azure, consulte [os modelos de autor .](../azure-resource-manager/templates/template-syntax.md)
* Para obter uma lista das funções que pode utilizar num modelo de Gestor de Recursos Azure, consulte [funções de modelo](../azure-resource-manager/templates/template-functions.md).
