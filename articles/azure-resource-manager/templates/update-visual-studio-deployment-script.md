---
title: Atualizar o script de implementação do modelo do Estúdio Visual para usar Az PowerShell
description: Atualize o script de implementação do modelo do Estúdio Visual de AzureRM para Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "76963874"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Atualizar script de implementação do modelo do estúdio visual para usar o módulo Az PowerShell

O Visual Studio 16.4 suporta a utilização do módulo Az PowerShell no script de implementação do modelo. No entanto, o Visual Studio não instala automaticamente este módulo. Para utilizar o módulo Az, é necessário dar quatro passos:

1. [Desinstale o módulo Azurerm](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Instalar módulo Az](/powershell/azure/install-az-ps)
1. Atualizar Estúdio Visual para 16.4
1. Atualize o script de implementação no seu projeto.

## <a name="update-visual-studio-to-164"></a>Atualizar Estúdio Visual para 16.4

Atualize a sua instalação do Visual Studio para a versão 16.4 ou posterior. Durante a atualização, certifique-se de que o componente Azure PowerShell não é verificado. Uma vez que instalou o módulo Az através da galeria, não pretende reinstalar o módulo AzureRM.

Se já tiver atualizado para 16.4 e o componente Azure PowerShell tiver sido verificado, pode desinstalá-lo executando o Instalador do Estúdio Visual. Não selecione o componente Azure PowerShell na Carga de Trabalho Azure ou na página de componentes individuais.

## <a name="update-the-deployment-script-in-your-project"></a>Atualize o script de implementação no seu projeto

Substitua todas as ocorrências da cadeia 'AzureRm' por 'Az' no seu script de implantação. Pode consultar as revisões neste [ponto para](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) ver as alterações. Para obter mais informações sobre a atualização dos scripts para o módulo Az, consulte [a Migrate Azure PowerShell de AzureRM a Az](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Passos seguintes

Para aprender a utilizar o projeto Visual Studio, consulte [criar e implementar projetos de grupo de recursos Azure através do Visual Studio.](create-visual-studio-deployment-project.md)
