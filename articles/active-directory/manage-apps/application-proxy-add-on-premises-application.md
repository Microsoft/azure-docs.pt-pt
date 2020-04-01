---
title: Tutorial - Adicione uma aplicação no local - Application Proxy in Azure AD
description: O Azure Ative Directory (Azure AD) dispõe de um serviço de Procuração de Aplicações que permite aos utilizadores aceder em aplicações no local através da assinatura com a sua conta Azure AD. Este tutorial mostra-lhe como preparar o seu ambiente para uso com procuração de aplicação. Em seguida, utiliza o portal Azure para adicionar uma aplicação no local ao seu inquilino Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73aa01ea08c8bab1395516c31bb46dbfd88045db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481420"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Adicione um pedido no local para acesso remoto através de Application Proxy no Diretório Ativo Azure

O Azure Ative Directory (Azure AD) dispõe de um serviço de Procuração de Aplicações que permite aos utilizadores aceder em aplicações no local através da assinatura com a sua conta Azure AD. Este tutorial prepara o seu ambiente para uso com procuração de aplicação. Assim que o seu ambiente estiver pronto, utilizará o portal Azure para adicionar uma aplicação no local ao seu inquilino Azure AD.

Este tutorial permite:

> [!div class="checklist"]
> * Abre portas para tráfego de saída e permite acesso a URLs específicos
> * Instala o conector no seu servidor Windows e regista-o com proxy de aplicação
> * Verifica o conector instalado e registado corretamente
> * Adiciona uma aplicação no local ao seu inquilino Azure AD
> * Verifica que um utilizador de teste pode iniciar sessão na aplicação utilizando uma conta Azure AD

## <a name="before-you-begin"></a>Antes de começar

Para adicionar uma aplicação no local ao Azure AD, precisa de:

