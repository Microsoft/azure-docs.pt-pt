---
title: Tutorial – criar uma relação de confiança de floresta no Azure AD Domain Services | Microsoft Docs
description: Saiba como criar uma floresta de saída unidirecional para um domínio de AD DS local no portal do Azure para Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: bd0ec46d224e68f92b5d042826633d1efc7c336e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425422"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Tutorial: criar uma relação de confiança de floresta de saída para um domínio local no Azure Active Directory Domain Services (versão prévia)

Em ambientes em que você não pode sincronizar hashes de senha, ou você tem usuários que se conectam exclusivamente usando cartões inteligentes para que não saibam sua senha, você pode usar uma floresta de recursos no Azure Active Directory Domain Services (AD DS). Uma floresta de recursos usa uma relação de confiança de saída unidirecional do Azure AD DS para um ou mais ambientes de AD DS locais. Essa relação de confiança permite que usuários, aplicativos e computadores se autentiquem em um domínio local por meio do domínio gerenciado AD DS do Azure. Atualmente, as florestas de recursos do Azure AD DS estão em versão prévia.

![Diagrama de confiança de floresta do Azure AD DS para o local AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o DNS em um ambiente de AD DS local para dar suporte à conectividade de AD DS do Azure
> * Criar uma relação de confiança de floresta de entrada unidirecional em um ambiente de AD DS local
> * Criar uma relação de confiança de floresta de saída unidirecional no Azure AD DS
> * Testar e validar a relação de confiança para autenticação e acesso a recursos

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado criado usando uma floresta de recursos e configurado em seu locatário do Azure AD.
    * Se necessário, [crie e configure uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Certifique-se de criar um domínio gerenciado do Azure AD DS usando uma floresta de *recursos* . A opção padrão cria uma floresta de *usuário* . Somente as florestas de recursos podem criar relações de confiança com ambientes AD DS locais.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, você criará e configurará a relação de confiança de floresta de saída do Azure AD DS usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="networking-considerations"></a>Considerações de redes

A rede virtual que hospeda a floresta de recursos do Azure AD DS precisa de conectividade de rede para seu Active Directory local. Aplicativos e serviços também precisam de conectividade de rede para a rede virtual que hospeda a floresta de recursos de AD DS do Azure. A conectividade de rede para a floresta de recursos do Azure AD DS deve estar sempre ativa e estável, caso contrário, os usuários podem falhar ao autenticar ou acessar os recursos.

Antes de configurar uma relação de confiança de floresta no Azure AD DS, verifique se a rede entre o Azure e o ambiente local atende aos seguintes requisitos:

* Use endereços IP privados. Não confie no DHCP com a atribuição de endereço IP dinâmico.
* Evite sobreposição de espaços de endereço IP para permitir o emparelhamento e o roteamento de rede virtual para se comunicar com êxito entre o Azure e o local.
* Uma rede virtual do Azure precisa de uma sub-rede de gateway para configurar uma conexão VPN S2S (site a site) ou ExpressRoute
* Crie sub-redes com endereços IP suficientes para dar suporte ao seu cenário.
* Verifique se o Azure AD DS tem sua própria sub-rede, não compartilhe essa sub-rede de rede virtual com VMs de aplicativo e serviços.
* Redes virtuais emparelhadas não são transitivas.
    * Os emparelhamentos de rede virtual do Azure devem ser criados entre todas as redes virtuais que você deseja usar a relação de confiança de floresta do recurso AD DS do Azure para o ambiente de AD DS local.
* Forneça conectividade de rede contínua para sua floresta de Active Directory local. Não use conexões sob demanda.
* Verifique se há uma resolução de nome contínua (DNS) entre o nome da floresta de recursos do Azure AD DS e o nome da floresta Active Directory local.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configurar o DNS no domínio local

Para resolver corretamente o domínio gerenciado AD DS do Azure do ambiente local, talvez seja necessário adicionar encaminhadores aos servidores DNS existentes. Se você não configurou o ambiente local para se comunicar com o domínio gerenciado AD DS do Azure, conclua as seguintes etapas de uma estação de trabalho de gerenciamento para o domínio AD DS local:

1. Selecione **Iniciar | Ferramentas administrativas | DNS**
1. Selecione o servidor DNS, como *myAD01*, selecione **Propriedades**
1. Escolha **encaminhadores**e **Editar** para adicionar encaminhadores adicionais.
1. Adicione os endereços IP do domínio gerenciado AD DS do Azure, como *10.0.1.4* e *10.0.1.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Criar a relação de confiança de floresta de entrada no domínio local

O domínio AD DS local precisa de uma relação de confiança de floresta de entrada para o domínio gerenciado do Azure AD DS. Essa relação de confiança deve ser criada manualmente no domínio AD DS local, não pode ser criada no portal do Azure.

Para configurar a relação de confiança de entrada no domínio AD DS local, conclua as seguintes etapas de uma estação de trabalho de gerenciamento para o domínio AD DS local:

1. Selecione **Iniciar | Ferramentas administrativas | Domínios e relações de confiança do Active Directory**
1. Selecione o domínio com o botão direito do mouse, como *OnPrem.contoso.com*, selecione **Propriedades**
1. Escolha a guia **relações de confiança** e, em seguida, **nova relação de confiança**

   > [!NOTE]
   > Se você não vir a opção de menu **relações de confiança** , verifique em **Propriedades** para o *tipo de floresta*. Somente as florestas de *recursos* podem criar relações de confiança. Se o tipo de floresta for *usuário*, você não poderá criar relações de confiança. Atualmente, não há como alterar o tipo de floresta de um domínio gerenciado AD DS do Azure. Você precisa excluir e recriar o domínio gerenciado como uma floresta de recursos.

1. Insira o nome no Azure AD DS nome de domínio, como *aadds.contoso.com*, em seguida, selecione **Avançar**
1. Selecione a opção para criar uma **relação de confiança de floresta**e, em seguida, criar uma **forma:** confiança de entrada.
1. Opte por criar a relação de confiança **somente para este domínio**. Na próxima etapa, você criará a relação de confiança no portal do Azure para o domínio gerenciado do AD DS do Azure.
1. Escolha usar **autenticação em toda a floresta**e, em seguida, insira e confirme uma senha de confiança. Essa mesma senha também é inserida no portal do Azure na próxima seção.
1. Percorra as próximas janelas com as opções padrão e, em seguida, escolha a opção para **não, não confirme a relação de confiança de saída**.
1. Selecione **Concluir**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Criar relação de confiança de floresta de saída no Azure AD DS

Com o domínio AD DS local configurado para resolver o domínio gerenciado do Azure AD DS e uma relação de confiança de floresta de entrada criada, agora criou a relação de confiança de floresta de saída. Essa confiança de floresta de saída conclui a relação de confiança entre o domínio AD DS local e o domínio gerenciado do AD DS do Azure.

Para criar a relação de confiança de saída para o domínio gerenciado AD DS do Azure no portal do Azure, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione **Azure AD Domain Services**e, em seguida, selecione seu domínio gerenciado, como *aadds.contoso.com*
1. No menu no lado esquerdo do domínio gerenciado AD DS do Azure, selecione **relações de confiança**e, em seguida, escolha **+ Adicionar** uma relação de confiança.
1. Insira um nome de exibição que identifique sua relação de confiança e, em seguida, o nome DNS da floresta confiável local, como *OnPrem.contoso.com*
1. Forneça a mesma senha de confiança que foi usada ao configurar a relação de confiança de floresta de entrada para o domínio AD DS local na seção anterior.
1. Forneça pelo menos dois servidores DNS para o domínio AD DS local, como *10.0.2.4* e *10.0.2.5*
1. Quando estiver pronto, **salve** a relação de confiança de floresta de saída

    [Criar uma relação de confiança de floresta de saída no portal do Azure](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Validar autenticação de recurso

Os cenários comuns a seguir permitem que você valide se a confiança da floresta autentica corretamente os usuários e o acesso aos recursos:

* [Autenticação de usuário local da floresta de recursos do Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Acessar recursos na floresta de recursos do Azure AD DS usando o usuário local](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Habilitar compartilhamento de arquivos e impressoras](#enable-file-and-printer-sharing)
    * [Criar um grupo de segurança e adicionar membros](#create-a-security-group-and-add-members)
    * [Criar um compartilhamento de arquivos para acesso entre florestas](#create-a-file-share-for-cross-forest-access)
    * [Validar a autenticação entre florestas para um recurso](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticação de usuário local da floresta de recursos do Azure AD DS

Você deve ter a máquina virtual do Windows Server ingressada no domínio do recurso de AD DS do Azure. Use esta máquina virtual para testar se o usuário local pode autenticar em uma máquina virtual.

1. Conecte-se à VM do Windows Server unida à floresta de recursos do Azure AD DS usando Área de Trabalho Remota e suas credenciais de administrador de AD DS do Azure. Se você receber um erro de Autenticação no Nível da Rede (NLA), verifique se a conta de usuário usada não é uma conta de usuário de domínio.

    > [!NOTE]
    > Para se conectar com segurança às suas VMs ingressadas no Azure AD Domain Services, você pode usar o [serviço do host de bastiões do Azure](https://docs.microsoft.com/azure/bastion/bastion-overview) em regiões do Azure com suporte.

1. Abra um prompt de comando e use o comando `whoami` para mostrar o nome distinto do usuário autenticado no momento:

    ```console
    whoami /fqdn
    ```

1. Use o comando `runas` para autenticar como um usuário do domínio local. No comando a seguir, substitua `userUpn@trusteddomain.com` pelo UPN de um usuário do domínio local confiável. O comando solicita a senha do usuário:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se a autenticação for bem-sucedida, um novo prompt de comando será aberto. O título do novo prompt de comando inclui `running as userUpn@trusteddomain.com`.
1. Use `whoami /fqdn` no novo prompt de comando para exibir o nome distinto do usuário autenticado do Active Directory local.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Acessar recursos na floresta de recursos do Azure AD DS usando o usuário local

Usando a VM do Windows Server unida à floresta de recursos do Azure AD DS, você pode testar o cenário em que os usuários podem acessar recursos hospedados na floresta de recursos quando eles se autenticam de computadores no domínio local com usuários do domínio local. Os exemplos a seguir mostram como criar e testar vários cenários comuns.

#### <a name="enable-file-and-printer-sharing"></a>Habilitar compartilhamento de arquivos e impressoras

1. Conecte-se à VM do Windows Server unida à floresta de recursos do Azure AD DS usando Área de Trabalho Remota e suas credenciais de administrador de AD DS do Azure. Se você receber um erro de Autenticação no Nível da Rede (NLA), verifique se a conta de usuário usada não é uma conta de usuário de domínio.

    > [!NOTE]
    > Para se conectar com segurança às suas VMs ingressadas no Azure AD Domain Services, você pode usar o [serviço do host de bastiões do Azure](https://docs.microsoft.com/azure/bastion/bastion-overview) em regiões do Azure com suporte.

1. Abra **configurações do Windows**, procure e selecione **central de rede e compartilhamento**.
1. Escolha a opção para **alterar** as configurações avançadas de compartilhamento.
1. No **perfil de domínio**, selecione **Ativar compartilhamento de arquivos e impressoras** e, em seguida, **salvar alterações**.
1. Feche a **central de rede e compartilhamento**.

#### <a name="create-a-security-group-and-add-members"></a>Criar um grupo de segurança e adicionar membros

1. Abra **Active Directory usuários e computadores**.
1. Selecione o nome do domínio, escolha **novo**e, em seguida, selecione **unidade organizacional**.
1. Na caixa nome, digite *LocalObjects*e, em seguida, selecione **OK**.
1. Selecione e clique com o botão direito do mouse em **LocalObjects** no painel de navegação. Selecione **novo** e **grupo**.
1. Digite *FileServerAccess* na caixa **nome do grupo** . Para o **escopo do grupo**, selecione **domínio local**e, em seguida, escolha **OK**.
1. No painel de conteúdo, clique duas vezes em **FileServerAccess**. Selecione **Membros**, escolha **Adicionar**e, em seguida, selecione **locais**.
1. Selecione sua Active Directory local no modo de exibição de **localização** e escolha **OK**.
1. Digite *usuários de domínio* na caixa **Inserir os nomes de objeto a serem selecionados** . Selecione **verificar nomes**, forneça credenciais para o Active Directory local e, em seguida, selecione **OK**.

    > [!NOTE]
    > Você deve fornecer credenciais porque a relação de confiança é apenas uma maneira. Isso significa que os usuários da AD DS do Azure não podem acessar recursos ou Pesquisar usuários ou grupos no domínio confiável (local).

1. O grupo de **usuários de domínio** do seu Active Directory local deve ser um membro do grupo **FileServerAccess** . Selecione **OK** para salvar o grupo e fechar a janela.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Criar um compartilhamento de arquivos para acesso entre florestas

1. Na VM do Windows Server unida à floresta de recursos do Azure AD DS, crie uma pasta e forneça um nome como *CrossForestShare*.
1. Selecione a pasta com o botão direito do mouse e escolha **Propriedades**.
1. Selecione a guia **segurança** e, em seguida, escolha **Editar**.
1. Na caixa *de diálogo permissões para CrossForestShare* , selecione **Adicionar**.
1. Digite *FileServerAccess* em **digite os nomes de objeto a serem selecionados**e, em seguida, selecione **OK**.
1. Selecione *FileServerAccess* na lista **grupos ou nomes de usuário** . Na lista **permissões para FileServerAccess** , escolha *permitir* para as permissões **Modificar** e **gravar** e, em seguida, selecione **OK**.
1. Selecione a guia **compartilhamento** e, em seguida, escolha **compartilhamento avançado...**
1. Escolha **compartilhar esta pasta**e insira um nome fácil de memorizar para o compartilhamento de arquivos em **nome do compartilhamento** , como *CrossForestShare*.
1. Selecione **Permissões**. Na lista **permissões para todos** , escolha **permitir** para a permissão **alterar** .
1. Selecione **OK** duas vezes e, em seguida, **fechar**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Validar a autenticação entre florestas para um recurso

1. Conecte-se a um computador Windows ingressado em seu Active Directory local usando uma conta de usuário do Active Directory local.
1. Usando o **Windows Explorer**, conecte-se ao compartilhamento que você criou usando o nome de host totalmente qualificado e o compartilhamento, como `\\fs1.aadds.contoso.com\CrossforestShare`.
1. Para validar a permissão de gravação, selecione o botão direito do mouse na pasta, escolha **novo**e, em seguida, selecione **documento de texto**. Use o **novo documento de texto**nome padrão.

    Se as permissões de gravação estiverem definidas corretamente, um novo documento de texto será criado. As etapas a seguir abrirão, editarão e excluirão o arquivo conforme apropriado.
1. Para validar a permissão de leitura, abra o **novo documento de texto**.
1. Para validar a permissão Modificar, adicione texto ao arquivo e feche o **bloco de notas**. Quando for solicitado a salvar as alterações, escolha **salvar**.
1. Para validar a permissão de exclusão, selecione **novo documento de texto** e escolha **excluir**. Escolha **Sim** para confirmar a exclusão do arquivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar o DNS em um ambiente de AD DS local para dar suporte à conectividade de AD DS do Azure
> * Criar uma relação de confiança de floresta de entrada unidirecional em um ambiente de AD DS local
> * Criar uma relação de confiança de floresta de saída unidirecional no Azure AD DS
> * Testar e validar a relação de confiança para autenticação e acesso a recursos

Para obter mais informações conceituais sobre tipos de floresta no Azure AD DS, consulte [o que são florestas de recursos?][concepts-forest] e [como as relações de confiança de floresta funcionam no AD DS do Azure?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
