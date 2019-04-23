---
title: Módulos do PowerShell para Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Utilize o PowerShell para criar e gerir o Azure Machine Learning Studio áreas de trabalho, experiências, serviços da web e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: e3c2587fceed265c9768b6ea6f2ecf3b9a8b7b1a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011354"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Módulos do PowerShell para o Azure Machine Learning Studio

Utilizar módulos do PowerShell, pode gerir programaticamente os recursos do Studio e a recursos como áreas de trabalho, os conjuntos de dados e serviços da web.

Pode interagir com recursos do Studio com três módulos do Powershell:

* [O Azure PowerShell Az](#az-rm) lançado em 2018, inclui todas as funcionalidades do AzureRM, embora com nomes de cmdlet diferentes
* [AzureRM](#az-rm) lançado em 2016, substituído pelo PowerShell Az
* [Azure clássico de PowerShell do Machine Learning](#classic) lançado em 2016

Embora estes módulos do PowerShell têm algumas semelhanças, cada uma foi projetado para cenários específicos. Este artigo descreve as diferenças entre os módulos do PowerShell e obter ajuda para que decidir quais escolher.  

Verifique os [tabela de suporte](#support-table) abaixo para ver quais os recursos que são suportados por cada módulo. 

## <a name="az-rm"></a> O Azure PowerShell Az e AzureRM

AZ agora é o módulo pretendido do PowerShell para interagir com o Azure e inclui toda a funcionalidade anterior do AzureRM. AzureRM vai continuar a receber correções de erros, mas irá receber sem novos cmdlets ou funcionalidades.  AZ e AzureRM ambos gerem soluções implementadas com o **do Azure Resource Manager** modelo de implementação. Esses recursos incluem áreas de trabalho do Studio e o Studio "New" web services. 

PowerShell clássico pode ser instalado juntamente com Az ou AzureRM para abordar os dois tipos de recurso "novo" e "clássico". No entanto, não é recomendado ter Az e AzureRM instalado ao mesmo tempo. Para decidir entre Az e AzureRM, a Microsoft recomenda Az para todas as implementações futuras.  Saiba mais sobre Az versus AzureRM e o caminho de migração no [introdução ao Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Para começar a utilizar com Az, siga os [instruções de instalação para o Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell clássico

O Studio [módulo do PowerShell clássico](https://aka.ms/amlps) permite-lhe gerir os recursos implementados com o **modelo de implementação clássica**. Esses recursos incluem recursos de utilizador do Studio, serviços web "clássico" e pontos finais de serviço da web "clássico".

No entanto, a Microsoft recomenda que utilize o modelo de implementação do Resource Manager para todos os recursos futuros para simplificar a implementação e gestão de recursos. Se gostaria de saber mais sobre os modelos de implementação, consulte a [vs. de implementação clássica do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) artigo.

Para começar a utilizar com o PowerShell clássico, transfira o [pacote da versão](https://github.com/hning86/azuremlps/releases) do GitHub e siga o [instruções de instalação de](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear a DLL transferida/descompactada e, em seguida, importe-o para o ambiente do PowerShell.

PowerShell clássico pode ser instalado juntamente com Az ou AzureRM para abordar os dois tipos de recurso "novo" e "clássico".

## <a name="support-table"></a> Tabela de suporte do PowerShell


| | **Az** |  **PowerShell clássico** |
| --- | --- | --- |
| Criar/eliminar as áreas de trabalho | [Modelos do Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gerir planos de alocação de área de trabalho | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gerir utilizadores da área de trabalho |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gerir os serviços Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("new" serviços da web)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(serviços web "clássico") |
| Gerir pontos finais de serviço web, as chaves |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gerir modelos treinados/conjuntos de dados do utilizador| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gerir experiências de utilizador |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gerir módulos personalizados | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Passos Seguintes
Consulte a documentação completa dessas módulo do PowerShell:
* [PowerShell clássico](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
