---
title: Junte-se a um VM do Windows Server para um domínio gerido  Microsoft Docs
description: Neste tutorial, aprenda a juntar uma máquina virtual do Windows Server a um domínio gerido pelo Azure Ative Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2020
ms.author: iainfou
ms.openlocfilehash: 05705d14db336b15a6ddf2317f9e69464c8e575b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239183"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Tutorial: Junte-se a uma máquina virtual do Windows Server para um domínio gerido

Os Serviços de Domínio de Diretório Ativo Azure (Azure AD DS) fornecem serviços de domínio geridos, tais como a adesão ao domínio, política de grupo, autenticação LDAP, Kerberos/NTLM que é totalmente compatível com o Diretório Ativo do Windows Server. Com um domínio gerido por Azure AD DS, pode fornecer funcionalidades de adesão de domínio e gestão a máquinas virtuais (VMs) em Azure. Este tutorial mostra-lhe como criar um VM do Servidor windows e, em seguida, junte-o a um domínio gerido pelo Azure AD DS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um VM do Servidor windows
> * Ligue o VM do Windows Server a uma rede virtual Azure
> * Junte-se ao VM ao domínio gerido pela AD DS Azure

Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, crie e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][create-azure-ad-ds-instance].
* Uma conta de utilizador que faz parte do domínio gerido pelo Azure AD DS.
    * Certifique-se de que a sincronização da palavra-passe Do Azure AD Connect ou o reset da palavra-passe autosserviço foi realizado para que a conta possa iniciar sessão no domínio gerido pelo Azure AD DS.
* Um hospedeiro Azure Bastion implantado na sua rede virtual Azure AD DS.
    * Se necessário, [crie um anfitrião do Bastião Azure.][azure-bastion]

