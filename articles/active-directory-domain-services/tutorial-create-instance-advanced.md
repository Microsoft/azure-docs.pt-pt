---
title: Tutorial – criar uma instância de Azure Active Directory Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a criar e configurar uma instância de Azure Active Directory Domain Services e especificar opções de configuração avançadas usando o portal do Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 334a5c3c76f1ebaf4c8c36020110ef9c0bcc8d69
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208705"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Tutorial: criar e configurar uma instância de Azure Active Directory Domain Services com opções de configuração avançadas

O Azure Active Directory Domain Services (Azure AD DS) fornece serviços de domínio gerenciados, como ingresso no domínio, diretiva de grupo, LDAP, autenticação Kerberos/NTLM que é totalmente compatível com o Windows Server Active Directory. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches a controladores de domínio por conta própria. O Azure AD DS integra-se ao seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar grupos existentes e contas de usuário para proteger o acesso aos recursos.

Você pode [criar um domínio gerenciado usando as opções de configuração padrão][tutorial-create-instance] para rede e sincronização ou definir manualmente essas configurações. Este tutorial mostra como definir essas opções de configuração avançadas para criar e configurar uma instância de AD DS do Azure usando o portal do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Definir as configurações de rede virtual e DNS para um domínio gerenciado
> * Criar uma instância do Azure AD DS
> * Adicionar usuários administrativos ao gerenciamento de domínio
> * Ativar a sincronização de hash de palavra-passe

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Você precisa de privilégios de *administrador global* em seu locatário do Azure ad para habilitar o Azure AD DS.
* Você precisa de privilégios de *colaborador* em sua assinatura do Azure para criar os recursos de AD DS do Azure necessários.

Embora não seja necessário para o Azure AD DS, é recomendável [Configurar o SSPR (redefinição de senha de autoatendimento)][configure-sspr] para o locatário do Azure AD. Os usuários podem alterar sua senha sem SSPR, mas o SSPR ajuda a esquecer sua senha e precisa redefini-la.

> [!IMPORTANT]
> Depois de criar um domínio gerenciado do Azure AD DS, você não poderá mover a instância para um grupo de recursos, rede virtual, assinatura etc. diferentes. Tome cuidado para selecionar a assinatura, o grupo de recursos, a região e a rede virtual mais apropriados ao implantar a instância de AD DS do Azure.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, você criará e configurará a instância de AD DS do Azure usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Criar uma instância e definir as configurações básicas

Para iniciar o assistente para **habilitar Azure AD Domain Services** , conclua as seguintes etapas:

1. No menu portal do Azure ou na **Home** Page do, selecione **criar um recurso**.
1. Insira *serviços de domínio* na barra de pesquisa e, em seguida, escolha *Azure AD Domain Services* nas sugestões de pesquisa.
1. Na página Azure AD Domain Services, selecione **criar**. O assistente para **habilitar Azure AD Domain Services** é iniciado.
1. Selecione a **assinatura** do Azure na qual você gostaria de criar o domínio gerenciado.
1. Selecione o **grupo de recursos** ao qual o domínio gerenciado deve pertencer. Escolha **criar novo** ou selecionar um grupo de recursos existente.

Ao criar uma instância de AD DS do Azure, você especifica um nome DNS. Há algumas considerações quando você escolhe esse nome DNS:

* **Nome de domínio interno:** Por padrão, o nome de domínio interno do diretório é usado (sufixo *. onmicrosoft.com* ). Se desejar habilitar o acesso LDAP seguro ao domínio gerenciado pela Internet, você não poderá criar um certificado digital para proteger a conexão com esse domínio padrão. A Microsoft possui o domínio *. onmicrosoft.com* , portanto, uma AC (autoridade de certificação) não emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, normalmente um que você já possui e é roteável. Quando você usa um domínio reroteável, personalizado, o tráfego pode fluir corretamente conforme necessário para dar suporte aos seus aplicativos.
* **Sufixos de domínio não roteáveis:** Geralmente, recomendamos que você evite um sufixo de nome de domínio não roteável, como *contoso. local*. O sufixo *. local* não é roteável e pode causar problemas com a resolução DNS.

