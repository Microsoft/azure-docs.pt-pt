---
title: Criar o Windows Virtual Desktop (clássico) anfitrião pool PowerShell - Azure
description: Como criar uma piscina de anfitriões no Windows Virtual Desktop (clássico) com cmdlets PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c035a7fbafe9b3a42fbd16e3f8377014010ddd49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88003553"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-powershell"></a>Crie uma piscina de anfitriões no Windows Virtual Desktop (clássico) com PowerShell

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../create-host-pools-powershell.md).

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Cada piscina de anfitriões pode conter um grupo de aplicações com o quais os utilizadores podem interagir como fariam num ambiente de trabalho físico.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Use o seu cliente PowerShell para criar uma piscina de anfitriões

Em primeiro lugar, [descarregue e importe o módulo PowerShell virtual do Windows Desktop](/powershell/windows-virtual-desktop/overview/) para utilizar na sessão PowerShell se ainda não o fez.

Executar o cmdlet seguinte para iniciar serção no ambiente de ambiente de trabalho virtual do Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Em seguida, executar este cmdlet para criar uma nova piscina de anfitriões no seu inquilino virtual Windows Desktop:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Execute o próximo cmdlet para criar um token de inscrição para autorizar um anfitrião de sessão a juntar-se à piscina anfitriã e guardá-lo para um novo ficheiro no seu computador local. Pode especificar quanto tempo o token de registo é válido utilizando o parâmetro -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Depois disso, execute este cmdlet para adicionar os utilizadores do Azure Ative Directory ao grupo de aplicações de ambiente de trabalho predefinido para o pool anfitrião.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

O **Cmdlet Add-RdsAppGroupUser** não suporta a adição de grupos de segurança e apenas adiciona um utilizador de cada vez ao grupo de aplicações. Se pretender adicionar vários utilizadores ao grupo de aplicações, reexecute o cmdlet com os nomes principais do utilizador apropriados.

Execute o seguinte cmdlet para exportar o token de registo para uma variável, que utilizará mais tarde no [Registo das máquinas virtuais para o conjunto de anfitriões virtual do Windows Desktop](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Criar máquinas virtuais para a piscina anfitriã

Agora pode criar uma máquina virtual Azure que pode ser juntada à sua piscina de anfitriões virtual do Windows Desktop.

Pode criar uma máquina virtual de várias formas:

- [Crie uma máquina virtual a partir de uma imagem da Galeria Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual a partir de uma imagem gerida](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [Criar uma máquina virtual a partir de uma imagem nãogerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Se estiver a implementar uma máquina virtual utilizando o Windows 7 como o SISTEMA anfitrião, o processo de criação e implementação será um pouco diferente. Para obter mais detalhes, consulte [implementar uma máquina virtual do Windows 7 no Windows Virtual Desktop](deploy-windows-7-virtual-machine.md).

Depois de ter criado as máquinas virtuais do anfitrião da sessão, [aplique uma licença Windows a um VM do anfitrião de sessão](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para executar as suas máquinas virtuais Windows ou Windows Server sem pagar outra licença.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Prepare as máquinas virtuais para instalações de agente virtual do Windows Desktop

Tem de fazer as seguintes coisas para preparar as suas máquinas virtuais antes de poder instalar os agentes virtuais do Windows desktop e registar as máquinas virtuais no seu pool de anfitriões virtual do Windows Desktop:

- Tem de se juntar à máquina. Isto permite que os utilizadores do Windows Virtual Desktop sejam mapeados a partir da sua conta Azure Ative Directory para a sua conta ative Directory e tenham acesso com sucesso à máquina virtual.
- Tem de instalar a função "Host Host" (RDSH) se a máquina virtual estiver a executar um SISTEMA do Servidor do Windows. A função RDSH permite que os agentes de ambiente de trabalho virtual do Windows sejam instalados corretamente.

Para conseguir juntar o domínio, faça as seguintes coisas em cada máquina virtual:

1. [Ligue-se à máquina virtual](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, lance o **Painel de Controlo** e selecione **Sistema.**
3. Selecione **nome de computador**, selecione Alterar as **definições** e, em seguida, selecione **Alterar...**
4. Selecione **Domínio** e, em seguida, introduza o domínio Ative Directory na rede virtual.
5. Autenticar com uma conta de domínio que tem privilégios para máquinas de união de domínios.

    >[!NOTE]
    > Se estiver a juntar os seus VMs a um ambiente Azure Ative Directory Domain Services (Azure AD DS), certifique-se de que o seu domínio se junta ao utilizador também é membro do grupo de [Administradores AAD DC](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registar as máquinas virtuais para a piscina de anfitriões virtual do Windows Desktop

Registar as máquinas virtuais num conjunto de anfitriões virtual do Windows é tão simples como instalar os agentes virtuais do Windows Desktop.

Para registar os agentes do Windows Virtual Desktop, faça o seguinte em cada máquina virtual:

1. [Ligue-se à máquina virtual](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Faça o download e instale o Windows Virtual Desktop Agent.
   - Descarregue o [Windows Virtual Desktop Agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Clique com o botão direito no instalador descarregado, selecione **Propriedades,** selecione **Desbloqueie** e, em seguida, selecione **OK**. Isto permitirá ao seu sistema confiar no instalador.
   - Execute o instalador. Quando o instalador lhe pedir o token de registo, insira o valor que obteve do cmdlet **Export-RdsRegistrationInfo.**
3. Descarregue e instale o Bootloader do Agente virtual de ambiente de trabalho do Windows.
   - Descarregue o [Bootloader do Agente virtual de Ambiente de Trabalho](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)do Windows .
   - Clique com o botão direito no instalador descarregado, selecione **Propriedades,** selecione **Desbloqueie** e, em seguida, selecione **OK**. Isto permitirá ao seu sistema confiar no instalador.
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nos seus VMs. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem aos VMs da piscina anfitriã. Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize [acesso vm just-in-time](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Passos seguintes

Agora que fez uma piscina de anfitriões, pode povoá-la com RemoteApps. Para saber mais sobre como gerir aplicações no Windows Virtual Desktop, consulte o tutorial de grupos de aplicações Manage.

> [!div class="nextstepaction"]
> [Gerir grupos de aplicações tutorial](../manage-app-groups.md)
