---
title: Adicionar uma aplicação no local - Proxy de aplicações no Azure Active Directory | Documentos da Microsoft
description: Azure Active Directory (Azure AD) tem um serviço de Proxy de aplicações que permite aos utilizadores aceder a aplicações no local ao iniciar sessão com a respetiva conta do Azure AD. Este tutorial mostra como preparar o ambiente para utilização com o Proxy de aplicações. Em seguida, utiliza o portal do Azure para adicionar uma aplicação no local com o seu inquilino do Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11bb99ada76131000f49be5a1216a1bb71fbb88a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108718"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações no Azure Active Directory

Azure Active Directory (Azure AD) tem um serviço de Proxy de aplicações que permite aos utilizadores aceder a aplicações no local ao iniciar sessão com a respetiva conta do Azure AD. Este tutorial prepara o ambiente para utilização com o Proxy de aplicações. Assim que o seu ambiente está pronto, usará o portal do Azure para adicionar uma aplicação no local com o seu inquilino do Azure AD.

Este tutorial permite:

> [!div class="checklist"]
> * Abre as portas para tráfego de saída e permite o acesso a URLs específicos
> * Instala o conector no seu servidor do Windows e regista-o com o Proxy de aplicações
> * Verifica se o conector instalado e registado corretamente
> * Adiciona uma aplicação no local com o seu inquilino do Azure AD
> * Verifica se que um utilizador de teste pode iniciar sessão na aplicação utilizando uma conta do Azure AD

## <a name="before-you-begin"></a>Antes de começar

Para adicionar uma aplicação no local para o Azure AD, é necessário:

