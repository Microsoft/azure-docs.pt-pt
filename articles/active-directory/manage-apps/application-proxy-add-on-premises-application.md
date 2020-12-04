---
title: Tutorial - Adicionar uma app no local - Application Proxy in Azure AD
description: O Azure Ative Directory (Azure AD) tem um serviço de Procuração de Aplicações que permite aos utilizadores aceder em aplicações no local, insinundo-se com a sua conta Azure AD. Este tutorial mostra-lhe como preparar o seu ambiente para uso com Aplicação Proxy. Em seguida, utiliza o portal Azure para adicionar uma aplicação no local ao seu inquilino AZure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 5c49a6ee25429ab324cea573425ea8b3dd56fdb2
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573617"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Adicionar uma aplicação no local para acesso remoto através de Aplicação Proxy em Diretório Ativo Azure

O Azure Ative Directory (Azure AD) tem um serviço de Procuração de Aplicações que permite aos utilizadores aceder em aplicações no local, insinundo-se com a sua conta Azure AD. Este tutorial prepara o seu ambiente para uso com Aplicação Proxy. Assim que o seu ambiente estiver pronto, utilizará o portal Azure para adicionar uma aplicação no local ao seu inquilino AZure AD.

Este tutorial permite:

> [!div class="checklist"]
> * Abre portas para tráfego de saída e permite acesso a URLs específicos
> * Instala o conector no seu servidor Windows e regista-o com Procuração de Aplicações
> * Verifica o conector instalado e registado corretamente
> * Adiciona um pedido no local ao seu inquilino Azure AD
> * Verifica que um utilizador de teste pode inscrever-se na aplicação utilizando uma conta AD Azure

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar uma aplicação no local à Azure AD, você precisa:

