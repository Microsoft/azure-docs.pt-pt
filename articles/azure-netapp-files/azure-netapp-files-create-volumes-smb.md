---
title: Criar um volume SMB para ficheiros Azure NetApp Microsoft Docs
description: Descreve como criar um volume SMB para ficheiros Azure NetApp.
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
ms.date: 05/29/2020
ms.author: b-juche
ms.openlocfilehash: 6bd6ddc8b75b83355f6761ef0567ea949c86b61a
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483708"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files suporta volumes NFS e SMBv3. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. Este artigo mostra-lhe como criar um volume SMBv3. Se pretender criar um volume NFS, consulte [criar um volume NFS para ficheiros Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Antes de começar 
Tem de ter um conjunto de capacidade já configurado.   
[Criar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada nos Ficheiros Azure NetApp.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisitos para ligações de Diretório Ativo

 É necessário criar ligações ative diretoria antes de criar um volume SMB. Os requisitos para ligações de Diretório Ativo são os seguintes: 

* A conta de administração que utiliza deve ter a capacidade de criar contas de máquinas no caminho da unidade organizacional (OU) que irá especificar.  

* As portas adequadas devem estar abertas no servidor do Windows Ative (AD) aplicável.  
    As portas necessárias são as seguintes: 

    |     Serviço           |     Porta     |     Protocolo     |
    |-----------------------|--------------|------------------|
    |    Serviços Web de Anúncios    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Resposta de eco    |
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
    |    w32 vezes            |    123       |    UDP           |

* A topologia do site para os serviços de domínio do diretório ativo direcionados deve aderir às diretrizes, em particular o Azure VNet onde os Ficheiros Azure NetApp são implantados.  

    O espaço de endereço para a rede virtual onde o Azure NetApp Files é implantado deve ser adicionado a um site de Ative Directory novo ou existente (onde um controlador de domínio acessível por Azure NetApp Files é). 

* Os servidores DNS especificados devem ser alcançáveis a partir da [sub-rede delegada](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) dos Ficheiros Azure NetApp.  

    Consulte [as Diretrizes para o planeamento da rede Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para as topologias de rede suportadas.

    Os Grupos de Segurança da Rede (NSGs) e as firewalls devem ter regras adequadamente configuradas para permitir pedidos de tráfego de Diretório Ativo e DNS. 

* A sub-rede delegada pelos Ficheiros Azure NetApp deve ser capaz de chegar a todos os controladores de domínio do Ative Directory Domain Services (ADDS) no domínio, incluindo todos os controladores de domínio locais e remotos. Caso contrário, pode ocorrer uma interrupção de serviço.  

    Se tiver controladores de domínio inacessíveis pela sub-rede delegada do Azure NetApp Files, pode especificar um site de Diretório Ativo durante a criação da ligação Ative Directory.  O Azure NetApp Files precisa de comunicar apenas com controladores de domínio no site onde se encontra o espaço de endereço de sub-redes Azure NetApp.

    Consulte [a topologia do site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sobre sites e serviços de AD. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers Microsoft Management Console (MMC):   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Consulte O Azure NetApp Files [SMB FAQs](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) sobre informações adicionais de AD. 

## <a name="decide-which-domain-services-to-use"></a>Decidir quais os Serviços de Domínio a utilizar 

O Azure NetApp Files suporta serviços [de domínio de diretório ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) e serviços de domínio de diretório ativo (AADDS) para ligações AD.  Antes de criar uma ligação AD, tem de decidir se utiliza ADDS ou AADDS.  

Para obter mais informações, consulte [Compare os Serviços de Domínio do Diretório Ativo auto-geridos, o Azure Ative Directory e os serviços de domínio do Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions) 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Pode utilizar o seu âmbito de serviço ativo preferido para [ficheiros](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) Azure NetApp. Esta opção permite leituras e escritas para controladores de domínio de Diretório Ativo (ADDS) que são [acessíveis por Ficheiros Azure NetApp](azure-netapp-files-network-topologies.md). Também impede que o serviço comunique com controladores de domínio que não estejam no site de Sites e Serviços de Diretório Ativo especificado. 

Para encontrar o nome do seu site quando utilizar o ADDS, pode contactar o grupo administrativo da sua organização responsável pelos Serviços de Domínio do Diretório Ativo. O exemplo abaixo mostra o plugin de Sites e Serviços de Diretório Ativo onde o nome do site é apresentado: 

![Serviços e Sites do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Quando configurar uma ligação AD para ficheiros Azure NetApp, especifique o nome do site no âmbito do campo **Nome do Site da AD.**

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Para configuração e orientação dos Serviços de Domínio do Diretório Ativo Azure (AADDS), consulte [a documentação dos Serviços de Domínio AD Azure](https://docs.microsoft.com/azure/active-directory-domain-services/).

Considerações adicionais da AADDS aplicam-se aos ficheiros Azure NetApp: 

* Certifique-se de que o VNet ou sub-rede onde o AADDS é implantado está na mesma região Azure que a implementação dos Ficheiros Azure NetApp.
* Se utilizar outro VNet na região onde o Azure NetApp Files é implantado, deverá criar um espremiamento entre os dois VNets.
* Azure NetApp Files suporta `user` e `resource forest` tipos.
* Para o tipo de sincronização, pode selecionar `All` ou `Scoped` .   
    Se `Scoped` selecionar, certifique-se de que o grupo Azure AD correto está selecionado para aceder a ações SMB.  Se tiver dúvidas, pode utilizar o `All` tipo de sincronização.
* É necessária a utilização da Enterprise ou Premium SKU. O SKU Standard não é suportado.

Quando criar uma ligação Ative Directory, note as seguintes especificidades para AADDS:

* Pode encontrar informações para **DNS Primários,** **DNS Secundários**e **Nome de Domínio AD DNS** no menu AADDS.  
Para os servidores DNS, serão utilizados dois endereços IP para configurar a ligação Ative Directory. 
* O **percurso da unidade organizacional** `OU=AADDC Computers` é.  
Esta definição está configurada nas Ligações de **Diretório Ativo** na **Conta NetApp:**

  ![Percurso da unidade organizacional](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **As** credenciais de nome de utilizador podem ser qualquer utilizador que seja membro dos administradores do grupo **Azure AD Azure.**


## <a name="create-an-active-directory-connection"></a>Criar uma ligação ative directy

1. A partir da sua conta NetApp, clique em **ligações de Ative Directory**e, em seguida, clique em **Juntar-se**.  

    ![Conexões de diretório ativo](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela 'Unir Diretório Activo', forneça as seguintes informações, com base nos Serviços de Domínio que pretende utilizar:  

    Para obter informações específicas dos Serviços de Domínio que utiliza, consulte [decidir quais os Serviços de Domínio a utilizar](#decide-which-domain-services-to-use). 

    * **DNS primários**  
        Este é o DNS que é necessário para as operações de alistamento do domínio do Diretório Ativo e operações de autenticação SMB. 
    * **DNS secundários**   
        Este é o servidor SECUNDÁRIO DNS para garantir serviços de nomes redundantes. 
    * **Nome de domínio de DNS de anúncio**  
        Este é o nome de domínio dos seus Serviços de Domínio de Diretório Ativo a que pretende aderir.
    * **Nome do site de anúncios**  
        Este é o nome do site a que a descoberta do Controlador de Domínio será limitada.
    * **Prefixo do servidor SMB (conta de computador)**  
        Este é o prefixo de nomeação para a conta de máquina no Ative Directory que o Azure NetApp Files utilizará para a criação de novas contas.

        Por exemplo, se o padrão de nomeação que a sua organização usa para servidores de ficheiros for NAS-01, NAS-02..., NAS-045, então você introduziria "NAS" para o prefixo. 

        O serviço criará contas de máquinas adicionais no Ative Directory, se necessário.

        > [!IMPORTANT] 
        > Renomear o prefixo do servidor SMB depois de criar a ligação Ative Directory é disruptivo. Terá de voltar a montar as ações SMB existentes depois de renomear o prefixo do servidor SMB.

    * **Percurso da unidade organizacional**  
        Este é o caminho LDAP para a unidade organizacional (OU) onde serão criadas contas de máquinas de servidor SMB. Ou=segundo nível, OU=primeiro nível. 

        Se estiver a utilizar ficheiros Azure NetApp com Serviços de Domínio do Diretório Ativo Azure, o percurso da unidade organizacional é `OU=AADDC Computers` quando configura o Ative Directory para a sua conta NetApp.

     * **Utilizadores de política de backup**  
        Pode incluir contas adicionais que requerem privilégios elevados à conta de computador criada para utilização com ficheiros Azure NetApp. As contas especificadas serão autorizadas a alterar as permissões NTFS ao nível do ficheiro ou da pasta. Por exemplo, pode especificar uma conta de serviço não privilegiada utilizada para a migração de dados para uma partilha de ficheiros SMB em Ficheiros Azure NetApp.  

        > [!IMPORTANT] 
        > A utilização da funcionalidade de utilização da política de backup requer whitelisting. Envie um e-mail anffeedback@microsoft.com com o seu ID de subscrição para solicitar esta funcionalidade. 

    * Credenciais, incluindo o seu **nome de utilizador** e **senha**

    ![Junte-se ao Diretório Ativo](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Clique em **Associar**.  

    A ligação Ative Directory que criou aparece.

    ![Conexões de diretório ativo](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Adicione um volume SMB

1. Clique na lâmina **volumes** da lâmina Pools de Capacidade. 

    ![Navegar para volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    Aparece a janela Criar um Volume.

3. Na janela Criar um Volume, clique em **Criar** e fornecer informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome do volume que está a criar.   

        Um nome de volume deve ser único dentro de cada piscina de capacidade. Deve ter pelo menos três caracteres de comprimento. Pode usar qualquer caracteres alfanuméricos.   

        Não pode usar `default` como nome de volume.

    * **Piscina de capacidade**  
        Especifique o pool de capacidade onde deseja que o volume seja criado.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual Azure (VNet) a partir da qual pretende aceder ao volume.  

        O VNet que especifique deve ter uma sub-rede delegada nos Ficheiros Azure NetApp. O serviço Azure NetApp Files só pode ser acedido a partir do mesmo VNet ou de um VNet que se encontra na mesma região que o volume através do espremiamento VNet. Também pode aceder ao volume a partir da sua rede no local através da Rota Expresso.   

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especificou deve ser delegada nos Ficheiros Azure NetApp. 
        
        Se não tiver delegado uma sub-rede, pode clicar em **Criar novo** na página Criar um Volume. Em seguida, na página 'Criar Sub-redes', especificar as informações da sub-rede e selecionar **o Microsoft.NetApp/volumes** para delegar a sub-rede para ficheiros Azure NetApp. Em cada VNet, apenas uma sub-rede pode ser delegada nos Ficheiros Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique **em Protocolo** e complete as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione a sua ligação **Ative Directory** a partir da lista de drop-down.
    * Especificar o nome do volume partilhado no **nome Share**.

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
* [FAQs sobre o SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Conheça a integração de redes virtuais para serviços Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instale uma nova floresta de Diretório Ativo utilizando o Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
