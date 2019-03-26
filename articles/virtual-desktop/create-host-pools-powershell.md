---
title: Criar um conjunto de anfitrião de pré-visualização de ambiente de trabalho virtuais Windows com o PowerShell - Azure
description: Como criar um conjunto de anfitrião na pré-visualização de ambiente de trabalho virtuais Windows com cmdlets do PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: bd46e5f7428bab58508521b2c7d4d7cca25d689b
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439078"
---
# <a name="create-a-host-pool-with-powershell"></a>Criar um conjunto de anfitriões com o PowerShell

Conjuntos de anfitrião são uma coleção de um ou mais máquinas virtuais idênticas em ambientes de inquilino do Windows Virtual Desktop Preview. Cada conjunto de anfitrião pode conter um grupo de aplicações que os usuários podem interagir com como numa área de trabalho física.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Utilizar o cliente do PowerShell para criar um conjunto de anfitrião

Primeiro, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

Execute o seguinte cmdlet para iniciar sessão no ambiente de área de Trabalho Virtual do Windows

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Depois disso, execute o seguinte cmdlet para definir o contexto ao seu grupo de inquilino. Se não tiver o nome do grupo de inquilino, o inquilino é mais provável no "Predefinido inquilino grupo," para que possa avançar este cmdlet.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Em seguida, execute este cmdlet para criar um novo conjunto de anfitriões no seu inquilino de área de Trabalho Virtual do Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Execute o cmdlet seguinte para criar um token de registo para autorizar um host de sessão se associam ao conjunto de anfitrião e guardá-lo para um novo ficheiro no seu computador local. Pode especificar o tempo que o token de registo é válido ao utilizar o parâmetro - ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Depois disso, execute este cmdlet para adicionar utilizadores do Active Directory do Azure para o grupo de aplicativo de desktop padrão para o conjunto de anfitrião.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

O **Add-RdsAppGroupUser** cmdlet não suporta a adição de grupos de segurança e só adiciona um utilizador ao mesmo tempo para o grupo de aplicações. Se desejar adicionar vários usuários ao grupo de aplicações, execute novamente o cmdlet com os nomes de principal de utilizador adequada.

Execute o cmdlet seguinte para exportar o token de registo para uma variável, o que irá utilizar posteriormente no [registe as máquinas virtuais ao agrupamento de anfitrião de área de Trabalho Virtual do Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Criar máquinas virtuais para o conjunto de anfitrião

Agora pode criar uma máquina virtual do Azure que pode ser associada ao seu conjunto de anfitrião de área de Trabalho Virtual do Windows.

Pode criar uma máquina virtual de várias formas:

- [Criar uma máquina virtual a partir de uma imagem da galeria do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Criar uma máquina virtual a partir de uma imagem gerida](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Criar uma máquina virtual a partir de uma imagem não gerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Preparar as máquinas virtuais para instalações de agentes do Windows Virtual Desktop pré-visualização

Precisa efetue os seguintes procedimentos para preparar as máquinas virtuais antes de poder instalar os agentes da área de Trabalho Virtual do Windows e registe as máquinas virtuais ao agrupamento de anfitrião de área de Trabalho Virtual do Windows:

- Tem a máquina de associação de domínio. Isso permite que os usuários de área de Trabalho Virtual do Windows recebidos ser mapeado a partir da respetiva conta do Azure Active Directory para a conta do Active Directory e com êxito permissão de acesso à máquina virtual.
- Tem de instalar a função de anfitrião de sessões de ambiente de trabalho remoto (RDSH) se a máquina virtual estiver a executar o SO do Windows Server. A função RDSH permite que os agentes da área de Trabalho Virtual do Windows instalar corretamente.

Para com êxito-associação a um domínio, efetue os seguintes procedimentos em cada máquina virtual:

1. [Ligar à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que forneceu ao criar a máquina virtual.
2. Na máquina virtual, inicie **painel de controlo** e selecione **sistema**.
3. Selecione **nome do computador**, selecione **alterar as definições**e, em seguida, selecione **alteração...**
4. Selecione **domínio** e, em seguida, introduza o domínio do Active Directory na rede virtual.
5. Autenticar com uma conta de domínio que tenha privilégios para máquinas de associação a um domínio.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Registe as máquinas virtuais para o conjunto de anfitrião do Windows Virtual Desktop pré-visualização

Registar as máquinas virtuais a um conjunto de anfitrião de área de Trabalho Virtual do Windows é tão simples quanto instalar os agentes da área de Trabalho Virtual do Windows.

Para registar os agentes da área de Trabalho Virtual do Windows, faça o seguinte em cada máquina virtual:

1. [Ligar à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que forneceu ao criar a máquina virtual.
2. Baixe e instale o agente de ambiente de Trabalho Virtual do Windows.
   - Transfira o [agente do Windows Virtual Desktop](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Com o botão direito no instalador que transferiu, selecione **propriedades**, selecione **desbloqueio**, em seguida, selecione **OK**. Isso permitirá que o sistema para o instalador de confiança.
   - Execute o instalador. Quando o instalador lhe pedir para o token de registo, introduza o valor tem do **Export-RdsRegistrationInfo** cmdlet.
3. Baixe e instale o carregador de inicialização para Windows um Virtual agentes de Desktop.
   - Transfira o [carregador de inicialização do Windows agente de ambiente de Trabalho Virtual](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Com o botão direito no instalador que transferiu, selecione **propriedades**, selecione **desbloqueio**, em seguida, selecione **OK**. Isso permitirá que o sistema para o instalador de confiança.
   - Execute o instalador.
4. Instalar ou ativar a pilha de lado a lado de área de Trabalho Virtual do Windows. Os passos serão diferentes consoante a versão SO da máquina virtual utiliza.
   - Se o SO da sua máquina virtual é o Windows Server 2016:
     - Transfira o [pilha de lado a lado de área de Trabalho Virtual do Windows](https://go.microsoft.com/fwlink/?linkid=2084270).
     - Com o botão direito no instalador que transferiu, selecione **propriedades**, selecione **desbloqueio**, em seguida, selecione **OK**. Isso permitirá que o sistema para o instalador de confiança.
     - Execute o instalador.
   - Se o SO da sua máquina virtual é o Windows 10 1809 ou posterior ou Windows Server 2019 ou posterior:
     - Transfira o [script](https://go.microsoft.com/fwlink/?linkid=2084268) para ativar a pilha de lado a lado.
     - O script transferido com o botão direito, selecione **propriedades**, selecione **desbloqueio**, em seguida, selecione **OK**. Isso permitirá que o sistema para o script de confiança.
     - Partir do **começar** menu, procure o ISE do Windows PowerShell, faça duplo clique nele, em seguida, selecione **executar como administrador**.
     - Selecione **arquivo**, em seguida, **abra...** e, em seguida, localize o script do PowerShell dos ficheiros transferidos e abri-lo.
     - Selecione o botão verde play para executar o script.

## <a name="next-steps"></a>Passos Seguintes

Agora que criou um conjunto de host, pode preenchê-lo com aplicativos remotos. Para saber mais sobre como gerir aplicações na área de Trabalho Virtual do Windows, veja o tutorial de grupos de aplicações de gerir.

> [!div class="nextstepaction"]
> [Gerir o tutorial de grupos de aplicações](./manage-app-groups.md)
