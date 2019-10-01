---
title: Atualizar o script de implantação de modelo do Visual Studio para usar AZ PowerShell
description: Atualizar o script de implantação de modelo do Visual Studio de AzureRM para AZ PowerShell
author: cweining
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: 483b20f0003994f88ec35cc9f31a77cf571804f3
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695849"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Atualizando o script de implantação de modelo do Visual Studio para usar AZ PowerShell

O Visual Studio 16,4 dá suporte ao uso de AZ PowerShell no script de implantação de modelo. O Visual Studio não instala o AZ PowerShell ou atualiza automaticamente o script de implantação em seu projeto de grupo de recursos. Para usar o cmdlet AZ mais recente, você precisa fazer estas quatro coisas:
1. Desinstalar o AzureRM PowerShell
1. Instalar o AZ PowerShell
1. Atualização para o Visual Studio 16,4
1. Atualize o script de implantação em seu projeto.

## <a name="uninstall-azurerm-powershell"></a>Desinstalar o AzureRM PowerShell
Siga estas [instruções](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module) para desinstalar o AzureRM PowerShell.

## <a name="install-az-powershell"></a>Instalar o AZ PowerShell
Siga estas [instruções](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0) para instalar AZ PowerShell.

## <a name="update-visual-studio-to-164"></a>Atualizar o Visual Studio para 16,4
Atualize para o Visual Studio 16,4 quando ele estiver disponível. Durante a atualização, certifique-se de que o componente Azure PowerShell não esteja marcado. Como você instalou o AZ PowerShell por meio da galeria, não quer a versão AzureRM do PowerShell que é instalada com o Visual Studio.

Se você já atualizou para o 16,4 e o componente do Azure PowerShell foi verificado, você pode desinstalá-lo executando o Instalador do Visual Studio e desmarcando o componente do Azure PowerShell na carga de trabalho do Azure ou na página componentes individuais.

## <a name="update-the-deployment-script-in-your-project"></a>Atualizar o script de implantação em seu projeto
Substitua todas as ocorrências da cadeia de caracteres ' AzureRm ' por ' az ' em seu script de implantação. Você pode consultar as [revisões neste barpara ver](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) as alterações. Consulte esta [documentação](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0) para obter mais informações sobre como atualizar scripts para AZ PowerShell.


