---
title: Crie uma floresta de recursos de serviços de domínio Azure AD utilizando a Azure PowerShell | Microsoft Docs
description: Neste artigo, aprenda a criar e configurar uma floresta de recursos de recursos do Azure Ative Directory Domain Services e floresta de saída para um ambiente de Serviços de Domínio de Diretório Ativo no local utilizando a Azure PowerShell.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: justinha
ms.openlocfilehash: ebfc2476b7955b926f86094de03973155386eb8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619972"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Crie uma floresta de recursos de serviços de diretório ativo Azure e uma confiança florestal de saída para um domínio no local usando a Azure PowerShell

Em ambientes onde não é possível sincronizar hashes de palavra-passe, ou tem utilizadores que assinam exclusivamente usando cartões inteligentes para que não saibam a sua palavra-passe, pode utilizar uma floresta de recursos nos Serviços de Domínio do Diretório Ativo Azure (Azure AD DS). Uma floresta de recursos usa uma confiança de saída de ida da Azure AD DS para um ou mais ambientes AD DS no local. Esta relação de confiança permite que utilizadores, aplicações e computadores autentem contra um domínio no local do domínio gerido pela Azure AD DS. Numa floresta de recursos, as hashes de senha no local nunca são sincronizadas.

![Diagrama de confiança florestal de Azure AD DS para as AD DS no local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar uma floresta de recursos AD DS Azure usando Azure PowerShell
> * Criar uma confiança florestal de ida no domínio gerido usando a Azure PowerShell
> * Configure o DNS num ambiente AD DS no local para suportar a conectividade de domínio gerido
> * Criar uma confiança florestal unidireccionária num ambiente de DS AD no local
> * Teste e valide a relação de confiança para a autenticação e acesso a recursos

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> As florestas de recursos de domínio geridos não suportam atualmente ficheiros Azure HDInsight ou Azure. As florestas de utilizadores de domínio geridas por defeito suportam ambos estes serviços adicionais.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]

* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure PowerShell e ligue-se à sua assinatura Azure](/powershell/azure/install-az-ps).
    * Certifique-se de que faz sedução na sua assinatura Azure utilizando o [cmdlet Connect-AzAccount.][Connect-AzAccount]
