---
title: Tutorial - Criar um Azure Ative Directory Domain Services gerido domínio / Microsoft Docs
description: Neste tutorial, aprende a criar e configurar um domínio gerido pelos Serviços de Domínio do Diretório Ativo Azure e especificar opções de configuração avançadas utilizando o portal Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 78eef9c84bb7610b067855b22a3fa0f51bf08253
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024796"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain-with-advanced-configuration-options"></a>Tutorial: Criar e configurar um Azure Ative Directory Domain Services gerido domínio com opções de configuração avançadas

Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a junção de domínio, política de grupo, autenticação de LDAP, Kerberos/NTLM que é totalmente compatível com o Windows Server Ative Directory. Você consome estes serviços de domínio sem implantar, gerir e remendar controladores de domínio si mesmo. A Azure AD DS integra-se com o seu inquilino AZure AD existente. Esta integração permite que os utilizadores assinem a utilização das suas credenciais corporativas, podendo utilizar grupos e contas de utilizador existentes para garantir o acesso aos recursos.

Pode [criar um domínio gerido utilizando opções de configuração predefinidos][tutorial-create-instance] para networking e sincronização, ou definir manualmente estas definições. Este tutorial mostra-lhe como definir essas opções avançadas de configuração para criar e configurar um domínio gerido AZure AD DS utilizando o portal Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar configurar DNS e configurações de rede virtuais para um domínio gerido
> * Criar um domínio gerido
> * Adicionar utilizadores administrativos à gestão de domínios
> * Ativar a sincronização de palavras-passe de hash

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Você precisa de privilégios *de administrador global* no seu inquilino AZure AD para ativar Azure AD DS.
* Precisa de privilégios *colaboradores* na sua subscrição Azure para criar os recursos Azure AD DS necessários.

Embora não seja necessário para o Azure AD DS, é recomendado [configurar o reset da palavra-passe de autosserviço (SSPR)][configure-sspr] para o inquilino Azure AD. Os utilizadores podem alterar a sua palavra-passe sem SSPR, mas a SSPR ajuda se esquecerem a sua palavra-passe e precisarem de a redefinir.

