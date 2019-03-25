---
title: Transferir dados com o Azure Data Box Gateway | Microsoft Docs
description: Saiba como adicionar e ligar a partilhas no dispositivo Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: d930b1db48e3a5c4bda96f0b7d80a9c9f24d53d9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400646"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Tutorial: Transferir dados com o Gateway de caixa de dados do Azure


## <a name="introduction"></a>Introdução

Este artigo descreve como adicionar e se conectar a compartilhamentos no seu Gateway de caixa de dados. Depois de adicionar as partilhas, o dispositivo de Gateway de caixa de dados pode transferir dados para o Azure.

Este procedimento pode demorar cerca de 10 minutos a concluir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar a uma partilha


## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar partilhas ao Data Box Gateway, certifique-se de que:

- Tiver aprovisionado um dispositivo virtual e ligada à mesma, conforme detalhado no [aprovisionar um Gateway de caixa de dados no Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [aprovisionar um Gateway de caixa de dados no VMware](data-box-gateway-deploy-provision-vmware.md).

- Ativar o dispositivo virtual descrito em [Connect e ativar o Gateway de caixa de dados do Azure](data-box-gateway-deploy-connect-setup-activate.md).

- O dispositivo está pronto para criar partilhas e transferir dados.

## <a name="add-a-share"></a>Adicionar uma partilha

Para criar uma partilha faça o seguinte procedimento:

1. Na [portal do Azure](https://portal.azure.com/), selecione o recurso de Gateway de caixa de dados e, em seguida, aceda à **descrição geral**. O dispositivo deve estar online. Selecione **+ Adicionar partilha** na barra de comando de dispositivo.
   
   ![Adicionar uma partilha](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Na **Adicionar partilha**, efetue o seguinte procedimento:

    1. Indique um nome exclusivo para a partilha. Os nomes de partilha só podem ter letras minúsculas, números e hífenes. O nome da partilha tem de ter entre 3 e 63 carateres de comprimento e começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.
    
    2. Selecione um **Tipo** para a partilha. O tipo pode ser SMB ou NFS, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux. Dependendo se escolher a partilha SMB ou NFS, as opções apresentadas são ligeiramente diferentes.

    3. Forneça uma conta de armazenamento onde a partilha irá residir. Se ainda não existir um contentor, é criado na conta de armazenamento com o nome da partilha criada recentemente. Se o contentor já existir, é usado nesse contentor.
    
    4. Escolha o **Serviço de armazenamento** do blob de blocos, blob de páginas ou ficheiros. O tipo de serviço escolhido depende do formato no qual pretende que os dados residam no Azure. Por exemplo, neste caso, queremos que os dados residam como blobs de blocos no Azure, por conseguinte, selecionamos Blob de Blocos. Se escolher o Blob de Páginas, certifique-se de que os dados têm um alinhamento de 512 bytes. Por exemplo, um VHDX tem sempre um alinhamento de 512 bytes.
   
    5. Este passo depende de estar a criar uma partilha SMB ou NFS.
     
    - **Partilha SMB** – em **todos os usuários de locais de privilégio**, selecione **criar novo** ou **utilizar existente**. Se criar um novo utilizador local, introduza um **nome de utilizador** e **palavra-passe**e, em seguida **Confirmar palavra-passe**. Esta ação atribui as permissões para o usuário local. Depois de atribuir as permissões aqui, pode utilizar o Explorador de ficheiros para modificar estas permissões.
    
        ![Adicionar uma partilha SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Se selecionou **permitir apenas operações de leitura** caixa de verificação para esta partilha de dados, pode especificar os utilizadores só de leitura.
        
    - **Partilhar de NFS** -introduza os endereços IP dos clientes permitidos que podem aceder à partilha.

        ![Adicionar uma partilha NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Selecione **criar** para criar a partilha.
    
    Foi notificado de que a criação de partilha está em curso. Depois de criar a partilha com as definições especificadas, o **partilhas** mosaico atualizações para refletir a nova partilha.
    
    ![Peça de mosaico partilhas atualizada](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Ligar à partilha

Agora pode ligar a um ou mais das partilhas de que criou no último passo. Consoante a existência de SMB ou uma partilha NFS, as etapas podem variar.

### <a name="connect-to-an-smb-share"></a>Ligar a uma partilha SMB

No cliente do Windows Server ligada ao seu Gateway de caixa de dados, ligue-se para uma partilha SMB ao introduzir os comandos:


1. Na janela de comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Introduza a palavra-passe da partilha quando lhe for pedido. A saída de exemplo deste comando é apresentada aqui.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. No seu teclado, selecione Windows + R. 
3. Na **execute** janela, especifique a `\\<device IP address>` e, em seguida, selecione **OK**. Explorador de ficheiros é aberto. Agora deve conseguir ver as partilhas que criou como pastas. No Explorador de ficheiros, faça duplo clique num compartilhamento (pasta) para ver o conteúdo.
 
    ![Ligar a uma partilha SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Os dados são escritos nestas partilhas à medida que são gerados, e o dispositivo emite os dados para a cloud.

### <a name="connect-to-an-nfs-share"></a>Ligar a uma partilha NFS

No seu cliente de Linux ligado ao seu dispositivo Edge de caixa de dados, efetue o seguinte procedimento:

1. Certifique-se de que o cliente tem o cliente de NFSv4 instalado. Para instalar o cliente NFS, utilize o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, aceda a [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Depois de o cliente NFS estar instalado, utilize o comando seguinte para montar a partilha NFS que criou no seu dispositivo Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Antes de configurar as montagens, certifique-se de que os diretórios que irão funcionar como pontos de montagem no computador local já foram criados e que já não contêm quaisquer ficheiros ou subpastas.

    O exemplo seguinte mostra como ligar através de NFS a uma partilha no dispositivo de Gateway. O IP do dispositivo virtual é `10.10.10.60`, a partilha `mylinuxshare2` está montada no ubuntuVM, sendo que o ponto de montagem é `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Os seguintes avisos são aplicáveis a esta versão:
> - Depois de um ficheiro ser criado nas partilhas, mudar o nome do ficheiro não é suportado.
> - A eliminação de um ficheiro de uma partilha não elimina a entrada na conta de armazenamento.
> - Se utilizar `rsync` para copiar dados, em seguida, `rsync -a` opção não é suportada.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar a uma partilha


Avance para o próximo tutorial para saber como administrar o Data Box Gateway.

> [!div class="nextstepaction"]
> [Utilizar a IU da Web local para administrar um Data Box Gateway](https://aka.ms/dbg-docs)


