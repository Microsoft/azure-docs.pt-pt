---
title: Como configurar o serviço ficheiros NetApp do Azure e criar um volume | Documentos da Microsoft
description: Descreve como configurar o serviço ficheiros NetApp do Azure e criar um volume rapidamente.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstarts
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 76c1543f5d9979d46b303f12c495dcaa403cef1d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56589125"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Configurar o Azure NetApp Files e criar um volume 

Este artigo mostra-lhe como configurar o serviço ficheiros NetApp do Azure e criar um volume rapidamente. 

## <a name="before-you-begin"></a>Antes de começar 

Tem de ser parte do programa de pré-visualização pública e na lista de permissões para aceder ao fornecedor de recursos de Microsoft.NetApp. Para obter detalhes sobre como aderir ao programa de Pré-visualização Pública, veja a [página de inscrição na Pré-visualização Pública do Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registre-se para ficheiros NetApp do Azure e o fornecedor de recursos de NetApp

1. No portal do Azure, clique no ícone do Azure Cloud Shell no canto superior direito.

      ![Ícone do Azure do Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Especifique a subscrição que estava na lista de permissões para ficheiros de NetApp do Azure:
    
        az account set --subscription <subscriptionId>

3. Registe o fornecedor de recursos do Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    O processo de registo pode demorar algum tempo a concluir.

## <a name="create-a-netapp-account"></a>Criar uma conta NetApp

1. Na caixa de pesquisa do portal do Azure, introduza **ficheiros do Azure NetApp** e, em seguida, selecione **NetApp serviço ficheiros do Azure (pré-visualização)** na lista que é apresentada.

      ![Selecione ficheiros NetApp do Azure](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Clique em **+ Adicionar** para criar uma nova conta NetApp.

     ![Criar nova conta de NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Na janela da nova conta de NetApp, forneça as seguintes informações: 
    1. Introduza **myaccount1** para o nome da conta. 
    2. Selecione a sua subscrição.
    3. Selecione **criar novo** para criar novo grupo de recursos. Introduza **myRG1** para o nome do grupo de recursos. Clique em **OK**. 
    4. Selecione seu local de conta.  

    ![Nova janela de conta de NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

    ![Janela de grupo de recursos](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Clique em **criar** para criar a sua nova conta de NetApp.

## <a name="set-up-a-capacity-pool"></a>Configurar um conjunto de capacidade

1. No painel de gestão do Azure NetApp ficheiros, selecione a sua conta de NetApp (**myaccount1**).

    ![Selecione a conta de NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. No painel de gestão do Azure NetApp ficheiros da sua conta de NetApp, clique em **conjuntos de capacidade**.

    ![Clique em conjuntos de capacidade](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Clique em **+ adicionar agrupamentos**. 

    ![Clique em Adicionar agrupamentos](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Forneça informações para o conjunto de capacidade: 
    1. Introduza **mypool1** como o nome do conjunto.
    2. Selecione **Premium** do nível de serviço. 
    3. Especifique **4 (TiB)** como o tamanho do conjunto. 

5. Clique em **OK**.

## <a name="create-a-volume-for-azure-netapp-files"></a>Criar um volume para o Azure NetApp Files

1. No painel de gestão do Azure NetApp ficheiros da sua conta de NetApp, clique em **Volumes**.

    ![Clicar em Volumes](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Clique em **+ Adicionar volume**.

    ![Clique em Adicionar volumes](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Criar uma janela de Volume, fornecem informações para o volume: 
    1. Introduza **myvol1** como o nome do volume. 
    2. Introduza **myfilepath1** como o caminho do ficheiro que será utilizado para criar o caminho de exportação para o volume.
    3. Selecione o conjunto de capacidade (**mypool1**).
    4. Utilize o valor predefinido para a quota. 
    5. Numa rede virtual, clique em **criar novo** para criar um novo Azure virtual network (Vnet).  Em seguida, preencha as seguintes informações:
        * Introduza **myvnet1** como o nome da Vnet.
        * Especifique um espaço de endereços, por exemplo, 10.7.0.0/16.
        * Introduza **myANFsubnet** como o nome da sub-rede.
        * Especifique o intervalo de endereços de sub-rede, por exemplo, 10.7.0.0/24.
        * Selecione **Microsoft.NetApp/volumes** para delegação de sub-rede.
        * Clique em **OK** para criar a Vnet.
    6. Na sub-rede, selecione a Vnet criada recentemente (**myvnet1**) como a sub-rede do delegado.

    ![Criar uma janela de volume](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

    ![Criar a janela de rede virtual](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Clique em **Rever + criar**.

    ![Reveja e criar a janela](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Rever as informações do volume, em seguida, clique em **criar**.  
    O volume criado é apresentada no painel de Volumes.

    ![Volume criado](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>Passos Seguintes  

* [Compreender a hierarquia de armazenamento de ficheiros do Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md)
* [Gerir volumes através do serviço ficheiros do Azure NetApp](azure-netapp-files-manage-volumes.md) 
