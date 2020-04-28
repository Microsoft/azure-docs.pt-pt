---
title: Usando a janela Azure Cloud Shell [ Microsoft Docs
description: Visão geral de como usar a janela Azure Cloud Shell.
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
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70860317"
---
# <a name="using-the-azure-cloud-shell-window"></a>Usando a janela Azure Cloud Shell

Este documento explica como usar a janela Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Troca entre ambientes Bash e PowerShell

Utilize o seletor de ambiente na barra de ferramentas Cloud Shell para trocar entre ambientes Bash e PowerShell.  
![Selecionar o ambiente](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Reiniciar a Nuvem Shell
Clique no ícone de reinício na barra de ferramentas Cloud Shell para redefinir a formação da máquina.  
![Reiniciar a Nuvem Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Reiniciar a Cloud Shell reiniciará o estado da máquina e quaisquer ficheiros não persistidos pela sua quota de ficheiroS Azure serão perdidos.

## <a name="change-the-text-size"></a>Alterar o tamanho do texto
Clique no ícone de definições na parte superior esquerda da janela, em seguida, paire sobre a opção "Tamanho de Texto" e selecione o tamanho de texto desejado. A sua seleção será persistiu em sessões.
![Tamanho do texto](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Alterar a fonte
Clique no ícone de definições na parte superior esquerda da janela, em seguida, paire sobre a opção "Fonte" e selecione o seu tipo de letra desejado.  A sua seleção será persistiu em sessões.
![Tipo de Letra](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Carregar e transferir os ficheiros
Clique no ícone de ficheiros de upload/download na parte superior esquerda da janela e, em seguida, selecione upload ou download.  
![Ficheiros de upload/download](media/using-the-shell-window/uploaddownload.png)
* Para fazer upload de ficheiros, utilize o pop-up para navegar no ficheiro no seu computador local, selecione o ficheiro pretendido e clique no botão "Abrir".  O ficheiro será enviado `/home/user` para o diretório.
* Para descarregar ficheiros, introduza o caminho de ficheiro totalmente qualificado na janela pop-up (isto é, basicamente um caminho sob o `/home/user` diretório que aparece por padrão) e selecione o botão "Descarregar".  
> [!NOTE] 
> Ficheiros e ficheiros são sensíveis a casos na Cloud Shell. Verifique duas vezes o seu invólucro no seu caminho de arquivo.

## <a name="open-another-cloud-shell-window"></a>Abra outra janela cloud shell
Cloud Shell permite múltiplas sessões simultâneas através de separadores do navegador, permitindo que cada sessão exista como um processo separado.
Se sair de uma sessão, certifique-se de sair de cada janela da sessão, uma vez que cada processo funciona de forma independente, embora corra na mesma máquina.  
Clique no ícone de sessão nova aberta na parte superior à esquerda da janela. Um novo separador abrirá com outra sessão ligada ao recipiente existente.
![Abrir nova sessão](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Editor da Cloud Shell
* Consulte a página de [editor da Azure Cloud Shell.](using-cloud-shell-editor.md)

## <a name="web-preview"></a>Pré-visualização da Web
Clique no ícone de pré-visualização da web na parte superior à esquerda da janela, selecione "Configure", especifique a porta desejada para abrir.  Selecione "Abrir a porta" para abrir apenas a porta, ou "Abrir e navegar" para abrir a porta e pré-visualizar a porta num novo separador.  
![Pré-visualização da Web](media/using-the-shell-window/preview.png)  
<br>
![Configure porta](media/using-the-shell-window/preview-configure.png)  
Clique no ícone de pré-visualização da web na parte superior à esquerda da janela, selecione "Preview porta..." para pré-visualizar uma porta aberta num novo separador. Clique no ícone de pré-visualização da web na parte superior à esquerda da janela, selecione "Feche a porta..." para fechar o porto aberto.  
![Pré-visualização/porta de fecho](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar & maximizar a janela Cloud Shell
Clique no ícone minimizar na parte superior direita da janela para escondê-lo. Clique novamente no ícone Cloud Shell para desesconder.
Clique no ícone maximizado para definir a janela até à altura máxima. Para restaurar a janela para o tamanho anterior, clique em restaurar.  
![Minimizar ou maximizar a janela](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Copiar e colar
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Janela de casca de nuvem de redimensionar
Clique e arraste a borda superior da barra de ferramentas para cima ou para baixo para redimensionar a janela Cloud Shell.

## <a name="scrolling-text-display"></a>Exibição de texto de scrolling
Percorra com o rato ou touchpad para mover o texto do terminal.

## <a name="exit-command"></a>Comando de saída
Correr `exit` termina a sessão ativa. Este comportamento ocorre por defeito após 20 minutos sem interação.

## <a name="next-steps"></a>Passos seguintes

[Bash em Cloud Shell Quickstart](quickstart.md) <br>
[PowerShell em Cloud Shell Quickstart](quickstart-powershell.md)