* Instale e configuure Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure AD PowerShell e ligar-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de que faz sedundo ao seu inquilino AD Azure usando o cmdlet [Connect-AzureAD.][Connect-AzureAD]
* Você precisa de privilégios *de administrador global* no seu inquilino AZure AD para ativar Azure AD DS.
* Precisa de privilégios *colaboradores* na sua subscrição Azure para criar os recursos Azure AD DS necessários.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste artigo, você cria e configura a confiança florestal de saída de um domínio gerido usando o portal Azure. Para começar, inicie primeiro o sinal no [portal Azure.](https://portal.azure.com)

## <a name="deployment-process"></a>Processo de implementação

É um processo multi-partes para criar uma floresta de recursos de domínio gerida e a relação de confiança com um DS AD no local. Os seguintes passos de alto nível constroem o seu ambiente híbrido e confiável:

1. Crie um principal de serviço de domínio gerido.
1. Criar uma floresta de recursos de domínio gerida.
1. Crie conectividade de rede híbrida utilizando VPN local ou Rota Expressa.
1. Crie o lado de domínio gerido da relação de confiança.
1. Crie o lado AD DS no local da relação de confiança.

Antes de começar, certifique-se de compreender as considerações da [rede, o nome da floresta e os requisitos de DNS](tutorial-create-forest-trust.md#networking-considerations). Não se pode mudar o nome da floresta de domínio gerido uma vez que é implantado.

## <a name="create-the-azure-ad-service-principal"></a>Criar o diretor de serviço AZure AD

A Azure AD DS requer um principal serviço sincronizar dados da Azure AD. Este principal deve ser criado no seu inquilino Azure AD antes de criar a floresta de recursos de domínio gerido.

Crie um principal de serviço Azure para a Azure AD DS para comunicar e autenticar-se. Um ID de aplicação específico é usado chamado *Serviços de Controlador* de Domínio com um ID de *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Não mude esta identificação da aplicação.

Criar um diretor de serviço AZure AD utilizando o [cmdlet New-AzureADServicePrincipal:][New-AzureADServicePrincipal]

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>Criar uma floresta de recursos de domínio gerido

Para criar uma floresta de recursos de domínio gerido, você usa o `New-AzureAaddsForest` script. Este script faz parte de um conjunto mais amplo de comandos que suportam a criação e gestão de florestas de recursos de domínio geridos, incluindo criar a floresta unidirecional numa secção seguinte. Estes scripts estão disponíveis na [PowerShell Gallery](https://www.powershellgallery.com/) e são digitalmente assinados pela equipa de engenharia Azure AD.

1. Em primeiro lugar, crie um grupo de recursos utilizando o cmdlet [New-AzResourceGroup.][New-AzResourceGroup] No exemplo seguinte, o grupo de recursos é nomeado *myResourceGroup* e é criado na região *oeste.* Use o seu próprio nome e região desejada:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. Instale o `New-AaddsResourceForestTrust` script a partir da Galeria [PowerShell][powershell-gallery] utilizando o cmdlet [Install-Script:][Install-Script]

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Reveja os seguintes parâmetros necessários para o `New-AzureAaddsForest` script. Certifique-se de que também possui os módulos **Azure PowerShell** e **Azure AD.** Certifique-se de que planeou os requisitos de rede virtual para fornecer conectividade de aplicação e no local.

    | Name                         | Parâmetro do script          | Descrição |
    |:-----------------------------|---------------------------|:------------|
    | Subscrição                 | *-azureSubscriptionId*    | ID de assinatura usado para faturação Azure AD DS. Pode obter a lista de subscrições utilizando o [cmdlet Get-AzureRMSubscription.][Get-AzureRMSubscription] |
    | Grupo de Recursos               | *-aaddsResourceGroupName* | Nome do grupo de recursos para o domínio gerido e recursos associados. |
    | Localização                     | *-aaddsLocalização*          | A região de Azure para acolher o seu domínio gerido. Para as regiões disponíveis, consulte [regiões apoiadas para Azure AD DS.](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) |
    | Administrador da Azure AD DS    | *-aaddsAdminUser*         | O nome principal do utilizador do primeiro administrador de domínio gerido. Esta conta deve ser uma conta de utilizador em nuvem existente no seu Diretório Ativo Azure. O utilizador, e o utilizador que executa o script, é adicionado ao grupo *de administradores AAD DC.* |
    | Nome de domínio Azure AD DS      | *-aaddsDomainName*        | O FQDN do domínio gerido, com base nas orientações anteriores sobre como escolher um nome florestal. |

    O `New-AzureAaddsForest` script pode criar a rede virtual Azure e a sub-rede Azure AD DS se estes recursos ainda não existirem. O script pode criar opcionalmente as sub-redes de carga de trabalho, quando especificado:

    | Name                              | Parâmetro do script                  | Description |
    |:----------------------------------|:----------------------------------|:------------|
    | Nome da rede virtual              | *-aaddsVnetName*                  | Nome da rede virtual para o domínio gerido.|
    | Espaço de endereços                     | *-aaddsVnetCIDRAddressSpace*      | Gama de endereços da rede virtual na notação CIDR (se criar a rede virtual).|
    | Nome da sub-rede Azure AD DS           | *-aaddsSubnetName*                | Nome da sub-rede da rede virtual *aaddsVnetName* que alberga o domínio gerido. Não coloque os seus próprios VMs e cargas de trabalho nesta sub-rede. |
    | Gama de endereços Azure AD DS         | *-aaddsSubnetCIDRAddressRange*    | Intervalo de endereços de sub-rede na notação CIDR para a instância DS da AAD, tais como *192.168.1.0/24*. O intervalo de endereços deve ser contido pelo intervalo de endereços da rede virtual e diferente de outras sub-redes. |
    | Nome da sub-rede de carga de trabalho (opcional)   | *-carga de trabalhoSubnetName*             | Nome opcional de uma sub-rede na rede virtual *aaddsVnetName* para criar para as suas próprias cargas de trabalho de aplicação. VMs e aplicações e também ser conectado a uma rede virtual Azure esprevada em vez disso. |
    | Gama de endereços de carga de trabalho (opcional) | *-carga de trabalhoSubnetCIDRAddressRange* | Gama de endereços de sub-rede opcional na notação CIDR para carga de trabalho de aplicação, tais como *192.168.2.0/24*. O intervalo de endereços deve ser contido pelo intervalo de endereços da rede virtual e diferente de outras sub-redes.|

1. Agora crie uma floresta de recursos de domínio gerido usando o `New-AzureAaaddsForest` script. O exemplo a seguir cria uma floresta chamada *addscontoso.com* e cria uma sub-rede de carga de trabalho. Forneça os seus próprios nomes de parâmetros e intervalos de endereços IP ou redes virtuais existentes.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    Leva bastante tempo para criar a floresta de recursos de domínio gerido e recursos de apoio. Deixe o script completo. Continue na secção seguinte para configurar a conectividade da sua rede no local enquanto as provisões de floresta de recursos AD AZure em segundo plano.

## <a name="configure-and-validate-network-settings"></a>Configurar e validar definições de rede

À medida que o domínio gerido continua a implantar, configurar e validar a conectividade da rede híbrida com o datacenter no local. Também precisa de um VM de gestão para usar com o domínio gerido para manutenção regular. Parte da conectividade híbrida pode já existir no seu ambiente, ou poderá ter de trabalhar com outros da sua equipa para configurar as ligações.

Antes de começar, certifique-se de compreender as [considerações e recomendações](tutorial-create-forest-trust.md#networking-considerations)da rede .

1. Crie a conectividade híbrida da sua rede no local para a Azure utilizando uma ligação Azure VPN ou Azure ExpressRoute. A configuração da rede híbrida está fora do âmbito desta documentação, e pode já existir no seu ambiente. Para mais detalhes sobre cenários específicos, consulte os seguintes artigos:

    * [VPN local-a-local do Azure.](../vpn-gateway/vpn-gateway-about-vpngateways.md)
    * [Visão geral do Azure ExpressRoute](../expressroute/expressroute-introduction.md).

    > [!IMPORTANT]
    > Se criar a ligação diretamente à rede virtual do seu domínio gerido, utilize uma sub-rede de gateway separada. Não crie o portal na sub-rede do domínio gerido.

1. Para administrar um domínio gerido, cria-se um VM de gestão, junta-o ao domínio gerido e instala as ferramentas de gestão de DS AD necessárias.

    Enquanto a floresta de recursos de domínio gerido está a ser implantada, [crie um VM do Servidor do Windows,](./join-windows-vm.md) [em seguida, instale as ferramentas de gestão de DS AD core](./tutorial-create-management-vm.md) para instalar as ferramentas de gestão necessárias. Aguarde para se juntar ao VM de gestão até que um dos seguintes passos após a implementação do domínio seja implementado com sucesso.

1. Valide a conectividade da rede entre a sua rede no local e a rede virtual Azure.

    * Confirme que o controlador de domínio no local pode ligar-se ao VM gerido utilizando `ping` ou ao ambiente de trabalho remoto, por exemplo.
    * Verifique se o seu VM de gestão pode ligar-se aos controladores de domínio no local, utilizando novamente um utilitário como `ping` .

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**. Escolha o seu domínio gerido, como *aaddscontoso.com* e aguarde que o estado se reporte como **Running**.

    Ao executar, [atualize as definições de DNS para a rede virtual Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) e, em seguida, [ative as contas do utilizador para Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para finalizar as configurações para a floresta de recursos de domínio gerido.

1. Tome nota dos endereços DNS apresentados na página geral. Precisa destes endereços quando configurar o lado ativo do Diretório Ativo da relação de confiança numa secção seguinte.
1. Reinicie o VM de gestão para que receba as novas definições de DNS e, em seguida, [junte o VM ao domínio gerido](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. Depois de o VM de gestão ser ligado ao domínio gerido, conecte-se novamente utilizando o ambiente de trabalho remoto.

    A partir de um pedido de comando, use `nslookup` e o nome de floresta de recursos de domínio gerido para validar a resolução de nome para a floresta de recursos.

    ```console
    nslookup aaddscontoso.com
    ```

    O comando deve devolver dois endereços IP para a floresta de recursos.

## <a name="create-the-forest-trust"></a>Criar a confiança da floresta

O fundo florestal tem duas partes - a confiança florestal de ida e saída na floresta gerida de recursos de domínio, e a confiança florestal unidireccionada na floresta AD DS no local. Crias manualmente ambos os lados desta relação de confiança. Quando ambos os lados são criados, os utilizadores e recursos podem autenticar com sucesso usando a confiança da floresta. Uma floresta de recursos de domínio gerido suporta até cinco fundos florestais de ida e saída para as florestas no local.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>Criar o lado de domínio gerido da relação de confiança

Use o `Add-AaddsResourceForestTrust` script para criar o lado de domínio gerido da relação de confiança. Primeiro, instale o `Add-AaddsResourceForestTrust` script a partir da Galeria [PowerShell][powershell-gallery] utilizando o cmdlet [Install-Script:][Install-Script]

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

Agora forneça ao guião as seguintes informações:

| Name                               | Parâmetro do script     | Description |
|:-----------------------------------|:---------------------|:------------|
| Nome de domínio Azure AD DS            | *-ManagedDomainFqdn* | FQDN do domínio gerido, como *aaddscontoso.com* |
| Nome de domínio AD DS nas instalações      | *-TrustFqdn*         | O FQDN da floresta de confiança, como *onprem.contoso.com* |
| Nome amigável de confiança                | *-TrustFriendlyName* | Nome amigável da relação de confiança. |
| Endereços IP AD DS DNS no local | *-TrustDnsiPs*       | Uma lista delimitada por vírgula de endereços IPv4 do servidor DNS para o domínio fidedigno listado. |
| Senha de confiança                     | *-TrustPassword*     | Uma senha complexa para a relação de confiança. Esta palavra-passe também é inserida ao criar a confiança de entrada unidirecionais no AD DS no local. |
| Credenciais                        | *-Credenciais*       | As credenciais usadas para autenticar para Azure. O utilizador deve estar no *grupo de administradores da AAD DC*. Se não for fornecido, o script solicita a autenticação. |

O exemplo a seguir cria uma relação de confiança chamada *myAzureADDSTrust* para *onprem.contoso.com*. Use os seus próprios nomes de parâmetros e palavras-passe:.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Lembre-se da sua senha de confiança. Deve usar a mesma palavra-passe quando criar o lado do fundo no local.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configurar DNS no domínio do local

Para resolver corretamente o domínio gerido a partir do ambiente no local, poderá ser necessário adicionar reencaminhadores aos servidores DNS existentes. Se não tiver configurado o ambiente no local para comunicar com o domínio gerido, complete os seguintes passos a partir de uma estação de trabalho de gestão para o domínio AD DS no local:

1. Selecione **Iniciar | Ferramentas Administrativas | DNS**
1. Servidor DNS de seleção direita, como *myAD01*, selecione **Properties**
1. Escolha **forwarders** e, em seguida, **edite** para adicionar reencaminhadores adicionais.
1. Adicione os endereços IP do domínio gerido, tais como *10.0.1.4* e *10.0.1.5*.
1. A partir de um pedido de comando local, valide a resolução de nome usando **nslookup** do nome de domínio de floresta de recursos de domínio gerido. Por exemplo, `Nslookup aaddscontoso.com` deve devolver os dois endereços IP para a floresta de recursos de domínio gerido.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Criar confiança florestal no domínio do local

O domínio AD DS no local precisa de uma confiança florestal para o domínio gerido. Esta confiança deve ser criada manualmente no domínio AD DS no local, não pode ser criada a partir do portal Azure.

Para configurar a confiança de entrada no domínio AD DS no local, complete os seguintes passos de uma estação de trabalho de gestão para o domínio DS AD no local:

1. Selecione **Iniciar | Ferramentas Administrativas | Domínios e Fidedignidades do Diretório Ativo**
1. Domínio selecionado à direita, como *onprem.contoso.com,* selecione **Properties**
1. Escolha o separador **Trusts,** em seguida, **New Trust**
1. Insira o nome do domínio gerido, como *aaddscontoso.com,* em seguida, selecione **Next**
1. Selecione a opção de criar um **fundo florestal,** em seguida, para criar uma **única maneira: confiança de entrada.**
1. Opte por criar a confiança **apenas** para este domínio . No passo seguinte, cria-se a confiança no portal Azure para o domínio gerido.
1. Opte por utilizar a **autenticação em toda** a Floresta, em seguida, insira e confirme uma senha de confiança. Esta mesma palavra-passe também é inserida no portal Azure na secção seguinte.
1. Passe pelas próximas janelas com opções predefinidas, em seguida, escolha a opção para **Não, não confirme a confiança de saída**. Não é possível validar a relação de confiança porque a sua conta de administração delegada na floresta de recursos de domínio gerido não tem as permissões necessárias. Este comportamento é propositado.
1. Selecione **Acabamento**

## <a name="validate-resource-authentication"></a>Validar a autenticação de recursos

Os seguintes cenários comuns permitem validar que a confiança florestal autentica corretamente os utilizadores e o acesso aos recursos:

* [Autenticação do utilizador no local da floresta de recursos Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Recursos de acesso na floresta de recursos Azure AD DS utilizando o utilizador no local](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Ativar a partilha de ficheiros e impressoras](#enable-file-and-printer-sharing)
    * [Criar um grupo de segurança e adicionar membros](#create-a-security-group-and-add-members)
    * [Criar uma partilha de ficheiros para acesso cross-forest](#create-a-file-share-for-cross-forest-access)
    * [Validar a autenticação transversal a um recurso](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticação do utilizador no local da floresta de recursos Azure AD DS

Deverá ter a máquina virtual do Windows Server unida ao domínio de recursos de domínio gerido. Utilize esta máquina virtual para testar o utilizador no local que pode autenticar numa máquina virtual.

1. Ligue-se ao Windows Server VM ligado à floresta de recursos de domínio gerida utilizando o Ambiente de Trabalho Remoto e as credenciais de administrador de domínio gerido. Se tiver um erro de autenticação de nível de rede (NLA), verifique se a conta de utilizador utilizada não é uma conta de utilizador de domínio.

    > [!TIP]
    > Para ligar de forma segura aos seus VMs aderidos aos Serviços de Domínio AD Azure, pode utilizar o [Serviço de Anfitriões Azure Bastion](../bastion/bastion-overview.md) em regiões de Azure suportadas.

1. Abra um pedido de comando e utilize o `whoami` comando para mostrar o nome distinto do utilizador atualmente autenticado:

    ```console
    whoami /fqdn
    ```

1. Utilize o `runas` comando para autenticar como utilizador a partir do domínio no local. No comando seguinte, `userUpn@trusteddomain.com` substitua-o pela UPN de um utilizador do domínio confiável no local. O comando solicita-lhe a palavra-passe do utilizador:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se a autenticação for bem sucedida, abre-se um novo pedido de comando. O título do novo pedido de comando inclui `running as userUpn@trusteddomain.com` .
1. Utilização `whoami /fqdn` no novo comando para visualizar o nome distinto do utilizador autenticado a partir do Diretório Ativo no local.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Recursos de acesso na floresta de recursos Azure AD DS utilizando o utilizador no local

Utilizando o VM do Servidor do Windows a que se junta a floresta de recursos de domínio gerido, é possível testar o cenário em que os utilizadores possam aceder aos recursos alojados na floresta de recursos quando autenticam a partir de computadores no domínio do local com utilizadores do domínio no local. Os exemplos que se seguem mostram-lhe como criar e testar vários cenários comuns.

#### <a name="enable-file-and-printer-sharing"></a>Ativar a partilha de ficheiros e impressoras

1. Ligue-se ao Windows Server VM ligado à floresta de recursos de domínio gerida utilizando o Ambiente de Trabalho Remoto e as credenciais de administrador de domínio gerido. Se tiver um erro de autenticação de nível de rede (NLA), verifique se a conta de utilizador utilizada não é uma conta de utilizador de domínio.

    > [!TIP]
    > Para ligar de forma segura aos seus VMs aderidos aos Serviços de Domínio AD Azure, pode utilizar o [Serviço de Anfitriões Azure Bastion](../bastion/bastion-overview.md) em regiões de Azure suportadas.

1. Abra **as definições do Windows** e, em seguida, procure e selecione o Centro de Rede e **Partilha**.
1. Escolha a opção para Alterar definições **de partilha avançadas.**
1. No **perfil** de domínio , **selecione Ligue a partilha de ficheiros e impressoras** e, em seguida, **guarde as alterações**.
1. Fechar **rede e partilhar centro.**

#### <a name="create-a-security-group-and-add-members"></a>Criar um grupo de segurança e adicionar membros

1. Abra **Utilizadores e Computadores do Active Directory**.
1. Selecione o nome de domínio à direita, escolha **Novo** e, em seguida, selecione **Unidade Organizacional**.
1. Na caixa de nomes, escreva *LocalObjects,* em seguida, selecione **OK**.
1. Selecione e clique à direita **LocaObjects** no painel de navegação. Selecione **Novo** e, em seguida, **Grupo**.
1. Digite *FileServerAccess* na caixa **de nomes do grupo.** Para o Âmbito do **Grupo**, selecione **Domain local**, em seguida, escolha **OK**.
1. No painel de conteúdo, clique **duas vezes em FileServerAccess**. Selecione **Os Membros**, escolha **adicionar** e, em seguida, selecione **Localizações**.
1. Selecione o seu Diretório Ativo no local a partir da vista **Localização** e, em seguida, escolha **OK**.
1. Digite *Utilizadores de Domínio* na **Introdução dos nomes do objeto para selecionar a** caixa. Selecione **Verificar Nomes**, forneça credenciais para o Diretório Ativo no local e, em seguida, selecione **OK**.

    > [!NOTE]
    > Tens de fornecer credenciais porque a relação de confiança é apenas uma maneira. Isto significa que os utilizadores do domínio gerido não podem aceder a recursos ou procurar utilizadores ou grupos no domínio confiável (no local).

1. O grupo de **Utilizadores** de Domínio saindo do seu Diretório Ativo no local deve ser membro do grupo **FileServerAccess.** Selecione **OK** para salvar o grupo e feche a janela.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Criar uma partilha de ficheiros para acesso cross-forest

1. No Windows Server VM juntou-se à floresta de recursos de domínio gerido, criar uma pasta e fornecer nome como *CrossForestShare*.
1. Selecione à direita a pasta e escolha **propriedades.**
1. Selecione o separador **Segurança** e, em seguida, escolha **Editar.**
1. Nas permissões para caixa de diálogo *CrossForestShare,* selecione **Add**.
1. Digite *FileServerAccess* in **Introduza os nomes do objeto para selecionar** e, em seguida, selecione **OK**.
1. *Selecione FileServerAccess* da lista **de grupos ou nomes de utilizadores.** Na lista **de Permissões para FileServerAccess,** escolha *Permitir* as permissões **de Modificação** e **Escrita** e, em seguida, selecione **OK**.
1. Selecione o separador **Partilhar** e, em seguida, escolha **Partilha Avançada...**
1. Escolha **Partilhar esta pasta** e, em seguida, introduzir um nome memorável para a partilha de **ficheiros** em nome de Partilha como *CrossForestShare*.
1. Selecione **Permissões**. Na lista **de Permissões para Todos,** escolha **Permitir** a permissão **de Alteração.**
1. Selecione **OK** duas vezes e, em seguida, **Feche**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Validar a autenticação transversal a um recurso

1. Inscreva-se num computador Windows ligado ao seu Ative Directy no local utilizando uma conta de utilizador a partir do seu Ative Directy no local.
1. Utilizando o **Windows Explorer,** ligue-se à partilha que criou utilizando o nome de anfitrião totalmente qualificado e a partilha como `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Para validar a permissão de escrita, selecione à direita na pasta, escolha **Novo** e, em seguida, selecione **Documento de Texto**. Utilize o nome predefinido **Novo Documento de Texto**.

    Se as permissões de escrita forem definidas corretamente, é criado um novo documento de texto. Os seguintes passos abrirão, editarão e eliminarão o ficheiro conforme apropriado.
1. Para validar a permissão de leitura, abra **novo documento de texto**.
1. Para validar a permissão de modificação, adicione texto ao ficheiro e feche o **Bloco de Notas**. Quando solicitado para guardar alterações, escolha **Guardar**.
1. Para validar a permissão de eliminação, selecione **o novo documento de texto** e escolha **Eliminar**. Escolha **Sim** para confirmar a eliminação de ficheiros.

## <a name="update-or-remove-outbound-forest-trust"></a>Atualizar ou remover a confiança da floresta de saída

Se precisar de atualizar uma floresta de saída de ida existente a partir do domínio gerido, pode utilizar os `Get-AaddsResourceForestTrusts` scripts e `Set-AaddsResourceForestTrust` scripts. Estes scripts ajudam em cenários onde você quer atualizar o nome amigável da confiança da floresta ou senha de confiança. Para remover uma confiança de saída de ida do domínio gerido, pode utilizar o `Remove-AaddsResourceForestTrust` script. Deve remover manualmente a confiança da floresta de entrada única na floresta AD DS associada.

### <a name="update-a-forest-trust"></a>Atualizar um fundo florestal

Em funcionamento normal, a floresta de domínio gerida e a floresta no local negoceiam um processo regular de atualização de senha entre si. Isto faz parte do processo normal de segurança da relação de confiança da AD DS. Não precisa de rodar manualmente a palavra-passe de confiança a menos que a relação de confiança tenha sofrido um problema e pretenda reiniciar manualmente uma palavra-passe conhecida. Para obter mais informações, consulte [as alterações de senha de objeto de domínio fidedignos](concepts-forest-trust.md#tdo-password-changes).

Os seguintes passos de exemplo mostram-lhe como atualizar uma relação de confiança existente se precisar de redefinir manualmente a palavra-passe de confiança de saída:

1. Instale os `Get-AaddsResourceForestTrusts` scripts e `Set-AaddsResourceForestTrust` scripts da [Galeria PowerShell][powershell-gallery] utilizando o [cmdlet Install-Script:][Install-Script]

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. Antes de atualizar uma confiança existente, primeiro obtenha o recurso fiduciário usando o `Get-AaddsResourceForestTrusts` script. No exemplo seguinte, a confiança existente é atribuída a um objeto denominado *ExistingTrust*. Especifique o seu próprio nome de floresta de domínio gerido e o nome da floresta no local para atualizar:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. Para atualizar a palavra-passe de confiança existente, utilize o `Set-AaddsResourceForestTrust` script. Especifique o objeto de confiança existente do passo anterior e, em seguida, uma nova senha de relacionamento de confiança. Nenhuma complexidade de senha é aplicada pelo PowerShell, por isso certifique-se de que gera e utiliza uma palavra-passe segura para o seu ambiente.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Apagar um fundo florestal

Se já não precisar da confiança florestal de ida e saída do domínio gerido para uma floresta AD DS no local, pode removê-la. Certifique-se de que nenhuma aplicação ou serviços deve autenticar-se contra a floresta AD DS no local antes de remover a confiança. Deve remover manualmente a confiança de entrada de ida na floresta AD DS no local, também.

1. Instale o `Remove-AaddsResourceForestTrust` script a partir da Galeria [PowerShell][powershell-gallery] utilizando o cmdlet [Install-Script:][Install-Script]

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. Agora remova a confiança da floresta usando o `Remove-AaddsResourceForestTrust` guião. No exemplo seguinte, o fundo denominado *myAzureADDSTrust* entre a floresta de domínio gerida chamada *aaddscontoso.com* e *onprem.contoso.com* floresta no local é removido. Especifique o seu próprio nome de floresta de domínio gerido e o nome da floresta no local para remover:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Para remover a confiança de entrada unidirecionais da floresta AD DS no local, ligue-se a um computador de gestão com acesso à floresta AD DS no local e complete os seguintes passos:

1. Selecione **Iniciar | Ferramentas Administrativas | Domínios e Fidedignidades do Diretório Ativo**
1. Domínio selecionado à direita, como *onprem.contoso.com,* selecione **Properties**
1. Escolha o separador **Trusts** e, em seguida, selecione a confiança de entrada existente na floresta de domínio gerida.
1. Selecione **Remover** e, em seguida, confirme que deseja remover a confiança recebida.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Criar uma floresta de recursos de domínio gerida usando a Azure PowerShell
> * Criar uma confiança florestal de ida no domínio gerido usando a Azure PowerShell
> * Configure o DNS num ambiente AD DS no local para suportar a conectividade de domínio gerido
> * Criar uma confiança florestal unidireccionária num ambiente de DS AD no local
> * Teste e valide a relação de confiança para a autenticação e acesso a recursos

Para obter mais informações conceptuais sobre os tipos de floresta em [][concepts-trust] Azure AD DS, veja [o que são as florestas de recursos?][concepts-forest]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/