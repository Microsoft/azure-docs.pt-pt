---
title: Criar uma ligação de VPN de site a site entre duas redes virtuais em diferentes ambientes do Kit de desenvolvimento do Azure Stack | Documentos da Microsoft
description: Procedimento passo a passo que um administrador da nuvem utiliza para criar uma ligação de VPN de site a site entre dois ambientes de Kit de desenvolvimento do Azure Stack de nó único.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: e038afe27718dcbd407e83b5c9190606e6b917c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004665"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Criar uma ligação de VPN de site a site entre duas redes virtuais em diferentes ambientes do Kit de desenvolvimento do Azure Stack
## <a name="overview"></a>Descrição geral
Este artigo mostra-lhe como criar uma ligação de VPN de site a site entre duas redes virtuais em dois ambientes separados do Kit de desenvolvimento do Azure Stack. Embora configurar as ligações, saiba como funcionam os gateways VPN no Azure Stack.

### <a name="connection-diagram"></a>Diagrama de ligação
O diagrama seguinte mostra o que a configuração da ligação deve ser semelhante quando tiver terminado.

![Configuração da ligação VPN site a site](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Antes de começar
Para concluir a configuração da ligação, certifique-se de que tem os seguintes itens antes de começar:

* Dois servidores e outros pré-requisitos que cumprem os requisitos de hardware do Kit de desenvolvimento do Azure Stack, conforme descrito em [início rápido: Avaliar o Kit de desenvolvimento do Azure Stack](azure-stack-deploy-overview.md). 
* O [Development Kit do Azure Stack](https://azure.microsoft.com/overview/azure-stack/try/) pacote de implementação.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Implementar os ambientes do Kit de desenvolvimento do Azure Stack
Para concluir a configuração da ligação, tem de implementar dois ambientes de Kit de desenvolvimento do Azure Stack.
> [!NOTE] 
> Para cada pilha Development Kit do Azure que implementar, siga os [instruções de implementação](azure-stack-run-powershell-script.md). Neste artigo, os ambientes do Kit de desenvolvimento do Azure Stack são chamados *POC1* e *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Preparar uma oferta na POC1 e POC2
Na POC1 e POC2, prepare-se uma oferta para que um utilizador pode subscrever a oferta e implementar as máquinas virtuais. Para obter informações sobre como criar uma oferta, consulte [disponibilizar as máquinas virtuais para os utilizadores do Azure Stack](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Reveja e conclua a tabela de configuração de rede
A tabela seguinte resume a configuração de rede em ambos os ambientes do Development Kit do Azure Stack. Utilize o procedimento que aparece depois da tabela para adicionar o endereço externo BGPNAT específica para a sua rede.

**Tabela de configuração de rede**

|   |POC1|POC2|
|---------|---------|---------|
|Nome da rede virtual:     |VNET-01|VNET-02 |
|Espaço de endereços de rede virtual |10.0.10.0/23|10.0.20.0/23|
|Nome da sub-rede     |Subnet-01|Subnet-02|
|Intervalo de endereços da sub-rede|10.0.10.0/24 |10.0.20.0/24 |
|Sub-rede de gateway     |10.0.11.0/24|10.0.21.0/24|
|Endereço BGPNAT externo     |         |         |

> [!NOTE]
> Os endereços de IP de BGPNAT externos no ambiente de exemplo são 10.16.167.195 para a POC1 e 10.16.169.131 para a POC2. Utilize o procedimento seguinte para determinar os endereços IP de BGPNAT externos para os anfitriões do Kit de desenvolvimento do Azure Stack e, em seguida, adicioná-los para a tabela de configuração de rede anterior.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obter o endereço IP do adaptador externo da VM NAT
1. Inicie sessão no computador físico do Azure Stack para a POC1.
2. Edite o seguinte código do Powershell para substituir a palavra-passe de administrador e, em seguida, execute o código do anfitrião da prova de conceito:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```
3. Adicione o endereço IP para a tabela de configuração de rede que é apresentada na secção anterior.

4. Repita este procedimento na POC2.

## <a name="create-the-network-resources-in-poc1"></a>Criar os recursos de rede na POC1
Agora, criar os recursos de rede de POC1 que precisa para definir a seus gateways. As instruções seguintes mostram-lhe como criar os recursos com o portal de utilizador. Também pode utilizar o código do PowerShell para criar os recursos.

![Fluxo de trabalho que é utilizado para criar recursos](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Inicie sessão como um inquilino
Um administrador de serviços pode iniciar sessão como um inquilino para testar os planos, ofertas e subscrições que seus inquilinos poderão utilizar. Se não tiver um, já [criar uma conta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sessão.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e a sub-rede VM
1. Utilize uma conta de inquilino para iniciar sessão no portal de utilizador.
2. No portal de utilizador, selecione **+ criar um recurso**.
3. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
4. Selecione **Rede virtual**.
5. Para **Name**, **espaço de endereços**, **nome da sub-rede**, e **intervalo de endereços da sub-rede**, utilize os valores que são apresentados anteriormente na rede tabela de configuração.
6. Na **subscrição**, aparece a subscrição que criou anteriormente.
7. Para **grupo de recursos**, pode criar um grupo de recursos ou se já tiver um, selecione **utilizar existente**.
8. Verifique a localização predefinida.
9. Selecione **Afixar ao dashboard**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway
1. No dashboard, abra o recurso de rede virtual da VNET-01 que criou anteriormente.
2. No painel **Definições**, selecione **Sub-redes**.
3. Para adicionar uma sub-rede de gateway para a rede virtual, selecione **sub-rede do Gateway**.
   
    ![Adicionar sub-rede do gateway](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Por predefinição, o nome da sub-rede está definido como **GatewaySubnet**.
   As sub-redes de gateway são especiais. Para funcionar corretamente, deve utilizar o *GatewaySubnet* nome.
5. Na **intervalo de endereços**, certifique-se de que o endereço está **10.0.11.0/24**.
6. Selecione **OK** para criar a sub-rede do gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **+ criar um recurso**. 
2. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Na **Name**, introduza **GW1**.
5. Selecione o **rede Virtual** item para escolher uma rede virtual.
   Selecione **VNET-01** da lista.
6. Selecione o **endereço IP público** item de menu. Quando o **escolher endereço IP público** é apresentado o painel, selecione **criar nova**.
7. Na **Name**, introduza **GW1-PiP**e, em seguida, selecione **OK**.
8.  Por predefinição, para **tipo de VPN**, **baseado na rota** está selecionada.
    Manter o **baseado na rota** tipo de VPN.
9. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao dashboard. Selecione **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local
A implementação de um *gateway de rede local* nesta implementação de avaliação do Azure Stack é um pouco diferente quando comparada com uma implementação do Azure real.

Numa implementação do Azure, um gateway de rede local representa um dispositivo físico no local (no inquilino), que utiliza para ligar a um gateway de rede virtual no Azure. Nesta implementação de avaliação do Azure Stack, ambas as extremidades da ligação são gateways de rede virtual!

Uma forma de pensar sobre isso, mais genericamente é que o recurso de gateway de rede local indica sempre o gateway remoto na outra extremidade da ligação. Devido à forma como o Kit de desenvolvimento do Azure Stack foi concebido, tem de fornecer o endereço IP do adaptador de rede externo na tradução de endereços de rede (NAT) VM a outras Stack Development Kit do Azure como o endereço IP público do gateway de rede local. Em seguida, criar mapeamentos NAT na VM NAT para se certificar de que ambas as extremidades estão ligadas corretamente.


### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local
1. Inicie sessão no computador físico do Azure Stack para a POC1.
2. No portal de utilizador, selecione **+ criar um recurso**.
3. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
4. Na lista de recursos, selecione **gateway de rede local**.
5. Na **Name**, introduza **POC2-GW**.
6. Na **endereço IP**, introduza o endereço externo BGPNAT para a POC2. Este endereço é apresentado anteriormente na tabela de configuração de rede.
7. Na **espaço de endereços**, para o espaço de endereços da VNET POC2 que criar mais tarde, introduza **10.0.20.0/23**.
8. Certifique-se de que sua **subscrição**, **grupo de recursos**, e **localização** estão corretos e, em seguida, selecione **criar**.

### <a name="create-the-connection"></a>Criar a ligação
1. No portal de utilizador, selecione **+ criar um recurso**.
2. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **ligação**.
4. Sobre o **Noções básicas** painel Definições, para o **tipo de ligação**, selecione **Site-site (IPSec)**.
5. Selecione o **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
6. Sobre o **configurações** painel, selecione **gateway de rede Virtual**e, em seguida, selecione **GW1**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **POC2-GW**.
8. Na **nome da ligação**, introduza **POC1-POC2**.
9. Na **chave partilhada (PSK)**, introduza **12345**e, em seguida, selecione **OK**.
10. Sobre o **resumo** painel, selecione **OK**.

### <a name="create-a-vm"></a>Criar uma VM
Para validar os dados que viaja através da ligação VPN, terá das máquinas virtuais para enviar e receber dados em cada Development Kit do Azure Stack. Criar uma máquina virtual na POC1 agora e, em seguida, na sua rede virtual, coloque-na sub-rede VM.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Aceda a **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens de máquinas virtuais, selecione o **Windows Server 2016 Datacenter Eval** imagem.
4. Sobre o **Noções básicas** painel, na **nome**, introduza **VM01**.
5. Introduza um nome de utilizador válido e palavra-passe. Utilize esta conta para iniciar sessão na VM depois de criado.
6. Fornecer um **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
7. Sobre o **tamanho** painel, para esta instância, selecione um tamanho de máquina virtual e, em seguida, selecione **selecione**.
8. Sobre o **definições** painel, aceite as predefinições. Certifique-se de que o **VNET-01** está selecionada a rede virtual. Certifique-se de que a sub-rede está definida como **10.0.10.0/24**. Em seguida, selecione **OK**.
9. Sobre o **resumo** painel, reveja as definições e, em seguida, selecione **OK**.



## <a name="create-the-network-resources-in-poc2"></a>Criar os recursos de rede na POC2

A próxima etapa é criar os recursos de rede para a POC2. As instruções seguintes mostram como criar os recursos com o portal de utilizador.

### <a name="sign-in-as-a-tenant"></a>Inicie sessão como um inquilino
Um administrador de serviços pode iniciar sessão como um inquilino para testar os planos, ofertas e subscrições que seus inquilinos poderão utilizar. Se não tiver um, já [criar uma conta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sessão.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e a sub-rede VM

1. Inicie sessão com uma conta de inquilino.
2. No portal de utilizador, selecione **+ criar um recurso**.
3. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
4. Selecione **Rede virtual**.
5. Utilize as informações que aparecem anteriormente na tabela de configuração de rede para identificar os valores para a POC2 **Name**, **espaço de endereços**, **nome da sub-rede**e **Intervalo de endereços da sub-rede**.
6. Na **subscrição**, aparece a subscrição que criou anteriormente.
7. Para **grupo de recursos**, crie um novo grupo de recursos ou, se já tiver um, selecione **utilizar existente**.
8. Certifique-se a predefinição **localização**.
9. Selecione **Afixar ao dashboard**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abra o recurso de rede Virtual que criou (**VNET-02**) a partir do dashboard.
2. No painel **Definições**, selecione **Sub-redes**.
3. Selecione **sub-rede do Gateway** para adicionar uma sub-rede de gateway para a rede virtual.
4. O nome da sub-rede está predefinido como **GatewaySubnet**.
   As sub-redes de gateway são especiais e têm de ter este nome específico para funcionarem corretamente.
5. Na **intervalo de endereços** campo, certifique-se de que o endereço é **10.0.21.0/24**.
6. Selecione **OK** para criar a sub-rede do gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **+ criar um recurso**.  
2. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Na **Name**, introduza **GW2**.
5. Para escolher uma rede virtual, selecione **rede Virtual**. Em seguida, selecione **VNET-02** da lista.
6. Selecione **Endereço IP público**. Quando o **escolher endereço IP público** é apresentado o painel, selecione **criar nova**.
7. Na **Name**, introduza **GW2-PiP**e, em seguida, selecione **OK**.
8. Por predefinição, para **tipo de VPN**, **baseado na rota** está selecionada.
    Manter o **baseado na rota** tipo de VPN.
9. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao dashboard. Selecione **Criar**.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. No portal de utilizador POC2, selecione **+ criar um recurso**. 
4. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
5. Na lista de recursos, selecione **gateway de rede Local**.
6. Na **Name**, introduza **POC1-GW**.
7. Na **endereço IP**, introduza o endereço externo BGPNAT para a POC1 listado anteriormente na tabela de configuração de rede.
8. Na **espaço de endereços**, da POC1, introduza o **10.0.10.0/23** espaço de endereços da **VNET-01**.
9. Certifique-se de que sua **subscrição**, **grupo de recursos**, e **localização** estão corretos e, em seguida, selecione **criar**.

## <a name="create-the-connection"></a>Criar a ligação
1. No portal de utilizador, selecione **+ criar um recurso**. 
2. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **ligação**.
4. Na **básica** painel Definições, para o **tipo de ligação**, escolha **Site-site (IPSec)**.
5. Selecione o **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
6. Sobre o **configurações** painel, selecione **gateway de rede Virtual**e, em seguida, selecione **GW2**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **POC1-GW**.
8. Na **nome da ligação**, introduza **POC2-POC1**.
9. Na **chave partilhada (PSK)**, introduza **12345**. Se escolher um valor diferente, lembre-se de que ele *tem* corresponde ao valor para a chave partilhada que criou na POC1. Selecione **OK**.
10. Reveja os **resumo** painel e, em seguida, selecione **OK**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Criar uma máquina virtual na POC2 agora e coloque-na sub-rede VM na sua rede virtual.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Aceda a **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens de máquinas virtuais, selecione o **Windows Server 2016 Datacenter Eval** imagem.
4. Sobre o **Noções básicas** painel, para **nome**, introduza **VM02**.
5. Introduza um nome de utilizador válido e palavra-passe. Utilize esta conta para iniciar sessão para a máquina virtual depois de criado.
6. Fornecer um **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
7. Sobre o **tamanho** painel, selecione uma máquina virtual de tamanho para esta instância e, em seguida, selecione **selecione**.
8. Sobre o **definições** painel, pode aceitar as predefinições. Certifique-se de que o **VNET-02** está selecionada a rede virtual e certifique-se de que a sub-rede está definida como **10.0.20.0/24**. Selecione **OK**.
9. Reveja as definições na **resumo** painel e, em seguida, selecione **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Configurar a máquina virtual do NAT em cada Development Kit do Azure Stack para o gateway transversal
Uma vez que o Kit de desenvolvimento do Azure Stack é independente e isolada da rede no qual está implementado no anfitrião físico, o *externo* rede VIP que os gateways estão ligados a não é efetivamente externa. Em vez disso, a rede VIP está oculta atrás de um router que executa a tradução de endereços de rede. 

O router é uma máquina virtual do Windows Server, chamada *AzS-bgpnat01*, que executa a função de encaminhamento e serviços de acesso remoto (RRAS) na infraestrutura do Azure Stack Development Kit. É necessário configurar a NAT na máquina virtual AzS-bgpnat01 para permitir a ligação de VPN de site a site ligar em ambas as extremidades. 

Para configurar a ligação VPN, tem de criar uma rota de mapa NAT estática que mapeie a interface externa na máquina de virtual BGPNAT para o VIP do conjunto de Gateway Edge. Uma rota de mapa NAT estática é necessária para cada porta numa ligação VPN.

> [!NOTE]
> Esta configuração é necessária para ambientes do Kit de desenvolvimento do Azure Stack apenas.
> 
> 

### <a name="configure-the-nat"></a>Configurar o NAT
> [!IMPORTANT]
> Tem de concluir este procedimento para *ambos* ambientes do Kit de desenvolvimento do Azure Stack.

1. Determinar os **endereço IP interno** para utilizar o seguinte script do PowerShell. Abra o gateway de rede virtual (GW1 e GW2) e, em seguida, no **descrição geral** painel, guarde o valor para o **endereço IP público** para utilização posterior.
![Endereço IP](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Inicie sessão no computador físico do Azure Stack para a POC1.
3. Copie e edite o seguinte script do PowerShell. Para configurar o NAT em cada Development Kit do Azure Stack, execute o script num ISE do PowerShell elevada do Windows. No script, adicione valores para o *endereço externo BGPNAT* e *endereço IP interno* marcadores de posição:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Repita este procedimento na POC2.

## <a name="test-the-connection"></a>Testar a ligação
Agora que a ligação de site a site é estabelecida, deve validar que pode obter o tráfego que flui através do mesmo. Para validar, iniciar sessão a uma das máquinas virtuais que criou em qualquer ambiente do Development Kit do Azure Stack. Em seguida, enviar um ping a máquina virtual que criou no outro ambiente. 

Para garantir que envia o tráfego através da ligação de site a site, certifique-se de que enviar pings para o endereço IP direto (DIP) da máquina virtual na sub-rede remota, não o VIP. Para tal, encontre o endereço do DIP na outra extremidade da ligação. Guarde o endereço para utilização posterior.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Inicie sessão na VM de inquilinos na POC1
1. Inicie sessão no computador físico do Azure Stack para a POC1 e, em seguida, utilizar uma conta de inquilino para iniciar sessão no portal de utilizador.
2. Na barra de navegação esquerdo, selecione **computação**.
3. Na lista de VMs, localize **VM01** que criou anteriormente e, em seguida, selecioná-lo.
4. No painel da máquina virtual, clique em **Connect**e, em seguida, abra o ficheiro de VM01.rdp.
   
     ![Botão Ligar](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Inicie sessão com a conta que configurou quando criou a máquina virtual.
6. Abra uma **Windows PowerShell** janela.
7. Enter **ipconfig /all**.
8. Na saída, localize a **endereço IPv4**e, em seguida, guarde o endereço para utilização posterior. Este é o endereço que ping irá enviar a partir da POC2. No ambiente de exemplo, o endereço é **10.0.10.4**, mas no seu ambiente poderá ser diferente. Ele deve coincidir com o **10.0.10.0/24** sub-rede que criou anteriormente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Inicie sessão na VM de inquilinos na POC2
1. Inicie sessão no computador físico do Azure Stack para a POC2 e, em seguida, utilizar uma conta de inquilino para iniciar sessão no portal de utilizador.
2. Na barra de navegação esquerdo, clique em **computação**.
3. Na lista de máquinas virtuais, encontrar **VM02** que criou anteriormente e, em seguida, selecioná-lo.
4. No painel da máquina virtual, clique em **Ligar**.
5. Inicie sessão com a conta que configurou quando criou a máquina virtual.
6. Abra uma **Windows PowerShell** janela.
7. Enter **ipconfig /all**.
8. Deverá ver um endereço IPv4, que se encontrem dentro **10.0.20.0/24**. No ambiente de exemplo, o endereço está **10.0.20.4**, mas seu endereço poderá ser diferente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Da máquina virtual na POC2, enviar um ping a máquina virtual na POC1, através do túnel. Para fazer isso, enviar pings para o DIP que registou da VM01.
   No ambiente de exemplo, isto é **10.0.10.4**, mas não se esqueça de fazer ping no endereço que anotou no seu laboratório. Deverá ver um resultado que é semelhante ao seguinte:
   
    ![Ping enviado com êxito](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Uma resposta de máquina virtual remota indica um teste com êxito! Pode fechar a janela de máquina virtual. Para testar a ligação, pode experimentar outros tipos de transferências de dados, como uma cópia de ficheiros.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Ver estatísticas de transferência de dados através da ligação de gateway
Se quiser saber a quantidade de dados passa por meio da sua ligação site a site, esta informação está disponível na **ligação** painel. Este teste também é outra forma de verificar que o ping que acabou de enviar foi efetivamente transmitido através da ligação VPN.

1. Enquanto tem sessão iniciada para a máquina virtual de inquilinos na POC2, utilize a sua conta de inquilino para iniciar sessão no portal de utilizador.
2. Aceda a **todos os recursos**e, em seguida, selecione a **POC2-POC1** ligação. **Ligações** aparece.
4. Sobre o **ligação** painel, as estatísticas de **dados no** e **dados de saída** aparecem. Captura de ecrã seguinte, os números grandes são atribuídos para transferência de ficheiros adicionais. Deverá ver alguns valores diferentes de zero aí.
   
    ![Entrada e saída de dados](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
