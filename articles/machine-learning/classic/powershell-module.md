---
title: 'ML Studio (clássico): Módulos PowerShell - Azure'
description: Use o PowerShell para criar e gerir espaços de trabalho (clássicos) do Azure Machine Learning Studio (clássico), experiências, serviços web e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 01c0b6610968c8eaa493a63aa9004d8ea4c36ca2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530771"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Módulos PowerShell para Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../compare-azure-ml-to-studio-classic.md)  


Utilizando módulos PowerShell, pode gerir programáticamente os recursos e ativos do seu Estúdio (clássico), tais como espaços de trabalho, conjuntos de dados e serviços web.

Pode interagir com recursos do Studio (clássico) utilizando três módulos PowerShell:

* [Azure PowerShell Az](#az-rm) lançado em 2018, inclui toda a funcionalidade do AzureRM, embora com diferentes nomes de cmdlet
* [AzureRM](#az-rm) lançado em 2016, substituído pela PowerShell Az
* [Clássico da Azure Machine Learning PowerShell](#classic) lançado em 2016

Embora estes módulos PowerShell tenham algumas semelhanças, cada um é projetado para cenários específicos. Este artigo descreve as diferenças entre os módulos PowerShell e ajuda-o a decidir quais escolher.  

Consulte a [tabela de apoio](#support-table) abaixo para ver quais os recursos suportados por cada módulo. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az e AzureRM

A Az é agora o módulo PowerShell pretendido para interagir com o Azure e inclui toda a funcionalidade anterior do AzureRM. O AzureRM continuará a receber correções de bugs, mas não receberá novos cmdlets ou funcionalidades.  A Az e a AzureRM gerem ambas as soluções implementadas utilizando o modelo de implementação do **Gestor de Recursos Azure.** Estes recursos incluem espaços de trabalho studio (clássicos) e studio (clássico) serviços web "Novos". 

O clássico PowerShell pode ser instalado ao lado de Az ou AzureRM para cobrir os tipos de recursos "novos" e "clássicos". No entanto, não é aconselhável ter Az e AzureRM instalados ao mesmo tempo. Para decidir entre Az e AzureRM, a Microsoft recomenda a Az para todas as futuras implementações.  Saiba mais sobre a Az contra a AzureRM e o caminho de migração na [introdução ao Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Para começar com a Az, siga as [instruções de instalação do Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> Clássico da PowerShell

O [módulo clássico PowerShell](https://aka.ms/amlps) (clássico) permite-lhe gerir os recursos implantados utilizando o **modelo clássico de implementação.** Estes recursos incluem ativos de utilizador studio (clássicos), serviços web "clássicos" e pontos finais de serviço web "clássicos".

No entanto, a Microsoft recomenda que utilize o modelo de implementação do Gestor de Recursos para todos os recursos futuros para simplificar a implementação e gestão de recursos. Se quiser saber mais sobre os modelos de implementação, consulte o [Azure Resource Manager vs. artigo de implementação clássico.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)

Para começar com o clássico powerShell, descarregue o pacote de [lançamento](https://github.com/hning86/azuremlps/releases) do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear o DLL descarregado/desapertado e, em seguida, importá-lo para o seu ambiente PowerShell.

O clássico PowerShell pode ser instalado ao lado de Az ou AzureRM para cobrir os tipos de recursos "novos" e "clássicos".

## <a name="powershell-support-table"></a><a name="support-table"></a> Tabela de suporte PowerShell


| Tarefa | **Rio Az** |  **Clássico da PowerShell** |
| --- | --- | --- |
| Criar/apagar espaços de trabalho | [Modelos do Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gerir planos de compromisso do espaço de trabalho | [Plano de Novos Compromissos](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gerir utilizadores de espaço de trabalho |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gerir os serviços Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("novos" serviços web)| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("serviços web clássicos") |
| Gerir pontos finais/chaves do serviço web |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gerir conjuntos de dados dos utilizadores/modelos treinados| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gerir experiências de utilizador |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gerir módulos personalizados | | [Novo-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Passos seguintes
Consulte a documentação completa destes módulos PowerShell:
* [Clássico da PowerShell](https://aka.ms/amlps)
* [Az do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