Se já tem um VM que pretende aderir ao domínio, salte para a secção para [se juntar ao VM ao domínio gerido pelo Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, cria um VM Do Servidor Windows para se juntar ao seu domínio gerido pelo Azure AD DS utilizando o portal Azure. Para começar, inicie a inscrição no [portal Azure.](https://portal.azure.com)

## <a name="create-a-windows-server-virtual-machine"></a>Criar uma máquina virtual do Windows Server

Para ver como se juntar um computador a um domínio gerido pelo Azure AD DS, vamos criar um VM do Windows Server. Este VM está ligado a uma rede virtual Azure que fornece conectividade ao domínio gerido pelo Azure AD DS. O processo de adesão a um domínio gerido pelo Azure AD DS é o mesmo que aderir a um domínio regular de Serviços de Domínio ativo no local.

Se já tem um VM que pretende aderir ao domínio, salte para a secção para [se juntar ao VM ao domínio gerido pelo Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. A partir do menu do portal Azure ou da página **Inicial,** selecione **Criar um recurso**.

1. A partir de **Get started**, escolha **O Datacenter do Windows Server 2016**.

    ![Opte por criar um VM datacenter do Windows Server 2016 no portal Azure](./media/join-windows-vm/select-vm-image.png)

1. Na janela **Basics,** configure as definições de núcleo para a máquina virtual. Deixe os predefinições para *opções de disponibilidade,* *Imagem*e *Tamanho.*

    | Parâmetro            | Valor sugerido   |
    |----------------------|-------------------|
    | Grupo de recursos       | Selecione ou crie um grupo de recursos, como o *myResourceGroup* |
    | Nome da máquina virtual | Insira um nome para o VM, como *o myVM* |
    | Região               | Escolha a região para criar o seu VM, como *os EUA Orientais* |
    | Nome de utilizador             | Introduza um nome de utilizador para a conta de administrador local para criar no VM, como *o azureuser* |
    | Palavra-passe             | Introduza e confirme, uma senha segura para o administrador local criar no VM. Não especifique as credenciais de uma conta de utilizador de domínio. |

1. Por padrão, os VMs criados em Azure são acessíveis a partir da Internet utilizando RDP. Quando o RDP está ativado, é provável que ocorra um sinal automatizado nos ataques, o que pode desativar contas com nomes comuns, como *administrador* ou *administrador,* devido a múltiplos sinais sucessivos falhados nas tentativas.

    O RDP só deve ser ativado quando necessário, e limitado a um conjunto de gamas IP autorizadas. Esta configuração ajuda a melhorar a segurança do VM e reduz a área para potenciais ataques. Ou criar e utilizar um anfitrião do Azure Bastion que só permite o acesso através do portal Azure sobre o SSL. No próximo passo deste tutorial, você usa um anfitrião do Azure Bastion para ligar-se de forma segura ao VM.

    Por enquanto, desative as ligações diretas de RDP ao VM.

    Sob **portas de entrada pública,** selecione *Nenhuma*.

1. Quando terminar, selecione **Seguinte: Discos**.
1. A partir do menu suspenso para **o tipo de disco OS,** escolha O *SSD Standard*e, em seguida, selecione **Seguinte: Networking**.
1. O seu VM deve ligar-se a uma subrede de rede virtual Azure que possa comunicar com a subrede em que o seu domínio gerido pelo Azure AD DS está implantado. Recomendamos que um domínio gerido por AD DS Azure seja implantado na sua própria subnet dedicada. Não coloque o VM na mesma sub-rede que o seu domínio gerido pelo Azure AD DS.

    Existem duas formas principais de implantar o seu VM e ligar-se a uma subrede de rede virtual apropriada:
    
    * Crie uma subrede existente ou selecione uma subrede existente na mesma rede virtual que o seu domínio gerido pelo Azure AD DS é implantado.
    * Selecione uma sub-rede numa rede virtual Azure que esteja ligada a ela utilizando o pino da [rede virtual Azure][vnet-peering].
    
    Se selecionar uma sub-rede virtual que não esteja ligada à subnet para a sua instância Azure AD DS, não pode aderir ao VM ao domínio gerido. Para este tutorial, vamos criar uma nova subnet na rede virtual Azure.

    No painel **de networking,** selecione a rede virtual na qual o seu domínio gerido por AD DS azure é implantado, como *aaads-vnet*
1. Neste exemplo, a *sub-rede aads existente* é mostrada a que o domínio gerido pelo Azure AD DS está ligado. Não ligue o seu VM a esta sub-rede. Para criar uma sub-rede para o VM, selecione Gerir a **configuração da sub-rede**.

    ![Opte por gerir a configuração da sub-rede no portal Azure](./media/join-windows-vm/manage-subnet.png)

1. No menu à esquerda da janela de rede virtual, selecione **espaço Address**. A rede virtual é criada com um único espaço de endereço de *10.0.1.0/24,* que é utilizado pela sub-rede predefinida.

    Adicione uma gama adicional de endereços IP à rede virtual. A dimensão desta gama de endereços e a gama real de endereços IP a utilizar depende de outros recursos de rede já implantados. A gama de endereços IP não deve sobrepor-se a quaisquer gamas de endereços existentes no seu ambiente Azure ou no local. Certifique-se de que dimensiona a gama de endereços IP suficientemente grande para o número de VMs que espera implantar na sub-rede.

    No exemplo seguinte, é acrescentado um intervalo adicional de endereços IP de *10.0.2.0.0/24.* Quando estiver pronto, selecione **Guardar**.

    ![Adicione uma gama adicional de endereços IP da rede virtual no portal Azure](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Em seguida, no menu esquerdo da janela de rede virtual, selecione **Subnets,** em seguida, escolha **+ Subnet** para adicionar uma subnet.

1. Selecione **+ Subnet,** em seguida, introduza um nome para a sub-rede, como *a gestão*. Forneça uma **gama de endereços (bloco CIDR)** , como *10.0.2.0/24*. Certifique-se de que esta gama de endereços IP não se sobrepõe a quaisquer outros intervalos de endereços existentes do Azure ou no local. Deixe as outras opções como valores predefinidos e, em seguida, selecione **OK**.

    ![Criar uma configuração de sub-rede no portal Azure](./media/join-windows-vm/create-subnet.png)

1. Leva alguns segundos para criar a sub-rede. Uma vez criado, selecione o *X* para fechar a janela da sub-rede.
1. De volta ao painel **de Networking** para criar um VM, escolha a subnet que criou a partir do menu suspenso, como *a gestão.* Mais uma vez, certifique-se de que escolhe a sub-rede correta e não implemente o seu VM na mesma sub-rede que o seu domínio gerido pelo Azure AD DS.
1. Deixe as outras opções como valores predefinidos e, em seguida, selecione **Management**.
1. Desbote **os diagnósticos** da bota para *desligar*. Deixe as outras opções como valores predefinidos e, em seguida, selecione **Review + criar**.
1. Reveja as definições vM e, em seguida, selecione **Criar**.

Leva alguns minutos para criar o VM. O portal Azure mostra o estado da implantação. Quando o VM estiver pronto, selecione **Ir para o recurso**.

![Vá ao recurso VM no portal Azure uma vez que é criado com sucesso](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Ligue-se ao VM do Servidor do Windows

Para se ligar em segurança aos seus VMs, utilize um hospedeiro Azure Bastion. Com o Azure Bastion, um anfitrião gerido é implantado na sua rede virtual e fornece ligações rdp ou SSH baseadas na web aos VMs. Não são necessários endereços IP públicos para os VMs, e você não precisa abrir regras do grupo de segurança da rede para tráfego remoto externo. Liga-se a VMs utilizando o portal Azure a partir do seu navegador web.

Para utilizar um anfitrião bastião para ligar ao seu VM, complete os seguintes passos:

1. No painel **de visão geral** para o seu VM, selecione **Connect**, depois **Bastion**.

    ![Ligue-se à máquina virtual do Windows utilizando o Bastião no portal Azure](./media/join-windows-vm/connect-to-vm.png)

1. Introduza as credenciais para o seu VM que especificou na secção anterior e, em seguida, selecione **Connect**.

   ![Ligue-se através do anfitrião bastião no portal Azure](./media/join-windows-vm/connect-to-bastion.png)

Se necessário, permita que o seu navegador web abra pop-ups para que a ligação Bastião seja exibida. Leva alguns segundos para fazer a ligação com o seu VM.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Junte-se ao VM ao domínio gerido pela AD DS Azure

Com o VM criado e uma ligação RDP baseada na web estabelecida usando o Azure Bastion, vamos agora juntar a máquina virtual do Windows Server ao domínio gerido pelo Azure AD DS. Este processo é o mesmo que um computador que se conecta a um domínio regular no local de Serviços de Domínio ativo no local.

1. Se o **Gestor do Servidor** não abrir por padrão quando iniciar sessão no VM, selecione o menu **Iniciar** e, em seguida, escolha o Gestor **do Servidor**.
1. No painel esquerdo da janela do Gestor do **Servidor,** selecione **Local Server**. Em **Propriedades** no painel direito, escolha **Workgroup**.

    ![Open Server Manager no VM e editar a propriedade do grupo de trabalho](./media/join-windows-vm/server-manager.png)

1. Na janela **System Properties,** selecione **Alterar** para se juntar ao domínio gerido pelo Azure AD DS.

    ![Opte por alterar as propriedades do grupo de trabalho ou do domínio](./media/join-windows-vm/change-domain.png)

1. Na caixa **de Domínio,** especifique o nome do seu domínio gerido pelo Azure AD DS, *como*aaddscontoso.com, e, em seguida, selecione **OK**.

    ![Especificar o domínio gerido pela AD DS azure para aderir](./media/join-windows-vm/join-domain.png)

1. Introduza credenciais de domínio para se juntar ao domínio. Utilize as credenciais para um utilizador que faz parte do domínio gerido pelo Azure AD DS. A conta deve fazer parte do domínio gerido pela Azure AD DS ou pelo inquilino Azure AD - contas de diretórios externos associados ao seu inquilino Azure AD não podem autenticar corretamente durante o processo de união de domínios. As credenciais de conta podem ser especificadas de uma das seguintes formas:

    * **Formato UPN** (recomendado) - Introduza o sufixo principal do utilizador (UPN) para a conta de utilizador, tal como configurado em Azure AD. Por exemplo, o sufixo UPN do utilizador de *contosoadmina* seria `contosoadmin@aaddscontoso.onmicrosoft.com`. Existem alguns casos comuns de utilização em que o formato UPN pode ser usado de forma fiável para iniciar sessão no domínio em vez do formato *SAMAccountName:*
        * Se o prefixo UPN de um utilizador for longo, como o *nome deehasareallylongname,* o *Nome SAMAccount pode* ser autogerado.
        * Se vários utilizadores tiverem o mesmo prefixo UPN no seu inquilino Azure AD, como *o DEE,* o seu formato *SAMAccountName* poderá ser autogerado.
    * **Formato SAMAccountName** - Introduza o nome da conta no formato *SAMAccountName.* Por exemplo, o *Nome samAccount da* *contosoadmina* do utilizador seria `AADDSCONTOSO\contosoadmin`.

1. Demora alguns segundos a juntar-se ao domínio gerido pelo Azure AD DS. Quando estiver concluída, a seguinte mensagem dá-lhe as boas-vindas ao domínio:

    ![Bem-vindo ao domínio](./media/join-windows-vm/join-domain-successful.png)

    Selecione **OK** para continuar.

1. Para completar o processo para aderir ao domínio gerido pela AD DS Azure, reinicie o VM.

> [!TIP]
> Pode juntar-se a um VM utilizando o PowerShell com o cmdlet [add-computer.][add-computer] O exemplo seguinte junta-se ao domínio *AADDSCONTOSO* e, em seguida, reinicia o VM. Quando solicitado, introduza as credenciais para um utilizador que faça parte do domínio gerido pelo Azure AD DS:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Para unir um VM sem ligar-lhe e configurar manualmente a ligação, pode utilizar o [cmdlet Set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell.

Uma vez reiniciado o VM do Windows Server, quaisquer políticas aplicadas no domínio gerido pelo Azure AD DS são empurradas para o VM. Também pode agora iniciar sessão no VM do Windows Server utilizando credenciais de domínio apropriadas.

## <a name="clean-up-resources"></a>Limpar recursos

No próximo tutorial, utiliza este VM do Windows Server para instalar as ferramentas de gestão que lhe permitem administrar o domínio gerido pelo Azure AD DS. Se não quiser continuar nesta série tutorial, reveja os seguintes passos de limpeza para [eliminar o VM](#delete-the-vm). Caso contrário, [continue para o próximo tutorial.](#next-steps)

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Un-join the VM from Azure AD DS managed domain

Para remover o VM do domínio gerido pelo Azure AD DS, siga novamente os passos para [juntar o VM a um domínio](#join-the-vm-to-the-azure-ad-ds-managed-domain). Em vez de aderir ao domínio gerido pelo Azure AD DS, opte por se juntar a um grupo de trabalho, como o *WORKGROUP*padrão . Após o vM ter reiniciado, o objeto de computador é removido do domínio gerido pelo Azure AD DS.

Se [eliminar o VM](#delete-the-vm) sem se juntar ao domínio, um objeto de computador órfão é deixado em DS AD Azure.

### <a name="delete-the-vm"></a>Elimine a VM

Se não for utilizar este VM do Windows Server, elimine o VM utilizando os seguintes passos:

1. A partir do menu à esquerda, selecione **Grupos de Recursos**
1. Escolha o seu grupo de recursos, como o *myResourceGroup*.
1. Escolha o seu VM, como *o myVM,* em seguida, selecione **Eliminar**. Selecione **Sim** para confirmar a eliminação do recurso. Leva alguns minutos para apagar o VM.
1. Quando o VM for eliminado, selecione o disco OS, o cartão de interface de rede e quaisquer outros recursos com o *myVM prefixo* e elimine-os.

## <a name="troubleshoot-domain-join-issues"></a>Problemas de resolução de problemas de união de domínio

O Windows Server VM deve aderir com sucesso ao domínio gerido pelo Azure AD DS, da mesma forma que um computador regular no local se juntaria a um domínio de Serviços de Domínio de Diretório Ativo. Se o VM do Windows Server não puder aderir ao domínio gerido pelo Azure AD DS, isso indica que há um problema relacionado com conectividade ou credenciais. Reveja as seguintes secções de resolução de problemas para se juntar com sucesso ao domínio gerido.

### <a name="connectivity-issues"></a>Problemas de conectividade

Se não receber um pedido que peça credenciais para se juntar ao domínio, há um problema de conectividade. O VM não consegue alcançar o domínio gerido pelo Azure AD DS na rede virtual.

Depois de experimentar cada uma destas etapas de resolução de problemas, tente juntar-se ao VM do Windows Server novamente para o domínio gerido.

* Verifique se o VM está ligado à mesma rede virtual em que o Azure AD DS está ativado ou tem uma ligação de rede com pares.
* Tente pingar o nome de domínio DNS do domínio gerido, como `ping aaddscontoso.com`.
    * Se o pedido de ping falhar, tente adoeça os endereços IP para o domínio gerido, como `ping 10.0.0.4`. O endereço IP para o seu ambiente é apresentado na página *Propriedades* quando selecionar o domínio gerido pelo Azure AD DS a partir da sua lista de recursos Azure.
    * Se conseguir obter o endereço IP, mas não o domínio, o DNS pode estar incorretamente configurado. Confirme que os endereços IP do domínio gerido estão configurados como servidores DNS para a rede virtual.
* Tente descarregar a cache de resolver DNS na máquina virtual utilizando o comando `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Questões relacionadas com credenciais

Se receber uma solicitação que pede credenciais para aderir ao domínio, mas depois de um erro depois de introduzir essas credenciais, o VM é capaz de se ligar ao domínio gerido pelo Azure AD DS. As credenciais que forneceu não deixam então que o VM se junte ao domínio gerido pela AD DS azure.

Depois de experimentar cada uma destas etapas de resolução de problemas, tente juntar-se ao VM do Windows Server novamente para o domínio gerido.

* Certifique-se de que a conta de utilizador que especifica pertence ao domínio gerido pelo Azure AD DS.
* Confirme que a conta faz parte do domínio gerido pela Azure AD DS ou pelo inquilino azure AD. As contas de diretórios externos associados ao seu inquilino Azure AD não podem autenticar corretamente durante o processo de união de domínios.
* Tente utilizar o formato UPN para especificar credenciais, como `contosoadmin@aaddscontoso.onmicrosoft.com`. Se houver muitos utilizadores com o mesmo prefixo UPN no seu inquilino ou se o seu prefixo UPN for excessivamente longo, o *Nome SAMAccount para* a sua conta pode ser autogerado. Nestes casos, o formato *SAMAccountName* para a sua conta pode ser diferente do que espera ou utiliza no seu domínio no local.
* Verifique se permitiu a sincronização da [palavra-passe][password-sync] no seu domínio gerido. Sem este passo de configuração, as hashes de senha necessárias não estarão presentes no domínio gerido pelo Azure AD DS para autenticar corretamente o seu sinal na tentativa.
* Aguarde a sincronização da palavra-passe. Quando a palavra-passe de uma conta de utilizador é alterada, uma sincronização automática de fundo da Azure AD atualiza a palavra-passe no Azure ADDS. Leva algum tempo para que a palavra-passe esteja disponível para uso de união de domínio.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um VM do Servidor windows
> * Ligue-se ao VM do Windows Server a uma rede virtual Azure
> * Junte-se ao VM ao domínio gerido pela AD DS Azure

Para administrar o seu domínio gerido pelo Azure AD DS, configure um VM de gestão utilizando o Ative Directory Administrative Center (ADAC).

> [!div class="nextstepaction"]
> [Instale ferramentas de administração num VM de gestão](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
