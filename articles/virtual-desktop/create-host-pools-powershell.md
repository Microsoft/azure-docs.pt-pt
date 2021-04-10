---
title: Criar o Windows Virtual Desktop host pool PowerShell - Azure
description: Como criar uma piscina de anfitriões no Windows Virtual Desktop com cmdlets PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 10/02/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2c1ce95f9eba8c31b20d8e992fa1880a5d33da8d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447860"
---
# <a name="create-a-windows-virtual-desktop-host-pool-with-powershell"></a>Crie uma piscina de anfitriões virtual do Windows desktop com PowerShell

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Cada piscina de anfitriões pode ser associada com vários grupos RemoteApp, um grupo de aplicações de desktop e vários anfitriões de sessão.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já seguiu as instruções na [Configuração do módulo PowerShell](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Use o seu cliente PowerShell para criar uma piscina de anfitriões

Executar o seguinte cmdlet para iniciar serção no ambiente de ambiente de trabalho virtual do Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

Este cmdlet criará o grupo de anfitriões, espaço de trabalho e aplicativos de desktop. Além disso, irá registar o grupo de aplicações de desktop para o espaço de trabalho. Pode criar um espaço de trabalho com este cmdlet ou utilizar um espaço de trabalho existente.

Execute o próximo cmdlet para criar um token de inscrição para autorizar um anfitrião de sessão a juntar-se à piscina anfitriã e guardá-lo para um novo ficheiro no seu computador local. Pode especificar quanto tempo o token de registo é válido utilizando o parâmetro -ExpirationHours.

>[!NOTE]
>A data de validade do token pode ser não inferior a uma hora e não mais de um mês. Se definir *-ExpirationTime* fora desse limite, o cmdlet não criará o token.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Por exemplo, se quiser criar um símbolo que expire em duas horas, execute este cmdlet:

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Depois disso, execute este cmdlet para adicionar os utilizadores do Azure Ative Directory ao grupo de aplicações de ambiente de trabalho predefinido para o pool anfitrião.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Execute este próximo cmdlet para adicionar grupos de utilizadores do Azure Ative Directory ao grupo de aplicações de ambiente de trabalho predefinido para o pool anfitrião:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Execute o seguinte cmdlet para exportar o token de registo para uma variável, que utilizará mais tarde no [Registo das máquinas virtuais para o conjunto de anfitriões virtual do Windows Desktop](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Criar máquinas virtuais para a piscina anfitriã

Agora pode criar uma máquina virtual Azure que pode ser juntada à sua piscina de anfitriões virtual do Windows Desktop.

Pode criar uma máquina virtual de várias formas:

- [Crie uma máquina virtual a partir de uma imagem da Galeria Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual a partir de uma imagem gerida](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Criar uma máquina virtual a partir de uma imagem nãogerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Se estiver a implementar uma máquina virtual utilizando o Windows 7 como o SISTEMA anfitrião, o processo de criação e implementação será um pouco diferente. Para obter mais detalhes, consulte [implementar uma máquina virtual do Windows 7 no Windows Virtual Desktop](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Depois de ter criado as máquinas virtuais do anfitrião da sessão, [aplique uma licença Windows a um VM do anfitrião de sessão](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para executar as suas máquinas virtuais Windows ou Windows Server sem pagar outra licença.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Prepare as máquinas virtuais para instalações de agente virtual do Windows Desktop

Tem de fazer as seguintes coisas para preparar as suas máquinas virtuais antes de poder instalar os agentes virtuais do Windows desktop e registar as máquinas virtuais no seu pool de anfitriões virtual do Windows Desktop:

- Tem de se juntar à máquina. Isto permite que os utilizadores do Windows Virtual Desktop sejam mapeados a partir da sua conta Azure Ative Directory para a sua conta ative Directory e tenham acesso com sucesso à máquina virtual.
- Tem de instalar a função "Host Host" (RDSH) se a máquina virtual estiver a executar um SISTEMA do Servidor do Windows. A função RDSH permite que os agentes de ambiente de trabalho virtual do Windows sejam instalados corretamente.

Para conseguir juntar o domínio, faça as seguintes coisas em cada máquina virtual:

1. [Ligue-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, lance o **Painel de Controlo** e selecione **Sistema.**
3. Selecione **nome de computador**, selecione Alterar as **definições** e, em seguida, selecione **Alterar...**
4. Selecione **Domínio** e, em seguida, introduza o domínio Ative Directory na rede virtual.
5. Autenticar com uma conta de domínio que tem privilégios para máquinas de união de domínios.

    >[!NOTE]
    > Se estiver a juntar os seus VMs a um ambiente Azure Ative Directory Domain Services (Azure AD DS), certifique-se de que o seu domínio se junta ao utilizador também é membro do grupo de [Administradores AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

>[!IMPORTANT]
>Recomendamos que não ative quaisquer políticas ou configurações que desativem o Instalador do Windows. Se desativar o Instalador do Windows, o serviço não poderá instalar atualizações de agentes nos anfitriões da sessão e os anfitriões da sessão não funcionarão corretamente.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registar as máquinas virtuais para a piscina de anfitriões virtual do Windows Desktop

Registar as máquinas virtuais num conjunto de anfitriões virtual do Windows é tão simples como instalar os agentes virtuais do Windows Desktop.

Para registar os agentes do Windows Virtual Desktop, faça o seguinte em cada máquina virtual:

1. [Ligue-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Faça o download e instale o Windows Virtual Desktop Agent.
   - Descarregue o [Windows Virtual Desktop Agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Execute o instalador. Quando o instalador lhe pedir o token de registo, insira o valor que obteve do cmdlet **Get-AzWvdRegistrationInfo.**
3. Descarregue e instale o Bootloader do Agente virtual de ambiente de trabalho do Windows.
   - Descarregue o [Bootloader do Agente virtual de Ambiente de Trabalho](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)do Windows .
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nos seus VMs. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem aos VMs da piscina anfitriã. Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize [acesso vm just-in-time](../security-center/security-center-just-in-time.md). Recomendamos também que não atribua os seus VMs a um IP público.

## <a name="update-the-agent"></a>Atualizar o agente

Terá de atualizar o agente se estiver numa das seguintes situações:

- Você quer migrar um anfitrião de sessão previamente registrado para uma nova piscina anfitriã
- O anfitrião da sessão não aparece na piscina do anfitrião após uma atualização

Para atualizar o agente:

1. Inscreva-se no VM como administrador.
2. Vá a **Serviços,** em seguida, pare os processos de Carregador de Agente de Ambiente de Trabalho **Rdagent** e **Remote.**
3. Em seguida, encontre o agente e a esclerose múltipla. Ou estão localizados na pasta **C:\DeployAgent** ou em qualquer local para onde a guardou quando a instalou.
4. Encontre os seguintes ficheiros e desinstale-os:
     
     - Microsoft.RDInfra.RDAgent.Installer-x64-verx.x.x
     - Microsoft.RDInfra.RDAgentBootLoader.Installer-x64

   Para desinstalar estes ficheiros, clique com o botão direito em cada nome de ficheiro e, em seguida, selecione **Desinstalar**.
5. Opcionalmente, também pode remover as seguintes definições de registo:
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. Uma vez desinstalados estes itens, isto deve remover todas as associações com a antiga piscina de anfitriões. Se pretender voltar a inscrever este anfitrião no serviço, siga as instruções no [Registo das máquinas virtuais para a piscina de anfitriões virtual do Windows Desktop](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).


## <a name="next-steps"></a>Passos seguintes

Agora que fez uma piscina de anfitriões, pode povoá-la com RemoteApps. Para saber mais sobre como gerir aplicações no Windows Virtual Desktop, consulte o tutorial de grupos de aplicações Manage.

> [!div class="nextstepaction"]
> [Gerir grupos de aplicações tutorial](./manage-app-groups.md)
