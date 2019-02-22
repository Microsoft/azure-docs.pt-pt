---
title: Transferir dados com o Azure Data Box Gateway | Microsoft Docs
description: Saiba como adicionar e ligar a partilhas no dispositivo Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: f36e13ccf91c983c54897dcff7e1c02689fb055c
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592660"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>Tutorial: Transferir dados com o Gateway de caixa de dados do Azure (pré-visualização)


## <a name="introduction"></a>Introdução

Este artigo descreve como adicionar e ligar a partilhas no Data Box Gateway. Assim que as partilhas forem adicionadas, o dispositivo Data Box Gateway pode transferir dados para o Azure.

Este procedimento pode demorar cerca de 10 minutos a concluir. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar a uma partilha

> [!IMPORTANT]
> - O Data Box Gateway está em pré-visualização. Reveja os [Termos de serviço do Azure para pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de encomendar e implementar esta solução. 
 
## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar partilhas ao Data Box Gateway, certifique-se de que:

* Aprovisionou um dispositivo virtual e ligou ao mesmo, conforme detalhado em [Aprovisionar um Data Box Gateway no Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [Aprovisionar um Data Box Gateway no VMware](data-box-gateway-deploy-provision-vmware.md). 

    O dispositivo virtual é ativado conforme detalhado em [Ligar e ativar o Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md) e está pronto para criar partilhas e transferir dados.


## <a name="add-a-share"></a>Adicionar uma partilha

Execute os seguintes passos no [portal do Azure](https://portal.azure.com/) para criar uma partilha.

1. Regresse ao portal do Azure. Aceda a **Todos os recursos** e procure o seu recurso Data Box Gateway.
    
2. Na lista filtrada de recursos, selecione o recurso Data Box Gateway e, em seguida, navegue para **Descrição Geral**. Clique em **+ Adicionar partilha** na barra de comando do dispositivo.
   
   ![Adicionar uma partilha](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Em **Adicionar Partilha**, especifique as definições de partilha. Indique um nome exclusivo para a partilha. 

   Os nomes das partilhas só podem conter números, letras minúsculas e hífenes. O nome da partilha tem de ter entre 3 e 63 carateres e começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.
    
5. Selecione um **Tipo** para a partilha. O tipo pode ser SMB ou NFS, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux. Dependendo se escolher a partilha SMB ou NFS, as opções apresentadas são ligeiramente diferentes. 

6. Tem de fornecer uma conta de armazenamento onde a partilha irá residir. É criado um contentor na conta de armazenamento com o nome da partilha, se o contentor ainda não existir. Se o contentor já existir, é utilizado o contentor existente. 
    
7. Escolha o **Serviço de armazenamento** do blob de blocos, blob de páginas ou ficheiros. O tipo de serviço escolhido depende do formato no qual pretende que os dados residam no Azure. Por exemplo, neste caso, queremos que os dados residam como blobs de blocos no Azure, por conseguinte, selecionamos Blob de Blocos. Se escolher o Blob de Páginas, certifique-se de que os dados têm um alinhamento de 512 bytes. Tenha em atenção que o VHDX tem sempre um alinhamento de 512 bytes.
   
8. Este passo depende de estar a criar uma partilha SMB ou NFS. 
     
    - **Se criar uma partilha SMB** - no campo de utilizador local Todos os privilégios, escolha **Criar novo** ou **Utilizar existente**. Se criar um novo utilizador local, forneça o **nome de utilizador**, a **palavra-passe** e, em seguida **confirme a palavra-passe**. Esta ação atribui as permissões ao utilizador local. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.
    
        ![Adicionar uma partilha SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Se marcar **permitir apenas operações de leitura** para esta partilha de dados, terá a opção de especificar utilizadores só de leitura.
        
    - **Se criar uma partilha NFS** - tem de fornecer os endereços IP dos clientes permitidos que podem aceder à partilha.

        ![Adicionar uma partilha NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Clique em **Criar** para criar a partilha. 
    
    Será notificado de que a criação da partilha está em curso. Depois de criar a partilha com as definições especificadas, o painel **Partilhas** é atualizado para refletir a nova partilha. 
    
    ![Lista atualizada de partilhas](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Ligar à partilha

Efetue estes passos no cliente do Windows Server ligado ao seu Data Box Gateway para ligar às partilhas.


1. Abra uma janela de comando. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Introduza a palavra-passe da partilha quando lhe for pedido. A saída de exemplo deste comando é apresentada aqui.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK**. Esta ação abre o Explorador de Ficheiros. Agora deve conseguir ver as partilhas que criou como pastas. Selecione e faça duplo clique numa partilha (pasta) para ver o conteúdo.
 
    ![Ligar a uma partilha SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Os dados são escritos nestas partilhas à medida que são gerados, e o dispositivo emite os dados para a cloud.

### <a name="connect-to-an-nfs-share"></a>Ligar a uma partilha NFS

Execute estes passos no seu cliente Linux ligado ao Data Box Edge.

1. Certifique-se de que o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, utilize o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, aceda a [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Depois de o cliente NFS estar instalado, utilize o comando seguinte para montar a partilha NFS que criou no seu dispositivo Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Antes de configurar as montagens, certifique-se de que os diretórios que irão funcionar como pontos de montagem no computador local já foram criados e que já não contêm quaisquer ficheiros ou subpastas.

    O exemplo seguinte mostra como ligar através de NFS a uma partilha no dispositivo de Gateway. O IP do dispositivo virtual é `10.10.10.60`, a partilha `mylinuxshare2` está montada no ubuntuVM, sendo que o ponto de montagem é `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Os seguintes avisos aplicam-se à versão de pré-visualização:
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


