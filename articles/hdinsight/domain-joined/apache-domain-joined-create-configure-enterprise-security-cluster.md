---
title: Criar, configurar clusters de pacotes de pacotes de segurança empresarial - Azure
description: Saiba como criar e configurar clusters de pacotes de pacotes de segurança empresarial em Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: fb3484d013314897ea2e9157b642d8f2b85dcd60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437638"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Criar e configurar clusters de pacotes de pacotes de segurança empresarial em Azure HDInsight

O Pacote de Segurança Empresarial (ESP) para o Azure HDInsight dá-lhe acesso à autenticação baseada no Diretório Ativo, suporte a vários utilizadores e controlo de acesso baseado em papéis para os seus clusters Apache Hadoop em Azure. Os clusters esP HDInsight permitem às organizações que aderem a políticas rígidas de segurança corporativa para processar dados sensíveis de forma segura.

Este guia mostra como criar um cluster Azure HDInsight ativado por ESP. Também mostra como criar um VM Windows IaaS no qual o Ative Directory e o Domain Name System (DNS) estão ativados. Utilize este guia para configurar os recursos necessários para permitir que os utilizadores no local insinuem um cluster HDInsight ativado pela ESP.

O servidor que criar funcionará como um substituto para o seu ambiente *real* no local. Irá usá-lo para os passos de configuração e configuração. Mais tarde, repetirá os passos no seu próprio ambiente.

Este guia também irá ajudá-lo a criar um ambiente de identidade híbrido utilizando a sincronização de hash de senha com o Azure Ative Directory (Azure AD). Os complementos do guia [Utilize esp no HDInsight](apache-domain-joined-architecture.md).

Antes de utilizar este processo no seu próprio ambiente:

* Configurar Diretório Ativo e DNS.
* Ativar a AD Azure.
* Sync on-premises user accounts to Azure AD.

