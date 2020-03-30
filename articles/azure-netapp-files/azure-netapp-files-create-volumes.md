---
title: Criar um volume NFS para Ficheiros Azure NetApp / Microsoft Docs
description: Descreve como criar um volume NFS para Ficheiros Azure NetApp.
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
ms.topic: conceptual
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274089"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Criar um volume NFS para o Azure NetApp Files

Os ficheiros Azure NetApp suportam os volumes NFS (NFSv3 e NFSv4.1) e SMBv3. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. Este artigo mostra-lhe como criar um volume NFS. Se quiser criar um volume SMB, consulte [Criar um volume SMB para Ficheiros Azure NetApp](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Antes de começar 
Tem de ter um conjunto de capacidade já configurado.   
[Criar uma piscina de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada nos Ficheiros Azure NetApp.  
[Delegar uma sub-rede para os Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Considerações 

* Decidir qual versão NFS usar  
  O NFSv3 pode lidar com uma grande variedade de casos de uso e é comumente implantado na maioria das aplicações empresariais. Deve validar qual a versão (NFSv3 ou NFSv4.1) que a sua aplicação necessita e criar o seu volume utilizando a versão adequada. Por exemplo, se utilizar [o Apache ActiveMQ,](https://activemq.apache.org/shared-file-system-master-slave)recomenda-se o bloqueio de ficheiros com NFSv4.1 em vez de NFSv3. 

* Segurança  
  O suporte para bits de modo UNIX (ler, escrever e executar) está disponível para NFSv3 e NFSv4.1. É necessário um acesso ao nível da raiz no cliente nFS para montar volumes NFS.

* Suporte local do utilizador/grupo e lDAP para NFSv4.1  
  Atualmente, o NFSv4.1 suporta apenas o acesso à raiz a volumes. Consulte o [domínio padrão Configure NFSv4.1 para Ficheiros Azure NetApp](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Melhores práticas

* Deve certificar-se de que está a utilizar as instruções de montagem adequadas para o volume.  Consulte [o Monte ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* O cliente NFS deve estar no mesmo VNet ou vNet com um par que o volume de Ficheiros Azure NetApp. A ligação de fora da VNet é suportada; no entanto, introduzirá latência adicional e diminuirá o desempenho global.

* Deve certificar-se de que o cliente NFS está atualizado e a executar as últimas atualizações para o sistema operativo.

## <a name="create-an-nfs-volume"></a>Criar um volume NFS

1.  Clique na lâmina **Volumes** a partir da lâmina De Capacidade Pools. 

    ![Navegar para volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Clique em **+ Adicionar volume** para criar um volume.  
    Aparece a janela Criar um Volume.

3.  Na janela Criar uma janela de Volume, clique em **Criar** e fornecer informações para os seguintes campos:   
    * **Nome de volume**      
        Especifique o nome do volume que está a criar.   

        Um nome de volume deve ser único dentro de cada piscina de capacidade. Deve ter pelo menos três caracteres de comprimento. Podes usar qualquer personagem alfanumérico.   

        Não pode `default` usar como nome de volume.

    * **Piscina de capacidade**  
        Especifique o conjunto de capacidades onde pretende que o volume seja criado.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual do Azure (Vnet) a partir da qual pretende aceder ao volume.  

        O Vnet que especifica deve ter uma sub-rede delegada nos Ficheiros Azure NetApp. O serviço Azure NetApp Files só pode ser acedido a partir do mesmo Vnet ou de um Vnet que se encontra na mesma região que o volume através do peering Vnet. Também pode aceder ao volume da sua rede no local através da Rota Expresso.   

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especifica deve ser delegada nos Ficheiros Azure NetApp. 
        
        Se não tiver delegado uma sub-rede, pode clicar em **Criar novo** na página Criar um Volume. Em seguida, na página Create Subnet, especifique as informações da subnet e selecione **Microsoft.NetApp/volumes** para delegar a subnet para Ficheiros Azure NetApp. Em cada Vnet, apenas uma subnet pode ser delegada nos Ficheiros Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique no **Protocolo**e, em seguida, complete as seguintes ações:  
    * Selecione **NFS** como o tipo de protocolo para o volume.   
    * Especifique a trajetória de **ficheiro** que será utilizada para criar a rota de exportação para o novo volume. O caminho de exportação é usado para montar e aceder ao volume.

        O nome de ficheiro pode conter apenas letras, números e hífenes ("-"). Tem de ter entre 16 e 40 carateres. 

        O caminho dos ficheiros deve ser único dentro de cada subscrição e de cada região. 

    * Selecione a versão NFS **(NFSv3** ou **NFSv4.1**) para o volume.  
    * Opcionalmente, configure a política de [exportação para o volume NFS](azure-netapp-files-configure-export-policy.md).

    ![Especificar protocolo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Clique em **Rever + Criar** para rever os detalhes do volume.  Em seguida, clique em **Criar** para criar o volume NFS.

    O volume que criou aparece na página Volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.


## <a name="next-steps"></a>Passos seguintes  

* [Configurar o domínio predefinido do NFSv 4.1 para o Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurar a política de exportação para um volume NFS](azure-netapp-files-configure-export-policy.md)
* [Limites de recurso para os Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Conheça a integração de redes virtuais para serviços Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
