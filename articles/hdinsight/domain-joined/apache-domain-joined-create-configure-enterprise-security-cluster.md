---
title: Criar e configurar o Enterprise Security Package clusters no Azure HDInsight
description: Saiba como criar e configurar o Enterprise Security Package clusters no Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: 5b5b83fe0028e43ca35bf883b29cb71bad6ca2c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66253690"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Criar e configurar o Enterprise Security Package clusters no Azure HDInsight

O Enterprise Security Package para Azure HDInsight fornece acesso a autenticação baseada no Active Directory, o suporte de vários utilizador e o controlo de acesso baseado em funções para os seus clusters do Apache Hadoop no Azure. Clusters do HDInsight ESP permitem que as organizações, o qual aderem às políticas de segurança corporativa strict, para processar dados confidenciais de forma segura.

O objetivo deste guia está configurado corretamente os recursos necessários, de modo que no local os utilizadores podem iniciar sessão para um ESP ativado o cluster do HDInsight. Este artigo explica os passos necessários para criar um Cluster de HDInsight do Azure Enterprise Security Package ativada. Os passos aborda a criação de uma VM de IaaS do Windows com o Active Directory e serviços de nome de domínio (DNS) ativado. Este servidor irá atuar como um substituto para sua **real** ambiente no local e permitirá que percorra os passos de instalação e configuração para que pode repeti-los mais tarde em seu próprio ambiente. Este guia também ajudará a criar um ambiente de identidade híbrida com sincronização de hash de palavra-passe no Azure Active Directory.

Este guia destina-se a complementar [utilização Enterprise Security Package no HDInsight](apache-domain-joined-architecture.md)

Antes de utilizar este processo em seu próprio ambiente, a configuração do Active Directory e serviços de nomes de domínio (DNS). Além disso, ative o Azure Active Directory e sincronização de contas de utilizador de no local ao Azure Active Directory.

