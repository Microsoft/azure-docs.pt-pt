---
title: 'Ligar a rede no local a uma rede virtual do Azure: Rede de VPNs (clássico): Portal | Microsoft Docs'
description: Crie uma ligação IPsec da sua rede no local para uma rede virtual do Azure clássica através da Internet pública.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/11/2020
ms.author: cherylmc
ms.openlocfilehash: 1f096993645aca6999667af88c91d3f55f79d914
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983058"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Criar uma ligação de Site a Site com o portal do Azure (clássico)


Este artigo mostra-lhe como utilizar o portal do Azure para criar uma ligação de gateway de VPN de Site para Site a partir da sua rede no local para a VNet. Os passos deste artigo aplicam-se ao modelo clássico de implementação e não se aplicam ao modelo de implementação atual, Gestor de Recursos. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para obter mais informações sobre o gateways de VPN, veja [About VPN gateway (Acerca do gateway de VPN)](vpn-gateway-about-vpngateways.md).

![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Before you begin

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Confirme que pretende trabalhar com o modelo de implementação clássica. Se quiser trabalhar com o modelo de implementação Resource Manager, veja [Criar uma ligação Site a Site (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Quando possível, recomendamos que utilize o modelo de implementação Resource Manager.
* Certifique-se de que tem um dispositivo VPN compatível e alguém que o possa configurar. Para obter mais informações sobre os dispositivos VPN compatíveis e a configuração do dispositivo, consulte [About VPN Devices (Acerca dos Dispositivos VPN)](vpn-gateway-about-vpn-devices.md).
* Verifique se tem um endereço IP IPv4 público com acesso exterior para o seu dispositivo VPN.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes. Ao criar esta configuração, tem de especificar prefixos de intervalo de endereços IP que o Azure irá encaminhar para a sua localização no local. Nenhuma das sub-redes da rede local pode sobrepor as sub-redes de rede virtual a que pretende ligar.
* O PowerShell é necessário para especificar a chave partilhada e criar a ligação de gateway VPN. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Valores de configuração de exemplo para este exercício

Os exemplos neste artigo utilizam os seguintes valores. Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

* **Nome VNet:** TestVNet1
* **Espaço de Endereços:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (opcional para este exercício)
* **Sub-redes:**
  * Front-End: 10.11.0.0/24
  * Back-End: 10.12.0.0/24 (opcional para este exercício)
* **GatewaySubnet:** 10.11.255.0/27
* **Grupo de Recursos:** TestRG1
* **Localização:** Leste dos EUA
* **Servidor DNS:** 10.11.0.3 (opcional para este exercício)
* **Nome do site local:** Site2
* **Espaço de endereços de cliente:** o espaço de endereços que se encontra no seu site no local.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Criar uma rede virtual

Quando cria uma rede virtual para utilizar numa ligação de S2S, tem de certificar-se de que os espaços de endereços que especificar não se sobrepõem a nenhum dos espaços de endereços do cliente para os sites locais aos quais pretende ligar. Se tiver sub-redes sobrepostas, a ligação não funcionará corretamente.

* Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. 

* Se ainda não tiver uma rede virtual, crie uma. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

1. Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique **+Criar um recurso.* No campo **Procurar no Marketplace**, escreva "Rede Virtual". Localizar **a Rede Virtual** a partir da lista de retornados e clicar para abrir a página **'Rede Virtual'.**
3. clique **(alterar para Clássico)** e, em seguida, clique em **Criar**.
4. Na página **Criar rede virtual (clássica)**, configure as definições da VNet. Nesta página, deve adicionar o seu primeiro espaço de endereços e um único intervalo de endereços de sub-rede. Depois de criar o VNet, pode voltar e adicionar sub-redes adicionais e espaços de endereço.

   ![Página Criar rede virtual](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Página Criar rede virtual")
5. Verifique se a **Subscrição** está correta. Pode utilizar o menu pendente para mudar de subscrição.
6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo grupo ao escrever um nome para o mesmo. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Azure Resource Manager](../azure-resource-manager/management/overview.md#resource-groups).
7. Em seguida, selecione as definições **Localização** para a sua VNet. A localização determina onde é que os recursos que implementar nesta VNet vão residir.
8. Clique em **Criar** para criar a VNet.
9. Depois de clicar em "Criar", é apresentado um mosaico no dashboard que reflete o progresso da sua VNet. O mosaico muda enquanto a VNet está a ser criada.

## <a name="2-add-additional-address-space"></a><a name="additionaladdress"></a>2. Adicionar espaço adicional de endereço

Depois de criar a rede virtual, pode adicionar espaço de endereços adicional. A adição de espaço de endereços adicional não é uma parte obrigatória de uma configuração de S2S, mas se precisar de vários espaços de endereços, utilize os seguintes passos:

1. Localize a rede virtual no portal.
2. Na página da sua rede virtual, na secção **Definições**, clique em **Espaço de endereços**.
3. Na página Espaço de endereços, clique em **+Adicionar** e introduza o espaço de endereços adicional.

## <a name="3-specify-a-dns-server"></a><a name="dns"></a>3. Especifique um servidor DNS

As definições de DNS não são uma parte obrigatória de uma configuração de S2S, mas o DNS é necessário se quiser resolução de nomes. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS que especificar deve ser um servidor DNS que possa resolver os nomes dos recursos a que se está a ligar. Nas definições de exemplo, utilizámos um endereço IP privado. O endereço IP que utilizamos provavelmente não é o endereço IP do seu servidor DNS. Certifique-se de que utiliza os seus próprios valores.

Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS, para lidar com a resolução de nomes. Abra as definições da rede virtual, clique em Servidores DNS e adicione o endereço IP do servidor DNS que quer utilizar para a resolução de nomes.

1. Localize a rede virtual no portal.
2. Na página da rede virtual, na secção **Definições**, clique em **Servidores DNS**.
3. Adicione um servidor DNS.
4. Para guardar as suas definições, clique em **Guardar** no início da página.

## <a name="4-configure-the-local-site"></a><a name="localsite"></a>4. Configure o site local

O site local refere-se, normalmente, à sua localização no local. Contém o endereço IP do dispositivo VPN para o qual irá criar uma ligação e os intervalos de endereços IP que serão encaminhados através do gateway de VPN para o dispositivo VPN.

1. Na página do seu VNet, em **Definições,** clique no **Diagrama**.
1. Na página de **ligações VPN,** clique em **Não ter nenhuma ligação VPN existente. Clique aqui para começar.**
1. Para **o tipo de ligação,** deixe o **Site-a-site** selecionado.
4. Clique em **Site local - Configurar definições necessárias** para abrir a página **Site local**. Configure as definições e, em seguida, clique em **OK** para guardar as definições.
   - **Nome:** crie um nome para o seu site local para que o consiga identificar facilmente.
   - **Endereço IP do gateway de VPN:** este é o endereço IP público do dispositivo VPN para a rede no local. O dispositivo VPN requer um endereço IP público IPv4. Especifique um endereço IP público válido para o dispositivo VPN ao qual pretende ligar. Deve ser acessível por Azure. Se não souber o endereço IP do seu dispositivo VPN, pode sempre colocar um valor de marcador de posição (desde que esteja no formato de um endereço IP público válido) e, em seguida, alterá-lo mais tarde.
   - **Espaço de endereços de cliente:** lista os intervalos de endereços IP que pretende que sejam encaminhados para a rede no local através deste gateway. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especifica aqui não se sobrepõem aos intervalos de outras redes às quais a rede virtual se liga ou aos intervalos de endereços da própria rede virtual.

   ![Site local](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Configurar site local")

Clique **em OK** para fechar a página do site local. **Não clique em OK para fechar a nova página de Ligação VPN**.

## <a name="5-configure-the-gateway-subnet"></a><a name="gatewaysubnet"></a>5. Configurar a sub-rede gateway

Tem de criar uma sub-rede de gateway para o gateway de VPN. A sub-rede do gateway contém os endereços IP que os serviços do gateway de VPN utilizam.


1. Na página **Nova Ligação VPN**, selecione a caixa de verificação **Criar gateway de imediato**. É apresentada a página "Configuração de gateway opcional". Se não selecionar a caixa de verificação, não verá a página para configurar a sub-rede do gateway.

   ![Configuração gateway - Subnet, tamanho, tipo de encaminhamento](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Configuração gateway - Subnet, tamanho, tipo de encaminhamento")
2. Para abrir a página **Configuração do gateway**, clique em **Configuração de gateway opcional - Sub-rede, tamanho e tipo de encaminhamento**.
3. Na página **Configuração do Gateway**, clique em **Sub-rede - Configurar definições necessárias** para abrir a página **Adicionar sub-rede**. Quando terminar de configurar estas definições, clique **em OK**.

   ![Configuração gateway - sub-rede gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Configuração gateway - sub-rede gateway")
4. Na página **Adicionar sub-rede**, adicione a sub-rede do gateway. O tamanho da sub-rede de gateway que especificar depende da configuração do gateway de VPN que pretende criar. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que utilize /27 ou /28. Desta forma, estará a criar uma sub-rede maior que inclui mais endereços. Utilizar uma sub-rede de gateway maior permite que os endereços IP suficientes suportem possíveis configurações futuras.

   ![Adicionar sub-rede gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Adicionar sub-rede gateway")

## <a name="6-specify-the-sku-and-vpn-type"></a><a name="sku"></a>6. Especificar o tipo SKU e VPN

1. Selecione o **Tamanho** do gateway. Este é o SKU de gateway que utiliza para criar o gateway de rede virtual. Os gateways de VPN clássicos utilizam os SKUs de gateway antigo (legados). Para obter mais informações sobre os SKU de gateway legados, veja [Trabalhar com SKUs de gateway de rede virtual (antigos)](vpn-gateway-about-skus-legacy.md).

   ![Selecione tipo SKUL e VPN](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Selecione tipo SKU e VPN")
2. Selecione o **Tipo de Encaminhamento** do gateway. Também é conhecido como o tipo de VPN. É importante selecionar o tipo correto porque não é possível converter o gateway de um tipo para outro. O dispositivo VPN tem de ser compatível com o tipo de encaminhamento que selecionar. Para obter mais informações sobre o Tipo de Encaminhamento, consulte [sobre as definições do gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Poderá ver artigos que fazem referência aos tipos de VPN 'RouteBased' e 'PolicyBased'. 'Dynamic' corresponde a 'RouteBased' e 'Static' corresponde a 'PolicyBased'.
3. Clique em **OK** para guardar as definições.
4. Na página **Nova Ligação VPN,** clique em **OK** na parte inferior da página para começar a implantar o seu gateway de rede virtual. Dependendo do SKU que selecionar, a criação de um gateway de rede virtual pode demorar até 45 minutos.

## <a name="7-configure-your-vpn-device"></a><a name="vpndevice"></a>7. Configurar o seu dispositivo VPN

As ligações de Site a Site para uma rede no local requerem um dispositivo VPN. Neste passo, configure o seu dispositivo VPN. Quando configurar o dispositivo VPN, irá precisar do seguinte:

- Uma chave partilhada. Esta é a mesma chave partilhada que especifica ao criar a ligação VPN de Site a Site. Nos nossos exemplos, iremos utilizar uma chave partilhada básica. Deve gerar uma chave mais complexa para utilizar.
- O endereço IP Público do gateway de rede virtual. Pode ver o endereço IP público através do portal do Azure, do PowerShell ou da CLI.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="8-create-the-connection"></a><a name="CreateConnection"></a>8. Criar a ligação
Neste passo, vai definir a chave partilhada e criar a ligação. A chave que definir tem de ser a mesma chave que foi utilizada na configuração do seu dispositivo VPN.

> [!NOTE]
> Atualmente, este passo não está disponível no portal do Azure. Terá de utilizar a versão da Gestão de Serviço (SM) dos cmdlets do Azure PowerShell. Ver [Antes de começar para](#before) obter informações sobre a instalação destes cmdlets.
>

### <a name="step-1-connect-to-your-azure-account"></a>Passo 1. Ligar à sua conta do Azure

Tem de executar estes comandos localmente utilizando o módulo de gestão de serviços PowerShell. 

1. Abra a sua consola PowerShell com direitos elevados. Para mudar para a gestão de serviços, utilize este comando:

   ```powershell
   azure config mode asm
   ```
2. Ligar à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Add-AzureAccount
   ```
3. Verifique as subscrições da conta.

   ```powershell
   Get-AzureSubscription
   ```
4. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Passo 2. Definir a chave partilhada e criar a ligação

Quando cria um VNet clássico no portal (não utilizando o PowerShell), o Azure adiciona o nome do grupo de recursos ao nome curto. Por exemplo, de acordo com a Azure, o nome do VNet que criou para este exercício é "Group TestRG1 TestVNet1", e não "TestVNet1". PowerShell requer o nome completo da rede virtual, não o nome curto que aparece no portal. O nome longo não é visível no portal. Os seguintes passos ajudam-no a exportar o ficheiro de configuração da rede para obter os valores exatos para o nome da rede virtual. 

1. Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Abra o ficheiro de configuração de rede com um editor de xml e verifique os valores de 'LocalNetworkSite name' e 'VirtualNetworkSite name'. Modifique o exemplo para este exercício para refletir os valores no xml. Ao especificar um nome que contenha espaços, utilize plicas à volta do valor.

3. Defina a chave partilhada e crie a ligação. '-SharedKey' é um valor que gera e especifica. No exemplo, utilizámos 'abc123', mas pode gerar e (deve) utilizar algo mais complexo. O importante é que o valor que especificar aqui seja igual ao valor que especificou quando configurou o seu dispositivo VPN.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   Quando a ligação é criada, o resultado é: **Estado: Com êxito**.

## <a name="9-verify-your-connection"></a><a name="verify"></a>9. Verifique a sua ligação

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Se estiver a ter problemas em ligar, veja a secção **Resolver problemas** do índice no painel esquerdo.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Como repor um gateway de VPN

Repor o gateway de VPN do Azure é útil se perder a conectividade VPN em vários locais num ou mais túneis de rede de VPNs. Nesta situação, os dispositivos VPN no local estão todos a funcionar corretamente, mas não podem estabelecer túneis IPsec com os gateways de VPN do Azure. Para obter os passos, veja [Reset a VPN gateway](vpn-gateway-resetgw-classic.md#resetclassic) (Repor um gateway de VPN).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Como alterar um SKU de gateway

Para obter os passos para alterar um SKU de gateway, veja [Redimensionar um SKU de gateway](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Próximos passos

* Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/).
* Para obter informações sobre o Túnel Forçado, veja [Acerca do Túnel Forçado](vpn-gateway-about-forced-tunneling.md).
