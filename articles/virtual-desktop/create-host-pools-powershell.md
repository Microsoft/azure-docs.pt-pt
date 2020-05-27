---
title: Criar piscina de anfitriões do Windows Virtual Desktop PowerShell - Azure
description: Como criar uma piscina de anfitriões no Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0a4d0c22318399370b9ec11046c33a4eb5460eb3
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860125"
---
# <a name="create-a-host-pool-with-powershell"></a>Criar um conjunto de anfitriões com o PowerShell

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Cada piscina de anfitriões pode ser associada a vários grupos RemoteApp, um grupo de aplicações para desktop e vários anfitriões de sessão.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já seguiu as instruções no [módulo PowerShell](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Use o seu cliente PowerShell para criar uma piscina de anfitriões

Execute o seguinte cmdlet para iniciar sessão no ambiente de ambiente de trabalho virtual do Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname> 
```

Este cmdlet criará o grupo de aplicativos host pool, workspace e desktop. Além disso, irá registar o grupo de aplicações de desktop para o espaço de trabalho. Pode criar um espaço de trabalho com este cmdlet ou utilizar um espaço de trabalho existente. 

Execute o próximo cmdlet para criar um sinal de inscrição para autorizar um anfitrião da sessão a juntar-se à piscina anfitriã e guardá-lo para um novo ficheiro no seu computador local. Pode especificar quanto tempo o token de registo é válido utilizando o parâmetro -ExpirationHours.

>[!NOTE]
>A data de validade do token pode ser não inferior a uma hora e não mais de um mês. Se definir *-Expiração Tempo* fora desse limite, o cmdlet não criará o símbolo.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Por exemplo, se quiser criar um símbolo que expire em duas horas, faça este cmdlet: 

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ')) 
```

Depois disso, execute este cmdlet para adicionar utilizadores do Azure Ative Directory ao grupo de aplicações de ambiente de trabalho padrão para o pool anfitrião.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

Execute este próximo cmdlet para adicionar grupos de utilizadores do Azure Ative Directory ao grupo de aplicações de ambiente de trabalho padrão para o pool anfitrião:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Execute o seguinte cmdlet para exportar o símbolo de registo para uma variável, que utilizará mais tarde no [Registo das máquinas virtuais para o pool de anfitriões do Windows Virtual Desktop](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> 
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Crie máquinas virtuais para a piscina anfitriã

Agora pode criar uma máquina virtual Azure que pode ser unida ao seu pool de anfitriões do Windows Virtual Desktop.

Pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual a partir de uma imagem da Galeria Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual a partir de uma imagem gerida](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Criar uma máquina virtual a partir de uma imagem não gerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Se estiver a implementar uma máquina virtual utilizando o Windows 7 como o sistema operativo anfitrião, o processo de criação e implementação será um pouco diferente. Para mais detalhes, consulte [A implementação de uma máquina virtual do Windows 7 no Windows Virtual Desktop](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

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
   - Execute o instalador. Quando o instalador lhe pedir o sinal de registo, insira o valor que obteve do **cmdlet Export-AzWVDRegistrationInfo.**
3. Descarregue e instale o Bootloader do Agente de Ambiente de Trabalho Virtual do Windows.
   - Descarregue o [Bootloader](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)do Agente de Ambiente de Trabalho Virtual do Windows .
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nos seus VMs. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem aos VMs do grupo anfitrião. Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize [o acesso VM just-in-time](../security-center/security-center-just-in-time.md). Também recomendamos que não atribua os seus VMs a um IP público.

## <a name="next-steps"></a>Passos seguintes

Agora que fez uma piscina de anfitriões, pode povoá-la com Aplicações Remotas. Para saber mais sobre como gerir aplicações no Windows Virtual Desktop, consulte o tutorial de grupos de aplicações Manage.

> [!div class="nextstepaction"]
> [Gerir o tutorial de grupos de aplicações](./manage-app-groups.md)
