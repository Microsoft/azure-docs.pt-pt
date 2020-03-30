---
title: 'VPN Gateway: Cliente VPN para ligações P2S protocolo OpenVPN: Autenticação Azure AD'
description: Pode utilizar o P2S VPN para se ligar ao seu VNet utilizando a autenticação Azure AD
services: vpn-gateway
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: alzam
ms.openlocfilehash: edb509d43742aeecf74107ae8cb625aeafbccb9f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385626"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Configure um cliente VPN para ligações de protocolo P2S OpenVPN: Autenticação Azure AD

Este artigo ajuda-o a configurar um cliente VPN para se conectar a uma rede virtual utilizando a autenticação de Diretório Ativo Point-to-Site. Antes de poder ligar e autenticar usando o Azure AD, tem primeiro de configurar o seu inquilino Azure AD. Para mais informações, consulte [Configure um inquilino DaD Azure.](openvpn-azure-ad-tenant.md)

> [!NOTE]
> A autenticação Azure AD é suportada apenas para ligações de protocolo openVPN®.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Trabalhar com perfis de clientes

Para se conectar, precisa de descarregar o Cliente VpN Azure e configurar um perfil de cliente VPN em todos os computadores que queiram ligar-se ao VNet. Pode criar um perfil de cliente num computador, exportá-lo e depois importá-lo para computadores adicionais.

### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente Azure VPN

Utilize este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para descarregar o Cliente VpN Azure. Certifique-se de que o Cliente VPN Azure tem permissão para correr em segundo plano. Para verificar/ativar a permissão siga os passos abaixo:

1. Vá iniciar e, em seguida, selecione Definições > Aplicações de Privacidade > Fundo.
2. Em Aplicativos de Fundo, **certifique-se de que as aplicações executadas em segundo plano** estão ligadas.
3. Em 'Escolha quais as aplicações que podem ser executadas em segundo plano', rode as definições para O Cliente VPN Azure para **On**.

  ![permissão](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Para criar um perfil de cliente baseado em certificados

Ao trabalhar com um perfil baseado em certificado, certifique-se de que os certificados apropriados são instalados no computador cliente. Para obter mais informações sobre certificados, consulte [Instalar certificados de cliente.](certificates-point-to-site.md)

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Para criar um perfil de cliente RADIUS

  ![raio](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> O Segredo do Servidor pode ser exportado no perfil de cliente P2S VPN.  As instruções sobre como exportar um perfil de cliente podem ser encontradas [aqui](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Para exportar e distribuir um perfil de cliente

Uma vez que tenha um perfil de trabalho e precise distribuí-lo a outros utilizadores, pode exportá-lo usando os seguintes passos:

1. Realce o perfil de cliente VPN que pretende exportar, selecione o **...**, em seguida, selecione **Export**.

    ![exportação](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecione a localização a que pretende guardar este perfil, deixe o nome de ficheiro como está e, em seguida, selecione **Guardar** para guardar o ficheiro xml.

    ![exportação](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Import**.

    ![importar](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Navegue no ficheiro xml de perfil e selecione-o. Com o ficheiro selecionado, selecione **Open**.

    ![importar](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Guardar**.

    ![importar](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecione **Ligar** para ligar à VPN.

    ![importar](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Uma vez ligado, o ícone ficará verde e dirá **Connected**.

    ![importar](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para eliminar um perfil de cliente

1. Selecione as elipses ao lado do perfil do cliente que pretende eliminar. Em seguida, selecione **Remover**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecione **Remover** para eliminar.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Criar uma ligação

1. Na página, **+** selecione, em seguida+ **Adicionar**.

    ![ligação](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Preencha as informações de ligação. Se não tiver a certeza dos valores, contacte o seu administrador. Depois de preencher os valores, selecione **Guardar**.

    ![ligação](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecione **Ligar** para ligar à VPN.

    ![ligação](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecione as credenciais adequadas e, em seguida, selecione **Continuar**.

    ![ligação](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Uma vez ligado com sucesso, o ícone ficará verde e dirá **Connected**.

    ![ligação](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Para ligar automaticamente

Estes passos ajudam-no a configurar a sua ligação para se ligar automaticamente ao Always-on.

1. Na página inicial do seu cliente VPN, selecione **Definições VPN**.

    ![Automático](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecione **Sim** na caixa de diálogo de apps switch.

    ![Automático](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Certifique-se de que a ligação que pretende definir ainda não está ligada, em seguida, realce o perfil e verifique automaticamente a caixa de verificação **de 'Connect'.**

    ![Automático](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecione **Ligar** para iniciar a ligação VPN.

    ![Automático](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticar problemas de conexão

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

Pode modificar o ficheiro XML de perfil descarregado e adicionar os ** \<dnssufixos>\<dnssufixo> \</dnssufix>\</dnssufixos>** tags

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

Pode modificar o ficheiro XML de perfil descarregado e adicionar os ** \<dnsservers \<>dnsserver> \</dnsserver>\</dnsservers>** tags

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

> [!NOTE]
> O cliente OpenVPN Azure AD utiliza as entradas da Tabela de Resolução de Nomes DNS (NRPT), o que significa que os servidores DNS não serão listados sob a saída de `ipconfig /all`. Para confirmar as definições de DNS em uso, consulte [o Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) no PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Como adiciono rotas personalizadas ao cliente VPN?

Pode modificar o ficheiro XML de perfil descarregado e adicionar as ** \<rotas>\<>\<destino \<>\< \<máscara \<> /destino>\</máscara>/route>/incluir rotas>** etiquetas

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

Pode modificar o ficheiro XML de perfil descarregado e adicionar as ** \<rotas>>\<destino>>\< \<máscara \<>/destino>\</máscara>\</rota>\</excluirotas>** etiquetas

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
