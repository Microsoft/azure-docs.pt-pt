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
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 3cd60f390f0233e2923660fc39675b5a307d8d8f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515429"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files dá suporte a volumes NFS e SMBv3. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. Este artigo mostra como criar um volume SMBv3. Se você quiser criar um volume de NFS, consulte [criar um volume de NFS para Azure NetApp files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Antes de começar 
Tem de ter um conjunto de capacidade já configurado.   
[Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada para Azure NetApp Files.  
[Delegar uma sub-rede a Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisitos para conexões de Active Directory

 Você precisa criar conexões Active Directory antes de criar um volume SMB. Os requisitos para conexões de Active Directory são os seguintes: 

* A conta de administrador que você usa deve ser capaz de criar contas de computador no caminho da UO (unidade organizacional) que você especificar.  

* As portas adequadas devem estar abertas no servidor Windows Active Directory (AD) aplicável.  
    As portas necessárias são as seguintes: 

    |     Serviço           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    Serviços Web do AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    Resposta de eco    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nome NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    Secure LDAP        |    636       |    TCP           |
    |    Secure LDAP        |    3269      |    TCP           |
    |    W32Time            |    123       |    UDP           |

* A topologia do site para o Active Directory Domain Services de destino deve aderir às práticas recomendadas, em particular a VNet do Azure onde Azure NetApp Files é implantado.  

    O espaço de endereço para a rede virtual em que Azure NetApp Files está implantado deve ser adicionado a um site de Active Directory novo ou existente (no qual um controlador de domínio acessível pelo Azure NetApp Files reside). 

* Os servidores DNS especificados devem estar acessíveis a partir da [sub-rede delegada](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) do Azure NetApp files.  

    Consulte as [diretrizes para Azure NetApp files planejamento de rede](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para topologias de rede com suporte.

    Os NSGs (grupos de segurança de rede) e os firewalls devem ter regras configuradas adequadamente para permitir solicitações de tráfego de Active Directory e DNS.

    Consulte [projetando a topologia do site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sobre serviços e sites do AD. 

## <a name="create-an-active-directory-connection"></a>Criar uma conexão Active Directory

1. Em sua conta do NetApp, clique em **Active Directory conexões**eclique em ingressar.  

    ![Conexões Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela ingressar Active Directory, forneça as seguintes informações:

    * **DNS primário**  
        Esse é o DNS necessário para as operações de autenticação SMB e de ingresso no domínio do Active Directory. 
    * **DNS secundário**   
        Esse é o servidor DNS secundário para garantir que os serviços de nome redundantes. 
    * **Domínio**  
        Esse é o nome de domínio de seu Active Directory Domain Services que você deseja unir.
    * **Prefixo do servidor SMB (conta do computador)**  
        Esse é o prefixo de nomenclatura para a conta do computador no Active Directory que Azure NetApp Files será usado para a criação de novas contas.

        Por exemplo, se o padrão de nomenclatura que sua organização usa para servidores de arquivos for NAS-01, NAS-02..., NAS-045, você digitaria "NAS" para o prefixo. 

        O serviço criará contas de computador adicionais no Active Directory conforme necessário.

    * **Caminho da unidade organizacional**  
        Esse é o caminho LDAP para a UO (unidade organizacional) em que as contas de máquina do servidor SMB serão criadas. Ou seja, OU = segundo nível, OU = primeiro nível. 
    * Credenciais, incluindo seu **nome de usuário** e **senha**

    ![Ingressar Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Clique em **Associar**.  

    A conexão Active Directory que você criou é exibida.

    ![Conexões Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Adicionar um volume SMB

1. Clique na folha **volumes** da folha pools de capacidade. 

    ![Navegar até volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    A janela criar um volume é exibida.

3. Na janela criar um volume, clique em **criar** e forneça informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome do volume que está a criar.   

        Um nome de volume deve ser exclusivo em cada pool de capacidade. Deve ter pelo menos três caracteres de comprimento. Você pode usar qualquer caractere alfanumérico.

    * **Pool de capacidade**  
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
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **criar nova** na página criar um volume. Em seguida, na página criar sub-rede, especifique as informações de sub-rede e selecione **Microsoft. NetApp/volumes** para delegar a sub-rede para Azure NetApp files. Em cada VNet, somente uma sub-rede pode ser delegada para Azure NetApp Files.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique em **protocolo** e preencha as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione a conexão **Active Directory** na lista suspensa.
    * Especifique o nome do volume compartilhado no **nome do compartilhamento**.

    ![Especificar protocolo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique em **examinar + criar** para examinar os detalhes do volume.  Em seguida, clique em **criar** para criar o volume SMB.

    O volume que você criou aparece na página volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="next-steps"></a>Passos Seguintes  

* [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [Perguntas frequentes sobre o SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Saiba mais sobre a integração de rede virtual para serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instalar uma nova floresta Active Directory usando CLI do Azure](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