> [!IMPORTANT]
> Depois de criar um domínio gerido, não pode então mover o domínio gerido para um grupo de recursos diferente, rede virtual, subscrição, etc. Tenha o cuidado de selecionar a subscrição mais adequada, grupo de recursos, região e rede virtual quando implementar o domínio gerido.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, cria-se e configura o domínio gerido utilizando o portal Azure. Para começar, inicie primeiro o sinal no [portal Azure.](https://portal.azure.com)

## <a name="create-a-managed-domain-and-configure-basic-settings"></a>Criar um domínio gerido e configurar configurações básicas

Para lançar o assistente **de Serviços de Domínio Azure Ad Enable,** complete os seguintes passos:

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
1. Introduza *os Serviços de Domínio* na barra de pesquisa e, em seguida, escolha os Serviços de Domínio *Azure AD* a partir das sugestões de pesquisa.
1. Na página Azure AD Domain Services, selecione **Criar**. É lançado o assistente **de Serviços de Domínio Enable Azure AD.**
1. Selecione a **Subscrição** Azure na qual pretende criar o domínio gerido.
1. Selecione o **grupo de Recursos** a que o domínio gerido deve pertencer. Opte por **criar um novo** ou selecione um grupo de recursos existente.

Quando cria um domínio gerido, especifica um nome DNS. Existem algumas considerações quando escolhe este nome DNS:

* **Nome de domínio incorporado:** Por predefinição, é utilizado o nome de domínio incorporado do diretório (um sufixo *.onmicrosoft.com).* Se desejar permitir o acesso seguro do LDAP ao domínio gerido através da internet, não é possível criar um certificado digital para garantir a ligação com este domínio predefinido. A Microsoft detém o domínio *.onmicrosoft.com,* pelo que uma Autoridade de Certificados (CA) não emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, tipicamente um que já possui e é roteável. Quando utiliza um domínio roteível e personalizado, o tráfego pode fluir corretamente conforme necessário para suportar as suas aplicações.
* **Sufixos de domínio não-encaminháveis:** Recomendamos geralmente que evite um sufixo de nome de domínio não-encaminhável, como *contoso.local*. O *sufixo .local* não é roteável e pode causar problemas com a resolução dns.

> [!TIP]
> Se criar um nome de domínio personalizado, tenha cuidado com os espaços de nome DNS existentes. Recomenda-se a utilização de um nome de domínio separado de qualquer espaço de nome DNS existente ou no local.
>
> Por exemplo, se tiver um espaço de nome DNS existente *de contoso.com,* crie um domínio gerido com o nome de domínio personalizado de *aaddscontoso.com*. Se precisar de utilizar lDAP seguro, deve registar-se e possuir este nome de domínio personalizado para gerar os certificados necessários.
>
> Poderá ser necessário criar alguns registos DNS adicionais para outros serviços no seu ambiente, ou reencaminhadores de DNS condicionados entre os espaços de nome DNS existentes no seu ambiente. Por exemplo, se executar um webserver que hospeda um site usando o nome DE DNS raiz, pode haver nomeação de conflitos que requerem entradas adicionais de DNS.
>
> Nestes tutoriais e artigos de como fazer, o domínio personalizado de *aaddscontoso.com* é usado como um exemplo curto. Em todos os comandos, especifique o seu próprio nome de domínio.

Aplicam-se também as seguintes restrições de nome DNS:

* **Restrições de prefixo de domínio:** Não é possível criar um domínio gerido com um prefixo superior a 15 caracteres. O prefixo do seu nome de domínio especificado (como *aaddscontoso* no *aaddscontoso.com* nome de domínio) deve conter 15 ou menos caracteres.
* **Conflitos de nomes de rede:** O nome de domínio DNS para o seu domínio gerido já não deveria existir na rede virtual. Especificamente, verifique os seguintes cenários que levariam a um conflito de nomes:
    * Se já tem um domínio ative Directory com o mesmo nome de domínio DNS na rede virtual Azure.
    * Se a rede virtual onde planeia ativar o domínio gerido tiver uma ligação VPN com a sua rede no local. Neste cenário, certifique-se de que não tem um domínio com o mesmo nome de domínio DNS na sua rede no local.
    * Se tiver um serviço em nuvem Azure existente com esse nome na rede virtual Azure.

Complete os campos na janela *Basics* do portal Azure para criar um domínio gerido:

1. Introduza um **nome de domínio DNS** para o seu domínio gerido, tendo em conta os pontos anteriores.
1. Escolha a **Localização** Azure em que o domínio gerido deve ser criado. Se escolher uma região que suporte Zonas de Disponibilidade, os recursos Azure AD DS são distribuídos por zonas para redundância adicional.

    > [!TIP]
    > As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas.
    >
    > Não há nada que possa configurar para que o Azure AD DS seja distribuído por zonas. A plataforma Azure lida automaticamente com a distribuição de recursos na zona. Para mais informações e para ver a disponibilidade da região, consulte [quais são as Zonas de Disponibilidade em Azure?][availability-zones]

1. O **SKU** determina o desempenho, a frequência de backup e o número máximo de fundos florestais que pode criar. Pode alterar o SKU após a criação do domínio gerido se as exigências ou requisitos do seu negócio mudarem. Para mais informações, consulte [os conceitos Azure AD DS SKU.][concepts-sku]

    Para este tutorial, selecione o *Standard* SKU.
1. Uma *floresta* é uma construção lógica usada pelos Serviços de Domínio do Diretório Ativo para agrupar um ou mais domínios. Por padrão, um domínio gerido é criado como uma floresta *de utilizadores.* Este tipo de floresta sincroniza todos os objetos a partir de Azure AD, incluindo quaisquer contas de utilizador criadas em um ambiente AD DS no local.

    Uma floresta *de recursos* apenas sincroniza utilizadores e grupos criados diretamente em Azure AD. As florestas de recursos estão atualmente em pré-visualização. Para obter mais informações sobre as florestas *de recursos,* incluindo por que você pode usar uma e como criar fidedignidades florestais com domínios AD DS no local, consulte a visão geral das [florestas de recursos AD DS AD.][resource-forests]

    Para este tutorial, opte por criar uma floresta *de utilizadores.*

    ![Configurar definições básicas para um domínio gerido por serviços de domínio Ad AD Azure](./media/tutorial-create-instance-advanced/basics-window.png)

1. Para configurar manualmente opções adicionais, escolha **Next - Networking**. Caso contrário, selecione **Rever + criar** para aceitar as opções de configuração predefinidos e, em seguida, saltar para a secção para Implementar o seu domínio [gerido](#deploy-the-managed-domain). As seguintes predefinições são configuradas quando escolhe esta opção de criar:

    * Cria uma rede virtual chamada *aadds-vnet* que utiliza o intervalo de endereço IP de *10.0.1.0/24*.
    * Cria uma sub-rede denominada *aadds-subnet* utilizando o intervalo de endereço IP de *10.0.1.0/24*.
    * Sincroniza *todos os* utilizadores do Azure AD no domínio gerido.

## <a name="create-and-configure-the-virtual-network"></a>Criar e configurar a rede virtual

Para fornecer conectividade, é necessária uma rede virtual Azure e uma sub-rede dedicada. O Azure AD DS está ativado nesta sub-rede de rede virtual. Neste tutorial, cria-se uma rede virtual, embora possa optar por utilizar uma rede virtual existente. Em qualquer uma das abordagens, deve criar uma sub-rede dedicada para utilização pela Azure AD DS.

Algumas considerações para esta sub-rede de rede virtual dedicada incluem as seguintes áreas:

* A sub-rede deve ter pelo menos 3-5 endereços IP disponíveis na sua gama de endereços para suportar os recursos Azure AD DS.
* Não selecione a sub-rede *Gateway* para a implantação do Azure AD DS. Não é suportado para colocar Azure AD DS numa sub-rede *Gateway.*
* Não coloque outras máquinas virtuais na sub-rede. Aplicações e VMs usam frequentemente grupos de segurança de rede para garantir a conectividade. Executar estas cargas de trabalho numa sub-rede separada permite aplicar esses grupos de segurança de rede sem perturbar a conectividade com o seu domínio gerido.
* Não é possível mover o seu domínio gerido para uma rede virtual diferente depois de ativar o Azure AD DS.

Para obter mais informações sobre como planear e configurar a rede virtual, consulte [considerações de networking para os Serviços de Domínio do Diretório Ativo Azure][network-considerations].

Complete os campos na janela *da rede* da seguinte forma:

1. Na página **'Rede',** escolha uma rede virtual para implementar Azure AD DS no menu suspenso ou selecione **Criar novo**.
    1. Se optar por criar uma rede virtual, insira um nome para a rede virtual, como *o myVnet,* em seguida, forneça uma gama de endereços, como *10.0.0.0/24*.
    1. Crie uma sub-rede dedicada com um nome claro, como *o DomainServices*. Fornecer um intervalo de endereços, como *10.0.1.0/24*.

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Certifique-se de escolher um intervalo de endereços que esteja dentro do seu alcance de endereço IP privado. Os intervalos de endereços IP que não possui que estão no espaço de endereço público causam erros dentro do Azure AD DS.

1. Selecione uma sub-rede de rede virtual, como *o DomainServices*.
1. Quando estiver pronto, escolha **Seguinte - Administração.**

## <a name="configure-an-administrative-group"></a>Configure um grupo administrativo

Um grupo administrativo especial chamado *AAD DC Administrators* é utilizado para a gestão do domínio Azure AD DS. Aos membros deste grupo são concedidas permissões administrativas em VMs que se unem ao domínio gerido. Em VMs ligados ao domínio, este grupo é adicionado ao grupo de administradores locais. Os membros deste grupo também podem utilizar o Remote Desktop para se ligarem remotamente aos VMs ligados ao domínio.

> [!IMPORTANT]
> Não tem permissões *de Administrador de Domínio* ou Administrador *Empresarial* num domínio gerido utilizando DS AD Azure. Estas permissões são reservadas pelo serviço e não são disponibilizadas aos utilizadores dentro do arrendatário.
>
> Em vez disso, o grupo *de administradores da AAD DC* permite-lhe realizar algumas operações privilegiadas. Estas operações incluem pertencer ao grupo de administração em VMs unidos pelo domínio, e configurar a Política de Grupo.

O assistente cria automaticamente o grupo *de administradores AAD DC* no seu diretório AD Azure. Se tiver um grupo existente com este nome no seu diretório AD Azure, o assistente seleciona este grupo. Pode optar opcionalmente por adicionar utilizadores adicionais a este grupo *de Administradores AAD DC* durante o processo de implementação. Estes passos podem ser concluídos mais tarde.

1. Para adicionar utilizadores adicionais a este grupo *de administradores AAD DC,* selecione **Gerir a adesão ao grupo**.

    ![Configure a filiação do grupo do grupo de administradores da AAD DC](./media/tutorial-create-instance-advanced/admin-group.png)

1. Selecione o botão **Adicionar membros** e, em seguida, procure e selecione utilizadores a partir do seu diretório AD Azure. Por exemplo, procure a sua própria conta e adicione-a ao grupo *de administradores da AAD DC.*
1. Se desejar, altere ou adicione destinatários adicionais para notificações quando há alertas no domínio gerido que requerem atenção.
1. Quando estiver pronto, escolha **Seguinte - Sincronização**.

## <a name="configure-synchronization"></a>Configurar a sincronização

O Azure AD DS permite sincronizar *todos os* utilizadores e grupos disponíveis no Azure AD, ou uma sincronização *de* grupos específicos. Se optar por sincronizar *todos os* utilizadores e grupos, não pode optar mais tarde por realizar apenas uma sincronização âmbito. Para obter mais informações sobre a sincronização de âmbito, consulte [a sincronização dos Serviços de Domínio AZure AD][scoped-sync].

1. Para este tutorial, opte por sincronizar **todos os** utilizadores e grupos. Esta escolha de sincronização é a opção padrão.

    ![Realizar uma sincronização completa de utilizadores e grupos da Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Selecione **Rever + criar**.

## <a name="deploy-the-managed-domain"></a>Implementar o domínio gerido

Na página **Resumo** do assistente, reveja as definições de configuração para o seu domínio gerido. Pode voltar a qualquer passo do assistente para fazer alterações. Para recolocar um domínio gerido para um inquilino AD Azure diferente de forma consistente usando estas opções de configuração, também pode **descarregar um modelo para automatização**.

1. Para criar o domínio gerido, selecione **Criar**. É apresentada uma nota segundo a qual determinadas opções de configuração, como o nome DNS ou a rede virtual, não podem ser alteradas uma vez que o Azure AD DS gerido foi criado. Para continuar, selecione **OK**.
1. O processo de provisionamento do seu domínio gerido pode demorar até uma hora. Uma notificação é apresentada no portal que mostra o progresso da sua implementação Azure AD DS. Selecione a notificação para ver progressos detalhados para a implantação.

    ![Notificação no portal Azure da implantação em curso](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Selecione o seu grupo de recursos, como *o myResourceGroup,* em seguida, escolha o seu domínio gerido na lista de recursos Azure, como *aaddscontoso.com*. O **separador Visão** Geral mostra que o domínio gerido está atualmente *a ser implementado.* Não se pode configurar o domínio gerido até que esteja totalmente a provisionado.

    ![Estado dos Serviços de Domínio durante o estado de avisão](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Quando o domínio gerido é totalmente provisionado, o **separador Visão Geral** mostra o estado do domínio como *Running*.

    ![Estado dos Serviços de Domínio uma vez a provisionado com sucesso](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

> [!IMPORTANT]
> O domínio gerido está associado ao seu inquilino AZure AD. Durante o processo de provisionamento, a Azure AD DS cria duas aplicações empresariais denominadas *Serviços de Controlador de Domínio* e *AzureActiveDirectorDomainControllerServices* no inquilino AZure AD. Estas Aplicações Empresariais são necessárias para atender o seu domínio gerido. Não apague estas aplicações.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Atualizar as definições de DNS para a Azure Virtual Network

Com o Azure AD DS implementado com sucesso, agora configurar a rede virtual para permitir que outros VMs e aplicações conectados utilizem o domínio gerido. Para fornecer esta conectividade, atualize as definições do servidor DNS para a sua rede virtual para apontar para os dois endereços IP onde o domínio gerido é implantado.

1. O **separador 'Visão Geral'** para o seu domínio gerido mostra alguns **passos de configuração necessários**. O primeiro passo de configuração é atualizar as definições do servidor DNS para a sua rede virtual. Uma vez configuradas corretamente as definições de DNS, este passo deixa de ser apresentado.

    Os endereços listados são os controladores de domínio para utilização na rede virtual. Neste exemplo, estes endereços são *10.0.1.4* e *10.0.1.5*. Mais tarde, pode encontrar estes endereços IP no separador **Propriedades.**

    ![Configurar as definições de DNS para a sua rede virtual com os endereços IP dos Serviços ip dos Serviços de Domínio Azure AD](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Para atualizar as definições do servidor DNS para a rede virtual, selecione o **botão Configure.** As definições de DNS são configuradas automaticamente para a sua rede virtual.

> [!TIP]
> Se selecionar uma rede virtual existente nos passos anteriores, quaisquer VMs ligados à rede só obtêm as novas definições de DNS após um reinício. Pode reiniciar os VM utilizando o portal Azure PowerShell ou o Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Ativar contas de utilizadores para Azure AD DS

Para autenticar os utilizadores no domínio gerido, o Azure AD DS necessita de hashes de senha num formato adequado para a autenticação do NT LAN Manager (NTLM) e da autenticação Kerberos. O Azure AD não gera ou armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até ativar o Azure AD DS para o seu inquilino. Por razões de segurança, a Azure AD também não armazena quaisquer credenciais de senha em formato de texto claro. Portanto, o Azure AD não pode gerar automaticamente estes hashes de palavra-passe NTLM ou Kerberos com base nas credenciais existentes dos utilizadores.

> [!NOTE]
> Uma vez configurados de forma adequada, os hashes de palavra-passe utilizáveis são armazenados no domínio gerido. Se eliminar o domínio gerido, qualquer hashes de palavra-passe armazenados nesse ponto também são eliminados.
>
> As informações de credenciais sincronizadas no Azure AD não podem ser reutilizadas se mais tarde criar um domínio gerido - tem de reconfigurar a sincronização do hash da palavra-passe para armazenar novamente o hashes da palavra-passe. VMs ou utilizadores previamente unidos ao domínio não poderão autenticar imediatamente - A Azure AD precisa gerar e armazenar as hashes de palavra-passe no novo domínio gerido.
>
> Para obter mais informações, consulte [o processo de sincronização de haxixe password para Azure AD DS e Azure AD Connect][password-hash-sync-process].

Os passos para gerar e armazenar estes hashes de palavra-passe são diferentes para contas de utilizador criadas apenas na nuvem criadas em AZure AD versus contas de utilizador que são sincronizadas a partir do seu diretório no local usando o Azure AD Connect.

Uma conta de utilizador apenas na cloud é uma conta que foi criada no diretório do Azure AD com o portal do Azure ou os cmdlets do PowerShell do Azure AD. Estas contas de utilizador não são sincronizadas a partir de um diretório no local.

Neste tutorial, vamos trabalhar com uma conta básica de utilizador apenas em nuvem. Para obter mais informações sobre os passos adicionais necessários para utilizar o Azure AD Connect, consulte [os hashes de palavra-passe sincronizados para contas de utilizador sincronizadas desde o AD no local até ao seu domínio gerido.][on-prem-sync]

> [!TIP]
> Se o seu inquilino AZURE AD tiver uma combinação de utilizadores e utilizadores apenas na nuvem a partir do seu AD no local, você precisa completar ambos os conjuntos de passos.

Para contas de utilizadores exclusivas na nuvem, os utilizadores devem alterar as suas palavras-passe antes de poderem utilizar o Azure AD DS. Este processo de alteração de palavra-passe faz com que os hashes de palavra-passe para a autenticação Kerberos e NTLM sejam gerados e armazenados em Azure AD. A conta não é sincronizada de Azure AD para Azure AD DS até que a palavra-passe seja alterada. Ou expira as palavras-passe para todos os utilizadores da nuvem no inquilino que precisam de usar O Azure AD DS, que força uma mudança de palavra-passe no próximo s-in, ou instrui os utilizadores da nuvem a alterar manualmente as suas palavras-passe. Para este tutorial, vamos alterar manualmente uma palavra-passe do utilizador.

Antes de um utilizador poder redefinir a sua palavra-passe, o inquilino AZURE AD tem de ser [configurado para reiniciar a palavra-passe de autosserviço][configure-sspr].

Para alterar a palavra-passe de um utilizador apenas na nuvem, o utilizador deve completar os seguintes passos:

1. Aceda à página do Painel de Acesso AD Azure em [https://myapps.microsoft.com](https://myapps.microsoft.com) .
1. No canto superior direito, selecione o seu nome e, em seguida, escolha **Profile** no menu suspenso.

    ![Selecionar perfil](./media/tutorial-create-instance-advanced/select-profile.png)

1. Na página **'Perfil',** selecione **Alterar a palavra-passe.**
1. Na página **'Alterar palavra-passe',** insira a sua palavra-passe (antiga) existente e, em seguida, insira e confirme uma nova palavra-passe.
1. Selecione **Submeter**.

Demora alguns minutos depois de ter alterado a sua palavra-passe para que a nova palavra-passe seja utilizável em Azure AD DS e para iniciar seduca com sucesso em computadores unidos ao domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar configurar DNS e configurações de rede virtuais para um domínio gerido
> * Criar um domínio gerido
> * Adicionar utilizadores administrativos à gestão de domínios
> * Ativar contas de utilizador para Azure AD DS e gerar hashes de palavra-passe

Para ver este domínio gerido em ação, crie e junte uma máquina virtual ao domínio.

> [!div class="nextstepaction"]
> [Junte uma máquina virtual do Windows Server ao seu domínio gerido](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
