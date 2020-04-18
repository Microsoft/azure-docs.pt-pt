---
title: Como permitir a virtualização aninhada em Máquinas Virtuais Azure
description: Como permitir a virtualização aninhada em Máquinas Virtuais Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 7858bd467c6e3fd82fcb3803a98e96b265f17d23
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605260"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Como permitir a virtualização aninhada num VM Azure

A virtualização aninhada é suportada em várias famílias de máquinas virtuais Azure. Esta capacidade proporciona uma grande flexibilidade no apoio a cenários como desenvolvimento, testes, formação e ambientes de demonstração.   

Este artigo passa por permitir o Hyper-V num VM Azure e configurar a conectividade da Internet com aquela máquina virtual do hóspede.

## <a name="create-a-nesting-capable-azure-vm"></a>Criar um Azure VM capaz de nidificação

Crie um novo Windows Server 2016 Azure VM. Para obter uma lista completa dos tamanhos das máquinas virtuais que suportam a nidificação, consulte o artigo da [Unidade de Computação Azure](acu.md).

Lembre-se de escolher um tamanho VM grande o suficiente para suportar as exigências de uma máquina virtual de hóspedes. Neste exemplo, estamos a utilizar um D3_v3 tamanho Azure VM. 

Pode ver aqui a disponibilidade regional de [máquinas](https://azure.microsoft.com/regions/services/)virtuais da série Dv3 ou Ev3.

>[!NOTE]
>
>Para obter instruções detalhadas sobre a criação de uma nova máquina virtual, consulte [Criar e Gerir VMs windows com o módulo PowerShell Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Ligue-se ao seu Azure VM

Crie uma ligação de ambiente de trabalho remoto para a máquina virtual.

1. Clique no botão **Ligar** nas propriedades da máquina virtual. É criado e transferido um ficheiro do Protocolo do Ambiente de Trabalho Remoto (ficheiro .rdp).

2. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) a partir da Mac App Store.

3. Introduza o nome de utilizador e palavra-passe que especificou ao criar a máquina virtual e, em seguida, clique em **Ok**.

4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Ativar a funcionalidade Hyper-V no Azure VM
Pode configurar estas definições manualmente ou fornecemos um script PowerShell para automatizar a configuração.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opção 1: Utilize um script PowerShell para configurar a virtualização aninhada
Um script PowerShell para permitir a virtualização aninhada num anfitrião do Windows Server 2016 está disponível no [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). O guião verifica os pré-requisitos e, em seguida, confunde a virtualização aninhada no VM Azure. É necessário um reinício do VM Azure para completar a configuração. Este guião pode funcionar noutros ambientes, mas não está garantido. Confira a publicação do blog azure com uma demonstração em vídeo ao vivo sobre a virtualização aninhada em execução no Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opção 2: Configurar a virtualização aninhada manualmente

1. No Azure VM, abra a PowerShell como administrador. 

2. Ative a funcionalidade Hyper-V e as Ferramentas de Gestão.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Este comando reinicia o VM Azure. Perderá a ligação RDP durante o processo de reinício.
    
3. Depois do VM Azure reiniciar, religue a sua VM utilizando RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Configurar a conectividade da internet para a máquina virtual do hóspede
Crie um novo adaptador de rede virtual para a máquina virtual do hóspede e configure um Portal NAT para permitir a conectividade da Internet.

### <a name="create-a-nat-virtual-network-switch"></a>Criar um interruptor de rede virtual NAT

1. No Azure VM, abra a PowerShell como administrador.
   
2. Criar um interruptor interno.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Veja as propriedades do interruptor e note o ifIndex para o novo adaptador.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Tome nota do "ifIndex" para o interruptor virtual que acabou de criar.
    
4. Crie um endereço IP para o Portal NAT.
    
Para configurar o portal, precisa de algumas informações sobre a sua rede:    
  * IPAddress - O IP de Gateway NAT especifica o endereço IPv4 ou IPv6 para usar como o endereço de gateway predefinido para a sub-rede virtual. A forma genérica é a.b.c.1 (por exemplo, "192.168.0.1"). Embora a posição final não tenha de ser .1, normalmente é (com base no comprimento de pré-fixação). Normalmente deve utilizar um espaço de endereço de rede privada RFC 1918. 
  * PrefixoComprimento - O comprimento de prefixo da sub-rede define o tamanho da sub-rede local (máscara de sub-rede). O comprimento de prefixo da sub-rede será um valor inteiro entre 0 e 32. 0 mapearia toda a internet, 32 só permitiria um IP mapeado. Os valores comuns variam entre 24 e 12, dependendo do número de IPs necessários para ser anexado ao NAT. Um PrefixLength comum é 24 - esta é uma máscara de sub-rede de 255.255.255.0.
  * InterfaceIndex - **seO Index** é o índice de interface do interruptor virtual criado no passo anterior. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Criar a rede NAT

Para configurar o portal, terá de fornecer informações sobre a rede e o PORTAL NAT:
  * Nome - Este é o nome da rede NAT. 
  * InternalIPInterfaceAddressPrefix - O prefixo da sub-rede NAT descreve tanto o prefixo IP DE Gateway NAT de cima como o comprimento de prefixo da sub-rede NAT de cima. O formulário genérico será a.b.c.0/NAT Subnet Prefix Comprimento. 

Na PowerShell, crie uma nova rede NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Crie a máquina virtual de hóspedes

>[!IMPORTANT] 
>
>O agente convidado azure não é apoiado em VMs aninhados, e pode causar problemas tanto no hospedeiro como nos VMs aninhados. Não instale o agente Azure em VMs aninhados e não utilize uma imagem para criar os VMs aninhados que já tem o agente convidado Azure instalado.

1. Abra o Hyper-V Manager e crie uma nova máquina virtual. Configure a máquina virtual para utilizar a nova rede Interna que criou.
    
    ![RedeConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Instale um sistema operativo na máquina virtual do hóspede.
    
    >[!NOTE] 
    >
    >Precisa de meios de instalação para instalar um sistema operativo no VM. Neste caso, estamos a utilizar o Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Atribuir um endereço IP à máquina virtual do hóspede

Pode atribuir um endereço IP à máquina virtual do hóspede, quer colocando manualmente um endereço IP estático na máquina virtual do hóspede ou configurando o DHCP no VM Azure para atribuir o endereço IP de forma dinâmica.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opção 1: Configure o DHCP para atribuir dinamicamente um endereço IP à máquina virtual do hóspede
Siga os passos abaixo para configurar o DHCP na máquina virtual do hospedeiro para uma atribuição dinâmica de endereços.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Instale o Servidor DCHP no Azure VM

1. Abra o Gestor de Servidores. No Dashboard, clique em **Adicionar papéis e funcionalidades**. Surge o Assistente para Adicionar Funções e Funcionalidades.
  
2. No assistente, clique em **Seguinte** até a página de Funções do Servidor.
  
3. Clique para selecionar a caixa de verificação **do Servidor DHCP,** clique em **Adicionar Funcionalidades**e, em seguida, clique **em Seguinte** até completar o assistente.
  
4. Clique em **Instalar**.

#### <a name="configure-a-new-dhcp-scope"></a>Configure um novo âmbito dHCP

1. Gestor aberto da DHCP.
  
2. No painel de navegação, expanda o nome do servidor, clique no **iPv4,** clique à direita e clique em **New Scope**. O Novo Assistente de Âmbito aparece, clique **em Next**.
  
3. Introduza um Nome e Descrição para o âmbito e clique **em Next**.
  
4. Defina uma gama IP para o seu Servidor DCHP (por exemplo, 192.168.0.100 a 192.168.0.200).
  
5. Clique em **Seguir** até a página 'Gateway' predefinido. Introduza o Endereço IP que criou anteriormente (por exemplo, 192.168.0.1) como gateway predefinido e, em seguida, clique em **Adicionar**.
  
6. Clique em **Seguir** até que o assistente complete, deixando todos os valores predefinidos e, em seguida, clique em **Terminar**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opção 2: Definir manualmente um endereço IP estático na máquina virtual do hóspede
Se não configurou o DHCP para atribuir dinamicamente um endereço IP à máquina virtual do hóspede, siga estes passos para definir um endereço IP estático.

1. No Azure VM, abra a PowerShell como administrador.

2. Clique na máquina virtual do hóspede e clique em Connect.

3. Inscreva-se na máquina virtual convidada.

4. Na máquina virtual de hóspedes, abra o Centro de Rede e Partilha.

5. Configure o adaptador de rede para um endereço dentro do alcance da rede NAT que criou na secção anterior.

Neste exemplo, utilizará um endereço na gama 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testar conectividade na máquina virtual do hóspede

Na máquina virtual de hóspedes, abra o seu navegador e navegue para uma página web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Configurar a conectividade intranet para a máquina virtual do hóspede

Para obter instruções sobre como permitir uma conectividade transparente entre VMs convidados e VMs Azure, por favor, consulte [este documento](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
