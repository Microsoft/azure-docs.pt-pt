---
title: Como ativar a virtualização aninhada em máquinas de virtuais do Azure | Documentos da Microsoft
description: Como ativar a virtualização aninhada em máquinas de virtuais do Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: f90ca51349eef92bd25095f5a2a10d7d181fdb2c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766534"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Como ativar a virtualização aninhada na VM do Azure

Virtualização aninhada é suportada em várias famílias de máquina virtual do Azure. Esta capacidade fornece grande flexibilidade no suporte aos cenários, como ambientes de desenvolvimento, teste, treinamento e demonstração.   

Este artigo explica ativar Hyper-V numa VM do Azure e configurar a ligação à Internet para que a máquina virtual convidada.

## <a name="create-a-nesting-capable-azure-vm"></a>Criar uma VM do Azure com capacidade de aninhamento

Crie um novo Azure VM do Windows Server 2016.  Para referência rápida, todas as máquinas de virtuais de v3 suporta virtualização aninhada. Para os tamanhos de uma lista completa de máquina virtual que aninhamento de suporte, consulte a [artigo de unidade de computação do Azure](acu.md).

Lembre-se de escolher um tamanho VM grande o suficiente para suportar as necessidades de uma máquina virtual convidada. Neste exemplo, estamos a utilizar um tamanho de D3_v3 VM do Azure. 

