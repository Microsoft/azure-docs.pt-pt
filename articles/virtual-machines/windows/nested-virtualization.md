---
title: Como habilitar a virtualização aninhada em máquinas virtuais do Azure
description: Como habilitar a virtualização aninhada em máquinas virtuais do Azure
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
ms.openlocfilehash: 16f5bed5a2342bb1d120d0d3dc853e0bc44376dc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033128"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Como habilitar a virtualização aninhada em uma VM do Azure

A virtualização aninhada tem suporte em várias famílias de máquinas virtuais do Azure. Esse recurso oferece grande flexibilidade ao dar suporte a cenários como ambientes de desenvolvimento, teste, treinamento e demonstração.   

Este artigo percorre como habilitar o Hyper-V em uma VM do Azure e configurar a conectividade com a Internet para essa máquina virtual convidada.

## <a name="create-a-nesting-capable-azure-vm"></a>Criar uma VM do Azure com capacidade de aninhamento

Crie uma nova VM do Windows Server 2016 do Azure.  Para referência rápida, todas as máquinas virtuais v3 dão suporte à virtualização aninhada. Para obter uma lista completa de tamanhos de máquinas virtuais que dão suporte ao aninhamento, confira o [artigo unidade de computação do Azure](acu.md).

Lembre-se de escolher um tamanho de VM grande o suficiente para dar suporte às demandas de uma máquina virtual convidada. Neste exemplo, estamos usando uma VM do Azure de tamanho D3_v3. 

