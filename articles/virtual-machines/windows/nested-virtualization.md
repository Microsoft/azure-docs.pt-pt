---
title: Como permitir a virtualização aninhada em Azure Virtual Machines
description: Como permitir a virtualização aninhada em Azure Virtual Machines
author: cynthn
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: how-to
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.openlocfilehash: 03df7db13ebd3ebec407bb046cc735c835e01068
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074244"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Como permitir a virtualização aninhada num VM Azure

A virtualização aninhada é suportada em várias famílias de máquinas virtuais Azure. Esta capacidade proporciona uma grande flexibilidade no apoio a cenários como ambientes de desenvolvimento, testes, formação e demonstração.   

Este artigo passa por permitir o Hiper-V num VM Azure e configurar a conectividade da Internet com a máquina virtual convidada.

## <a name="create-a-nesting-capable-azure-vm"></a>Criar um Azure VM capaz de nidificação

Criar um novo Windows Server 2016 Azure VM. Para obter uma lista completa dos tamanhos de máquinas virtuais que suportam a nidificação, confira o [artigo da Unidade Azure Compute](../acu.md).

Lembre-se de escolher um tamanho VM grande o suficiente para suportar as exigências de uma máquina virtual convidada. Neste exemplo, estamos a usar um D3_v3 tamanho Azure VM. 

Pode ver a disponibilidade regional de máquinas virtuais da série Dv3 ou Ev3 [aqui.](https://azure.microsoft.com/regions/services/)

>[!NOTE]
>
>Para obter instruções detalhadas sobre a criação de uma nova máquina virtual, consulte [Criar e Gerir VMs do Windows com o módulo Azure PowerShell](./tutorial-manage-vm.md)
    
## <a name="connect-to-your-azure-vm"></a>Ligue-se ao seu Azure VM

Crie uma ligação de ambiente de trabalho remoto para a máquina virtual.

1. Clique no botão **Ligar** nas propriedades da máquina virtual. É criado e transferido um ficheiro do Protocolo do Ambiente de Trabalho Remoto (ficheiro .rdp).

2. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) a partir da Mac App Store.

3. Introduza o nome de utilizador e palavra-passe que especificou ao criar a máquina virtual e, em seguida, clique em **Ok**.

4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Ativar a função Hiper-V no Azure VM
Pode configurar estas definições manualmente ou fornecemos um script PowerShell para automatizar a configuração.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opção 1: Utilize um script PowerShell para configurar a virtualização aninhada
Um script PowerShell para permitir a virtualização aninhada num anfitrião do Windows Server 2016 está disponível no [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). O script verifica os pré-requisitos e, em seguida, configura a virtualização aninhada no Azure VM. É necessário reiniciar o Azure VM para completar a configuração. Este script pode funcionar noutros ambientes, mas não está garantido. Confira a publicação do blog Azure com uma demonstração de vídeo ao vivo sobre a virtualização aninhada a decorrer no Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opção 2: Configurar a virtualização aninhada manualmente

1. No Azure VM, abra a PowerShell como administrador. 

2. Ativar a função Hyper-V e as Ferramentas de Gestão.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Este comando reinicia o Azure VM. Perderá a sua ligação RDP durante o processo de reinício.
    
3. Depois de recomeçar o Azure VM, voltar a ligar-se ao seu VM utilizando RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Configurar conectividade de internet para a máquina virtual do hóspede
Crie um novo adaptador de rede virtual para a máquina virtual do hóspede e configura um NAT Gateway para permitir a conectividade da Internet.

### <a name="create-a-nat-virtual-network-switch"></a>Criar um interruptor de rede virtual NAT

1. No Azure VM, abra a PowerShell como administrador.
   
2. Criar um interruptor interno.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Ver as propriedades do interruptor e observar o ifIndex para o novo adaptador.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Tome nota do "ifIndex" para o interruptor virtual que acabou de criar.
    
4. Crie um endereço IP para o GATEWAY NAT.
    
Para configurar o portal, precisa de algumas informações sobre a sua rede:    
  * IPAddress - O IP DO GATEWAY NAT especifica o endereço IPv4 ou IPv6 para utilizar como endereço de gateway predefinido para a sub-rede de rede virtual. O formulário genérico é a.b.c.1 (por exemplo, "192.168.0.1"). Embora a posição final não tenha de ser .1, geralmente é (com base no comprimento do prefixo). Normalmente deve usar um espaço de endereço de rede privada RFC 1918. 
  * PrefixLength - O comprimento do prefixo da sub-rede define o tamanho da sub-rede local (máscara de sub-rede). O comprimento do prefixo do sub-rede será um valor inteiro entre 0 e 32. 0 iria mapear toda a internet, 32 só permitiria um IP mapeado. Os valores comuns variam de 24 a 12 dependendo do número de IPs necessários para ser ligado ao NAT. Um prefixLength comum é 24- esta é uma máscara de sub-rede de 255.255.255.0.
  * InterfaceIndex - **seIndex** é o índice de interface do interruptor virtual criado no passo anterior. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Criar a rede NAT

