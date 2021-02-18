---
title: Criar um volume SMB para ficheiros Azure NetApp | Microsoft Docs
description: Este artigo mostra-lhe como criar um volume SMB3 em Ficheiros Azure NetApp. Saiba mais sobre os requisitos para ligações de Diretório Ativo e Serviços de Domínio.
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
ms.topic: how-to
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 91f4f90658281282cdcb01b091bd9c9647d8d702
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635494"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files suporta a criação de volumes utilizando NFSv3 (NFSv3 e NFSv4.1), SMB3 ou protocolo duplo (NFSv3 e SMB). O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. Este artigo mostra-lhe como criar um volume SMB3.

## <a name="before-you-begin"></a>Antes de começar 

* Tem de ter um conjunto de capacidade já configurado. Consulte [configurar uma piscina de capacidade.](azure-netapp-files-set-up-capacity-pool.md)     
* Uma sub-rede deve ser delegada nos Ficheiros Azure NetApp. Consulte [a sub-rede de delegados aos ficheiros Azure NetApp](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Configurar ligações de Diretório Ativo 

Antes de criar um volume SMB, é necessário criar uma ligação Ative Directory. Se não tiver configurado ligações ative directory para ficheiros Azure NetApp, siga as instruções descritas na [Criação e gere as ligações ative directory](create-active-directory-connections.md).

## <a name="add-an-smb-volume"></a>Adicione um volume SMB

1. Clique na lâmina **volumes** da lâmina Pools de Capacidade. 

    ![Navegar para volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    Aparece a janela Criar um Volume.

3. Na janela Criar um Volume, clique em **Criar** e fornecer informações para os seguintes campos no separador Básicos:   
    * **Nome do volume**      
        Especifique o nome do volume que está a criar.   

        Um nome de volume deve ser único dentro de cada piscina de capacidade. Deve ter pelo menos três caracteres de comprimento. Pode usar qualquer caracteres alfanuméricos.   

        Não pode usar `default` ou como nome de `bin` volume.

    * **Piscina de capacidade**  
        Especifique o pool de capacidade onde deseja que o volume seja criado.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.  

    * **Produção (MiB/S)**   
        Se o volume for criado num pool manual de capacidade QoS, especifique a produção desejada para o volume.   

        Se o volume for criado num pool de capacidade auto QoS, o valor apresentado neste campo é (rendimento de nível de serviço quota x).   

    * **Rede virtual**  
        Especifique a rede virtual Azure (VNet) a partir da qual pretende aceder ao volume.  

        O VNet que especifique deve ter uma sub-rede delegada nos Ficheiros Azure NetApp. O serviço Azure NetApp Files só pode ser acedido a partir do mesmo VNet ou de um VNet que se encontra na mesma região que o volume através do espremiamento VNet. Também pode aceder ao volume a partir da sua rede no local através da Rota Expresso.   

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especificou deve ser delegada nos Ficheiros Azure NetApp. 
        
        Se não tiver delegado uma sub-rede, pode clicar em **Criar novo** na página Criar um Volume. Em seguida, na página 'Criar Sub-redes', especificar as informações da sub-rede e selecionar **o Microsoft.NetApp/volumes** para delegar a sub-rede para ficheiros Azure NetApp. Em cada VNet, apenas uma sub-rede pode ser delegada nos Ficheiros Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se pretender aplicar uma política de instantâneo existente no volume, clique em **Mostrar secção avançada** para expandi-la, especificar se pretende ocultar o caminho do instantâneo e selecionar uma política de instantâneo no menu pull-down. 

        Para obter informações sobre a criação de uma política de instantâneo, consulte [Gerir as políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostrar seleção avançada](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Clique **em Protocolo** e complete as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione a sua ligação **Ative Directory** a partir da lista de drop-down.
    * Especificar o nome do volume partilhado no  **nome Share**.

    ![Especificar o protocolo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique **em 'Rever + Criar'** para rever os detalhes do volume.  Em seguida, clique em **Criar** para criar o volume SMB.

    O volume que criou aparece na página Volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="control-access-to-an-smb-volume"></a>Controlar o acesso a um volume SMB  

O acesso a um volume SMB é gerido através de permissões.  

### <a name="share-permissions"></a>Partilhar permissões  

Por predefinição, um novo volume tem as permissões de partilha **De Todos /Controlo Total.** Os membros do grupo Domain Admins podem alterar as permissões de partilha utilizando a Gestão de Computadores na conta de computador que é utilizada para o volume de Ficheiros Azure NetApp.

![SMB caminho de montagem ](../media/azure-netapp-files/smb-mount-path.png) 
 ![ Definir permissões de partilha](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Permissões de ficheiros e pastas NTFS  

Pode definir permissões para um ficheiro ou pasta utilizando o separador **Segurança** das propriedades do objeto no cliente Windows SMB.
 
![Definir permissões de ficheiros e pastas](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Passos seguintes  

* [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [FAQs sobre o SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [SMB de resolução de problemas ou volumes de duplo protocolo](troubleshoot-dual-protocol-volumes.md)
* [Conheça a integração de redes virtuais para serviços Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Instale uma nova floresta de Diretório Ativo utilizando o Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
