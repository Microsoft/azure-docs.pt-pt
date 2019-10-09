---
title: Criar e configurar clusters Enterprise Security Package no Azure HDInsight
description: Saiba como criar e configurar clusters Enterprise Security Package no Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: aed656c65fc70946f7d91cb4354e1c081954e68c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030388"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Criar e configurar clusters Enterprise Security Package no Azure HDInsight

O Enterprise Security Package (ESP) para Azure HDInsight fornece acesso a autenticação baseada em Active Directory, suporte a multiusuário e controle de acesso baseado em função para seus clusters de Apache Hadoop no Azure. Os clusters de ESP do HDInsight permitem que as organizações que aderem às políticas de segurança corporativa possam processar dados confidenciais com segurança.

Este guia mostra como criar um cluster do Azure HDInsight habilitado para ESP. Ele também mostra como criar uma VM IaaS do Windows na qual Active Directory e o DNS (sistema de nomes de domínio) estão habilitados. Use este guia para configurar os recursos necessários para permitir que os usuários locais entrem em um cluster HDInsight habilitado para ESP.

O servidor criado funcionará como uma substituição para seu ambiente local *real* . Você o usará para as etapas de instalação e configuração. Posteriormente, você repetirá as etapas em seu próprio ambiente. 

Este guia também ajudará você a criar um ambiente de identidade híbrida usando a sincronização de hash de senha com o Azure Active Directory (Azure AD). O guia complementa o [uso do ESP no HDInsight](apache-domain-joined-architecture.md).

Antes de usar esse processo em seu próprio ambiente:
* Configurar Active Directory e DNS.
* Habilitar o Azure AD.
* Sincronizar contas de usuário locais com o Azure AD.