> [!TIP]
> Se você criar um nome de domínio personalizado, tome cuidado com os namespaces DNS existentes. É recomendável incluir um prefixo exclusivo para o nome de domínio. Por exemplo, se o nome raiz do DNS for *contoso.com*, crie um domínio gerenciado AD DS do Azure com o nome de domínio personalizado *Corp.contoso.com* ou *DS.contoso.com*. Em um ambiente híbrido com um ambiente de AD DS local, esses prefixos já podem estar em uso. Use um prefixo exclusivo para AD DS do Azure.
>
> Você pode usar o nome DNS raiz para seu domínio gerenciado AD DS do Azure, mas talvez precise criar alguns registros DNS adicionais para outros serviços em seu ambiente. Por exemplo, se você executar um servidor Webque hospeda um site usando o nome DNS raiz, poderá haver conflitos de nomenclatura que exigem entradas DNS adicionais.
>
> Nesses tutoriais e artigos de instruções, o domínio personalizado do *contoso.com* é usado como um breve exemplo. Em todos os comandos, especifique seu próprio nome de domínio, que pode incluir um prefixo exclusivo.
>
> Para obter mais informações, consulte [selecionar um prefixo de nomenclatura para o domínio] [nome-prefixo].

As seguintes restrições de nome DNS também se aplicam:

* **Restrições de prefixo de domínio:** Você não pode criar um domínio gerenciado com um prefixo com mais de 15 caracteres. O prefixo do nome de domínio especificado (como *contoso* no nome de domínio *contoso.com* ) deve conter 15 caracteres ou menos.
* **Conflitos de nome de rede:** O nome de domínio DNS para seu domínio gerenciado já não deve existir na rede virtual. Especificamente, verifique os seguintes cenários que levam a um conflito de nome:
    * Se você já tiver um domínio Active Directory com o mesmo nome de domínio DNS na rede virtual do Azure.
    * Se a rede virtual em que você planeja habilitar o domínio gerenciado tiver uma conexão VPN com sua rede local. Nesse cenário, verifique se você não tem um domínio com o mesmo nome de domínio DNS em sua rede local.
    * Se você tiver um serviço de nuvem do Azure existente com esse nome na rede virtual do Azure.

Preencha os campos na janela *noções básicas* do portal do Azure para criar uma instância do AD DS do Azure:

1. Insira um **nome de domínio DNS** para seu domínio gerenciado, levando em consideração os pontos anteriores.
1. Escolha o **local** do Azure no qual o domínio gerenciado deve ser criado. Se você escolher uma região com suporte a Zonas de Disponibilidade, os recursos de AD DS do Azure serão distribuídos entre zonas para redundância adicional.

    As zonas de disponibilidade são localizações físicas únicas dentro de uma região do Azure. Cada zona é constituída por um ou mais datacenters equipados com energia, refrigeração e redes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.

    Não há nada a ser configurado para o Azure AD DS ser distribuído entre zonas. A plataforma Azure manipula automaticamente a distribuição de zona de recursos. Para obter mais informações e ver a disponibilidade da região, consulte [o que são zonas de disponibilidade no Azure?][availability-zones]

1. Uma *floresta* é uma construção lógica usada pelo Active Directory Domain Services para agrupar um ou mais domínios. Por padrão, um domínio gerenciado do Azure AD DS é criado como uma floresta de *usuário* . Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente de AD DS local. Uma floresta de *recursos* só sincroniza usuários e grupos criados diretamente no Azure AD. Atualmente, as florestas de recursos estão em versão prévia. Para obter mais informações sobre florestas de *recursos* , incluindo por que você pode usar um e como criar relações de confiança de floresta com domínios AD DS locais, consulte [visão geral das florestas de recursos do Azure AD DS][resource-forests].

    Para este tutorial, escolha criar uma floresta de *usuário* .

    ![Definir configurações básicas para uma instância de Azure AD Domain Services](./media/tutorial-create-instance-advanced/basics-window.png)

