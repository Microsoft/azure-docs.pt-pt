---
title: Ativar o Monitor Azure para VMs utilizando modelos de gestor de recursos
description: Este artigo descreve como permite o Azure Monitor para VMs para uma ou mais máquinas virtuais Azure ou conjuntos de balanças de máquinas virtuais utilizando modelos Azure PowerShell ou Azure Resource Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 89a9a1b762e02237a8ee08dca5d6eedefabaafbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87328356"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>Ativar o Monitor Azure para VMs utilizando modelos de gestor de recursos
Este artigo descreve como ativar o Azure Monitor para VMs para uma máquina virtual ou conjunto de escala de máquina virtual usando modelos de Gestor de Recursos. Este procedimento pode ser utilizado para o seguinte procedimento:

- Máquina virtual do Azure
- Conjunto de escala de máquina virtual Azure
- Máquina virtual híbrida ligada ao Arco Azure

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho Log Analytics](vminsights-configure-workspace.md). 
- Consulte [sistemas operativos suportados](vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operativo da máquina virtual ou da balança de máquinas virtuais que está a ativar é suportado. 

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

Criámos modelos de gestores de recursos Azure para embarcar as suas máquinas virtuais e conjuntos de escala de máquinas virtuais. Estes modelos incluem cenários que pode utilizar para permitir a monitorização de um recurso existente e criar um novo recurso que tenha ativado a monitorização.

>[!NOTE]
>O modelo precisa de ser implantado no mesmo grupo de recursos que a máquina virtual ou o conjunto de escala de máquina virtual que está a ser ativado.


Os modelos Azure Resource Manager são fornecidos num ficheiro de arquivo (.zip) que pode [descarregar](https://aka.ms/VmInsightsARMTemplates) a partir do nosso repo GitHub. O conteúdo do ficheiro inclui pastas que representam cada cenário de implantação com um modelo e ficheiro de parâmetros. Antes de executá-los, modifique o ficheiro de parâmetros e especifique os valores necessários. 

O ficheiro de descarregamento contém os seguintes modelos para diferentes cenários:

- O modelo **de tabuleiro de marLM existente** permite o Monitor Azure para VMs se a máquina virtual já existir.
- O modelo **newVmOnboarding** cria uma máquina virtual e permite ao Azure Monitor para VMs monitorizá-lo.
- O modelo **de prancha de vídeoVmss existente** permite o Monitor Azure para VMs se o conjunto de escala de máquina virtual já existir.
- O modelo **newVmssOnboarding** cria conjuntos de escala de máquina virtual e permite que o Monitor Azure para VMs os monitorize.
- O modelo **ConfigureWorkspace** configura o seu espaço de trabalho Log Analytics para suportar o Azure Monitor para VMs, permitindo as soluções e a recolha de contadores de desempenho do sistema operativo Linux e Windows.

>[!NOTE]
>Se já estavam presentes conjuntos de escala de máquina virtual e a política de atualização está definida como **Manual,** o Azure Monitor para VMs não será ativado por defeito após a **execução** do modelo existente de Gestor de Recursos Azure. Tem que atualizar manualmente as ocorrências.

## <a name="deploy-templates"></a>Implementar modelos
Os modelos podem ser implementados usando [qualquer método de implementação para modelos de Gestor de Recursos,](../../azure-resource-manager/templates/deploy-powershell.md) incluindo os seguintes exemplos usando PowerShell e CLI.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Passos seguintes

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com o Azure Monitor para VMs.

- Para ver as dependências de aplicações descobertas, consulte [o Monitor do Azure para o Mapa de VMs](vminsights-maps.md).

- Para identificar estrangulamentos e utilização global com o desempenho do seu VM, consulte [Ver Azure VM Performance](vminsights-performance.md).
