---
title: 'Virtual WAN: Inquilino AD AD para diferentes grupos de utilizadores: Autenticação AD AZure'
description: Crie um inquilino AZure AD para a autenticação P2S OpenVPN e crie e registe várias aplicações em Azure AD para permitir diferentes acessos para diferentes utilizadores e grupos.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e25ef7f55492be4ee491b9ebbbef4aa1eb03c80b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898092"
---
# <a name="create-an-azure-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um inquilino de Diretório Ativo (AD) para ligações de protocolo P2S OpenVPN

Ao ligar-se ao seu VNet, pode utilizar a autenticação baseada em certificados ou a autenticação RADIUS. No entanto, quando utilizar o protocolo Open VPN, também pode utilizar a autenticação do Azure Ative Directory. Se quiser que um conjunto diferente de utilizadores seja capaz de se conectar a diferentes gateways, pode registar várias aplicações em AD e ligá-las a diferentes gateways.

Este artigo ajuda a configurar um inquilino Azure AD para a autenticação P2S OpenVPN, e criar e registar várias aplicações em Azure AD para permitir um acesso diferente para diferentes utilizadores e grupos.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para &reg; ligações de protocolo OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Criar uma nova configuração P2S

Uma configuração P2S define os parâmetros para ligar clientes remotos.

1. Desaprova as seguintes variáveis, substituindo os valores necessários para o seu ambiente.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Executar os seguintes comandos para criar a configuração:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Não utilize o ID de aplicação do cliente Azure VPN nos comandos acima: Irá permitir a todos os utilizadores o acesso ao gateway. Utilize o ID das aplicações que registou.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Atribuição do hub de edição

1. Navegue para a lâmina **hubs** sob o WAN virtual.

2. Selecione o hub ao que pretende associar a configuração do servidor VPN e clique na elipse (...).

    ![Screenshot mostra Editar o hub virtual selecionado a partir do menu.](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Clique **em Editar o hub virtual.**

4. Verifique a caixa de verificação **de gateway ponto-a-local** e escolha a **unidade de escala Gateway** que deseja.

    ![A screenshot mostra a caixa de diálogo do hub virtual Editar onde pode selecionar a sua unidade de escala Gateway.](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Insira o **pool** address a partir do qual os clientes VPN serão atribuídos endereços IP.

6. Clique em **Confirmar**.

7. A operação pode demorar até 30 minutos para ser concluída.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Baixar perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

1. Na página para o seu WAN virtual, clique nas **configurações VPN do utilizador**.

2. No topo da página, clique **em Download user VPN config**.

3. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.

4. Utilize o ficheiro de perfil para configurar os clientes VPN.

5. Extraia o ficheiro zip descarregado.

6. Navegue na pasta "AzureVPN" desapertada.

7. Tome nota da localização do ficheiro "azurevpnconfig.xml". O azurevpnconfig.xml contém a definição para a ligação VPN e pode ser importado diretamente para a aplicação do Cliente Azure VPN. Também pode distribuir este ficheiro a todos os utilizadores que necessitem de se conectar por e-mail ou outros meios. O utilizador necessitará de credenciais AZure AD válidas para se conectar com sucesso.

## <a name="9-configure-user-vpn-clients"></a>9. Configurar clientes VPN de utilizador

Para se conectar, precisa de descarregar o Cliente Azure VPN e importar o perfil de cliente VPN que foi descarregado nos passos anteriores em todos os computadores que queiram ligar ao VNet.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para &reg; ligações de protocolo OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente Azure VPN

Utilize este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para descarregar o Cliente Azure VPN.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Importar um perfil de cliente

1. Na página, selecione **Import**.

    ![Screenshot mostra Import selecionado a partir do menu plus.](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Navegue no ficheiro xml do perfil e selecione-o. Com o ficheiro selecionado, selecione **Abrir**.

    ![A screenshot mostra uma caixa de diálogo aberta onde pode selecionar um ficheiro.](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Especifique o nome do perfil e **selecione Guardar**.

    ![A screenshot mostra o nome de ligação adicionado e o botão Guardar selecionado.](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Selecione **Connect** para ligar à VPN.

    ![A screenshot mostra o botão 'Ligar' para a ligação que acabaste de criar.](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Uma vez ligado, o ícone ficará verde e dirá **Conectado**.

    ![A screenshot mostra a ligação num estado ligado com a opção de desligar.](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para eliminar um perfil de cliente

1. Selecione a elipse (...) ao lado do perfil do cliente que pretende eliminar. Em seguida, **selecione Remover**.

    ![Screenshot mostra Remover selecionado do menu.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Selecione **Remover** para eliminar.

    ![A screenshot mostra uma caixa de diálogo de confirmação com a opção de Remover ou Cancelar.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Para diagnosticar problemas de conexão

1. Para diagnosticar problemas de ligação, pode utilizar a ferramenta **Diagnosticar.** Selecione a elipse (...) ao lado da ligação VPN que pretende diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![A screenshot mostra o Diagnóstico selecionado no menu.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Na página Propriedades de **Ligação,** selecione **'Executar Diagnóstico'.**

    ![A screenshot mostra o botão de diagnóstico de execução para uma ligação.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Inscreva-se com as suas credenciais.

    ![diagnóstico 3](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Veja os resultados do diagnóstico.

    ![A screenshot mostra o botão de diagnóstico de execução para uma ligação.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Inscreva-se com as suas credenciais.

    ![A screenshot mostra o Sinal na caixa de diálogo para esta ação.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Veja os resultados do diagnóstico.

    ![A imagem mostra os resultados do diagnóstico.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Veja o seu WAN virtual

1. Navegue para a WAN virtual.

2. Na página Overview, cada ponto no mapa representa um hub.

3. Na secção Hubs e ligações, pode ver o estado do hub, o site, a região, o estado da ligação VPN e os bytes de entrada e saída.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar os recursos

Quando já não precisar desses recursos, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que o mesmo contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
