---
title: Ativar insights VM usando modelos de Gestor de Recursos
description: Este artigo descreve como permite insights VM para uma ou mais máquinas virtuais Azure ou conjuntos de escala de máquina virtual utilizando modelos Azure PowerShell ou Azure Resource Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: fc0c304a3fea81f44e01d3e815f34e44728ea42e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031884"
---
# <a name="enable-vm-insights-using-resource-manager-templates"></a>Ativar insights VM usando modelos de Gestor de Recursos
Este artigo descreve como ativar insights VM para uma máquina virtual ou conjunto de escala de máquina virtual usando modelos de Gestor de Recursos. Este procedimento pode ser utilizado para o seguinte procedimento:

- Máquina virtual do Azure
- Conjunto de escala de máquina virtual Azure
- Máquina virtual híbrida ligada ao Arco Azure

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho Log Analytics](./vminsights-configure-workspace.md). 
- Consulte [sistemas operativos suportados](./vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operativo da máquina virtual ou da balança de máquinas virtuais que está a ativar é suportado. 

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

Criámos modelos de gestores de recursos Azure para embarcar as suas máquinas virtuais e conjuntos de escala de máquinas virtuais. Estes modelos incluem cenários que pode utilizar para permitir a monitorização de um recurso existente e criar um novo recurso que tenha ativado a monitorização.

>[!NOTE]
>O modelo precisa de ser implantado no mesmo grupo de recursos que a máquina virtual ou o conjunto de escala de máquina virtual que está a ser ativado.


Os modelos do Gestor de Recursos Azure são fornecidos num ficheiro de arquivo (.zip) que pode [descarregar](https://aka.ms/VmInsightsARMTemplates) a partir do nosso repo GitHub. O conteúdo do ficheiro inclui pastas que representam cada cenário de implantação com um modelo e ficheiro de parâmetros. Antes de executá-los, modifique o ficheiro de parâmetros e especifique os valores necessários. 

O ficheiro de descarregamento contém os seguintes modelos para diferentes cenários:

- O modelo **de tabuleiro de marLm existente** permite insights de VM se a máquina virtual já existe.
- O modelo **newVmOnboarding** cria uma máquina virtual e permite insights VM para monitorizá-lo.
- O modelo **de prancha de vídeoVmss existente** permite insights de VM se o conjunto de escala de máquina virtual já existir.
- O modelo **newVmssOnboarding** cria conjuntos de escala de máquina virtual e permite insights VM para monitorizá-los.
- O modelo **ConfigureWorkspace** configura o seu espaço de trabalho Log Analytics para suportar insights VM, permitindo as soluções e a recolha de contadores de desempenho do sistema operativo Linux e Windows.

>[!NOTE]
>Se já estavam **presentes conjuntos** de escala de máquina virtual e a política de atualização está definida como **Manual,** os insights VM não serão ativados por defeito após a execução do modelo existente do Gestor de Recursos Azure. Tem que atualizar manualmente as ocorrências.

## <a name="deploy-templates"></a>Implementar modelos
Os modelos podem ser implementados usando [qualquer método de implementação para modelos de Gestor de Recursos,](../../azure-resource-manager/templates/deploy-powershell.md) incluindo os seguintes exemplos usando PowerShell e CLI.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Passos seguintes

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com insights VM.

- Para ver as dependências de aplicações descobertas, consulte [o Mapa de insights VM da Vista.](vminsights-maps.md)

- Para identificar estrangulamentos e utilização global com o desempenho do seu VM, consulte [Ver Azure VM Performance](vminsights-performance.md).