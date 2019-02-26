---
title: Módulos do PowerShell para Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Utilize o PowerShell para criar e gerir o Azure Machine Learning Studio áreas de trabalho, experiências, serviços da web e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=haining, previous-author=hning86
ms.date: 01/25/2019
ms.openlocfilehash: d25c9a4a828a8b536a5b54ecb3894ff9e5818d85
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819634"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Módulos do PowerShell para o Azure Machine Learning Studio

Utilizar módulos do PowerShell, pode gerir programaticamente os recursos do Studio e a recursos como áreas de trabalho, os conjuntos de dados e serviços da web.

Pode interagir com recursos do Studio com três módulos do Powershell:

* [O Azure PowerShell Az](#az-rm) lançado em 2018, inclui todas as funcionalidades do AzureRM, embora com nomes de cmdlet diferentes
* [AzureRM](#az-rm) lançado em 2016
* [Azure clássico de PowerShell do Machine Learning](#classic) lançado em 2016

Embora esses módulos tem algumas semelhanças, cada uma foi projetado para cenários específicos. Este artigo descreve as diferenças entre os módulos do PowerShell e obter ajuda para que decidir quais escolher.

## <a name="choosing-modules"></a> Escolher módulos

Escolher entre os módulos do PowerShell disponíveis depende do tipo de recursos que está gerenciando.

Verifique os [tabela de suporte](#support-table) abaixo para ver quais os recursos que são suportados por cada módulo. Uma vez que o PowerShell clássico pode ser instalado em paralelo com Az ou AzureRM, pode instalar dois módulos e abordar todos os tipos de recursos (clássicos com Az ou Clássicos com o AzureRM)

No entanto, não é recomendado ter Az e AzureRM instalado ao mesmo tempo. Para decidir entre Az e AzureRM, a Microsoft recomenda Az para todas as implementações futuras. Use AzureRm apenas se houver circunstâncias especiais em seu ambiente que precisam dela.

Para saber mais sobre as diferenças entre Az e AzureRM, bem como o nosso caminho de migração fornecido, consulte nosso [introdução para o Azure PowerShell Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

## <a name="az-rm"></a> O Azure PowerShell Az e AzureRM

AZ e AzureRM ambos gerem soluções implementadas com o **do Azure Resource Manager** modelo de implementação. Esses recursos incluem áreas de trabalho do Studio e o Studio novos serviços web. Para gerir os recursos implementados com o modelo de implementação clássica, deve utilizar o módulo do PowerShell clássico. Se gostaria de saber mais sobre os modelos de implementação, consulte a [vs. de implementação clássica do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) artigo.

AZ agora é o módulo pretendido do PowerShell para interagir com o Azure e inclui toda a funcionalidade anterior do AzureRM. AzureRM vai continuar a receber correções de erros, mas irá receber sem novos cmdlets ou funcionalidades. Embora haja um caminho de atualização do AzureRM, se tiver problemas com Az ao trabalhar com o Studio, comunique o problema e voltar a usar o AzureRM.

Para começar a utilizar com Az, siga os [instruções de instalação para o Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell clássico

O Studio [módulo do PowerShell clássico](https://aka.ms/amlps) permite-lhe gerir os recursos implementados com o **modelo de implementação clássica**. Esses recursos incluem recursos de utilizador do Studio, serviços web clássicos e pontos finais de serviço web clássico.

No entanto, a Microsoft recomenda que utilize o modelo de implementação do Resource Manager para todos os novos recursos para simplificar a implementação e gestão de recursos. Se gostaria de saber mais sobre os modelos de implementação, consulte a [vs. de implementação clássica do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) artigo.

Para começar a utilizar com o PowerShell clássico, transfira o [pacote da versão](https://github.com/hning86/azuremlps/releases) do GitHub e siga o [instruções de instalação de](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear a DLL transferida/descompactada e, em seguida, importe-o para o ambiente do PowerShell.

## <a name="support-table"></a> Tabela de suporte do PowerShell

 **Áreas de trabalho do Studio** | **Az** |  **AzureRM** | **PowerShell clássico** |
| --- | --- | --- | --- | --- |
| Criar/eliminar as áreas de trabalho | [Modelos do Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Modelos do Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gerir utilizadores da área de trabalho |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gerir planos de alocação | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Serviços Web** | **Az** | **AzureRM** | **PowerShell clássico** |
| Gerir os serviços Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (Novos serviços web) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (Novos serviços web) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (serviços web clássicos) |
| Gerir pontos finais/chaves |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (Novos serviços web) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (Novos serviços web) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (serviços web clássicos) |
|||
| **Recursos de utilizador** | **Az** | **AzureRM** | **PowerShell clássico** |
| Gerir modelos treinados/conjuntos de dados |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gerir experimentações |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gerir módulos personalizados |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Passos Seguintes
Siga estas ligações para documentação completa para os módulos do PowerShell:
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell clássico](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
