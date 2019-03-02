---
title: Usando a janela do Azure Cloud Shell | Documentos da Microsoft
description: Descrição geral de como usar a janela do Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: a02642540e6eb39f35b9cc0d38d187a7afa36b7a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243453"
---
# <a name="using-the-azure-cloud-shell-window"></a>Usando a janela do Azure Cloud Shell

Este documento explica como usar a janela do Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Alternar entre ambientes de Bash e o PowerShell
![](media/using-the-shell-window/env-selector.png)

Utilize o Seletor de ambiente na barra de ferramentas da Cloud Shell para mudar entre ambientes de Bash e o PowerShell.

## <a name="restart-cloud-shell"></a>Reiniciar o Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Reiniciar o Cloud Shell irá repor o estado da máquina e todos os ficheiros não mantido pelo seu Azure partilha de ficheiros serão perdida.

* Clique no ícone de reinício da barra de ferramentas do Cloud Shell para repor o estado da máquina.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar & Maximizar a janela do Cloud Shell
![](media/using-the-shell-window/minmax.png)
* Clique no ícone de minimizar no canto superior direito da janela para ocultá-lo. Clique no ícone do Cloud Shell novamente para mostrar.
* Clique no ícone de maximizar para definir a janela para a altura máxima. Para restaurar a janela para tamanho anterior, clique em restaurar.

## <a name="concurrent-sessions"></a>Sessões simultâneas
Cloud Shell permite várias sessões simultâneas em separadores de browser, permitindo que cada sessão de existir como um processo separado do Bash.
Se sair de uma sessão, certifique-se de que sair de cada janela de sessão como cada processo é executado independentemente embora eles são executados na mesma máquina.

## <a name="copy-and-paste"></a>Copiar e colar
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Redimensionar a janela do Cloud Shell
* Clique e arraste o limite superior da barra de ferramentas de cópia de segurança ou para baixo para redimensionar a janela do Cloud Shell.

## <a name="scrolling-text-display"></a>Exibição de texto de rolagem
* Desloque-se com o rato ou touchpad para mover o texto de terminal.

## <a name="changing-the-text-size"></a>Alterar o tamanho do texto
![](media/using-the-shell-window/text-size.png)
* Clique no ícone de definições no canto superior esquerdo da janela, em seguida, coloque o cursor sobre a opção de "Tamanho do texto" e selecione o tamanho do texto desejado. A seleção continuarão a ter a seguinte entre sessões.

## <a name="exit-command"></a>Comando de saída
Executar `exit` termina a sessão ativa. Este comportamento ocorre por predefinição, passados 20 minutos sem a interação.

## <a name="next-steps"></a>Passos Seguintes

[Guia de introdução do Cloud Shell do bash](quickstart.md)
[PowerShell no guia de introdução do Cloud Shell](quickstart-powershell.md)