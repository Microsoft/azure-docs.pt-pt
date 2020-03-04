---
title: 'VPN Gateway: Cliente VPN para ligações P2S protocolo OpenVPN: Autenticação Azure AD'
description: Pode utilizar o P2S VPN para se ligar ao seu VNet utilizando a autenticação Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: alzam
ms.openlocfilehash: fc48b0ae9cf4162b4b9abba14c6e909ca091fd23
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251606"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Configure um cliente VPN para ligações de protocolo P2S OpenVPN: Autenticação Azure AD

Este artigo ajuda-o a configurar um cliente VPN para se conectar a uma rede virtual utilizando a autenticação de Diretório Ativo Point-to-Site. Antes de poder ligar e autenticar usando o Azure AD, tem primeiro de configurar o seu inquilino Azure AD. Para mais informações, consulte [Configure um inquilino DaD Azure.](openvpn-azure-ad-tenant.md)

> [!NOTE]
> A autenticação Azure AD é suportada apenas para ligações de protocolo openVPN®.
>

## <a name="profile"></a>Trabalhar com perfis de clientes

Para se conectar, precisa de descarregar o Cliente VpN Azure e configurar um perfil de cliente VPN em todos os computadores que queiram ligar-se ao VNet. Pode criar um perfil de cliente num computador, exportá-lo e depois importá-lo para computadores adicionais.

### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente Azure VPN

Utilize este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para descarregar o Cliente VpN Azure.

### <a name="cert"></a>Para criar um perfil de cliente baseado em certificados

Ao trabalhar com um perfil baseado em certificado, certifique-se de que os certificados apropriados são instalados no computador cliente. Para obter mais informações sobre certificados, consulte [Instalar certificados de cliente.](point-to-site-how-to-vpn-client-install-azure-cert.md)

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Para criar um perfil de cliente RADIUS

  ![raio](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> O Segredo do Servidor pode ser exportado no perfil de cliente P2S VPN.  As instruções sobre como exportar um perfil de cliente podem ser encontradas [aqui](about-vpn-profile-download.md).
>

### <a name="export"></a>Para exportar e distribuir um perfil de cliente

Uma vez que tenha um perfil de trabalho e precise distribuí-lo a outros utilizadores, pode exportá-lo usando os seguintes passos:

1. Realce o perfil de cliente VPN que pretende exportar, selecione o **...** , em seguida, selecione **Export**.

    ![exportação](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecione a localização a que pretende guardar este perfil, deixe o nome de ficheiro como está e, em seguida, selecione **Guardar** para guardar o ficheiro xml.

    ![exportação](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Import**.

    ![importação](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Navegue no ficheiro xml de perfil e selecione-o. Com o ficheiro selecionado, selecione **Open**.

    ![importação](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Guardar**.

    ![importação](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecione **Ligar** para ligar à VPN.

    ![importação](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Uma vez ligado, o ícone ficará verde e dirá **Connected**.

    ![importação](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Para eliminar um perfil de cliente

1. Selecione as elipses ao lado do perfil do cliente que pretende eliminar. Em seguida, selecione **Remover**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecione **Remover** para eliminar.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Criar uma ligação

1. Na página, selecione **+,** em seguida+ **Adicionar**.

    ![ligação](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Preencha as informações de ligação. Se não tiver a certeza dos valores, contacte o seu administrador. Depois de preencher os valores, selecione **Guardar**.

    ![ligação](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecione **Ligar** para ligar à VPN.

    ![ligação](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecione as credenciais adequadas e, em seguida, selecione **Continuar**.

    ![ligação](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Uma vez ligado com sucesso, o ícone ficará verde e dirá **Connected**.

    ![ligação](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Para ligar automaticamente

Estes passos ajudam-no a configurar a sua ligação para se ligar automaticamente ao Always-on.

1. Na página inicial do seu cliente VPN, selecione **Definições VPN**.

    ![Automático](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecione **Sim** na caixa de diálogo de apps switch.

    ![Automático](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Certifique-se de que a ligação que pretende definir ainda não está ligada, em seguida, realce o perfil e verifique automaticamente a caixa de verificação **de 'Connect'.**

    ![Automático](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecione **Ligar** para iniciar a ligação VPN.

    ![Automático](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de ligação, pode utilizar a ferramenta **Diagnosticar.** Selecione o **...** ao lado da ligação VPN que pretende diagnosticar para revelar o menu. Em seguida, **selecione Diagnosticar**.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na página Propriedades de **Ligação,** selecione **Diagnóstico de Execução**.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Inscreva-se com as suas credenciais.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Veja os resultados do diagnóstico.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>FAQ

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Como adiciono sufixos DNS ao cliente VPN?

Pode modificar o ficheiro XML de perfil descarregado e adicionar o **\<dnssufixs>\<dnssufix> \</dnssufix>\</dnssufixs>** tags

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Como adiciono servidores DNS personalizados ao cliente VPN?

Pode modificar o ficheiro XML de perfil descarregado e adicionar o **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** tags

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Como adiciono rotas personalizadas ao cliente VPN?

Pode modificar o ficheiro XML de perfil descarregado e adicionar o **\<incluiroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>** tags

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Como bloquear (excluo) rotas do cliente VPN?

Pode modificar o ficheiro XML de perfil descarregado e adicionar o **\<excluirotas>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</excluderoutes>** tags

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [Criar um inquilino azure ative diretório para ligações P2S Open VPN que utilizem a autenticação Azure AD](openvpn-azure-ad-tenant.md).
