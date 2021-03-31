---
title: Montar uma partilha de ficheiros do Azure através de SMB com macOS | Microsoft Docs
description: Saiba como montar uma partilha de ficheiros Azure sobre sMB com o macOS usando Finder ou Terminal. Ficheiros do Azure é o sistema de ficheiros na cloud fácil de utilizar da Microsoft.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326070"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montar uma partilha de ficheiros do Azure através de SMB com macOS
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. As ações de ficheiros Azure podem ser montadas com o protocolo SMB 3 padrão da indústria pelo macOS High Sierra 10.13+. Este artigo mostra duas formas diferentes de montar uma partilha de ficheiros do Azure em macOS com a IU do Finder e o Terminal.

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Pré-requisitos para montar uma partilha de ficheiros do Azure em macOS
* **Nome da conta de armazenamento**: Para montar uma partilha de ficheiroS Azure, necessitará do nome da conta de armazenamento.

* **Tecla de conta de armazenamento**: Para montar uma partilha de ficheiros Azure, necessitará da chave de armazenamento primária (ou secundária). Atualmente, não são suportadas chaves SAS para a montagem.

* **Confirme que a porta 445 está aberta**: o SMB comunica através da porta TCP 445. No computador cliente (o Mac), verifique se a firewall não está a bloquear a porta TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montar uma partilha de ficheiros do Azure através do Finder
1. **Abra o Finder**: o Finder é aberto no macOS por predefinição, mas pode confirmar que é a aplicação que está selecionada ao clicar no “ícone de cara do macOS” no dock:  
    ![O ícone de cara do macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Selecione "Connect to Server" a partir do menu "Go":** Utilizando o caminho UNC a partir dos pré-requisitos, converta o backslash duplo inicial `\\` () para `smb://` e todas as outras costas ( `\` ) para os cortes para a frente `/` (). A ligação deverá ter um aspeto semelhante ao seguinte: ![caixa de diálogo "Connect to Server"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Utilize o nome e a chave da conta de armazenamento quando lhe for pedido o nome de utilizador e a palavra-passe**: quando clica em "Connect" (Ligar) na caixa de diálogo "Connect to Server" (Ligar ao Servidor), é-lhe pedido o nome de utilizador e a palavra-passe (que são preenchidos automaticamente com o seu nome de utilizador de macOS). Tem a opção de armazenar o nome/chave da conta de armazenamento no porta-chaves do macOS.

4. **Utilize a partilha de ficheiros Azure conforme desejado**: Depois de substituir a chave do nome de partilha e da conta de armazenamento para o nome de utilizador e senha, a partilha será montada. Pode utilizá-la tal como se fosse uma partilha pasta/ficheiro local normal, incluindo arrastar e largar ficheiros na partilha:

    ![Instantâneo de uma partilha de ficheiros do Azure montada](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montar uma partilha de ficheiros do Azure através do Terminal
1. `<storage-account-name>` `<storage-account-key>` Substitua, e pelos `<share-name>` valores adequados para o seu ambiente.

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **Utilize a partilha de ficheiros Azure conforme desejado**: A partilha de ficheiros Azure será montada no ponto de montagem especificado pelo comando anterior.  

    ![Instantâneo da partilha de ficheiros do Azure montada](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Passos seguintes
* [Ligue o seu Mac a computadores e servidores partilhados - Suporte apple](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)