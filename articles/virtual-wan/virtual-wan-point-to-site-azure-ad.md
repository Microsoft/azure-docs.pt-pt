---
title: Configurar a autenticação do Azure AD para a conexão ponto a site com o Azure | Microsoft Docs
description: Neste tutorial, saiba como configurar a autenticação de Azure Active Directory para VPN de usuário.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: alzam
ms.openlocfilehash: 19aa029311584b5a9762691d24ed10c1666a032c
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782432"
---
# <a name="tutorial-create-a-user-vpn-connection-by-using-azure-virtual-wan"></a>Tutorial: criar uma conexão VPN de usuário usando a WAN virtual do Azure

Este tutorial mostra como configurar a autenticação do Azure AD para VPN de usuário na WAN virtual para se conectar aos seus recursos no Azure por meio de uma conexão VPN OpenVPN. A autenticação Azure Active Directory só está disponível para gateways usando o protocolo OpenVPN e os clientes que executam o Windows.

Este tipo de ligação exige a configuração de um cliente no computador cliente. Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar um hub
> * Criar uma configuração P2S
> * Baixar um perfil de cliente VPN
> * Aplicar a configuração P2S a um hub
> * Ligar uma VNet a um hub
> * Transferir e aplicar a configuração do cliente VPN
> * Ver a WAN Virtual
> * Ver o estados de funcionamento dos recursos

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes de suas redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o guia de [início rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem nenhum gateway de rede virtual. Se sua rede virtual tiver um gateway (VPN ou ExpressRoute), você deverá remover todos os gateways. Essa configuração requer que as redes virtuais estejam conectadas ao gateway do Hub WAN virtual.

* Obtenha um intervalo de endereços IP para a região do seu hub. O Hub é uma rede virtual que é criada e usada pela WAN virtual. O intervalo de endereços especificado para o Hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se você não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, coordene com alguém que possa fornecer esses detalhes para você.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="wan"></a>Criar uma WAN virtual

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue até a página WAN virtual. No portal, clique em **Criar um recurso**. Digite **Wan virtual** na caixa de pesquisa e selecione Enter.
2. Selecione **Wan virtual** nos resultados. Na página WAN virtual, clique em **criar** para abrir a página Criar Wan.
3. Na página **criar Wan** , na guia **noções básicas** , preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Grupo de recursos** -criar novo ou usar existente.
   * **Local do grupo de recursos** -escolha um local de recurso no menu suspenso. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** -digite o nome que você deseja chamar para sua Wan.
   * **Tipo:** Standardization. Se você criar uma WAN básica, poderá criar apenas um hub básico. Os hubs básicos são capazes de somente conectividade VPN site a site.
4. Depois de concluir o preenchimento dos campos, selecione **revisar + criar**.
5. Depois que a validação for aprovada, selecione **criar** para criar a WAN virtual.

## <a name="site"></a>Criar um hub virtual vazio

1. Em sua WAN virtual, selecione hubs e clique em **+ novo hub**.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Na página Criar Hub virtual, preencha os campos a seguir.

   **Região** – selecione a região em que você deseja implantar o Hub virtual.

   **Nome** – Insira o nome que você deseja chamar para o seu hub virtual.

   **Espaço de endereço privado do Hub** -o intervalo de endereços do Hub na notação CIDR.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Clique em **Rever + criar**.
4. Na página **validação aprovada** , clique em **criar**.

## <a name="site"></a>Criar uma nova configuração do P2S

Uma configuração P2S define os parâmetros para ligar clientes remotos.

1. Defina as variáveis a seguir, substituindo os valores conforme necessário para o seu ambiente.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Execute os seguintes comandos para criar a configuração:

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

## <a name="hub"></a>Editar atribuição de Hub

1. Navegue até a folha **hubs** na WAN virtual.
2. Selecione o Hub ao qual você deseja associar a configuração do servidor VPN e clique nas reticências (...).

   ![novo site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Clique em **Editar Hub virtual**.
4. Marque a caixa de seleção **incluir gateway de ponto a site** e escolha a **unidade de escala do gateway** desejada.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Insira o **pool de endereços** do qual serão atribuídos endereços IP aos clientes VPN.
6. Clique em **Confirmar**.
7. A operação pode levar até 30 minutos para ser concluída.

## <a name="device"></a>Baixar perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

1. Na página de sua WAN virtual, clique em **configurações de VPN de usuário**.
2. Na parte superior da página, clique em **baixar configuração de VPN do usuário**.
3. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.
4. Use o arquivo de perfil para configurar os clientes VPN.

## <a name="configure-user-vpn-clients"></a>Configurar clientes VPN de usuário

Para se conectar, você precisa baixar o cliente VPN do Azure (versão prévia) e importar o perfil de cliente VPN que foi baixado nas etapas anteriores em cada computador que deseja se conectar à VNet.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para conexões de protocolo OpenVPN®.
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente VPN do Azure

Use este [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) para baixar o cliente VPN do Azure (versão prévia).

#### <a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **importar**.

    ![importe](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **abrir**.

    ![importe](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Especifique o nome do perfil e selecione **salvar**.

    ![importe](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecione **conectar** para conectar-se à VPN.

    ![importe](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e diria **conectado**.

    ![importe](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as reticências (...) ao lado do perfil do cliente que você deseja excluir. Em seguida, selecione **remover**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecione **remover** para excluir.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta de **diagnóstico** . Selecione as reticências (...) ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **diagnosticar**.

    ![tect](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na página **Propriedades da conexão** , selecione **Executar diagnóstico**.

    ![tect](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![tect](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![tect](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Exibir sua WAN virtual

1. Navegue para a WAN virtual.
2. Na página Overview, cada ponto no mapa representa um hub. Coloque o cursor sobre um ponto para ver o resumo do estado de funcionamento do hub.
3. Na secção Hubs e ligações, pode ver o estado do hub, o site, a região, o estado da ligação VPN e os bytes de entrada e saída.

## <a name="viewhealth"></a>Exibir o Resource Health

1. Navegue para a WAN.
2. Na página WAN, na secção **SUPPORT + Troubleshooting** (SUPORTE + Resolução de Problemas), clique em **Health** (Estado de funcionamento) e veja o recurso.


## <a name="cleanup"></a>Limpar recursos

Quando já não precisar desses recursos, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que o mesmo contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
