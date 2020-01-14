---
title: 'Gateway de VPN: locatário do Azure AD para grupos de usuários diferentes: autenticação do Azure AD'
description: Você pode usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: b390b5f8b00f61994db820a3af7bce26a3e0a30d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934976"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um locatário de Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao conectar-se à sua VNet, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo VPN aberto, você também pode usar a autenticação Azure Active Directory. Se desejar que um conjunto diferente de usuários seja capaz de se conectar a diferentes gateways de VPN, você poderá registrar vários aplicativos no AD e vinculá-los a diferentes gateways de VPN. Este artigo ajuda você a configurar um locatário do Azure AD para autenticação P2S OpenVPN e criar e registrar vários aplicativos no Azure AD para permitir acesso diferente para diferentes usuários e grupos.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para conexões de protocolo OpenVPN®.
>

## <a name="tenant"></a>1. criar o locatário do Azure AD

Crie um locatário do Azure AD usando as etapas no artigo [criar um novo locatário](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome organizacional
* Nome de domínio inicial

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

    Microsoft Cloud Germany

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
> [!NOTE]
> Do not use the Azure VPN client's application ID in the commands above as it will grant all users access to the VPN gateway. Use the ID of the application(s) you registered.

## <a name="hub"></a>7. Edit hub assignment

1. Navigate to the **Hubs** blade under the virtual WAN.
2. Select the hub that you want to associate the vpn server configuration to and click the ellipsis (...).

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Click **Edit virtual hub**.
4. Check the **Include point-to-site gateway** check box and pick the **Gateway scale unit** that you want.

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Enter the **Address pool** from which the VPN clients will be assigned IP addresses.
6. Click **Confirm**.
7. The operation will can take up to 30 minutes to complete.

## <a name="device"></a>8. Download VPN profile

Use the VPN profile to configure your clients.

1. On the page for your virtual WAN, click **User VPN configurations**.
2. At the top of the  page, click **Download user VPN config**.
3. Once the file has finished creating, you can click the link to download it.
4. Use the profile file to configure the VPN clients.

4. Extract the downloaded zip file.

5. Browse to the unzipped “AzureVPN” folder.

6. Make a note of the location of the “azurevpnconfig.xml” file. The azurevpnconfig.xml contains the setting for the VPN connection and can be imported directly into the Azure VPN Client application. You can also distribute this file to all the users that need to connect via e-mail or other means. The user will need valid Azure AD credentials to connect successfully.
## Configure user VPN clients

To connect, you need to download the Azure VPN Client (Preview) and import the VPN client profile that was downloaded in the previous steps on every computer that wants to connect to the VNet.

> [!NOTE]
> Azure AD authentication is supported only for OpenVPN® protocol connections.
>

#### To download the Azure VPN client

Use this [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) to download the Azure VPN Client (Preview).

#### <a name="import"></a>To import a client profile

1. On the page, select **Import**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Browse to the profile xml file and select it. With the file selected, select **Open**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Specify the name of the profile and select **Save**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Select **Connect** to connect to the VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Once connected, the icon will turn green and say **Connected**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>To delete a client profile

1. Select the ellipsis (...) next to the client profile that you want to delete. Then, select **Remove**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Select **Remove** to delete.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnose connection issues

1. To diagnose connection issues, you can use the **Diagnose** tool. Select the ellipsis (...) next to the VPN connection that you want to diagnose to reveal the menu. Then select **Diagnose**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. On the **Connection Properties** page, select **Run Diagnosis**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Sign in with your credentials.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. View the diagnosis results.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>View your virtual WAN

1. Navigate to the virtual WAN.
2. On the Overview page, each point on the map represents a hub. Hover over any point to view the hub health summary.
3. In the Hubs and connections section, you can view hub status, site, region, VPN connection status, and bytes in and out.

## <a name="viewhealth"></a>View your resource health

1. Navigate to your WAN.
2. On your WAN page, in the **SUPPORT + Troubleshooting** section, click **Health** and view your resource.


## <a name="cleanup"></a>Clean up resources

When you no longer need these resources, you can use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) to remove the resource group and all of the resources it contains. Replace "myResourceGroup" with the name of your resource group and run the following PowerShell command:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).