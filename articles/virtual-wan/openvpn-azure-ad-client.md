---
title: 'VPN Gateway: cliente VPN para ligações P2S do protocolo OpenVPN: Autenticação Azure AD'
description: Saiba como utilizar o P2S VPN para ligar ao seu VNet utilizando a autenticação AD Azure.
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3dbdfb461bc2ae79439f39df3efbc2c564782110
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548398"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Configure a VPN client for P2S OpenVPN protocol connections: Azure AD authentication (Configurar um cliente VPN para ligações de protocolo P2S OpenVPN: autenticação do Azure Active Directory)

Este artigo ajuda-o a configurar um cliente VPN para se conectar a uma rede virtual utilizando a autenticação point-to-site VPN e Azure Ative Directory. Antes de poder ligar e autenticar usando a Azure AD, tem primeiro de configurar o seu inquilino AZure AD. Para mais informações, consulte [Configure um inquilino AD AZure.](openvpn-azure-ad-tenant.md)

> [!NOTE]
> A autenticação AZure AD é suportada apenas para ligações de protocolo ® OpenVPN.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Trabalhar com perfis de clientes

Para se conectar, precisa de descarregar o Cliente Azure VPN e configurar um perfil de cliente VPN em todos os computadores que queiram ligar-se ao VNet. Pode criar um perfil de cliente num computador, exportá-lo e depois importá-lo para computadores adicionais.

### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente Azure VPN

Utilize este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para descarregar o Cliente Azure VPN. Certifique-se de que o Cliente Azure VPN tem permissão para executar em segundo plano. Para verificar/ativar a permissão siga os passos abaixo:

