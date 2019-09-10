---
title: Usando a janela de Azure Cloud Shell | Microsoft Docs
description: Visão geral de como usar a janela de Azure Cloud Shell.
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
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860317"
---
# <a name="using-the-azure-cloud-shell-window"></a>Usando a janela de Azure Cloud Shell

Este documento explica como usar a janela de Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Alternar entre os ambientes bash e PowerShell

Use o seletor de ambiente na barra de ferramentas Cloud Shell para alternar entre os ambientes bash e PowerShell.  
![Selecionar ambiente](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Reiniciar o Cloud Shell
Clique no ícone de reinicialização na barra de ferramentas Cloud Shell para redefinir o estado da máquina.  
![Reiniciar o Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Reiniciar Cloud Shell redefinirá o estado do computador e todos os arquivos não persistentes pelo compartilhamento de arquivos do Azure serão perdidos.

## <a name="change-the-text-size"></a>Alterar o tamanho do texto
Clique no ícone de configurações na parte superior esquerda da janela, em seguida, passe o mouse sobre a opção "tamanho do texto" e selecione o tamanho desejado do texto. Sua seleção será persistida entre as sessões.
![Tamanho do texto](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Alterar a fonte
Clique no ícone de configurações na parte superior esquerda da janela, em seguida, passe o mouse sobre a opção "fonte" e selecione a fonte desejada.  Sua seleção será persistida entre as sessões.
![La](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Carregar e transferir os ficheiros
Clique no ícone carregar/baixar arquivos na parte superior esquerda da janela e, em seguida, selecione carregar ou baixar.  
![Carregar/baixar arquivos](media/using-the-shell-window/uploaddownload.png)
* Para carregar arquivos, use o pop-up para navegar até o arquivo no computador local, selecione o arquivo desejado e clique no botão "abrir".  O arquivo será carregado no `/home/user` diretório.
* Para baixar o arquivo, insira o caminho do arquivo totalmente qualificado na janela pop-up (ou seja, basicamente um caminho sob `/home/user` o diretório que aparece por padrão) e selecione o botão "baixar".  
> [!NOTE] 
> Arquivos e caminhos de arquivo diferenciam maiúsculas de minúsculas no Cloud Shell. Verifique novamente sua capitalização no caminho do arquivo.

## <a name="open-another-cloud-shell-window"></a>Abrir outra janela de Cloud Shell
O Cloud Shell permite várias sessões simultâneas nas guias do navegador, permitindo que cada sessão exista como um processo separado.
Se você sair de uma sessão, não deixe de sair de cada janela de sessão, pois cada processo é executado de forma independente, embora seja executado no mesmo computador.  
Clique no ícone abrir nova sessão na parte superior esquerda da janela. Uma nova guia será aberta com outra sessão conectada ao contêiner existente.
![Abrir nova sessão](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Editor de Cloud Shell
* Consulte a página [usando o editor de Azure cloud Shell](using-cloud-shell-editor.md) .

## <a name="web-preview"></a>Visualização da Web
Clique no ícone de visualização da Web na parte superior esquerda da janela, selecione "configurar", especifique a porta desejada a ser aberta.  Selecione "abrir porta" para abrir apenas a porta ou "abrir e procurar" para abrir a porta e visualizar a porta em uma nova guia.  
![Visualização da Web](media/using-the-shell-window/preview.png)  
<br>
![Configurar porta](media/using-the-shell-window/preview-configure.png)  
Clique no ícone de visualização da Web na parte superior esquerda da janela, selecione "porta de visualização..." para visualizar uma porta aberta em uma nova guia. Clique no ícone de visualização da Web na parte superior esquerda da janela, selecione "fechar porta..." para fechar a porta aberta.  
![Visualizar/fechar porta](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar & maximizar Cloud Shell janela
Clique no ícone minimizar na parte superior direita da janela para ocultá-la. Clique no ícone de Cloud Shell novamente para Reexibir.
Clique no ícone maximizar para definir a janela como altura máxima. Para restaurar a janela para o tamanho anterior, clique em restaurar.  
![Minimizar ou maximizar a janela](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Copiar e colar
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Redimensionar Cloud Shell janela
Clique e arraste a borda superior da barra de ferramentas para cima ou para baixo para redimensionar a janela de Cloud Shell.

## <a name="scrolling-text-display"></a>Exibição de texto de rolagem
Role com o mouse ou o Touchpad para mover o texto do terminal.

## <a name="exit-command"></a>Comando exit
A `exit` execução encerra a sessão ativa. Esse comportamento ocorre por padrão após 20 minutos sem interação.

## <a name="next-steps"></a>Passos seguintes

[Bash no guia de início rápido Cloud Shell](quickstart.md) <br>
[PowerShell no guia de início rápido Cloud Shell](quickstart-powershell.md)
