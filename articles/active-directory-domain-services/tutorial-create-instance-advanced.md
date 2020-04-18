---
title: Tutorial - Criar uma instância de Serviços de Domínio de Diretório Ativo Azure [ Microsoft Docs
description: Neste tutorial, aprende-se a criar e configurar uma instância de Serviços de Domínio de Diretório Ativo Azure e especifica opções avançadas de configuração utilizando o portal Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: f2d7f1725623dcc031f3c2b36bacd6dbc9ad339d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639959"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Tutorial: Crie e configure uma instância de Serviços de Domínio de Diretório Ativo Azure com opções avançadas de configuração

Os Serviços de Domínio de Diretório Ativo Azure (Azure AD DS) fornecem serviços de domínio geridos, tais como a adesão ao domínio, política de grupo, autenticação LDAP, Kerberos/NTLM que é totalmente compatível com o Diretório Ativo do Windows Server. Você consome estes serviços de domínio sem implementar, gerir e remendar controladores de domínio por si mesmo. A Azure AD DS integra-se com o seu inquilino Azure AD existente. Esta integração permite que os utilizadores assinem usando as suas credenciais corporativas, e você pode usar grupos e contas de utilizador existentes para garantir o acesso aos recursos.

Pode [criar um domínio gerido utilizando opções de configuração predefinidas][tutorial-create-instance] para a sincronização e sincronização em rede ou definir manualmente estas definições. Este tutorial mostra-lhe como definir essas opções avançadas de configuração para criar e configurar uma instância Azure AD DS usando o portal Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure as definições de DNS e rede virtual para um domínio gerido
> * Criar uma instância do Azure AD DS
> * Adicionar utilizadores administrativos à gestão de domínios
> * Ativar a sincronização de palavras-passe de hash

Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Você precisa de privilégios *de administrador global* no seu inquilino Azure AD para permitir o Azure AD DS.
* Você precisa de privilégios *Contributivos* na sua subscrição Azure para criar os recursos DS Azure necessários.

Apesar de não ser necessário para o Azure AD DS, é recomendado configurar o reset de [senha de autosserviço (SSPR)][configure-sspr] para o inquilino Azure AD. Os utilizadores podem alterar a sua palavra-passe sem SSPR, mas o SSPR ajuda se esquecerem a sua palavra-passe e precisarem de a redefinir.

> [!IMPORTANT]
> Depois de criar um domínio gerido pelo Azure AD DS, não pode então mover a instância para um grupo de recursos diferente, rede virtual, subscrição, etc. Tenha o cuidado de selecionar a subscrição mais adequada, grupo de recursos, região e rede virtual quando implementar a instância Azure AD DS.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, cria e configura a instância Azure AD DS utilizando o portal Azure. Para começar, inicie a inscrição no [portal Azure.](https://portal.azure.com)

## <a name="create-an-instance-and-configure-basic-settings"></a>Criar uma instância e configurar definições básicas

Para lançar o assistente enable **Azure AD Domain Services,** complete os seguintes passos:

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
1. Insira *os Serviços de Domínio* na barra de pesquisa e, em seguida, escolha os Serviços de Domínio *Azure AD* a partir das sugestões de pesquisa.
1. Na página de Serviços de Domínio AD Azure, selecione **Criar**. O assistente **enable Azure AD Domain Services** é lançado.
1. Selecione a **Subscrição** Azure na qual deseja criar o domínio gerido.
1. Selecione o **grupo Derecursos** ao qual deve pertencer o domínio gerido. Escolha **criar um novo** ou selecione um grupo de recursos existente.

Quando cria uma instância Azure AD DS, especifica um nome DNS. Existem algumas considerações quando escolhe este nome DNS:

* **Nome de domínio incorporado:** Por predefinição, é utilizado o nome de domínio incorporado do diretório (um *sufixo .onmicrosoft.com).* Se desejar ativar o acesso lDAP seguro ao domínio gerido através da internet, não pode criar um certificado digital para garantir a ligação com este domínio predefinido. A Microsoft é proprietária do domínio *.onmicrosoft.com,* pelo que uma Autoridade de Certificados (CA) não emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, tipicamente um que você já possui e é routable. Quando utiliza um domínio personalizado e roteável, o tráfego pode fluir corretamente conforme necessário para suportar as suas aplicações.
* **Sufixos de domínio não-enrpreensíveis:** Recomendamos geralmente que evite um sufixo de nome de domínio não rotativa, como *contoso.local*. O sufixo *.local* não é repreensível e pode causar problemas com a resolução do DNS.

> [!TIP]
> Se criar um nome de domínio personalizado, tenha cuidado com os espaços de nome DNS existentes. É aconselhável usar um nome de domínio separado de qualquer espaço de nome DNS existente ou no local.
>
> Por exemplo, se tiver um espaço de nome DNS existente de *contoso.com,* crie um domínio gerido por Azure AD DS com o nome de domínio personalizado de *aaddscontoso.com*. Se precisar de utilizar LDAP seguro, tem de registar e possuir este nome de domínio personalizado para gerar os certificados necessários.
>
> Poderá ser necessário criar alguns registos DNS adicionais para outros serviços no seu ambiente, ou avançados de DNS condicionados entre os espaços de nome DNS existentes no seu ambiente. Por exemplo, se executar um webserver que acolhe um site usando o nome DNS raiz, pode haver conflitos de nomeação que requerem entradas dNS adicionais.
>
> Nestes tutoriais e artigos como fazer, o domínio personalizado da *aaddscontoso.com* é usado como um exemplo curto. Em todos os comandos, especifique o seu próprio nome de domínio.

Aplicam-se igualmente as seguintes restrições de nome dNS:

* **Restrições de prefixo de domínio:** Não se pode criar um domínio gerido com um prefixo superior a 15 caracteres. O prefixo do seu nome de domínio especificado (como *aaddscontoso* no nome de domínio *aaddscontoso.com)* deve conter 15 ou menos caracteres.
* **Conflitos de nome de rede:** O nome de domínio DNS para o seu domínio gerido não deve já existir na rede virtual. Especificamente, verifique os seguintes cenários que levariam a um conflito de nomes:
    * Se já tem um domínio de Diretório Ativo com o mesmo nome de domínio DNS na rede virtual Azure.
    * Se a rede virtual onde planeia ativar o domínio gerido tiver uma ligação VPN com a sua rede no local. Neste cenário, certifique-se de que não tem um domínio com o mesmo nome de domínio DNS na sua rede no local.
    * Se tiver um serviço de nuvem Azure existente com esse nome na rede virtual Azure.

Complete os campos na janela *Basics* do portal Azure para criar uma instância Azure AD DS:

1. Introduza um nome de **domínio DNS** para o seu domínio gerido, tendo em conta os pontos anteriores.
1. Escolha o **Local** Azure no qual deve ser criado o domínio gerido. Se escolher uma região que apoie zonas de disponibilidade, os recursos Da DS Azure são distribuídos por zonas para despedimentoadicional.

    As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas.

    Não há nada para configurar para que o Azure AD DS seja distribuído por zonas. A plataforma Azure lida automaticamente com a distribuição de recursos da zona. Para mais informações e para ver disponibilidade da região, consulte [As Zonas de Disponibilidade em Azure?][availability-zones]

1. O **SKU** determina o desempenho, a frequência de backup e o número máximo de fundos florestais que pode criar. Pode alterar o SKU depois de criado o domínio gerido se o seu negócio exigir ou requisitos mudar. Para mais informações, consulte os [conceitos Azure AD DS SKU][concepts-sku].

    Para este tutorial, selecione o *SKU Padrão.*
1. Uma *floresta* é uma construção lógica usada pelos Serviços de Domínio de Diretório Ativo para agrupar um ou mais domínios. Por padrão, um domínio gerido por Azure AD DS é criado como uma floresta *de utilizadores.* Este tipo de floresta sincroniza todos os objetos da AD Azure, incluindo quaisquer contas de utilizador criadas num ambiente AD DS no local. Uma floresta de *recursos* apenas sincroniza utilizadores e grupos criados diretamente em Azure AD. As florestas de recursos estão atualmente em pré-visualização. Para obter mais informações sobre as florestas de *Recursos,* incluindo por que você pode usar uma e como criar fundos florestais com domínios AD DS no local, consulte a visão geral das florestas de [recursos da AD DS azure][resource-forests].

    Para este tutorial, opte por criar uma floresta *de Utilizadores.*

    ![Configure as definições básicas para uma instância de Serviços de Domínio AD Azure](./media/tutorial-create-instance-advanced/basics-window.png)

1. Para configurar manualmente opções adicionais, escolha **Seguinte - Networking**. Caso contrário, selecione **Review + crie** para aceitar as opções de configuração predefinidas e, em seguida, salte para a secção para [implementar o seu domínio gerido](#deploy-the-managed-domain). As seguintes predefinições são configuradas quando escolher esta opção criar:

    * Cria uma rede virtual chamada *aadds-vnet* que utiliza a gama de endereços IP de *10.0.1.0/24*.
    * Cria uma sub-rede *denominada aadds-subnet* utilizando a gama de endereços IP de *10.0.1.0/24*.
    * Sincroniza *todos os* utilizadores do Azure AD para o domínio gerido pelo Azure AD DS.

## <a name="create-and-configure-the-virtual-network"></a>Criar e configurar a rede virtual

Para proporcionar conectividade, é necessária uma rede virtual Azure e uma subnet dedicada. O Azure AD DS está ativado nesta subnet de rede virtual. Neste tutorial, cria-se uma rede virtual, embora possa optar por utilizar uma rede virtual existente. Em qualquer uma das abordagens, deve criar uma subrede dedicada para utilização pelo Azure AD DS.

Algumas considerações para esta subnet de rede virtual dedicada incluem as seguintes áreas:

* A sub-rede deve ter pelo menos 3-5 endereços IP disponíveis na sua gama de endereços para suportar os recursos Da DS Azure.
* Não selecione a subnet *Gateway* para a implementação de DS Azure. Não é suportado para implantar o Azure AD DS numa subnet *gateway.*
* Não desloque outras máquinas virtuais para a sub-rede. Aplicações e VMs usam frequentemente grupos de segurança de rede para garantir conectividade. Executar estas cargas de trabalho numa subnet separada permite-lhe aplicar esses grupos de segurança da rede sem perturbar a conectividade com o seu domínio gerido.
* Não pode mover o seu domínio gerido para uma rede virtual diferente depois de ativar o Azure AD DS.

Para obter mais informações sobre como planear e configurar a rede virtual, consulte considerações de [networking para os Serviços de Domínio do Diretório Ativo azure.][network-considerations]

Complete os campos na janela da *Rede* da seguinte forma:

1. Na página **da Rede,** escolha uma rede virtual para implantar O DS Azure no menu suspenso ou selecione **Criar novo**.
    1. Se optar por criar uma rede virtual, insira um nome para a rede virtual, como o *myVnet,* em seguida, forneça uma gama de endereços, como *10.0.1.0/24*.
    1. Crie uma subnet dedicada com um nome claro, como *DomainServices*. Forneça um intervalo de endereços, como *10.0.1.0/24*.

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Certifique-se de que escolhe um intervalo de endereços dentro do seu intervalo de endereços IP privado. Os intervalos de endereços IP que não possui que estão no espaço de endereço público causam erros dentro do Azure AD DS.

1. Selecione uma subnet de rede virtual, como *DomainServices*.
1. Quando estiver pronto, escolha **Next - Administração**.

## <a name="configure-an-administrative-group"></a>Configurar um grupo administrativo

Um grupo administrativo especial chamado *AAD DC Administrators* é utilizado para a gestão do domínio Azure AD DS. Aos membros deste grupo são concedidas permissões administrativas em VMs que são unidas ao domínio gerido. Em VMs de domínio, este grupo é adicionado ao grupo de administradores locais. Os membros deste grupo também podem usar o Remote Desktop para ligar remotamente a VMs unidos pelo domínio.

Não tem permissões de Administrador de *Domínio* ou *Administrador de Empresa* num domínio gerido utilizando O DS Azure. Estas permissões são reservadas pelo serviço e não são disponibilizadas aos utilizadores dentro do inquilino. Em vez disso, o grupo de administradores da *AAD DC* permite-lhe realizar algumas operações privilegiadas. Estas operações incluem pertencer ao grupo de administração em VMs filiados em domínios e configurar a Política de Grupo.

O assistente cria automaticamente o grupo *de administradores aAD DC* no seu diretório Azure AD. Se tiver um grupo existente com este nome no seu diretório Azure AD, o assistente seleciona este grupo. Pode optar opcionalmente por adicionar utilizadores adicionais a este grupo de *Administradores aAD DC* durante o processo de implementação. Estes passos podem ser concluídos mais tarde.

1. Para adicionar utilizadores adicionais a este grupo de *Administradores aAD DC,* selecione Gerir a **adesão ao grupo**.

    ![Configure a adesão ao grupo de administradores da AAD DC](./media/tutorial-create-instance-advanced/admin-group.png)

1. Selecione o botão **adicionar membros** e, em seguida, procure e selecione utilizadores a partir do seu diretório Azure AD. Por exemplo, procure a sua própria conta e adicione-a ao grupo de *Administradores da AAD DC.*
1. Se desejar, altere ou adicione destinatários adicionais para notificações quando houver alertas no domínio gerido pelo Azure AD DS que requerem atenção.
1. Quando estiver pronto, escolha **Seguinte - Sincronização**.

## <a name="configure-synchronization"></a>Configurar a sincronização

O Azure AD DS permite sincronizar *todos os* utilizadores e grupos disponíveis em Azure AD, ou uma sincronização *de* apenas grupos específicos. Se optar por sincronizar *todos os* utilizadores e grupos, não poderá optar mais tarde por realizar apenas uma sincronização com âmbito de aplicação. Para obter mais informações sobre a sincronização por via geminada, consulte a [sincronização de sincronização dos Serviços de Domínio da AD Azure.][scoped-sync]

1. Para este tutorial, opte por sincronizar **todos os** utilizadores e grupos. Esta escolha de sincronização é a opção padrão.

    ![Realizar uma sincronização completa de utilizadores e grupos a partir de Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Selecione **Rever + criar**.

## <a name="deploy-the-managed-domain"></a>Implementar o domínio gerido

Na página **resumo** do assistente, reveja as definições de configuração para o domínio gerido. Pode voltar a qualquer passo do assistente para fazer alterações. Para reimplantar um domínio gerido pelo Azure AD dS para um inquilino Azure AD diferente de uma forma consistente usando estas opções de configuração, também pode **descarregar um modelo para automação**.

1. Para criar o domínio gerido, selecione **Criar**. É apresentada uma nota de que certas opções de configuração como o nome DNS ou a rede virtual não podem ser alteradas uma vez que o Azure AD DS gerido tenha sido criado. Para continuar, selecione **OK**.
1. O processo de provisionamento do seu domínio gerido pode demorar até uma hora. Uma notificação é apresentada no portal que mostra o progresso da sua implementação azure AD DS. Selecione a notificação para ver progressos detalhados para a implementação.

    ![Notificação no portal Azure da implantação em curso](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Selecione o seu grupo de recursos, como o *myResourceGroup,* e depois escolha a sua instância Azure AD DS da lista de recursos Azure, como *aaddscontoso.com*. O separador **Overview** mostra que o domínio gerido está atualmente *a ser implantado*. Não pode configurar o domínio gerido até que esteja totalmente provisionado.

    ![Estatuto dos Serviços de Domínio durante o estado de provisionamento](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Quando o domínio gerido está totalmente provisionado, o separador **Overview** mostra o estado de domínio como *Execução*.

    ![Estatuto dos Serviços de Domínio uma vez aprovisionado com sucesso](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

O domínio gerido está associado ao seu inquilino Azure AD. Durante o processo de provisionamento, o Azure AD DS cria duas aplicações empresariais chamadas *Serviços* de Controlador de Domínio e Serviços de Controlador de *Domínio AzureActiveyDomainno* no inquilino DaD Azure. Estas Aplicações Empresariais são necessárias para servir o seu domínio gerido. Não apague estas aplicações.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Atualizar as definições de DNS para a Azure Virtual Network

Com o Azure AD DS implementado com sucesso, configurar agora a rede virtual para permitir que outros VMs e aplicações conectados utilizem o domínio gerido. Para fornecer esta conectividade, atualize as definições do servidor DNS para a sua rede virtual para apontar para os dois endereços IP onde o Azure AD DS está implantado.

1. O separador **Overview** para o seu domínio gerido mostra alguns **passos de configuração exigidos**. O primeiro passo de configuração é atualizar as definições do servidor DNS para a sua rede virtual. Uma vez configuradas corretamente as definições de DNS, este passo já não é mostrado.

    Os endereços listados são os controladores de domínio para utilização na rede virtual. Neste exemplo, estes endereços são *10.1.0.4* e *10.1.0.5*. Mais tarde, poderá encontrar estes endereços IP no separador **Propriedades.**

    ![Configure as definições de DNS para a sua rede virtual com os endereços IP dos Serviços de Domínio Da AD Azure](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Para atualizar as definições do servidor DNS para a rede virtual, selecione o botão **Configurar.** As definições de DNS são configuradas automaticamente para a sua rede virtual.

> [!TIP]
> Se selecionar uma rede virtual existente nos passos anteriores, quaisquer VMs ligados à rede só obtêm as novas definições de DNS após o reinício. Pode reiniciar os VMs utilizando o portal Azure, o Azure PowerShell ou o Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Ativar as contas de utilizador para O DS Azure

Para autenticar os utilizadores no domínio gerido, o Azure AD DS necessita de hashes de senha num formato adequado para nt LAN Manager (NTLM) e autenticação Kerberos. A Azure AD não gera nem armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até ativar o Azure AD DS para o seu inquilino. Por razões de segurança, a Azure AD também não armazena credenciais de senha em formato de texto claro. Portanto, a Azure AD não pode gerar automaticamente estas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos utilizadores.

> [!NOTE]
> Uma vez configurados adequadamente, as hashes de senha utilizáveis são armazenadas no domínio gerido pelo Azure AD DS. Se eliminar o domínio gerido pelo Azure AD DS, também são eliminados quaisquer hashes de senha armazenados nessa altura. As informações credenciais sincronizadas em Azure AD não podem ser reutilizadas se criar mais tarde um domínio gerido por Azure AD DS - tem de reconfigurar a sincronização de hash de palavra-passe para armazenar novamente as hashes de senha. Os VMs ou utilizadores anteriormente associados ao domínio não poderão autenticar imediatamente - o Azure AD precisa de gerar e armazenar as hashes de senha no novo domínio gerido pela AD DS do Azure. Para mais informações, consulte o processo de sincronização de [hash password para Azure AD DS e Azure AD Connect][password-hash-sync-process].

Os passos para gerar e armazenar estas hashes de senha são diferentes para contas de utilizadores apenas na nuvem criadas em contas De AD Azure versus utilizadores que são sincronizadas a partir do seu diretório no local usando O Azure AD Connect. Uma conta de utilizador apenas na cloud é uma conta que foi criada no diretório do Azure AD com o portal do Azure ou os cmdlets do PowerShell do Azure AD. Estas contas de utilizador não são sincronizadas a partir de um diretório no local. Neste tutorial, vamos trabalhar com uma conta de utilizador básica apenas na nuvem. Para obter mais informações sobre os passos adicionais necessários para utilizar o Azure AD Connect, consulte [sincronize hashes de palavra-passe para contas][on-prem-sync]de utilizador sincronizadas desde o seu AD no local até ao seu domínio gerido .

> [!TIP]
> Se o seu inquilino Azure AD tiver uma combinação de utilizadores e utilizadores apenas em nuvem a partir do seu AD no local, você precisa completar ambos os conjuntos de passos.

Para contas de utilizadores apenas na nuvem, os utilizadores devem alterar as suas palavras-passe antes de poderem utilizar o Azure AD DS. Este processo de alteração de palavra-passe faz com que as hashes de senha para a autenticação Kerberos e NTLM sejam geradas e armazenadas em Azure AD. A conta não é sincronizada de Azure AD para Azure AD DS até que a palavra-passe seja alterada. Ou expira as palavras-passe para todos os utilizadores da nuvem do inquilino que precisam de usar O DS Azure, que obriga a uma alteração de palavra-passe no próximo início de sessão, ou instrua os utilizadores da nuvem a alterarmanualmente as suas palavras-passe. Para este tutorial, vamos alterar manualmente uma palavra-passe do utilizador.

Antes de um utilizador poder redefinir a sua palavra-passe, o inquilino DaD Azure deve ser [configurado para redefinir a palavra-passe de autosserviço][configure-sspr].

Para alterar a palavra-passe para um utilizador apenas na nuvem, o utilizador deve completar os seguintes passos:

1. Aceda à página do Painel [https://myapps.microsoft.com](https://myapps.microsoft.com)de Acesso AD Azure em .
1. No canto superior direito, selecione o seu nome e, em seguida, escolha **o Perfil** a partir do menu suspenso.

    ![Selecionar perfil](./media/tutorial-create-instance-advanced/select-profile.png)

1. Na página **perfil,** selecione **Alterar a palavra-passe**.
1. Na página de **alterar palavra-passe,** introduza a sua senha (antiga) existente e, em seguida, introduza e confirme uma nova palavra-passe.
1. Selecione **Submeter**.

Demora alguns minutos depois de ter mudado a sua palavra-passe para que a nova senha seja utilizável no Azure AD DS e para iniciar sessão com sucesso em computadores unidos ao domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configure as definições de DNS e rede virtual para um domínio gerido
> * Criar uma instância do Azure AD DS
> * Adicionar utilizadores administrativos à gestão de domínios
> * Ativar as contas do utilizador para O DS Azure e gerar hashes de senha

Para ver este domínio gerido em ação, crie e junte uma máquina virtual ao domínio.

> [!div class="nextstepaction"]
> [Junte-se a uma máquina virtual do Windows Server ao seu domínio gerido](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
