---
title: Transferir dados com o Azure Data Box Edge | Microsoft Docs
description: Saiba como adicionar e ligar a partilhas no dispositivo Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e902f0c9465f65f31f6e1a5cadc7b6b30cda1a27
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403685"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Tutorial: Transferir dados com o Edge de caixa de dados do Azure

Este tutorial descreve como adicionar e se conectar a compartilhamentos no seu dispositivo do Edge de caixa de dados. Depois de adicionar as partilhas, dados de caixa de borda pode transferir dados para o Azure.

Este procedimento pode demorar cerca de 10 minutos a concluir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar à partilha

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar partilhas de dados de caixa de borda, certifique-se de que:

- Instalou o dispositivo físico, conforme descrito em [instalar o Azure dados caixa Edge](data-box-edge-deploy-install.md).

- Ativar o dispositivo físico, tal como descrito em [ligue-se de que configurar e ative o Edge de caixa de dados do Azure](data-box-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Adicionar uma partilha

Para criar uma partilha, faça o seguinte procedimento:

1. Na [portal do Azure](https://portal.azure.com/), selecione o recurso de borda de caixa de dados e, em seguida, vá para o **descrição geral**. O dispositivo deve estar online.

   ![Dispositivo online](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Selecione **+ Adicionar partilha** na barra de comando de dispositivo.

   ![Adicionar uma partilha](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. Na **Adicionar partilha** painel, efetue o seguinte procedimento:

    a. Na **nome** caixa, forneça um nome exclusivo para a sua partilha.  
    O nome da partilha pode ter apenas letras minúsculas, números e hífenes. Ele tem de ter entre 3 a 63 carateres e começar com uma letra ou um numeral. Hífenes tem de ser precedidos e seguidos por uma letra ou um numeral.
    
    b. Selecione um **Tipo** para a partilha.  
    O tipo pode ser **SMB** ou **NFS**, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux.  
    Dependendo se escolher a partilha SMB ou NFS, o resto das opções são ligeiramente diferentes. 

    c. Forneça uma conta de armazenamento onde a partilha irá residir. 

    
    d. Na **serviço de armazenamento** na lista pendente, selecione **Blob de blocos**, **BLOBs de páginas**, ou **ficheiros**.  
    O tipo de serviço que selecionar depende do formato de que pretende que os dados para utilizar no Azure. Neste exemplo, uma vez que queremos armazenar os dados como blobs de blocos no Azure, selecionamos **Blob de blocos**. Se selecionou **BLOBs de páginas**, certifique-se de que os dados estão alinhados de 512 bytes. Por exemplo, um VHDX tem sempre um alinhamento de 512 bytes.

    e. Crie um novo contentor de BLOBs ou utilize um já existente na lista pendente. Se criar um contentor de BLOBs, indique um nome de contentor. Se ainda não existir um contentor, é criado na conta de armazenamento com o nome da partilha criada recentemente.
   
    f. Dependendo de se criar uma partilha SMB ou uma partilha NFS, efetue um dos seguintes passos: 
     
    - **Partilha SMB**: Sob **todos os usuários de locais de privilégio**, selecione **criar nova** ou **utilizar existente**. Se criar um novo utilizador local, introduza um nome de utilizador e palavra-passe e, em seguida, confirme a palavra-passe. Esta ação atribui permissões para o usuário local. Depois de atribuir as permissões aqui, pode utilizar o Explorador de ficheiros de modificá-las.

        Se selecionar a **permitir apenas operações de leitura** caixa de verificação para esta partilha de dados, pode especificar os utilizadores só de leitura.

        ![Adicionar uma partilha SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Partilhar de NFS**: Introduza os endereços IP dos clientes permitidos que podem aceder à partilha.

        ![Adicionar uma partilha NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Selecione **criar** para criar a partilha.
    
    Foi notificado de que a criação de partilha está em curso. Depois de criar a partilha com as definições especificadas, o **partilhas** mosaico atualizações para refletir a nova partilha.
    

## <a name="connect-to-the-share"></a>Ligar à partilha

Agora pode ligar a um ou mais das partilhas de que criou no último passo. Consoante a existência de SMB ou uma partilha NFS, as etapas podem variar.

### <a name="connect-to-an-smb-share"></a>Ligar a uma partilha SMB

No cliente do Windows Server ligada ao seu dispositivo Edge de caixa de dados, ligue-se para uma partilha SMB ao introduzir os comandos:


1. Na janela de comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Quando lhe for pedido para fazer isso, introduza a palavra-passe para a partilha.  
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

4. Na **execute** janela, especifique a `\\<device IP address>`e, em seguida, selecione **OK**.  
   Explorador de ficheiros é aberto. Agora deve conseguir ver as partilhas que criou como pastas. No Explorador de ficheiros, faça duplo clique num compartilhamento (pasta) para ver o conteúdo.
 
    ![Ligar a uma partilha SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Os dados são escritos nestas partilhas à medida que são gerados, e o dispositivo emite os dados para a cloud.

### <a name="connect-to-an-nfs-share"></a>Ligar a uma partilha NFS

No seu cliente de Linux ligado ao seu dispositivo Edge de caixa de dados, efetue o seguinte procedimento:

1. Certifique-se de que o cliente tem o cliente de NFSv4 instalado. Para instalar o cliente NFS, utilize o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, aceda a [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Depois do cliente NFS está instalado, monte a partilha NFS que criou no seu dispositivo do Edge de caixa de dados utilizando o seguinte comando:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Utilizar `sync` opção ao montar partilhas melhora as taxas de transferência de ficheiros grandes.
    > Antes de montar a partilha, certifique-se de que já são criados os diretórios que atuará como mountpoints no seu computador local. Esses diretórios não devem conter quaisquer ficheiros ou subpastas.

    O exemplo seguinte mostra como ligar através de NFS a uma partilha no dispositivo Data Box Edge. O IP do dispositivo é `10.10.10.60`. A partilha `mylinuxshare2` é montada no ubuntuVM. O ponto de montagem da partilha é `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Os seguintes avisos são aplicáveis a esta versão:
> - Depois de criar um ficheiro na partilha, mudar o nome do ficheiro não é suportado. 
> - Eliminar um ficheiro de uma partilha não elimina a entrada na conta de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre os tópicos seguintes do Edge de caixa de dados:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar a uma partilha

Para saber como transformar os seus dados através da utilização de dados de caixa de borda, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Transformar dados com o Data Box Edge](./data-box-edge-deploy-configure-compute.md)