* Uma [subscrição premium microsoft Azure AD](https://azure.microsoft.com/pricing/details/active-directory)
* Uma conta de administrador de aplicação
* As identidades dos utilizadores devem ser sincronizadas a partir de um diretório no local ou criadas diretamente dentro dos seus inquilinos Da Azure AD. A sincronização de identidade permite que a AD Azure autentique previamente os utilizadores antes de lhes conceder acesso às aplicações publicadas pela App Proxy e ter as informações necessárias para o identificador de utilizador para realizar um único registo (SSO).

### <a name="windows-server"></a>Windows server

Para utilizar o Proxy da Aplicação, necessita de um servidor Windows que execute o Windows Server 2012 R2 ou mais tarde. Instalará o conector Proxy de aplicação no servidor. Este servidor de conector precisa de se ligar aos serviços proxy de aplicação em Azure, e às aplicações no local que pretende publicar.

Para uma elevada disponibilidade no seu ambiente de produção, recomendamos ter mais do que um servidor Windows. Para este tutorial, um servidor Windows é suficiente.

> [!IMPORTANT]
> Se estiver a instalar o conector no Windows Server 2019, existe uma limitação http2. Uma suver para utilizar o conector nesta versão está a adicionar a seguinte tecla de registo e reiniciar o servidor. Nota: esta é uma chave de registo de máquinas. 
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

#### <a name="recommendations-for-the-connector-server"></a>Recomendações para o servidor de conector

1. Localize fisicamente o servidor do conector perto dos servidores de aplicações para otimizar o desempenho entre o conector e a aplicação. Para mais informações, consulte [as considerações de topologia da Rede.](application-proxy-network-topology.md)
1. O servidor do conector e os servidores de aplicações web devem pertencer ao mesmo domínio de Diretório Ativo ou a domínios de confiança. Ter os servidores no mesmo domínio ou domínios de confiança é um requisito para a utilização de um único sign-on (SSO) com autenticação integrada do Windows (IWA) e Delegação Limitada kerberos (KCD). Se o servidor do conector e os servidores de aplicações web estiverem em diferentes domínios do Diretório Ativo, tem de utilizar uma delegação baseada em recursos para um único sinal. Para mais informações, consulte [o KCD para obter um único sinal com procuração de aplicação](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Se tiver implantado o Proxy de Proteção de Passwords Azure AD, não instale o Proxy de Aplicação AD Azure e o Proxy de Proteção de Passwords Azure AD juntos na mesma máquina. O Proxy de Aplicação AD Azure e o Proxy de Proteção de Passwords AD Azure instalam diferentes versões do serviço de atualização do agente de ligação ad ad. Estas diferentes versões são incompatíveis quando instaladas juntas na mesma máquina.

#### <a name="tls-requirements"></a>Requisitos tLS

O servidor do conector Windows precisa de ter o TLS 1.2 ativado antes de instalar o conector Proxy da aplicação.

Para ativar o TLS 1.2:

1. Detete as seguintes teclas de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie o servidor.

> [!IMPORTANT]
> Para fornecer a encriptação da melhor classe aos nossos clientes, o serviço Proxy de aplicação limita o acesso a apenas protocolos TLS 1.2. Estas alterações foram gradualmente lançadas e eficazes desde 31 de agosto de 2019. Certifique-se de que todas as combinações do servidor de clientes e servidores de navegador estão atualizadas para utilizar o TLS 1.2 para manter a ligação ao serviço proxy de aplicação. Estes incluem clientes que os seus utilizadores estão a usar para aceder a aplicações publicadas através do Application Proxy. Consulte a Preparação para [TLS 1.2 no Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) para obter referências e recursos úteis.

## <a name="prepare-your-on-premises-environment"></a>Prepare o seu ambiente no local

Comece por permitir a comunicação aos centros de dados do Azure para preparar o seu ambiente para a Procuração de Aplicação AD Azure. Se houver uma firewall no caminho, certifique-se de que está aberta. Uma firewall aberta permite ao conector fazer pedidos HTTPS (TCP) para o Proxy de Aplicação.

### <a name="open-ports"></a>Abrir portas

Abra os seguintes portos para o tráfego **de saída.**

   | Número da porta | Como é usado |
   | --- | --- |
   | 80 | Listas de revogação de certificados de descarregamento (CRLs) enquanto valida o certificado TLS/SSL |
   | 443 | Todas as comunicações de saída com o serviço Proxy de Aplicação |

Se a sua firewall impor o tráfego de acordo com os utilizadores originários, também abra as portas 80 e 443 para tráfego a partir de serviços Windows que funcionam como um Serviço de Rede.

### <a name="allow-access-to-urls"></a>Permitir o acesso aos URLs

Permitir o acesso aos seguintes URLs:

| do IdP | Como é usado |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem application Proxy |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O conector utiliza estes URLs para verificar os certificados. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*.microsoftonline.com<br>\*.microsoftonline-p.com<br>\*.msauth.net<br>\*.msauthimages.net<br>\*.msecnd.net<br>\*.msftauth.net<br>\*.msftauthimages.net<br>\*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | O conector utiliza estes URLs durante o processo de registo. |

Pode permitir ligações \*a \*.msappproxy.net e .servicebus.windows.net se a sua firewall ou proxy permitir configurar o DNS permitir listas. Caso contrário, tem de permitir o acesso às [gamas Azure IP e às etiquetas](https://www.microsoft.com/download/details.aspx?id=56519)de serviço - Nuvem Pública . As gamas IP são atualizadas todas as semanas.

## <a name="install-and-register-a-connector"></a>Instalar e registar um conector

Para utilizar o Proxy da Aplicação, instale um conector em cada servidor do Windows que esteja a utilizar com o serviço Proxy de Aplicação. O conector é um agente que gere a ligação de saída dos servidores de aplicações no local para o Application Proxy em Azure AD. Pode instalar um conector em servidores que também tenham outros agentes de autenticação instalados, como o Azure AD Connect.

Para instalar o conector:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador de candidatura sintetizador do diretório que utiliza o Application Proxy. Por exemplo, se o domínio do inquilino admin@contoso.com for contoso.com, o administrador deve ser ou qualquer outro pseudónimo administrativo nesse domínio.
1. Selecione o seu nome de utilizador no canto superior direito. Verifique se está inscrito num diretório que usa procuração de aplicação. Se precisar de mudar de diretórios, selecione **'Diretório' da Switch** e escolha um diretório que utilize proxy de aplicação.
1. No painel de navegação esquerdo, selecione **Azure Ative Directory**.
1. Em **'Gerir',** selecione **Proxy de Aplicação**.
1. Selecione **serviço de conector de descarregamento**.

    ![Descarregue o serviço de conector para ver os Termos de Serviço](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Leia os Termos de Serviço. Quando estiver pronto, **selecione Aceitar termos & Baixar**.
1. Na parte inferior da janela, selecione **Executar** para instalar o conector. Abre-se um assistente de instalação.
1. Siga as instruções do assistente para instalar o serviço. Quando lhe for solicitado que registe o conector com o Proxy de Aplicação para o seu inquilino Azure AD, forneça as credenciais do administrador de candidatura.
    - Para o Internet Explorer (IE), se a Configuração de **Segurança Melhorada do IE** estiver definida para o **on,** pode não ver o ecrã de registo. Para obter acesso, siga as instruções na mensagem de erro. Certifique-se de que a **configuração** de segurança melhorada do Internet Explorer está definida para **desligar**.

### <a name="general-remarks"></a>Observações gerais

Se já instalou um conector, reinstale para obter a versão mais recente. Para ver informações sobre versões previamente lançadas e quais as alterações que incluem, consulte [Application Proxy: Version Release History](application-proxy-release-version-history.md).

Se optar por ter mais do que um servidor Windows para as suas aplicações no local, terá de instalar e registar o conector em cada servidor. Pode organizar os conectores em grupos de conectores. Para mais informações, consulte [os grupos Connector](application-proxy-connector-groups.md).

Se a sua organização utilizar servidores proxy para se conectar à internet, precisa configurá-los para Proxy de Aplicação.  Para mais informações, consulte [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md). 

Para obter informações sobre conectores, planeamento de capacidades e como se mantêm atualizados, consulte [conectores de procuração de aplicação ad -, compreenda Azure AD Application Proxy](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifique o conector instalado e registado corretamente

Pode utilizar o portal Azure ou o seu servidor Windows para confirmar que um novo conector foi instalado corretamente.

### <a name="verify-the-installation-through-azure-portal"></a>Verifique a instalação através do portal Azure

Para confirmar o conector instalado e registado corretamente:

1. Inscreva-se no seu diretório de inquilinos no [portal Azure.](https://portal.azure.com)
1. No painel de navegação esquerdo, selecione **Azure Ative Directory**e, em seguida, selecione **Application Proxy** sob a secção **Gerir.** Todos os seus conectores e grupos de conectores aparecem nesta página.
1. Consulte um conector para verificar os seus detalhes. Os conectores devem ser expandidos por defeito. Se o conector que pretende ver não estiver expandido, expanda o conector para visualizar os detalhes. Uma etiqueta verde ativa indica que o conector pode ligar-se ao serviço. No entanto, apesar de a etiqueta ser verde, um problema de rede ainda pode impedir o conector de receber mensagens.

    ![Conectores de proxy de aplicação da AD Azure](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Para obter mais ajuda na instalação de um conector, consulte [problema de instalação do Conector proxy](application-proxy-connector-installation-problem.md)da aplicação .

### <a name="verify-the-installation-through-your-windows-server"></a>Verifique a instalação através do seu servidor Windows

Para confirmar o conector instalado e registado corretamente:

1. Abra o Gestor de Serviços windows clicando na chave **Windows** e inserindo *serviços.msc*.
1. Verifique se o estado dos dois seguintes serviços está **em execução**.
   - O **Conector proxy da aplicação Microsoft AAD** permite conectividade.
   - O **AAD Application Proxy Updater da Microsoft AAD** é um serviço de atualização automatizado. O atualizador verifica as novas versões do conector e atualiza o conector conforme necessário.

     ![Serviços do Conector do Proxy da Aplicação – captura de ecrã](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Se o estado dos serviços não estiver **em execução,** clique no clique direito para selecionar cada serviço e escolher **Iniciar**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Adicione uma aplicação no local ao Azure AD

Agora que preparou o seu ambiente e instalou um conector, está pronto para adicionar aplicações no local ao Azure AD.  

1. Inscreva-se como administrador no [portal Azure.](https://portal.azure.com/)
2. No painel de navegação esquerdo, selecione **Azure Ative Directory**.
3. Selecione **aplicações Enterprise**e, em seguida, selecione **Nova aplicação**.
4. Na secção de **aplicações No local,** selecione **Adicionar uma aplicação no local**.
5. Na secção **de aplicação Adicionar no local,** forneça as seguintes informações sobre a sua aplicação:

    | Campo | Descrição |
    | :---- | :---------- |
    | **Nome** | O nome da aplicação que vai aparecer no painel de acesso e no portal Azure. |
    | **URL interno** | O URL para aceder à aplicação a partir de dentro da sua rede privada. Pode fornecer um caminho específico no servidor de back-end para publicação, enquanto o resto do servidor não é publicado. Desta forma, pode publicar diferentes sites no mesmo servidor que diferentes aplicações, e dar a cada um o seu próprio nome e regras de acesso.<br><br>Se publicar um caminho, certifique-se de que inclui todas as imagens, scripts e folhas de estilo necessários para a sua aplicação. Por exemplo, se a sua\/aplicação estiver em https: /yourapp/app e utilizar imagens localizadas em https:\//yourapp/media, então deve publicar https:\//yourapp/ as the path. Este URL interno não tem de ser a página de aterragem que os utilizadores vêem. Para mais informações, consulte [Definir uma página inicial personalizada para aplicações publicadas.](application-proxy-configure-custom-home-page.md) |
    | **URL Externo** | O endereço para os utilizadores acederem à aplicação de fora da sua rede. Se não quiser utilizar o domínio proxy de aplicação predefinido, leia sobre [domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md).|
    | **Pré-Autenticação** | Como o Proxy de Aplicação verifica os utilizadores antes de lhes dar acesso à sua aplicação.<br><br>**Azure Ative Directory** - Application Proxy redireciona os utilizadores para iniciars a sua participação na Azure AD, que autentica as suas permissões para o diretório e aplicação. Recomendamos que mantenha esta opção como padrão para que possa usufruir de funcionalidades de segurança da AD Azure, como acesso condicional e autenticação multi-factor. **O Diretório Ativo Azure** é necessário para monitorizar a aplicação com a Microsoft Cloud Application Security.<br><br>**Passthrough** - Os utilizadores não têm de autenticar contra a AD Azure para aceder à aplicação. Ainda pode configurar requisitos de autenticação no backend. |
    | **Grupo conector** | Os conectores processam o acesso remoto à sua aplicação, e os grupos de conectores ajudam-no a organizar conectores e aplicações por região, rede ou finalidade. Se ainda não tiver nenhum grupo de conector criado, a sua aplicação está atribuída ao **Padrão**.<br><br>Se a sua aplicação utilizar WebSockets para ligar, todos os conectores do grupo devem ser da versão 1.5.612.0 ou posterior.|

6. Se necessário, configure **configurar configurações adicionais**. Para a maioria das aplicações, deve manter estas definições nos seus estados padrão. 

    | Campo | Descrição |
    | :---- | :---------- |
    | **Timeout de aplicação de backend** | Derete este valor para **Long** apenas se a sua aplicação for lenta para autenticar e ligar. No padrão, o tempo de aplicação backend tem um comprimento de 85 segundos. Quando programado para longo prazo, o tempo de paragem é aumentado para 180 segundos. |
    | **Utilizar cookie apenas http** | Defina este valor para **Sim** para ter cookies proxy de aplicação incluem a bandeira HTTPOnly no cabeçalho de resposta HTTP. Se utilizar os Serviços de Ambiente de Trabalho Remoto, detete este valor para **Nº**.|
    | **Use cookie seguro**| Defina este valor para **Sim** para transmitir cookies num canal seguro, como um pedido HTTPS encriptado.
    | **Use cookie persistente**| Mantenha este valor definido para **Nº**. Utilize apenas esta definição para aplicações que não possam partilhar cookies entre processos. Para obter mais informações sobre as definições de cookies, consulte [as definições de Cookie para aceder às aplicações no local no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Traduzir URLs em Cabeçalhos** | Mantenha este valor como **Sim,** a menos que a sua aplicação exigisse o cabeçalho original do anfitrião no pedido de autenticação. |
    | **Traduzir URLs no Corpo de Aplicação** | Mantenha este valor como **Não,** a menos que tenha ligações HTML codificadas a outras aplicações no local e não utilize domínios personalizados. Para mais informações, consulte [a tradução de Link com procuração de aplicação](application-proxy-configure-hard-coded-link-translation.md).<br><br>Detete este valor para **Sim** se planeia monitorizar esta aplicação com o Microsoft Cloud App Security (MCAS). Para mais informações, consulte a monitorização do acesso à aplicação em tempo real com o [Microsoft Cloud App Security e o Azure Ative Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Selecione **Adicionar**.

## <a name="test-the-application"></a>Testar a aplicação

Está pronto para testar a aplicação corretamente adicionada. Nos seguintes passos, irá adicionar uma conta de utilizador à aplicação e tentar iniciar sessão.

### <a name="add-a-user-for-testing"></a>Adicione um utilizador para testes

Antes de adicionar um utilizador à aplicação, verifique se a conta de utilizador já tem permissões para aceder à aplicação a partir de dentro da rede corporativa.

Para adicionar um utilizador de teste:

1. Selecione **aplicações Enterprise**e, em seguida, selecione a aplicação que pretende testar.
2. Selecione **Iniciar a partida**e, em seguida, selecione Atribuir um utilizador para **teste**.
3. Em **Utilizadores e grupos,** selecione **Adicionar utilizador**.
4. Sob **a tarefa Adicionar,** selecione **Utilizadores e grupos**. Aparece a secção **user e grupos.**
5. Escolha a conta que pretende adicionar.
6. Escolha **Selecionar**e, em seguida, selecione **Atribuir**.

### <a name="test-the-sign-on"></a>Teste o sinal on

Para testar a inscrição na aplicação:

1. A partir da aplicação que pretende testar, selecione **Application Proxy**.
2. No topo da página, selecione **Aplicação de Teste** para eexecutar um teste na aplicação e verificar se há quaisquer problemas de configuração.
3. Certifique-se de lançar primeiro a aplicação para testar a assinatura na aplicação e, em seguida, fazer o download do relatório de diagnóstico para rever as orientações de resolução para quaisquer problemas detetados.

Para resolução de problemas, consulte problemas de [procuração de aplicação de resolução de problemas e mensagens de erro](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, preparou o seu ambiente no local para trabalhar com o Application Proxy e, em seguida, instalou e registou o conector Proxy de Aplicação. Em seguida, adicionou um pedido ao seu inquilino da AD Azure. Verificou que um utilizador pode iniciar sessão na aplicação utilizando uma conta Azure AD.

Fez tudo isto:
> [!div class="checklist"]
> * Portas abertas para tráfego de saída e permitiu o acesso a URLs específicos
> * Instalou o conector no seu servidor Windows e registou-o com procuração de aplicação
> * Verificado o conector instalado e registado corretamente
> * Adicionou um pedido no local ao seu inquilino da AD Azure
> * Verificado um utilizador de teste pode iniciar sessão na aplicação utilizando uma conta Azure AD

Está pronto para configurar o pedido de inscrição individual. Utilize o seguinte link para escolher um único método de inscrição e para encontrar tutoriais de inscrição simples.

> [!div class="nextstepaction"]
> [Configurar o início de sessão único](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
