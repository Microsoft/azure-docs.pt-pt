---
title: Criar um volume para o Azure NetApp Files | Microsoft Docs
description: Descreve como criar um volume para o Azure NetApp Files.
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
ms.topic: how-to-article
ms.date: 3/17/2019
ms.author: b-juche
ms.openlocfilehash: b45879adca58f60e5c16829a7fd2fffab33eee46
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400857"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Criar um volume para o Azure NetApp Files

Cada conjunto de capacidade pode ter um máximo de 500 volumes. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. Os ficheiros NetApp do Azure suporta volumes NFS e SMBv3. 

## <a name="before-you-begin"></a>Antes de começar 
Tem de ter um conjunto de capacidade já configurado.   
[Configure um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede tem de ser delegada a ficheiros do Azure NetApp.  
[Delegar uma sub-rede para ficheiros do Azure NetApp](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Criar um volume NFS

1.  Clique nas **Volumes** painel a partir do painel de conjuntos de capacidade. 

    ![Navegue para Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Clique em **+ Adicionar volume** para criar um volume.  
    O criar que é apresentada uma janela de Volume.

3.  Em criar uma janela de Volume, clique em **criar** e forneça informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome do volume que está a criar.   

        O nome tem de ser exclusivo dentro de um grupo de recursos. Tem de ser, pelo menos, três carateres de comprimento.  Pode utilizar quaisquer carateres alfanuméricos.

    * **Conjunto de capacidade**  
        Especifique o conjunto de capacidade onde pretende que o volume a ser criada.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual do Azure (Vnet) a partir da qual pretende aceder ao volume.  

        A Vnet que especificar tem de ter uma sub-rede de delegado ao serviço ficheiros do Azure NetApp. O serviço de ficheiros do Azure NetApp pode ser acedido apenas a partir da mesma Vnet ou de uma Vnet que está na mesma região que o volume através de Vnet peering. Também pode acessar o volume a partir da sua rede no local através de Express Route.   

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especificar tem de ser delegada a ficheiros do Azure NetApp. 
        
        Se não tiver delegado uma sub-rede, pode clicar **criar novo** no criar um Volume página. Em seguida, na página Criar sub-rede, especifique as informações de sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para ficheiros de NetApp do Azure. Em cada Vnet, apenas uma sub-rede pode ser delegada a ficheiros do Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique em **protocolo**, em seguida, selecione **NFS** como o tipo de protocolo para o volume.   
    * Especifique a **caminho do ficheiro** que será utilizado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e aceder ao volume.

        O nome de ficheiro pode conter apenas letras, números e hífenes ("-"). Tem de ter entre 16 e 40 carateres.  

    * Opcionalmente, [configurar política de exportação para o volume NFS](azure-netapp-files-configure-export-policy.md)

    ![Especificar o protocolo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Clique em **rever + criar** para rever os detalhes de volume.  Em seguida, clique em **criar** para criar o volume NFS.

    O volume que criou é apresentada na página Volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="create-an-smb-volume"></a>Criar um volume SMB

Os ficheiros NetApp do Azure suporta SMBv3 volumes. Terá de criar ligações de Active Directory antes de adicionar um volume SMB. 

### <a name="create-an-active-directory-connection"></a>Crie uma ligação do Active Directory

1. Na sua conta de NetApp, clique em **ligações do Active Directory**, em seguida, clique em **associar**.  

    ![Ligações do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela de associação do Active Directory, forneça as seguintes informações:

    * **DNS primário**   
        Este é o endereço IP de controlador de domínio para o preferencial Active Directory Domain Services para utilização com ficheiros de NetApp do Azure. 
    * **DNS secundário**  
        Este é o endereço IP de controlador de domínio para o secundário Active Directory Domain Services para utilização com ficheiros de NetApp do Azure. 
    * **Domínio**  
        Este é o nome de domínio de seu Active Directory Domain Services que pretende associar.
    * **Prefixo do servidor (conta de computador) de SMB**  
        Este é o prefixo de nomenclatura para a conta de computador no Active Directory que o serviço ficheiros do Azure NetApp irá utilizar para a criação de novas contas.

        Por exemplo, se o padrão de nomenclatura que sua organização utiliza para servidores de ficheiros for NAS-01, NAS-02..., NAS 045, em seguida, deve introduzir "NAS" para o prefixo. 

        O serviço irá criar contas de computador adicionais no Active Directory, conforme necessário.

    * **Caminho de unidade organizacional**  
        Este é o caminho LDAP para a unidade organizacional (UO) onde serão criadas contas de computador do servidor SMB. Ou seja, UO = segundo nível, UO = primeiro nível. 
    * As credenciais, incluindo seus **nome de utilizador** e **palavra-passe**

    ![Junte-se do Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Clique em **Associar**.  

    A ligação do Active Directory que criou é apresentada.

    ![Ligações do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Adicionar um volume SMB

1. Clique nas **Volumes** painel a partir do painel de conjuntos de capacidade. 

    ![Navegue para Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    O criar que é apresentada uma janela de Volume.

3. Em criar uma janela de Volume, clique em **criar** e forneça informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome do volume que está a criar.   

        O nome tem de ser exclusivo dentro de um grupo de recursos. Tem de ser, pelo menos, três carateres de comprimento.  Pode utilizar quaisquer carateres alfanuméricos.

    * **Caminho do ficheiro**  
        Especifique o caminho de ficheiro que será utilizado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e aceder ao volume.   
     
        O nome de ficheiro pode conter apenas letras, números e hífenes ("-"). Tem de ter entre 16 e 40 carateres.  

    * **Conjunto de capacidade**  
        Especifique o conjunto de capacidade onde pretende que o volume a ser criada.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual do Azure (Vnet) a partir da qual pretende aceder ao volume.  

        A Vnet que especificar tem de ter uma sub-rede de delegado ao serviço ficheiros do Azure NetApp. O serviço de ficheiros do Azure NetApp pode ser acedido apenas a partir da mesma Vnet ou de uma Vnet que está na mesma região que o volume através de Vnet peering. Também pode acessar o volume a partir da sua rede no local através de Express Route.   

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especificar tem de ser delegada a ficheiros do Azure NetApp. 
        
        Se não tiver delegado uma sub-rede, pode clicar **criar novo** no criar um Volume página. Em seguida, na página Criar sub-rede, especifique as informações de sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para ficheiros de NetApp do Azure. Em cada Vnet, apenas uma sub-rede pode ser delegada a ficheiros do Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique em **protocolo** e conclua as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione seu **do Active Directory** ligação na lista pendente.
    * Especifique o nome do volume partilhado em **nome da partilha**.

    ![Especifique o protocolo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique em **rever + criar** para rever os detalhes de volume.  Em seguida, clique em **criar** para criar o volume SMB.

    O volume que criou é apresentada na página Volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="next-steps"></a>Passos Seguintes  

* [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurar a política de exportação para um volume NFS](azure-netapp-files-configure-export-policy.md)
* [Saiba mais sobre a integração da rede virtual para serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
