---
title: Junte-se a um Windows Server VM a um domínio gerido por Azure AD Domain Services Microsoft Docs
description: Neste tutorial, aprenda a juntar uma máquina virtual do Windows Server a um domínio gerido por Serviços de Domínio do Diretório Ativo Azure.
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 3df96f5576829694b5eb12fd1811de112279884d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963232"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Junte uma máquina virtual do Windows Server a um domínio gerido por Serviços de Domínio do Diretório Ativo Azure

Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a junção de domínio, política de grupo, autenticação de LDAP, Kerberos/NTLM que é totalmente compatível com o Windows Server Ative Directory. Com um domínio gerido AD DS Azure, pode fornecer funcionalidades de junção de domínio e gestão a máquinas virtuais (VMs) em Azure. Este tutorial mostra-lhe como criar um VM do Servidor do Windows e, em seguida, junte-o a um domínio gerido.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar um VM do Servidor do Windows
> * Ligue o Windows Server VM a uma rede virtual Azure
> * Junte-se ao VM ao domínio gerido

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de utilizador que faz parte do domínio gerido.
    * Certifique-se de que a sincronização de hash de senha de ligação a password Azure Ou a redefinição da palavra-passe de autosserviço foi executada para que a conta seja capaz de iniciar scontabilidade para o domínio gerido.
* Um anfitrião Azure Bastion implantado na sua rede virtual Azure AD DS.
    * Se necessário, [crie um anfitrião Azure Bastion][azure-bastion].

