---
title: Tutorial - Criar um fundo florestal nos Serviços de Domínio Da AD Azure  Microsoft Docs
description: Aprenda a criar uma floresta de saída única para um domínio AD DS no local no portal Azure para serviços de domínio azure AD
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 5620d1cdc7dc71bdac17057b9a13a74150b12d5c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612513"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Tutorial: Criar uma confiança florestal de saída para um domínio no local em Azure Ative Directory Domain Services (pré-visualização)

Em ambientes onde não se pode sincronizar hashes de palavra-passe, ou tem utilizadores que assinam exclusivamente usando cartões inteligentes para que não saibam a sua palavra-passe, pode utilizar uma floresta de recursos em Serviços de Domínio de Diretório Ativo Azure (AD DS). Uma floresta de recursos usa uma confiança de saída única de Azure AD DS para um ou mais ambientes AD DS no local. Esta relação de confiança permite que utilizadores, aplicações e computadores se autentiquem contra um domínio no local a partir do domínio gerido pelo Azure AD DS. As florestas de recursos Azure AD DS estão atualmente em pré-visualização.

![Diagrama de confiança florestal de Azure AD DS para a AD DS no local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure dNS em um ambiente AD DS no local para apoiar a conectividade Azure AD DS
> * Criar uma confiança florestal de entrada unidirecional num ambiente ad DS no local
> * Criar um fundo florestal de saída unidirecional em Azure AD DS
> * Testar e validar a relação fidedigna para autenticação e acesso a recursos

Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio gerido pelo Azure Ative Directory Services foi criado com uma floresta de recursos e configurado no seu inquilino Azure AD.
    * Se necessário, crie e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Certifique-se de que cria um domínio gerido pelo Azure AD DS utilizando uma floresta de *recursos.* A opção padrão cria uma floresta *de utilizadores.* Só as florestas de recursos podem criar fundos para ambientes DS on-prem. Também precisa de utilizar um mínimo de *Enterprise* SKU para o seu domínio gerido. Se necessário, mude o SKU para um domínio gerido por [AD DS azure][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, você cria e configura o fundo florestal de saída da Azure AD DS usando o portal Azure. Para começar, inicie a inscrição no [portal Azure.](https://portal.azure.com)

## <a name="networking-considerations"></a>Considerações de redes

A rede virtual que acolhe a floresta de recursos Azure AD DS necessita de conectividade de rede com o seu Diretório Ativo no local. Aplicações e serviços também precisam de conectividade de rede para a rede virtual que acolhe a floresta de recursos Azure AD DS. A conectividade da rede com a floresta de recursos Azure AD DS deve estar sempre acesada e estável, caso contrário, os utilizadores podem não autenticar ou aceder a recursos.

Antes de configurar um fundo florestal em Azure AD DS, certifique-se de que o seu networking entre o ambiente Azure e o ambiente no local satisfaz os seguintes requisitos:

* Utilize endereços IP privados. Não confie no DHCP com atribuição dinâmica de endereçoip.
* Evite sobrepor-se aos espaços de endereçoIP para permitir que o espreite e o encaminhamento de rede virtual comunique com sucesso entre o Azure e o local.
* Uma rede virtual Azure precisa de uma subnet de gateway para configurar uma ligação VPN ou ExpressRoute site-to-site (S2S)
* Crie subredes com endereços IP suficientes para suportar o seu cenário.
* Certifique-se de que o Azure AD DS tem a sua própria subnet, não partilhe esta subnet de rede virtual com VMs e serviços de aplicação.
* As redes virtuais não são transitórias.
    * Os pares de rede virtual Azure devem ser criados entre todas as redes virtuais que pretende utilizar o fundo florestal de recursos Azure AD DS para o ambiente AD DS no local.
* Forneça conectividade contínua de rede à sua floresta de Diretório Ativo no local. Não use ligações a pedido.
* Certifique-se de que existe uma resolução contínua de nomes (DNS) entre o nome da floresta de recursos Azure AD DS e o seu nome de floresta de diretório ativo no local.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configure dNS no domínio no local

Para resolver corretamente o domínio gerido pelo Azure AD DS a partir do ambiente no local, poderá ser necessário adicionar os avançados aos servidores DNS existentes. Se não configurar o ambiente no local para comunicar com o domínio gerido pela AD DS azure, complete os seguintes passos de uma estação de trabalho de gestão para o domínio AD DS no local:

1. Selecione **Início / Ferramentas Administrativas [ Ferramentas Administrativas] DNS**
1. Servidor DNS selecionado à direita, como *myAD01,* selecione **Properties**
1. Escolha **Os Forwarders** **e,** em seguida, editar para adicionar mais avançados.
1. Adicione os endereços IP do domínio gerido azure AD DS, tais como *10.0.1.4* e *10.0.1.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Criar confiança florestal de entrada no domínio no local

O domínio AD DS no local precisa de uma confiança florestal para o domínio gerido pela AD DS azure. Esta confiança deve ser criada manualmente no domínio AD DS no local, não pode ser criada a partir do portal Azure.

Para configurar a confiança de entrada no domínio AD DS no local, complete os seguintes passos de uma estação de trabalho de gestão para o domínio AD DS no local:

1. Selecione **Início / Ferramentas Administrativas [ Ferramentas Administrativas] Domínios e Fundos** de Diretório Ativo
1. Domínio selecionado à direita, como *onprem.contoso.com,* selecione **Propriedades**
1. Escolha o separador **Trusts,** em seguida, **New Trust**

   > [!NOTE]
   > Se não vir a opção de menu **Trusts,** consulte em **Propriedades** para o *tipo Forest*. Só as florestas de *recursos* podem criar fundos. Se o tipo de floresta for *Utilizador,* não pode criar fundos. Não há forma de alterar o tipo florestal de um domínio gerido pela AD DS azure. É necessário eliminar e recriar o domínio gerido como uma floresta de recursos.

1. Introduza o nome no nome de domínio Azure AD DS, como *aaddscontoso.com,* em seguida, selecione **Next**
1. Selecione a opção de criar um **fundo Florestal,** em seguida, criar uma **forma única: a entrada** de confiança.
1. Opte por criar a confiança **apenas**para este domínio . No passo seguinte, cria-se a confiança no portal Azure para o domínio gerido pela Azure AD DS.
1. Opte por utilizar a autenticação em **toda a Floresta**e, em seguida, insira e confirme uma senha de confiança. Esta mesma senha também é inserida no portal Azure na secção seguinte.
1. Pise nas próximas janelas com opções predefinidas, em seguida, escolha a opção para **Não, não confirme a confiança de saída**.
1. Selecione **Concluir**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Criar confiança florestal de saída em Azure AD DS

Com o domínio AD DS configurado para resolver o domínio gerido pela AD DS azure e um fundo florestal de entrada criado, criou agora a confiança florestal de saída. Este fundo florestal de saída completa a relação de confiança entre o domínio AD DS no local e o domínio gerido pela AD DS azure.

Para criar a confiança de saída para o domínio gerido pela AD DS Azure no portal Azure, complete os seguintes passos:

1. No portal Azure, procure e selecione Serviços de **Domínio Azure AD,** em seguida, selecione o seu domínio gerido, como *aaddscontoso.com*
1. A partir do menu do lado esquerdo do domínio gerido pelo Azure AD DS, selecione **Trusts,** e depois opte por **+ Adicionar** um fundo.
1. Introduza um nome de exibição que identifique a sua confiança, em seguida, o nome DNS da floresta confiável no local, como *onprem.contoso.com*
1. Forneça a mesma senha de confiança que foi usada ao configurar o fundo florestal de entrada para o domínio AD DS no local na secção anterior.
1. Forneça pelo menos dois servidores DNS para o domínio AD DS no local, tais como *10.0.2.4* e *10.0.2.5*
1. Quando estiver pronto, **salve** a confiança da floresta de saída

    [Criar confiança florestal de saída no portal Azure](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Validar a autenticação de recursos

Os seguintes cenários comuns permitem validar que a confiança florestal autentica corretamente os utilizadores e o acesso aos recursos:

* [Autenticação de utilizadores no local da floresta de recursos Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Recursos de acesso na floresta de recursos Da DS Azure utilizando utilizadores no local](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Ativar a partilha de ficheiros e impressoras](#enable-file-and-printer-sharing)
    * [Criar um grupo de segurança e adicionar membros](#create-a-security-group-and-add-members)
    * [Criar uma partilha de ficheiros para acesso cross-forest](#create-a-file-share-for-cross-forest-access)
    * [Validar a autenticação transversal a um recurso](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticação de utilizadores no local da floresta de recursos Azure AD DS

Deve ter a máquina virtual do Windows Server unida ao domínio de recursos Azure AD DS. Utilize esta máquina virtual para testar o utilizador no local pode autenticar numa máquina virtual.

1. Ligue-se ao VM do Windows Server juntou-se à floresta de recursos Azure AD DS utilizando o Remote Desktop e as credenciais de administrador do Azure AD DS. Se tiver um erro de autenticação de nível de rede (NLA), verifique se a conta de utilizador utilizada não é uma conta de utilizador de domínio.

    > [!NOTE]
    > Para ligar de forma segura aos seus VMs unidos aos Serviços de Domínio Azure AD, pode utilizar o Serviço de [Hospedagem Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) em regiões azure apoiadas.

1. Abra um pedido de comando e utilize o comando `whoami` para mostrar o nome distinto do utilizador atualmente autenticado:

    ```console
    whoami /fqdn
    ```

1. Utilize o comando `runas` para autenticar como utilizador a partir do domínio no local. No comando seguinte, substitua `userUpn@trusteddomain.com` com a UPN de um utilizador do domínio de confiança no local. O comando solicita-lhe a palavra-passe do utilizador:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se a autenticação for bem sucedida, abre-se um novo pedido de comando. O título do novo pedido de comando inclui `running as userUpn@trusteddomain.com`.
1. Utilize `whoami /fqdn` no novo pedido de comando para visualizar o nome distinto do utilizador autenticado do Diretório Ativo no local.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Recursos de acesso na floresta de recursos Da DS Azure utilizando utilizadores no local

Utilizando o VM do Windows Server, aderido à floresta de recursos Azure AD DS, pode testar o cenário em que os utilizadores podem aceder aos recursos alojados na floresta de recursos quando autenticam a partir de computadores no domínio no local com utilizadores do domínio no local. Os exemplos que se seguem mostram como criar e testar vários cenários comuns.

#### <a name="enable-file-and-printer-sharing"></a>Ativar a partilha de ficheiros e impressoras

1. Ligue-se ao VM do Windows Server juntou-se à floresta de recursos Azure AD DS utilizando o Remote Desktop e as credenciais de administrador do Azure AD DS. Se tiver um erro de autenticação de nível de rede (NLA), verifique se a conta de utilizador utilizada não é uma conta de utilizador de domínio.

    > [!NOTE]
    > Para ligar de forma segura aos seus VMs unidos aos Serviços de Domínio Azure AD, pode utilizar o Serviço de [Hospedagem Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) em regiões azure apoiadas.

1. Abra **as definições do Windows,** procure e selecione **Network and Sharing Center**.
1. Escolha a opção para Alterar definições **de partilha avançadas.**
1. No perfil de **domínio,** selecione **'Turn on file and printer sharing' e,** em seguida, **guarde alterações**.
1. Fechar **rede e centro de partilha.**

#### <a name="create-a-security-group-and-add-members"></a>Criar um grupo de segurança e adicionar membros

1. Aberto **Utilizadores e Computadores de Diretório Ativo.**
1. Selecione o nome de domínio, escolha **Novo**, e, em seguida, selecione **Unidade Organizacional**.
1. Na caixa de nomes, digite *Objetos Locais*e, em seguida, selecione **OK**.
1. Selecione e clique à direita **LocaiS Objetos** no painel de navegação. Selecione **Novo** e, em seguida, **Grupo**.
1. Tipo *FileServerAccess* na caixa **de nome sinuosa.** Para o Âmbito de **Grupo,** selecione **Domain local,** em seguida, escolha **OK**.
1. No painel de conteúdos, clique duas vezes **no FileServerAccess**. Selecione **Membros,** escolha **adicionar**e, em seguida, selecione **Localizações**.
1. Selecione o seu Diretório Ativo no local a partir da vista **Localização** e, em seguida, escolha **OK**.
1. Digite *os utilizadores* de domínio nos **nomes do objeto para selecionar** a caixa. Selecione **'Ver Nomes',** forneça credenciais para o Diretório Ativo no local e, em seguida, selecione **OK**.

    > [!NOTE]
    > Tens de fornecer credenciais porque a relação de confiança é apenas uma maneira. Isto significa que os utilizadores do Azure AD DS não podem aceder a recursos ou procurar utilizadores ou grupos no domínio confiável (no local).

1. O grupo Utilizadores de **Domínio** do seu Diretório Ativo no local deve ser membro do grupo **FileServerAccess.** Selecione **OK** para salvar o grupo e feche a janela.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Criar uma partilha de ficheiros para acesso cross-forest

1. No Windows Server VM juntou-se à floresta de recursos Azure AD DS, criar uma pasta e fornecer nomes como *CrossForestShare*.
1. Selecione a pasta à direita e escolha **Propriedades**.
1. Selecione o separador **Segurança** e, em seguida, escolha **Editar**.
1. Nas *permissões da* caixa de diálogo CrossForestShare, selecione **Adicionar**.
1. Digite *FileServerAce* in **the object names to select**, then select **OK**.
1. Selecione *FileServerAccess* da lista de nomes de **grupos ou utilizadores.** Nas **permissões da** lista de Acesso ao FileServer, escolha *permitir* as permissões **de Modificação** e **Escrita** e, em seguida, selecione **OK**.
1. Selecione o separador **Partilha** e, em seguida, escolha **Partilha Avançada...**
1. Escolha **partilhar esta pasta**e, em seguida, introduza um nome memorável para a parte de ficheiro no nome de **Partilha,** como *CrossForestShare*.
1. Selecione **Permissões**. Na lista **de Permissões para Todos,** escolha **permitir** a permissão **de alteração.**
1. Selecione **OK** duas vezes e, em seguida, **fechar**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Validar a autenticação transversal a um recurso

1. Assine num computador Windows ligado ao seu Diretório Ativo no local utilizando uma conta de utilizador a partir do seu Diretório Ativo no local.
1. Utilizando o **Windows Explorer,** ligue-se à parte que criou utilizando o nome de anfitrião totalmente qualificado e a parte como `\\fs1.aaddscontoso.com\CrossforestShare`.
1. Para validar a permissão de escrita, selecione à direita na pasta, escolha **Novo,** e selecione **Documento de Texto**. Utilize o nome predefinido **Novo Documento de Texto**.

    Se as permissões de escrita forem corretamente definidas, é criado um novo documento de texto. Os seguintes passos abrirão, editarão e eliminarão o ficheiro conforme apropriado.
1. Para validar a permissão de leitura, abra **o Novo Documento de Texto**.
1. Para validar a permissão de modificação, adicione texto ao ficheiro e feche o Bloco de **Notas**. Quando solicitado a guardar alterações, escolha **Guardar**.
1. Para validar a permissão de eliminação, selecione **o Novo Documento de Texto** e escolha **eliminar**. Escolha **Sim** para confirmar a eliminação do ficheiro.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configure dNS em um ambiente AD DS no local para apoiar a conectividade Azure AD DS
> * Criar uma confiança florestal de entrada unidirecional num ambiente ad DS no local
> * Criar um fundo florestal de saída unidirecional em Azure AD DS
> * Testar e validar a relação fidedigna para autenticação e acesso a recursos

Para obter informações mais conceptuais sobre tipos de florestas [][concepts-trust] em Azure AD DS, veja [O que são as florestas][concepts-forest] de recursos?

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
