---
title: Atualizar o script de implementação do modelo do Estúdio Visual para usar Az PowerShell
description: Atualize o script de implementação do modelo do Estúdio Visual de AzureRM para Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76963874"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Atualizar script de implementação de modelo de estúdio visual para usar módulo Az PowerShell

O Visual Studio 16.4 suporta a utilização do módulo Az PowerShell no script de implementação do modelo. No entanto, o Visual Studio não instala automaticamente esse módulo. Para utilizar o módulo Az, é necessário dar quatro passos:

1. [Desinstalar módulo AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Instalar módulo Az](/powershell/azure/install-az-ps)
1. Atualizar o Estúdio Visual para 16.4
1. Atualize o script de implementação no seu projeto.

## <a name="update-visual-studio-to-164"></a>Atualizar o Estúdio Visual para 16.4

Atualize a sua instalação do Visual Studio para a versão 16.4 ou posterior. Durante a atualização, certifique-se de que o componente Azure PowerShell não é verificado. Uma vez que instalou o módulo Az através da galeria, não pretende reinstalar o módulo AzureRM.

Se já atualizou para 16.4 e o componente Azure PowerShell foi verificado, pode desinstalá-lo executando o Instalador de Estúdio Visual. Não selecione o componente Azure PowerShell na carga de trabalho azure ou na página de componentes individuais.

## <a name="update-the-deployment-script-in-your-project"></a>Atualize o script de implementação no seu projeto

Substitua todas as ocorrências da corda 'AzureRm' por 'Az' no seu script de implantação. Pode consultar revisões nesta [essência](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) para ver as alterações. Para obter mais informações sobre a atualização de scripts para o módulo Az, consulte [Migrate Azure PowerShell de AzureRM para Az](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre a utilização do projeto Visual Studio, consulte criar e implementar projetos do grupo de [recursos Azure através do Visual Studio.](create-visual-studio-deployment-project.md)