* Uma [subscrição premium AD da Microsoft Azure](https://azure.microsoft.com/pricing/details/active-directory)
* Uma conta de administrador de candidatura
* As identidades dos utilizadores devem ser sincronizadas a partir de um diretório no local ou criadas diretamente dentro dos seus inquilinos AZure AD. A sincronização de identidade permite que a Azure AD pré-autenticar os utilizadores antes de lhes conceder acesso às aplicações publicadas pela App Proxy e ter as informações necessárias do identificador do utilizador para realizar um único sinal de acesso (SSO).

### <a name="windows-server"></a>Windows Server

Para utilizar o Proxy da aplicação, precisa de um servidor Windows com o Windows Server 2012 R2 ou mais tarde. Instalará o conector Application Proxy no servidor. Este servidor de conector precisa de se ligar aos serviços de Procuração de Aplicações em Azure e às aplicações no local que planeia publicar.

Para uma elevada disponibilidade no seu ambiente de produção, recomendamos ter mais de um servidor Windows. Para este tutorial, um servidor Windows é suficiente.

> [!IMPORTANT]
> Se estiver a instalar o conector no Windows Server 2019, tem de desativar o suporte ao protocolo HTTP2 no componente WinHttp para a Delegação Restrita Kerberos funcionar corretamente. Isto é desativado por padrão em versões anteriores de sistemas operativos suportados. Adicionar a seguinte chave de registo e reiniciar o servidor desativa-a no Windows Server 2019. Note que esta é uma chave de registo em toda a máquina.
>
> ```
> Windows Registry Editor Version 5.00
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp] "EnableDefaultHttp2"=dword:00000000
> ```
>

#### <a name="recommendations-for-the-connector-server"></a>Recomendações para o servidor do conector

1. Localize fisicamente o servidor de conector perto dos servidores de aplicações para otimizar o desempenho entre o conector e a aplicação. Para obter mais informações, consulte [as considerações de topologia da Rede.](application-proxy-network-topology.md)
1. O servidor de conector e os servidores de aplicações web devem pertencer ao mesmo domínio ative directory ou domínios de confiança de envergadura. Ter os servidores no mesmo domínio ou domínios de confiança é um requisito para a utilização de um único sign-on (SSO) com autenticação integrada do Windows (IWA) e delegação restrita kerberos (KCD). Se o servidor do conector e os servidores de aplicações web estiverem em diferentes domínios do Ative Directory, é necessário utilizar a delegação baseada em recursos para um único sinal de sação. Para obter mais informações, consulte [o KCD para obter um único s-on com o Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Se tiver implementado o Azure AD Password Protection Proxy, não instale o Proxy da Aplicação AD Azure e o Azure AD Password Protection Proxy na mesma máquina. Azure AD Application Proxy e Azure AD Password Protection Proxy instalam diferentes versões do serviço Azure AD Connect Agent Updater. Estas diferentes versões são incompatíveis quando instaladas juntas na mesma máquina.

#### <a name="tls-requirements"></a>Requisitos TLS

O servidor de conector Windows necessita de ter o TLS 1.2 ativado antes de instalar o conector Proxy da aplicação.

Para ativar o TLS 1.2:

1. Definir as seguintes chaves de registo:
    
   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. Reinicie o servidor.

> [!Note]
> A Microsoft está a atualizar os serviços da Azure para utilizar certificados TLS de um conjunto diferente de Autoridades de Certificados De Raiz (CAs). Esta alteração está a ser feita porque os certificados ac atuais não cumprem um dos requisitos de Base do Fórum CA/Browser. Consulte [as alterações do certificado Azure TLS](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes) para obter mais informações.

## <a name="prepare-your-on-premises-environment"></a>Prepare o seu ambiente no local

Comece por permitir a comunicação aos centros de dados Azure para preparar o seu ambiente para o Azure AD Application Proxy. Se houver uma firewall no caminho, certifique-se de que está aberta. Uma firewall aberta permite ao conector fazer pedidos HTTPS (TCP) ao Application Proxy.

> [!IMPORTANT]
> Se estiver a instalar o conector para a nuvem do Governo Azure, siga os [passos de pré-requisitos](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) e [de instalação](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Isto requer permitir o acesso a um conjunto diferente de URLs e um parâmetro adicional para executar a instalação.

### <a name="open-ports"></a>Abrir portas

Abra as seguintes portas para o tráfego **de saída.**

   | Número da porta | Como é usado |
   | --- | --- |
   | 80 | Baixar listas de revogação de certificados (CRLs) ao mesmo tempo que valida o certificado TLS/SSL |
   | 443 | Toda a comunicação de saída com o serviço Application Proxy |

Se a sua firewall impor o tráfego de acordo com os utilizadores originários, também abra as portas 80 e 443 para o tráfego dos serviços windows que funcionam como um Serviço de Rede.

### <a name="allow-access-to-urls"></a>Permitir o acesso aos URLs

Permitir o acesso aos seguintes URLs:

| URL | Porta | Como é usado |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Comunicação entre o conector e o serviço de nuvem Proxy de aplicação |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP |O conector utiliza estes URLs para verificar os certificados. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com<br>www.microsoft.com/pkiops | 443/HTTPS |O conector utiliza estes URLs durante o processo de registo. |
| ctldl.windowsupdate.com | 80/HTTP |O conector utiliza este URL durante o processo de registo. |

Pode permitir ligações a &ast; .msappproxy.net, &ast; .servicebus.windows.net e outros URLs acima se a sua firewall ou proxy permitir que ele configuure listas de autorizações DNS. Caso contrário, tem de permitir o acesso às [gamas Azure IP e Tags de Serviço - Nuvem Pública](https://www.microsoft.com/download/details.aspx?id=56519). Os intervalos IP são atualizados todas as semanas.

## <a name="install-and-register-a-connector"></a>Instalar e registar um conector

Para utilizar o Application Proxy, instale um conector em cada servidor do Windows que está a utilizar com o serviço Application Proxy. O conector é um agente que gere a ligação de saída dos servidores de aplicações no local para o Application Proxy em Azure AD. Pode instalar um conector em servidores que também têm outros agentes de autenticação instalados, como o Azure AD Connect.


Para instalar o conector:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador de aplicação do diretório que utiliza o Application Proxy. Por exemplo, se o domínio do inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro pseudónimo de administração nesse domínio.
1. Selecione o seu nome de utilizador no canto superior direito. Verifique se está inscrito num diretório que utiliza o Application Proxy. Se precisar de alterar os diretórios, selecione **o diretório da Switch** e escolha um diretório que utilize o Application Proxy.
1. No painel de navegação esquerdo, selecione **Azure Ative Directory**.
1. Em **Gestão**, selecione **Application proxy**.
1. Selecione **Baixar o serviço de conector**.

    ![Baixe o serviço de conector para ver os Termos de Serviço](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Leia os Termos de Serviço. Quando estiver pronto, **selecione Aceitar termos & Descarregar**.
1. Na parte inferior da janela, selecione **Executar** para instalar o conector. Abre-se um assistente de instalação.
1. Siga as instruções do assistente para instalar o serviço. Quando lhe for solicitado que registe o conector com o Application Proxy para o seu inquilino AZure AD, forneça as credenciais do administrador de aplicação.
    - Para o Internet Explorer (IE), se **a configuração de segurança melhorada do IE** estiver definida para **On**, poderá não ver o ecrã de registo. Para obter acesso, siga as instruções na mensagem de erro. Certifique-se de que a **configuração de segurança melhorada do Internet Explorer** está definida para **desligar**.

### <a name="general-remarks"></a>Observações gerais

Se já instalou um conector, reinstale para obter a versão mais recente. Para ver informações sobre versões previamente lançadas e que alterações incluem, consulte [Application Proxy: Version Release History](application-proxy-release-version-history.md).

Se optar por ter mais do que um servidor Windows para as suas aplicações no local, terá de instalar e registar o conector em cada servidor. Pode organizar os conectores em grupos de conector. Para obter mais informações, consulte [os grupos Connector](application-proxy-connector-groups.md).

Se a sua organização utilizar servidores proxy para se ligar à internet, tem de os configurar para Aplicação Proxy.  Para obter mais informações, consulte [Trabalhar com servidores proxy existentes no local.](application-proxy-configure-connectors-with-proxy-servers.md) 

Para obter informações sobre conectores, planeamento de capacidades e como se mantêm [atualizados, consulte os conectores Proxy da aplicação AD Azure](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifique o conector instalado e registado corretamente

Pode utilizar o portal Azure ou o servidor Windows para confirmar que um novo conector foi instalado corretamente.

### <a name="verify-the-installation-through-azure-portal"></a>Verifique a instalação através do portal Azure

Para confirmar o conector instalado e registado corretamente:

1. Inscreva-se no seu diretório de inquilinos no [portal Azure.](https://portal.azure.com)
1. No painel de navegação esquerdo, selecione **O Diretório Ativo Azure** e, em seguida, selecione **Application Proxy** sob a secção **Gerir.** Todos os seus conectores e grupos de conector aparecem nesta página.
1. Consulte um conector para verificar os seus detalhes. Os conectores devem ser expandidos por defeito. Se o conector que pretende visualizar não for expandido, expanda o conector para ver os detalhes. Uma etiqueta verde ativa indica que o seu conector pode ligar-se ao serviço. No entanto, apesar de a etiqueta ser verde, um problema de rede ainda pode impedir o conector de receber mensagens.

    ![Conectores de procuração de aplicação Azure AD](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Para obter mais ajuda na instalação de um conector, consulte [Problema de instalação do Conector Proxy da aplicação](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verifique a instalação através do seu servidor Windows

Para confirmar o conector instalado e registado corretamente:

1. Abra o Gestor de Serviços do Windows clicando na tecla **Windows** e inserindo *serviços.msc*.
1. Verifique se o estado dos dois serviços seguintes está **em funcionamento**.
   - **O Conector Proxy da aplicação do Microsoft AAD** permite a conectividade.
   - **Microsoft AAD Application Proxy Connector Updater** é um serviço de atualização automatizado. O atualizador verifica as novas versões do conector e atualiza o conector conforme necessário.

     ![Serviços do Conector do Proxy da Aplicação – captura de ecrã](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Se o estado dos serviços não estiver **a funcionar,** clique à direita para selecionar cada serviço e escolher **Start**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Adicione uma app no local ao Azure AD

Agora que preparou o seu ambiente e instalou um conector, está pronto para adicionar aplicações no local ao Azure AD.  

1. Inscreva-se como administrador no [portal Azure](https://portal.azure.com/).
2. No painel de navegação esquerdo, selecione **Azure Ative Directory**.
3. Selecione **as aplicações da Enterprise** e, em seguida, selecione Nova **aplicação**.
4. Na secção **de aplicações no local,** selecione **Adicionar uma aplicação no local**.
5. Na secção **de aplicação adicionar** a sua própria no local, forneça as seguintes informações sobre a sua aplicação:

    | Campo | Descrição |
    | :---- | :---------- |
    | **Nome** | O nome da aplicação que vai aparecer nas Minhas Apps e no portal Azure. |
    | **URL interno** | O URL para aceder à aplicação a partir de dentro da sua rede privada. Pode fornecer um caminho específico no servidor de back-end para publicação, enquanto o resto do servidor não é publicado. Desta forma, pode publicar diferentes sites no mesmo servidor que diferentes aplicações, e dar a cada um o seu próprio nome e regras de acesso.<br><br>Se publicar um caminho, certifique-se de que inclui todas as imagens, scripts e folhas de estilo necessários para a sua aplicação. Por exemplo, se a sua aplicação estiver em https: \/ /yourapp/app e utilizar imagens localizadas em https: \/ /yourapp/media, então deve publicar https: \/ /yourapp/ como o caminho. Este URL interno não tem de ser a página de aterragem que os seus utilizadores vêem. Para obter mais informações, consulte [definir uma página inicial personalizada para aplicações publicadas.](application-proxy-configure-custom-home-page.md) |
    | **URL Externo** | O endereço para os utilizadores acederem à aplicação de fora da sua rede. Se não quiser utilizar o domínio proxy de aplicação predefinido, leia sobre [domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Pré Autenticação** | Como a Aplicação Proxy verifica os utilizadores antes de lhes dar acesso à sua aplicação.<br><br>**Azure Ative Directory** - Application Proxy redireciona os utilizadores para iniciar súm na Azure AD, que autentica as suas permissões para o diretório e aplicação. Recomendamos manter esta opção como padrão para que possa tirar partido das funcionalidades de segurança Azure AD, como acesso condicional e autenticação multi-factor. **O Azure Ative Directory** é necessário para monitorizar a aplicação com a Microsoft Cloud Application Security.<br><br>**Passthrough** - Os utilizadores não têm de autenticar contra a AZure AD para aceder à aplicação. Ainda pode configurar requisitos de autenticação no backend. |
    | **Grupo de Conector** | Os conectores processam o acesso remoto à sua aplicação, e os grupos de conector ajudam-no a organizar conectores e aplicações por região, rede ou finalidade. Se ainda não tiver nenhum grupo de conector criado, a sua aplicação é atribuída ao **Padrão.**<br><br>Se a sua aplicação utilizar WebSockets para ligar, todos os conectores do grupo devem ser a versão 1.5.612.0 ou posterior.|

6. Se necessário, configurar **definições adicionais**. Para a maioria das aplicações, deve manter estas definições nos seus estados predefinidos. 

    | Campo | Descrição |
    | :---- | :---------- |
    | **Tempo de tempo da aplicação backend** | Desaça este valor **apenas** para Long se a sua aplicação for lenta a autenticar e a ligar. Por predefinição, o tempo limite de tempo da aplicação backend tem um comprimento de 85 segundos. Quando definido para longo, o tempo de reencaminhar é aumentado para 180 segundos. |
    | **Use HTTP-Only Cookie** | Desaprote este valor para **Sim** para ter cookies Proxy de aplicação incluem a bandeira HTTPOnly no cabeçalho de resposta HTTP. Se utilizar serviços de ambiente de trabalho remoto, desa um valor para **O**.|
    | **Use Secure Cookie**| Desagure este valor para **Sim** para transmitir cookies através de um canal seguro, como um pedido HTTPS encriptado.
    | **Use cookie persistente**| Mantenha este valor definido para **nº**. Utilize apenas esta definição para aplicações que não podem partilhar cookies entre processos. Para obter mais informações sobre as definições de cookies, consulte [as definições de Cookies para aceder a aplicações no local no Azure Ative Directory](./application-proxy-configure-cookie-settings.md).
    | **Traduzir URLs em Cabeçalhos** | Mantenha este valor como **Sim,** a menos que a sua aplicação tenha exigido o cabeçalho original do anfitrião no pedido de autenticação. |
    | **Traduzir URLs no Corpo de Aplicação** | Mantenha este valor como **Nº,** a menos que tenha ligações HTML codificadas com outras aplicações no local e não utilize domínios personalizados. Para obter mais informações, consulte [a tradução de Link com Application Proxy](application-proxy-configure-hard-coded-link-translation.md).<br><br>Desaprove este valor para **Sim** se planeia monitorizar esta aplicação com a Microsoft Cloud App Security (MCAS). Para obter mais informações, consulte a [monitorização do acesso a aplicações em tempo real com a Microsoft Cloud App Security e o Azure Ative Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Selecione **Adicionar**.

## <a name="test-the-application"></a>Testar a aplicação

Está pronto para testar a aplicação corretamente. Nos seguintes passos, irá adicionar uma conta de utilizador à aplicação e tentar iniciar sessão.

### <a name="add-a-user-for-testing"></a>Adicione um utilizador para testes

Antes de adicionar um utilizador à aplicação, verifique se a conta de utilizador já tem permissões para aceder à aplicação a partir de dentro da rede corporativa.

Para adicionar um utilizador de teste:

1. Selecione **as aplicações Da Enterprise** e, em seguida, selecione a aplicação que pretende testar.
2. **Selecione Começar a trabalhar** e, em seguida, selecione **Atribua um utilizador para testes**.
3. Sob **Utilizadores e grupos**, selecione **Adicionar utilizador**.
4. Em **Atribuição adicionar**, selecione **Utilizadores e grupos**. Aparece a secção **Utilizador e grupos.**
5. Escolha a conta que pretende adicionar.
6. Escolha **Selecionar** e, em seguida, selecione **Atribuir**.

### <a name="test-the-sign-on"></a>Teste a inscrição

Para testar o sinal de inscrição na aplicação:

1. A partir da aplicação que pretende testar, selecione **Application Proxy**.
2. No topo da página, selecione **Test Application** para executar um teste na aplicação e verifique se há problemas de configuração.
3. Certifique-se de lançar primeiro a aplicação para testar a assinatura da aplicação, em seguida, descarregue o relatório de diagnóstico para rever a orientação de resolução para quaisquer problemas detetados.

Para a resolução de problemas, consulte [problemas de procuração de aplicação de resolução de problemas e mensagens de erro](application-proxy-troubleshoot.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine os recursos que criou neste tutorial.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, preparou o seu ambiente no local para trabalhar com o Application Proxy e, em seguida, instalou e registou o conector Application Proxy. Em seguida, adicionou um pedido ao seu inquilino AZure AD. Verificou que um utilizador pode inscrever-se na aplicação utilizando uma conta AZure AD.

Fez tudo isto:
> [!div class="checklist"]
> * Portas abertas para tráfego de saída e permitiu o acesso a URLs específicos
> * Instalou o conector no seu servidor Windows e registou-o com o Application Proxy
> * Verifiquei o conector instalado e registado corretamente
> * Adicionei um pedido no local ao seu inquilino Azure AD
> * Verificado um utilizador de teste pode iniciar sação na aplicação utilizando uma conta AD Azure

Está pronto para configurar o pedido de inscrição única. Use o seguinte link para escolher um único método de inscrição e para encontrar tutoriais de assinatura única.

> [!div class="nextstepaction"]
> [Configurar o início de sessão único](sso-options.md#choosing-a-single-sign-on-method)
