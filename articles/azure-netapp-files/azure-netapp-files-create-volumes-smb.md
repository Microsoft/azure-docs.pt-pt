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
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: eeeaf01dd20e5b309884a01f954ceca576cbcbb9
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259630"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files suporta a criação de volumes utilizando NFSv3 (NFSv3 e NFSv4.1), SMB3 ou protocolo duplo (NFSv3 e SMB). O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. 

Este artigo mostra-lhe como criar um volume SMB3. Para volumes NFS, consulte [Criar um volume NFS](azure-netapp-files-create-volumes.md). Para volumes de duplo protocolo, consulte [Criar um volume de duplo protocolo](create-volumes-dual-protocol.md).

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
    * Se pretender ativar a Disponibilidade Contínua para o volume SMB, selecione **Ativar a Disponibilidade Contínua**.    

        > [!IMPORTANT]   
        > A funcionalidade disponibilidade contínua SMB encontra-se atualmente em pré-visualização pública. Tem de submeter um pedido de lista de espera para aceder à funcionalidade através da página de submissão da lista de espera de **[pré-visualização de Azure NetApp.](https://aka.ms/anfsmbcasharespreviewsignup)** Aguarde um e-mail oficial de confirmação da equipa do Azure NetApp Files antes de utilizar a funcionalidade Disponibilidade Contínua.   
        > 
        > Deve ativar a Disponibilidade Contínua apenas para cargas de trabalho SQL. A utilização de ações de disponibilidade contínua SMB para cargas de trabalho que não o SQL Server *não* é suportada. Esta funcionalidade é suportada atualmente no Windows SQL Server. O Linux SQL Server não está atualmente suportado. Se estiver a utilizar uma conta não administrador (domínio) para instalar o SQL Server, certifique-se de que a conta tem o privilégio de segurança necessário atribuído. Se a conta de domínio não tiver o privilégio de segurança exigido ( `SeSecurityPrivilege` ), e o privilégio não puder ser definido ao nível do domínio, pode conceder o privilégio à conta utilizando o campo de **privilégios** de Segurança das ligações Ative Directory. Ver [Criar uma ligação ative directory](create-active-directory-connections.md#create-an-active-directory-connection).

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![Screenshot que descreve o separador Protocolo de criação de um volume SMB.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique **em 'Rever + Criar'** para rever os detalhes do volume.  Em seguida, clique em **Criar** para criar o volume SMB.

    O volume que criou aparece na página Volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="control-access-to-an-smb-volume"></a>Controlar o acesso a um volume SMB  

O acesso a um volume SMB é gerido através de permissões.  

### <a name="share-permissions"></a>Partilhar permissões  

Por predefinição, um novo volume tem as permissões de partilha **De Todos /Controlo Total.** Os membros do grupo Domain Admins podem alterar as permissões de partilha da seguinte forma:  

1. Mapear a partilha para uma unidade.  
2. Clique com o botão direito na unidade, selecione **Propriedades** e, em seguida, vá para o **separador Segurança.**

[![Definir permissões de partilha](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

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