![Diagrama de arquitetura do Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Criar um ambiente local

Nesta seção, você usará um modelo de implantação de início rápido do Azure para criar novas VMs, configurar o DNS e adicionar uma nova floresta Active Directory.

1. Vá para o modelo de implantação de início rápido para [criar uma VM do Azure com uma nova floresta Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Selecione **implantar no Azure**.
1. Entre em sua assinatura do Azure.
1. Na página **criar uma VM do Azure com uma nova floresta do AD** :
    1. Na lista suspensa **assinatura** , selecione a assinatura na qual você deseja implantar os recursos.
    1. Ao lado de **grupo de recursos**, selecione **criar novo**e insira o nome *OnPremADVRG*.
    1. Para o restante dos campos de modelo, insira os seguintes detalhes:

        * **Local**: EUA Central
        * **Nome de usuário do administrador**: HDIFabrikamAdmin
        * **Senha do administrador**: \<YOUR_PASSWORD >
        * **Nome de domínio**: HDIFabrikam.com
        * **Prefixo DNS**: hdifabrikam

        ![Modelo para criar uma VM do Azure com uma nova floresta do Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Selecione **Comprar**.
    1. Monitore a implantação e aguarde sua conclusão.
    1. Verifique se os recursos foram criados no grupo de recursos correto, **OnPremADVRG**.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurar usuários e grupos para acesso ao cluster

Nesta seção, você criará os usuários que terão acesso ao cluster HDInsight no final deste guia.

1. Conecte-se ao controlador de domínio usando Área de Trabalho Remota.
    1. Se você usou o modelo mencionado no início, o controlador de domínio é uma VM chamada **adVM** no grupo de recursos **OnPremADVRG** .
    1. Na portal do Azure, selecione **grupos de recursos** > **OnPremADVRG** > **adVM** > **conectar**.
    1. Selecione a guia **RDP** > **baixar o arquivo RDP**.
    1. Salve o arquivo em seu computador e abra-o.
    1. Quando solicitado a fornecer credenciais, use *HDIFabrikam\HDIFabrikamAdmin* como o nome de usuário. Em seguida, insira a senha que você escolheu para a conta de administrador.

1. Quando a sessão de Área de Trabalho Remota for aberta na VM do controlador de domínio, no painel do **Gerenciador do servidor** , abra **Active Directory usuários e computadores**. No canto superior direito, selecione **ferramentas** > **Active Directory usuários e computadores**.

    ![No painel Gerenciador do Servidor, abra Active Directory Management](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Crie dois novos usuários: **HDIAdmin** e **HDIUser**. Esses dois usuários entrarão em clusters HDInsight.

    1. Na página **Active Directory usuários e computadores** , selecione **ação** > **novo** **usuário**do  > .

        ![Criar um novo usuário Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na página **novo objeto – usuário** , para o **nome de logon do usuário**, digite *HDIUser*. Em seguida, selecione **Seguinte**.

        ![Criar o primeiro objeto de usuário administrador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Na janela pop-up exibida, insira uma senha para a nova conta. A **senha Select nunca expira** > **OK**.
    1. Selecione **concluir** para criar a nova conta.
    1. Crie outro usuário chamado *HDIAdmin*.

        ![Criar um segundo objeto de usuário administrador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Na página **Active Directory usuários e computadores** , selecione **ação** > **novo** **grupo** > . Adicione um grupo chamado *HDIUserGroup*.

    ![Criar um novo grupo de Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Criar um novo objeto](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Adicione **HDIUser** ao **HDIUserGroup** como um membro do grupo.

    1. Clique com o botão direito do mouse em **HDIUserGroup** e selecione **Propriedades**.
    1. Na guia **Membros** , selecione **Adicionar**.
    1. Na caixa **Inserir os nomes de objeto a serem selecionados** , digite *HDIUser*. Em seguida, selecione **OK**.
    1. Repita as etapas anteriores para a conta **HDIAdmin** .

        ![Adicione o membro HDIUser ao grupo HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Agora você criou seu ambiente de Active Directory. Você adicionou dois usuários e um grupo de usuários que pode acessar o cluster HDInsight.

Os usuários serão sincronizados com o Azure AD.

### <a name="create-an-azure-ad-directory"></a>Criar um diretório do AD do Azure

1. Inicie sessão no Portal do Azure.
1. Selecione **criar um recurso** e digite o *diretório*. Selecione **Azure Active Directory** > **criar**.
1. Em **nome da organização**, insira *HDIFabrikam*.
1. Em **nome de domínio inicial**, insira *HDIFabrikamoutlook*.
1. Selecione **Criar**.

    ![Criar um diretório do AD do Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. No lado esquerdo da portal do Azure, selecione **Azure Active Directory**.
1. Se necessário, selecione **alternar diretório** para alterar para o novo diretório **HDIFabrikamoutlook** .
1. Em **gerenciar**, selecione **nomes de domínio personalizados** > **Adicionar domínio personalizado**.
1. Em **nome de domínio personalizado**, insira *HDIFabrikam.com* e selecione **Adicionar domínio**.

![Criar um domínio personalizado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Configurar seu locatário do Azure AD

Agora você configurará seu locatário do Azure AD para que você possa sincronizar usuários e grupos da instância de Active Directory local para a nuvem.

1. Crie um administrador de locatários Active Directory.
    1. Entre no portal do Azure e selecione seu locatário do Azure AD, **HDIFabrikam**.
    1. Em **gerenciar**, selecione **usuários** > **novo usuário**.
    1. Insira os seguintes detalhes para o novo usuário:
        * **Nome**: fabrikamazureadmin
        * **Nome de usuário**: fabrikamazureadmin@hdifabrikam.com
        * **Senha**: Uma senha segura de sua escolha

    1. Na seção **grupos** , procure administradores do **AAD DC** e clique em **selecionar**.

        ![A caixa de diálogo grupos do Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Abra a seção **função de diretório** e, à direita, selecione **administrador global** > **OK**.

        ![A caixa de diálogo função do Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Insira uma senha para o usuário. Em seguida, selecione **Criar**.

1. Se você quiser alterar a senha do usuário recém-criado \< @ no__t-1 >, use a identidade para entrar no portal do Azure. Em seguida, será solicitado que você altere a senha.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizar usuários locais com o Azure AD

### <a name="download-and-install-azure-ad-connect"></a>Baixar e instalar o Azure AD Connect

1. [Baixar Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Instale Azure AD Connect no controlador de domínio.

    1. Abra o arquivo executável que você baixou e concorde com os termos de licença. Selecione **Continuar**.

        ![A página "bem-vindo ao Azure AD Connect"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Selecione **usar configurações expressas** e concluir a instalação.

        ![Azure AD Connect configurações expressas](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>Configurar uma sincronização com o controlador de domínio local

1. Na página **conectar ao Azure ad** , insira o nome de usuário e a senha do administrador global do Azure AD. Use o nome de usuário `fabrikamazureadmin@hdifabrikam.com` que você criou quando configurou seu locatário Active Directory. Em seguida, selecione **Seguinte**. 

    ![A página "conectar ao Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Na página **conectar a Active Directory Domain Services** , insira o nome de usuário e a senha de uma conta de administrador corporativo. Use o nome de usuário `HDIFabrikam\HDIFabrikamAdmin` e sua senha que você criou anteriormente. Em seguida, selecione **Seguinte**. 

   ![A página "conectar ao Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na página de **configuração de entrada do Azure ad** , selecione **Avançar**.
   ![The "configuração de entrada do Azure AD" página @ no__t-1

1. Na página **pronto para configurar** , selecione **instalar**.

   ![A página "pronto para configurar"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Na página **configuração concluída** , selecione **sair**.
   ![The "configuração concluída", página @ no__t-1

1. Após a conclusão da sincronização, confirme se os usuários que você criou no diretório IaaS estão sincronizados com o Azure AD.
   1. Inicie sessão no Portal do Azure.
   1. Selecione **Azure Active Directory** > **HDIFabrikam** > **usuários**.

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Crie uma identidade gerenciada atribuída pelo usuário que você possa usar para configurar o Azure AD Domain Services (AD DS do Azure). Para obter mais informações, consulte [criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Inicie sessão no Portal do Azure.
1. Selecione **criar um recurso** e digite *identidade gerenciada*. Selecione **identidade gerenciada atribuída pelo usuário** > **criar**.
1. Para o **nome do recurso**, digite *HDIFabrikamManagedIdentity*.
1. Selecione a sua subscrição.
1. Em **grupo de recursos**, selecione **criar novo** e digite *HDIFabrikam-centralus*.
1. Em **local**, selecione **EUA Central**.
1. Selecione **Criar**.

![Criar uma nova identidade gerenciada atribuída pelo usuário](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Habilitar AD DS do Azure

Siga estas etapas para habilitar o Azure AD DS. Para obter mais informações, consulte [habilitar o Azure AD DS usando o portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Crie uma rede virtual para hospedar AD DS do Azure. Execute o código do PowerShell a seguir.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Inicie sessão no Portal do Azure.
1. Selecione **criar recurso**, insira *serviços de domínio*e selecione **Azure AD Domain Services**.
1. Na página **noções básicas** :
    1. Em **nome do diretório**, selecione o diretório do Azure AD que você criou: **HDIFabrikam**.
    1. Para **nome de domínio DNS**, insira *HDIFabrikam.com*.
    1. Selecione a sua subscrição.
    1. Especifique o grupo de recursos **HDIFabrikam-centralus**. Para **local**, selecione **EUA Central**.

        ![Detalhes básicos do Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Na página **rede** , selecione a rede (**HDIFabrikam-VNET**) e a sub-rede (**AADDS**) que você criou usando o script do PowerShell. Ou escolha **criar novo** para criar uma rede virtual agora.

    ![A etapa "criar rede virtual"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na página **grupo de administradores** , você deverá ver uma notificação de que um grupo chamado **Administradores do AAD DC** já foi criado para administrar esse grupo. Você pode modificar a associação desse grupo se desejar, mas nesse caso você não precisará alterá-la. Selecione **OK**.

    ![Exibir o grupo de administradores do Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na página **sincronização** , habilite sincronização completa selecionando **todos os** > **OK**.

    ![Habilitar a sincronização de AD DS do Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na página **Resumo** , verifique os detalhes do Azure AD DS e selecione **OK**.

    ![O resumo de "Habilitar Azure AD Domain Services"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Depois de habilitar o Azure AD DS, um servidor DNS local é executado nas VMs do Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurar sua rede virtual do Azure AD DS

Use as etapas a seguir para configurar sua rede virtual do Azure AD DS (**HDIFabrikam-AADDSVNET**) para usar seus servidores DNS personalizados.

1. Localize os endereços IP dos seus servidores DNS personalizados. 
    1. Selecione o recurso de AD DS do Azure **HDIFabrikam.com** . 
    1. Em **gerenciar**, selecione **Propriedades**. 
    1. Localize os endereços IP em **endereço IP na rede virtual**.

    ![Localizar endereços IP de DNS personalizados para o Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configure **HDIFabrikam-AADDSVNET** para usar os endereços IP personalizados 10.0.0.4 e 10.0.0.5.

    1. Em **configurações**, selecione **servidores DNS**. 
    1. Selecione **personalizado**.
    1. Na caixa de texto, insira o primeiro endereço IP (*10.0.0.4*).
    1. Selecione **Guardar**.
    1. Repita as etapas para adicionar o outro endereço IP (*10.0.0.5*).

Em nosso cenário, configuramos o Azure AD DS para usar os endereços IP 10.0.0.4 e 10.0.0.5, definindo o mesmo endereço IP na rede virtual do Azure AD DS:

![A página servidores DNS personalizados](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Protegendo o tráfego LDAP

O protocolo LDAP (Lightweight Directory Access Protocol) é usado para ler e gravar em Azure Active Directory. Você pode tornar o tráfego LDAP confidencial e seguro usando protocolo SSL (SSL) ou a tecnologia TLS (Transport Layer Security). Você pode habilitar LDAP sobre SSL (LDAPs) instalando um certificado formatado corretamente.

Para obter mais informações sobre LDAP seguro, consulte [Configurar LDAPS para um domínio gerenciado do Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Nesta seção, você criará um certificado autoassinado, baixará o certificado e configurará os LDAPs para o domínio gerenciado do **HDIFabrikam** Azure AD DS.

O script a seguir cria um certificado para **HDIFabrikam**. O certificado é salvo no caminho *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> Qualquer utilitário ou aplicativo que cria uma solicitação de PKCS (padrões de criptografia de chave pública) \#10 pode ser usado para formar a solicitação de certificado SSL.

Verifique se o certificado está instalado no repositório **pessoal** do computador:

1. Inicie o MMC (console de gerenciamento Microsoft).
1. Adicione o snap-in de **certificados** que gerencia certificados no computador local.
1. Expanda **certificados (computador local)**  > **certificados** **pessoais** > . Um novo certificado deve existir no repositório **pessoal** . Esse certificado é emitido para o nome de host totalmente qualificado.

    ![Verificar a criação do certificado local](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. No painel à direita, clique com o botão direito do mouse no certificado que você criou. Aponte para **todas as tarefas**e, em seguida, selecione **Exportar**.

1. Na página **Exportar chave privada** , selecione **Sim, exportar a chave privada**. O computador em que a chave será importada precisa da chave privada para ler as mensagens criptografadas.

    ![A página Exportar chave privada do assistente para exportação de certificados](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na página **formato do arquivo de exportação** , deixe as configurações padrão e, em seguida, selecione **Avançar**. 
1. Na página **senha** , digite uma senha para a chave privada. Para **criptografia**, selecione **TripleDES-SHA1**. Em seguida, selecione **Seguinte**.
1. Na página **arquivo a ser exportado** , digite o caminho e o nome do arquivo de certificado exportado e, em seguida, selecione **Avançar**. O nome do arquivo deve ter uma extensão. pfx. Esse arquivo é configurado no portal do Azure para estabelecer uma conexão segura.
1. Habilite LDAPs para um domínio gerenciado AD DS do Azure.
    1. No portal do Azure, selecione o domínio **HDIFabrikam.com**.
    1. Em **gerenciar**, selecione **LDAP seguro**.
    1. Na página **LDAP seguro** , em **LDAP seguro**, selecione **habilitar**.
    1. Procure o arquivo de certificado. pfx que você exportou em seu computador.
    1. Insira a senha do certificado.

    ![Habilitar LDAP seguro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Agora que você habilitou LDAPs, verifique se ele está acessível habilitando a porta 636.
    1. No grupo de recursos **HDIFabrikam-centralus** , selecione o grupo de segurança de rede **AADDS-HDIFabrikam.com-NSG**.
    1. Em **configurações**, selecione **regras de segurança de entrada** > **Adicionar**.
    1. Na página **Adicionar regra de segurança de entrada** , insira as seguintes propriedades e selecione **Adicionar**:

        | Propriedade | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | número \<Desired > |
        | Name | Port_LDAP_636 |

    ![A caixa de diálogo "Adicionar regra de segurança de entrada"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** é a identidade gerenciada atribuída pelo usuário. A função colaborador de serviços de domínio do HDInsight está habilitada para a identidade gerenciada que permitirá que essa identidade Leia, crie, modifique e exclua operações de serviços de domínio.

![Criar uma identidade gerida atribuída pelo utilizador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Criar um cluster HDInsight habilitado para ESP

Esta etapa requer os seguintes pré-requisitos:

1. Crie um novo grupo de recursos *HDIFabrikam-westus* no local **oeste dos EUA**.
1. Crie uma rede virtual que hospedará o cluster HDInsight habilitado para ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Crie uma relação de pares entre a rede virtual que hospeda o Azure AD DS (`HDIFabrikam-AADDSVNET`) e a rede virtual que hospedará o cluster HDInsight habilitado para ESP (`HDIFabrikam-HDIVNet`). Use o código do PowerShell a seguir para emparelhar as duas redes virtuais.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Crie uma nova conta de Azure Data Lake Storage Gen2 chamada **Hdigen2store**. Configure a conta com o **HDIFabrikamManagedIdentity**de identidade gerenciada pelo usuário. Para obter mais informações, consulte [usar Azure data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configure o DNS personalizado na rede virtual **HDIFabrikam-AADDSVNET** .
    1. Vá para o portal do Azure > **grupos de recursos**@no__t-**1 OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **servidores DNS**.
    1. Selecione **personalizado** e insira *10.0.0.4* e *10.0.0.5*.
    1. Selecione **Guardar**.

        ![Salvar configurações personalizadas de DNS para uma rede virtual](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Crie um novo cluster HDInsight Spark habilitado para ESP.
    1. Selecione **personalizado (tamanho, configurações, aplicativos)** .
    2. Insira detalhes para **noções básicas** (seção 1). Verifique se o **tipo de cluster** é **Spark 2,3 (HDI 3,6)** . Verifique se o **grupo de recursos** é **HDIFabrikam-centralus**.

    1. Para **segurança + rede** (seção 2), preencha os seguintes detalhes:
        * Em **Enterprise Security Package**, selecione **habilitado**.
        * Selecione **usuário administrador de cluster** e selecione a conta **HDIAdmin** que você criou como o usuário administrador local. Clique em **Selecionar**.
        * Selecione **grupo de acesso ao Cluster** > **HDIUserGroup**. Qualquer usuário que você adicionar a esse grupo no futuro poderá acessar os clusters HDInsight.

            ![Selecione o grupo de acesso ao cluster HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Conclua as outras etapas da configuração do cluster e verifique os detalhes no **Resumo do cluster**. Selecione **Criar**.

1. Entre na interface do usuário do amAmbari para o cluster recém-criado em `https://CLUSTERNAME.azurehdinsight.net`. Use o nome de usuário do administrador `hdiadmin@hdifabrikam.com` e sua senha.

    ![A janela de entrada da interface do usuário do Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. No painel do cluster, selecione **funções**.
1. Na página **funções** , em **atribuir funções a elas**, ao lado da função de **administrador de cluster** , insira o grupo *hdiusergroup*. 

    ![Atribuir a função de administrador de cluster ao hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Abra seu cliente do Secure Shell (SSH) e entre no cluster. Use o **hdiuser** que você criou na instância de Active Directory local.

    ![Entrar no cluster usando o cliente SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Se você puder entrar com essa conta, você configurou o cluster do ESP corretamente para sincronizar com sua instância de Active Directory local.

## <a name="next-steps"></a>Passos seguintes

Leia [uma introdução à segurança de Apache Hadoop com o ESP](hdinsight-security-overview.md).
