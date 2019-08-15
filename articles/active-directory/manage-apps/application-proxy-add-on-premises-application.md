---
title: Adicionar um proxy de aplicativo de aplicativo local no Azure Active Directory | Microsoft Docs
description: O Azure Active Directory (AD do Azure) tem um serviço de proxy de aplicativo que permite aos usuários acessar aplicativos locais ao entrar com sua conta do Azure AD. Este tutorial mostra como preparar seu ambiente para uso com o proxy de aplicativo. Em seguida, ele usa o portal do Azure para adicionar um aplicativo local ao seu locatário do Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: aefa6d21488b617b26ddefe5fa4fc61cdd203f96
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032530"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory

O Azure Active Directory (AD do Azure) tem um serviço de proxy de aplicativo que permite aos usuários acessar aplicativos locais ao entrar com sua conta do Azure AD. Este tutorial prepara seu ambiente para uso com o proxy de aplicativo. Quando seu ambiente estiver pronto, você usará o portal do Azure para adicionar um aplicativo local ao seu locatário do Azure AD.

Este tutorial permite:

> [!div class="checklist"]
> * Abre portas para tráfego de saída e permite o acesso a URLs específicas
> * Instala o conector no seu Windows Server e o registra com o proxy de aplicativo
> * Verifica o conector instalado e registrado corretamente
> * Adiciona um aplicativo local ao seu locatário do Azure AD
> * Verifica se um usuário de teste pode fazer logon no aplicativo usando uma conta do Azure AD

## <a name="before-you-begin"></a>Antes de começar

Para adicionar um aplicativo local ao Azure AD, você precisará de:

