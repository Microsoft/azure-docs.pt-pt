---
title: Módulos do PowerShell
titleSuffix: ML Studio (classic) - Azure
description: Use a PowerShell para criar e gerir espaços de trabalho do Azure Machine Learning Studio (clássicos), experiências, serviços web e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 04/25/2019
ms.openlocfilehash: 0d465c27621464d840555edb1a0f513fbc195e13
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148118"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Módulos PowerShell para Azure Machine Learning Studio (clássico)

Utilizando módulos PowerShell, pode gerir programaticamente os recursos e ativos do seu Estúdio (clássicos), tais como espaços de trabalho, conjuntos de dados e serviços web.

Pode interagir com os recursos do Studio (clássico) utilizando três módulos Powershell:

* [A Azure PowerShell Az](#az-rm) lançada em 2018, inclui todas as funcionalidades do AzureRM, embora com diferentes nomes cmdlet
* [AzureRM](#az-rm) lançado em 2016, substituído pela PowerShell Az
* Clássico da [Azure Machine Learning PowerShell](#classic) lançado em 2016

Embora estes módulos PowerShell tenham algumas semelhanças, cada um é projetado para cenários particulares. Este artigo descreve as diferenças entre os módulos PowerShell e ajuda-o a decidir quais escolher.  

Consulte a tabela de [suporte](#support-table) abaixo para ver quais os recursos suportados por cada módulo. 

## <a name="az-rm"></a>Azure PowerShell Az e AzureRM

A Z é agora o módulo PowerShell pretendido para interagir com o Azure e inclui toda a funcionalidade anterior do AzureRM. O AzureRM continuará a receber correções de bugs, mas não receberá novos cmdlets ou funcionalidades.  A Z e a AzureRM gerem ambas as soluções implementadas utilizando o modelo de implementação do Gestor de **Recursos Azure.** Estes recursos incluem espaços de trabalho Studio (clássico) e Studio (clássico) "New" web services. 

O clássico powerShell pode ser instalado ao lado de Az ou AzureRM para cobrir os tipos de recursos "novos" e "clássicos". No entanto, não é aconselhável instalar a Az e o AzureRM ao mesmo tempo. Para decidir entre a Az e a AzureRM, a Microsoft recomenda a Az para todas as futuras implementações.  Saiba mais sobre Az contra AzureRM e o caminho de migração em [introdução ao Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Para começar com a Az, siga as instruções de [instalação para o Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a>Clássico powerShell

O [módulo clássico PowerShell](https://aka.ms/amlps) (clássico) do Studio permite-lhe gerir os recursos implantados utilizando o modelo clássico de **implementação.** Estes recursos incluem ativos de utilizador studio (clássicos), serviços web "clássicos" e pontos finais de serviço web "clássicos".

No entanto, a Microsoft recomenda que utilize o modelo de implementação do Gestor de Recursos para todos os recursos futuros para simplificar a implementação e gestão de recursos. Se quiser saber mais sobre os modelos de implantação, consulte o artigo de [implantação clássico do Azure Resource Manager vs. Classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) Deployment.

Para começar com o clássico powerShell, descarregue o pacote de [lançamento](https://github.com/hning86/azuremlps/releases) do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear o DLL descarregado/desapertado e, em seguida, importá-lo para o seu ambiente PowerShell.

O clássico powerShell pode ser instalado ao lado de Az ou AzureRM para cobrir os tipos de recursos "novos" e "clássicos".

## <a name="support-table"></a>Tabela de suporte PowerShell


| | **Rio Az** |  **PowerShell clássico** |
| --- | --- | --- |
| Criar/excluir espaços de trabalho | [Modelos do Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gerir planos de compromisso do espaço de trabalho | [Novo AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gerir utilizadores do espaço de trabalho |  | [Adicionar AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gerir os serviços Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("novos" serviços web)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(serviços web "clássicos") |
| Gerir pontos finais/teclas do serviço web |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gerir conjuntos de dados de utilizadores/modelos treinados| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gerir experiências de utilizador |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gerir módulos personalizados | | [Módulo Novo AmlCustom](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Passos seguintes
Consulte a documentação completa deste módulo PowerShell:
* [PowerShell clássico](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
