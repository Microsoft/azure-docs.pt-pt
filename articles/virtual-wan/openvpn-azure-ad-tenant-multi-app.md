---
title: 'WAN Virtual: Inquilino da AD Azure para diferentes grupos de utilizadores: Autenticação Azure AD'
description: Pode utilizar o P2S VPN para se ligar ao seu VNet utilizando a autenticação Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 4e667dcccd612873e8633c375bc3ce611e11c962
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485953"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um inquilino de Diretório Ativo Azure para ligações ao protocolo P2S OpenVPN

Ao ligar-se ao seu VNet, pode utilizar autenticação baseada em certificado ou autenticação RADIUS. No entanto, ao utilizar o protocolo Open VPN, também pode utilizar a autenticação do Diretório Ativo Azure. Se pretender que diferentes utilizadores possam ligar-se a diferentes gateways VPN, pode registar várias aplicações em AD e ligá-las a diferentes gateways VPN.

Este artigo ajuda-o a configurar um inquilino Azure AD para autenticação P2S OpenVPN, e criar e registar várias aplicações em Azure AD para permitir diferentes acessos para diferentes utilizadores e grupos.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para ligações de protocolo openVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="site"></a>6. Criar uma nova configuração P2S

Uma configuração P2S define os parâmetros para ligar clientes remotos.

1. Desloque as seguintes variáveis, substituindo os valores necessários para o seu ambiente.

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
   > Não utilize o ID de aplicação do cliente Azure VPN nos comandos acima: Irá conceder a todos os utilizadores o acesso ao gateway VPN. Utilize a identificação do(s) pedido que registou.

## <a name="hub"></a>7. Editar atribuição do hub

1. Navegue para a lâmina **Hubs** sob o WAN virtual.

2. Selecione o centro a que pretende associar a configuração do servidor vpn e clique na elipse (...).

    ![novo site](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Clique em **Editar o centro virtual**.

4. Verifique a caixa de verificação de **gateway ponto-a-local** e escolha a **unidade de escala Gateway** que deseja.

    ![novo site](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Introduza o **pool de Endereços** a partir do qual os clientes VPN serão atribuídos endereços IP.

6. Clique em **Confirmar**.

7. A operação pode demorar até 30 minutos para ser concluída.

## <a name="device"></a>8. Descarregue o perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

1. Na página para o seu WAN virtual, clique nas **configurações VPN do utilizador**.

2. No topo da página, clique em **Baixar o utilizador VPN config**.

3. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.

4. Utilize o ficheiro de perfil para configurar os clientes VPN.

5. Extraio o ficheiro zip descarregado.

6. Navegue na pasta "AzureVPN" desapertado.

7. Tome nota da localização do ficheiro "azurevpnconfig.xml". O azurevpnconfig.xml contém a definição para a ligação VPN e pode ser importado diretamente para a aplicação azure VPN Client. Também pode distribuir este ficheiro a todos os utilizadores que necessitem de se conectar via e-mail ou outros meios. O utilizador necessitará de credenciais Azure AD válidas para se conectar com sucesso.

## <a name="9-configure-user-vpn-clients"></a>9. Configurar clientes VPN do utilizador

Para se conectar, precisa de descarregar o Cliente VpN Azure e importar o perfil de cliente VPN que foi descarregado nos passos anteriores em todos os computadores que queiram ligar-se ao VNet.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para ligações de protocolo openVPN®.
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente Azure VPN

Utilize este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para descarregar o Cliente VpN Azure.

#### <a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Import**.

    ![importação](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Navegue no ficheiro xml de perfil e selecione-o. Com o ficheiro selecionado, selecione **Open**.

    ![importação](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Guardar**.

    ![importação](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Selecione **Ligar** para ligar à VPN.

    ![importação](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Uma vez ligado, o ícone ficará verde e dirá **Connected**.

    ![importação](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="delete"></a>Para eliminar um perfil de cliente

1. Selecione a elipse (...) junto ao perfil do cliente que pretende eliminar. Em seguida, selecione **Remover**.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Selecione **Remover** para eliminar.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="diagnose"></a>Para diagnosticar problemas de conexão

1. Para diagnosticar problemas de ligação, pode utilizar a ferramenta **Diagnosticar.** Selecione a elipse (...) junto à ligação VPN que pretende diagnosticar para revelar o menu. Em seguida, **selecione Diagnosticar**.

    ![diagnosticar](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Na página Propriedades de **Ligação,** selecione **Diagnóstico de Execução**.

    ![diagnosticar](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Inscreva-se com as suas credenciais.

    ![diagnosticar](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Veja os resultados do diagnóstico.

    ![diagnosticar](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>10. Veja o seu WAN virtual

1. Navegue para a WAN virtual.

2. Na página Overview, cada ponto no mapa representa um hub. Coloque o cursor sobre um ponto para ver o resumo do estado de funcionamento do hub.

3. Na secção Hubs e ligações, pode ver o estado do hub, o site, a região, o estado da ligação VPN e os bytes de entrada e saída.

## <a name="viewhealth"></a>11. Veja a sua saúde de recursos

1. Navegue para a WAN.

2. Na página WAN, na secção **SUPPORT + Troubleshooting** (SUPORTE + Resolução de Problemas), clique em **Health** (Estado de funcionamento) e veja o recurso.

## <a name="cleanup"></a>Limpar recursos

Quando já não precisar desses recursos, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que o mesmo contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
