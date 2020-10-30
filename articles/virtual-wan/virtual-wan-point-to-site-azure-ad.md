---
title: 'Configure autenticação Ad AD para ligação VPN do utilizador: WAN virtual'
description: Saiba como configurar a autenticação do Azure Ative Directory para a VPN do Utilizador.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alzam
ms.openlocfilehash: 9cc68eb60096c4431acfc988c87ca9bf99f1f045
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043395"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Configurar autenticação do Diretório Ativo Azure para VPN do utilizador

Este artigo mostra-lhe como configurar a autenticação Azure AD para o Utilizador VPN em SÉRIE Virtual para ligar aos seus recursos em Azure sobre uma ligação OpenVPN VPN. A autenticação do Azure Ative Directory só está disponível para gateways utilizando o protocolo OpenVPN e clientes que executam o Windows.

Este tipo de ligação exige a configuração de um cliente no computador cliente. Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).

Neste artigo, vai aprender a:

* Criar um WAN virtual
* Criar um Hub Virtual
* Criar uma configuração VPN do utilizador
* Faça o download de um perfil VPN do utilizador VIRTUAL WAN
* Aplicar a configuração VPN do utilizador num Hub Virtual
* Ligue um VNet a um Hub Virtual
* Descarregue e aplique a configuração do cliente VPN do utilizador
* Veja o seu WAN Virtual

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Tem uma rede virtual a que pretende ligar. Verifique se nenhuma das sub-redes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [Quickstart](../virtual-network/quick-create-portal.md).

* A sua rede virtual não tem quaisquer portas de rede virtuais. Se a sua rede virtual tiver um gateway (VPN ou ExpressRoute), deve remover todos os gateways. Esta configuração requer que as redes virtuais estejam ligadas ao gateway do hub Virtual WAN.

* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual que é criada e usada pela Virtual WAN. O intervalo de endereços que especifica para o hub não pode sobrepor-se a nenhuma das suas redes virtuais existentes a que se conecta. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração da rede no local, coordene com alguém que possa fornecer esses detalhes para si.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar um WAN virtual

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue para a página Virtual WAN. No portal, clique em **Criar um recurso** . Digite **WAN virtual** na caixa de pesquisa e selecione Enter.
2. Selecione **VIRTUAL WAN** a partir dos resultados. Na página VIRTUAL WAN, clique em **Criar** para abrir a página Create WAN.
3. Na página **Create WAN,** no separador **Básicos,** preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Grupo de recursos** - Criar novo ou utilizar existente.
   * **Localização do grupo de recursos** - Escolha uma localização de recurso a partir do dropdown. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** - Digite o nome que pretende chamar de WAN.
   * **Tipo:** O padrão. Se criar um WAN básico, pode criar apenas um hub Básico. Os centros básicos são capazes apenas de conectividade local-a-local VPN.
4. Depois de terminar de preencher os campos, selecione **Review +Create** .
5. Após os passes de validação, **selecione Criar** para criar o WAN virtual.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Criar um centro virtual vazio