* A [subscrição basic ou premium do Microsoft Azure AD](https://azure.microsoft.com/pricing/details/active-directory)
* Uma conta de administrador da aplicação
* Identidades de utilizador tem de ser sincronizadas a partir de um diretório no local ou criadas diretamente dentro de seus inquilinos do Azure AD. Sincronização de identidade permite que o Azure AD para autenticar previamente os utilizadores antes de lhes conceder acesso ao Proxy de aplicação aplicações publicadas e para que as informações de identificador de utilizador necessários para efetuar logon único (SSO).

### <a name="windows-server"></a>Servidor do Windows

Para utilizar o Proxy de aplicações, precisa de um servidor do Windows com o Windows Server 2012 R2 ou posterior. Instalará o conector do Proxy de aplicações no servidor. Este servidor do conector tem de ligar a serviços de Proxy de aplicações no Azure e as aplicações no local que pretende publicar.

Para elevada disponibilidade no seu ambiente de produção, é recomendável ter mais de um servidor do Windows. Para este tutorial, um servidor do Windows é suficiente.

#### <a name="recommendations-for-the-connector-server"></a>Recomendações para o servidor do conector

1. Fisicamente, localize o servidor do conector perto os servidores de aplicações para otimizar o desempenho entre o conector e a aplicação. Para obter mais informações, consulte [considerações sobre a topologia de rede](application-proxy-network-topology.md).

2. O servidor do conector e os servidores de aplicativos da web devem pertencer ao mesmo domínio do Active Directory ou span domínios fidedignas. Ter os servidores no mesmo domínio ou domínios de confiança é um requisito para utilizar o início de sessão único (SSO) com a autenticação integrada do Windows (IWA) e Kerberos Constrained Delegation (KCD). Se o servidor do conector e a servidores de aplicações web estiverem em domínios diferentes do Active Directory, terá de utilizar a delegação baseada em recursos para início de sessão único. Para obter mais informações, consulte [KCD para início de sessão único com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="tls-requirements"></a>Requisitos de TLS

O servidor de conector do Windows tem de ter o TLS 1.2, ativado antes de instalar o conector do Proxy de aplicações.

Para ativar o TLS 1.2:

1. Defina as seguintes chaves de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Reinicie o servidor.

>[!Important] 
> Para fornecer a criptografia de melhor na classe aos nossos clientes, estamos a efetuar atualizações para o serviço de Proxy de aplicações para limitar o acesso a apenas os protocolos TLS 1.2. Com base em preparação para o cliente alterações serão gradualmente implementadas para os clientes que só esteja usando protocolos de TLS 1.2 e não verá nenhum impacto esta alteração. O TLS 1.0 e 1.1 preterição serão concluída em 31 de Agosto de 2019 e os clientes receberão prévio para se preparar para esta alteração. Para se preparar para esta alteração, certifique-se que todas as combinações de servidor de cliente e servidor de navegador são atualizadas para utilizar TLS 1.2 para manter a ligação ao serviço de Proxy de aplicações. Estes incluem os clientes que os utilizadores utilizam para aceder a aplicações publicadas através do Proxy de aplicações. Consulte preparando [TLS 1.2 no Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) para útil referências e recursos.

## <a name="prepare-your-on-premises-environment"></a>Preparar o ambiente no local

Comece por ativar a comunicação a centros de dados do Azure para preparar o ambiente para o Proxy de aplicações do Azure AD. Se existir uma firewall no caminho, certifique-se de está aberto. Um firewall aberta permite que o conector para fazer pedidos HTTPS (TCP) para o Proxy de aplicações.

### <a name="open-ports"></a>Abrir portas

Abrir as seguintes portas **saída** tráfego. 

   | Número da porta | Como são utilizadas |
   | --- | --- |
   | 80 | Baixar a revogação de certificados apresenta uma lista (CRL) ao validar o certificado SSL |
   | 443 | Todas as comunicações de saída com o serviço de Proxy de aplicações |

Se a firewall impuser tráfego de acordo com os utilizadores de origem, também abra as portas 80 e 443 para tráfego dos serviços do Windows que são executados como um serviço de rede.

Poderá ter uma versão mais antiga do conector instalado se já estiver a utilizar o Proxy de aplicações. Siga este tutorial para instalar a versão mais recente do conector. Versões anteriores ao 1.5.132.0 também necessitam de que portas de abrir o seguinte: 5671, 8080, 9090-9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>Permitir o acesso aos URLs

Permitir o acesso aos seguintes URLs:

| do IdP | Como são utilizadas |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem do Proxy de aplicações |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O Azure utiliza estes URLs para verificar os certificados. |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | O conector utiliza estes URLs durante o processo de registo. |

É possível permitir ligações ao \*. msappproxy.net e \*. servicebus.windows.net se a sua firewall ou proxy permite-lhe configurar o DNS permitir listas. Se não, precisa permitir o acesso para o [intervalos de IP de DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Os intervalos de IP são atualizados a cada semana.

## <a name="install-and-register-a-connector"></a>Instalar e registar um conector

Para utilizar o Proxy de aplicações, instale um conector em cada servidor do Windows que estiver a utilizar com o serviço de Proxy de aplicações. O conector é um agente que gere a ligação de saída dos servidores de aplicações no local para o Proxy de aplicações no Azure AD. Pode instalar um conector em servidores que têm também de outros agentes de autenticação instalados, como o Azure AD Connect.

Para instalar o conector:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) como um administrador da aplicação do diretório que utiliza o Proxy de aplicações. Por exemplo, se o domínio de inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias de administrador nesse domínio.
2. Selecione o seu nome de utilizador no canto superior direito. Certifique-se de que tem sessão iniciada num diretório que utiliza o Proxy de aplicações. Se precisar de mude de diretório, selecione **trocar diretório** e escolha um diretório que utiliza o Proxy de aplicações.
3. No painel de navegação esquerdo, selecione **do Azure Active Directory**. 
4. Sob **Manage**, selecione **proxy de aplicações**.
5. Selecione **transferir o conector serviço**.
    
    ![Transferir o serviço do conector](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

6. Leia os termos de serviço.  Quando estiver pronto, selecione **aceitar termos e transferir**.
7. Na parte inferior da janela, selecione **executar** para instalar o conector. É aberto um Assistente de instalação. 
8. Siga as instruções no Assistente para instalar o serviço. Quando lhe for pedido para registar o conector com o Proxy de aplicações para o seu inquilino do Azure AD, forneça as credenciais de administrador do aplicativo.
    - Para o Internet Explorer (IE), se **configuração de segurança avançada do IE** está definida como **no**, poderá não ver o ecrã de registo. Para obter acesso, siga as instruções na mensagem de erro. Certifique-se de que **a configuração de segurança avançada do Internet Explorer** está definida como **desativar**.

### <a name="general-remarks"></a>Observações gerais

Se instalou anteriormente um conector, reinstale-se para obter a versão mais recente. Para ver informações sobre versões anteriores e que alterações elas incluem, consulte [Proxy de aplicações: Histórico de versões](application-proxy-release-version-history.md).

Se optar por mais de um servidor do Windows para as suas aplicações no local, terá de instalar e registar o conector em cada servidor. Pode organizar os conectores em grupos de conector. Para obter mais informações, consulte [grupos de conectores](application-proxy-connector-groups.md). 

Se a sua organização utilizar servidores proxy para estabelecer ligação à internet, terá de configurá-los para o Proxy de aplicações.  Para obter mais informações, consulte [funcionam com o existente no local servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md). 

Para obter informações sobre conectores, planejamento de capacidade e como eles se manter atualizados, consulte [conectores de Proxy de aplicações do AD Azure compreender](application-proxy-connectors.md). 


## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifique se o conector instalado e registado corretamente

Pode utilizar o portal do Azure ou no seu servidor do Windows para confirmar que um novo conector é instalado corretamente.

### <a name="verify-the-installation-through-azure-portal"></a>Verificar a instalação através do portal do Azure

Para confirmar o conector instalado e registado corretamente:

1. Inicie sessão no seu diretório do inquilino no [portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, selecione **do Azure Active Directory**e, em seguida, selecione **Proxy de aplicações** sob o **gerir** secção. Todos os seus conectores e os grupos de conexão são apresentados nesta página. 
3. Ver um conector para verificar os detalhes. Os conectores deverão estar expandidos por predefinição. Se o conector que pretende ver não estiver expandido, expanda o conector para ver os detalhes. Uma Active Directory etiqueta verde indica que o conector pode ligar ao serviço. No entanto, mesmo que a etiqueta for verde, um problema de rede ainda pode bloquear o conector de recebimento de mensagens. 

    ![Conectores de Proxy de aplicações do AzureAD](./media/application-proxy-connectors/app-proxy-connectors.png)

Para obter mais ajuda com a instalação de um conector, consulte [problema ao instalar o conector do Proxy de aplicações](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verificar a instalação por meio do seu servidor do Windows

Para confirmar o conector instalado e registado corretamente:

1. Abra o Gestor de serviços do Windows ao clicar o **Windows** chave e introduzir *Services. msc*.
2. Verifique se o estado para os dois serviços seguintes encontra **em execução**.
   - **Conector do Proxy de aplicações do Microsoft AAD** permite a conectividade.
   - **Atualizador de conector do Proxy de aplicação do Microsoft AAD** é um serviço de atualização automática. O atualizador verifica a existência de novas versões do conector e atualiza o conector conforme necessário.

     ![Serviços do Conector do Proxy da Aplicação – captura de ecrã](./media/application-proxy-enable/app_proxy_services.png)

3. Se o estado para os serviços não estiver **em execução**, botão direito do mouse para selecionar cada serviço e escolha **iniciar**. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Adicionar uma aplicação no local ao Azure AD

Agora que já preparou o seu ambiente e instalado um conector, está pronto para adicionar aplicações no local para o Azure AD.  

1. Inicie sessão como administrador no [portal do Azure](https://portal.azure.com/).
2. No painel de navegação esquerdo, selecione **do Azure Active Directory**.
3. Selecione **aplicações empresariais**e, em seguida, selecione **novo aplicativo**.
4. Selecione **aplicação no local**.  
5. Na **adicionar sua própria aplicação no local** secção, forneça as seguintes informações sobre a sua aplicação:

    | Campo | Descrição |
    | :---- | :---------- |
    | **Nome** | O nome da aplicação que será apresentado no painel de acesso e no portal do Azure. |
    | **URL interno** | O URL para aceder à aplicação a partir de dentro da rede privada. Pode fornecer um caminho específico no servidor de back-end para publicação, enquanto o resto do servidor não é publicado. Dessa forma, pode publicar sites diferentes no mesmo servidor que aplicações diferentes e atribuir cada uma de suas próprias regras de acesso e o nome.<br><br>Se publicar um caminho, certifique-se de que inclui todas as imagens, scripts e folhas de estilo necessários para a sua aplicação. Por exemplo, se seu aplicativo está em https:\//yourapp/aplicação e utiliza imagens localizadas em https:\//yourapp/suporte de dados, em seguida, deve publicar https:\//yourapp/ como o caminho. Este URL interno não tem de ser os seus utilizadores verão a página de destino. Para obter mais informações, consulte [definir uma página inicial personalizada para aplicações publicadas](application-proxy-configure-custom-home-page.md). |
    | **URL externo** | O endereço que os utilizadores acedam a aplicação a partir de fora da rede. Se não pretender utilizar o domínio de Proxy de aplicações predefinido, ler sobre [domínios personalizados no Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md).|
    | **Pré-autenticação** | Como o Proxy da aplicação verifica os utilizadores antes de conceder acesso à sua aplicação.<br><br>**O Azure Active Directory** -Proxy da aplicação redireciona os utilizadores iniciem sessão com o Azure AD, que autentica as respetivas permissões para o diretório e a aplicação. Recomendamos que mantenha esta opção como predefinição para que possam tirar partido das funcionalidades de segurança do Azure AD como o acesso condicional e multi-factor Authentication. **O Azure Active Directory** é necessária para monitorizar a aplicação com a segurança de aplicações do Microsoft Cloud.<br><br>**Pass-through** -os utilizadores não têm de autenticar no Azure AD para aceder à aplicação. Pode ainda configurar os requisitos de autenticação back-end. |
    | **Grupo do conector** | Conectores de processam o acesso remoto à sua aplicação e grupos de conectores ajudá-lo a organizar os conectores e aplicações por região, rede ou para fins. Se não tem quaisquer grupos de conector ainda criados, a sua aplicação é atribuída a **predefinido**.<br><br>Se a sua aplicação utilizar WebSockets para se ligar, todos os conectores do grupo tem de ser versão 1.5.612.0 ou posterior.|

5. Se for necessário, configure **definições adicionais**. Para a maioria dos aplicativos, deve manter estas definições em seus Estados de predefinição. 

    | Campo | Descrição |
    | :---- | :---------- |
    | **Tempo limite da aplicação de back-end** | Definir este valor como **longo** apenas se a sua aplicação está lenta autenticar e ligar. |
    | **Utilizar Cookie somente de HTTP** | Definir este valor como **Sim** para que o Proxy de aplicações cookies incluem o sinalizador de HTTPOnly no cabeçalho de resposta HTTP. Se utilizar os serviços de ambiente de trabalho remoto, definir este valor como **não**.|
    | **Utilizar Cookie seguro**| Definir este valor como **Sim** transmitir cookies através de um canal seguro, como um pedido HTTPS encriptado.
    | **Utilizar Cookie persistente**| Manter este valor definido como **não**. Só utilize esta definição para aplicações que não é possível partilhar cookies entre processos. Para obter mais informações sobre as definições de cookie, consulte [definições de cookies para aceder a aplicações no local no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Traduzir URLs em cabeçalhos** | Manter este valor como **Sim** , a menos que seu aplicativo necessário o cabeçalho de anfitrião original no pedido de autenticação. |
    | **Traduzir URLs no corpo de aplicação** | Manter este valor como **não** a menos que tenha inserido no código HTML links para outras aplicações no local e não utilizar domínios personalizados. Para obter mais informações, consulte [vincular a tradução com Proxy de aplicações](application-proxy-configure-hard-coded-link-translation.md).<br><br>Definir este valor como **Sim** se pretender monitorizar esta aplicação com o Microsoft Cloud App Security (MCAS). Para obter mais informações, consulte [configurar a monitorização de acesso de aplicações em tempo real com o Microsoft Cloud App Security e Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |
   
6. Selecione **Adicionar**.

## <a name="test-the-application"></a>Testar a aplicação

Está pronto para testar o aplicativo é adicionado corretamente. Nos passos seguintes, irá adicionar uma conta de utilizador à aplicação e tente iniciar sessão.

### <a name="add-a-user-for-testing"></a>Adicionar um utilizador para fins de teste

Antes de adicionar um utilizador para a aplicação, certifique-se de que a conta de utilizador já tem permissões para aceder à aplicação a partir de dentro da rede empresarial.

Para adicionar um utilizador de teste:

1. Selecione **aplicações empresariais**e, em seguida, selecione a aplicação que pretende testar.
2. Selecione **introdução**e, em seguida, selecione **atribuir um utilizador para fins de teste**.
3. Sob **utilizadores e grupos**, selecione **adicionar utilizador**.
4. Sob **adicionar atribuição**, selecione **utilizadores e grupos**. O **grupos de utilizadores e** secção é apresentado. 
5. Selecione a conta que pretende adicionar. 
6. Escolher **selecionar**e, em seguida, selecione **atribuir**.

### <a name="test-the-sign-on"></a>Testar o início de sessão

Para testar o início de sessão para a aplicação:

1. No seu browser, navegue para o URL externo que configurou durante a etapa de publicação. Deverá ver a tela inicial.
2. Inicie sessão como o utilizador que criou na secção anterior.

Para resolução de problemas, consulte [problemas de resolução de problemas de Proxy de aplicações e mensagens de erro](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, preparou seu ambiente no local para trabalhar com o Proxy de aplicações e, em seguida, instalado e registado o conector do Proxy de aplicações. Em seguida, adicionou uma aplicação ao seu inquilino do Azure AD. Verificar a que um utilizador pode iniciar sessão para a aplicação utilizando uma conta do Azure AD.

Fez tudo isto:
> [!div class="checklist"]
> * Portas abertas para tráfego de saída e ter permissão para aceder a URLs específicos
> * Instalou o conector no servidor do Windows e o registrei proxy de aplicações
> * Verificar o conector instalado e registado corretamente
> * Adicionar um aplicativo no local com o seu inquilino do Azure AD
> * Verificar que um utilizador de teste pode iniciar sessão na aplicação utilizando uma conta do Azure AD

Está pronto para configurar a aplicação para início de sessão único. Utilize a seguinte hiperligação para escolher um método de início de sessão único e para obter tutoriais de início de sessão únicos. 

> [!div class="nextstepaction"]
>[Configure single sign-on](what-is-single-sign-on.md#choosing-a-single-sign-on-method) (Configurar o início de sessão único)
