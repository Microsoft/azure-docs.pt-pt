---
title: Criar e gerir ligações ative directory para ficheiros Azure NetApp | Microsoft Docs
description: Este artigo mostra-lhe como criar e gerir ligações ative directory para ficheiros Azure NetApp.
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: add907923cc2284939acd972237fd4ec74ee2d12
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864011"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Criar e gerir ligações ative directory para ficheiros Azure NetApp

Várias funcionalidades dos Ficheiros Azure NetApp exigem que tenha uma ligação Ative Directory.  Por exemplo, é necessário ter uma ligação Ative Directory antes de criar um [volume SMB](azure-netapp-files-create-volumes-smb.md) ou um [volume de dois protocolos](create-volumes-dual-protocol.md).  Este artigo mostra-lhe como criar e gerir ligações ative directory para ficheiros Azure NetApp.

## <a name="before-you-begin"></a>Antes de começar  

Tem de ter um conjunto de capacidade já configurado.   
[Criar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada nos Ficheiros Azure NetApp.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisitos das ligações do Active Directory

 Os requisitos para ligações de Diretório Ativo são os seguintes: 

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

* Os servidores DNS especificados devem ser alcançáveis a partir da [sub-rede delegada](./azure-netapp-files-delegate-subnet.md) dos Ficheiros Azure NetApp.  

    Consulte [as Diretrizes para o planeamento da rede Azure NetApp Files](./azure-netapp-files-network-topologies.md) para as topologias de rede suportadas.

    Os Grupos de Segurança da Rede (NSGs) e as firewalls devem ter regras adequadamente configuradas para permitir pedidos de tráfego de Diretório Ativo e DNS. 

* A sub-rede delegada pelos Ficheiros Azure NetApp deve ser capaz de chegar a todos os controladores de domínio do Ative Directory Domain Services (ADDS) no domínio, incluindo todos os controladores de domínio locais e remotos. Caso contrário, pode ocorrer uma interrupção de serviço.  

    Se tiver controladores de domínio inacessíveis pela sub-rede delegada do Azure NetApp Files, pode especificar um site de Diretório Ativo durante a criação da ligação Ative Directory.  O Azure NetApp Files precisa de comunicar apenas com controladores de domínio no site onde se encontra o espaço de endereço de sub-redes Azure NetApp.

    Consulte [a topologia do site](/windows-server/identity/ad-ds/plan/designing-the-site-topology) sobre sites e serviços de AD. 
    
* Pode ativar a encriptação AES para autenticação AD, verificando a caixa **de encriptação AES** na janela [''s AES's](#create-an-active-directory-connection) O Azure NetApp Files suporta os tipos de encriptação DES, Kerberos AES 128 e Kerberos AES 256 (dos menos seguros aos mais seguros). Se ativar a encriptação AES, as credenciais de utilizador utilizadas para aderir ao Ative Directory devem ter a opção de conta correspondente mais elevada, ativada que corresponda às capacidades ativadas para o seu Diretório Ativo.    

    Por exemplo, se o seu Diretório Ativo tiver apenas a capacidade AES-128, deve ativar a opção de conta AES-128 para as credenciais do utilizador. Se o seu Diretório Ativo tiver a capacidade AES-256, deve ativar a opção conta AES-256 (que também suporta AES-128). Se o seu Ative Directory não tiver qualquer capacidade de encriptação Kerberos, o Azure NetApp Files utiliza o DES por padrão.  

    Pode ativar as opções de conta nas propriedades da Consola de Gestão de Diretório Ativo e Computadores Microsoft Management (MMC):   

    ![Utilizadores de Diretório Ativo e Computadores MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* O Azure NetApp Files suporta [a assinatura LDAP,](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server)que permite a transmissão segura do tráfego LDAP entre o serviço Azure NetApp Files e os controladores de [domínio ative directy direcionados](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Se estiver a seguir as orientações do Microsoft Advisory [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) para a assinatura do LDAP, deverá ativar a funcionalidade de assinatura LDAP nos Ficheiros Azure NetApp, verificando a caixa **de assinatura LDAP** na janela ['Join Ative Directory'.](#create-an-active-directory-connection) 

    Só a configuração [de ligação ao canal LDAP](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) não tem qualquer efeito no serviço Azure NetApp Files. No entanto, se utilizar tanto o canal LDAP como o LDAP seguro (por exemplo, LDAPS `start_tls` ou), então a criação de volume SMB falhará.

## <a name="decide-which-domain-services-to-use"></a>Decidir quais os Serviços de Domínio a utilizar 

O Azure NetApp Files suporta serviços [de domínio de diretório ativo](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) e serviços de domínio de diretório ativo (AADDS) para ligações AD.  Antes de criar uma ligação AD, tem de decidir se utiliza ADDS ou AADDS.  

Para obter mais informações, consulte [Compare os Serviços de Domínio do Diretório Ativo auto-geridos, o Azure Ative Directory e os serviços de domínio do Diretório Ativo Azure.](../active-directory-domain-services/compare-identity-solutions.md) 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Pode utilizar o seu âmbito de serviço ativo preferido para [ficheiros](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) Azure NetApp. Esta opção permite leituras e escritas para controladores de domínio de Diretório Ativo (ADDS) que são [acessíveis por Ficheiros Azure NetApp](azure-netapp-files-network-topologies.md). Também impede que o serviço comunique com controladores de domínio que não estejam no site de Sites e Serviços de Diretório Ativo especificado. 

Para encontrar o nome do seu site quando utilizar o ADDS, pode contactar o grupo administrativo da sua organização responsável pelos Serviços de Domínio do Diretório Ativo. O exemplo abaixo mostra o plugin de Sites e Serviços de Diretório Ativo onde o nome do site é apresentado: 

![Serviços e Sites do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Quando configurar uma ligação AD para ficheiros Azure NetApp, especifique o nome do site no âmbito do campo **Nome do Site da AD.**

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Para configuração e orientação dos Serviços de Domínio do Diretório Ativo Azure (AADDS), consulte [a documentação dos Serviços de Domínio AD Azure](../active-directory-domain-services/index.yml).

Considerações adicionais da AADDS aplicam-se aos ficheiros Azure NetApp: 

* Certifique-se de que o VNet ou sub-rede onde o AADDS é implantado está na mesma região Azure que a implementação dos Ficheiros Azure NetApp.
* Se utilizar outro VNet na região onde o Azure NetApp Files é implantado, deverá criar um espremiamento entre os dois VNets.
* Azure NetApp Files suporta `user` e `resource forest` tipos.
* Para o tipo de sincronização, pode selecionar `All` ou `Scoped` .   
    Se `Scoped` selecionar, certifique-se de que o grupo Azure AD correto está selecionado para aceder a ações SMB.  Se tiver dúvidas, pode utilizar o `All` tipo de sincronização.
* É necessária a utilização da Enterprise ou Premium SKU. O SKU Standard não é suportado.

Quando criar uma ligação Ative Directory, note as seguintes especificidades para AADDS:

* Pode encontrar informações para **DNS Primários,** **DNS Secundários** e **Nome de Domínio AD DNS** no menu AADDS.  
Para os servidores DNS, serão utilizados dois endereços IP para configurar a ligação Ative Directory. 
* O **percurso da unidade organizacional** `OU=AADDC Computers` é.  
Esta definição está configurada nas Ligações de **Diretório Ativo** na **Conta NetApp:**

  ![Percurso da unidade organizacional](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **As** credenciais de nome de utilizador podem ser qualquer utilizador que seja membro dos administradores do grupo **Azure AD Azure.**


## <a name="create-an-active-directory-connection"></a>Criar uma ligação ative directy

1. A partir da sua conta NetApp, clique em **ligações de Ative Directory** e, em seguida, clique em **Juntar-se**.  

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
        Este é o nome do site a que a descoberta do controlador de domínio será limitada. Isto deve coincidir com o nome do site em Sites e Serviços de Diretório Ativo.
    * **Prefixo do servidor SMB (conta de computador)**  
        Este é o prefixo de nomeação para a conta de máquina no Ative Directory que o Azure NetApp Files utilizará para a criação de novas contas.

        Por exemplo, se o padrão de nomeação que a sua organização usa para servidores de ficheiros for NAS-01, NAS-02..., NAS-045, então você introduziria "NAS" para o prefixo. 

        O serviço criará contas de máquinas adicionais no Ative Directory, se necessário.

        > [!IMPORTANT] 
        > Renomear o prefixo do servidor SMB depois de criar a ligação Ative Directory é disruptivo. Terá de voltar a montar as ações SMB existentes depois de renomear o prefixo do servidor SMB.

    * **Percurso da unidade organizacional**  
        Este é o caminho LDAP para a unidade organizacional (OU) onde serão criadas contas de máquinas de servidor SMB. Ou=segundo nível, OU=primeiro nível. 

        Se estiver a utilizar ficheiros Azure NetApp com Serviços de Domínio do Diretório Ativo Azure, o percurso da unidade organizacional é `OU=AADDC Computers` quando configura o Ative Directory para a sua conta NetApp.

        ![Junte-se ao Diretório Ativo](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **Encriptação AES**   
        Selecione esta caixa de verificação se pretender ativar a encriptação AES para um volume SMB. Consulte [os requisitos relativos às ligações do Diretório Ativo](#requirements-for-active-directory-connections) para os requisitos. 

        ![Encriptação ative Directory AES](../media/azure-netapp-files/active-directory-aes-encryption.png)

        A funcionalidade **de encriptação AES** encontra-se atualmente em pré-visualização. Se esta for a sua primeira utilização, registe a funcionalidade antes de a utilizar: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Verifique o estado do registo da funcionalidade: 

        > [!NOTE]
        > O **Estado de Registo** pode estar no estado até `Registering` 60 minutos antes de mudar para `Registered` . Aguarde até que o estado seja `Registered` antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Também pode utilizar [comandos Azure CLI](/cli/azure/feature) `az feature register` e registar a funcionalidade e exibir o estado de `az feature show` registo. 

    * **Assinatura LDAP**   
        Selecione esta caixa de verificação para ativar a assinatura de LDAP. Esta funcionalidade permite uma pesquisa segura de LDAP entre o serviço Azure NetApp Files e os controladores de [domínio de serviços de domínio de diretório ativo especificados](/windows/win32/ad/active-directory-domain-services)pelo utilizador . Para mais informações, consulte [a ADV190023 | Orientação da Microsoft para permitir a ligação do canal LDAP e a assinatura LDAP](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Assinatura do Diretório Ativo LDAP](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        A funcionalidade **de Assinatura LDAP** está atualmente em pré-visualização. Se esta for a sua primeira utilização, registe a funcionalidade antes de a utilizar: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Verifique o estado do registo da funcionalidade: 

        > [!NOTE]
        > O **Estado de Registo** pode estar no estado até `Registering` 60 minutos antes de mudar para `Registered` . Aguarde até que o estado seja `Registered` antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Também pode utilizar [comandos Azure CLI](/cli/azure/feature) `az feature register` e registar a funcionalidade e exibir o estado de `az feature show` registo. 

     * **Utilizadores de privilégios de segurança**   <!-- SMB CA share feature -->   
        Pode conceder privilégios de segurança `SeSecurityPrivilege` () a utilizadores que necessitem de privilégio elevado para aceder aos volumes dos Ficheiros Azure NetApp. As contas de utilizador especificadas serão autorizadas a executar determinadas ações em ações SMB do Azure NetApp Files que requerem privilégio de segurança não atribuído por padrão aos utilizadores de domínio.   

        Por exemplo, as contas de utilizador utilizadas para instalar o SQL Server em certos cenários devem ter um privilégio de segurança elevado. Se estiver a utilizar uma conta não administrador (domínio) para instalar o SQL Server e a conta não tiver o privilégio de segurança atribuído, deverá adicionar privilégio de segurança à conta.  

        > [!IMPORTANT]
        > A conta de domínio utilizada para instalar o SQL Server já deve existir antes de a adicionar ao campo de **utilizadores de privilégios de Segurança.** Quando adicionar a conta do instalador sql server aos **utilizadores de privilégios** de segurança, o serviço Azure NetApp Files poderá validar a conta contactando o controlador de domínio. O comando pode falhar se não conseguir contactar o controlador de domínio.  

        Para obter mais informações sobre `SeSecurityPrivilege` e SQL Server, consulte [a instalação do SQL Server falha se a conta de Configuração não tiver determinados direitos de utilizador](/troubleshoot/sql/install/installation-fails-if-remove-user-right).

        ![Screenshot mostrando a caixa de utilizadores de privilégios de segurança da janela de ligações do Ative Directory.](../media/azure-netapp-files/security-privilege-users.png) 

     * **Utilizadores de política de backup**  
        Pode incluir contas adicionais que requerem privilégios elevados à conta de computador criada para utilização com ficheiros Azure NetApp. As contas especificadas serão autorizadas a alterar as permissões NTFS ao nível do ficheiro ou da pasta. Por exemplo, pode especificar uma conta de serviço não privilegiada utilizada para a migração de dados para uma partilha de ficheiros SMB em Ficheiros Azure NetApp.  

        ![Utilizadores de política de backup de diretório ativo](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        A funcionalidade **de utilizadores de política de backup** está atualmente em pré-visualização. Se esta for a sua primeira utilização, registe a funcionalidade antes de a utilizar: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Verifique o estado do registo da funcionalidade: 

        > [!NOTE]
        > O **Estado de Registo** pode estar no estado até `Registering` 60 minutos antes de mudar para `Registered` . Aguarde até que o estado seja `Registered` antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Também pode utilizar [comandos Azure CLI](/cli/azure/feature) `az feature register` e registar a funcionalidade e exibir o estado de `az feature show` registo. 

    * Credenciais, incluindo o seu **nome de utilizador** e **senha**

        ![Credenciais de diretório ativo](../media/azure-netapp-files/active-directory-credentials.png)

3. Clique em **Associar**.  

    A ligação Ative Directory que criou aparece.

    ![Conexões de Diretório Ativo Criadas](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="next-steps"></a>Passos seguintes  

* [Criar um volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Criar um volume de protocolo duplo](create-volumes-dual-protocol.md)
* [Configurar a encriptação Kerberos NFSv4.1](configure-kerberos-encryption.md)
* [Instale uma nova floresta de Diretório Ativo utilizando o Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
