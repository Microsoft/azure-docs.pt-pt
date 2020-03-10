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
ms.openlocfilehash: 623ec5e082a8ed889329936d020bf28434a56fbf
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942537"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Tutorial: Transferir dados com portal de caixas de dados Azure


## <a name="introduction"></a>Introdução

Este artigo descreve como adicionar e ligar a partilhas no seu Portal da Caixa de Dados. Depois de adicionar as ações, o dispositivo Data Box Gateway pode transferir dados para o Azure.

Este procedimento pode demorar cerca de 10 minutos a concluir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar a uma partilha


## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar partilhas ao Data Box Gateway, certifique-se de que:

- Você disponibilizou um dispositivo virtual e ligou-o conforme detalhado na [Provision a Data Box Gateway em Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou Provision a Data Box Gateway em [VMware](data-box-gateway-deploy-provision-vmware.md).

- Ativou o dispositivo virtual descrito no [Connect e ativou o portal da caixa](data-box-gateway-deploy-connect-setup-activate.md)de dados Azure .

- O dispositivo está pronto para criar partilhas e transferir dados.

## <a name="add-a-share"></a>Adicionar uma partilha

Para criar uma parte faça o seguinte procedimento:

1. No [portal Azure,](https://portal.azure.com/)selecione o seu recurso Data Box Gateway e, em seguida, vá para **a visão geral**. O seu dispositivo deve estar online. Selecione **+ Adicione a partilha** na barra de comando do dispositivo.
   
   ![Adicionar uma partilha](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Em **Add Share,** faça o seguinte procedimento:

    1. Indique um nome exclusivo para a partilha. Os nomes das ações só podem ter letras minúsculas, números e hífenes. O nome da partilha deve ter entre 3 e 63 caracteres de comprimento e começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.
    
    2. Selecione um **Tipo** para a partilha. O tipo pode ser SMB ou NFS, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux. Dependendo se escolher a partilha SMB ou NFS, as opções apresentadas são ligeiramente diferentes.

    3. Forneça uma conta de armazenamento onde a parte resida. Se um recipiente já não existe, é criado na conta de armazenamento com o nome de partilha recém-criado. Se o recipiente já existir, o recipiente é utilizado.
    
    4. Escolha o **Serviço de armazenamento** do blob de blocos, blob de páginas ou ficheiros. O tipo de serviço escolhido depende do formato no qual pretende que os dados residam no Azure. Por exemplo, neste caso, queremos que os dados residam como blobs de blocos no Azure, por conseguinte, selecionamos Blob de Blocos. Se escolher o Blob de Páginas, certifique-se de que os dados têm um alinhamento de 512 bytes. Por exemplo, um VHDX tem sempre um alinhamento de 512 bytes.
   
    5. Este passo depende de estar a criar uma partilha SMB ou NFS.
     
    - **Partilha SMB** - Sob **todos os privilégios do utilizador local,** selecione **Criar novo** ou **utilizar existentes**. Se criar um novo utilizador local, introduza um **nome** de utilizador e **uma palavra-passe,** e, em seguida, **confirme a palavra-passe**. Esta ação atribui as permissões ao utilizador local. Atualmente, a modificação das permissões de nível de partilha não é suportada.
    
        ![Adicionar uma partilha SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Se selecionar **Permitir apenas ler a** caixa de verificação de operações para estes dados de partilha, pode especificar utilizadores apenas de leitura.
        
    - **NFS share** - Introduza os endereços IP dos clientes permitidos que podem aceder à partilha.

        ![Adicionar uma partilha NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Selecione **Criar** para criar a parte.
    
    Foi notificado de que a criação de ações está em andamento. Após a criação da parte com as definições especificadas, as atualizações de azulejos **de Partilha** para refletir a nova ação.
    
    ![Azulejo de ações atualizadas](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Ligar à partilha

Agora pode ligar-se a uma ou mais das ações que criou no último passo. Dependendo se tem uma participação SMB ou NFS, os passos podem variar.

### <a name="connect-to-an-smb-share"></a>Ligar a uma partilha SMB

No seu cliente do Windows Server ligado ao portal da caixa de dados, ligue-se a uma parte SMB ao introduzir os comandos:


1. Numa janela de comando, escreva:

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
3. Na janela **Correr,** especifique a `\\<device IP address>` e, em seguida, selecione **OK**. O Explorador de Ficheiros abre. Agora deve poder ver as ações que criou como pastas. No File Explorer, clique duas vezes numa partilha (pasta) para visualizar o conteúdo.
 
    ![Ligar a uma partilha SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Os dados são escritos nestas partilhas à medida que são gerados, e o dispositivo emite os dados para a cloud.

### <a name="connect-to-an-nfs-share"></a>Ligar a uma partilha NFS

No seu cliente Linux ligado ao seu dispositivo Data Box Edge, faça o seguinte procedimento:

1. Certifique-se de que o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, utilize o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, aceda a [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Depois de o cliente NFS estar instalado, utilize o comando seguinte para montar a partilha NFS que criou no seu dispositivo Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Antes de configurar as montagens, certifique-se de que os diretórios que irão funcionar como pontos de montagem no computador local já foram criados e que já não contêm quaisquer ficheiros ou subpastas.

    O exemplo seguinte mostra como ligar através de NFS a uma partilha no dispositivo de Gateway. O IP do dispositivo virtual é `10.10.10.60`, a partilha `mylinuxshare2` está montada no ubuntuVM, sendo que o ponto de montagem é `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> As seguintes ressalvas aplicam-se a esta libertação:
> - Depois de um ficheiro ser criado nas ações, a renomeação do ficheiro não é suportada.
> - A eliminação de um ficheiro de uma partilha não elimina a entrada na conta de armazenamento.
> - Se utilizar `rsync` para copiar dados, `rsync -a` opção não é suportada.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar a uma partilha


Avance para o próximo tutorial para saber como administrar o Data Box Gateway.

> [!div class="nextstepaction"]
> [Utilizar a IU da Web local para administrar um Data Box Gateway](https://aka.ms/dbg-docs)