![Diagrama da arquitetura](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Criar o ambiente no local

Descrição geral: Nesta secção, irá utilizar um modelo de implementação rápida do Azure para criar novas VMs, configurar serviços de nomes de domínio (DNS) e uma nova floresta do AD.

1. Aceda a [criar uma VM do Azure com uma nova floresta de AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), para ver o modelo de implementação rápida.

1. Clique em **implementar no Azure**.
1. Inicie sessão na sua subscrição do Azure.
1. Sobre o **criar uma VM do Azure com uma nova floresta de AD** ecrã, conclua os seguintes passos:
    1. Selecione a subscrição onde pretende que os recursos implementados a partir da **subscrição** lista pendente.
    1. Selecione **criar novo** junto a **grupo de recursos** e introduza o nome **OnPremADVRG**
    1. Introduza os detalhes seguintes para o resto dos campos do modelo:

        * **Localização**: EUA Central
        * **Nome de utilizador administrador**: HDIFabrikamAdmin
        * **Palavra-passe de administrador**: < YOUR_PASSWORD >
        * **Domínio**: HDIFabrikam.com
        * **Prefixo DNS**: hdifabrikam

        ![Modelo criar VM do Azure e a floresta do AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Clique em **compra**
    1. Monitorizar a implementação e aguarde pela conclusão da mesma.
    1. Confirmar os recursos são criados no grupo de recursos correto `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurar utilizadores e grupos para acesso de cluster

Descrição geral: Nesta secção, irá criar os utilizadores que terão acesso ao cluster do HDInsight no final deste guia.

1. Ligar ao controlador de domínio com o ambiente de trabalho remoto.
    1. Se utilizou o modelo mencionado no início, o controlador de domínio é uma VM chamada **adVM** no `OnPremADVRG` grupo de recursos.
    1. Aceda ao portal do Azure > **grupos de recursos** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Clique nas **RDP** separador e, em seguida, clique em **baixar arquivo RDP**.
    1. Guarde o ficheiro no seu computador e abra-o.
    1. Quando lhe forem pedidas credenciais, utilize `HDIFabrikam\HDIFabrikamAdmin` como o nome de utilizador e, em seguida, introduza a palavra-passe que escolheu para a conta de administrador.

1. Assim que a sua sessão de ambiente de trabalho remoto é aberto numa VM do controlador de domínio, inicie **Active Directory Users and Computers** partir a **Gestor de servidor** dashboard. Clique em **ferramentas** no canto superior direito e, em seguida **Active Directory Users and Computers** na lista pendente.

    ![Gerenciamento de diretório do Active Directory do Gestor de servidor aberto](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Criar dois novos usuários **HDIAdmin**, **HDIUser**. Estes dois utilizadores serão utilizados para iniciar sessão em clusters do HDInsight.

    1. Na **Active Directory Users and Computers** ecrã, clique em **ação** > **novo** > **utilizador**.

        ![Criar novo utilizador do Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. Na **novo objeto - utilizador** ecrã, introduza `HDIUser` como a **nome de início de sessão do utilizador** e clique em **seguinte**.

        ![Criar o primeiro utilizador administrador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. Na caixa que aparece, introduza a palavra-passe pretendida para a nova conta. Marque a caixa que diz **palavra-passe nunca expira**. HDIClick **OK**.
    1. Clique em **concluir** para criar a nova conta.
    1. Criar outro utilizador `HDIAdmin`.

        ![Criar o segundo utilizador de administrador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. Na **Active Directory Users and Computers** ecrã, clique em **ação** > **novo** > **grupo**. Criar `HDIUserGroup` como um novo grupo.

    ![Criar novo grupo do Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Criar novo CN=grupo2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Adicionar a **HDIUser** criado no passo anterior para o **HDIUserGroup** como membro.

    1. Clique com o botão direito do rato sobre o **HDIUserGroup** e clique em **propriedades**.
    1. Aceda a **membros** separador e clique em **Add**.
    1. ENTER `HDIUser` na caixa rotulada **introduzir os nomes de objeto a selecionar** e clique em **OK**.
    1. Repita os passos anteriores para a outra conta `HDIAdmin`

        ![adicionar membros ao grupo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Acabou de criar seu ambiente do Active Directory, juntamente com dois usuários e um grupo de utilizadores para aceder ao HDInsight cluster.

Estes utilizadores serão sincronizados com o Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Criar um novo do Azure Active Directory

1. Inicie sessão no Portal do Azure.
1. Clique em **criar um recurso** e escreva **directory**. Selecione **do Azure Active Directory** > **criar**.
1. Introduza **HDIFabrikam** sob **nome da organização**.
1. Introduza **HDIFabrikamoutlook** sob **nome de domínio inicial**.
1. Clique em **Criar**.
1. No lado esquerdo no portal do Azure, clique em **do Azure Active Directory**.
1. Se necessário, clique em **trocar diretório** para alterar para o novo diretório que criou **HDIFabrikamoutlook**.
1. Sob **Manage** clique em **nomes de domínio personalizado** > **Adicionar domínio personalizado**.
1. Introduza **HDIFabrikam.com** sob **nome de domínio personalizado** e clique em **Adicionar domínio**.

![criar um novo diretório do Active Directory do azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![criar um novo domínio personalizado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Configure o seu inquilino do Azure AD

Descrição geral: Agora irá configurar o seu inquilino do Azure AD para que pode sincronizar os utilizadores e grupos locais AD para a cloud.

1. Crie um administrador de inquilino do AD.
    1. Inicie sessão portal do Azure e selecione o inquilino do Azure AD **HDIFabrikam**
    1. Selecione **usuários** sob **gerir** e, em seguida **novo utilizador**.
    1. Introduza os detalhes seguintes para o novo utilizador:

        * Name: fabrikamazureadmin
        * Nome de utilizador: fabrikamazureadmin@hdifabrikam.com
        * Palavra-passe: uma palavra-passe segura à sua escolha

    1. Clique nas **grupos** secção, procure **administradores do AAD DC**e clique em **selecionar**.

        ![Grupos](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Clique nas **função de diretório** secção e selecione **Administrador Global** no lado direito. Clique em **OK**.

        ![Função de diretório](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Introduza uma palavra-passe do utilizador. Clique em **Criar**.

1. Se pretender alterar a palavra-passe para o utilizador recém-criado <fabrikamazureadmin@hdifabrikam.com>. Inicie sessão no portal do Azure com que a identidade e, em seguida, será solicitado a alterar a palavra-passe.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizar os utilizadores no local para o Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Transferir e instalar o Microsoft Azure Active Directory connect

1. [Transferir o Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Instalação Microsoft Azure Active Directory, ligar-se no controlador de domínio.
    1. Abra o ficheiro executável que transferiu no passo anterior e aceite os termos de licenciamento. Clique em **Continue** (Continuar).

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Clique em **utilizar definições rápidas** e concluir a instalação.

        ![Utilizar definições rápidas](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Configurar a sincronização com o controlador de domínio no local

1. Sobre o **ligar para o Azure AD** ecrã, introduza o nome de utilizador e palavra-passe de administrador global para o Azure AD. Clique em **seguinte**. Este é o nome de utilizador `fabrikamazureadmin@hdifabrikam.com` que criou ao configurar o seu inquilino do AD.
    ![Ligar ao Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. Sobre o **ligar a serviços de domínio do Active Directory** ecrã, introduza o nome de utilizador e palavra-passe para uma conta de administrador de empresa. Clique em **seguinte**. Este é o nome de utilizador `HDIFabrikam\HDIFabrikamAdmin` e respetiva palavra-passe correspondente que criou anteriormente.

   ![Ligar a serviços de domínio do Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Sobre o **configuração do início de sessão no Azure AD** página, clique em **próxima**.
    ![Configuração do Azure AD início de sessão](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. No preparado para configurar o ecrã, clique em **instalar**.
    ![install](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Quando o **concluir a configuração** é apresentado o ecrã, clique em **saída**.
    ![Configuração concluída](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. Depois de concluída a sincronização, confirme se os utilizadores que criou no IAAS Active Directory são sincronizados com o Azure Active Directory.
    1. Inicie sessão no Portal do Azure.
    1. Selecione **do Azure Active Directory** > **HDIFabrikam** > **utilizadores**.

### <a name="create-an-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuído ao utilizador

Crie uma identidade de gerido atribuído ao utilizador que será utilizada para configurar o Azure Active Directory Domain Services (Azure AD-DS). Para obter mais informações sobre como criar uma identidade gerida atribuído ao utilizador, consulte [Create, lista, delete ou o atribuir uma função para uma identidade gerida atribuído ao utilizador com o portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Inicie sessão no Portal do Azure.
1. Clique em **criar um recurso** e escreva **identidade gerida**. Selecione **utilizador atribuído a identidade gerida** > **criar**.
1. Introduza **HDIFabrikamManagedIdentity** como o **nome do recurso**.
1. Selecione a sua subscrição.
1. Sob **grupo de recursos** clique em **criar nova** e introduza **HDIFabrikam CentralUS**.
1. Selecione **EUA Central** sob **localização**.
1. Clique em **Criar**.

![criar uma nova identidade gerida atribuído ao utilizador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Ativar o Azure Active Directory Domain Services

Para obter mais informações, consulte [ativar o Azure Active Directory Domain Services no portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Crie a rede Virtual para o anfitrião do Azure AD-DS. Execute o seguinte código do powershell.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Inicie sessão no Portal do Azure.
1. Clique em **criar recurso**, introduza **dos serviços de domínio** e selecione **Azure AD Domain Services**.
1. Sobre o **Noções básicas** ecrã conclua os seguintes passos:
    1. Sob **nome do diretório** selecione o Azure Active Directory criada para este tutorial **HDIFabrikam**.
    1. Introduza um **nome de domínio DNS** dos **HDIFabrikam.com**.
    1. Selecione a sua subscrição.
    1. Especifique o grupo de recursos **HDIFabrikam CentralUS** e o **localização** de **centro dos E.U.A.** .

        ![detalhes básicos do Azure ad ds](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Sobre o **rede** ecrã completo, selecione a rede (**HDIFabrikam-VNET**) e a sub-rede (**AADDS-sub-rede**) que criou com o script do powershell anterior. Ou pode utilizar o **criar novo** opção para criar uma rede virtual agora.

    ![Selecione a rede](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. Sobre o **grupo Administrador** ecrã, verá uma notificação a indicar que um grupo chamado **administradores do AAD DC** já foi criado para administrar este grupo. Opcionalmente, pode modificar a associação deste grupo, mas não é necessário para obter os passos deste tutorial. Clique em **OK**.

    ![grupo de administrador do modo de exibição](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. Sobre o **sincronização** ecrã, ativar a sincronização completa ao selecionar **todos os** e, em seguida, clique em **OK**.

    ![Ativar a sincronização](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Sobre o **resumo** ecrã, verifique os detalhes para o Azure AD-DS e clique em **Ok**.

    ![Verifique os detalhes](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Depois de ativar o Azure AD-DS, um servidor local do serviço de nomes de domínio (DNS) é executado em máquinas de virtuais AD (VMs).

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurar a rede virtual do Azure AD-DS

Os passos nesta secção irão ajudá-lo a configurar a rede virtual do Azure AD-DS (**HDIFabrikam AADDSVNET**) para utilizar seus servidores DNS personalizados.

1. Localize os endereços IP dos seus servidores DNS personalizados. Clique no **HDIFabrikam.com** recurso do AD DS, clique em **propriedades** sob **gerir**   e examinar os endereços IP listados na **IP Endereço de rede Virtual**.

    ![Localizar endereços de IP de DNS personalizados para o Azure AD-DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Configurar **HDIFabrikam AADDSVNET** para IPs personalizado `10.0.0.4` e `10.0.0.5`.

    1. Selecione **servidores DNS** sob o **definições** categoria. em seguida, clique no botão de opção junto a **personalizada**, introduza o endereço de IP (10.0.0.4) primeiro na caixa de texto abaixo e clique em **guardar**.
    1. Adicione mais endereços de IP (10.0.0.5) com os mesmos passos.

1. Em nosso cenário do Azure AD-DS foi configurado para utilizar o endereço IP de endereços IP 10.0.0.4 e 10.0.0.5, definir o mesmo numa AADDS VNet como mostrado na imagem abaixo.

    ![visualizar os servidores de dns personalizado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Proteger o tráfego LDAP

Protocolo de acesso de diretório leve (LDAP) é utilizado para ler e gravar no Active Directory. Pode tornar LDAP de tráfego confidenciais e segura, utilizando Secure Sockets Layer (SSL) / tecnologia de Transport Layer Security (TLS). Pode ativar o LDAP sobre SSL (LDAPS) ao instalar um certificado corretamente formatado.

Para obter mais informações sobre LDAP seguro, consulte [configurar secure LDAP (LDAPS) para um Azure AD Domain Services o domínio gerido](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Nesta secção, criar um certificado autoassinado, transfira o certificado e configurar o secure LDAP (LDAPS) para o **hdifabrikam** domínio gerido do Azure AD-DS.

O seguinte script cria um certificado para hdifabrikam. O certificado é guardado no caminho "LocalMachine".

> [!Note] 
> Qualquer aplicação que cria um PKCS válido ou utilitário \#10 podem ser utilizados para formar o pedido de certificado SSL do pedido.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Certifique-se de que o certificado é instalado no computador\'arquivo pessoal do s. Conclua os seguintes passos:

1. Inicie o Console de gerenciamento Microsoft (MMC).
1. Adicione o snap-in de certificados que gere certificados no computador local.
1. Expanda **certificados (computador Local)** , expanda **pessoais**e, em seguida, expanda **certificados**. Um novo certificado deve existir no arquivo pessoal. Este certificado é emitido para o nome de anfitrião totalmente qualificado.

    ![Certifique-se a criação do certificado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. No painel da direita, faça duplo clique no certificado que criou no passo anterior, aponte para **todas as tarefas**e, em seguida, clique em **exportar**.

1. Sobre o **exportar chave privada** , clique em **Sim, exportar a chave privada,** . A chave privada é necessária para as mensagens criptografadas ler a partir do computador em que a chave será importada.

    ![Exportar chave privada](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Sobre o **exportar formato de ficheiro** página, deixe as predefinições e, em seguida, clique em **seguinte**. 
1. Sobre o **palavra-passe** página, escreva uma palavra-passe para a chave privada, selecione **TripleDES SHA1** para **encriptação** e clique em **próxima**.
1. Sobre o **ficheiro a exportar** página, escreva o caminho e o nome do ficheiro de certificado exportado e, em seguida, clique em **seguinte**.
1. O nome do ficheiro tem de ser a extensão. pfx, este ficheiro está configurado no portal do Azure para estabelecer a ligação segura.
1. Ative o secure LDAP (LDAPS) para um domínio gerido do Azure AD Domain Services.
    1. Selecione o domínio **HDIFabrikam.com** do portal do Azure.
    1. Clique em **LDAP seguro** sob **gerir**.
    1. Sobre o **Secure LDAP** ecrã, clique em **ativar** sob **Secure LDAP**.
    1. Navegue para o ficheiro de certificado. pfx que exportou no seu computador.
    1. Introduza a palavra-passe do certificado.

    ![Ativar o ldap seguro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Agora que ativou o Secure LDAP, certifique-se de que seja acessível ao ativar a porta 636.
    1. Clique no grupo de segurança de rede **AADDS-HDIFabrikam.com-NSG** no **HDIFabrikam CentralUS** grupo de recursos.
    1. Sob **configurações** clique em **regras de segurança de entrada** > **adicionar**.
    1. Sobre o **Adicionar regra de segurança de entrada** ecrã, introduza as seguintes propriedades e clique em **Add**:

        | Propriedade | Value |
        |---|---|
        | source | Qualquer |
        | Intervalo de portas de origem | * |
        | Destino | Qualquer |
        | Intervalo de portas de destino | 636 |
        | Protocol | Qualquer |
        | Ação | Permitir |
        | Prioridade | <Desired Number> |
        | Name | Port_LDAP_636 |

    ![regra de segurança de entrada](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` é o atribuído ao utilizador a identidade gerida, a função de contribuinte de serviços de domínio do HDInsight está ativada para a identidade gerida que permitirá que esta identidade ler, criar, modificar e eliminar operações de serviços de domínio.

    ![Criar utilizador atribuído a identidade gerida](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Criação de Enterprise Security Package ativado o cluster do HDInsight

Este passo requer os seguintes pré-requisitos:

1. Criar um novo grupo de recursos `HDIFabrikam-WestUS` na localização `West US`.
1. Criar uma virtual a rede que irá alojar o ESP ativada cluster do HDInsight.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Criar uma relação de ponto a ponto entre a rede Virtual que está a alojar AADDS (`HDIFabrikam-AADDSVNET`) e a rede Virtual que irá alojar o ESP ativado o cluster do HDInsight (`HDIFabrikam-HDIVNet `). Utilize o seguinte código do powershell para configurar o peering entre estas duas redes virtuais.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Criar uma nova conta de geração 2 de armazenamento do Azure Data Lake **Hdigen2store**, que é configurado com a identidade do utilizador gerido **HDIFabrikamManagedIdentity**. Para obter mais informações sobre como criar as contas de geração 2 de armazenamento do Data Lake ativadas com utilizador identidades geridas, consulte [Use Azure Data Lake armazenamento Gen2 com o Azure HDInsight clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configurar DNS personalizado no **HDIFabrikam AADDSVNET** rede virtual.
    1. Aceda ao portal do Azure > **grupos de recursos** > **OnPremADVRG** > **HDIFabrikam AADDSVNET**  >   **Servidores DNS**.
    1. Selecione **personalizada** e introduza `10.0.0.4` e `10.0.0.5`.
    1. Clique em **Guardar**.

        ![guardar as definições de dns personalizado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Crie um novo cluster do Spark do HDInsight habilitados no ESP.
    1. Clique em **personalizado (tamanho, definições, aplicações)** .
    2. Introduza os detalhes de pretendida para a secção 1 **Noções básicas**. Certifique-se de que o **tipo de Cluster** é **Spark 2.3 (HDI 3.6)** e o **grupo de recursos** é **HDIFabrikam CentralUS**

    1. Na secção 2 **segurança + rede**, conclua os seguintes passos:
        1. Clique em **habilitado** sob **Enterprise Security Package**.
        1. Clique em **utilizador de administrador de Cluster** e selecione o **HDIAdmin** conta que criou anteriormente como utilizador de administrador no local. Clique em **Selecionar**.

        1. Clique em **grupo de acesso de Cluster** e, em seguida, selecione **HDIUserGroup**. Qualquer utilizador que adicionar a este grupo no futuro poderá aceder aos clusters do HDInsight.

            ![Selecione o grupo de acesso de cluster](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Concluir os outros passos da configuração do cluster e certifique-se os detalhes sobre o **resumo do Cluster**. Clique em **Criar**.

1. Inicie sessão na IU do Ambari do cluster recém-criado em `https://CLUSTERNAME.azurehdinsight.net` com o seu nome de utilizador administrador `hdiadmin@hdifabrikam.com` e palavra-passe.

    ![Inicie sessão no Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Clique em **funções** partir do dashboard do cluster.
1. Na **funções** página, introduza o grupo **hdiusergroup** atribuí-la para o **administrador de Cluster** função em **atribuir funções para cada uma delas**.

    ![atribuir a função de administrador de cluster para hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Abrir o seu cliente SSH e o início de sessão para o cluster com o **hdiuser** que criou anteriormente no Active Directory no local.

    ![início de sessão ao cluster com SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Se for capaz de iniciar sessão com esta conta, em seguida, configurou o cluster de ESP corretamente para sincronizar com o active directory no local.

## <a name="next-steps"></a>Passos Seguintes

* [Uma introdução à segurança do Apache Hadoop com o Enterprise Security Package](apache-domain-joined-introduction.md)