Você pode exibir a disponibilidade regional das máquinas virtuais da série Dv3 ou Ev3 [aqui](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Para obter instruções detalhadas sobre como criar uma nova máquina virtual, consulte [criar e gerenciar VMs do Windows com o módulo Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Conectar-se à sua VM do Azure

Crie uma ligação de ambiente de trabalho remoto para a máquina virtual.

1. Clique no botão **Ligar** nas propriedades da máquina virtual. É criado e transferido um ficheiro do Protocolo do Ambiente de Trabalho Remoto (ficheiro .rdp).

2. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) a partir da Mac App Store.

3. Introduza o nome de utilizador e palavra-passe que especificou ao criar a máquina virtual e, em seguida, clique em **Ok**.

4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Habilitar o recurso Hyper-V na VM do Azure
Você pode definir essas configurações manualmente ou nós fornecemos um script do PowerShell para automatizar a configuração.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opção 1: usar um script do PowerShell para configurar a virtualização aninhada
Um script do PowerShell para habilitar a virtualização aninhada em um host do Windows Server 2016 está disponível no [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). O script verifica os pré-requisitos e, em seguida, configura a virtualização aninhada na VM do Azure. Uma reinicialização da VM do Azure é necessária para concluir a configuração. Esse script pode funcionar em outros ambientes, mas não é garantido. Confira a postagem do blog do Azure com uma demonstração de vídeo ao vivo sobre a virtualização aninhada em execução no Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opção 2: configurar a virtualização aninhada manualmente

1. Na VM do Azure, abra o PowerShell como administrador. 

2. Habilite o recurso Hyper-V e as ferramentas de gerenciamento.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Este comando reinicia a VM do Azure. Você perderá a conexão RDP durante o processo de reinicialização.
    
3. Depois que a VM do Azure for reiniciada, reconecte-se à sua VM usando o RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Configurar a conectividade com a Internet para a máquina virtual convidada
Crie um novo adaptador de rede virtual para a máquina virtual convidada e configure um gateway NAT para habilitar a conectividade com a Internet.

### <a name="create-a-nat-virtual-network-switch"></a>Criar um comutador de rede virtual NAT

1. Na VM do Azure, abra o PowerShell como administrador.
   
2. Crie um comutador interno.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Exiba as propriedades da opção e observe o ifIndex para o novo adaptador.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Anote o "ifIndex" para o comutador virtual que você acabou de criar.
    
4. Crie um endereço IP para o gateway NAT.
    
Para configurar o gateway, você precisa de algumas informações sobre sua rede:    
  * IPAddress-o IP do gateway NAT especifica o endereço IPv4 ou IPv6 a ser usado como o endereço de gateway padrão para a sub-rede da rede virtual. O formulário genérico é a. b. c. 1 (por exemplo, "192.168.0.1"). Embora a posição final não tenha de ser 1, ela geralmente é (com base no comprimento do prefixo). Normalmente, você deve usar um espaço de endereço de rede privada RFC 1918. 
  * PrefixLength-o comprimento do prefixo de sub-rede define o tamanho da sub-rede local (máscara de sub-rede). O comprimento do prefixo de sub-rede será um valor inteiro entre 0 e 32. 0 mapearia toda a Internet, 32 permitiria apenas um IP mapeado. Os valores comuns variam de 24 a 12, dependendo de quantos IPs precisam ser anexados ao NAT. Um PrefixLength comum é 24--é uma máscara de sub-rede de 255.255.255.0.
  * InterfaceIndex- **ifIndex** é o índice de interface do comutador virtual criado na etapa anterior. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Criar a rede NAT

Para configurar o gateway, você precisará fornecer informações sobre a rede e o gateway NAT:
  * Nome-esse é o nome da rede NAT. 
  * InternalIPInterfaceAddressPrefix-o prefixo de sub-rede NAT descreve o prefixo IP do gateway NAT acima, bem como o comprimento do prefixo da sub-rede NAT acima. O formulário genérico será a. b. c. 0/comprimento do prefixo de sub-rede NAT. 

No PowerShell, crie uma nova rede NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Criar a máquina virtual convidada

>[!IMPORTANT] 
>
>O agente convidado do Azure não tem suporte em VMs aninhadas e pode causar problemas no host e nas VMs aninhadas. Não instale o agente do Azure em VMs aninhadas e não use uma imagem para criar as VMs aninhadas que já têm o agente convidado do Azure instalado.

1. Abra o Gerenciador do Hyper-V e crie uma nova máquina virtual. Configure a máquina virtual para usar a nova rede interna que você criou.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Instale um sistema operacional na máquina virtual convidada.
    
    >[!NOTE] 
    >
    >Você precisa de mídia de instalação para que um sistema operacional seja instalado na VM. Nesse caso, estamos usando o Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Atribuir um endereço IP à máquina virtual convidada

Você pode atribuir um endereço IP à máquina virtual convidada definindo manualmente um endereço IP estático na máquina virtual convidada ou configurando o DHCP na VM do Azure para atribuir o endereço IP dinamicamente.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opção 1: configurar o DHCP para atribuir dinamicamente um endereço IP à máquina virtual convidada
Siga as etapas abaixo para configurar o DHCP na máquina virtual do host para atribuição de endereço dinâmico.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Instalar o servidor DCHP na VM do Azure

1. Abra Gerenciador do Servidor. No painel, clique em **adicionar funções e recursos**. O assistente Adicionar funções e recursos é exibido.
  
2. No assistente, clique em **Avançar** até a página funções de servidor.
  
3. Clique para selecionar a caixa de seleção **servidor DHCP** , clique em **Adicionar recursos**e, em seguida, clique em **Avançar** até concluir o assistente.
  
4. Clique em **Instalar**.

#### <a name="configure-a-new-dhcp-scope"></a>Configurar um novo escopo do DHCP

1. Abra o Gerenciador DHCP.
  
2. No painel de navegação, expanda o nome do servidor, clique com o botão direito do mouse em **IPv4**e clique em **novo escopo**. O assistente de novo escopo é exibido, clique em **Avançar**.
  
3. Insira um nome e uma descrição para o escopo e clique em **Avançar**.
  
4. Defina um intervalo de IP para o servidor DCHP (por exemplo, 192.168.0.100 para 192.168.0.200).
  
5. Clique em **Avançar** até a página gateway padrão. Insira o endereço IP que você criou anteriormente (por exemplo, 192.168.0.1) como o gateway padrão e clique em **Adicionar**.
  
6. Clique em **Avançar** até que o assistente seja concluído, deixando todos os valores padrão e clique em **concluir**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opção 2: definir manualmente um endereço IP estático na máquina virtual convidada
Se você não configurou o DHCP para atribuir dinamicamente um endereço IP à máquina virtual convidada, siga estas etapas para definir um endereço IP estático.

1. Na VM do Azure, abra o PowerShell como administrador.

2. Clique com o botão direito do mouse na máquina virtual convidada e clique em conectar.

3. Entre na máquina virtual convidada.

4. Na máquina virtual convidada, abra a central de rede e compartilhamento.

5. Configure o adaptador de rede para um endereço dentro do intervalo da rede NAT que você criou na seção anterior.

Neste exemplo, você usará um endereço no intervalo 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testar a conectividade na máquina virtual convidada

Na máquina virtual convidada, abra o navegador e navegue até uma página da Web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Configurar a conectividade da intranet para a máquina virtual convidada

Para obter instruções sobre como habilitar a conectividade transparente entre VMs convidadas e VMs do Azure, consulte [este documento](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
