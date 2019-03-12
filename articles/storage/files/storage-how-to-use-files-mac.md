---
title: Montar uma partilha de ficheiros do Azure através de SMB com macOS | Microsoft Docs
description: Saiba como montar uma partilha de ficheiros do Azure através de SMB com macOS.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 198e9102561af9ebec7a337a7b5e01f59453c8be
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760738"
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
* **Nome da conta de armazenamento**: Montar uma partilha de ficheiros do Azure, terá o nome da conta de armazenamento.

* **Chave da conta de armazenamento**: Para montar uma partilha de ficheiros do Azure, terá da chave de armazenamento primária (ou secundário). Atualmente, não são suportadas chaves SAS para a montagem.

* **Certifique-se de que a porta 445 está aberta**: O SMB comunica através da porta TCP 445. No computador cliente (o Mac), verifique se a firewall não está a bloquear a porta TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montar uma partilha de ficheiros do Azure através do Finder
1. **Abra o Finder**: Finder é aberto no macOS por predefinição, mas pode certificar-se é a aplicação selecionada atualmente ao clicar o "cara do macOS ícone" no dock:  
    ![O ícone de cara do macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Selecione "Ligar ao servidor" no Menu "Ir"**: Usando o caminho UNC dos pré-requisitos, converta a barra invertida duplo de início (`\\`) para `smb://` e todas as outras barras invertidas (`\`) para barras (`/`). A ligação deve ter um aspeto semelhante ao seguinte: ![A caixa de diálogo "Connect to Server"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Utilize o nome da conta de armazenamento e a chave de conta de armazenamento quando lhe for pedido um nome de utilizador e palavra-passe**: Quando clicar em "Ligar" na caixa de diálogo "Connect to Server", será solicitado para o nome de utilizador e palavra-passe (será preenchido automaticamente com o seu nome de utilizador de macOS). Tem a opção de armazenar o nome/chave da conta de armazenamento no porta-chaves do macOS.

4. **Utilize a partilha de ficheiros do Azure conforme pretendido**: Depois de substituir a partilha de nome e o armazenamento de chave da conta de nome de utilizador e palavra-passe, será possível montar a partilha. Pode utilizá-la tal como se fosse uma partilha pasta/ficheiro local normal, incluindo arrastar e largar ficheiros na partilha:

    ![Instantâneo de uma partilha de ficheiros do Azure montada](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montar uma partilha de ficheiros do Azure através do Terminal
1. Substitua `<storage-account-name>` com o nome da conta de armazenamento. Indique a Chave da Conta de Armazenamento como a palavra-passe, quando lhe for pedido. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Utilize a partilha de ficheiros do Azure conforme pretendido**: A partilha de ficheiros do Azure é montada no ponto de montagem especificado pelo comando anterior.  

    ![Instantâneo da partilha de ficheiros do Azure montada](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Passos Seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.

* [Artigo de Suporte da Apple - Como efetuar a ligação à Partilha de ficheiros no Mac](https://support.apple.com/HT204445)
* [FAQ](../storage-files-faq.md)
* [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Resolução de Problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)    
