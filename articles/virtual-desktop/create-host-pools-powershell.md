---
title: Criar piscina de anfitriões do Windows Virtual Desktop PowerShell - Azure
description: Como criar uma piscina de anfitriões no Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246964"
---
# <a name="create-a-host-pool-with-powershell"></a>Criar um conjunto de anfitriões com o PowerShell

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Cada piscina de anfitriões pode conter um grupo de aplicações com o quais os utilizadores podem interagir como fariam num ambiente de trabalho físico.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Use o seu cliente PowerShell para criar uma piscina de anfitriões

Primeiro, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez.

Executar o seguinte cmdlet para iniciar sessão no ambiente de ambiente de trabalho virtual do Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Em seguida, execute este cmdlet para criar uma nova piscina de anfitriões no seu inquilino windows Virtual Desktop:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Execute o próximo cmdlet para criar um sinal de inscrição para autorizar um anfitrião da sessão a juntar-se à piscina anfitriã e guardá-lo para um novo ficheiro no seu computador local. Pode especificar quanto tempo o token de registo é válido utilizando o parâmetro -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Depois disso, execute este cmdlet para adicionar utilizadores do Azure Ative Directory ao grupo de aplicações de ambiente de trabalho padrão para o pool anfitrião.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

O **Add-RdsAppGroupUser** cmdlet não suporta adicionar grupos de segurança e apenas adiciona um utilizador de cada vez ao grupo de aplicações. Se pretender adicionar vários utilizadores ao grupo de aplicações, refaça o cmdlet com os principais nomes do utilizador apropriados.

Execute o seguinte cmdlet para exportar o símbolo de registo para uma variável, que utilizará mais tarde no [Registo das máquinas virtuais para o pool de anfitriões do Windows Virtual Desktop](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Crie máquinas virtuais para a piscina anfitriã

Agora pode criar uma máquina virtual Azure que pode ser unida ao seu pool de anfitriões do Windows Virtual Desktop.

Pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual a partir de uma imagem da Galeria Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual a partir de uma imagem gerida](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Criar uma máquina virtual a partir de uma imagem não gerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Se estiver a implementar uma máquina virtual utilizando o Windows 7 como o sistema operativo anfitrião, o processo de criação e implementação será um pouco diferente. Para mais detalhes, consulte [A implementação de uma máquina virtual do Windows 7 no Windows Virtual Desktop](deploy-windows-7-virtual-machine.md).

Depois de ter criado as suas máquinas virtuais de anfitrião da sessão, [aplique uma licença do Windows num VM de sessão](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para executar as suas máquinas virtuais Windows ou Windows Server sem pagar outra licença. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Prepare as máquinas virtuais para instalações de agente de desktop virtual do Windows

Tem de fazer as seguintes coisas para preparar as suas máquinas virtuais antes de poder instalar os agentes de ambiente de trabalho virtual do Windows e registar as máquinas virtuais no seu pool de anfitriões do Windows Virtual Desktop:

- Tens de juntar o domínio à máquina. Isto permite que os utilizadores do Windows Virtual Desktop sejam mapeados da sua conta De Diretório Ativo Azure para a sua conta De Diretório Ativo e tenham acesso com sucesso à máquina virtual.
- Tem de instalar a função Remote Desktop Session Host (RDSH) se a máquina virtual estiver a executar um Sistema operativo windows. A função RDSH permite que os agentes de ambiente de trabalho virtual do Windows se instalem corretamente.

Para se juntar com sucesso ao domínio, faça as seguintes coisas em cada máquina virtual:

1. [Ligue-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais que forneceu ao criar a máquina virtual.
2. Na máquina virtual, lance **o Painel de Controlo** e selecione **System**.
3. Selecione **o nome do computador,** selecione **definições de alteração**e, em seguida, selecione **Mudar...**
4. Selecione **Domínio** e, em seguida, introduza o domínio de Diretório Ativo na rede virtual.
5. Autenticar com uma conta de domínio que tenha privilégios de unir máquinas de união de domínios.

    >[!NOTE]
    > Se estiver a juntar os seus VMs a um ambiente azure Ative Directory Domain Services (Azure AD DS), certifique-se de que o utilizador de adesão ao seu domínio é também membro do grupo de administradores da [AAD DC.](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registe as máquinas virtuais na piscina de anfitriões do Windows Virtual Desktop

Registar as máquinas virtuais num conjunto de anfitriões do Windows Virtual Desktop é tão simples como instalar os agentes de ambiente de trabalho virtual do Windows.

Para registar os agentes do Windows Virtual Desktop, faça o seguinte em cada máquina virtual:

1. [Ligue-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais que forneceu ao criar a máquina virtual.
2. Descarregue e instale o Windows Virtual Desktop Agent.
   - Descarregue o [Windows Virtual Desktop Agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Clique no instalador descarregado, selecione **Propriedades,** selecione **Desbloquear**e, em seguida, selecione **OK**. Isto permitirá ao seu sistema confiar no instalador.
   - Execute o instalador. Quando o instalador lhe pedir o sinal de registo, insira o valor que obteve do **cmdlet Export-RdsRegistrationInfo.**
3. Descarregue e instale o Bootloader do Agente de Ambiente de Trabalho Virtual do Windows.
   - Descarregue o [Bootloader](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)do Agente de Ambiente de Trabalho Virtual do Windows .
   - Clique no instalador descarregado, selecione **Propriedades,** selecione **Desbloquear**e, em seguida, selecione **OK**. Isto permitirá ao seu sistema confiar no instalador.
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nos seus VMs. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem aos VMs do grupo anfitrião. Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize [o acesso VM just-in-time](../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Passos seguintes

Agora que fez uma piscina de anfitriões, pode povoá-la com Aplicações Remotas. Para saber mais sobre como gerir aplicações no Windows Virtual Desktop, consulte o tutorial de grupos de aplicações Manage.

> [!div class="nextstepaction"]
> [Gerir o tutorial de grupos de aplicações](./manage-app-groups.md)
