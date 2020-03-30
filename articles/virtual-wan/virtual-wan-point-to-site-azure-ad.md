---
title: 'Configure Autenticação AD Azure para ligação VPN do utilizador: Wan Virtual'
description: Saiba como configurar a autenticação do Diretório Ativo Azure para o Utilizador VPN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: 703b832d58f2374eac131cfd380ba27f2c890618
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059490"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Configure Autenticação de Diretório Ativo Do Azure para VPN do utilizador

Este artigo mostra-lhe como configurar a autenticação AD Azure para o Utilizador VPN em Wan Virtual ligar-se aos seus recursos em Azure sobre uma ligação VPN OpenVPN. A autenticação do Diretório Ativo Azure só está disponível para gateways utilizando o protocolo OpenVPN e os clientes que executam o Windows.

Este tipo de ligação exige a configuração de um cliente no computador cliente. Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar um hub
> * Criar uma configuração P2S
> * Descarregue um perfil de cliente VPN
> * Aplicar a configuração P2S a um hub
> * Ligar uma VNet a um hub
> * Transferir e aplicar a configuração do cliente VPN
> * Ver a WAN Virtual

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Tem uma rede virtual a que se quer ligar. Verifique se nenhuma das subredes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [Quickstart](../virtual-network/quick-create-portal.md).

* A sua rede virtual não dispõe de gateways de rede virtuais. Se a sua rede virtual tiver um portal (VPN ou ExpressRoute), deve remover todos os gateways. Esta configuração requer que as redes virtuais estejam ligadas ao portal virtual WAN hub.

* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual que é criada e usada pela Virtual WAN. O intervalo de endereços que especifica para o hub não pode sobrepor-se a nenhuma das suas redes virtuais existentes a que se liga. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se não estiver familiarizado com as gamas de endereços IP localizadas na configuração da sua rede no local, coordene com alguém que possa fornecer esses detalhes para si.

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar uma WAN Virtual

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue para a página Virtual WAN. No portal, clique em **Criar um recurso**. Digite o **WAN virtual** na caixa de pesquisa e selecione Enter.
2. Selecione **Virtual WAN** a partir dos resultados. Na página WAN Virtual, clique em **Criar** para abrir a página Create WAN.
3. Na página **Create WAN,** no separador **Basics,** preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Grupo de recursos** - Criar novos ou utilizar os existentes.
   * **Localização** do grupo de recursos - Escolha uma localização de recursos a partir da queda. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** - Digite o nome a que pretende chamar wan.
   * **Tipo:** Padrão. Se criar um WAN básico, só pode criar um centro básico. Os centros básicos são capazes apenas de conectividade vpn site-to-site.
4. Depois de terminar de preencher os campos, selecione **Review +Create**.
5. Depois de passar os passes de validação, selecione **Criar** para criar o WAN virtual.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Criar um centro virtual vazio

1. Sob o seu WAN virtual, selecione Hubs e clique em **+New Hub**.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Na página de criar hub virtual, preencha os seguintes campos.

   **Região** - Selecione a região onde pretende implantar o centro virtual.

   **Nome** - Introduza o nome a que pretende chamar o seu centro virtual.

   Espaço de **endereço sino privado** - O endereço do hub na notação CIDR.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Clique em **Rever + criar**.
4. Na página **de validação passada,** clique **em criar**.

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>Criar uma nova configuração P2S

Uma configuração P2S define os parâmetros para ligar clientes remotos.

1. Sob o seu WAN virtual, selecione **configurações VPN do utilizador**.

   ![novo config](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. clique +Criar o **utilizador VPN config**.

   ![novo config](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Introduza a informação e clique em **Criar**

   ![novo config](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Editar atribuição de hub

1. Navegue para a lâmina **Hubs** sob o WAN virtual.
2. Selecione o centro a que pretende associar a configuração do servidor vpn e clique na elipse (...).

   ![novo site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Clique em **Editar o centro virtual**.
4. Verifique a caixa de verificação de **gateway ponto-a-local** e escolha a **unidade de escala Gateway** que deseja.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Introduza o **pool de Endereços** a partir do qual os clientes VPN serão atribuídos endereços IP.
6. Clique em **Confirmar**.
7. A operação pode demorar até 30 minutos para ser concluída.

## <a name="download-vpn-profile"></a><a name="device"></a>Transferir perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

1. Na página para o seu WAN virtual, clique nas **configurações VPN do utilizador**.
2. No topo da página, clique em **Baixar o utilizador VPN config**.
3. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.
4. Utilize o ficheiro de perfil para configurar os clientes VPN.

## <a name="configure-user-vpn-clients"></a>Configure os clientes VPN do utilizador

Para se conectar, precisa de descarregar o Cliente VpN Azure e importar o perfil de cliente VPN que foi descarregado nos passos anteriores em todos os computadores que queiram ligar-se ao VNet.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para ligações protocolares OpenVPN.&reg;
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente Azure VPN

Utilize este [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) para descarregar o Cliente VpN Azure.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Import**.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Navegue no ficheiro xml de perfil e selecione-o. Com o ficheiro selecionado, selecione **Open**.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Guardar**.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecione **Ligar** para ligar à VPN.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Uma vez ligado, o ícone ficará verde e dirá **Connected**.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para eliminar um perfil de cliente

1. Selecione a elipse (...) junto ao perfil do cliente que pretende eliminar. Em seguida, selecione **Remover**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecione **Remover** para eliminar.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de ligação, pode utilizar a ferramenta **Diagnosticar.** Selecione a elipse (...) junto à ligação VPN que pretende diagnosticar para revelar o menu. Em seguida, **selecione Diagnosticar**.

    ![diagnosticar](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na página Propriedades de **Ligação,** selecione **Diagnóstico de Execução**.

    ![diagnosticar](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Inscreva-se com as suas credenciais.

    ![diagnosticar](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Veja os resultados do diagnóstico.

    ![diagnosticar](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Ver a WAN Virtual

1. Navegue para a WAN virtual.
2. Na página Overview, cada ponto no mapa representa um hub.
3. Na secção Hubs e ligações, pode ver o estado do hub, o site, a região, o estado da ligação VPN e os bytes de entrada e saída.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando já não precisar desses recursos, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que o mesmo contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