1. Para configurar manualmente as opções adicionais, escolha **Next-Networking**. Caso contrário, selecione **revisar + criar** para aceitar as opções de configuração padrão e, em seguida, vá para a seção para [implantar seu domínio gerenciado](#deploy-the-managed-domain). Os padrões a seguir são configurados quando você escolhe essa opção de criação:

* Cria uma rede virtual chamada *aadds-vnet* que usa o intervalo de endereços IP de *10.0.1.0/24*.
* Cria uma sub-rede denominada *aadds-subnet* usando o intervalo de endereços IP de *10.0.1.0/24*.
* Sincroniza *todos* os usuários do Azure AD com o domínio gerenciado AD DS do Azure.

## <a name="create-and-configure-the-virtual-network"></a>Criar e configurar a rede virtual

Para fornecer conectividade, é necessária uma rede virtual do Azure e uma sub-rede dedicada. O AD DS do Azure está habilitado nesta sub-rede de rede virtual. Neste tutorial, você criará uma rede virtual, embora possa optar por usar uma rede virtual existente. Em qualquer abordagem, você deve criar uma sub-rede dedicada para uso pelo Azure AD DS.

Algumas considerações para essa sub-rede de rede virtual dedicada incluem as seguintes áreas:

* A sub-rede deve ter pelo menos 3-5 endereços IP disponíveis em seu intervalo de endereços para dar suporte aos recursos de AD DS do Azure.
* Não selecione a sub-rede de *Gateway* para implantar AD DS do Azure. Não há suporte para implantar o Azure AD DS em uma sub-rede de *Gateway* .
* Não implante nenhuma outra máquina virtual na sub-rede. Os aplicativos e as VMs geralmente usam grupos de segurança de rede para proteger a conectividade. A execução dessas cargas de trabalho em uma sub-rede separada permite que você aplique esses grupos de segurança de rede sem interromper a conectividade com o domínio gerenciado.
* Você não pode mover seu domínio gerenciado para uma rede virtual diferente depois de habilitar o Azure AD DS.

Para obter mais informações sobre como planejar e configurar a rede virtual, consulte [considerações de rede para Azure Active Directory Domain Services][network-considerations].

Preencha os campos na janela *rede* da seguinte maneira:

1. Na página **rede** , escolha uma rede virtual para implantar o Azure AD DS no no menu suspenso ou selecione **criar novo**.
    1. Se você optar por criar uma rede virtual, insira um nome para a rede virtual, como *myVnet*, em seguida, forneça um intervalo de endereços, como *10.0.1.0/24*.
    1. Crie uma sub-rede dedicada com um nome claro, como *DomainServices*. Forneça um intervalo de endereços, como *10.0.1.0/24*.

    ![Criar uma rede virtual e uma sub-rede para uso com Azure AD Domain Services](./media/tutorial-create-instance-advanced/create-vnet.png)

    Certifique-se de escolher um intervalo de endereços que esteja dentro de seu intervalo de endereços IP privado. Os intervalos de endereços IP que não são de sua propriedade no espaço de endereço público causam erros no AD DS do Azure.

1. Selecione uma sub-rede de rede virtual, como *DomainServices*.
1. Quando estiver pronto, escolha **Avançar-administração**.

## <a name="configure-an-administrative-group"></a>Configurar um grupo administrativo

Um grupo administrativo especial chamado *Administradores de DC do AAD* é usado para o gerenciamento do domínio de AD DS do Azure. Os membros desse grupo recebem permissões administrativas em VMs que ingressaram no domínio para o domínio gerenciado. Em VMs ingressadas no domínio, esse grupo é adicionado ao grupo local de administradores. Os membros desse grupo também podem usar Área de Trabalho Remota para se conectar remotamente a VMs ingressadas no domínio.

Você não tem permissões de administrador de *domínio* ou de *administrador corporativo* em um domínio gerenciado usando o Azure AD DS. Essas permissões são reservadas pelo serviço e não são disponibilizadas para os usuários dentro do locatário. Em vez disso, o grupo de *Administradores de DC do AAD* permite que você execute algumas operações privilegiadas. Essas operações incluem o ingresso de computadores no domínio, pertencente ao grupo de administração em VMs ingressadas no domínio e a configuração de Política de Grupo.

O assistente cria automaticamente o grupo de *Administradores de DC do AAD* em seu diretório do Azure AD. Se você tiver um grupo existente com esse nome em seu diretório do Azure AD, o assistente selecionará esse grupo. Opcionalmente, você pode optar por adicionar outros usuários a esse grupo de *Administradores de DC do AAD* durante o processo de implantação. Essas etapas podem ser concluídas mais tarde.

1. Para adicionar usuários adicionais a esse grupo de *Administradores de DC do AAD* , selecione **gerenciar associação de grupo**.

    ![Configurar a associação de grupo do grupo de administradores de DC do AAD](./media/tutorial-create-instance-advanced/admin-group.png)

1. Selecione o botão **adicionar membros** e procure e selecione usuários no seu diretório do Azure AD. Por exemplo, pesquise sua própria conta e adicione-a ao grupo de *Administradores de DC do AAD* .
1. Se desejar, altere ou adicione destinatários adicionais para notificações quando houver alertas no domínio gerenciado AD DS do Azure que exigem atenção.
1. Quando estiver pronto, escolha **próxima sincronização**.

## <a name="configure-synchronization"></a>Configurar sincronização

O AD DS do Azure permite sincronizar *todos* os usuários e grupos disponíveis no Azure ad ou uma sincronização com *escopo* apenas de grupos específicos. Se você optar por sincronizar *todos* os usuários e grupos, não poderá optar por executar apenas uma sincronização com escopo. Para obter mais informações sobre sincronização com escopo, consulte [Azure AD Domain Services sincronização com escopo][scoped-sync].

1. Para este tutorial, escolha sincronizar **todos** os usuários e grupos. Essa opção de sincronização é a opção padrão.

    ![Executar uma sincronização completa de usuários e grupos do Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Selecione **Rever + criar**.

## <a name="deploy-the-managed-domain"></a>Implantar o domínio gerenciado

Na página **Resumo** do assistente, examine as definições de configuração do domínio gerenciado. Você pode voltar a qualquer etapa do assistente para fazer alterações. Para reimplantar um domínio gerenciado do Azure AD DS em um locatário do Azure AD diferente de forma consistente usando essas opções de configuração, você também pode **baixar um modelo para automação**.

1. Para criar o domínio gerenciado, selecione **criar**. É exibida uma observação de que determinadas opções de configuração, como o nome DNS ou a rede virtual, não podem ser alteradas depois que o Azure AD DS gerenciado foi criado. Para continuar, selecione **OK**.
1. O processo de provisionamento do domínio gerenciado pode levar até uma hora. Uma notificação é exibida no portal que mostra o progresso de sua implantação de AD DS do Azure. Selecione a notificação para ver o progresso detalhado da implantação.

    ![Notificação na portal do Azure da implantação em andamento](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Selecione seu grupo de recursos, como *MyResource*Group e, em seguida, escolha sua instância de AD DS do Azure na lista de recursos do Azure, como *contoso.com*. A guia **visão geral** mostra que o domínio gerenciado está sendo *implantado*no momento. Você não pode configurar o domínio gerenciado até que ele seja totalmente provisionado.

    ![Status dos serviços de domínio durante o estado de provisionamento](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Quando o domínio gerenciado é totalmente provisionado, a guia **visão geral** mostra o status do domínio como *em execução*.

    ![Status dos serviços de domínio depois de provisionado com êxito](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

O domínio gerenciado é associado ao seu locatário do Azure AD. Durante o processo de provisionamento, o Azure AD DS cria dois aplicativos empresariais denominados *serviços de controlador de domínio* e *AzureActiveDirectoryDomainControllerServices* no locatário do Azure AD. Esses aplicativos empresariais são necessários para atender ao domínio gerenciado. Não exclua esses aplicativos.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Atualizar as definições de DNS para a Azure Virtual Network

Com o Azure AD DS implantado com êxito, agora configure a rede virtual para permitir que outras VMs e aplicativos conectados usem o domínio gerenciado. Para fornecer essa conectividade, atualize as configurações do servidor DNS para sua rede virtual para apontar para os dois endereços IP em que o Azure AD DS é implantado.

1. A guia **visão geral** do domínio gerenciado mostra algumas **etapas de configuração necessárias**. A primeira etapa de configuração é atualizar as configurações do servidor DNS para sua rede virtual. Depois que as configurações de DNS estiverem configuradas corretamente, essa etapa não será mais mostrada.

    Os endereços listados são os controladores de domínio para uso na rede virtual. Neste exemplo, esses endereços são *10.1.0.4* e *10.1.0.5*. Posteriormente, você poderá encontrar esses endereços IP na guia **Propriedades** .

    ![Definir as configurações de DNS para sua rede virtual com os endereços IP Azure AD Domain Services](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Para atualizar as configurações do servidor DNS para a rede virtual, selecione o botão **Configurar** . As configurações de DNS são configuradas automaticamente para sua rede virtual.

> [!TIP]
> Se você selecionou uma rede virtual existente nas etapas anteriores, todas as VMs conectadas à rede só obtêm as novas configurações de DNS após uma reinicialização. Você pode reiniciar as VMs usando o portal do Azure, Azure PowerShell ou a CLI do Azure.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Habilitar contas de usuário para o Azure AD DS

Para autenticar usuários no domínio gerenciado, o Azure AD DS precisa de hashes de senha em um formato adequado para autenticação Kerberos e NTLM (NT LAN Manager). O Azure AD não gera ou armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena nenhuma credencial de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente esses hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

> [!NOTE]
> Uma vez configurado adequadamente, os hashes de senha utilizáveis são armazenados no domínio gerenciado AD DS do Azure. Se você excluir o domínio gerenciado AD DS do Azure, todos os hashes de senha armazenados nesse ponto também serão excluídos. As informações de credenciais sincronizadas no Azure AD não poderão ser reutilizadas se você criar posteriormente um domínio gerenciado do Azure AD DS-você deve reconfigurar a sincronização de hash de senha para armazenar os hashes de senha novamente. As VMs previamente Unidas ao domínio ou os usuários não poderão autenticar imediatamente – o AD do Azure precisa gerar e armazenar os hashes de senha no novo domínio gerenciado do Azure AD DS. Para obter mais informações, consulte [processo de sincronização de hash de senha para Azure AD DS e Azure ad Connect][password-hash-sync-process].

As etapas para gerar e armazenar esses hashes de senha são diferentes para contas de usuário somente em nuvem criadas no Azure AD em vez de contas de usuário que são sincronizadas do seu diretório local usando Azure AD Connect. Uma conta de utilizador apenas na cloud é uma conta que foi criada no diretório do Azure AD com o portal do Azure ou os cmdlets do PowerShell do Azure AD. Essas contas de usuário não são sincronizadas de um diretório local. Neste tutorial, vamos trabalhar com uma conta de usuário básica somente de nuvem. Para obter mais informações sobre as etapas adicionais necessárias para usar Azure AD Connect, consulte [sincronizar hashes de senha para contas de usuário sincronizadas do seu ad local para seu domínio gerenciado][on-prem-sync].

> [!TIP]
> Se o seu locatário do Azure AD tiver uma combinação de usuários somente de nuvem e usuários do seu AD local, você precisará concluir os dois conjuntos de etapas.

Para contas de usuário somente em nuvem, os usuários devem alterar suas senhas antes de poderem usar o Azure AD DS. Esse processo de alteração de senha faz com que os hashes de senha para autenticação Kerberos e NTLM sejam gerados e armazenados no Azure AD. Você pode expirar as senhas para todos os usuários no locatário que precisam usar o AD DS do Azure, que força uma alteração de senha na próxima entrada ou instrui-los a alterar suas senhas manualmente. Para este tutorial, vamos alterar manualmente uma senha de usuário.

Antes que um usuário possa redefinir sua senha, o locatário do Azure AD deve ser [configurado para redefinição de senha de autoatendimento][configure-sspr].

Para alterar a senha de um usuário somente em nuvem, o usuário deve concluir as seguintes etapas:

1. Acesse a página do painel de acesso do Azure AD em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, selecione seu nome e, em seguida, escolha **perfil** no menu suspenso.

    ![Selecionar perfil](./media/tutorial-create-instance-advanced/select-profile.png)

1. Na página **perfil** , selecione **alterar senha**.
1. Na página **alterar senha** , insira sua senha existente (antiga) e, em seguida, insira e confirme uma nova senha.
1. Selecione **Enviar**.

Levará alguns minutos após você ter alterado sua senha para que a nova senha possa ser usada no Azure AD DS e entrar com êxito em computadores ingressados no domínio gerenciado.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Definir as configurações de rede virtual e DNS para um domínio gerenciado
> * Criar uma instância do Azure AD DS
> * Adicionar usuários administrativos ao gerenciamento de domínio
> * Habilitar contas de usuário para o Azure AD DS e gerar hashes de senha

Para ver esse domínio gerenciado em ação, crie e ingresse uma máquina virtual no domínio.

> [!div class="nextstepaction"]
> [Ingressar uma máquina virtual do Windows Server em seu domínio gerenciado](join-windows-vm.md)

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

<!-- EXTERNAL LINKS -->
