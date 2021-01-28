---
title: Criar, configurar clusters de pacotes de segurança empresarial - Azure
description: Saiba como criar e configurar clusters de pacotes de segurança empresarial em Azure HDInsight
services: hdinsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/10/2019
ms.openlocfilehash: 914acfab3935bc81e7d8382163ca9283c7f71a53
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934041"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Criar e configurar clusters de pacotes de segurança empresarial em Azure HDInsight

O Pacote de Segurança Empresarial (ESP) para Azure HDInsight dá-lhe acesso à autenticação baseada em Diretório Ativo, suporte multiusador e controlo de acesso baseado em funções para os seus clusters Apache Hadoop em Azure. Os clusters HDInsight ESP permitem que as organizações que aderirem a políticas rígidas de segurança corporativa processem dados sensíveis de forma segura.

Este guia mostra como criar um cluster Azure HDInsight ativado pelo ESP. Também mostra como criar um Windows IaaS VM no qual o Ative Directory and Domain Name System (DNS) está ativado. Utilize este guia para configurar os recursos necessários para permitir que os utilizadores no local entrem num cluster HDInsight habilitado a ES.

O servidor que criar funcionará como um substituto para o seu ambiente *real* no local. Irá usá-lo para os passos de configuração e configuração. Mais tarde, repetirá os passos no seu próprio ambiente.

Este guia também o ajudará a criar um ambiente de identidade híbrida utilizando a sincronização de haxixe de palavra-passe com o Azure Ative Directory (Azure AD). O guia complementa [o Uso ESP em HDInsight](apache-domain-joined-architecture.md).

Antes de utilizar este processo no seu próprio ambiente:

* Configurar o Ative Directory e o DNS.
* Ativar a ad Azure.
* Sync nas contas de utilizador no local para Azure AD.

