---
title: 'Gateway de VPN: locatário do Azure AD para grupos de usuários diferentes: autenticação do Azure AD'
description: Você pode usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985650"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um locatário de Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao conectar-se à sua VNet, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo VPN aberto, você também pode usar a autenticação Azure Active Directory. Se desejar que um conjunto diferente de usuários seja capaz de se conectar a diferentes gateways de VPN, você poderá registrar vários aplicativos no AD e vinculá-los a diferentes gateways de VPN. Este artigo ajuda você a configurar um locatário do Azure AD para autenticação P2S OpenVPN e criar e registrar vários aplicativos no Azure AD para permitir acesso diferente para diferentes usuários e grupos.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para conexões de protocolo OpenVPN®.
>

## <a name="tenant"></a>1. criar o locatário do Azure AD

Crie um locatário do Azure AD usando as etapas no artigo [criar um novo locatário](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome organizacional
* Nome inicial de domínio

Exemplo:

   ![Novo locatário do Azure AD](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. criar usuários de locatário do Azure AD

Em seguida, crie duas contas de usuário. Crie uma conta de administrador global e uma conta de usuário mestre. A conta de usuário mestre é usada como sua conta de incorporação mestre (conta de serviço). Ao criar uma conta de usuário de locatário do Azure AD, você ajusta a função de diretório para o tipo de usuário que deseja criar.

Use as etapas neste [artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois usuários para seu locatário do Azure AD. Certifique-se de alterar a **função de diretório** para criar os tipos de conta:

* Admin Global
* Utilizador

## <a name="enable-authentication"></a>3. registrar o cliente VPN do Azure no locatário do Azure AD

1. Localize a ID de diretório do diretório que você deseja usar para autenticação. Ele é listado na seção Propriedades da página Active Directory.

    ![ID do diretório](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Copie a ID do diretório.

3. Entre no portal do Azure como um usuário que é atribuído à função de **administrador global** .

4. Em seguida, dê consentimento ao administrador. Copie e cole a URL que pertence ao seu local de implantação na barra de endereços do seu navegador:

    Público

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Alemanha

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta de **administrador global** , se solicitado.

    ![ID do diretório](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selecione **aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Em seu Azure AD, em **aplicativos empresariais**, você verá a **VPN do Azure** listada.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. registrar aplicativos adicionais para vários usuários/grupos

1. Em seu Azure Active Directory, clique em **registros de aplicativo** e em **+ novo registro**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na folha **registrar um aplicativo** , insira o **nome** e selecione os **tipos de conta com suporte** desejados e clique em **registrar**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Depois que o novo aplicativo tiver sido registrado, clique em **expor uma API** na folha do aplicativo

4. Clique em **+ Adicionar um escopo**
5. Deixe o **URI da ID do aplicativo** padrão e clique em **salvar e continuar**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Preencha os campos obrigatórios e verifique se o **estado** está **habilitado**. Clique em **Adicionar escopo**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Clique em **expor uma API** e, em seguida, em **+ Adicionar um aplicativo cliente**.  Para **ID do cliente**, insira os seguintes valores, dependendo da nuvem:
    -   Insira **41b23e61-6c1e-4545-B367-cd054e0ed4b4** para o Azure **Public**
    -   Insira o **51bb15d4-3a4f-4ebf-9dca-40096fe32426** para o Azure **governamental**
    -   Insira o **538ee9e6-310A-468d-AFEF-ea97365856a9** para o Azure **Alemanha**
    -   Insira o **49f817b6-84ae-4cc0-928C-73f27289b3aa** para o Azure **China 21vianet**


8. Clique em **Adicionar aplicativo**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Copie a **ID do aplicativo (cliente)** da página **visão geral** . Você precisará dela para configurar seu gateway (s) de VPN

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Repita as etapas nesta seção (4) para criar quantos aplicativos forem necessários para seu requisito de segurança. Cada aplicativo será associado a um gateway de VPN e pode ter um conjunto diferente de usuários. Somente um aplicativo pode ser associado a um gateway.

## <a name="enable-authentication"></a>5. atribuir usuários a seus aplicativos

1. Em Azure AD, **aplicativos empresariais**, selecione o aplicativo recentemente registrado e clique em **Propriedades**. Verifique se a **atribuição de usuário é necessária?** está definida como **Sim**. Clicar em **Guardar**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na página do aplicativo, clique em **usuários e grupos** e, em seguida, em **Adicionar usuário**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. Em **Adicionar atribuição**, clique em **usuários e grupos**. Selecione os usuários que você deseja que possam acessar este aplicativo VPN. Clique em **Selecionar**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="site"></a>6. criar uma nova configuração do P2S

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

> [!NOTE]
> Não use a ID do aplicativo do cliente de VPN do Azure nos comandos acima, pois ele concederá a todos os usuários acesso ao gateway de VPN. Use a ID dos aplicativos que você registrou.

## <a name="hub"></a>7. Editar atribuição do hub

1. Navegue para a lâmina **Hubs** sob o WAN virtual.
2. Selecione o centro a que pretende associar a configuração do servidor vpn e clique na elipse (...).

   ![novo site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Clique em **Editar Hub virtual**.
4. Marque a caixa de seleção **incluir gateway de ponto a site** e escolha a **unidade de escala do gateway** desejada.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Insira o **pool de endereços** do qual serão atribuídos endereços IP aos clientes VPN.
6. Clique em **Confirmar**.
7. A operação pode levar até 30 minutos para ser concluída.

## <a name="device"></a>8. Descarregue o perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

1. Na página de sua WAN virtual, clique em **configurações de VPN de usuário**.
2. Na parte superior da página, clique em **baixar configuração de VPN do usuário**.
3. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.
4. Use o arquivo de perfil para configurar os clientes VPN.

4. Extraia o arquivo zip baixado.

5. Navegue até a pasta "AzureVPN" descompactada.

6. Anote o local do arquivo "azurevpnconfig. xml". O azurevpnconfig. xml contém a configuração para a conexão VPN e pode ser importado diretamente para o aplicativo cliente VPN do Azure. Você também pode distribuir esse arquivo para todos os usuários que precisam se conectar por email ou outros meios. O usuário precisará de credenciais válidas do Azure AD para se conectar com êxito.
## <a name="configure-user-vpn-clients"></a>Configurar clientes VPN de usuário

Para se conectar, precisa de descarregar o Cliente VpN Azure (Pré-visualização) e importar o perfil de cliente VPN que foi descarregado nos passos anteriores em todos os computadores que queiram ligar-se ao VNet.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para conexões de protocolo OpenVPN®.
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente VPN do Azure

Use este [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) para baixar o cliente VPN do Azure (versão prévia).

#### <a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **importar**.

    ![importação](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **abrir**.

    ![importação](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Especifique o nome do perfil e selecione **salvar**.

    ![importação](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecione **conectar** para conectar-se à VPN.

    ![importação](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e diria **conectado**.

    ![importação](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Para excluir um perfil de cliente

1. Selecione a elipse (...) junto ao perfil do cliente que pretende eliminar. Em seguida, selecione **remover**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecione **remover** para excluir.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta de **diagnóstico** . Selecione a elipse (...) junto à ligação VPN que pretende diagnosticar para revelar o menu. Em seguida, selecione **diagnosticar**.

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