Se já tem um VM que pretende juntar-se ao domínio, salte para a secção para [se juntar ao VM ao domínio gerido](#join-the-vm-to-the-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, cria um VM do Windows Server para se juntar ao seu domínio gerido utilizando o portal Azure. Para começar, inicie primeiro o sinal no [portal Azure.](https://portal.azure.com)

## <a name="create-a-windows-server-virtual-machine"></a>Criar uma máquina virtual do Windows Server

Para ver como juntar um computador a um domínio gerido, vamos criar um VM do Servidor do Windows. Este VM está ligado a uma rede virtual Azure que fornece conectividade ao domínio gerido. O processo de adesão a um domínio gerido é o mesmo que juntar um domínio regular de Serviços de Domínio de Diretório Ativo no local.

Se já tem um VM que pretende juntar-se ao domínio, salte para a secção para [se juntar ao VM ao domínio gerido](#join-the-vm-to-the-managed-domain).

1. A partir do menu do portal Azure ou da página **Inicial,** selecione **Criar um recurso**.

1. A partir de **Começar**, escolha **o Centro de Dados do Windows Server 2016**.

    ![Opte por criar um VM do Datacenter 2016 do Windows Server no portal Azure](./media/join-windows-vm/select-vm-image.png)

1. Na janela **Basics,** configufique as definições do núcleo para a máquina virtual. Deixe as predefinições para *opções de disponibilidade,* *imagem*e *tamanho*.

    | Parâmetro            | Valor sugerido   |
    |----------------------|-------------------|
    | Grupo de recursos       | Selecione ou crie um grupo de recursos, como *o myResourceGroup* |
    | Nome da máquina virtual | Insira um nome para o VM, como *myVM* |
    | Região               | Escolha a região para criar o seu VM em, como *o Leste dos EUA* |
    | Nome de utilizador             | Introduza um nome de utilizador para a conta de administrador local para criar no VM, como *azureuser* |
    | Palavra-passe             | Introduza e confirme, uma senha segura para o administrador local criar no VM. Não especifique as credenciais de uma conta de utilizador de domínio. |

1. Por padrão, os VMs criados em Azure são acessíveis a partir da Internet usando RDP. Quando o RDP está ativado, é provável que ocorram ataques de sing-in automatizados, o que pode desativar contas com nomes comuns, como *administração* ou *administrador,* devido a múltiplas tentativas de inscrição sucessivas falhadas.

    O PDR só deve ser ativado quando necessário, e limitado a um conjunto de intervalos de IP autorizados. Esta configuração ajuda a melhorar a segurança do VM e reduz a área para um potencial ataque. Ou, crie e use um anfitrião Azure Bastion que permita o acesso apenas através do portal Azure sobre TLS. No próximo passo deste tutorial, você usa um anfitrião Azure Bastion para ligar de forma segura ao VM.

    Sob **portas públicas de entrada,** selecione *Nenhum*.

1. Quando terminar, selecione **Seguinte: Discos**.
1. A partir do menu suspenso para o **tipo de disco OS,** escolha *Standard SSD*e, em seguida, selecione **Seguinte: Networking**.
1. O seu VM deve ligar-se a uma sub-rede virtual Azure que possa comunicar com a sub-rede onde o seu domínio gerido é implantado. Recomendamos que um domínio gerido seja implantado na sua própria sub-rede dedicada. Não coloque o seu VM na mesma sub-rede que o seu domínio gerido.

    Existem duas formas principais de implantar o seu VM e ligar-se a uma sub-rede de rede virtual apropriada:
    
    * Crie uma sub-rede existente na mesma rede virtual que o seu domínio gerido.
    * Selecione uma sub-rede numa rede virtual Azure que está ligada a ela utilizando [o espreitamento da rede virtual Azure.][vnet-peering]
    
    Se selecionar uma sub-rede de rede virtual que não esteja ligada à sub-rede para o seu domínio gerido, não pode juntar-se ao VM ao domínio gerido. Para este tutorial, vamos criar uma nova sub-rede na rede virtual Azure.

    No **painel de rede,** selecione a rede virtual na qual o seu domínio gerido é implantado, tal como *aaads-vnet*
1. Neste exemplo, a *sub-rede aaads existente* é demonstrada de que o domínio gerido está ligado. Não ligue o seu VM a esta sub-rede. Para criar uma sub-rede para o VM, **selecione Gerir a configuração da sub-rede**.

    ![Opte por gerir a configuração da sub-rede no portal Azure](./media/join-windows-vm/manage-subnet.png)

1. No menu à esquerda da janela de rede virtual, selecione **Espaço de endereço**. A rede virtual é criada com um espaço de endereço único de *10.0.2.0/24*, que é utilizada pela sub-rede predefinido. Outras sub-redes, como para *cargas de trabalho* ou Bastião Azure também podem existir.

    Adicione um intervalo adicional de endereços IP à rede virtual. A dimensão desta gama de endereços e a gama de endereços IP real para utilizar dependem de outros recursos de rede já implantados. O intervalo de endereços IP não deve sobrepor-se a quaisquer intervalos de endereços existentes no seu ambiente Azure ou no local. Certifique-se de que dimensiona o intervalo de endereço IP suficientemente grande para o número de VMs que espera implantar na sub-rede.

    No exemplo seguinte, é adicionado um intervalo adicional de endereço IP de *10.0.5.0/24.* Quando estiver pronto, **selecione Guardar**.

    ![Adicione uma gama adicional de endereços IP de rede virtual no portal Azure](./media/join-windows-vm/add-vnet-address-range.png)

1. Em seguida, no menu à esquerda da janela de rede virtual, selecione **Subnetas**e, em seguida, escolha **+ Subnet** para adicionar uma sub-rede.

1. Selecione **+ Sub-rede,** em seguida, insira um nome para a sub-rede, como *a gestão*. Fornecer uma **gama de endereços (bloco CIDR)**, como *10.0.5.0/24*. Certifique-se de que este intervalo de endereços IP não se sobrepõe a quaisquer outros intervalos de endereços Azure ou no local. Deixe as outras opções como valores predefinidos e, em seguida, selecione **OK**.

    ![Criar uma configuração de sub-rede no portal Azure](./media/join-windows-vm/create-subnet.png)

1. Leva alguns segundos para criar a sub-rede. Uma vez criado, selecione o *X* para fechar a janela da sub-rede.
1. De volta ao painel **de networking** para criar um VM, escolha a sub-rede que criou a partir do menu suspenso, como *a gestão*. Mais uma vez, certifique-se de que escolhe a sub-rede correta e não implanta o seu VM na mesma sub-rede que o seu domínio gerido.
1. Para **IP público**, selecione *Nenhum* do menu suspenso. Ao utilizar o Azure Bastion neste tutorial para se ligar à gestão, não precisa de um endereço IP público atribuído ao VM.
1. Deixe as outras opções como valores predefinidos e, em seguida, **selecione Gestão**.
1. Desema **esta semana, desemarr os diagnósticos** *da*Bota . Deixe as outras opções como valores predefinidos e, em seguida, selecione **Review + create**.
1. Reveja as definições de VM e, em seguida, **selecione Criar**.

Leva alguns minutos para criar o VM. O portal Azure mostra o estado da implantação. Assim que o VM estiver pronto, selecione **Ir para o recurso**.

![Vá ao recurso VM no portal Azure uma vez que é criado com sucesso](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Ligue-se ao VM do Servidor do Windows

Para ligar de forma segura aos seus VMs, utilize um anfitrião Azure Bastion. Com o Azure Bastion, um hospedeiro gerido é implantado na sua rede virtual e fornece ligações RDP ou SSH baseadas na Web a VMs. Não são necessários endereços IP públicos para os VMs, e não é necessário abrir regras de grupo de segurança de rede para tráfego remoto externo. Você se conecta a VMs usando o portal Azure a partir do seu navegador web. Se necessário, [crie um anfitrião Azure Bastion][azure-bastion].

Para utilizar um anfitrião bastonário para ligar ao seu VM, complete os seguintes passos:

1. No **painel de visão geral** para o seu VM, selecione **Connect**, em **seguida, Bastion**.

    ![Ligue à máquina virtual do Windows utilizando o Bastion no portal Azure](./media/join-windows-vm/connect-to-vm.png)

1. Introduza as credenciais para o seu VM que especificou na secção anterior e, em seguida, selecione **Connect**.

   ![Conecte-se através do hospedeiro Bastião no portal Azure](./media/join-windows-vm/connect-to-bastion.png)

Se necessário, permita que o seu navegador da Web abra pop-ups para a ligação Bastion ser exibida. Demora alguns segundos a fazer a ligação ao seu VM.

## <a name="join-the-vm-to-the-managed-domain"></a>Junte-se ao VM ao domínio gerido

Com o VM criado e uma ligação RDP baseada na web estabelecida usando Azure Bastion, vamos agora juntar a máquina virtual do Windows Server ao domínio gerido. Este processo é o mesmo que um computador que se conecta a um domínio regular de Serviços de Domínio de Diretório Ativo no local.

1. Se **o Gestor do Servidor** não abrir por predefinição quando iniciar serção no VM, selecione o menu **Iniciar** e, em seguida, escolha o Gestor **do Servidor**.
1. No painel esquerdo da janela Do Gestor do **Servidor,** selecione **Local Server**. Em **Propriedades** no painel direito, escolha **Grupo de Trabalho.**

    ![Abrir o Gestor do Servidor no VM e editar a propriedade do grupo de trabalho](./media/join-windows-vm/server-manager.png)

1. Na janela Propriedades do **Sistema,** selecione **Alterar** para se juntar ao domínio gerido.

    ![Opte por alterar as propriedades do grupo de trabalho ou do domínio](./media/join-windows-vm/change-domain.png)

1. Na caixa **de Domínio,** especifique o nome do seu domínio gerido, como *aaddscontoso.com,* selecione **OK**.

    ![Especifique o domínio gerido para aderir](./media/join-windows-vm/join-domain.png)

1. Introduza credenciais de domínio para se juntar ao domínio. Fornecer credenciais para um utilizador que faz parte do domínio gerido. A conta deve fazer parte do domínio gerido ou inquilino Azure AD - contas de diretórios externos associados ao seu inquilino AD AZure não podem autenticar corretamente durante o processo de junção de domínio.

    As credenciais de conta podem ser especificadas de uma das seguintes formas:

    * **Formato UPN** (recomendado) - Introduza o sufixo do nome principal do utilizador (UPN) para a conta do utilizador, tal como configurado em Azure AD. Por exemplo, o sufixo UPN do *utilizador contosoadmin* seria `contosoadmin@aaddscontoso.onmicrosoft.com` . Existem alguns casos de uso comuns em que o formato UPN pode ser usado de forma fiável para iniciar sposição no domínio em vez do formato *SAMAccountName:*
        * Se o prefixo UPN de um utilizador for longo, como *o deehasareallylongname,* o *SAMAccountName* pode ser autogerido.
        * Se vários utilizadores tiverem o mesmo prefixo UPN no seu inquilino AZure AD, como *dee,* o seu formato *SAMAccountName* pode ser autogerido.
    * **SAMAccountName -** Introduza o nome da conta no formato *SAMAccountName.* Por exemplo, o *SAMAccountName* do *utilizador contosoadmin* seria `AADDSCONTOSO\contosoadmin` .

1. Leva alguns segundos para se juntar ao domínio gerido. Quando concluída, a seguinte mensagem dá-lhe as boas-vindas ao domínio:

    ![Bem-vindo ao domínio](./media/join-windows-vm/join-domain-successful.png)

    Selecione **OK** para continuar.

1. Para completar o processo de junção ao domínio gerido, reinicie o VM.

> [!TIP]
> Pode juntar-se a um VM utilizando o PowerShell com o [cmdlet Add-Computer.][add-computer] O exemplo a seguir junta-se ao domínio *AADDSCONTOSO* e, em seguida, reinicia o VM. Quando solicitado, insira as credenciais para um utilizador que faz parte do domínio gerido:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Para unir um VM sem ligar a ele e configurar manualmente a ligação, pode utilizar o [cmdlet De Defini-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell.

Uma vez reiniciado o VM do Servidor do Windows, quaisquer políticas aplicadas no domínio gerido são empurradas para o VM. Também pode agora iniciar serção no VM do Servidor do Windows utilizando credenciais de domínio apropriadas.

## <a name="clean-up-resources"></a>Limpar recursos

No próximo tutorial, utiliza este VM do Windows Server para instalar as ferramentas de gestão que lhe permitem administrar o domínio gerido. Se não quiser continuar nesta série tutorial, reveja os seguintes passos de limpeza para [eliminar o VM](#delete-the-vm). Caso contrário, [continue para o próximo tutorial.](#next-steps)

### <a name="unjoin-the-vm-from-the-managed-domain"></a>Unjoin the VM from the managed domain

Para remover o VM do domínio gerido, siga novamente os passos para [juntar o VM a um domínio](#join-the-vm-to-the-managed-domain). Em vez de aderir ao domínio gerido, opte por se juntar a um grupo de trabalho, como o *GRUPO DE TRABALHO*predefinido . Depois de o VM ter sido reiniciado, o objeto do computador é removido do domínio gerido.

Se [eliminar o VM](#delete-the-vm) sem se juntar ao domínio, um objeto de computador órfão é deixado em Azure AD DS.

### <a name="delete-the-vm"></a>Elimine a VM

Se não utilizar este VM do Windows Server, elimine o VM utilizando os seguintes passos:

1. A partir do menu à esquerda, selecione **grupos de recursos**
1. Escolha o seu grupo de recursos, como *o myResourceGroup.*
1. Escolha o seu VM, como *o myVM,* e, em seguida, selecione **Delete**. Selecione **Sim** para confirmar a eliminação de recursos. Demora alguns minutos a apagar o VM.
1. Quando o VM for eliminado, selecione o disco DE, o cartão de interface de rede e quaisquer outros recursos com o prefixo *myVM* e elimine-os.

## <a name="troubleshoot-domain-join-issues"></a>Problemas de resolução de domínios

O VM do Servidor do Windows deve aderir com sucesso ao domínio gerido, da mesma forma que um computador regular no local se juntaria a um domínio de Serviços de Domínio de Diretório Ativo. Se o VM do Servidor do Windows não puder aderir ao domínio gerido, isso indica que existe um problema de conectividade ou de credenciais. Reveja as seguintes secções de resolução de problemas para se juntar com sucesso ao domínio gerido.

### <a name="connectivity-issues"></a>Problemas de conectividade

Se não receber uma solicitação que pede credenciais para se juntar ao domínio, há um problema de conectividade. O VM não consegue alcançar o domínio gerido na rede virtual.

Depois de experimentar cada um destes passos de resolução de problemas, tente juntar o VM do Windows Server novamente ao domínio gerido.

* Verifique se o VM está ligado à mesma rede virtual em que o Azure AD DS está ativado ou tem uma ligação de rede esprevada.
* Tente verificar o nome de domínio DNS do domínio gerido, como `ping aaddscontoso.com` .
    * Se o pedido de ping falhar, tente verificar os endereços IP para o domínio gerido, tais como `ping 10.0.0.4` . O endereço IP para o seu ambiente é apresentado na página *Propriedades* quando seleciona o domínio gerido a partir da sua lista de recursos Azure.
    * Se conseguir verificar o endereço IP mas não o domínio, o DNS pode estar configurado incorretamente. Confirme que os endereços IP do domínio gerido são configurados como servidores DNS para a rede virtual.
* Tente descarregar a cache de resolver DNS na máquina virtual utilizando o `ipconfig /flushdns` comando.

### <a name="credentials-related-issues"></a>Questões relacionadas com credenciais

Se receber uma solicitação que peça credenciais para se juntar ao domínio, mas depois de introduzir essas credenciais, o VM é capaz de se ligar ao domínio gerido. As credenciais que forneceu não permitem então que o VM se junte ao domínio gerido.

Depois de experimentar cada um destes passos de resolução de problemas, tente juntar o VM do Windows Server novamente ao domínio gerido.

* Certifique-se de que a conta de utilizador que especifica pertence ao domínio gerido.
* Confirme que a conta faz parte do domínio gerido ou inquilino da AD Azure. As contas de diretórios externos associados ao seu inquilino AZure AD não podem autenticar corretamente durante o processo de junção de domínios.
* Tente utilizar o formato UPN para especificar credenciais, tais como `contosoadmin@aaddscontoso.onmicrosoft.com` . Se houver muitos utilizadores com o mesmo prefixo UPN no seu inquilino ou se o seu prefixo UPN for excessivamente longo, o *SAMAccountName* para a sua conta pode ser autogerido. Nestes casos, o formato *SAMAccountName* para a sua conta pode ser diferente do que espera ou utiliza no seu domínio no local.
* Verifique se [ativou a sincronização de palavras-passe][password-sync] no seu domínio gerido. Sem este passo de configuração, os hashes de senha necessários não estarão presentes no domínio gerido para autenticar corretamente a sua tentativa de entrada.
* Aguarde a conclusão da sincronização da palavra-passe. Quando a palavra-passe de uma conta de utilizador é alterada, uma sincronização automática de fundo a partir do Azure AD atualiza a palavra-passe em Azure AD DS. Leva algum tempo para que a palavra-passe esteja disponível para uso de união de domínio.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um VM do Servidor do Windows
> * Ligue-se ao Windows Server VM a uma rede virtual Azure
> * Junte-se ao VM ao domínio gerido

Para administrar o seu domínio gerido, configuure um VM de gestão utilizando o Ative Directory Administrative Center (ADAC).

> [!div class="nextstepaction"]
> [Instale ferramentas de administração num VM de gestão](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension