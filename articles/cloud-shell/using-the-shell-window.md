---
title: Usando a janela Azure Cloud Shell ! Microsoft Docs
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "70860317"
---
# <a name="using-the-azure-cloud-shell-window"></a>Usando a janela Azure Cloud Shell

Este documento explica como usar a janela Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Troca entre ambientes Bash e PowerShell

Utilize o seletor de ambiente na barra de ferramentas Cloud Shell para trocar entre ambientes Bash e PowerShell.  
![Selecionar o ambiente](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Reiniciar a camada de nuvem
Clique no ícone de reinício na barra de ferramentas Cloud Shell para redefinir o estado da máquina.  
![Reiniciar a camada de nuvem](media/using-the-shell-window/restart.png)
> [!WARNING]
> Reiniciar a Cloud Shell irá reiniciar o estado da máquina e quaisquer ficheiros não persistidos pela sua partilha de ficheiros Azure serão perdidos.

## <a name="change-the-text-size"></a>Alterar o tamanho do texto
Clique no ícone de definições no lado superior esquerdo da janela e, em seguida, paire sobre a opção "Tamanho de texto" e selecione o tamanho de texto pretendido. A sua seleção será persistiu durante as sessões.
![Tamanho do texto](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Alterar a fonte
Clique no ícone de definições no lado superior esquerdo da janela e, em seguida, paire sobre a opção "Fonte" e selecione o tipo de letra pretendido.  A sua seleção será persistiu durante as sessões.
![Tipo de Letra](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Carregar e transferir ficheiros
Clique no ícone de ficheiros de upload/download na parte superior esquerda da janela e, em seguida, selecione upload ou download.  
![Carregar/transferir ficheiros](media/using-the-shell-window/uploaddownload.png)
* Para fazer o upload de ficheiros, utilize o pop-up para navegar no ficheiro no computador local, selecione o ficheiro pretendido e clique no botão "Abrir".  O ficheiro será enviado para o `/home/user` diretório.
* Para descarregar ficheiro, introduza o caminho de ficheiro totalmente qualificado na janela pop-up (isto é, basicamente um caminho sob o `/home/user` diretório que aparece por predefinição) e selecione o botão "Transferir".  
> [!NOTE] 
> Ficheiros e caminhos de ficheiros são sensíveis a casos na Cloud Shell. Verifique duas vezes o seu invólucro no seu caminho de arquivo.

## <a name="open-another-cloud-shell-window"></a>Abra outra janela cloud shell
O Cloud Shell permite várias sessões simultâneas em separado nos separadores do navegador, permitindo que cada sessão exista como um processo separado.
Se sair de uma sessão, certifique-se de que sai de cada janela de sessão, uma vez que cada processo funciona independentemente, embora a funcionar na mesma máquina.  
Clique no novo ícone de sessão aberto na parte superior esquerda da janela. Um novo separador abrirá com outra sessão ligada ao recipiente existente.
![Abrir nova sessão](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Editor da Cloud Shell
* Consulte a página [de editores da Azure Cloud Shell.](using-cloud-shell-editor.md)

## <a name="web-preview"></a>Pré-visualização web
Clique no ícone de pré-visualização da web no lado superior esquerdo da janela, selecione "Configurar", especifique a porta desejada para abrir.  Selecione "Open port" para abrir apenas a porta, ou "Abrir e navegar" para abrir a porta e pré-visualizar a porta num novo separador.  
![Pré-visualização web](media/using-the-shell-window/preview.png)  
<br>
![Porto de configuração](media/using-the-shell-window/preview-configure.png)  
Clique no ícone de pré-visualização da web no lado superior esquerdo da janela, selecione "Preview port..." para pré-visualizar uma porta aberta em um novo separador. Clique no ícone de pré-visualização da web no topo esquerdo da janela, selecione "Feche a porta..." para fechar a porta aberta.  
![Pré-visualização/porta de fecho](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar & maximizar a janela cloud shell
Clique no ícone de minimização no topo direito da janela para escondê-lo. Clique novamente no ícone Cloud Shell para desagrear.
Clique no ícone maximizado para definir a janela para a altura máxima. Para restaurar a janela para o tamanho anterior, clique em restaurar.  
![Minimizar ou maximizar a janela](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Copiar e colar
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Redimensionar a janela cloud shell
Clique e arraste a borda superior da barra de ferramentas para cima ou para baixo para redimensionar a janela Cloud Shell.

## <a name="scrolling-text-display"></a>Exibição de texto de deslocamento
Percorra com o rato ou o touchpad para mover texto terminal.

## <a name="exit-command"></a>Comando de saída
Correr `exit` termina a sessão ativa. Este comportamento ocorre por padrão após 20 minutos sem interação.

## <a name="next-steps"></a>Passos seguintes

[Bash em Cloud Shell Quickstart](quickstart.md) <br>
[PowerShell em Cloud Shell Quickstart](quickstart-powershell.md)
