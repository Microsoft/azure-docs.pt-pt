---
title: Criar pool de hosts da área de trabalho virtual do Windows PowerShell – Azure
description: Como criar um pool de hosts na área de trabalho virtual do Windows com cmdlets do PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: f510879e7df967944f5e7a3deac308a430d53d0c
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771313"
---
# <a name="create-a-host-pool-with-powershell"></a>Criar um conjunto de anfitriões com o PowerShell

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas em ambientes de locatário da área de trabalho virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativos com os quais os usuários podem interagir como fariam em uma área de trabalho física.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Usar o cliente do PowerShell para criar um pool de hosts

Primeiro, [Baixe e importe o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso.

Execute o seguinte cmdlet para entrar no ambiente de área de trabalho virtual do Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Em seguida, execute este cmdlet para criar um novo pool de hosts em seu locatário de área de trabalho virtual do Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Execute o próximo cmdlet para criar um token de registro para autorizar um host de sessão a ingressar no pool de hosts e salvá-lo em um novo arquivo no computador local. Você pode especificar por quanto tempo o token de registro é válido usando o parâmetro-ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Depois disso, execute este cmdlet para adicionar Azure Active Directory usuários ao grupo de aplicativos de área de trabalho padrão para o pool de hosts.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

O cmdlet **Add-RdsAppGroupUser** não dá suporte à adição de grupos de segurança e adiciona apenas um usuário por vez ao grupo de aplicativos. Se você quiser adicionar vários usuários ao grupo de aplicativos, execute novamente o cmdlet com os nomes principais de usuário apropriados.

Execute o cmdlet a seguir para exportar o token de registro para uma variável, que será usada posteriormente no [registro das máquinas virtuais no pool de hosts da área de trabalho virtual do Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Criar máquinas virtuais para o pool de hosts

Agora você pode criar uma máquina virtual do Azure que pode ser unida ao seu pool de hosts de área de trabalho virtual do Windows.

Você pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual por meio de uma imagem da galeria do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Criar uma máquina virtual com base em uma imagem gerenciada](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Criar uma máquina virtual com base em uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Se você estiver implantando uma máquina virtual usando o Windows 7 como o sistema operacional do host, o processo de criação e implantação será um pouco diferente. Para obter mais detalhes, consulte [implantar uma máquina virtual do Windows 7 na área de trabalho virtual do Windows](deploy-windows-7-virtual-machine.md).

Depois de criar as máquinas virtuais do host de sessão, [aplique uma licença do Windows a uma VM host de sessão](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para executar suas máquinas virtuais Windows ou Windows Server sem pagar por outra licença. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Preparar as máquinas virtuais para instalações do agente de área de trabalho virtual do Windows

Você precisa fazer o seguinte para preparar suas máquinas virtuais antes de instalar os agentes de área de trabalho virtual do Windows e registrar as máquinas virtuais em seu pool de hosts de área de trabalho virtual do Windows:

- Você deve ingressar no domínio no computador. Isso permite que os usuários de área de trabalho virtual do Windows sejam mapeados de sua conta de Azure Active Directory para sua conta do Active Directory e tenham acesso permitido à máquina virtual com êxito.
- Você deve instalar a função de Host da Sessão da Área de Trabalho Remota (RDSH) se a máquina virtual estiver executando um sistema operacional Windows Server. A função de RDSH permite que os agentes de área de trabalho virtual do Windows sejam instalados corretamente.

Para ingressar no domínio com êxito, execute as ações a seguir em cada máquina virtual:

1. [Conecte-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, inicie o **painel de controle** e selecione **sistema**.
3. Selecione **nome do computador**, selecione **alterar configurações**e, em seguida, selecione **alterar...**
4. Selecione **domínio** e, em seguida, insira o domínio Active Directory na rede virtual.
5. Autentique com uma conta de domínio que tenha privilégios para computadores de ingresso no domínio.

    >[!NOTE]
    > Se você estiver ingressando suas VMs em um ambiente Azure Active Directory Domain Services (AD DS do Azure), verifique se o usuário ingressar no domínio também é membro do [grupo de administradores de DC do AAD](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrar as máquinas virtuais no pool de hosts da área de trabalho virtual do Windows

Registrar as máquinas virtuais em um pool de hosts da área de trabalho virtual do Windows é tão simples quanto instalar os agentes de área de trabalho virtual do Windows.

Para registrar os agentes de área de trabalho virtual do Windows, faça o seguinte em cada máquina virtual:

1. [Conecte-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Baixe e instale o agente de área de trabalho virtual do Windows.
   - Baixe o [agente de área de trabalho virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Clique com o botão direito do mouse no instalador baixado, selecione **Propriedades**, selecione **desbloquear**e, em seguida, selecione **OK**. Isso permitirá que o sistema confie no instalador.
   - Execute o instalador. Quando o instalador solicitar o token de registro, insira o valor obtido do cmdlet **Export-RdsRegistrationInfo** .
3. Baixe e instale o carregador de janelas do agente de área de trabalho virtual do Windows.
   - Baixe o [carregador de janelas do agente de área de trabalho virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Clique com o botão direito do mouse no instalador baixado, selecione **Propriedades**, selecione **desbloquear**e, em seguida, selecione **OK**. Isso permitirá que o sistema confie no instalador.
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente de área de trabalho virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 em suas VMs. A área de trabalho virtual do Windows não requer uma porta de entrada aberta 3389 para que os usuários acessem as VMs do pool de hosts. Se você precisar abrir a porta 3389 para fins de solução de problemas, recomendamos o uso [do acesso à VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Passos seguintes

Agora que você já fez um pool de hosts, você pode preenchê-lo com RemoteApps. Para saber mais sobre como gerenciar aplicativos na área de trabalho virtual do Windows, consulte o tutorial gerenciar grupos de aplicativos.

> [!div class="nextstepaction"]
> [Tutorial para gerenciar grupos de aplicativos](./manage-app-groups.md)