1. Vá para iniciar, em seguida, selecione Definições > aplicações de privacidade > de fundo.
2. No âmbito de Aplicações de Fundo, certifique-se **de que as aplicações são executadas em segundo plano.**
3. Em Escolha quais aplicações podem ser executadas em segundo plano, rode as definições para O Cliente VPN Azure para **On**.

  ![permissão](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Para criar um perfil de cliente baseado em certificados

Ao trabalhar com um perfil baseado em certificado, certifique-se de que os certificados apropriados são instalados no computador cliente. Para obter mais informações sobre certificados, consulte [os certificados de cliente instalar.](certificates-point-to-site.md)

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Para criar um perfil de cliente RADIUS

  ![raio](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> O Server Secret pode ser exportado no perfil de cliente P2S VPN.  As instruções sobre como exportar um perfil de cliente podem ser [encontradas aqui.](about-vpn-profile-download.md)
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Para exportar e distribuir um perfil de cliente

Uma vez que tenha um perfil de trabalho e precise distribuí-lo a outros utilizadores, pode exportá-lo usando os seguintes passos:

1. Realce o perfil de cliente VPN que pretende exportar, selecione o **...** e, em seguida, selecione **Export**.

    ![Screenshot mostra Exportação selecionada do menu.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecione a localização para a qual pretende guardar este perfil, deixe o nome do ficheiro como está e, em seguida, **selecione Guardar** para guardar o ficheiro xml.

    ![A screenshot mostra uma caixa de diálogo Save As onde pode introduzir um nome de ficheiro.](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Importar um perfil de cliente

1. Na página, selecione **Import**.

    ![Screenshot mostra Import selecionado a partir do menu plus.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Navegue no ficheiro xml do perfil e selecione-o. Com o ficheiro selecionado, selecione **Abrir**.

    ![A screenshot mostra uma caixa de diálogo aberta onde pode selecionar um ficheiro.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Especifique o nome do perfil e **selecione Guardar**.

    ![A screenshot mostra o nome de ligação adicionado e o botão Guardar selecionado.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecione **Connect** para ligar à VPN.

    ![A screenshot mostra o botão 'Ligar' para a ligação que acabaste de criar.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Uma vez ligado, o ícone ficará verde e dirá **Conectado**.

    ![A screenshot mostra a ligação num estado ligado com a opção de desligar.](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para eliminar um perfil de cliente

1. Selecione as elipses ao lado do perfil do cliente que pretende eliminar. Em seguida, **selecione Remover**.

    ![Screenshot mostra Remover selecionado do menu.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecione **Remover** para eliminar.

    ![A screenshot mostra uma caixa de diálogo de confirmação com a opção de Remover ou Cancelar.](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Criar uma ligação

1. Na página, selecione **+** , em seguida , + **Adicionar**.

    ![Screenshot mostra Adicionar selecionado a partir do menu mais.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Preencha a informação de ligação. Se não tiver a certeza dos valores, contacte o seu administrador. Depois de preencher os valores, **selecione Guardar**.

    ![A screenshot mostra o painel onde pode introduzir os valores necessários.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecione **Connect** para ligar à VPN.

    ![A screenshot mostra o botão Ligar para a sua ligação.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecione as credenciais adequadas e, em seguida, **selecione Continue**.

    ![A imagem mostra o Sinal na caixa de diálogo.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Uma vez ligado com sucesso, o ícone ficará verde e dirá **Conectado**.

    ![A screenshot mostra a ligação num estado ligado.](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Para ligar automaticamente

Estes passos ajudam-no a configurar a sua ligação para ligar automaticamente com a Always-on.

1. Na página inicial do seu cliente VPN, selecione **VPN Settings**.

    ![A screenshot mostra V P N Connections onde pode selecionar Configurações V P N.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecione **Sim** na caixa de diálogo de aplicações switch.

    ![A screenshot mostra uma mensagem de verificação sobre a troca de aplicações.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Certifique-se de que a ligação que pretende definir ainda não está ligada, em seguida, realce o perfil e verifique a caixa de verificação **do Connect automaticamente.**

    ![A screenshot mostra uma caixa de diálogo definições onde pode selecionar Connect automaticamente.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecione **Connect** para iniciar a ligação VPN.

    ![A imagem mostra o botão 'Ligar'.](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de ligação, pode utilizar a ferramenta **Diagnosticar.** Selecione a **...** ao lado da ligação VPN que pretende diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![A screenshot mostra o Diagnóstico selecionado no menu.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na página Propriedades de **Ligação,** selecione **'Executar Diagnóstico'.**

    ![A screenshot mostra o botão de diagnóstico de execução para uma ligação.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Inscreva-se com as suas credenciais.

    ![A screenshot mostra o Sinal na caixa de diálogo para esta ação.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Veja os resultados do diagnóstico.

    ![A imagem mostra os resultados do diagnóstico.](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>FAQ

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Como adiciono sufixos DNS ao cliente VPN?

Pode modificar o ficheiro XML de **\<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes>** perfil descarregado e adicionar as tags

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

Pode modificar o ficheiro XML de **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** perfil descarregado e adicionar as tags

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
> O cliente OpenVPN Azure AD utiliza entradas na Tabela de Política de Resolução de Nomes DNS (NRPT), o que significa que os servidores DNS não serão listados sob a saída de `ipconfig /all` . Para confirmar as definições de DNS em utilização, consulte [a Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy) no PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Como adiciono rotas personalizadas ao cliente VPN?

Pode modificar o ficheiro XML de **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** perfil descarregado e adicionar as tags

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
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>Como direciono todo o tráfego para o túnel VPN (túnel de força)?

Pode modificar o ficheiro XML de **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** perfil descarregado e adicionar as tags

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>0.0.0.0</destination><mask>1</mask>
        </route>
        <route>
            <destination>128.0.0.0</destination><mask>1</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Como posso bloquear (excluir) rotas do cliente VPN?

Pode modificar o ficheiro XML de **\<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes>** perfil descarregado e adicionar as tags

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
### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Posso importar o perfil de uma linha de comando?

Pode importar o perfil a partir de uma linha de comando, colocando o ficheiro **deazurevpnconfig.xml** descarregado no **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** e executando o seguinte comando:

```
azurevpn -i azurevpnconfig.xml 
```
para forçar a importação usar o interruptor **-f** também


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [Criar um inquilino do Azure Ative Directory para ligações P2S Open VPN que utilizam a autenticação AZURE AD](openvpn-azure-ad-tenant.md).