![Diagrama de arquitetura Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Criar um ambiente no local

Nesta secção, você usará um modelo de implantação Azure Quickstart para criar novos VMs, configurar DNS e adicionar uma nova floresta de Diretório Ativo.

1. Vá ao modelo de implantação Quickstart para [criar um VM Azure com uma nova floresta de Diretório Ativo.](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)

1. Selecione **Implementar para Azure**.
1. Inscreva-se na sua assinatura Azure.
1. Na **Create a Azure VM com uma nova página da Floresta AD,** forneça as seguintes informações:

    |Propriedade | Valor |
    |---|---|
    |Subscrição|Selecione a subscrição onde pretende utilizar os recursos.|
    |Grupo de recursos|Selecione **Criar novo,** e insira o nome`OnPremADVRG`|
    |Localização|Selecione uma localização.|
    |Nome de utilizador de administrador|`HDIFabrikamAdmin`|
    |Palavra-passe de Administrador|Introduza uma senha.|
    |Nome de Domínio|`HDIFabrikam.com`|
    |Prefixo Dns|`hdifabrikam`|

    Deixe os restantes valores predefinidos.

    ![Modelo para criar um VM Azure com uma nova floresta ad azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Reveja os **Termos e Condições**, e selecione **concordo com os termos e condições acima indicados**.
1. Selecione **Comprar,** monitorize a implementação e aguarde que esteja concluída. O destacamento leva cerca de 30 minutos para ser concluído.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configure utilizadores e grupos para acesso ao cluster

Nesta secção, irá criar os utilizadores que terão acesso ao cluster HDInsight até ao final deste guia.

1. Ligue-se ao controlador de domínio utilizando o Remote Desktop.
    1. A partir do portal Azure, navegue para **os grupos** > de recursos**OnPremADVRG** > **adVM** > **Connect**.
    1. A partir da lista de desatentes do **endereço IP,** selecione o endereço IP público.
    1. Selecione **Baixar ficheiro RDP**e, em seguida, abrir o ficheiro.
    1. Utilize `HDIFabrikam\HDIFabrikamAdmin` como nome de utilizador.
    1. Introduza a palavra-passe que escolheu para a conta de administração.
    1. Selecione **OK**.

1. A partir do **dashboard** do Gestor de Servidordo do controlador de domínio, navegue para **Tools** > **Ative Directory Users and Computers**.

    ![No dashboard Do Server Manager, abra a Gestão de Diretórios Ativos](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Crie dois novos utilizadores: **HDIAdmin** e **HDIUser**. Estes dois utilizadores irão iniciar sessão nos clusters HDInsight.

    1. A partir da página De utilizadores e `HDIFabrikam.com` **computadores de Diretório Ativo,** clique à direita, e depois navegue para **Novo** > **Utilizador**.

        ![Criar um novo utilizador de Diretório Ativo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na página New Object - `HDIUser` **User,** introduza o nome do primeiro **nome** e o nome de início de **sessão do utilizador**. Os outros campos vão auto-povoar. Em seguida, selecione **Seguinte**.

        ![Criar o primeiro objeto de utilizador administrativo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Na janela pop-up que aparece, introduza uma senha para a nova conta. Selecione **A Palavra-Passe nunca expira** **e,** em seguida, OK na mensagem pop-up.
    1. Selecione **Em seguida,** e depois **termine** para criar a nova conta.
    1. Repita os passos acima `HDIAdmin`para criar o utilizador .

        ![Criar um segundo objeto de utilizador administrativo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Criar um grupo de segurança global.

    1. De Utilizadores e Computadores de `HDIFabrikam.com` **Diretório Ativo,** clique à direita, e depois navegue para **o Novo** > **Grupo**.

    1. Introduza `HDIUserGroup` na caixa de **texto de nome de grupo.**

    1. Selecione **OK**.

    ![Criar um novo grupo de Diretório Ativo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Criar um novo objeto](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Adicione membros ao **HDIUserGroup**.

    1. Clique no **HDIUser** e selecione **Adicionar a um grupo...**.
    1. No **insira os nomes do objeto para selecionar** a caixa de texto, introduza `HDIUserGroup`. Em seguida, selecione **OK**, e **OK** novamente no pop-up.
    1. Repita os passos anteriores para a conta **HDIAdmin.**

        ![Adicione o membro HDIUser ao grupo HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Agora criaste o teu ambiente de Diretório Ativo. Adicionou dois utilizadores e um grupo de utilizadores que pode aceder ao cluster HDInsight.

Os utilizadores serão sincronizados com a AD Azure.

### <a name="create-an-azure-ad-directory"></a>Criar um diretório Azure AD

1. Inicie sessão no Portal do Azure.
1. Selecione Criar `directory`um **recurso** e escrever . Selecione **Azure Ative Directory** > **Create**.
1. Sob o nome `HDIFabrikam`da **Organização,** insira .
1. Sob o nome `HDIFabrikamoutlook`inicial de **domínio,** introduza .
1. Selecione **Criar**.

    ![Criar um diretório Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Criar um domínio personalizado

1. A partir do seu novo **Diretório Ativo Azure**, em **Manage**, selecione nomes de **domínio personalizados.**
1. Selecione **+ Adicionar domínio personalizado**.
1. Sob o nome `HDIFabrikam.com`de **domínio personalizado,** introduza , e, em seguida, selecione Adicionar **domínio**.
1. Em seguida, complete [Adicione as suas informações de DNS ao registo de domínio](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Criar um domínio personalizado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Criar um grupo

1. Do seu novo **Diretório Ativo Azure,** em **Manage**, selecione **Grupos**.
1. Selecione **+ Novo grupo**.
1. Na caixa de texto `AAD DC Administrators`de nome de **grupo,** introduza .
1. Selecione **Criar**.

## <a name="configure-your-azure-ad-tenant"></a>Configure o seu inquilino Azure AD

Agora vai configurar o seu inquilino Azure AD para que possa sincronizar utilizadores e grupos desde a instância de Diretório Ativo no local até à nuvem.

Crie um administrador de inquilinos de Diretório Ativo.

1. Inscreva-se no portal Azure e selecione o seu inquilino Azure AD, **HDIFabrikam**.

1. Navegar para **gerir** > **utilizadores** > **Novo utilizador**.

1. Introduza os seguintes detalhes para o novo utilizador:

    **Identidade**

    |Propriedade |Descrição |
    |---|---|
    |Nome de utilizador|Introduza `fabrikamazureadmin` na caixa de texto. A partir da lista de lançamento seletiva de nome de domínio, selecione`hdifabrikam.com`|
    |Nome| Introduza `fabrikamazureadmin`.|

    **Palavra-passe**
    1. Selecione **Deixe-me criar a palavra-passe**.
    1. Introduza uma senha segura à sua escolha.

    **Grupos e funções**
    1. Selecione **0 grupos selecionados**.
    1. Selecione **Administradores AAD DC**e, em seguida, **selecione**.

    ![A caixa de diálogo dos Grupos AD Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Selecione **Utilizador**.
    1. Selecione **administrador Global**e, em seguida, **selecione**.

    ![A caixa de diálogo de papel Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Selecione **Criar**.

1. Em seguida, faça o novo utilizador iniciar sessão no portal Azure onde será solicitado a alterar a palavra-passe. Terá de o fazer antes de configurar o Microsoft Azure Ative Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sync on-premises users to Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Configure Microsoft Azure Ative Directory Connect

1. Do controlador de domínio, baixe o [Microsoft Azure Ative Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Abra o ficheiro executável que descarregou e concorde com os termos da licença. Selecione **Continuar**.

1. Selecione **Utilize as definições de expresso**.

1. Na página **'Connect to Azure AD',** introduza o nome de utilizador e a palavra-passe do administrador global para a AD Azure. Use o `fabrikamazureadmin@hdifabrikam.com` nome de utilizador que criou quando configurao o seu inquilino de Diretório Ativo. Em seguida, selecione **Seguinte**.

    ![A página "Connect to Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Na página De Serviços de **Domínio de Diretório Ativo,** introduza o nome de utilizador e a palavra-passe para uma conta de administração da empresa. Utilize o `HDIFabrikam\HDIFabrikamAdmin` nome de utilizador e a sua palavra-passe que criou anteriormente. Em seguida, selecione **Seguinte**.

   ![A página "Connect to Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na página de configuração de entrada de **adato do Azure,** selecione **Next**.
   ![A página "Configuração de entrada de ad ad" azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. No **pronto configurar a** página, selecione **Instalar**.

   ![A página "Pronto para configurar"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Na página completa da **Configuração,** selecione **Saída**.
   ![A página "Configuração completa"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Após a conclusão da sincronização, confirme que os utilizadores que criou no diretório IaaS estão sincronizados com a Azure AD.
   1. Inicie sessão no Portal do Azure.
   1. Selecione Utilizadores de **Diretório** > Ativo**Azure HDIFabrikam** > **.**

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Crie uma identidade gerida atribuída ao utilizador que pode utilizar para configurar os Serviços de Domínio Azure AD (Azure AD DS). Para mais informações, consulte [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)pelo utilizador utilizando o portal Azure .

1. Inicie sessão no Portal do Azure.
1. Selecione Criar `managed identity`um **recurso** e escrever . Selecione **User Assigned Managed Identity** > **Create**.
1. Para o Nome `HDIFabrikamManagedIdentity`do **Recurso,** insira .
1. Selecione a sua subscrição.
1. No **grupo Recursos,** selecione **Criar novo** e entrar `HDIFabrikam-CentralUS`.
1. Em **baixo da localização,** selecione **Central US**.
1. Selecione **Criar**.

![Criar uma nova identidade gerida atribuída ao utilizador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Ativar o Azure AD DS

Siga estes passos para ativar o Azure AD DS. Para mais informações, consulte [Enable Azure AD DS utilizando o portal Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Crie uma rede virtual para hospedar o Azure AD DS. Executar o seguinte código PowerShell.

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
1. Selecione Criar `Domain services` **recurso,** introduzir e selecionar Serviços > de Domínio **Azure AD****Criar**.
1. Na página **Basics:**
    1. Sob **o nome do Diretório,** selecione o diretório Azure AD que criou: **HDIFabrikam**.
    1. Para o nome de **domínio DNS,** insira *HDIFabrikam.com*.
    1. Selecione a sua subscrição.
    1. Especifique o grupo de recursos **HDIFabrikam-CentralUS**. Para **localização**, selecione **Central US**.

        ![Detalhes básicos da AD DS azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Na página **da Rede,** selecione a rede **(HDIFabrikam-VNET)** e a subnet **(AADDS-subnet**) que criou utilizando o script PowerShell. Ou escolha **Criar uma nova** rede para criar uma rede virtual agora.

    ![O passo "Criar rede virtual"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na página do **grupo Administrator,** deve ver uma notificação de que um grupo chamado **AAD DC Administrators** já foi criado para administrar este grupo. Pode modificar a adesão a este grupo se quiser, mas neste caso não precisa de o mudar. Selecione **OK**.

    ![Ver o grupo de administrador da AD Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na página **de Sincronização,** ative a sincronização completa selecionando **All** > **OK**.

    ![Ativar a sincronização de DS Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na página **Resumo,** verifique os detalhes para Azure AD DS e selecione **OK**.

    ![O resumo dos "Enable Azure AD Domain Services"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Depois de ativar o Azure AD DS, um servidor DNS local funciona nos VMs AD Azure.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configure a sua rede virtual Azure AD DS

Utilize os seguintes passos para configurar a sua rede virtual Azure AD DS **(HDIFabrikam-AADDSVNET**) para utilizar os seus servidores DNS personalizados.

1. Localize os endereços IP dos seus servidores DNS personalizados.
    1. Selecione o `HDIFabrikam.com` recurso Azure AD DS.
    1. Em **'Gerir',** selecione **Propriedades**.
    1. Encontre os endereços IP no **endereço IP na rede virtual**.

    ![Localizar endereços IP dNS personalizados para DS AD Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configure **HDIFabrikam-AADDSVNET** para utilizar endereços IP personalizados 10.0.0.4 e 10.0.0.5.

    1. Em **Definições,** selecione **Servidores DNS**.
    1. Selecione **Custom**.
    1. Na caixa de texto, introduza o primeiro endereço IP *(10.0.0.4*).
    1. Selecione **Guardar**.
    1. Repita os passos para adicionar o outro endereço IP *(10.0.0.5*).

No nosso cenário, configurámos o Azure AD DS para utilizar os endereços IP 10.0.0.4 e 10.0.0.5, definindo o mesmo endereço IP na rede virtual Azure AD DS:

![A página personalizada dos servidores DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Assegurar o tráfego lDAP

O Protocolo de Acesso ao Diretório Leve (LDAP) é utilizado para ler e escrever ao Azure Ative Directory. Pode tornar o tráfego LDAP confidencial e seguro utilizando a tecnologia Secure Sockets Layer (SSL) ou Transport Layer Security (TLS). Pode ativar o LDAP sobre o SSL (LDAPS) instalando um certificado devidamente formatado.

Para obter mais informações sobre o LDAP seguro, consulte [configure LDAPS para um domínio gerido por DS Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Nesta secção, cria-se um certificado auto-assinado, descarrega o certificado e configura lDAPS para o domínio gerido pelo **HDIFabrikam** Azure AD DS.

O seguinte script cria um certificado para **HDIFabrikam**. O certificado é guardado no caminho *LocalMachine.*

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Qualquer pedido de utilidade ou aplicação que crie \#uma norma de criptografia de chaves públicas válida (PKCS) 10 pode ser usado para formar o pedido de certificado TLS/SSL.

Verifique se o certificado está instalado na loja **pessoal** do computador:

1. Inicie a Consola de Gestão da Microsoft (MMC).
1. Adicione o snap-in **certificados** que gere os certificados no computador local.
1. Expandir **Certificados Pessoais (Computador Local)** > **Certificados****Pessoais** > . Deve existir um novo certificado na loja **pessoal.** Este certificado é emitido para o nome de anfitrião totalmente qualificado.

    ![Verificar a criação de certificados locais](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. No painel à direita, clique no certificado que criou. Aponte para **todas as tarefas**e, em seguida, selecione **Exportação**.

1. Na página **Chave Privada de Exportação,** selecione **Sim, exporte a chave privada**. O computador onde a chave será importada precisa da chave privada para ler as mensagens encriptadas.

    ![A página-chave privada de exportação do Certificado De Saque do Feiticeiro de Exportação](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na página formato de formato de **ficheiro de exportação,** deixe as definições predefinidas e, em seguida, selecione **Next**.
1. Na página **Password,** digite uma palavra-passe para a chave privada. Para **encriptação,** selecione **TripleDES-SHA1**. Em seguida, selecione **Seguinte**.
1. Na página **De Registo para Exportação,** digite o caminho e o nome do ficheiro de certificado exportado e, em seguida, selecione **Next**. O nome do ficheiro tem de ter uma extensão .pfx. Este ficheiro está configurado no portal Azure para estabelecer uma ligação segura.
1. Ativar o LDAPS para um domínio gerido por DS Azure AD.
    1. A partir do portal Azure, selecione o domínio `HDIFabrikam.com`.
    1. Em **'Gerir',** selecione **Secure LDAP**.
    1. Na página **Secure LDAP,** em **Secure LDAP,** selecione **Enable**.
    1. Procure o ficheiro de certificado .pfx que exportou no seu computador.
    1. Introduza a senha do certificado.

    ![Ativar LDAP seguro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Agora que ativou o LDAPS, certifique-se de que é acessível através da porta 636.
    1. No grupo de recursos **HDIFabrikam-CentralUS,** selecione o grupo de segurança da rede **AADDS-HDIFabrikam.com-NSG**.
    1. Em **Definições,** selecione **Regras** > de segurança de entrada**Adicionar**.
    1. Na página de regra de **segurança adicionar,** introduza as seguintes propriedades e selecione **Adicionar:**

        | Propriedade | Valor |
        |---|---|
        | Origem | Qualquer |
        | Intervalo de portas de origem | * |
        | Destino | Qualquer |
        | Intervalo de portas de destino | 636 |
        | Protocolo | Qualquer |
        | Ação | Permitir |
        | Prioridade | \<Número desejado> |
        | Nome | Port_LDAP_636 |

    ![A caixa de diálogo "Adicionar regra de segurança de entrada"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** é a identidade gerida atribuída pelo utilizador. A função de Colaborador de Serviços de Domínio HDInsight está ativada para a identidade gerida que permitirá que esta identidade leia, crie, modifique e elimine operações de serviços de domínio.

![Criar uma identidade gerida atribuída pelo utilizador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Criar um cluster HDInsight ativado por ESP

Este passo requer os seguintes pré-requisitos:

1. Crie um novo grupo de recursos *HDIFabrikam-WestUS* no local **West US**.
1. Crie uma rede virtual que acolherá o cluster HDInsight ativado pela ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Crie uma relação entre a rede virtual que`HDIFabrikam-AADDSVNET`acolhe o Azure AD DS ( ) e`HDIFabrikam-HDIVNet`a rede virtual que acolherá o cluster HDInsight ativado pela ESP ( ). Utilize o seguinte código PowerShell para analisar as duas redes virtuais.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Crie uma nova conta Azure Data Lake Storage Gen2 chamada **Hdigen2store**. Configure a conta com a identidade gerida pelo utilizador **HDIFabrikamManagedIdentity**. Para mais informações, consulte [Use Azure Data Lake Storage Gen2 com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configurar DNS personalizados na rede virtual **HDIFabrikam-AADDSVNET.**
    1. Vá ao portal Azure > **grupos** > de recursos**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **Servidores DNS**.
    1. Selecione **Custom** e *introduza 10.0.0.4* e *10.0.0.5*.
    1. Selecione **Guardar**.

        ![Guarde as definições personalizadas de DNS para uma rede virtual](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Crie um novo cluster hDInsight spark ativado por ESP.
    1. Selecione **Custom (tamanho, configurações, apps)**.
    1. Introduza detalhes para **o Básico** (secção 1). Certifique-se de que o **tipo cluster** é **Spark 2.3 (HDI 3.6)**. Certifique-se de que o **grupo de recursos** é **HDIFabrikam-CentralUS**.

    1. Para **Segurança + networking** (secção 2), preencha os seguintes detalhes:
        * No **pacote de segurança da empresa,** selecione **Ativado**.
        * Selecione **o utilizador de administração do Cluster** e selecione a conta **HDIAdmin** que criou como utilizador de administração no local. Clique em **Selecionar**.
        * Selecione **Cluster access group** > **HDIUserGroup**. Qualquer utilizador que adicione a este grupo no futuro poderá aceder aos clusters HDInsight.

            ![Selecione o grupo de acesso ao cluster HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Complete os outros passos da configuração do cluster e verifique os detalhes no resumo do **Cluster**. Selecione **Criar**.

1. Inscreva-se na UI Ambari para `https://CLUSTERNAME.azurehdinsight.net`o aglomerado recém-criado em . Utilize o seu `hdiadmin@hdifabrikam.com` nome de utilizador administrativo e a sua palavra-passe.

    ![A janela de inscrição da Apache Ambari UI](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. A partir do painel de instrumentos de cluster, selecione **Funções**.
1. Na página **Funções,** sob **as funções de Atribuição a estas,** junto à função de Administrador de **Cluster,** insira o *grupo de hdiusergroup*do grupo . 

    ![Atribuir a função de administrador do cluster ao hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Abra o seu cliente Secure Shell (SSH) e inscreva-se no cluster. Utilize o **hísero** que criou no local de diretório ativo.

    ![Inscreva-se no cluster utilizando o cliente SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Se conseguir iniciar sessão com esta conta, configurou corretamente o seu cluster ESP para sincronizar com a sua instância de Diretório Ativo no local.

## <a name="next-steps"></a>Passos seguintes

Leia [Uma introdução à segurança de Apache Hadoop com a ESP.](hdinsight-security-overview.md)