![Diagrama de arquitetura Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Criar um ambiente no local

Nesta secção, você usará um modelo de implementação Azure Quickstart para criar novos VMs, configurar DNS e adicionar uma nova floresta ative direy.

1. Aceda ao modelo de implementação Quickstart para [criar um VM Azure com uma nova floresta de Diretório Ativo](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Selecione **Implementar para Azure**.
1. Inscreva-se na sua assinatura Azure.
1. No **Create a Azure VM com uma nova página de AD Forest,** forneça as seguintes informações:

    |Propriedade | Valor |
    |---|---|
    |Subscrição|Selecione a subscrição onde pretende implementar os recursos.|
    |Grupo de recursos|Selecione **Criar novo,** e insira o nome `OnPremADVRG`|
    |Localização|Selecione uma localização.|
    |Nome de utilizador Admin|`HDIFabrikamAdmin`|
    |Palavra-passe de Administrador|Introduza uma senha.|
    |Nome de Domínio|`HDIFabrikam.com`|
    |Prefixo dns|`hdifabrikam`|

    Deixe os valores predefinidos restantes.

    ![Modelo para criar um VM Azure com uma nova floresta AD AZure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Reveja os **Termos e Condições** e, em seguida, selecione **Concordo com os termos e condições acima indicados**.
1. Selecione **Comprar**, monitorize a implementação e aguarde que esteja concluída. A colocação leva cerca de 30 minutos para ser concluída.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configure utilizadores e grupos para acesso ao cluster

Nesta secção irá criar os utilizadores que terão acesso ao cluster HDInsight até ao final deste guia.

1. Ligue-se ao controlador de domínio utilizando o Ambiente de Trabalho Remoto.
    1. A partir do portal Azure, navegue para **grupos de recursos**  >  **OnPremADVRG**  >  **adVM**  >  **Connect**.
    1. A partir da lista de drop-down do **endereço IP,** selecione o endereço IP público.
    1. Selecione **Download RDP File** e, em seguida, abra o ficheiro.
    1. Use `HDIFabrikam\HDIFabrikamAdmin` como nome de utilizador.
    1. Introduza a palavra-passe que escolheu para a conta de administração.
    1. Selecione **OK**.

1. A partir do painel de controlador de controlador de domínio **Server Manager,** navegue para   >  **utilizadores e computadores de Diretório Ativo** de Ferramentas .

    ![No painel de instrumentos do Gestor do Servidor, abra a Gestão do Diretório Ativo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Criar dois novos utilizadores: **HDIAdmin** e **HDIUser**. Estes dois utilizadores vão iniciar sação nos clusters HDInsight.

    1. A partir da página **Ative Directory Users and Computers,** clique à direita `HDIFabrikam.com` e, em seguida, navegue para **Novo**  >  **Utilizador**.

        ![Criar um novo utilizador de Ative Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na página New **Object - User,** introduza `HDIUser` o nome do primeiro **nome** e o nome de logotipo **do utilizador.** Os outros campos autopovoam. Em seguida, selecione **Seguinte**.

        ![Crie o primeiro objeto de utilizador administrativo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Na janela pop-up que aparece, introduza uma palavra-passe para a nova conta. Selecione **Password nunca expira** e, em seguida, **OK** na mensagem pop-up.
    1. Selecione **Seguinte**, e em **seguida, Termine** para criar a nova conta.
    1. Repita os passos acima para criar o utilizador `HDIAdmin` .

        ![Criar um segundo objeto de utilizador de administração](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Criar um grupo de segurança global.

    1. A partir de **Utilizadores e Computadores de Diretório Ativo,** clique à `HDIFabrikam.com` direita, e depois navegue para **o Novo**  >  **Grupo**.

    1. Introduza `HDIUserGroup` na caixa de texto do nome do **grupo.**

    1. Selecione **OK**.

    ![Criar um novo grupo de Diretório Ativo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Criar um novo objeto](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Adicione membros ao **HDIUserGroup**.

    1. Clique com o botão direito **HDIUser** e **selecione Adicionar a um grupo...**.
    1. Na **Introdução dos nomes do objeto para selecionar caixa** de texto, insira `HDIUserGroup` . Em seguida, selecione **OK**, e **OK** novamente no pop-up.
    1. Repita os passos anteriores para a conta **HDIAdmin.**

        ![Adicione o membro HDIUser ao grupo HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Agora criaste o teu ambiente de Diretório Ativo. Adicionou dois utilizadores e um grupo de utilizadores que podem aceder ao cluster HDInsight.

Os utilizadores serão sincronizados com Azure AD.

### <a name="create-an-azure-ad-directory"></a>Criar um diretório AD Azure

1. Inicie sessão no Portal do Azure.
1. Selecione **Criar um recurso** e escrever `directory` . Selecione **Azure Ative Directory**  >  **Create**.
1. Sob **o nome da Organização,** insira `HDIFabrikam` .
1. No **nome de domínio inicial,** insira `HDIFabrikamoutlook` .
1. Selecione **Criar**.

    ![Criar um diretório AD Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Criar um domínio personalizado

1. A partir do seu novo **Diretório Ativo Azure**, em **Manage**, selecione **nomes de domínio personalizados**.
1. Selecione **+ Adicionar domínio personalizado**.
1. Em **nome de domínio personalizado,** `HDIFabrikam.com` insira e, em seguida, **selecione Adicionar domínio**.
1. Em seguida, preencha [as informações de DNS ao registo de domínio](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Criar um domínio personalizado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Criar um grupo

1. A partir do seu novo **Diretório Ativo Azure**, em **Gestão,** selecione **Grupos**.
1. Selecione **+ Novo grupo**.
1. Na caixa de texto do nome de **grupo,** insira `AAD DC Administrators` .
1. Selecione **Criar**.

## <a name="configure-your-azure-ad-tenant"></a>Configure o seu inquilino AZURE AD

Agora você vai configurar o seu inquilino Azure AD para que você possa sincronizar utilizadores e grupos desde o local de diretório ativo para a nuvem.

Criar um administrador de inquilino do Ative Directory.

1. Inscreva-se no portal Azure e selecione o seu inquilino AZure AD, **HDIFabrikam**.

1. Navegar para **gerir**  >  **utilizadores**  >  **Novo utilizador**.

1. Introduza os seguintes detalhes para o novo utilizador:

    **Identidade**

    |Propriedade |Descrição |
    |---|---|
    |Nome de utilizador|`fabrikamazureadmin`Insira na caixa de texto. A partir da lista de drop-down de nome de domínio, selecione `hdifabrikam.com`|
    |Name| Introduza `fabrikamazureadmin`.|

    **Palavra-passe**
    1. Selecione **Deixe-me criar a palavra-passe.**
    1. Introduza uma senha segura à sua escolha.

    **Grupos e funções**
    1. Selecione **0 grupos selecionados**.
    1. Selecione **administradores AAD DC** e, em seguida, **selecione**.

    ![A caixa de diálogo Azure AD Groups](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Selecione **Utilizador**.
    1. Selecione **Administrador Global** e, em seguida, **Selecione**.

    ![A caixa de diálogo de função Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Selecione **Criar**.

1. Em seguida, faça o novo teste de acesso ao portal Azure onde será solicitado para alterar a palavra-passe. Terá de o fazer antes de configurar o Microsoft Azure Ative Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizar utilizadores no local para a Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Configure Microsoft Azure Ative Directory Connect

1. A partir do controlador de domínio, baixe [o Microsoft Azure Ative Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Abra o ficheiro executável que descarregou e concorde com os termos da licença. Selecione **Continuar**.

1. **Selecione Utilize as definições expressas**.

1. Na página **Connect to Azure AD,** insira o nome de utilizador e a palavra-passe do administrador global para Azure AD. Use o nome de utilizador `fabrikamazureadmin@hdifabrikam.com` que criou quando configurar o seu inquilino Ative Directory. Em seguida, selecione **Seguinte**.

    ![A página "Ligar a Azure A D".](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Na página **'Ligar aos Serviços de Domínio do Diretório Activo',** insira o nome de utilizador e a palavra-passe para uma conta administrada da empresa. Utilize o nome de utilizador `HDIFabrikam\HDIFabrikamAdmin` e a sua palavra-passe que criou anteriormente. Em seguida, selecione **Seguinte**.

   ![A página "Ligar a Um D D S".](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na página **de configuração de inscrição AD Azure,** selecione **Seguinte**.
   ![A página "Azure AD sign-in"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Na página **Pronto para configurar,** selecione **Instalar**.

   ![A página "Pronto para configurar"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Na página completa da **Configuração,** selecione **Sair**.
   ![A página "Configuração completa"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Após a conclusão da sincronização, confirme que os utilizadores que criou no diretório iaaS estão sincronizados com o Azure AD.
   1. Inicie sessão no Portal do Azure.
   1. Selecione Utilizadores do **Azure Ative Directory**  >  **HDIFabrikam**  >  .

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Crie uma identidade gerida atribuída ao utilizador que possa utilizar para configurar os Serviços de Domínio Azure AD (Azure AD DS). Para obter mais informações, consulte [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador utilizando o portal Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Inicie sessão no Portal do Azure.
1. Selecione **Criar um recurso** e escrever `managed identity` . Selecione **Utilizador Atribuído Identidade Gerida**  >  **Criar**.
1. Para o **Nome de Recursos,** insira `HDIFabrikamManagedIdentity` .
1. Selecione a sua subscrição.
1. No **grupo de Recursos,** selecione **Criar novo** e insira `HDIFabrikam-CentralUS` .
1. Em **Localização**, selecione **Central US**.
1. Selecione **Criar**.

![Criar uma nova identidade gerida atribuída ao utilizador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Ativar o Azure AD DS

Siga estes passos para ativar a Azure AD DS. Para obter mais informações, consulte [Enable Azure AD DS utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance.md).

1. Crie uma rede virtual para hospedar Azure AD DS. Executar o seguinte código PowerShell.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Inicie sessão no Portal do Azure.
1. **Selecione Criar recurso,** insira `Domain services` e selecione **Azure AD Domain Services**  >  **Create**.
1. Na página **Basics:**
    1. Sob **o nome do Diretório,** selecione o diretório AD Azure que criou: **HDIFabrikam**.
    1. Para **o nome de domínio DNS,** introduza *HDIFabrikam.com*.
    1. Selecione a sua subscrição.
    1. Especificar o grupo de recursos **HDIFabrikam-CentralUS**. Para **localização**, selecione **Central US**.

        ![Detalhes básicos da Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Na página **'Rede',** selecione a rede (**HDIFabrikam-VNET)** e a sub-rede **(sub-rede AADDS)** que criou utilizando o script PowerShell. Ou escolha **Criar novo** para criar uma rede virtual agora.

    ![O passo "Criar rede virtual"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na página do **grupo do Administrador,** deverá ver uma notificação de que um grupo chamado **Administradores AAD DC** já foi criado para administrar este grupo. Pode modificar a adesão a este grupo se quiser, mas neste caso não precisa mudá-lo. Selecione **OK**.

    ![Ver o grupo de administradores AD Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na página **de Sincronização,** ativar a sincronização completa selecionando **All**  >  **OK**.

    ![Permitir a sincronização do Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na página **Resumo,** verifique os detalhes do Azure AD DS e selecione **OK**.

    ![O resumo de "Enable Azure AD Domain Services"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Depois de ativar O Azure AD DS, um servidor DNS local funciona nos VMS AD Azure.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configure a sua rede virtual Azure AD DS

Utilize os seguintes passos para configurar a sua rede virtual Azure AD DS **(HDIFabrikam-AADDSVNET)** para utilizar os seus servidores DNS personalizados.

1. Localize os endereços IP dos seus servidores DNS personalizados.
    1. Selecione o `HDIFabrikam.com` recurso Azure AD DS.
    1. Em **Gerir**, selecione **Propriedades**.
    1. Encontre os endereços IP no **endereço IP na rede virtual.**

    ![Localizar endereços IP DNS personalizados para Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configure **hDIFabrikam-AADDSVNET** para utilizar endereços IP personalizados 10.0.0.4 e 10.0.0.5.

    1. Em **Definições**, selecione **SERVIDORES DNS**.
    1. Selecione **Custom**.
    1. Na caixa de texto, insira o primeiro endereço IP *(10.0.0.4*).
    1. Selecione **Guardar**.
    1. Repita os passos para adicionar o outro endereço IP *(10.0.0.5*).

No nosso cenário, configuramos o Azure AD DS para utilizar endereços IP 10.0.0.4 e 10.0.0.5, definindo o mesmo endereço IP na rede virtual Azure AD DS:

![A página personalizada dos servidores DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Assegurar o tráfego LDAP

O Protocolo de Acesso ao Diretório Leve (LDAP) é utilizado para ler e escrever para o Azure Ative Directory. Pode tornar o tráfego LDAP confidencial e seguro utilizando a tecnologia Secure Sockets Layer (SSL) ou Transport Layer Security (TLS). Pode ativar o LDAP sobre O SSL (LDAPS) instalando um certificado devidamente formatado.

Para obter mais informações sobre lDAP seguro, consulte [Configure LDAPS para um domínio gerido AZure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Nesta secção, você cria um certificado auto-assinado, descarregue o certificado e configuure LDAPS para o domínio gerido **HDIFabrikam** Ad DS.

O seguinte script cria um certificado para **HDIFabrikam**. O certificado é guardado no caminho *local da Prisão.*

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Qualquer utilidade ou aplicação que crie um pedido válido de Criptografia de Chave Pública (PKCS) \# 10 pode ser usado para formar o pedido de certificado TLS/SSL.

Verifique se o certificado está instalado na loja **pessoal** do computador:

1. Iniciar a Consola de Gestão da Microsoft (MMC).
1. Adicione o snap-in **certificados** que gere os certificados no computador local.
1. Expandir **Certificados (Computador Local)**  >  Certificados **Pessoais.**  >   Deve existir um novo certificado na loja **Personal.** Este certificado é emitido para o nome de anfitrião totalmente qualificado.

    ![Verificar a criação de certificados locais](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. No painel à direita, clique à direita no certificado que criou. Aponte para **todas as tarefas** e, em seguida, selecione **Export**.

1. Na página **"Export Private Key",** selecione **Sim, exporte a chave privada**. O computador onde a chave será importada precisa da chave privada para ler as mensagens encriptadas.

    ![A página chave privada de exportação do assistente de exportação de certificados](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na página **'Formato de Ficheiro de Exportação',** deixe as definições predefinidos e, em seguida, selecione **Seguinte**.
1. Na página **Palavra-passe,** digite uma palavra-passe para a chave privada. Para **encriptação,** selecione **TripleDES-SHA1**. Em seguida, selecione **Seguinte**.
1. Na página **'Ficheiro para Exportação',** digite o caminho e o nome do ficheiro de certificado exportado e, em seguida, selecione **Seguinte**. O nome do ficheiro tem de ter uma extensão .pfx. Este ficheiro está configurado no portal Azure para estabelecer uma ligação segura.
1. Ativar o LDAPS para um domínio gerido Azure AD DS.
    1. A partir do portal Azure, selecione o domínio `HDIFabrikam.com` .
    1. Em **Gestão**, selecione **Secure LDAP**.
    1. Na página **Secure LDAP,** em **Secure LDAP**, selecione **Enable**.
    1. Procure o ficheiro de certificado .pfx que exportou no seu computador.
    1. Introduza a senha do certificado.

    ![Ativar LDAP seguro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Agora que ativou lDAPS, certifique-se de que está acessível, permitindo a porta 636.
    1. No grupo de recursos **HDIFabrikam-CentralUS,** selecione o grupo de segurança de rede **AADDS-HDIFabrikam.com-NSG**.
    1. Em **Definições**, selecione **regras de segurança de entrada**  >  **Adicionar**.
    1. Na página de **regra de segurança de entrada adicionar,** insira as seguintes propriedades e selecione **Adicionar**:

        | Propriedade | Valor |
        |---|---|
        | Origem | Qualquer |
        | Intervalo de portas de origem | * |
        | Destino | Qualquer |
        | Intervalo de portas de destino | 636 |
        | Protocolo | Qualquer |
        | Ação | Permitir |
        | Prioridade | \<Desired number> |
        | Name | Port_LDAP_636 |

    ![A caixa de diálogo "Adicionar regra de segurança de entrada"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** é a identidade gerida atribuída pelo utilizador. A função hdInsight Domain Services Contributor está ativada para a identidade gerida que permitirá que esta identidade leia, crie, modifique e elimine as operações de serviços de domínio.

![Criar uma identidade gerida atribuída pelo utilizador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Criar um cluster HDInsight habilitado a ESP

Este passo requer os seguintes pré-requisitos:

1. Criar um novo grupo de recursos *HDIFabrikam-WestUS* na localização **West US.**
1. Crie uma rede virtual que acolherá o cluster HDInsight ativado pelo ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Crie uma relação entre a rede virtual que acolhe o Azure AD DS ( `HDIFabrikam-AADDSVNET` ) e a rede virtual que irá acolher o cluster HDInsight ativado pelo ESP ( `HDIFabrikam-HDIVNet` ). Utilize o seguinte código PowerShell para espreitar as duas redes virtuais.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Crie uma nova conta Azure Data Lake Storage Gen2 chamada **Hdigen2store**. Configure a conta com a identidade gerida pelo utilizador **HDIFabrikamManagedIdentity**. Para obter mais informações, consulte [Use Azure Data Lake Storage Gen2 com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configurar DNS personalizados na rede virtual **HDIFabrikam-AADDSVNET.**
    1. Aceda ao portal Azure > **grupos de recursos** Nos servidores DNS do portal AzdVRG  >    >  **HDIFabrikam-AADDSVNET**  >  .
    1. Selecione **Custom** e introduza *10.0.0.4* e *10.0.0.5*.
    1. Selecione **Guardar**.

        ![Guarde as definições personalizadas de DNS para uma rede virtual](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Crie um novo cluster HDInsight Spark habilitado para a ESP.
    1. Selecione **Custom (tamanho, configurações, aplicativos)**.
    1. Introduza detalhes para **Básicos** (secção 1). Certifique-se de que o **tipo de cluster** é Faísca **2.3 (HDI 3.6)**. Certifique-se de que o **grupo de recursos** é **HDIFabrikam-CentralUS**.

    1. Para **Segurança + networking** (secção 2), preencha os seguintes detalhes:
        * No âmbito **do Pacote de Segurança Empresarial**, selecione **Enabled**.
        * Selecione **o utilizador de administração Cluster** e selecione a conta **HDIAdmin** que criou como utilizador de administração no local. Clique em **Selecionar**.
        * Selecione **Cluster access group**  >  **HDIUserGroup**. Qualquer utilizador que adicione a este grupo no futuro poderá aceder a clusters HDInsight.

            ![Selecione o grupo de acesso ao cluster HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Preencha os outros passos da configuração do cluster e verifique os detalhes no resumo do **Cluster**. Selecione **Criar**.

1. Inscreva-se na UI Ambari para o cluster recém-criado em `https://CLUSTERNAME.azurehdinsight.net` . Use o seu nome de utilizador de administrador `hdiadmin@hdifabrikam.com` e a sua palavra-passe.

    ![A janela de inscrição apache Ambari UI](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. A partir do painel de instrumentos do cluster, selecione **Roles**.
1. Na página **Funções,** sob **atribuir funções a estas,** junto à função **de Administrador de Cluster,** insira o grupo *hdiusergroup*. 

    ![Atribuir o papel de administrador de cluster ao grupo hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Abra o seu cliente Secure Shell (SSH) e inscreva-se no cluster. Use o **hdiuser** que criou no local do Diretório Ativo.

    ![Inscreva-se no cluster utilizando o cliente SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Se puder iniciar sômpa com esta conta, configura o seu cluster ESP corretamente para sincronizar com o seu caso ative diretório no local.

## <a name="next-steps"></a>Próximos passos

Leia [Uma introdução à segurança apache Hadoop com ESP.](hdinsight-security-overview.md)