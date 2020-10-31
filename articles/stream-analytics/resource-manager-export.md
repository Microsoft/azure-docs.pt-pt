---
title: Exporte um azure stream analytics job Azure Resource Manager modelo
description: Este artigo descreve como exportar um modelo de Gestor de Recursos Azure para o seu trabalho Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 2afebe61c4b2998692c823e66d8fc73584ec1dc2
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125653"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Exporte um azure stream analytics job Azure Resource Manager modelo

[Os modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) permitem-lhe implementar a infraestrutura como código. O modelo é um ficheiro JavaScript Object Notation (JSON) que define a infraestrutura e configuração para os seus recursos. Especifica os recursos a implementar e as propriedades para esses recursos.

Pode recolocar um trabalho Azure Stream Analytics exportando o modelo Azure Resource Manager.

## <a name="open-a-job-in-vs-code"></a>Abrir um trabalho no Código VS

Antes de poder exportar um modelo, tem primeiro de abrir um trabalho de Stream Analytics existente no Código do Estúdio Visual. 

Para exportar um emprego para um projeto local, localize o trabalho que deseja exportar no **Stream Analytics Explorer** no portal Azure. Na página **'Consulta',** selecione **Abrir no Estúdio Visual** . Em seguida, selecione **Visual Studio Code** .

![Open Stream Analytics trabalho em Código de Estúdio Visual](./media/resource-manager-export/open-job-vs-code.png)

Para obter mais informações sobre a utilização do Código do Estúdio Visual para gerir os trabalhos do Stream Analytics, consulte o [quickstart do Código do Estúdio Visual](quick-create-visual-studio-code.md).

## <a name="compile-the-script"></a>Compilar o script 

O próximo passo é compilar o script de trabalho para um modelo de Gestor de Recursos Azure. Antes de compilar o script, certifique-se de que o seu trabalho tem pelo menos uma entrada e uma saída configurada. Se não houver entrada ou saída configurada, é necessário configurar primeiro a entrada e a saída.

1. No Código do Estúdio Visual, navegue para o ficheiro *Transformation.asaql* do seu trabalho.

   ![Processo.asaql em Código de Estúdio Visual](./media/resource-manager-export/transformation-asaql.png)

1. Clique com o botão direito no ficheiro *Transformation.asaql* e selecione **ASA: Compile Script** a partir do menu.

1. Note que uma pasta **De implementação** aparece no seu espaço de trabalho Stream Analytics.

1. Explore o *JobTemplate.jsem* ficheiro, que é o modelo de Gestão de Recursos Azure utilizado para implementar.

## <a name="complete-the-parameters-file"></a>Preencha o ficheiro de parâmetros

Em seguida, preencha o ficheiro de parâmetros do modelo de Gestão de Recursos Azure.

1. Abra a *JobTemplate.parameters.jsno* ficheiro localizado na pasta **Implementar** do seu espaço de trabalho Stream Analytics no Código do Estúdio Visual.

1. Note que as teclas de entrada e saída são nulas. Substitua os valores nulos pelas teclas de acesso reais para os seus recursos de entrada e saída.

1. Guarde o ficheiro de parâmetros.

## <a name="deploy-using-templates"></a>Implementar usando modelos

Está pronto para implementar o seu trabalho Azure Stream Analytics utilizando os modelos do Gestor de Recursos Azure que gerou na secção anterior.

Numa janela PowerShell, executar o seguinte comando. Certifique-se de que reaplet o *Nome do Grupo de Recursos,* o *TemplateFile* e *o TemplateParameterFile* com o nome do grupo de recursos reais e os caminhos completos do ficheiro para o *JobTemplate.jse* JobTemplate.parameters.js *em* ficheiros na Pasta **de Implementação** do seu espaço de trabalho.

Se não tiver a Azure PowerShell configurada, siga os passos no [módulo Install Azure PowerShell](/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Passos seguintes

* [Teste Azure Stream Analytics empregos localmente com entrada ao vivo usando Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)

* [Explore os trabalhos do Azure Stream Analytics com o Código do Estúdio Visual (Preview)](visual-studio-code-explore-jobs.md)