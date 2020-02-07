---
title: Criar um volume SMB para Azure NetApp Files | Microsoft Docs
description: Descreve como criar um volume SMB para Azure NetApp Files.
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
ms.openlocfilehash: c65da771dd483b3a79785d4bec2b89cbeefca5c4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049894"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files dá suporte a volumes NFS e SMBv3. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. Este artigo mostra como criar um volume SMBv3. Se quiser criar um volume NFS, consulte [Criar um volume NFS para Ficheiros Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Antes de começar 
Tem de ter um conjunto de capacidade já configurado.   
[Criar uma piscina de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada para Azure NetApp Files.  
[Delege uma subnet para ficheiros Azure NetApp](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisitos para conexões de Active Directory

 Você precisa criar conexões Active Directory antes de criar um volume SMB. Os requisitos para conexões de Active Directory são os seguintes: 

* A conta de administrador que você usa deve ser capaz de criar contas de computador no caminho da UO (unidade organizacional) que você especificar.  

* As portas adequadas devem estar abertas no servidor Windows Active Directory (AD) aplicável.  
    As portas necessárias são as seguintes: 

    |     Serviço           |     Porta     |     Protocolo     |
    |-----------------------|--------------|------------------|
    |    Serviços Web do AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Resposta de eco    |
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
    |    W32Time            |    123       |    UDP           |

* A topologia do site para o Active Directory Domain Services de destino deve aderir às práticas recomendadas, em particular a VNet do Azure onde Azure NetApp Files é implantado.  

    O espaço de endereço para a rede virtual em que Azure NetApp Files está implantado deve ser adicionado a um site de Active Directory novo ou existente (no qual um controlador de domínio acessível pelo Azure NetApp Files reside). 

* Os servidores DNS especificados devem ser acessíveis a partir da [subnet delegada](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) dos Ficheiros Azure NetApp.  

    Consulte as Diretrizes para o planeamento da [rede Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para topoologias de rede suportadas.

    Os NSGs (grupos de segurança de rede) e os firewalls devem ter regras configuradas adequadamente para permitir solicitações de tráfego de Active Directory e DNS. 

* O Azure NetApp Files sub-rede delegada deve ser capaz de alcançar todos os controladores de domínio de Active Directory Domain Services (ADDS) no domínio, incluindo todos os controladores de domínio locais e remotos. Caso contrário, pode ocorrer interrupção do serviço.  

    Se tiver controladores de domínio inacessíveis através da subnet delegada do Azure Net Files, pode submeter um pedido de suporte Azure para alterar o âmbito de aplicação do **global** (padrão) para o **site**.  Azure NetApp Files precisa se comunicar somente com controladores de domínio no site em que reside o espaço de endereço de sub-rede delegado Azure NetApp Files.

    Consulte [a designação da topologia do site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sobre sites e serviços de AD. 
    
Consulte o Azure NetApp Files [SMB FAQs](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) sobre informações adicionais de Anúncio. 

## <a name="create-an-active-directory-connection"></a>Criar uma conexão Active Directory

1. Na sua conta NetApp, clique em ligações de **Diretório Ativo**e, em seguida, clique em **Juntar**.  

    ![Conexões Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela ingressar Active Directory, forneça as seguintes informações:

    * **DNS primários**  
        Esse é o DNS necessário para as operações de autenticação SMB e de ingresso no domínio do Active Directory. 
    * **  secundária do DNS**  
        Esse é o servidor DNS secundário para garantir que os serviços de nome redundantes. 
    * **Domínio**  
        Esse é o nome de domínio de seu Active Directory Domain Services que você deseja unir.
    * **Prefixo do servidor SMB (conta de computador)**  
        Esse é o prefixo de nomenclatura para a conta do computador no Active Directory que Azure NetApp Files será usado para a criação de novas contas.

        Por exemplo, se o padrão de nomenclatura que sua organização usa para servidores de arquivos for NAS-01, NAS-02..., NAS-045, você digitaria "NAS" para o prefixo. 

        O serviço criará contas de computador adicionais no Active Directory conforme necessário.

    * **Caminho de unidade organizacional**  
        Esse é o caminho LDAP para a UO (unidade organizacional) em que as contas de máquina do servidor SMB serão criadas. Ou seja, OU = segundo nível, OU = primeiro nível. 

        Se estiver a utilizar ficheiros Azure NetApp com serviços de domínio de diretório ativo Azure, o percurso da unidade organizacional é `OU=AADDC Computers` quando configura o Diretório Ativo para a sua conta NetApp.
        
    * Credenciais, incluindo o seu **nome de utilizador** e **senha**

    ![Ingressar Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Clique em **Associar**.  

    A conexão Active Directory que você criou é exibida.

    ![Conexões Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Você pode editar os campos de nome de usuário e senha depois de salvar a conexão de Active Directory. Nenhum outro valor pode ser editado depois de salvar a conexão. Se precisar alterar outros valores, primeiro exclua todos os volumes SMB implantados e exclua e recrie a conexão de Active Directory.

## <a name="add-an-smb-volume"></a>Adicionar um volume SMB

1. Clique na lâmina **Volumes** a partir da lâmina De Capacidade Pools. 

    ![Navegar até volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    A janela criar um volume é exibida.

3. Na janela Criar uma janela de Volume, clique em **Criar** e fornecer informações para os seguintes campos:   
    * **Nome de volume**      
        Especifique o nome do volume que está a criar.   

        Um nome de volume deve ser exclusivo em cada pool de capacidade. Deve ter pelo menos três caracteres de comprimento. Você pode usar qualquer caractere alfanumérico.   

        Não pode usar `default` como nome de volume.

    * **Piscina de capacidade**  
        Especifique o pool de capacidade no qual você deseja que o volume seja criado.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual do Azure (VNet) da qual você deseja acessar o volume.  

        A VNet que você especificar deve ter uma sub-rede delegada para Azure NetApp Files. O serviço de Azure NetApp Files pode ser acessado somente da mesma VNet ou de uma VNet que está na mesma região que o volume por meio de emparelhamento VNet. Você também pode acessar o volume de sua rede local por meio da rota expressa.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede que você especificar deve ser delegada para Azure NetApp Files. 
        
        Se não tiver delegado uma sub-rede, pode clicar em **Criar novo** na página Criar um Volume. Em seguida, na página Create Subnet, especifique as informações da subnet e selecione **Microsoft.NetApp/volumes** para delegar a subnet para Ficheiros Azure NetApp. Em cada VNet, somente uma sub-rede pode ser delegada para Azure NetApp Files.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique no **Protocolo** e complete as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione a sua ligação **Ative Directy** a partir da lista de lançamentos.
    * Especifique o nome do volume partilhado no **nome da partilha**.

    ![Especificar protocolo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique em **Rever + Criar** para rever os detalhes do volume.  Em seguida, clique em **Criar** para criar o volume SMB.

    O volume que você criou aparece na página volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="next-steps"></a>Passos seguintes  

* [Monte ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [Perguntas-Gerais SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Conheça a integração de redes virtuais para serviços Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instale uma nova floresta de Diretório Ativo utilizando o Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
