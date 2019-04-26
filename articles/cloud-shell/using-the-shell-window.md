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
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 2511f2c8fb706e232cde9ee4c02c7f8114bd3a2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60200705"
---
# <a name="using-the-azure-cloud-shell-window"></a>Usando a janela do Azure Cloud Shell

Este documento explica como usar a janela do Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Alternar entre ambientes de Bash e o PowerShell

Utilize o Seletor de ambiente na barra de ferramentas da Cloud Shell para mudar entre ambientes de Bash e o PowerShell.  
![Selecionar ambiente](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Reiniciar o Cloud Shell
Clique no ícone de reinício da barra de ferramentas do Cloud Shell para repor o estado da máquina.  
![Reiniciar o Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Reiniciar o Cloud Shell irá repor o estado da máquina e todos os ficheiros não mantido pelo seu Azure partilha de ficheiros serão perdida.

## <a name="change-the-text-size"></a>Alterar o tamanho do texto
Clique no ícone de definições no canto superior esquerdo da janela, em seguida, coloque o cursor sobre a opção de "Tamanho do texto" e selecione o tamanho do texto desejado. A seleção continuarão a ter a seguinte entre sessões.
![Tamanho do texto](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Alterar o tipo de letra
Clique no ícone de definições no canto superior esquerdo da janela, em seguida, coloque o cursor sobre a opção de "Tipo de letra" e selecione o tipo de letra pretendido.  A seleção continuarão a ter a seguinte entre sessões.
![Font](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Carregar e transferir os ficheiros
Clique no ícone de ficheiros de carregamento/transferência no canto superior esquerdo da janela, em seguida, selecione o carregamento ou transferência.  
![Carregar/transferir ficheiros](media/using-the-shell-window/uploaddownload.png)
* Para carregar ficheiros, utilize o pop-up para procurar o ficheiro no seu computador local, selecione o arquivo desejado e clique no botão "Abrir".  O arquivo será carregado para o `/home/user` diretório.
* Para transferir ficheiros, introduza o caminho de ficheiro completamente qualificado para a janela de pop-up e selecione o botão "Transferir".  
> [!NOTE] 
> Ficheiros e caminhos de ficheiro são maiúsculas de minúsculas no Cloud Shell. Volte a verificar as maiúsculas e minúsculas no caminho do ficheiro.

## <a name="open-another-cloud-shell-window"></a>Abrir outra janela do Cloud Shell
Cloud Shell permite várias sessões simultâneas em separadores de browser, permitindo que cada sessão de existir como um processo separado.
Se sair de uma sessão, certifique-se de que sair de cada janela de sessão como cada processo é executado independentemente embora eles são executados na mesma máquina.  
Clique no ícone abrir sessão de novo no canto superior esquerdo da janela. Uma nova guia será aberto com outra sessão ligado para o contentor existente.
![Abrir nova sessão](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Editor do cloud Shell
* Consulte a [com o editor do Azure Cloud Shell](using-cloud-shell-editor.md) página.

## <a name="web-preview"></a>Pré-visualização Web
Clique no ícone de pré-visualização da web no canto superior esquerdo da janela, selecione "Configurar", especifique a porta pretendida para abrir.  Selecione qualquer um dos "porta aberta" apenas para abrir a porta, ou "abrir e procure" para abrir a porta e a porta num novo separador de pré-visualização.  
![Pré-visualização Web](media/using-the-shell-window/preview.png)  
<br>
![Configurar a porta](media/using-the-shell-window/preview-configure.png)  
Clique no ícone de pré-visualização da web no canto superior esquerdo da janela, selecione "Pré-visualizar porta..." Para pré-visualizar uma porta aberta num novo separador. Clique no ícone de pré-visualização da web no canto superior esquerdo da janela, selecione "Fechar porta..." para fechar a porta aberta.  
![Pré-visualização/fechar a porta](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar & Maximizar a janela do Cloud Shell
Clique no ícone de minimizar no canto superior direito da janela para ocultá-lo. Clique no ícone do Cloud Shell novamente para mostrar.
Clique no ícone de maximizar para definir a janela para a altura máxima. Para restaurar a janela para tamanho anterior, clique em restaurar.  
![Minimizar ou maximizar a janela](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Copiar e colar
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Redimensionar a janela do Cloud Shell
Clique e arraste o limite superior da barra de ferramentas de cópia de segurança ou para baixo para redimensionar a janela do Cloud Shell.

## <a name="scrolling-text-display"></a>Exibição de texto de rolagem
Desloque-se com o rato ou touchpad para mover o texto de terminal.

## <a name="exit-command"></a>Comando de saída
Executar `exit` termina a sessão ativa. Este comportamento ocorre por predefinição, passados 20 minutos sem a interação.

## <a name="next-steps"></a>Passos Seguintes

[Guia de introdução do Cloud Shell de bash](quickstart.md) <br>
[PowerShell no guia de introdução do Cloud Shell](quickstart-powershell.md)