1. No seu WAN Virtual, selecione Hubs e clique **em +New Hub** .

   ![A screenshot mostra a caixa de diálogo de configuração hubs com o Novo Hub selecionado.](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Na página do hub virtual de criar, preencha os seguintes campos.

   **Região** - Selecione a região em que pretende implantar o hub virtual.

   **Nome** - Introduza o nome que pretende chamar ao seu centro virtual.

   **Espaço de endereço privado hub** - A gama de endereços do hub na notação CIDR.

   ![A screenshot mostra o painel de hub virtual Create onde pode introduzir valores.](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Clique em **Rever + criar** .
4. Na página **de validação passada,** clique em **criar** .

## <a name="create-a-new-user-vpn-configuration"></a><a name="site"></a>Criar uma nova configuração VPN do utilizador

Uma configuração VPN do utilizador define os parâmetros para ligar clientes remotos.

1. No âmbito do seu WAN virtual, selecione **as configurações VPN do utilizador** .

   ![A screenshot mostra o item do menu de configurações do Utilizador V P N selecionado.](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. clique **em +Criar config VPN do utilizador.**

   ![A screenshot mostra a ligação configurar o utilizador V P N config.](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Introduza a informação e clique em **Criar** .

   * **Nome de configuração** - Introduza o nome que pretende chamar de Configuração VPN do utilizador.
   * **Tipo de túnel** - Selecione OpenVPN.
   * **Método de autenticação** - Selecione Azure Ative Directory.
   * **Público** - Digite o ID de aplicação da Aplicação [VPN Azure](openvpn-azure-ad-tenant.md) registada no seu inquilino Azure AD. 
   * **Emitente** - `https://sts.windows.net/<your Directory ID>/`
   * **Inquilino da AAD** - `https://login.microsoftonline.com/<your Directory ID>`
  
   ![A screenshot mostra o novo painel de configuração do utilizador V P N onde pode introduzir os valores.](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Editar atribuição de hub

1. Navegue para a lâmina **hubs** sob o WAN virtual.
2. Selecione o hub ao que pretende associar a configuração do servidor VPN e clique na elipse (...).

   ![Screenshot mostra Editar o hub virtual selecionado a partir do menu.](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Clique **em Editar o hub virtual.**
4. Verifique a caixa de verificação **de gateway ponto-a-local** e escolha a **unidade de escala Gateway** que deseja.

   ![A screenshot mostra a caixa de diálogo do hub virtual Editar onde pode selecionar a sua unidade de escala Gateway.](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Insira o **pool** address a partir do qual os clientes VPN serão atribuídos endereços IP.
6. Clique em **Confirmar** .
7. A operação pode demorar até 30 minutos para ser concluída.

## <a name="download-user-vpn-profile"></a><a name="device"></a>Baixar perfil VPN do utilizador

Utilize o perfil VPN para configurar os seus clientes.

1. Na página para o seu WAN Virtual, clique nas **configurações VPN do utilizador** .
2. No topo da página, clique **em Download user VPN config** .
3. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.
4. Utilize o ficheiro de perfil para configurar os clientes VPN.

## <a name="configure-user-vpn-clients"></a>Configure clientes VPN de utilizador

Para se conectar, precisa de descarregar o Cliente Azure VPN e importar o perfil de cliente VPN que foi descarregado nos passos anteriores em todos os computadores que queiram ligar ao VNet.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para &reg; ligações de protocolo OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente Azure VPN

Utilize este [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) para descarregar o Cliente Azure VPN.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Importar um perfil de cliente

1. Na página, selecione **Import** .

    ![Screenshot mostra Import selecionado a partir do menu plus.](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Navegue no ficheiro xml do perfil e selecione-o. Com o ficheiro selecionado, selecione **Abrir** .

    ![A screenshot mostra uma caixa de diálogo aberta onde pode selecionar um ficheiro.](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Especifique o nome do perfil e **selecione Guardar** .

    ![A screenshot mostra o nome de ligação adicionado e o botão Guardar selecionado.](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecione **Connect** para ligar à VPN.

    ![A screenshot mostra o botão 'Ligar' para a ligação que acabaste de criar.](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Uma vez ligado, o ícone ficará verde e dirá **Conectado** .

    ![A screenshot mostra a ligação num estado ligado com a opção de desligar.](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para eliminar um perfil de cliente

1. Selecione a elipse (...) ao lado do perfil do cliente que pretende eliminar. Em seguida, **selecione Remover** .

    ![Screenshot mostra Remover selecionado do menu.](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecione **Remover** para eliminar.

    ![A screenshot mostra uma caixa de diálogo de confirmação com a opção de Remover ou Cancelar.](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de ligação, pode utilizar a ferramenta **Diagnosticar.** Selecione a elipse (...) ao lado da ligação VPN que pretende diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar** .

    ![A screenshot mostra o Diagnóstico selecionado no menu.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na página Propriedades de **Ligação,** selecione **'Executar Diagnóstico'.**

    ![A screenshot mostra o botão de diagnóstico de execução para uma ligação.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Inscreva-se com as suas credenciais.

    ![A screenshot mostra o Sinal na caixa de diálogo para esta ação.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Veja os resultados do diagnóstico.

    ![A imagem mostra os resultados do diagnóstico.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Ver a WAN Virtual

1. Navegue para a WAN virtual.
2. Na página Overview, cada ponto no mapa representa um hub.
3. Na secção Hubs e ligações, pode ver o estado do hub, o site, a região, o estado da ligação VPN e os bytes de entrada e saída.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando já não precisar destes recursos, pode utilizar [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
