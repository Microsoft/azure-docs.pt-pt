---
title: Exportar um modelo de gestor de recursos azure de fluxo de fluxo azure
description: Este artigo descreve como exportar um modelo de Gestor de Recursos Azure para o seu trabalho de Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968951"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Exportar um modelo de gestor de recursos azure de fluxo de fluxo azure

Os [modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) permitem-lhe implementar a infraestrutura como código. O modelo é um ficheiro JavaScript Object Notation (JSON) que define a infraestrutura e a configuração para os seus recursos. Especifica os recursos para implantar e as propriedades para esses recursos.

Pode reimplantar um trabalho de Azure Stream Analytics exportando o modelo de Gestor de Recursos Azure.

## <a name="open-a-job-in-vs-code"></a>Abrir um emprego no Código VS

Antes de exportar um modelo, primeiro deve abrir um trabalho de Stream Analytics existente no Visual Studio Code. 

Para exportar um emprego para um projeto local, localize o trabalho que pretende exportar no **Stream Analytics Explorer** no portal Azure. A partir da página **Consulta,** selecione **Open in Visual Studio**. Em seguida, selecione **Visual Studio Code**.

![Trabalho de Open Stream Analytics no Código do Estúdio Visual](./media/resource-manager-export/open-job-vs-code.png)

Para obter mais informações sobre a utilização do Código de Estúdio Visual para gerir os trabalhos do Stream Analytics, consulte o [Visual Studio Code quickstart](quick-create-vs-code.md).

## <a name="compile-the-script"></a>Compilar o guião 

O próximo passo é compilar o guião de trabalho para um modelo de Gestor de Recursos Azure. Antes de compilar o script, certifique-se de que o seu trabalho tem pelo menos uma entrada e uma saída configurada. Se não estiver configurada nenhuma entrada ou saída, terá de configurar primeiro a entrada e a saída.

1. No Visual Studio Code, navegue para o ficheiro *Transformation.asaql* do seu trabalho.

   ![Ficheiro Transformation.asaql no Código do Estúdio Visual](./media/resource-manager-export/transformation-asaql.png)

1. Clique no ficheiro *Transformation.asaql* e **selecione ASA: Compile** script a partir do menu.

1. Note que uma pasta **Deploy** aparece no seu espaço de trabalho Stream Analytics.

1. Explore o ficheiro *JobTemplate.json,* que é o modelo de Gestão de Recursos Azure usado para implementar.

## <a name="complete-the-parameters-file"></a>Complete o ficheiro de parâmetros

Em seguida, complete o ficheiro de parâmetros do modelo de Gestão de Recursos Azure.

1. Abra o ficheiro *JobTemplate.parameters.json* localizado na pasta **de implementação** do seu espaço de trabalho stream analytics no Código de Estúdio Visual.

1. Note que as teclas de entrada e de saída são nulas. Substitua os valores nulos pelas chaves de acesso reais para os seus recursos de entrada e saída.

1. Guarde o ficheiro de parâmetros.

## <a name="deploy-using-templates"></a>Implementar usando modelos

Está pronto para implementar o seu trabalho de Análise de Fluxo seletiva utilizando os modelos do Gestor de Recursos Azure gerados na secção anterior.

Numa janela PowerShell, execute o seguinte comando. Certifique-se de reaplce o Nome do *Grupo de Recursos,* *ModeloFile*, e *TemplateParameterFile* com o nome real do grupo de recursos, e os caminhos completos de ficheiro para o *JobTemplate.json* e *JobTemplate.parameters.json* ficheiros na **pasta de implementação** do seu espaço de trabalho.

Se não tiver o Azure PowerShell configurado, siga os passos no [módulo Deinstalação Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Passos seguintes

* [Test Azure Stream Analytics empregos localmente com entrada ao vivo usando Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)

* [Explore os trabalhos do Azure Stream Analytics com código de estúdio visual (pré-visualização)](visual-studio-code-explore-jobs.md)