Pode ver a disponibilidade regional de máquinas virtuais da série Dv3 ou Ev3 [aqui](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Para obter instruções detalhadas sobre como criar uma nova máquina virtual, consulte [criar e gerir VMs do Windows com o módulo Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Ligar à sua VM do Azure

Crie uma ligação de ambiente de trabalho remoto para a máquina virtual.

1. Clique no botão **Ligar** nas propriedades da máquina virtual. É criado e transferido um ficheiro do Protocolo do Ambiente de Trabalho Remoto (ficheiro .rdp).

2. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) a partir da Mac App Store.

3. Introduza o nome de utilizador e palavra-passe que especificou ao criar a máquina virtual e, em seguida, clique em **Ok**.

4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Ativar a funcionalidade de Hyper-V na VM do Azure
Pode configurar estas definições manualmente ou fornecemos um script do PowerShell para automatizar a configuração.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opção 1: Utilizar um script do PowerShell para configurar a virtualização aninhada
Um script do PowerShell para ativar a virtualização aninhada num anfitrião Windows Server 2016 está disponível no [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). O script verifica os pré-requisitos e, em seguida, configura a virtualização aninhada na VM do Azure. Reinício da VM do Azure é necessário para concluir a configuração. Este script pode funcionar em outros ambientes, mas não é garantido. Veja a mensagem de blogue do Azure com uma demonstração em vídeo em direto em Virtualização aninhada em execução no Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opção 2: Configurar manualmente a virtualização aninhada

1. Na VM do Azure, abra o PowerShell como administrador. 

2. Ative a funcionalidade de Hyper-V e ferramentas de gestão.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Este comando reinicia a VM do Azure. Irá perder a ligação de RDP durante o processo de reinício.
    
3. Depois de reiniciar a VM do Azure, voltar a ligar à sua VM através de RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Configurar a conectividade de internet para a máquina virtual de convidado
Criar um novo adaptador de rede virtual para a máquina virtual de convidado e configurar um Gateway de NAT para ativar a conectividade à Internet.

### <a name="create-a-nat-virtual-network-switch"></a>Criar um comutador de rede virtual de NAT

1. Na VM do Azure, abra o PowerShell como administrador.
   
2. Crie um comutador interno.

    ```powershell
    New-VMSwitch -Name "InternalNATSwitch" -SwitchType Internal
    ```

3. Ver as propriedades do comutador e tenha em atenção o ifIndex para o novo adaptador.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Tome nota do "ifIndex" para o comutador virtual que acabou de criar.
    
4. Crie um endereço IP para o Gateway de NAT.
    
Para configurar o gateway, terá algumas informações sobre a sua rede:    
  * IPAddress - o IP do Gateway de NAT Especifica o endereço IPv4 ou IPv6 para utilizar como o endereço de gateway predefinido para a sub-rede de rede virtual. O formulário genérico é a.b.c.1 (por exemplo, "192.168.0.1"). Embora a posição final não precisa ser.1, ele normalmente é (com base no comprimento do prefixo). Normalmente, deve usar um espaço de endereços de rede privada RFC 1918. 
  * PrefixLength - o comprimento do prefixo de sub-rede define o tamanho da sub-rede local (máscara de sub-rede). O comprimento do prefixo de sub-rede será um número inteiro entre 0 e 32. 0 mapearão toda a internet, 32 devem permitir que um IP mapeado. Intervalo de valores comuns entre 24 e 12 consoante o número de IPs tem de ser anexados ao NAT. Um PrefixLength comum é 24 – isso é uma máscara de sub-rede de 255.255.255.0.
  * InterfaceIndex - **ifIndex** é o índice de interface do comutador virtual criado no passo anterior. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Criar a rede NAT

Para configurar o gateway, terá de fornecer informações sobre a rede e o Gateway de NAT:
  * Nome - este é o nome da rede NAT. 
  * InternalIPInterfaceAddressPrefix - o prefixo de sub-rede NAT descreve tanto o prefixo de IP do Gateway de NAT acima, bem como o comprimento do prefixo de sub-rede NAT acima. O formulário genérico será a.b.c.0/NAT comprimento do prefixo de sub-rede. 

No PowerShell, crie uma nova rede NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Criar a máquina virtual convidada

1. Abra o Gestor de Hyper-V e criar uma nova máquina virtual. Configure a máquina virtual para utilizar a nova rede interna que criou.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Instale um sistema operativo na máquina virtual convidada.
    
    >[!NOTE] 
    >
    >Terá de mídia de instalação de um sistema operativo instalar na VM. Neste caso estamos a utilizar do Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Atribuir um endereço IP para a máquina virtual convidada

Pode atribuir um endereço IP para a máquina de virtual de convidado ao manualmente a definição de um endereço IP estático na máquina de virtual de convidado ou configurar o DHCP na VM do Azure para atribuir o endereço IP dinamicamente.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opção 1: Configurar o DHCP para atribuir dinamicamente um endereço IP a máquina virtual convidada
Siga os passos abaixo para configurar o DHCP na máquina de virtual de anfitrião para a atribuição de endereço dinâmico.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Instalar o servidor DHCP na VM do Azure

1. Abra o Gestor de servidor. No Dashboard, clique em **para adicionar funções e funcionalidades**. A adicionar funções e funcionalidades assistente será exibida.
  
2. No assistente, clique em **seguinte** até a página de funções de servidor.
  
3. Clique para selecionar o **servidor DHCP** caixa de seleção, clique em **adicionar funcionalidades**e, em seguida, clique em **seguinte** até concluir o assistente.
  
4. Clique em **Instalar**.

#### <a name="configure-a-new-dhcp-scope"></a>Configurar um novo âmbito DHCP

1. Abra o Gestor de DHCP.
  
2. No painel de navegação, expanda o nome do servidor, clique com botão direito **IPv4**e clique em **novo âmbito**. O Assistente de novo âmbito é apresentada, clique em **seguinte**.
  
3. Introduza um nome e descrição para o âmbito e clique em **seguinte**.
  
4. Defina um intervalo de IP do servidor DHCP (por exemplo, 192.168.0.100 para 192.168.0.200).
  
5. Clique em **seguinte** até a página de Gateway predefinido. Introduza o endereço de IP que criou anteriormente (por exemplo, 192.168.0.1) como o Gateway predefinido, em seguida, clique em **adicionar**.
  
6. Clique em **próxima** até que o assistente ser concluído, deixando todos os valores predefinidos, clique em **concluir**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opção 2: Definir manualmente um endereço IP estático na máquina virtual convidada
Se não tiver configurado o DHCP para atribuir dinamicamente um endereço IP para a máquina virtual convidada, siga estes passos para definir um endereço IP estático.

1. Na VM do Azure, abra o PowerShell como administrador.

2. A máquina virtual convidada com o botão direito e clique em ligar.

3. Inicie sessão máquina virtual convidada.

4. Na máquina virtual convidada, abra o Centro de compartilhamento e rede.

5. Configure o adaptador de rede para um endereço dentro do intervalo da rede NAT que criou na secção anterior.

Neste exemplo irá utilizar um endereço no intervalo 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testar a conectividade na máquina virtual convidada

Na máquina virtual convidada, abra o browser e navegue para uma página da web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Configurar a conectividade de intranet para a máquina virtual de convidado

Para obter instruções sobre como ativar a conectividade transparente entre máquinas virtuais convidadas e VMs do Azure, consulte [este documento](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
