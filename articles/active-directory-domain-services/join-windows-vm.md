---
title: Ingressar uma VM do Windows Server em um domínio gerenciado | Microsoft Docs
description: Neste tutorial, saiba como unir uma máquina virtual do Windows Server a um Azure Active Directory Domain Services domínio gerenciado.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 164ba5ff7be38d3b11a8c5f8e5c76a3ff19ff508
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172917"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Tutorial: ingressar uma máquina virtual do Windows Server em um domínio gerenciado

O Azure Active Directory Domain Services (Azure AD DS) fornece serviços de domínio gerenciados, como ingresso no domínio, diretiva de grupo, LDAP, autenticação Kerberos/NTLM que é totalmente compatível com o Windows Server Active Directory. Com um domínio gerenciado do Azure AD DS, você pode fornecer recursos de ingresso no domínio e gerenciamento para VMs (máquinas virtuais) no Azure. Este tutorial mostra como criar uma VM do Windows Server e, em seguida, associá-la a um domínio gerenciado AD DS do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM do Windows Server
> * Conectar-se à VM do Windows Server a uma rede virtual do Azure
> * Ingresse a VM no domínio gerenciado AD DS do Azure

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, [crie e configure uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é membro do grupo de *Administradores de DC do Azure ad* em seu locatário do Azure AD.
    * Certifique-se de que Azure AD Connect sincronização de hash de senha ou redefinição de senha de autoatendimento tenha sido executada para que a conta seja capaz de entrar no Azure AD DS domínio gerenciado.

Se você já tiver uma VM que deseja ingressar no domínio, pule para a seção para [ingressar a VM no domínio gerenciado AD DS do Azure](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, você cria uma VM do Windows Server para ingressar em seu domínio gerenciado AD DS do Azure usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Criar uma máquina virtual do Windows Server

Para ver como unir um computador a um domínio gerenciado AD DS do Azure, vamos criar uma VM do Windows Server. Essa VM está conectada a uma rede virtual do Azure que fornece conectividade com o domínio gerenciado AD DS do Azure. O processo para ingressar em um domínio gerenciado do Azure AD DS é o mesmo que ingressar em um domínio de Active Directory Domain Services local regular.

Se você já tiver uma VM que deseja ingressar no domínio, pule para a seção para [ingressar a VM no domínio gerenciado AD DS do Azure](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. No canto superior esquerdo da portal do Azure, selecione **+ criar um recurso**.
1. Em **introdução**, escolha **Windows Server 2016 datacenter**.

    ![Escolha criar uma VM do Windows Server 2016 datacenter no portal do Azure](./media/join-windows-vm/select-vm-image.png)

1. Na janela **noções básicas** , defina as configurações principais para a máquina virtual. Deixe os padrões para *Opções de disponibilidade*, *imagem*e *tamanho*.

    | Parâmetro            | Valor sugerido   |
    |----------------------|-------------------|
    | Grupo de recursos       | Selecionar ou criar um grupo de recursos, como *MyResource* Group |
    | Nome da máquina virtual | Insira um nome para a VM, como *myVM* |
    | Região               | Escolha a região na qual criar sua VM, como *leste dos EUA* |
    | Nome de utilizador             | Insira um nome de usuário para a conta de administrador local a ser criada na VM, como *azureuser* |
    | Palavra-passe             | Insira e, em seguida, confirme uma senha segura para o administrador local criar na VM. Não especifique as credenciais de uma conta de usuário de domínio. |

1. Por padrão, as VMs criadas no Azure não podem ser acessadas pela Internet. Essa configuração ajuda a melhorar a segurança da VM e reduz a área para ataques potenciais. Na próxima etapa deste tutorial, você precisa se conectar à VM usando o protocolo RDP e, em seguida, ingressar o Windows Server no domínio gerenciado AD DS do Azure.

    Quando o RDP está habilitado, é provável que os ataques de conexão automatizada ocorram, o que pode desabilitar contas com nomes comuns, como *admin* ou *administrador* , devido a várias tentativas de entrada sucessivas com falha. O RDP só deve ser habilitado quando necessário e limitado a um conjunto de intervalos de IP autorizados. O [acesso à VM just in time do Azure][jit-access] como parte da central de segurança do Azure pode habilitar essas sessões RDP restritas e de curta duração. Você também pode [criar e usar um host de bastiões do Azure (atualmente em versão prévia)][azure-bastion] para permitir o acesso somente por meio do portal do Azure sobre SSL.

    Para este tutorial, habilite manualmente as conexões RDP para a VM.

    Em **portas de entrada públicas**, selecione a opção para **permitir portas selecionadas**. No menu suspenso para **selecionar portas de entrada**, escolha *RDP (3389)* .

1. Quando terminar, selecione **Avançar: discos**.
1. No menu suspenso do **tipo de disco do sistema operacional**, escolha *SSD Standard*e, em seguida, selecione **Avançar: rede**.
1. Sua VM deve se conectar a uma sub-rede de rede virtual do Azure que pode se comunicar com a sub-rede em que o domínio gerenciado do Azure AD DS está implantado. É recomendável que um domínio gerenciado AD DS do Azure seja implantado em sua própria sub-rede dedicada. Não implante sua VM na mesma sub-rede que o seu domínio gerenciado AD DS do Azure.

    Há duas maneiras principais de implantar sua VM e conectar-se a uma sub-rede de rede virtual apropriada:
    
    * Crie um ou selecione uma sub-rede existente na mesma rede virtual que o domínio gerenciado do Azure AD DS for implantado.
    * Selecione uma sub-rede em uma rede virtual do Azure que esteja conectada a ela usando o [emparelhamento de rede virtual do Azure][vnet-peering].
    
    Se você selecionar uma sub-rede de rede virtual que não esteja conectada à sub-rede para sua instância de AD DS do Azure, não será possível ingressar a VM no domínio gerenciado. Para este tutorial, vamos criar uma nova sub-rede na rede virtual do Azure.

    No painel **rede** , selecione a rede virtual na qual o domínio gerenciado pelo AD DS do Azure é implantado, como *aaads-vnet*
1. Neste exemplo, a *aaads* existente é mostrada para a qual o domínio gerenciado do Azure AD DS está conectado. Não conecte sua VM a esta sub-rede. Para criar uma sub-rede para a VM, selecione **gerenciar configuração de sub-rede**.

    ![Escolha gerenciar a configuração de sub-rede no portal do Azure](./media/join-windows-vm/manage-subnet.png)

1. No menu à esquerda da janela rede virtual, selecione **espaço de endereço**. A rede virtual é criada com um único espaço de endereço de *10.0.1.0/24*, que é usado pela sub-rede padrão.

    Adicione um intervalo de endereços IP adicional à rede virtual. O tamanho desse intervalo de endereços e o intervalo de endereços IP real a ser usado dependem de outros recursos de rede já implantados. O intervalo de endereços IP não deve se sobrepor a quaisquer intervalos de endereços existentes em seu ambiente do Azure ou local. Certifique-se de dimensionar o intervalo de endereços IP grande o suficiente para o número de VMs que você espera implantar na sub-rede.

    No exemplo a seguir, um intervalo de endereços IP adicional de *10.0.2.0/24* é adicionado. Quando estiver pronto, selecione **salvar**.

    ![Adicionar um intervalo de endereços IP de rede virtual adicional no portal do Azure](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Em seguida, no menu à esquerda da janela rede virtual, selecione sub- **redes**e, em seguida, escolha **+ sub** -rede para adicionar uma sub-rede.

1. Selecione **+ sub-rede**e insira um nome para a sub-rede, como *Gerenciamento*. Forneça um **intervalo de endereços (bloco CIDR)** , como *10.0.2.0/24*. Verifique se esse intervalo de endereços IP não se sobrepõe a outros intervalos de endereços do Azure ou locais existentes. Deixe as outras opções como seus valores padrão e selecione **OK**.

    ![Criar uma configuração de sub-rede no portal do Azure](./media/join-windows-vm/create-subnet.png)

1. Leva alguns segundos para criar a sub-rede. Após a criação, selecione o *X* para fechar a janela de sub-rede.
1. De volta ao painel de **rede** para criar uma VM, escolha a sub-rede que você criou no menu suspenso, como *Gerenciamento*. Novamente, certifique-se de escolher a sub-rede correta e não implantar sua VM na mesma sub-rede que o seu domínio gerenciado do Azure AD DS.
1. Deixe as outras opções como seus valores padrão e, em seguida, selecione **Gerenciamento**.
1. Defina **diagnóstico de inicialização** como *desativado*. Deixe as outras opções como seus valores padrão e selecione **revisar + criar**.
1. Examine as configurações da VM e, em seguida, selecione **criar**.

Leva alguns minutos para criar a VM. O portal do Azure mostra o status da implantação. Quando a VM estiver pronta, selecione **ir para o recurso**.

![Vá para o recurso de VM no portal do Azure depois que ele for criado com êxito](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Conectar-se à VM do Windows Server

Agora, vamos conectar-se à VM do Windows Server recém-criada usando o RDP e ingressar no domínio gerenciado do Azure AD DS. Use as credenciais de administrador local que você especificou quando a VM foi criada na etapa anterior, e não as credenciais de domínio existentes.

1. No painel **visão geral** , selecione **conectar**.

    ![Conectar-se à máquina virtual do Windows no portal do Azure](./media/join-windows-vm/connect-to-vm.png)

1. Selecione a opção para *baixar o arquivo RDP*. Salve este arquivo RDP em seu navegador da Web.
1. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for pedido, selecione **Ligar**.
1. Insira as credenciais de administrador local que você inseriu na etapa anterior para criar a VM, como *localhost\azureuser*
1. Se você vir um aviso de certificado durante o processo de entrada, selecione **Sim** ou **continuar** a se conectar.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Ingresse a VM no domínio gerenciado AD DS do Azure

Com a VM criada e uma conexão de RDP estabelecer, agora vamos unir a máquina virtual do Windows Server ao domínio gerenciado AD DS do Azure. Esse processo é o mesmo que um computador que se conecta a um domínio Active Directory Domain Services local regular.

1. Se **Gerenciador do servidor** não abrir por padrão quando você entrar na VM, selecione o menu **Iniciar** e, em seguida, escolha **Gerenciador do servidor**.
1. No painel esquerdo da janela **Gerenciador do servidor** , selecione **servidor local**. Em **Propriedades** no painel direito, escolha **grupo de trabalho**.

    ![Abra Gerenciador do Servidor na VM e edite a propriedade grupo de trabalho](./media/join-windows-vm/server-manager.png)

1. Na janela **Propriedades do sistema** , selecione **alterar** para ingressar no domínio gerenciado AD DS do Azure.

    ![Escolha alterar as propriedades de grupo de trabalho ou domínio](./media/join-windows-vm/change-domain.png)

1. Na caixa **domínio** , especifique o nome do seu domínio gerenciado AD DS do Azure, como *contoso.com*, e selecione **OK**.

    ![Especifique o domínio gerenciado AD DS do Azure para ingressar](./media/join-windows-vm/join-domain.png)

1. Insira as credenciais de domínio para ingressar no domínio. Use as credenciais para um usuário que pertence ao grupo de *Administradores de DC do Azure ad* . Somente os membros desse grupo têm privilégios para ingressar computadores no domínio gerenciado AD DS do Azure. A conta deve fazer parte do domínio gerenciado do Azure AD DS ou as contas de locatário do Azure AD de diretórios externos associados ao seu locatário do Azure AD não podem ser autenticadas corretamente durante o processo de ingresso no domínio. As credenciais da conta podem ser especificadas de uma das seguintes maneiras:

    * **Formato UPN** (recomendado) – Insira o sufixo UPN (nome principal do usuário) para a conta de usuário, conforme configurado no Azure AD. Por exemplo, o sufixo UPN do usuário *contosoadmin* seria `contosoadmin@contoso.onmicrosoft.com`. Há alguns casos de uso comuns em que o formato UPN pode ser usado de forma confiável para entrar no domínio em vez do formato *sAMAccountName* :
        * Se o prefixo UPN de um usuário for longo, como *deehasareallylongname*, o *sAMAccountName* poderá ser gerado automaticamente.
        * Se vários usuários tiverem o mesmo prefixo UPN em seu locatário do Azure AD, como *Dee*, seu formato *sAMAccountName* poderá ser gerado automaticamente.
    * **Formato sAMAccountName** – Insira o nome da conta no formato *sAMAccountName* . Por exemplo, o *sAMAccountName* do usuário *contosoadmin* seria `CONTOSO\contosoadmin`.

1. Leva alguns segundos para ingressar no domínio gerenciado AD DS do Azure. Ao concluir, a seguinte mensagem lhe informará o domínio:

    ![Bem-vindo ao domínio](./media/join-windows-vm/join-domain-successful.png)

    Selecione **OK** para continuar.

1. Para concluir o processo de ingresso no domínio gerenciado AD DS do Azure, reinicie a VM.

> [!TIP]
> Você pode ingressar em um domínio em uma VM usando o PowerShell com o cmdlet [Add-Computer][add-computer] . O exemplo a seguir une o domínio *contoso* e, em seguida, reinicia a VM. Quando solicitado, insira as credenciais para um usuário que pertence ao grupo de *Administradores de DC do Azure ad* :
>
> `Add-Computer -DomainName CONTOSO -Restart`
>
> Para ingressar no domínio em uma VM sem se conectar a ela e configurar a conexão manualmente, você pode usar o cmdlet [set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell.

Depois que a VM do Windows Server for reiniciada, todas as políticas aplicadas no domínio gerenciado AD DS do Azure serão enviadas para a VM. Agora, você também pode entrar na VM do Windows Server usando as credenciais de domínio apropriadas.

## <a name="clean-up-resources"></a>Limpar recursos

No próximo tutorial, você usará essa VM do Windows Server para instalar as ferramentas de gerenciamento que permitem administrar o domínio gerenciado AD DS do Azure. Se você não quiser continuar nesta série de tutoriais, examine as etapas de limpeza a seguir para [desabilitar o RDP](#disable-rdp) ou [excluir a VM](#delete-the-vm). Caso contrário, [prossiga para o próximo tutorial](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Cancelar a junção da VM do Azure AD DS domínio gerenciado

Para remover a VM do domínio gerenciado AD DS do Azure, siga as etapas novamente para [ingressar a VM em um domínio](#join-the-vm-to-the-azure-ad-ds-managed-domain). Em vez de ingressar no domínio gerenciado AD DS do Azure, escolha ingressar em um grupo de trabalho, como o *grupo de trabalho*padrão. Depois que a VM for reinicializada, o objeto de computador será removido do domínio gerenciado do Azure AD DS.

Se você [excluir a VM](#delete-the-vm) sem sair do domínio, um objeto de computador órfão será deixado no Azure AD DS.

### <a name="disable-rdp"></a>Desabilitar RDP

Se você continuar a usar a VM do Windows Server criada neste tutorial para executar seus próprios aplicativos ou cargas de trabalho, lembre-se de que o RDP foi aberto pela Internet. Para melhorar a segurança e reduzir o risco de ataque, o RDP deve ser desabilitado pela Internet. Para desabilitar o RDP para a VM do Windows Server pela Internet, conclua as seguintes etapas:

1. No menu à esquerda, selecione grupos de **recursos**
1. Escolha seu grupo de recursos, como *MyResource*Group.
1. Escolha sua VM, como *myVM*, e, em seguida, selecione *rede*.
1. Em **regras de segurança de rede de entrada** para o grupo de segurança de rede, selecione a regra que permite o RDP e escolha **excluir**. Leva alguns segundos para remover a regra de segurança de entrada.

### <a name="delete-the-vm"></a>Elimine a VM

Se você não estiver usando essa VM do Windows Server, exclua a VM usando as seguintes etapas:

1. No menu à esquerda, selecione grupos de **recursos**
1. Escolha seu grupo de recursos, como *MyResource*Group.
1. Escolha sua VM, como *myVM*, e selecione **excluir**. Selecione **Sim** para confirmar a exclusão do recurso. Leva alguns minutos para excluir a VM.
1. Quando a VM for excluída, selecione o disco do sistema operacional, placa de interface de rede e quaisquer outros recursos com o prefixo *myVM* e exclua-os.

## <a name="troubleshoot-domain-join-issues"></a>Solucionar problemas de ingresso no domínio

A VM do Windows Server deve ingressar com êxito no domínio gerenciado do AD DS do Azure, da mesma forma como um computador local comum ingressaria em um domínio de Active Directory Domain Services. Se a VM do Windows Server não puder ingressar no domínio gerenciado do Azure AD DS, isso indicará que há uma conectividade ou um problema relacionado a credenciais. Examine as seções de solução de problemas a seguir para ingressar com êxito no domínio gerenciado.

### <a name="connectivity-issues"></a>Problemas de ligação

Se você não receber uma solicitação solicitando que as credenciais ingressem no domínio, haverá um problema de conectividade. A VM não pode acessar o domínio gerenciado AD DS do Azure na rede virtual.

Depois de tentar cada uma dessas etapas de solução de problemas, tente unir a VM do Windows Server ao domínio gerenciado novamente.

* Verifique se a VM está conectada à mesma rede virtual em que o AD DS do Azure está habilitado ou tem uma conexão de rede emparelhada.
* Tente executar o ping no nome de domínio DNS do domínio gerenciado, como `ping contoso.com`.
    * Se a solicitação de ping falhar, tente executar ping nos endereços IP para o domínio gerenciado, como `ping 10.0.0.4`. O endereço IP do seu ambiente é exibido na página *Propriedades* quando você seleciona o domínio gerenciado AD DS do Azure na lista de recursos do Azure.
    * Se você puder executar o ping no endereço IP, mas não no domínio, o DNS poderá estar configurado incorretamente. Confirme se os endereços IP do domínio gerenciado estão configurados como servidores DNS para a rede virtual.
* Tente liberar o cache do resolvedor de DNS na máquina virtual usando o comando `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Problemas relacionados a credenciais

Se você receber um prompt solicitando credenciais para ingressar no domínio, mas depois um erro depois de inserir essas credenciais, a VM será capaz de se conectar ao domínio gerenciado do Azure AD DS. As credenciais fornecidas não permitem que a VM ingresse no domínio gerenciado do Azure AD DS.

Depois de tentar cada uma dessas etapas de solução de problemas, tente unir a VM do Windows Server ao domínio gerenciado novamente.

* Certifique-se de que a conta de usuário especificada pertence ao grupo de *Administradores de DC do AAD* .
* Confirme se a conta faz parte do domínio gerenciado do Azure AD DS ou do locatário do Azure AD. Contas de diretórios externos associados ao seu locatário do Azure AD não podem ser autenticadas corretamente durante o processo de ingresso no domínio.
* Tente usar o formato UPN para especificar as credenciais, como `contosoadmin@contoso.onmicrosoft.com`. Se houver muitos usuários com o mesmo prefixo UPN em seu locatário ou se o prefixo UPN for excessivamente longo, o *sAMAccountName* para sua conta poderá ser gerado automaticamente. Nesses casos, o formato *sAMAccountName* para sua conta pode ser diferente do que você espera ou usa em seu domínio local.
* Verifique se você [habilitou a sincronização de senha][password-sync] para seu domínio gerenciado. Sem essa etapa de configuração, os hashes de senha necessários não estarão presentes no domínio gerenciado AD DS do Azure para autenticar corretamente sua tentativa de entrada.
* Aguarde a conclusão da sincronização de senha. Quando a senha de uma conta de usuário é alterada, uma sincronização automática em segundo plano do Azure AD atualiza a senha no Azure AD DS. Leva algum tempo para que a senha fique disponível para uso de ingresso no domínio.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma VM do Windows Server
> * Conectar-se à VM do Windows Server a uma rede virtual do Azure
> * Ingresse a VM no domínio gerenciado AD DS do Azure

Para administrar seu domínio gerenciado do Azure AD DS, configure uma VM de gerenciamento usando o Centro Administrativo do Active Directory (ADAC).

> [!div class="nextstepaction"]
> [Instalar ferramentas de administração em uma VM de gerenciamento](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[jit-access]: ../security-center/security-center-just-in-time.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
