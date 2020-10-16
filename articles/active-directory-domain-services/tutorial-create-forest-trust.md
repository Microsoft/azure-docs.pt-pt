---
title: Tutorial - Criar uma confiança florestal nos Serviços de Domínio Azure AD / Microsoft Docs
description: Saiba como criar uma floresta de ida e saída para um domínio AD DS no portal Azure para serviços de domínio Azure AD
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: cbdcd170e6c6fb768172acfe3eb3c907714cd560
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967261"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services"></a>Tutorial: Criar uma confiança florestal de saída para um domínio no local em Azure Ative Directory Domain Services

Em ambientes onde não é possível sincronizar hashes de palavra-passe, ou tem utilizadores que assinam exclusivamente usando cartões inteligentes para que não saibam a sua palavra-passe, pode utilizar uma floresta de recursos nos Serviços de Domínio do Diretório Ativo Azure (Azure AD DS). Uma floresta de recursos usa uma confiança de saída de ida da Azure AD DS para um ou mais ambientes AD DS no local. Esta relação de confiança permite que utilizadores, aplicações e computadores autentem contra um domínio no local do domínio gerido pela Azure AD DS. Numa floresta de recursos, as hashes de senha no local nunca são sincronizadas.

![Diagrama de confiança florestal de Azure AD DS para as AD DS no local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Configure o DNS num ambiente AD DS no local para apoiar a conectividade Azure AD DS
> * Criar uma confiança florestal unidireccionária num ambiente de DS AD no local
> * Criar uma única saída da floresta em Azure AD DS
> * Teste e valide a relação de confiança para a autenticação e acesso a recursos

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio gerido por Azure Ative Directory Domain Services criou usando uma floresta de recursos e configurado no seu inquilino AZure AD.
    * Se necessário, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Certifique-se de que cria um domínio gerido utilizando uma floresta *de recursos.* A opção predefinitiva cria uma floresta *de utilizadores.* Apenas as florestas de recursos podem criar fidedignidades para ambientes DS AD on-prem.
    >
    > Também precisa de utilizar um mínimo de *Enterprise* SKU para o seu domínio gerido. Se necessário, [mude o SKU para um domínio gerido][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, você cria e configura a confiança florestal de saída da Azure AD DS usando o portal Azure. Para começar, inicie primeiro o sinal no [portal Azure.](https://portal.azure.com)

## <a name="networking-considerations"></a>Considerações de redes

A rede virtual que acolhe a floresta de recursos Azure AD DS necessita de conectividade de rede para o seu Ative Directory no local. As aplicações e serviços também precisam de conectividade de rede para a rede virtual que hospeda a floresta de recursos Azure AD DS. A conectividade da rede com a floresta de recursos Azure AD DS deve estar sempre on-and estável, caso contrário os utilizadores podem não autenticar ou aceder recursos.

Antes de configurar uma confiança florestal em Azure AD DS, certifique-se de que a sua ligação em rede entre o ambiente Azure e no local cumpre os seguintes requisitos:

* Utilize endereços IP privados. Não confie no DHCP com uma atribuição dinâmica de endereço IP.
* Evite sobrepor-se a espaços de endereço IP para permitir que o espreitamento e encaminhamento de rede virtuais se comunique com sucesso entre o Azure e o local.
* Uma rede virtual Azure precisa de uma sub-rede de gateway para configurar uma ligação [VPN ou ExpressRoute (Azure site-to-site)][vpn-gateway] ou uma ligação [ExpressRoute][expressroute]
* Crie sub-redes com endereços IP suficientes para suportar o seu cenário.
* Certifique-se de que o Azure AD DS tem a sua própria sub-rede, não partilhe esta sub-rede de rede virtual com VMs e serviços de aplicação.
* As redes virtuais espreitadas NÃO são transitivas.
    * Os perspetores de rede virtuais Azure devem ser criados entre todas as redes virtuais que pretende utilizar a confiança florestal de recursos Azure AD DS para o ambiente AD DS no local.
* Forneça conectividade contínua de rede à sua floresta ative directy. Não utilize ligações a pedido.
* Certifique-se de que existe uma resolução contínua de nomes (DNS) entre o nome da floresta de recursos Azure AD DS e o nome da floresta ative Diretório no local.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configurar DNS no domínio do local

Para resolver corretamente o domínio gerido a partir do ambiente no local, poderá ser necessário adicionar reencaminhadores aos servidores DNS existentes. Se ainda não configurar o ambiente no local para comunicar com o domínio gerido, complete os seguintes passos a partir de uma estação de trabalho de gestão para o domínio AD DS no local:

1. Selecione **Iniciar / Ferramentas Administrativas / DNS**
1. Selecione o servidor DNS, como *o myAD01,* em seguida, selecione **Propriedades**
1. Escolha **forwarders**e, em seguida, **edite** para adicionar reencaminhadores adicionais.
1. Adicione os endereços IP do domínio gerido, tais como *10.0.2.4* e *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Criar confiança florestal no domínio do local

O domínio AD DS no local precisa de uma confiança florestal para o domínio gerido. Esta confiança deve ser criada manualmente no domínio AD DS no local, não pode ser criada a partir do portal Azure.

Para configurar a confiança de entrada no domínio AD DS no local, complete os seguintes passos de uma estação de trabalho de gestão para o domínio DS AD no local:

1. Selecione **Iniciar / Ferramentas Administrativas / Domínios e Fidedignidades do Diretório Ativo**
1. Domínio de seleção à direita, como *onprem.contoso.com,* em seguida, selecione **Propriedades**
1. Escolha o separador **Trusts,** em seguida, **New Trust**
1. Introduza o nome para nome de domínio Azure AD DS, como *aaddscontoso.com,* em seguida, selecione **Next**
1. Selecione a opção de criar um **fundo florestal,** em seguida, para criar uma **única maneira: confiança de entrada.**
1. Opte por criar a confiança **apenas**para este domínio . No passo seguinte, cria-se a confiança no portal Azure para o domínio gerido.
1. Opte por utilizar a **autenticação em toda**a Floresta, em seguida, insira e confirme uma senha de confiança. Esta mesma palavra-passe também é inserida no portal Azure na secção seguinte.
1. Passe pelas próximas janelas com opções predefinidas, em seguida, escolha a opção para **Não, não confirme a confiança de saída**.
1. Selecione **Acabamento**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Criar a confiança florestal de saída em Azure AD DS

Com o domínio AD DS no local configurado para resolver o domínio gerido e uma confiança florestal de entrada criada, agora criar a confiança florestal de saída. Esta confiança florestal de saída completa a relação de confiança entre o domínio DS AD no local e o domínio gerido.

Para criar a confiança de saída para o domínio gerido no portal Azure, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**, selecione o seu domínio gerido, como *aaddscontoso.com*
1. A partir do menu no lado esquerdo do domínio gerido, selecione **Trusts,** em seguida, escolha **+ Adicionar** um fundo.

   > [!NOTE]
   > Se não vir a opção do menu **Trusts,** consulte as **Propriedades** para o *tipo Forest*. Só as florestas *de recursos* podem criar fidedignidades. Se o tipo de floresta for *Utilizador,* não pode criar fidedignidades. Não há forma de mudar o tipo de floresta de um domínio gerido. É necessário eliminar e recriar o domínio gerido como uma floresta de recursos.

1. Introduza um nome de exibição que identifique a sua confiança, em seguida, o nome DNS da floresta confiável no local, como *onprem.contoso.com*
1. Forneça a mesma senha de confiança que foi usada ao configurar o fundo florestal de entrada para o domínio AD DS no local na secção anterior.
1. Fornecer pelo menos dois servidores DNS para o domínio DS AD no local, tais como *10.1.1.4* e *10.1.1.5*
1. Quando estiver pronto, **salve** a confiança da floresta de saída

    ![Criar confiança florestal de saída no portal Azure](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Validar a autenticação de recursos

Os seguintes cenários comuns permitem validar que a confiança florestal autentica corretamente os utilizadores e o acesso aos recursos:

* [Autenticação do utilizador no local da floresta de recursos Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Recursos de acesso na floresta de recursos Azure AD DS utilizando o utilizador no local](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Ativar a partilha de ficheiros e impressoras](#enable-file-and-printer-sharing)
    * [Criar um grupo de segurança e adicionar membros](#create-a-security-group-and-add-members)
    * [Criar uma partilha de ficheiros para acesso cross-forest](#create-a-file-share-for-cross-forest-access)
    * [Validar a autenticação transversal a um recurso](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticação do utilizador no local da floresta de recursos Azure AD DS

Deverá ter a máquina virtual do Windows Server unida ao domínio gerido. Utilize esta máquina virtual para testar o utilizador no local que pode autenticar numa máquina virtual. Se necessário, [crie um VM do Windows e junte-o ao domínio gerido][join-windows-vm].

1. Ligue-se ao Windows Server VM aderido à floresta de recursos Azure AD DS utilizando [O Azure Bastion](../bastion/bastion-overview.md) e as suas credenciais de administrador AD DS Azure.
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

Utilizando o VM do Servidor do Windows a que se junta a floresta de recursos Azure AD DS, é possível testar o cenário em que os utilizadores possam aceder aos recursos alojados na floresta de recursos quando autenticarem a partir de computadores no domínio do local com utilizadores do domínio do local. Os exemplos que se seguem mostram-lhe como criar e testar vários cenários comuns.

#### <a name="enable-file-and-printer-sharing"></a>Ativar a partilha de ficheiros e impressoras

1. Ligue-se ao Windows Server VM aderido à floresta de recursos Azure AD DS utilizando [O Azure Bastion](../bastion/bastion-overview.md) e as suas credenciais de administrador AD DS Azure.

1. Abra **as definições do Windows**e, em seguida, procure e selecione o Centro de Rede e **Partilha**.
1. Escolha a opção para Alterar definições **de partilha avançadas.**
1. No **perfil**de domínio , **selecione Ligue a partilha de ficheiros e impressoras** e, em seguida, **guarde as alterações**.
1. Fechar **rede e partilhar centro.**

#### <a name="create-a-security-group-and-add-members"></a>Criar um grupo de segurança e adicionar membros

1. Abra **Utilizadores e Computadores do Active Directory**.
1. Selecione o nome de domínio à direita, escolha **Novo**e, em seguida, selecione **Unidade Organizacional**.
1. Na caixa de nomes, escreva *LocalObjects,* em seguida, selecione **OK**.
1. Selecione e clique à direita **LocaObjects** no painel de navegação. Selecione **Novo** e, em seguida, **Grupo**.
1. Digite *FileServerAccess* na caixa **de nomes do grupo.** Para o Âmbito do **Grupo**, selecione **Domain local**, em seguida, escolha **OK**.
1. No painel de conteúdo, clique **duas vezes em FileServerAccess**. Selecione **Os Membros**, escolha **adicionar**e, em seguida, selecione **Localizações**.
1. Selecione o seu Diretório Ativo no local a partir da vista **Localização** e, em seguida, escolha **OK**.
1. Digite *Utilizadores de Domínio* na **Introdução dos nomes do objeto para selecionar a** caixa. Selecione **Verificar Nomes**, forneça credenciais para o Diretório Ativo no local e, em seguida, selecione **OK**.

    > [!NOTE]
    > Tens de fornecer credenciais porque a relação de confiança é apenas uma maneira. Isto significa que os utilizadores do domínio gerido AZure AD não podem aceder a recursos ou procurar utilizadores ou grupos no domínio confiável (no local).

1. O grupo de **Utilizadores** de Domínio saindo do seu Diretório Ativo no local deve ser membro do grupo **FileServerAccess.** Selecione **OK** para salvar o grupo e feche a janela.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Criar uma partilha de ficheiros para acesso cross-forest

1. No Windows Server VM juntou-se à floresta de recursos Azure AD DS, crie uma pasta e forneça nomes como *CrossForestShare*.
1. Selecione à direita a pasta e escolha **propriedades.**
1. Selecione o separador **Segurança** e, em seguida, escolha **Editar.**
1. Nas permissões para caixa de diálogo *CrossForestShare,* selecione **Add**.
1. Digite *FileServerAccess* in **Introduza os nomes do objeto para selecionar**e, em seguida, selecione **OK**.
1. *Selecione FileServerAccess* da lista **de grupos ou nomes de utilizadores.** Na lista **de Permissões para FileServerAccess,** escolha *Permitir* as permissões **de Modificação** e **Escrita** e, em seguida, selecione **OK**.
1. Selecione o separador **Partilhar** e, em seguida, escolha **Partilha Avançada...**
1. Escolha **Partilhar esta pasta**e, em seguida, introduzir um nome memorável para a partilha de **ficheiros** em nome de Partilha como *CrossForestShare*.
1. Selecione **Permissões**. Na lista **de Permissões para Todos,** escolha **Permitir** a permissão **de Alteração.**
1. Selecione **OK** duas vezes e, em seguida, **Feche**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Validar a autenticação transversal a um recurso

1. Inscreva-se num computador Windows ligado ao seu Ative Directy no local utilizando uma conta de utilizador a partir do seu Ative Directy no local.
1. Utilizando o **Windows Explorer,** ligue-se à partilha que criou utilizando o nome de anfitrião totalmente qualificado e a partilha como `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Para validar a permissão de escrita, selecione à direita na pasta, escolha **Novo**e, em seguida, selecione **Documento de Texto**. Utilize o nome predefinido **Novo Documento de Texto**.

    Se as permissões de escrita forem definidas corretamente, é criado um novo documento de texto. Os seguintes passos abrirão, editarão e eliminarão o ficheiro conforme apropriado.
1. Para validar a permissão de leitura, abra **novo documento de texto**.
1. Para validar a permissão de modificação, adicione texto ao ficheiro e feche o **Bloco de Notas**. Quando solicitado para guardar alterações, escolha **Guardar**.
1. Para validar a permissão de eliminação, selecione **o novo documento de texto** e escolha **Eliminar**. Escolha **Sim** para confirmar a eliminação de ficheiros.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configure o DNS num ambiente AD DS no local para apoiar a conectividade Azure AD DS
> * Criar uma confiança florestal unidireccionária num ambiente de DS AD no local
> * Criar uma única saída da floresta em Azure AD DS
> * Teste e valide a relação de confiança para a autenticação e acesso a recursos

Para obter mais informações conceptuais sobre os tipos de floresta em [How do forest trusts work in Azure AD DS?][concepts-trust] Azure AD DS, veja [o que são as florestas de recursos?][concepts-forest]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md