---
title: Tutorial para transferir dados para partilhas com a Azure Stack Edge Pro GPU Microsoft Docs
description: Saiba como adicionar e conectar-se a ações no dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 12/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 845eef6e119823af789c9a263bfb750845d55bc0
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740917"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>Tutorial: Transferir dados através de ações com a Azure Stack Edge Pro GPU

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este tutorial descreve como adicionar e conectar-se a ações no seu dispositivo Azure Stack Edge Pro. Depois de adicionar as ações, o Azure Stack Edge Pro pode transferir dados para o Azure.

Este procedimento pode demorar cerca de 10 minutos a concluir.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * Adicionar uma partilha
> * Ligar à partilha

## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar ações ao Azure Stack Edge Pro, certifique-se de que:

* Instalou o seu dispositivo físico como descrito no [Install Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).

* Ativou o dispositivo físico como descrito no [Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

## <a name="add-a-share"></a>Adicionar uma partilha

Para criar uma partilha, faça o seguinte procedimento:

1. No [portal Azure,](https://portal.azure.com/)selecione o seu recurso Azure Stack Edge e, em seguida, vá à **Visão Geral**. O seu dispositivo deve estar online.

   ![Dispositivo online](./media/azure-stack-edge-j-series-deploy-add-shares/device-online-1.png)

2. **Selecione + Adicione a partilha** na barra de comando do dispositivo.

   ![Adicionar uma partilha](./media/azure-stack-edge-j-series-deploy-add-shares/select-add-share-1.png)

3. No painel **de partilha Add,** faça o seguinte procedimento:

    a. Na caixa **Nome,** forneça um nome único para a sua parte.  
    O nome da partilha só pode ter letras, algarismos e hífenes. Deve ter entre 3 a 63 caracteres e começar com uma letra ou um numeral. Os hífenes devem ser precedidos e seguidos por uma letra ou um numeral.
    
    b. Selecione um **Tipo** para a partilha.  
    O tipo pode ser **SMB** ou **NFS,** sendo o SMB o padrão. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux.  
    Dependendo se escolhe ações SMB ou NFS, as restantes opções variam ligeiramente. 

    c. Indique uma conta de armazenamento onde a partilha vai residir.

    d. Na lista de drop-down **do serviço de armazenamento,** selecione **Block Blob**, **Page Blob** ou **Files**.  
    O tipo de serviço que seleciona depende do formato que pretende utilizar no Azure. Neste exemplo, porque queremos armazenar os dados como blobs de bloco em Azure, selecionamos **Block Blob**. Se selecionar **Page Blob,** certifique-se de que os seus dados estão alinhados com 512 bytes. Por exemplo, um VHDX tem sempre um alinhamento de 512 bytes.

   > [!IMPORTANT]
   > Certifique-se de que a conta de Armazenamento Azure que utiliza não tem políticas de imutabilidade definidas se estiver a usá-la com um dispositivo Azure Stack Edge Pro ou Data Box Gateway. Para obter mais informações, consulte [Definir e gerir políticas de imutabilidade para armazenamento de bolhas](../storage/blobs/storage-blob-immutability-policies-manage.md).

    e. Crie um novo recipiente blob ou utilize um existente na lista de retiradas. Se criar um recipiente de bolhas, forneça um nome de recipiente. Se ainda não existir nenhum contentor, este será criado na conta de armazenamento com o nome da partilha recentemente criada.
   
    f. Dependendo se criou uma participação na SMB ou numa participação da NFS, faça um dos seguintes passos: 
     
    - **Partilha SMB**: Sob **todos os privilégios utilizador local,** selecione **Criar novo** ou **utilizar existente**. Se criar um utilizar local novo, introduza um nome de utilizador e uma palavra-passe e, em seguida, confirme a palavra-passe. Esta ação atribui as permissões ao utilizador local. Atualmente, não é suportada a modificação das permissões de nível de ação. Se selecionar a caixa **de verificação de operações de leitura apenas** para estes dados de partilha, pode especificar utilizadores apenas de leitura.
    
        ![Adicionar uma partilha SMB](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS partilhar**: Introduza os endereços IP de clientes autorizados que possam aceder à parte.

        ![Adicionar uma partilha NFS](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-nfs-1.png)
   
4. Selecione **Criar** para criar a partilha.
    
    Foi-lhe notificada de que a criação de ações está em andamento. Após a criação da partilha com as definições especificadas, **as ações** tile atualizações para refletir a nova ação.
    

## <a name="connect-to-the-share"></a>Ligar à partilha

Agora pode ligar-se a uma ou mais das ações que criou no último passo. Dependendo se tem uma participação SMB ou NFS, os passos podem variar.

O primeiro passo é garantir que o nome do dispositivo pode ser resolvido quando estiver a utilizar a partilha SMB ou NFS.

### <a name="modify-host-file-for-name-resolution"></a>Modificar o ficheiro do anfitrião para a resolução do nome

Irá agora adicionar o IP do dispositivo e o nome amigável do dispositivo que definiu na UI web local do dispositivo para:

- O ficheiro do anfitrião no cliente, OU,
- O ficheiro de anfitrião no servidor DNS

> [!IMPORTANT]
> Recomendamos que modifique o ficheiro anfitrião no servidor DNS para a resolução do nome do dispositivo.

No seu cliente Windows que está a utilizar para se ligar ao dispositivo, tome os seguintes passos:

1. Inicie **o Bloco de Notas** como administrador e, em seguida, abra o ficheiro de **anfitriões** localizado em `C:\Windows\System32\Drivers\etc` .

    ![Ficheiro de anfitriões do Windows Explorer](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-1.png)


2. Adicione a seguinte entrada no ficheiro dos **anfitriões** substituindo os valores adequados para o seu dispositivo: 

    ```
    <Device IP>   <device friendly name>
    ``` 
    Pode obter o IP do dispositivo da **Rede** e o nome amigável do dispositivo na página **dispositivo** na UI web local. A imagem a seguir do ficheiro dos anfitriões mostra a entrada:

    ![Windows Explorer acolhe ficheiro 2](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>Ligar a uma partilha SMB

No seu cliente Windows Server ligado ao seu dispositivo Azure Stack Edge Pro, conecte-se a uma partilha SMB inserindo os comandos:


1. Numa janela de comandos, escreva: 

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > Pode ligar-se a uma partilha SMB apenas com o nome do dispositivo e não através do endereço IP do dispositivo.

2. Quando lhe for pedido, introduza a palavra-passe para a partilha.  
   A saída de exemplo deste comando é apresentada aqui.

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. No seu teclado, selecione Windows + R.

4. Na janela **Executar,** especifique o `\\<device name>` , e, em seguida, selecione **OK**.  

    ![Diálogo de execução do Windows](media/azure-stack-edge-j-series-deploy-add-shares/run-window-1.png)

   O Explorador de Ficheiros é aberto. Deve agora conseguir ver as partilhas que criou como pastas. No Explorador de Ficheiros, faça duplo clique numa partilha (pasta) para ver o conteúdo.
 
    ![Ligar a uma partilha SMB](./media/azure-stack-edge-j-series-deploy-add-shares/file-explorer-smbshare-1.png)

    Os dados são escritos nestas partilhas à medida que são gerados, e o dispositivo emite os dados para a cloud.

### <a name="connect-to-an-nfs-share"></a>Ligar a uma partilha NFS

No seu cliente Linux ligado ao seu dispositivo Azure Stack Edge Pro, faça o seguinte procedimento:

1. Confirme que o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, utilize o seguinte comando:

   `sudo apt-get install nfs-common`

    Para mais informações, aceda ao [cliente NFSv4.](https://help.ubuntu.com/community/NFSv4Howto)

2. Depois de instalar o cliente NFS, monte a partilha NFS que criou no seu dispositivo Azure Stack Edge Pro utilizando o seguinte comando:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Pode obter o IP do dispositivo na página **de Rede** da sua UI web local.

    > [!IMPORTANT]
    > A `sync` utilização da opção ao acumular ações melhora as taxas de transferência de grandes ficheiros.
    > Antes de montar a partilha, certifique-se de que os diretórios que funcionarão como pontos de montagem no seu computador local já estão criados. Estes diretórios não devem conter quaisquer ficheiros ou sub-dobradores.

    O exemplo a seguir mostra como ligar via NFS a uma partilha no seu dispositivo Azure Stack Edge Pro. O IP do dispositivo é `10.10.10.60`. A partilha `mylinuxshare2` é montada no ubuntuVM. O ponto de montagem da partilha é `/home/azurestackedgeubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> As seguintes ressalvas são aplicáveis a esta versão:
> - Depois de um ficheiro ser criado na partilha, o renomeamento do ficheiro não é suportado. 
> - A eliminação de um ficheiro de uma ação não elimina a entrada na conta de Armazenamento Azure.
> - Quando `rsync` utilizar para copiar sobre NFS, utilize a `--inplace` bandeira. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre os seguintes tópicos Azure Stack Edge Pro:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Ligar a uma partilha

Para aprender a transformar os seus dados utilizando o Azure Stack Edge Pro, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transforme dados com Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)