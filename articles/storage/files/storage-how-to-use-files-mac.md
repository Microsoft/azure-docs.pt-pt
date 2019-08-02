---
title: Montar uma partilha de ficheiros do Azure através de SMB com macOS | Microsoft Docs
description: Saiba como montar uma partilha de ficheiros do Azure através de SMB com macOS.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bd696ce5a314b0c849256311d0629b917036ea2
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699536"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montar uma partilha de ficheiros do Azure através de SMB com macOS
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. As partilhas de ficheiros do Azure podem ser montadas com o protocolo padrão da indústria, SMB 3, pelo macOS El Capitan 10.11+. Este artigo mostra duas formas diferentes de montar uma partilha de ficheiros do Azure em macOS com a IU do Finder e o Terminal.

> [!Note]  
> Antes de montar uma partilha de ficheiros do Azure através de SMB, recomendamos desativar a assinatura de pacotes SMB. Mantê-la ativada pode originar um mau desempenho ao aceder à partilha a partir de macOS. A ligação SMB será encriptada, pelo que a segurança da sua ligação não é afetada. No terminal, os comandos seguintes desativam a assinatura de pacotes SMB, conforme descrito neste [artigo de suporte da Apple relativo à desativação da assinatura de pacotes SMB](https://support.apple.com/HT205926):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Pré-requisitos para montar uma partilha de ficheiros do Azure em macOS
* **Nome da conta de armazenamento**: Para montar um compartilhamento de arquivos do Azure, será necessário o nome da conta de armazenamento.

* **Chave da conta de armazenamento**: Para montar um compartilhamento de arquivos do Azure, você precisará da chave de armazenamento primária (ou secundária). Atualmente, não são suportadas chaves SAS para a montagem.

* **Verifique se a porta 445 está aberta**: O SMB comunica através da porta TCP 445. No computador cliente (o Mac), verifique se a firewall não está a bloquear a porta TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montar uma partilha de ficheiros do Azure através do Finder
1. **Abrir localizador**: O Finder é aberto no macOS por padrão, mas você pode garantir que ele seja o aplicativo selecionado no momento clicando no "ícone de face do macOS" no Dock:  
    ![O ícone de cara do macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Selecione "conectar ao servidor" no menu "ir"** : Usando o caminho UNC dos pré-requisitos, converta a barra invertida dupla inicial (`\\`) para `smb://` e todas as barras invertidas`\`() para encaminhar barras (`/`). O link deve ser semelhante ao seguinte: ![A caixa de diálogo "conectar ao servidor"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Use o nome da conta de armazenamento e a chave da conta de armazenamento quando for solicitado um nome de usuário e uma senha**: Ao clicar em "conectar" na caixa de diálogo "conectar ao servidor", você será solicitado a fornecer o nome de usuário e a senha (isso será preenchido automaticamente com seu nome de usuário do macOS). Tem a opção de armazenar o nome/chave da conta de armazenamento no porta-chaves do macOS.

4. **Use o compartilhamento de arquivos do Azure conforme desejado**: Depois de substituir o nome do compartilhamento e a chave da conta de armazenamento em para o nome de usuário e a senha, o compartilhamento será montado. Pode utilizá-la tal como se fosse uma partilha pasta/ficheiro local normal, incluindo arrastar e largar ficheiros na partilha:

    ![Instantâneo de uma partilha de ficheiros do Azure montada](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montar uma partilha de ficheiros do Azure através do Terminal
1.  `<storage-account-name>`Substitua pelo nome da sua conta de armazenamento. Indique a Chave da Conta de Armazenamento como a palavra-passe, quando lhe for pedido. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Use o compartilhamento de arquivos do Azure conforme desejado**: O compartilhamento de arquivos do Azure será montado no ponto de montagem especificado pelo comando anterior.  

    ![Instantâneo da partilha de ficheiros do Azure montada](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Passos Seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.

* [Artigo de Suporte da Apple - Como efetuar a ligação à Partilha de ficheiros no Mac](https://support.apple.com/HT204445)
* [FAQ](../storage-files-faq.md)
* [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Resolução de Problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)    
