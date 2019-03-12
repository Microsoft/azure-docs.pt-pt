---
title: Ligar o Azure Stack ao Azure através de VPN
description: Como ligar redes virtuais no Azure Stack para redes virtuais no Azure através da VPN.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2018
ms.openlocfilehash: d2766416c84233281e55ff6f8be8519b7aadfb8c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763559"
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Ligar o Azure Stack ao Azure através de VPN

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo mostra-lhe como criar uma VPN de site a site para ligar uma rede virtual no Azure Stack para uma rede virtual no Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir a configuração da ligação, certifique-se de que tem os seguintes itens antes de começar:

* Uma pilha do Azure integrado a implementação de sistemas (com vários nós) que estejam ligada diretamente à internet. O intervalo de endereços IP público externo tem de ser diretamente acessível a partir da internet pública.
* Uma subscrição do Azure válida. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>Diagrama da ligação VPN

A figura seguinte mostra o que a configuração da ligação deve ser semelhante quando tiver terminado:

![Configuração da ligação VPN site a site](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Valores de exemplo de configuração de rede

A tabela de exemplos de configuração de rede mostra os valores que são utilizados para os exemplos neste artigo. Pode utilizar estes valores, ou pode consultá-los para compreender melhor os exemplos neste artigo:

|   |Azure Stack|Azure|
|---------|---------|---------|
|Nome da rede virtual:     |Azs-VNet|AzureVNet |
|Espaço de endereços de rede virtual |10.1.0.0/16|10.100.0.0/16|
|Nome da sub-rede     |FrontEnd|FrontEnd|
|Intervalo de endereços da sub-rede|10.1.0.0/24 |10.100.0.0/24 |
|Sub-rede de gateway     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Criar os recursos de rede no Azure

Em primeiro lugar, crie os recursos de rede para o Azure. As instruções seguintes mostram como criar os recursos com o [portal do Azure](https://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Criar a rede virtual e uma sub-rede de máquinas virtuais (VM)

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.
2. No portal de utilizador, selecione **+ criar um recurso**.
3. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
4. Selecione **Rede virtual**.
5. Utilize as informações da tabela de configuração de rede para identificar os valores para o Azure **Name**, **espaço de endereços**, **nome da sub-rede**, e **endereço de sub-rede intervalo**.
6. Para **grupo de recursos**, crie um novo grupo de recursos ou, se já tiver um, selecione **utilizar existente**.
7. Selecione o **localização** da sua VNet.  Se estiver a utilizar os valores de exemplo, selecione **E.U.A. Leste** ou utilize outra localização.
8. Selecione **Afixar ao dashboard**.
9. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a sub-rede do Gateway

1. Abra o recurso de rede virtual que criou (**AzureVNet**) a partir do dashboard.
2. Sobre o **configurações** secção, selecione **sub-redes**.
3. Selecione **sub-rede do Gateway** para adicionar uma sub-rede de gateway para a rede virtual.
4. O nome da sub-rede está predefinido como **GatewaySubnet**.

   >[!IMPORTANT]
   >As sub-redes de gateway são especiais e têm de ter este nome específico para funcionarem corretamente.

5. Na **intervalo de endereços** campo, certifique-se de que o endereço é **10.100.1.0/24**.
6. Selecione **OK** para criar a sub-rede do gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal do Azure, selecione **+ criar um recurso**.  
2. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Na **Name**, tipo **do Azure-GW**.
5. Para escolher uma rede virtual, selecione **rede Virtual**. Em seguida, selecione **AzureVnet** da lista.
6. Selecione **Endereço IP público**. Quando o **escolher endereço IP público** secção se abrir, selecione **criar nova**.
7. Na **Name**, tipo **do Azure-GW-PiP**e, em seguida, selecione **OK**.
8. Por predefinição, para **tipo de VPN**, **baseado na rota** está selecionada. Manter o **baseado na rota** tipo de VPN.
9. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao dashboard. Selecione **Criar**.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. No portal do Azure, selecione **+ criar um recurso**.
2. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **gateway de rede Local**.
4. Na **Name**, tipo **Azs-GW**.
5. Na **endereço IP**, escreva o endereço IP público para o Azure Stack Gateway de rede Virtual que está listado anteriormente na tabela de configuração de rede.
6. Na **espaço de endereços**, a partir do Azure Stack, escreva o **10.1.0.0/24** e **10.1.1.0/24** espaço de endereços **AzureVNet**.
7. Certifique-se de que sua **subscrição**, **grupo de recursos**, e **localização** estão corretos e, em seguida, selecione **criar**.

## <a name="create-the-connection"></a>Criar a ligação

1. No portal de utilizador, selecione **+ criar um recurso**.
2. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **ligação**.
4. Na **básica** da secção definições, para o **tipo de ligação**, escolha **Site-site (IPSec)**.
5. Selecione o **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
6. Sobre o **definições** secção, selecione **gateway de rede Virtual**e, em seguida, selecione **Azure-GW**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **Azs-GW**.
8. Na **nome da ligação**, tipo **Azure Azs**.
9. Na **chave partilhada (PSK)**, tipo **12345**, em seguida, selecione **OK**.

   >[!NOTE]
   >Se utilizar um valor diferente para a chave partilhada, lembre-se de que tem de corresponder o valor para a chave partilhada que criou na outra extremidade da ligação.

10. Reveja os **resumo** secção e, em seguida, selecione **OK**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Agora, crie uma máquina virtual no Azure e colocá-la na sua sub-rede VM na sua rede virtual.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Aceda a **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens de máquinas virtuais, selecione o **Windows Server 2016 Datacenter Eval** imagem.
4. Sobre o **Noções básicas** secção, para **nome**, tipo **AzureVM**.
5. Escreva um nome de utilizador válido e palavra-passe. Utilize esta conta para iniciar sessão para a máquina virtual depois de criado.
6. Fornecer um **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
7. Sobre o **tamanho** secção, selecione um tamanho de máquina virtual para esta instância e, em seguida, selecione **selecione**.
8. Na **definições** secção, pode utilizar as predefinições. Antes de selecionar **OK**, confirme que:

   * O **AzureVnet** está selecionada a rede virtual.
   * A sub-rede está definida como **10.100.0.0/24**.

   Selecione **OK**.

9. Reveja as definições na **resumo** secção e, em seguida, selecione **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>Criar os recursos de rede no Azure Stack

Em seguida, crie os recursos de rede no Azure Stack.

### <a name="sign-in-as-a-user"></a>Inicie sessão como um utilizador

Um administrador de serviços pode iniciar sessão como um utilizador para testar os planos, ofertas e subscrições que seus usuários poderão utilizar. Se não tiver um, já [criar uma conta de utilizador](../azure-stack-add-new-user-aad.md) antes de iniciar sessão.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Criar a rede virtual e uma sub-rede VM

1. Utilize uma conta de utilizador para iniciar sessão no portal de utilizador.
2. No portal de utilizador, selecione **+ criar um recurso**.

    ![Criar uma nova rede virtual](media/azure-stack-connect-vpn/image3.png)

3. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
4. Selecione **Rede virtual**.
5. Para **Name**, **espaço de endereços**, **nome da sub-rede**, e **intervalo de endereços da sub-rede**, utilize os valores da tabela de configuração de rede.
6. Na **subscrição**, aparece a subscrição que criou anteriormente.
7. Para **grupo de recursos**, pode criar um grupo de recursos ou se já tiver um, selecione **utilizar existente**.
8. Verifique a localização predefinida.
9. Selecione **Afixar ao dashboard**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. No dashboard, abra o recurso de rede virtual Azs-VNet que criou.
2. Sobre o **configurações** secção, selecione **sub-redes**.
3. Para adicionar uma sub-rede de gateway para a rede virtual, selecione **sub-rede do Gateway**.

    ![Adicionar sub-rede do gateway](media/azure-stack-connect-vpn/image4.png)

4. Por predefinição, o nome da sub-rede está definido como **GatewaySubnet**. Para sub-redes de gateway funcione corretamente, deve utilizar o **GatewaySubnet** nome.
5. Na **intervalo de endereços**, certifique-se de que o endereço está **10.1.1.0/24**.
6. Selecione **OK** para criar a sub-rede do gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal do Azure Stack, selecione **+ criar um recurso**.
2. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Na **Name**, tipo **Azs-GW**.
5. Selecione o **rede Virtual** item para escolher uma rede virtual. Selecione **Azs-VNet** da lista.
6. Selecione o **endereço IP público** item de menu. Quando o **escolher endereço IP público** secção se abrir, selecione **criar nova**.
7. Na **Name**, tipo **Azs-GW-PiP**e, em seguida, selecione **OK**.
8. Por predefinição, **baseado na rota** está selecionada para **tipo de VPN**. Manter o **baseado na rota** tipo de VPN.
9. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao dashboard. Selecione **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local

O conceito de um *gateway de rede local* no Azure Stack é um pouco diferente numa implementação do Azure.

Numa implementação do Azure, um gateway de rede local representa um dispositivo físico no local (disponíveis na localização do utilizador) que se liga a um gateway de rede virtual no Azure. No entanto, no Azure Stack ambas as extremidades da ligação são gateways de rede virtual.

Uma descrição mais genérica é que o recurso de gateway de rede local indica sempre o gateway remoto na outra extremidade da ligação.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. Inicie sessão no portal do Azure Stack.
2. No portal de utilizador, selecione **+ criar um recurso**.
3. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
4. Na lista de recursos, selecione **gateway de rede local**.
5. Na **Name**, tipo **do Azure-GW**.
6. Na **endereço IP**, escreva o endereço IP público para o gateway de rede virtual no Azure **do Azure-GW-PiP**. Este endereço é apresentado anteriormente na tabela de configuração de rede.
7. Na **espaço de endereços**, para o espaço de endereços da VNET do Azure que criou, escreva **10.100.0.0/24** e **10.100.1.0/24**.
8. Certifique-se de que sua **subscrição**, **grupo de recursos**, e **localização** valores estão corretos e, em seguida, selecione **criar**.

### <a name="create-the-connection"></a>Criar a ligação

1. No portal de utilizador, selecione **+ criar um recurso**.
2. Aceda a **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **ligação**.
4. Sobre o **Noções básicas** da secção definições, para o **tipo de ligação**, selecione **Site-site (IPSec)**.
5. Selecione o **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
6. Sobre o **configurações** secção, selecione **gateway de rede Virtual**e, em seguida, selecione **Azs-GW**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **do Azure-GW**.
8. Na **nome da ligação**, tipo **Azs-Azure**.
9. Na **chave partilhada (PSK)**, tipo **12345**e, em seguida, selecione **OK**.
10. Sobre o **resumo** secção, selecione **OK**.

### <a name="create-a-virtual-machine-vm"></a>Criar uma máquina virtual (VM)

Para verificar a ligação VPN, crie duas VMs: um no Azure e um no Azure Stack. Depois de criar estas VMs, pode usá-los para enviar e receber dados através do túnel VPN.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Aceda a **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens de máquinas virtuais, selecione o **Windows Server 2016 Datacenter Eval** imagem.
4. Sobre o **Noções básicas** na secção **nome**, tipo **Azs-VM**.
5. Escreva um nome de utilizador válido e palavra-passe. Utilize esta conta para iniciar sessão na VM depois de criado.
6. Fornecer um **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
7. Sobre o **tamanho** secção, para esta instância, selecione um tamanho de máquina virtual e, em seguida, selecione **selecione**.
8. Sobre o **definições** secção, aceite as predefinições. Certifique-se de que o **Azs-VNet** está selecionada a rede virtual. Certifique-se de que a sub-rede está definida como **10.1.0.0/24**. Em seguida, selecione **OK**.
9. Sobre o **resumo** secção, reveja as definições e, em seguida, selecione * OK * *.

## <a name="test-the-connection"></a>Testar a ligação

Após é estabelecida a ligação de site a site, deve verificar-se de que pode obter dados a fluir em ambas as direções. É a maneira mais fácil para testar a ligação ao fazer um teste de ping:

* Inicie sessão para a máquina virtual que criou no Azure Stack e ping a máquina virtual no Azure.
* Inicie sessão para a máquina virtual criada no Azure e enviar um ping a máquina virtual no Azure Stack.

>[!NOTE]
>Para certificar-se de que está a enviar tráfego através da ligação de site a site, ping no endereço de Direct IP (DIP) da máquina virtual na sub-rede remota, não o VIP.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Entrar para a VM no Azure Stack de utilizador

1. Inicie sessão no portal do Azure Stack.
2. Na barra de navegação esquerdo, selecione **máquinas virtuais**.
3. Na lista de VMs, localize **Azs-VM** que criou anteriormente e, em seguida, selecioná-lo.
4. Na secção de para a máquina virtual, selecione **Connect**e, em seguida, abra o ficheiro de Azs-VM.rdp.

     ![Botão Ligar](media/azure-stack-connect-vpn/image17.png)

5. Inicie sessão com a conta que configurou quando criou a máquina virtual.
6. Abra uma linha elevada do Windows PowerShell.
7. Escreva **ipconfig /all**.
8. Na saída, localize a **endereço IPv4**e, em seguida, guarde o endereço para utilização posterior. Este é o endereço que deve enviar o ping do Azure. No ambiente de exemplo, o endereço está **10.1.0.4**, mas no seu ambiente poderá ser diferente. Ele deve coincidir com o **10.1.0.0/24** sub-rede que criou anteriormente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Inicie sessão no inquilino do VM no Azure

1. Inicie sessão no Portal do Azure.
2. Na barra de navegação esquerdo, selecione **máquinas virtuais**.
3. Na lista de máquinas virtuais, encontrar **VM do Azure** que criou anteriormente e, em seguida, selecioná-lo.
4. Na secção de para a máquina virtual, selecione **Connect**.
5. Inicie sessão com a conta que configurou quando criou a máquina virtual.
6. Abra uma **Windows PowerShell** janela.
7. Escreva **ipconfig /all**.
8. Deverá ver um endereço IPv4, que se encontrem dentro **10.100.0.0/24**. No ambiente de exemplo, o endereço está **10.100.0.4**, mas seu endereço poderá ser diferente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Da máquina virtual no Azure, enviar um ping a máquina virtual no Azure Stack, através do túnel. Para fazer isso, enviar pings para o DIP que registou da Azs-VM. No ambiente de exemplo, isto é **10.1.0.4**, mas não se esqueça de fazer ping no endereço que anotou no seu laboratório. Deverá ver um resultado semelhante à captura de ecrã seguinte:

    ![Ping enviado com êxito](media/azure-stack-connect-vpn/image19b.png)

11. Uma resposta de máquina virtual remota indica um teste com êxito. Pode fechar a janela de máquina virtual.

Também deve fazer a transferência de dados mais rigorosa testes; Por exemplo, copiar diferentes em tamanho normal ficheiros em ambas as direções.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Ver estatísticas de transferência de dados através da ligação de gateway

Se quiser saber a quantidade de dados passa por meio da sua ligação site a site, esta informação está disponível na **ligação** secção. Este teste também é outra forma de verificar que o ping que acabou de enviar foi efetivamente transmitido através da ligação VPN.

1. Enquanto tiver sessão iniciada máquina de virtual de utilizador no Azure Stack, utilize a sua conta de utilizador para iniciar sessão no portal de utilizador.
2. Aceda a **todos os recursos**e, em seguida, selecione a **Azs-Azure** ligação. **Ligações** aparece.
3. Na **ligação** secção, as estatísticas **dados no** e **dados de saída** aparecem. Na captura de ecrã seguinte, os números grandes são atribuídos a transferência de ficheiros adicionais. Deverá ver alguns valores diferentes de zero aí.

    ![Entrada e saída de dados](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Passos Seguintes

* [Implementar aplicações no Azure e o Azure Stack](azure-stack-solution-pipeline.md)
