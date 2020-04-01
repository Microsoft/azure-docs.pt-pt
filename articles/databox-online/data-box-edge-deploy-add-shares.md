---
title: Tutorial para transferir dados para partilhas com A Borda da Caixa de Dados do Azure [ Microsoft Docs
description: Saiba como adicionar e ligar a partilhas no dispositivo Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3b1988656e2c15515e121df3ee71e31ce7edd750
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79212951"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Tutorial: Transferir dados com borda de caixa de dados Azure

Este tutorial descreve como adicionar e ligar a partilhas no seu dispositivo Data Box Edge. Depois de adicionar as ações, data Box Edge pode transferir dados para o Azure.

Este procedimento pode demorar cerca de 10 minutos a concluir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar à partilha

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar ações ao Data Box Edge, certifique-se de que:

- Instalou o seu dispositivo físico conforme descrito no Edge de Caixa de [Dados Azure](data-box-edge-deploy-install.md).

- Ativou o dispositivo físico conforme descrito no [Connect, configurae e ativou](data-box-edge-deploy-connect-setup-activate.md)o Azure Data Box Edge .


## <a name="add-a-share"></a>Adicionar uma partilha

Para criar uma parte, faça o seguinte procedimento:

1. No [portal Azure,](https://portal.azure.com/)selecione o seu recurso Data Box Edge e, em seguida, vá para a **visão geral**. O seu dispositivo deve estar online.

   ![Dispositivo online](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Selecione **+ Adicione a partilha** na barra de comando do dispositivo.

   ![Adicionar uma partilha](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. No painel **de partilha adicionar,** faça o seguinte procedimento:

    a. Na caixa **Nome,** forneça um nome único para a sua parte.  
    O nome da partilha só pode ter letras minúsculas, algarismos e hífenes. Deve ter entre 3 a 63 caracteres e começar com uma letra ou um numeral. Os hífenes devem ser precedidos e seguidos por uma letra ou um numeral.
    
    b. Selecione um **Tipo** para a partilha.  
    O tipo pode ser **SMB** ou **NFS**, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux.  
    Dependendo se escolhe ações da SMB ou da NFS, as restantes opções variam ligeiramente. 

    c. Forneça uma conta de armazenamento onde a parte resida.

      > [!IMPORTANT]
      > Certifique-se de que a conta de Armazenamento Azure que utiliza não tem políticas de imutabilidade definidas se estiver a usá-la com um dispositivo Azure Stack Edge ou Data Box Gateway. Para mais informações, consulte definir e gerir as políticas de [imutabilidade para armazenamento de blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

    d. Na lista de drop-down do **serviço de armazenamento,** selecione **Block Blob,** **Page Blob**ou **Files**.  
    O tipo de serviço que selecionar depende do formato que pretende utilizar os dados no Azure. Neste exemplo, porque queremos armazenar os dados como blocos blobs em Azure, selecionamos **Block Blob**. Se selecionar o **Page Blob,** certifique-se de que os seus dados estão alinhados 512 bytes. Por exemplo, um VHDX tem sempre um alinhamento de 512 bytes.

    e. Crie um novo recipiente de bolhas ou utilize um existente a partir da lista de abandono. Se criar um recipiente de bolhas, forneça um nome de recipiente. Se um recipiente já não existe, é criado na conta de armazenamento com o nome de partilha recém-criado.

    f. Dependendo se criou uma quota SMB ou uma participação nFS, faça um dos seguintes passos:

    * **Partilha SMB**: Sob **todos os privilégios do utilizador local,** selecione **Criar novo** ou **utilizar existentes**. Se criar um novo utilizador local, introduza um nome de utilizador e uma senha e, em seguida, confirme a palavra-passe. Esta ação atribui permissões ao utilizador local. Atualmente, a modificação das permissões de nível de partilha não é suportada.

        Se selecionar a caixa de verificação de **operações apenas para** ver estas ações, pode especificar utilizadores apenas de leitura.

        ![Adicionar uma partilha SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)

    * **NFS share**: Introduza os endereços IP de clientes autorizados que possam aceder à partilha.

        ![Adicionar uma partilha NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)

4. Selecione **Criar** para criar a parte.
    
    Foi notificado de que a criação de ações está em andamento. Após a criação da parte com as definições especificadas, as atualizações de azulejos **de Partilha** para refletir a nova ação.
    

## <a name="connect-to-the-share"></a>Ligar à partilha

Agora pode ligar-se a uma ou mais das ações que criou no último passo. Dependendo se tem uma participação SMB ou NFS, os passos podem variar.

### <a name="connect-to-an-smb-share"></a>Ligar a uma partilha SMB

No seu cliente Do Windows Server ligado ao seu dispositivo Data Box Edge, ligue-se a uma parte SMB ao introduzir os comandos:


1. Numa janela de comando, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Quando for solicitado, introduza a palavra-passe para a partilha.  
   A saída de exemplo deste comando é apresentada aqui.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. No seu teclado, selecione Windows + R.

4. Na janela **Correr,** `\\<device IP address>`especifique o , e, em seguida, selecione **OK**.  
   O Explorador de Ficheiros abre. Agora deve poder ver as ações que criou como pastas. No File Explorer, clique duas vezes numa partilha (pasta) para visualizar o conteúdo.
 
    ![Ligar a uma partilha SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Os dados são escritos nestas partilhas à medida que são gerados, e o dispositivo emite os dados para a cloud.

### <a name="connect-to-an-nfs-share"></a>Ligar a uma partilha NFS

No seu cliente Linux ligado ao seu dispositivo Data Box Edge, faça o seguinte procedimento:

1. Certifique-se de que o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, utilize o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, aceda a [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Depois de instalado o cliente NFS, monte a parte NFS que criou no seu dispositivo Data Box Edge utilizando o seguinte comando:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > A `sync` utilização da opção ao montar ações melhora as taxas de transferência de ficheiros grandes.
    > Antes de montar a parte, certifique-se de que os diretórios que funcionarão como pontos de montagem no seu computador local já estão criados. Estes diretórios não devem conter ficheiros ou subpastas.

    O exemplo seguinte mostra como ligar através de NFS a uma partilha no dispositivo Data Box Edge. O IP do dispositivo é `10.10.10.60`. A partilha `mylinuxshare2` é montada no ubuntuVM. O ponto de montagem da partilha é `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> As seguintes ressalvas aplicam-se a esta libertação:
> - Depois de um ficheiro ser criado na parte, a renomeação do ficheiro não é suportada. 
> - A eliminação de um ficheiro de uma ação não elimina a entrada na conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre os seguintes tópicos data Box Edge:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar a uma partilha

Para aprender a transformar os seus dados utilizando data box edge, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transformar dados com o Data Box Edge](./data-box-edge-deploy-configure-compute.md)


