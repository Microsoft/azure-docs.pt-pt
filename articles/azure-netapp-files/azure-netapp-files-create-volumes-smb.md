---
title: Criar um volume SMB para Ficheiros Azure NetApp  Microsoft Docs
description: Descreve como criar um volume SMB para Ficheiros Azure NetApp.
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
ms.date: 02/05/2020
ms.author: b-juche
ms.openlocfilehash: 7affd408ce2471f34a8362ba32101b639aafc514
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586615"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files suporta volumes NFS e SMBv3. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. Este artigo mostra-lhe como criar um volume SMBv3. Se quiser criar um volume NFS, consulte [Criar um volume NFS para Ficheiros Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Antes de começar 
Tem de ter um conjunto de capacidade já configurado.   
[Criar uma piscina de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada nos Ficheiros Azure NetApp.  
[Delege uma subnet para ficheiros Azure NetApp](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisitos para ligações de Diretório Ativo

 Você precisa criar conexões de Diretório Ativo antes de criar um volume SMB. Os requisitos para as ligações de Diretório Ativo são os seguintes: 

* A conta de administração que utiliza deve ser capaz de criar contas de máquinas no caminho da unidade organizacional (OU) que irá especificar.  

* As portas adequadas devem estar abertas no servidor de Diretório Ativo do Windows (AD) aplicável.  
    As portas necessárias são as seguintes: 

    |     Serviço           |     Porta     |     Protocolo     |
    |-----------------------|--------------|------------------|
    |    Serviços Web aD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Resposta eco    |
    |    Rio Kerberos           |    464       |    TCP           |
    |    Rio Kerberos           |    464       |    UDP           |
    |    Rio Kerberos           |    88        |    TCP           |
    |    Rio Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nome NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* A topologia do site para os Serviços de Domínio de Diretório Ativo direcionados deve aderir às melhores práticas, em particular o Azure VNet onde os Ficheiros Azure NetApp são implementados.  

    O espaço de endereço para a rede virtual onde o Azure NetApp Files é implementado deve ser adicionado a um novo ou existente site de Diretório Ativo (onde reside um controlador de domínio acessível por Ficheiros Azure NetApp). 

* Os servidores DNS especificados devem ser acessíveis a partir da [subnet delegada](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) dos Ficheiros Azure NetApp.  

    Consulte as Diretrizes para o planeamento da [rede Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para topoologias de rede suportadas.

    Os Grupos de Segurança da Rede (NSGs) e firewalls devem ter regras devidamente configuradas para permitir pedidos de tráfego de Diretório Ativo e DNS. 

* A sub-rede de ficheiros de Ficheiros Azure Net deve ser capaz de alcançar todos os controladores de domínio de domínio de diretório ativo (ADDS) no domínio, incluindo todos os controladores de domínio locais e remotos. Caso contrário, pode ocorrer uma interrupção do serviço.  

    Se tiver controladores de domínio inacessíveis através da subnet delegada do Azure Net Files, pode especificar um site de Diretório Ativo durante a criação da ligação Ative Directory.  O Azure NetApp Files precisa de comunicar apenas com controladores de domínio no site onde reside o espaço de endereço seleto do Azure NetApp Files.

    Consulte [a designação da topologia do site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sobre sites e serviços de AD. 
    
Consulte o Azure NetApp Files [SMB FAQs](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) sobre informações adicionais de Anúncio. 

## <a name="create-an-active-directory-connection"></a>Criar uma ligação de Diretório Ativo

1. Na sua conta NetApp, clique em ligações de **Diretório Ativo**e, em seguida, clique em **Juntar**.  

    ![Conexões de Diretório Ativo](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela Diretório Ativa de Adesão, forneça as seguintes informações:

    * **DNS primários**  
        Este é o DNS que é necessário para a adesão ao domínio Ative Directy e operações de autenticação SMB. 
    * **  secundária do DNS**  
        Este é o servidor dNS secundário para garantir serviços de nome sancionatório. 
    * **Nome de domínio DNS AD**  
        Este é o nome de domínio dos seus Serviços de Domínio de Diretório Ativo a que pretende aderir.
    * **Nome do site do anúncio**  
        Este é o nome do site a que a descoberta do Controlador de Domínio se limitará.
    * **Prefixo do servidor SMB (conta de computador)**  
        Este é o prefixo de nomeação para a conta de máquina seleção em Diretório Ativo que o Azure NetApp Files utilizará para a criação de novas contas.

        Por exemplo, se o padrão de nomeação que a sua organização usa para servidores de ficheiros for NAS-01, NAS-02..., NAS-045, então você introduziria "NAS" para o prefixo. 

        O serviço criará contas adicionais de máquinas no Diretório Ativo, conforme necessário.

    * **Caminho de unidade organizacional**  
        Este é o caminho LDAP para a unidade organizacional (OU) onde serão criadas as contas da máquina do servidor SMB. Ou=segundo nível, OU=primeiro nível. 

        Se estiver a utilizar ficheiros Azure NetApp com serviços de domínio de diretório ativo Azure, o percurso da unidade organizacional é `OU=AADDC Computers` quando configura o Diretório Ativo para a sua conta NetApp.
        
    * Credenciais, incluindo o seu **nome de utilizador** e **senha**

    ![Junte-se ao Diretório Ativo](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Clique em **Associar**.  

    Aparece a ligação Ative Directy.

    ![Conexões de Diretório Ativo](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Pode editar o nome de utilizador e os campos de palavra-passe depois de guardar a ligação Ative Directory. Nenhum outro valore pode ser editado depois de salvar a ligação. Se precisar de alterar quaisquer outros valores, primeiro deve eliminar quaisquer volumes SMB implantados e, em seguida, eliminar e recriar a ligação ative diretório.

## <a name="add-an-smb-volume"></a>Adicione um volume SMB

1. Clique na lâmina **Volumes** a partir da lâmina De Capacidade Pools. 

    ![Navegar para volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    Aparece a janela Criar um Volume.

3. Na janela Criar uma janela de Volume, clique em **Criar** e fornecer informações para os seguintes campos:   
    * **Nome de volume**      
        Especifique o nome do volume que está a criar.   

        Um nome de volume deve ser único dentro de cada piscina de capacidade. Deve ter pelo menos três caracteres de comprimento. Podes usar qualquer personagem alfanumérico.   

        Não pode usar `default` como nome de volume.

    * **Piscina de capacidade**  
        Especifique o conjunto de capacidades onde pretende que o volume seja criado.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual Azure (VNet) a partir da qual pretende aceder ao volume.  

        O VNet que especifica deve ter uma sub-rede delegada nos Ficheiros Azure NetApp. O serviço Azure NetApp Files só pode ser acedido a partir do mesmo VNet ou de um VNet que se encontra na mesma região que o volume através do peering VNet. Também pode aceder ao volume da sua rede no local através da Rota Expresso.   

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especifica deve ser delegada nos Ficheiros Azure NetApp. 
        
        Se não tiver delegado uma sub-rede, pode clicar em **Criar novo** na página Criar um Volume. Em seguida, na página Create Subnet, especifique as informações da subnet e selecione **Microsoft.NetApp/volumes** para delegar a subnet para Ficheiros Azure NetApp. Em cada VNet, apenas uma subnet pode ser delegada nos Ficheiros Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique no **Protocolo** e complete as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione a sua ligação **Ative Directy** a partir da lista de lançamentos.
    * Especifique o nome do volume partilhado no **nome da partilha**.

    ![Especificar protocolo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique em **Rever + Criar** para rever os detalhes do volume.  Em seguida, clique em **Criar** para criar o volume SMB.

    O volume que criou aparece na página Volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="next-steps"></a>Passos seguintes  

* [Monte ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [Perguntas-Gerais SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Conheça a integração de redes virtuais para serviços Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instale uma nova floresta de Diretório Ativo utilizando o Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
