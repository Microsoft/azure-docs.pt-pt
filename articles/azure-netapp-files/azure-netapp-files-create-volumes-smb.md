---
title: Criar um volume SMB para Ficheiros Azure NetApp [ Microsoft Docs
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
ms.date: 05/19/2020
ms.author: b-juche
ms.openlocfilehash: 6cb3fa56e679bc911f12e99379152fc8e1fb7526
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832838"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files suporta volumes NFS e SMBv3. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. Este artigo mostra-lhe como criar um volume SMBv3. Se quiser criar um volume NFS, consulte [Criar um volume NFS para Ficheiros Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Antes de começar 
Tem de ter um conjunto de capacidade já configurado.   
[Criar uma piscina de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada nos Ficheiros Azure NetApp.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisitos para ligações de Diretório Ativo

 Você precisa criar conexões de Diretório Ativo antes de criar um volume SMB. Os requisitos para as ligações de Diretório Ativo são os seguintes: 

* A conta de administração que utiliza deve ter a capacidade de criar contas de máquinas no caminho da unidade organizacional (OU) que irá especificar.  

* As portas adequadas devem estar abertas no servidor de Diretório Ativo do Windows (AD) aplicável.  
    As portas necessárias são as seguintes: 

    |     Serviço           |     Porta     |     Protocolo     |
    |-----------------------|--------------|------------------|
    |    Serviços Web aD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Resposta eco    |
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
    |    w32time            |    123       |    UDP           |

* A topologia do site para os Serviços de Domínio de Diretório Ativo direcionados deve aderir às diretrizes, em particular o Azure VNet onde os Ficheiros Azure NetApp são implementados.  

    O espaço de endereço para a rede virtual onde o Azure NetApp Files é implementado deve ser adicionado a um novo ou existente site ative diretório (onde um controlador de domínio é acessível por Ficheiros Azure NetApp). 

* Os servidores DNS especificados devem ser acessíveis a partir da [subnet delegada](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) dos Ficheiros Azure NetApp.  

    Consulte as Diretrizes para o planeamento da [rede Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para topoologias de rede suportadas.

    Os Grupos de Segurança da Rede (NSGs) e firewalls devem ter regras devidamente configuradas para permitir pedidos de tráfego de Diretório Ativo e DNS. 

* A sub-rede de ficheiros de Ficheiros Azure Net deve ser capaz de alcançar todos os controladores de domínio de domínio de diretório ativo (ADDS) no domínio, incluindo todos os controladores de domínio locais e remotos. Caso contrário, pode ocorrer uma interrupção do serviço.  

    Se tiver controladores de domínio inacessíveis pela subnet delegada do Azure Net Files, pode especificar um site de Diretório Ativo durante a criação da ligação Ative Directory.  O Azure NetApp Files precisa de comunicar apenas com controladores de domínio no site onde se encontra o espaço de endereço seleto do Azure NetApp Files.

    Consulte [a designação da topologia do site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sobre sites e serviços de AD. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers Microsoft Management Console (MMC):   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Consulte o Azure NetApp Files [SMB FAQs](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) sobre informações adicionais de Anúncio. 

## <a name="decide-which-domain-services-to-use"></a>Decida quais os Serviços de Domínio a utilizar 

O Azure NetApp Files suporta tanto os Serviços de [Domínio de Diretório Ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) como os Serviços de Domínio de Diretório Ativo azure (AADDS) para ligações aD.  Antes de criar uma ligação AD, tem de decidir se utiliza ADDS ou AADDS.  

Para mais informações, consulte [Compare serviços de domínio de diretório ativo autogeridos, Diretório Ativo Azure e serviços de domínio de diretório ativo azure.](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions) 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Pode utilizar o seu âmbito preferido de [Sites e Serviços de Diretório Ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) para Ficheiros Azure NetApp. Esta opção permite leituras e escritos para controladores de domínio de domínio de diretório ativo (ADDS) que são [acessíveis por Ficheiros Azure NetApp](azure-netapp-files-network-topologies.md). Também impede que o serviço se comunique com controladores de domínio que não estejam no site de sites e serviços de diretório ativo especificado. 

Para encontrar o nome do seu site quando utilizar ADDS, pode contactar o grupo administrativo da sua organização responsável pelos Serviços de Domínio de Diretório Ativo. O exemplo abaixo mostra o plugin ative Directory Sites and Services onde o nome do site é exibido: 

![Serviços e Sites do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Quando configurar uma ligação AD para Ficheiros Azure NetApp, especifica o nome do site no âmbito do campo Nome do **Site AD.**

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Para configuração e orientação dos Serviços de Domínio ativo azure (AADDS), consulte a documentação dos Serviços de [Domínio Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/).

Solicitações adicionais de AADDS aplicáveis aos Ficheiros Azure NetApp: 

* Certifique-se de que o VNet ou subnet onde o AADDS é implantado está na mesma região do Azure netApp que a implementação de Ficheiros Azure NetApp.
* Se utilizar outro VNet na região onde o Azure NetApp Files é implementado, deverá criar um olhar entre os dois VNets.
* Os ficheiros Azure NetApp `user` suportam e `resource forest` tipos.
* Para o tipo de sincronização, pode selecionar `All` ou `Scoped` .   
    Se `Scoped` selecionar, certifique-se de que o grupo AD Azure correto está selecionado para aceder a ações SMB.  Se tiver dúvidas, pode usar o `All` tipo de sincronização.
* É necessária a utilização da Enterprise ou Premium SKU. O SKU Padrão não é suportado.

Quando criar uma ligação de Diretório Ativo, note as seguintes especificidades para AADDS:

* Pode encontrar informações para **DNS Primários,** **DNS Secundários**e Nome de **Domínio DNS AD** no menu AADDS.  
Para servidores DNS, serão utilizados dois endereços IP para configurar a ligação Ative Directory. 
* O caminho da **unidade organizacional** `OU=AADDC Computers` é.  
Esta definição está configurada nas Conexões de **Diretório Ativo** sob **conta NetApp:**

  ![Caminho de unidade organizacional](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **As** credenciais de nome de utilizador podem ser qualquer utilizador que seja membro dos administradores do **Azure AD Dc Administrators**do grupo Azure AD.


## <a name="create-an-active-directory-connection"></a>Criar uma ligação de Diretório Ativo

1. Na sua conta NetApp, clique em ligações de **Diretório Ativo**e, em seguida, clique em **Juntar**.  

    ![Conexões de Diretório Ativo](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela Join Ative Directy, forneça as seguintes informações, com base nos Serviços de Domínio que pretende utilizar:  

    Para obter informações específicas dos Serviços de Domínio que utiliza, consulte [Decida quais os Serviços](#decide-which-domain-services-to-use)de Domínio a utilizar. 

    * **DNS primários**  
        Este é o DNS que é necessário para a adesão ao domínio Ative Directy e operações de autenticação SMB. 
    * **DNS secundários**   
        Este é o servidor dNS secundário para garantir serviços de nome sancionatório. 
    * **Nome de domínio DNS AD**  
        Este é o nome de domínio dos seus Serviços de Domínio de Diretório Ativo a que pretende aderir.
    * **Nome do site do anúncio**  
        Este é o nome do site a que a descoberta do Controlador de Domínio se limitará.
    * **Prefixo do servidor SMB (conta de computador)**  
        Este é o prefixo de nomeação para a conta de máquina seleção em Diretório Ativo que o Azure NetApp Files utilizará para a criação de novas contas.

        Por exemplo, se o padrão de nomeação que a sua organização usa para servidores de ficheiros for NAS-01, NAS-02..., NAS-045, então você introduziria "NAS" para o prefixo. 

        O serviço criará contas adicionais de máquinas no Diretório Ativo, conforme necessário.

        > [!IMPORTANT] 
        > Renomear o prefixo do servidor SMB depois de criar a ligação Ative Directory é disruptivo. Terá de remontar as ações sMB existentes depois de renomear o prefixo do servidor SMB.

    * **Caminho de unidade organizacional**  
        Este é o caminho LDAP para a unidade organizacional (OU) onde serão criadas as contas da máquina do servidor SMB. Ou=segundo nível, OU=primeiro nível. 

        Se estiver a utilizar ficheiros Azure NetApp com serviços de domínio de diretório ativo Azure, o percurso da unidade organizacional é quando configura o `OU=AADDC Computers` Diretório Ativo para a sua conta NetApp.

     * **Utilizadores de política de backup**  
        Pode incluir contas adicionais que requerem privilégios elevados à conta de computador criada para utilização com Ficheiros Azure NetApp. As contas especificadas serão autorizadas a alterar as permissões NTFS ao nível do ficheiro ou da pasta. Por exemplo, pode especificar uma conta de serviço não privilegiada utilizada para migrar dados para uma participação de ficheiroS SMB em Ficheiros Azure NetApp.  

        > [!IMPORTANT] 
        > A utilização da função de utilizador da política de backup requer uma listagem branca. Envie um e-mail com o seu ID de anffeedback@microsoft.com subscrição para solicitar esta funcionalidade. 

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
        
        Se ainda não delegou uma sub-rede, pode clicar em **Criar novo** na página Criar um Volume. Em seguida, na página Create Subnet, especifique as informações da subnet e selecione **Microsoft.NetApp/volumes** para delegar a subnet para Ficheiros Azure NetApp. Em cada VNet, apenas uma subnet pode ser delegada nos Ficheiros Azure NetApp.   
 
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

## <a name="control-access-to-an-smb-volume"></a>Controlar o acesso a um volume SMB  

O acesso a um volume SMB é gerido através de permissões.  

### <a name="share-permissions"></a>Partilhar permissões  

Por padrão, um novo volume tem as permissões de partilha **De Todos / Controlo Total.** Os membros do grupo Domain Admins podem alterar as permissões de partilha utilizando a Computer Management na conta de computador que é usada para o volume de Ficheiros Azure NetApp.

![Caminho de montagem SMB ](../media/azure-netapp-files/smb-mount-path.png) 
 ![ Definir permissões de partilha](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Permissões de ficheiros e pastas NTFS  

Pode definir permissões para um ficheiro ou pasta utilizando o separador **De Segurança** das propriedades do objeto no cliente SMB do Windows.
 
![Definir permissões de ficheiros e pastas](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Passos seguintes  

* [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [FAQs sobre o SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Conheça a integração de redes virtuais para serviços Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instale uma nova floresta de Diretório Ativo utilizando o Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