* Uma [assinatura Microsoft Azure ad Basic ou Premium](https://azure.microsoft.com/pricing/details/active-directory)
* Uma conta de administrador de aplicativos
* As identidades de usuário devem ser sincronizadas de um diretório local ou criadas diretamente em seus locatários do Azure AD. Sincronização de Identidades permite que o Azure AD autentique previamente os usuários antes de conceder a eles acesso aos aplicativos publicados do proxy de aplicativo e ter as informações necessárias do identificador de usuário para executar o logon único (SSO).

### <a name="windows-server"></a>Servidor do Windows

Para usar o proxy de aplicativo, você precisa de um Windows Server que executa o Windows Server 2012 R2 ou posterior. Você instalará o conector do proxy de aplicativo no servidor. Esse servidor do conector precisa se conectar aos serviços de proxy de aplicativo no Azure e aos aplicativos locais que você planeja publicar.

Para alta disponibilidade em seu ambiente de produção, recomendamos ter mais de um Windows Server. Para este tutorial, um Windows Server é suficiente.

#### <a name="recommendations-for-the-connector-server"></a>Recomendações para o servidor do conector

1. Localize fisicamente o servidor do conector próximo aos servidores de aplicativos para otimizar o desempenho entre o conector e o aplicativo. Para obter mais informações, consulte [considerações de topologia de rede](application-proxy-network-topology.md).
1. O servidor do conector e os servidores de aplicativos Web devem pertencer ao mesmo domínio Active Directory ou abranger Domínios confiantes. Ter os servidores no mesmo domínio ou domínios confiantes é um requisito para usar o logon único (SSO) com autenticação integrada do Windows (IWA) e KCD (delegação restrita de Kerberos). Se o servidor do conector e os servidores de aplicativos Web estiverem em domínios de Active Directory diferentes, você precisará usar a delegação baseada em recursos para logon único. Para obter mais informações, consulte [KCD para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="tls-requirements"></a>Requisitos de TLS

O servidor do Windows Connector precisa ter o TLS 1,2 habilitado antes de instalar o conector do proxy de aplicativo.

Para ativar o TLS 1.2:

1. Defina as seguintes chaves de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie o servidor.

> [!IMPORTANT]
> Para fornecer a melhor criptografia para nossos clientes, estamos fazendo atualizações no serviço de proxy de aplicativo para limitar o acesso a apenas protocolos TLS 1,2. Com base nas alterações de preparação do cliente, elas serão distribuídas gradualmente para clientes que usam apenas protocolos TLS 1,2 e não verão nenhum impacto dessa alteração. A substituição de TLS 1,0 e 1,1 será concluída em 31 de agosto de 2019 e os clientes receberão um aviso antecipado para se preparar para essa alteração. Para se preparar para essa alteração, certifique-se de que todas as combinações de servidor cliente e navegador-servidor sejam atualizadas para usar o TLS 1,2 para manter a conexão com o serviço de proxy de aplicativo. Isso inclui clientes que os usuários estão usando para acessar aplicativos publicados por meio do proxy de aplicativo. Consulte preparando para o [TLS 1,2 no Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) para obter referências e recursos úteis.

## <a name="prepare-your-on-premises-environment"></a>Preparar seu ambiente local

Comece habilitando a comunicação com os data centers do Azure para preparar seu ambiente para o Azure Proxy de Aplicativo do AD. Se houver um firewall no caminho, verifique se ele está aberto. Um firewall aberto permite que o conector faça solicitações de HTTPS (TCP) para o proxy de aplicativo.

### <a name="open-ports"></a>Abrir portas

Abra as portas a seguir para o tráfego de **saída** .

   | Número da porta | Como é usado |
   | --- | --- |
   | 80 | Baixando listas de certificados revogados (CRLs) ao validar o certificado SSL |
   | 443 | Toda a comunicação de saída com o serviço de proxy de aplicativo |

Se o firewall impõe o tráfego de acordo com os usuários de origem, abra as portas 80 e 443 para o tráfego dos serviços do Windows que são executados como um serviço de rede.

### <a name="allow-access-to-urls"></a>Permitir acesso a URLs

Permitir acesso às seguintes URLs:

| URL | Como é usado |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem do proxy de aplicativo |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O Azure usa essas URLs para verificar os certificados. |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | O conector usa essas URLs durante o processo de registro. |

Você pode permitir conexões com \*. msappproxy.net e \*. ServiceBus.Windows.net se o firewall ou o proxy permitir que você configure listas de permissões de DNS. Caso contrário, você precisará permitir o acesso aos [intervalos de IP e marcas de serviço do Azure-nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519). Os intervalos de IP são atualizados a cada semana.

## <a name="install-and-register-a-connector"></a>Instalar e registrar um conector

Para usar o proxy de aplicativo, instale um conector em cada servidor Windows que você está usando com o serviço de proxy de aplicativo. O conector é um agente que gerencia a conexão de saída dos servidores de aplicativos locais para o proxy de aplicativo no Azure AD. Você pode instalar um conector em servidores que também têm outros agentes de autenticação instalados, como Azure AD Connect.

Para instalar o conector:

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de aplicativos do diretório que usa o proxy de aplicativo. Por exemplo, se o domínio do locatário for contoso.com, o administrador deverá admin@contoso.com ser ou qualquer outro alias de administrador nesse domínio.
1. Selecione seu nome de usuário no canto superior direito. Verifique se você está conectado a um diretório que usa o proxy de aplicativo. Se você precisar alterar os diretórios, selecione **alternar diretório** e escolha um diretório que usa o proxy de aplicativo.
1. No painel de navegação esquerdo, selecione **Azure Active Directory**.
1. Em **gerenciar**, selecione **proxy de aplicativo**.
1. Selecione **baixar serviço do conector**.

    ![Baixar o serviço do conector para ver os termos de serviço](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Leia os termos de serviço. Quando estiver pronto, selecione **aceitar termos & baixar**.
1. Na parte inferior da janela, selecione **executar** para instalar o conector. Um assistente de instalação é aberto.
1. Siga as instruções no Assistente para instalar o serviço. Quando for solicitado que você registre o conector com o proxy de aplicativo para seu locatário do Azure AD, forneça suas credenciais de administrador do aplicativo.
    - Para o Internet Explorer (IE), se a **configuração de segurança** reforçada do IE estiver definida como **ativada**, talvez você não veja a tela de registro. Para obter acesso, siga as instruções na mensagem de erro. Verifique se a **configuração de segurança reforçada do Internet Explorer** está definida como **desativado**.

### <a name="general-remarks"></a>Comentários gerais

Se você já tiver instalado um conector, reinstale-o para obter a versão mais recente. Para ver informações sobre versões lançadas anteriormente e quais alterações elas incluem, [consulte proxy de aplicativo: Histórico](application-proxy-release-version-history.md)de lançamento de versão.

Se você optar por ter mais de um Windows Server para seus aplicativos locais, será necessário instalar e registrar o conector em cada servidor. Você pode organizar os conectores em grupos de conectores. Para obter mais informações, consulte [grupos](application-proxy-connector-groups.md)de conectores.

Se sua organização usa servidores proxy para se conectar à Internet, você precisa configurá-los para o proxy de aplicativo.  Para obter mais informações, consulte [trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md). 

Para obter informações sobre conectores, planejamento de capacidade e como eles permanecem atualizados, consulte entender os [conectores de proxy de aplicativo do AD do Azure](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifique se o conector foi instalado e registrado corretamente

Você pode usar o portal do Azure ou o Windows Server para confirmar se um novo conector foi instalado corretamente.

### <a name="verify-the-installation-through-azure-portal"></a>Verifique a instalação por meio do portal do Azure

Para confirmar se o conector foi instalado e registrado corretamente:

1. Entre no seu diretório de locatário no [portal do Azure](https://portal.azure.com).
1. No painel de navegação esquerdo, selecione **Azure Active Directory**e, em seguida, selecione **proxy de aplicativo** na seção **gerenciar** . Todos os seus conectores e grupos de conectores aparecem nesta página.
1. Exiba um conector para verificar seus detalhes. Os conectores devem ser expandidos por padrão. Se o conector que você deseja exibir não estiver expandido, expanda o conector para exibir os detalhes. Um rótulo verde ativo indica que o conector pode se conectar ao serviço. No entanto, embora o rótulo esteja verde, um problema de rede ainda pode impedir que o conector receba mensagens.

    ![Conectores de Proxy de Aplicativo do AD do Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

Para obter mais ajuda com a instalação de um conector, consulte [problema ao instalar o conector de proxy de aplicativo](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verifique a instalação por meio do Windows Server

Para confirmar se o conector foi instalado e registrado corretamente:

1. Abra o Gerenciador de serviços do Windows clicando na tecla **Windows** e inserindo *Services. msc*.
1. Verifique se o status dos dois serviços a seguir está **em execução**.
   - **O conector de proxy de aplicativo do Microsoft AAD** permite a conectividade.
   - **Atualizador do conector de proxy de aplicativo do Microsoft AAD** é um serviço de atualização automatizado. O atualizador verifica se há novas versões do conector e atualiza o conector conforme necessário.

     ![Serviços do Conector do Proxy da Aplicação – captura de ecrã](./media/application-proxy-enable/app_proxy_services.png)

1. Se o status dos serviços não estiver **em execução**, clique com o botão direito do mouse para selecionar cada serviço e escolha **Iniciar**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Adicionar um aplicativo local ao Azure AD

Agora que você preparou seu ambiente e instalou um conector, você está pronto para adicionar aplicativos locais ao Azure AD.  

1. Entre como administrador no [portal do Azure](https://portal.azure.com/).
1. No painel de navegação esquerdo, selecione **Azure Active Directory**.
1. Selecione **aplicativos empresariais**e, em seguida, selecione **novo aplicativo**.
1. Selecione **aplicação no local**.  
1. Na seção **Adicionar seu próprio aplicativo local** , forneça as seguintes informações sobre seu aplicativo:

    | Campo | Descrição |
    | :---- | :---------- |
    | **Name** | O nome do aplicativo que será exibido no painel de acesso e no portal do Azure. |
    | **URL interna** | A URL para acessar o aplicativo de dentro de sua rede privada. Pode fornecer um caminho específico no servidor de back-end para publicação, enquanto o resto do servidor não é publicado. Dessa forma, você pode publicar diferentes sites no mesmo servidor como aplicativos diferentes e dar a cada um deles seu próprio nome e suas regras de acesso.<br><br>Se publicar um caminho, certifique-se de que inclui todas as imagens, scripts e folhas de estilo necessários para a sua aplicação. Por exemplo, se seu aplicativo estiver em https:\//yourapp/app e usar imagens localizadas em https\/:/yourapp/Media, você deverá publicar https:\//yourapp/como o caminho. Essa URL interna não precisa ser a página de aterrissagem que os usuários veem. Para obter mais informações, consulte [definir um Home Page personalizado para aplicativos publicados](application-proxy-configure-custom-home-page.md). |
    | **URL externo** | O endereço para os usuários acessarem o aplicativo de fora da rede. Se você não quiser usar o domínio do proxy de aplicativo padrão, leia sobre [domínios personalizados no Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md).|
    | **Pré-autenticação** | Como o proxy de aplicativo verifica os usuários antes de conceder a eles acesso ao seu aplicativo.<br><br>**Azure Active Directory** -o proxy de aplicativo redireciona os usuários para entrar com o Azure AD, que autentica suas permissões para o diretório e o aplicativo. É recomendável manter essa opção como o padrão para que você possa aproveitar os recursos de segurança do Azure AD como o acesso condicional e a autenticação multifator. **Azure Active Directory** é necessário para monitorar o aplicativo com Microsoft Cloud segurança de aplicativo.<br><br>**Passagem** -os usuários não precisam se autenticar no Azure ad para acessar o aplicativo. Você ainda pode configurar os requisitos de autenticação no back-end. |
    | **Grupo de conectores** | Os conectores processam o acesso remoto ao seu aplicativo, e os grupos de conectores ajudam a organizar os conectores e aplicativos por região, rede ou finalidade. Se você ainda não tiver grupos de conectores criados, seu aplicativo será atribuído ao **padrão**.<br><br>Se seu aplicativo usa Websockets para se conectar, todos os conectores no grupo devem ser da versão 1.5.612.0 ou posterior.|

1. Se necessário, defina **configurações adicionais**. Para a maioria dos aplicativos, você deve manter essas configurações em seus Estados padrão. 

    | Campo | Descrição |
    | :---- | :---------- |
    | **Tempo limite do aplicativo de back-end** | Defina esse valor para **Long** somente se o aplicativo estiver lento para autenticar e se conectar. No padrão, o tempo limite do aplicativo de back-end tem um comprimento de 85 segundos. Quando definido como longo, o tempo limite de back-end é aumentado para 180 segundos. |
    | **Usar cookie somente HTTP** | Defina esse valor como **Sim** para que os cookies do proxy de aplicativo incluam o sinalizador HTTPOnly no cabeçalho de resposta http. Se estiver usando Serviços de Área de Trabalho Remota, defina esse valor como **não**.|
    | **Usar cookie seguro**| Defina esse valor como **Sim** para transmitir cookies por um canal seguro, como uma solicitação HTTPS criptografada.
    | **Usar cookie persistente**| Mantenha esse valor definido como **não**. Use essa configuração somente para aplicativos que não podem compartilhar cookies entre processos. Para obter mais informações sobre configurações de cookie, consulte [configurações de cookie para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Converter URLs em cabeçalhos** | Mantenha esse valor como **Sim** , a menos que seu aplicativo precise do cabeçalho de host original na solicitação de autenticação. |
    | **Traduzir URLs no corpo do aplicativo** | Mantenha esse valor como **não** , a menos que você tenha codificado os links HTML para outros aplicativos locais e não use domínios personalizados. Para obter mais informações, consulte [conversão de link com o proxy de aplicativo](application-proxy-configure-hard-coded-link-translation.md).<br><br>Defina esse valor como **Sim** se você planeja monitorar esse aplicativo com Microsoft Cloud app Security (MCAS). Para obter mais informações, consulte [Configurar o monitoramento de acesso do aplicativo em tempo real com Microsoft Cloud app Security e Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

1. Selecione **Adicionar**.

## <a name="test-the-application"></a>Testar a aplicação

Você está pronto para testar se o aplicativo foi adicionado corretamente. Nas etapas a seguir, você adicionará uma conta de usuário ao aplicativo e tentará entrar.

### <a name="add-a-user-for-testing"></a>Adicionar um usuário para teste

Antes de adicionar um usuário ao aplicativo, verifique se a conta de usuário já tem permissões para acessar o aplicativo de dentro da rede corporativa.

Para adicionar um usuário de teste:

1. Selecione **aplicativos empresariais**e, em seguida, selecione o aplicativo que você deseja testar.
1. Selecione **Guia de introdução**e, em seguida, selecione **atribuir um usuário para teste**.
1. Em **usuários e grupos**, selecione **Adicionar usuário**.
1. Em **Adicionar atribuição**, selecione **usuários e grupos**. A seção **usuário e grupos** é exibida.
1. Escolha a conta que você deseja adicionar.
1. Escolha **selecionar**e, em seguida, selecione **atribuir**.

### <a name="test-the-sign-on"></a>Testar o logon

Para testar o logon no aplicativo:

1. No navegador, navegue até a URL externa que você configurou durante a etapa de publicação. Você deve ver a tela iniciar.
1. Entre como o usuário que você criou na seção anterior.

Para solucionar problemas, consulte solucionar problemas [de proxy de aplicativo e mensagens de erro](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você preparou seu ambiente local para trabalhar com o proxy de aplicativo e, em seguida, instalou e registrou o conector de proxy de aplicativo. Em seguida, você adicionou um aplicativo ao seu locatário do Azure AD. Você verificou que um usuário pode fazer logon no aplicativo usando uma conta do Azure AD.

Fez tudo isto:
> [!div class="checklist"]
> * Portas abertas para tráfego de saída e acesso permitido a URLs específicas
> * Instalou o conector no seu Windows Server e o registrou com o proxy de aplicativo
> * Verificado se o conector foi instalado e registrado corretamente
> * Adicionado um aplicativo local ao seu locatário do Azure AD
> * Verificado que um usuário de teste pode fazer logon no aplicativo usando uma conta do Azure AD

Você está pronto para configurar o aplicativo para logon único. Use o link a seguir para escolher um método de logon único e localizar tutoriais de logon único.

> [!div class="nextstepaction"]
> [Configure single sign-on](what-is-single-sign-on.md#choosing-a-single-sign-on-method) (Configurar o início de sessão único)
