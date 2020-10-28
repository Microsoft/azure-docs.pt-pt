---
title: Criar clusters Apache Hadoop usando modelos - Azure HDInsight
description: Saiba como criar clusters para HDInsight utilizando modelos de Gestor de Recursos
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/07/2020
ms.openlocfilehash: aa42e9691eac223e7e72da0d792d14262470e15c
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748770"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Crie clusters Apache Hadoop em HDInsight utilizando modelos de Gestor de Recursos

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste artigo, você aprende várias maneiras de criar clusters Azure HDInsight usando [modelos Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). Para saber mais sobre outras ferramentas e funcionalidades de criação de clusters, clique no seletor de separadores no topo desta página. Consulte também os [métodos de criação de cluster.](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

Um modelo de Gestor de Recursos facilita a criação dos seguintes recursos para a sua aplicação numa única operação coordenada:

* Clusters HDInsight e seus recursos dependentes (como a conta de armazenamento padrão).
* Outros recursos (como a Base de Dados Azure SQL para utilizar [o Apache Sqoop).](https://sqoop.apache.org/)

No modelo, define-se os recursos necessários para a aplicação. Também especifica parâmetros de implementação para valores de entrada para diferentes ambientes. O modelo consiste em JSON e expressões que usa para construir valores para a sua implantação.

Você pode encontrar amostras de modelo HDInsight em [modelos de arranque rápido Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Utilize o [Código do Estúdio Visual](https://code.visualstudio.com/#alt-downloads) com a [extensão do Gestor de Recursos](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) ou um editor de texto para guardar o modelo num ficheiro na sua estação de trabalho.

Para obter mais informações sobre os modelos do Gestor de Recursos, consulte os seguintes artigos e exemplos:

* [Modelos de gestor de recursos do autor Azure](../azure-resource-manager/templates/template-syntax.md)
* [Implementar uma aplicação com modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md)
* Referência do modelo [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Modelos de arranque rápido Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Gerar modelos

O Gestor de Recursos permite-lhe exportar um modelo de Gestor de Recursos a partir de recursos existentes na sua subscrição utilizando diferentes ferramentas. Pode utilizar esse modelo gerado para saber mais sobre a sintaxe do modelo ou para automatizar a reimplementação da sua solução conforme necessário. Para obter mais informações, consulte [os modelos de exportação.](../azure-resource-manager/templates/export-template-portal.md)

## <a name="deploy-using-the-portal"></a>Implementar usando o portal

Pode implementar um modelo de Gestor de Recursos utilizando o portal Azure. Para obter mais informações, consulte [implementar recursos a partir do modelo personalizado.](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)

## <a name="deploy-using-powershell"></a>Implementar com o PowerShell

Pode implementar um modelo de Gestor de Recursos utilizando o Azure PowerShell. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [implementar o modelo de Gestor de Recursos Privados com token SAS e Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure

Pode implementar um modelo de Gestor de Recursos utilizando o Azure CLI. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure CLI](../azure-resource-manager/templates/deploy-cli.md) e [implementar o modelo de Gestor de Recursos Privados com token SAS e Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Implementar utilizando a API REST

Pode implementar um modelo de Gestor de Recursos utilizando a API REST. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e API do Gestor de Recursos](../azure-resource-manager/templates/deploy-rest.md)REST.

## <a name="deploy-with-visual-studio"></a>Implementar com o Visual Studio

 Utilize o Visual Studio para criar um projeto de grupo de recursos e implantá-lo para a Azure através da interface do utilizador. Selecione o tipo de recursos a incluir no seu projeto. Esses recursos são automaticamente adicionados ao modelo de Gestor de Recursos. O projeto também fornece um script PowerShell para implementar o modelo.

Para uma introdução à utilização do Visual Studio com grupos de recursos, consulte [criar e implementar grupos de recursos Azure através do Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu várias formas de criar um cluster HDInsight. Para saber mais, leia os artigos seguintes:

* Para obter mais modelos relacionados com HDInsight, consulte [os modelos de arranque rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Para um exemplo de implantação de recursos através da biblioteca de clientes .NET, consulte [recursos de implantação utilizando bibliotecas .NET e um modelo](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obter um exemplo aprofundado de implantação de uma aplicação, consulte [Provisão e implemente microserviços previsivelmente em Azure](../app-service/deploy-complex-application-predictably.md).
* Para obter orientações sobre a implementação da sua solução em ambientes diferentes, consulte [Ambientes de desenvolvimento e teste no Microsoft Azure](../devtest-labs/devtest-lab-overview.md).
* Para saber mais sobre as secções do modelo do Gestor de Recursos Azure, consulte [os modelos de autoria](../azure-resource-manager/templates/template-syntax.md).
* Para obter uma lista das funções que pode utilizar num modelo de Gestor de Recursos Azure, consulte [as funções do Modelo](../azure-resource-manager/templates/template-functions.md).