Para configurar o gateway, você precisará fornecer informações sobre a rede e NAT Gateway:
  * Nome - Este é o nome da rede NAT. 
  * InternalIPInterfaceAddressPrefix - O prefixo da sub-rede NAT descreve tanto o prefixo IP do GATEWAY NAT de cima como o comprimento do prefixo do sub-rede NAT a partir de cima. O formulário genérico será a.b.c.0/NAT Subnet Prefix Length. 

Na PowerShell, crie uma nova rede NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Crie a máquina virtual do hóspede

>[!IMPORTANT] 
>
>O agente convidado Azure não é suportado em VMs aninhados, e pode causar problemas tanto no hospedeiro como nos VMs aninhados. Não instale o agente Azure em VMs aninhados e não utilize uma imagem para criar os VMs aninhados que já têm o agente convidado Azure instalado.

1. Abra o Hyper-V Manager e crie uma nova máquina virtual. Configure a máquina virtual para utilizar a nova rede Interna que criou.
    
    ![RedeConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Instale um sistema operativo na máquina virtual do hóspede.
    
    >[!NOTE] 
    >
    >É necessário um meio de instalação para instalar um sistema operativo no VM. Neste caso, estamos a usar o Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Atribua um endereço IP à máquina virtual de hóspedes

Pode atribuir um endereço IP à máquina virtual do hóspede, definindo manualmente um endereço IP estático na máquina virtual do hóspede ou configurando o DHCP no VM Azure para atribuir o endereço IP dinamicamente.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opção 1: Configurar o DHCP para atribuir dinamicamente um endereço IP à máquina virtual do hóspede
Siga os passos abaixo para configurar o DHCP na máquina virtual do anfitrião para a atribuição de endereços dinâmicos.

#### <a name="install-dhcp-server-on-the-azure-vm"></a>Instale o Servidor DHCP no Azure VM

1. Abra o Gestor de Servidores. No Painel de Instrumentos, clique em **Adicionar papéis e funcionalidades**. Surge o Assistente para Adicionar Funções e Funcionalidades.
  
2. No assistente, clique em **Seguinte** até à página 'Funções do Servidor'.
  
3. Clique para selecionar a caixa de verificação **do Servidor DHCP,** clique em **Adicionar Funcionalidades**e, em seguida, clique em **Seguinte** até completar o assistente.
  
4. Clique em **Install** (Instalar).

#### <a name="configure-a-new-dhcp-scope"></a>Configure um novo âmbito DHCP

1. Gestor aberto da DHCP.
  
2. No painel de navegação, expanda o nome do servidor, clique com o botão direito **IPv4**e clique em **New Scope**. O Novo Assistente de Âmbito aparece, clique em **Seguinte**.
  
3. Introduza um Nome e Descrição para o âmbito e clique em **Seguinte**.
  
4. Defina um intervalo IP para o seu Servidor DHCP (por exemplo, 192.168.0.100 a 192.168.0.200).
  
5. Clique em **seguida** até à página "Gateway Predefinido". Introduza o Endereço IP criado anteriormente (por exemplo, 192.168.0.1) como gateway padrão e, em seguida, clique em **Adicionar**.
  
6. Clique **em Seguinte** até que o assistente complete, deixando todos os valores predefinidos e, em seguida, clique em **Terminar**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opção 2: Definir manualmente um endereço IP estático na máquina virtual do hóspede
Se não configurar o DHCP para atribuir dinamicamente um endereço IP à máquina virtual do hóspede, siga estes passos para definir um endereço IP estático.

1. No Azure VM, abra a PowerShell como administrador.

2. Clique com o botão direito na máquina virtual do hóspede e clique em Connect.

3. Inscreva-se na máquina virtual do hóspede.

4. Na máquina virtual convidada, abra o Network and Sharing Center.

5. Configure o adaptador de rede para um endereço dentro do alcance da rede NAT que criou na secção anterior.

Neste exemplo, utilizará um endereço na gama 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testar conectividade na máquina virtual de hóspedes

Na máquina virtual do hóspede, abra o seu navegador e navegue para uma página web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

Para obter instruções sobre como permitir uma conectividade transparente entre VMs de hóspedes e VMs Azure, consulte [este documento](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization).
