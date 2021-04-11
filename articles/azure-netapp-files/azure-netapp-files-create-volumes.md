---
title: Crie um volume NFS para ficheiros Azure NetApp | Microsoft Docs
description: Este artigo mostra-lhe como criar um volume NFS em Ficheiros Azure NetApp. Conheça as considerações, como qual a versão a usar e as melhores práticas.
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
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 3b6104bcf68c720fa727d16e408a25adcba805aa
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259579"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Criar um volume NFS para o Azure NetApp Files

O Azure NetApp Files suporta a criação de volumes utilizando NFSv3 (NFSv3 e NFSv4.1), SMB3 ou protocolo duplo (NFSv3 e SMB). O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. 

Este artigo mostra-lhe como criar um volume NFS. Para volumes SMB, consulte [Criar um volume SMB](azure-netapp-files-create-volumes-smb.md). Para volumes de duplo protocolo, consulte [Criar um volume de duplo protocolo](create-volumes-dual-protocol.md).

## <a name="before-you-begin"></a>Antes de começar 
* Tem de ter um conjunto de capacidade já configurado.  
    Consulte [configurar uma piscina de capacidade.](azure-netapp-files-set-up-capacity-pool.md)   
* Uma sub-rede deve ser delegada nos Ficheiros Azure NetApp.  
    Consulte [a sub-rede de delegados aos ficheiros Azure NetApp](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considerações 

* Decidir qual versão NFS a utilizar  
  O NFSv3 pode lidar com uma grande variedade de casos de uso e é geralmente implementado na maioria das aplicações empresariais. Deve validar a versão (NFSv3 ou NFSv4.1) que a sua aplicação necessita e criar o seu volume utilizando a versão adequada. Por exemplo, se utilizar [o Apache ActiveMQ,](https://activemq.apache.org/shared-file-system-master-slave)recomenda-se o bloqueio de ficheiros com NFSv4.1 sobre o NFSv3. 

* Segurança  
  O suporte para bits de modo UNIX (ler, escrever e executar) está disponível para NFSv3 e NFSv4.1. É necessário aceder ao nível da raiz no cliente NFS para montar volumes NFS.

* Suporte local de utilizador/grupo e LDAP para NFSv4.1  
  Atualmente, o NFSv4.1 suporta apenas o acesso à raiz aos volumes. Consulte [o domínio padrão NFSv4.1 para ficheiros Azure NetApp](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Melhores práticas

* Certifique-se de que está a utilizar as instruções de montagem adequadas para o volume.  Consulte [o Mount ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* O cliente NFS deve estar no mesmo VNet ou VNet esprevado que o volume de Ficheiros Azure NetApp. A ligação a partir de fora do VNet é suportada; no entanto, introduzirá latência adicional e diminuirá o desempenho global.

* Certifique-se de que o cliente NFS está atualizado e executa as últimas atualizações para o sistema operativo.

## <a name="create-an-nfs-volume"></a>Criar um volume NFS

1.  Clique na lâmina **volumes** da lâmina Pools de Capacidade. Clique em **+ Adicionar volume** para criar um volume. 

    ![Navegar para volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Na janela Criar um Volume, clique em **Criar** e fornecer informações para os seguintes campos no separador Básicos:   
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

        O Vnet que especifica deve ter uma sub-rede delegada nos Ficheiros Azure NetApp. O serviço Azure NetApp Files só pode ser acedido a partir do mesmo Vnet ou de um Vnet que está na mesma região que o volume através do espreitamento Vnet. Também pode aceder ao volume a partir da sua rede no local através da Rota Expresso.   

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especificou deve ser delegada nos Ficheiros Azure NetApp. 
        
        Se não tiver delegado uma sub-rede, pode clicar em **Criar uma** página de Volume. Em seguida, na página 'Criar Sub-redes', especificar as informações da sub-rede e selecionar **o Microsoft.NetApp/volumes** para delegar a sub-rede para ficheiros Azure NetApp. Em cada Vnet, apenas uma sub-rede pode ser delegada nos Ficheiros Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se pretender aplicar uma política de instantâneo existente no volume, clique em **Mostrar secção avançada** para expandi-la, especificar se pretende ocultar o caminho do instantâneo e selecionar uma política de instantâneo no menu pull-down. 

        Para obter informações sobre a criação de uma política de instantâneo, consulte [Gerir as políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostrar seleção avançada](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Clique **em Protocolo** e, em seguida, complete as seguintes ações:  
    * Selecione **NFS** como o tipo de protocolo para o volume.   
    * Especifique o caminho do **ficheiro** que será usado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e aceder ao volume.

        O nome de ficheiro pode conter apenas letras, números e hífenes ("-"). Tem de ter entre 16 e 40 carateres. 

        O percurso do ficheiro deve ser único em cada subscrição e em cada região. 

    * Selecione a versão NFS **(NFSv3** ou **NFSv4.1**) para o volume.  

    * Se estiver a utilizar o NFSv4.1, indique se pretende ativar a encriptação **Kerberos** para o volume.  

        São necessárias configurações adicionais se utilizar Kerberos com NFSv4.1. Siga as instruções na [encriptação Configure NFSv4.1 Kerberos](configure-kerberos-encryption.md).

    * Se pretender capacitar os utilizadores do Diretório Ativo LDAP e grupos alargados (até 1024 grupos) para aceder ao volume, selecione a opção **LDAP.** Siga as instruções em [Configure ADDS LDAP com grupos alargados para acesso ao volume NFS](configure-ldap-extended-groups.md) para completar as configurações necessárias. 
 
    * Opcionalmente, [configurar a política de exportação para o volume NFS](azure-netapp-files-configure-export-policy.md).

    ![Especificar o protocolo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. Clique **em 'Rever + Criar'** para rever os detalhes do volume.  Em seguida, clique em **Criar** para criar o volume.

    O volume que criou aparece na página Volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.


## <a name="next-steps"></a>Passos seguintes  

* [Configurar o domínio predefinido do NFSv 4.1 para o Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Configurar a encriptação Kerberos NFSv4.1](configure-kerberos-encryption.md)
* [Configure ADDS LDAP com grupos alargados para acesso ao volume NFS](configure-ldap-extended-groups.md)
* [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurar a política de exportação para um volume NFS](azure-netapp-files-configure-export-policy.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [Conheça a integração de redes virtuais para serviços Azure](../virtual-network/virtual-network-for-azure-services.md)