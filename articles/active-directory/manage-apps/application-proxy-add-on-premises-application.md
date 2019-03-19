---
title: Adicionar uma aplicação no local - Proxy de aplicações no Azure Active Directory | Documentos da Microsoft
description: Azure Active Directory (Azure AD) tem um serviço de Proxy de aplicações que permite aos utilizadores aceder a aplicações no local ao iniciar sessão com a respetiva conta do Azure AD. Este tutorial mostra como preparar o ambiente para utilização com o Proxy de aplicações e, em seguida, utiliza o portal do Azure para adicionar uma aplicação no local com o seu inquilino do Azure AD.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fb504e7c2f76f2edd0921cae0fb02ea0849ff4b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878351"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações no Azure Active Directory

Azure Active Directory (Azure AD) tem um serviço de Proxy de aplicações que permite aos utilizadores aceder a aplicações no local ao iniciar sessão com a respetiva conta do Azure AD. Este tutorial prepara o ambiente para utilização com o Proxy de aplicações. Assim que o seu ambiente está pronto, usará o portal do Azure para adicionar uma aplicação no local com o seu inquilino do Azure AD.

Este tutorial permite:

> [!div class="checklist"]
> * Abre as portas para tráfego de saída e permite o acesso a URLs específicos.
> * Instala o conector no seu servidor do Windows e regista-o com o Proxy de aplicações.
> * Verifica se o conector instalado e registado corretamente.
> * Adiciona uma aplicação no local com o seu inquilino do Azure AD.
> * Verifica se que um utilizador de teste pode iniciar sessão na aplicação utilizando uma conta do Azure AD.

## <a name="before-you-begin"></a>Antes de começar

Para adicionar uma aplicação ao seu inquilino, precisa de:

* R [subscrição do Microsoft Azure AD básica ou premium](https://azure.microsoft.com/pricing/details/active-directory). 
* Uma conta de administrador do aplicativo.

### <a name="windows-server"></a>Servidor do Windows

Para utilizar o Proxy de aplicações, precisa de um servidor do Windows com o Windows Server 2012 R2 ou posterior. Instalará o conector do Proxy de aplicações no servidor. Este servidor do conector tem de ligar a serviços de Proxy de aplicações no Azure e as aplicações no local que pretende publicar.

Para elevada disponibilidade no seu ambiente de produção, é recomendável ter mais de um servidor do Windows. Para este tutorial, um servidor do Windows é suficiente.

#### <a name="recommendations-for-the-connector-server"></a>Recomendações para o servidor do conector

1. Fisicamente, localize o servidor do conector perto os servidores de aplicações para otimizar o desempenho entre o conector e a aplicação. Para obter mais informações, consulte [considerações sobre a topologia de rede](application-proxy-network-topology.md).

2. O servidor do conector e os servidores de aplicativos da web devem pertencer ao mesmo domínio do Active Directory. Ter os servidores no mesmo domínio é um requisito para utilizar o início de sessão único (SSO) com a autenticação integrada do Windows (IWA) e Kerberos Constrained Delegation (KCD). Se o servidor do conector e a servidores de aplicações web estiverem em domínios diferentes do Active Directory, terá de utilizar a delegação baseada em recursos para início de sessão único. Para obter mais informações, consulte [KCD para início de sessão único com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="software-requirements"></a>Requisitos de software

O servidor de conector do Windows tem de ter o TLS 1.2, ativado antes de instalar o conector do Proxy de aplicações. Os conectores existentes com versões anteriores ao 1.5.612.0 irão continuar a trabalhar em versões anteriores do TLS até indicações em contrário. 

Para ativar o TLS 1.2:

1. Defina as seguintes chaves de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Reinicie o servidor

## <a name="prepare-your-on-premises-environment"></a>Preparar o ambiente no local

Para preparar o ambiente para o Proxy de aplicações do Azure AD, primeiro tem de ativar a comunicação a centros de dados do Azure. Se existir uma firewall no caminho, certifique-se de que está aberto para que o conector possa fazer pedidos HTTPS (TCP) para o Proxy de aplicações.

### <a name="open-ports"></a>Abrir portas

Abrir as seguintes portas **saída** tráfego. 

   | Número da porta | Como são utilizadas |
   | --- | --- |
   | 80 | Baixar a revogação de certificados apresenta uma lista (CRL) ao validar o certificado SSL |
   | 443 | Todas as comunicações de saída com o serviço de Proxy de aplicações |

Se a firewall impuser tráfego de acordo com os utilizadores de origem, também abra as portas 80 e 443 para tráfego dos serviços do Windows que são executados como um serviço de rede.

Se já estiver a utilizar o Proxy de aplicações, poderá ter uma versão mais antiga do conector instalado.  Siga este tutorial para instalar a versão mais recente do conector. Versões anteriores ao 1.5.132.0 também necessitam de que portas de abrir o seguinte: 5671, 8080, 9090-9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>Permitir o acesso aos URLs

Permitir o acesso aos seguintes URLs:

| do IdP | Como são utilizadas |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem do Proxy de aplicações |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O Azure utiliza estes URLs para verificar os certificados |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | O conector utiliza estes URLs durante o processo de registo. |

Se a sua firewall ou proxy permite que listas de permissões de DNS, pode ligações de lista de permissões para \*. msappproxy.net e \*. servicebus.windows.net. Se não, precisa permitir o acesso para o [intervalos de IP de DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Os intervalos de IP são atualizados a cada semana.

## <a name="install-and-register-a-connector"></a>Instalar e registar um conector

Para utilizar o Proxy de aplicações, terá de instalar um conector em cada servidor de Windows que optar por utilizar com o serviço de Proxy de aplicações. O conector é um agente que gere a ligação de saída dos servidores de aplicações no local para o Proxy de aplicações no Azure AD. Pode instalar um conector em servidores que têm também de outros agentes de autenticação instalados, como o Azure AD Connect.

Para instalar o conector:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) como um administrador da aplicação do diretório que utiliza o Proxy de aplicações. Por exemplo, se o domínio de inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias de administrador nesse domínio.
2. O diretório atual é apresentado em seu nome de utilizador no canto superior direito. Certifique-se de que tem sessão iniciada diretório que utiliza o Proxy de aplicações. Se precisar de mude de diretório, selecione esse ícone.
3. No painel esquerdo, clique em **do Azure Active Directory**e, em seguida **proxy de aplicações**.
4. Clique em **transferir o conector serviço**.
5. Leia os termos de serviço.  Quando estiver pronto, clique em **aceitar termos e transferir**.
6. Na parte inferior da janela, verá um pedido para transferir **AADApplicationProxyConnectorInstaller.exe**. Clique em **executar** para instalar o conector. É aberto um Assistente de instalação. 
7. Siga as instruções do assistente de instalação. Quando lhe for pedido para registar o conector com o Proxy de aplicações para o seu inquilino do Azure AD, forneça as credenciais de administrador do aplicativo.
    - Para o Internet Explorer (IE), se **configuração de segurança avançada do IE** está definida como **no**, poderá não ver o ecrã de registo. Para obter acesso, siga as instruções na mensagem de erro. Certifique-se de que a segurança avançada do Internet Explorer está definida como **desativar**.

### <a name="general-remarks"></a>Observações gerais

Se instalou anteriormente um conector, reinstale-se para obter a versão mais recente.

Se optar por mais de um servidor do Windows para as suas aplicações no local, terá de instalar e registar o conector em cada servidor. Pode organizar os conectores em grupos de conector. Para obter mais informações, consulte [grupos de conectores](application-proxy-connector-groups.md). 

Se a sua organização utilizar servidores proxy para estabelecer ligação à internet, terá de configurá-los para o Proxy de aplicações.  Para obter mais informações, consulte [funcionam com o existente no local servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md). 

Para obter informações sobre conectores, planejamento de capacidade e como eles se manter atualizados, consulte [conectores de Proxy de aplicações do AD Azure compreender](application-proxy-connectors.md). 

Se estiver a utilizar a aplicação de Qlik Sense, instale sempre o connector mais recente. Qlik Sense utiliza WebSockets, que é apenas suportado em versões de conector 1.5.612.0 ou posterior.

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifique se o conector instalado e registado corretamente

Pode utilizar o portal do Azure ou no seu servidor do Windows para confirmar que um novo conector é instalado corretamente.

### <a name="verify---azure-portal"></a>Certifique-se - portal do Azure

Para confirmar o conector instalado e registado corretamente:

1. Inicie sessão no seu diretório do inquilino no [portal do Azure](https://portal.azure.com).
2. Clique em **do Azure Active Directory** e, em seguida **Proxy de aplicações**. Todos os seus conectores e os grupos de conexão são apresentados nesta página. 
3. Selecione um conector para verificar os detalhes. Uma Active Directory etiqueta verde indica que o conector pode ligar ao serviço. No entanto, mesmo que a etiqueta for verde, um problema de rede ainda pode bloquear o conector de recebimento de mensagens. 

    ![Conectores de Proxy de aplicações do AzureAD](./media/application-proxy-connectors/app-proxy-connectors.png)

Para obter mais ajuda com a instalação de um conector, consulte [problemas ao instalar um conector de Proxy de aplicações](application-proxy-connector-installation-problem.md).

### <a name="verify---windows-server"></a>Certifique-se - server do Windows

Para confirmar o conector instalado e registado corretamente:

1. Abra o Gestor de serviços do Windows ao clicar o **Windows** chave e introduzir *Services. msc*.
2. Verifique se o estado para os dois serviços seguintes encontra **em execução**.
   - O **Conector do Proxy da Aplicação do Microsoft AAD** ativa a conectividade
   - **Atualizador de conector do Proxy de aplicação do Microsoft AAD** é um serviço de atualização automática. O atualizador verifica a existência de novas versões do conector e atualiza o conector conforme necessário.

     ![Serviços do Conector do Proxy da Aplicação – captura de ecrã](./media/application-proxy-enable/app_proxy_services.png)

3. Se o estado para os serviços não estiver **em execução**, clique com o botão direito cada serviço e escolha **iniciar**. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Adicionar uma aplicação no local ao Azure AD

Agora que já preparou o seu ambiente e instalado um conector, está pronto para adicionar aplicações no local para o Azure AD.  

1. Inicie sessão como administrador no [portal do Azure](https://portal.azure.com/).
2. Selecione **do Azure Active Directory** > **aplicações empresariais** > **novo aplicativo**.

    ![Adicionar uma aplicação empresarial](./media/application-proxy-publish-azure-portal/add-app.png)

3. Selecione **todos os**, em seguida, selecione **aplicação no local**.  

    ![Adicionar a sua própria aplicação](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Na **adicionar sua própria aplicação no local** painel, forneça as seguintes informações sobre a sua aplicação:

    ![Configurar a sua aplicação no local](./media/application-proxy-add-on-premises-application/add-on-premises-app-with-application-proxy-updated.png)

    | Campo | Descrição |
    | :---- | :---------- |
    | **Nome** | O nome da aplicação que será apresentado no painel de acesso e no portal do Azure. |
    | **URL interno** | O URL para aceder à aplicação a partir de dentro da rede privada. Pode fornecer um caminho específico no servidor de back-end para publicação, enquanto o resto do servidor não é publicado. Dessa forma, pode publicar sites diferentes no mesmo servidor que aplicações diferentes e atribuir cada uma de suas próprias regras de acesso e o nome.<br><br>Se publicar um caminho, certifique-se de que inclui todas as imagens, scripts e folhas de estilo necessários para a sua aplicação. Por exemplo, se seu aplicativo está em <https://yourapp/app> e utiliza as imagens localizadas em <https://yourapp/media>, em seguida, deve publicar <https://yourapp/> como o caminho. Este URL interno não tem de ser os seus utilizadores verão a página de destino. Para obter mais informações, consulte [definir uma página inicial personalizada para aplicações publicadas](application-proxy-configure-custom-home-page.md). |
    | **URL externo** | O endereço que os utilizadores acedam a aplicação a partir de fora da rede. Se não pretender utilizar o domínio de Proxy de aplicações predefinido, ler sobre [domínios personalizados no Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md).|
    | **Pré-autenticação** | Como o Proxy da aplicação verifica os utilizadores antes de conceder acesso à sua aplicação.<br><br>**O Azure Active Directory** -Proxy da aplicação redireciona os utilizadores iniciem sessão com o Azure AD, que autentica as respetivas permissões para o diretório e a aplicação. É recomendável manter esta opção como predefinição, para que possam tirar partido das funcionalidades de segurança do Azure AD como o acesso condicional e multi-factor Authentication. **O Azure Active Directory** é necessária para monitorizar a aplicação com a segurança de aplicações do Microsoft Cloud.<br><br>**Pass-through** -os utilizadores não têm a autenticação no Azure Active Directory para aceder à aplicação. Pode ainda configurar os requisitos de autenticação back-end. |
    | **Grupo do conector** | Conectores de processam o acesso remoto à sua aplicação e grupos de conectores ajudá-lo a organizar os conectores e aplicações por região, rede ou para fins. Se não tem quaisquer grupos de conector ainda criados, a sua aplicação é atribuída a **predefinido**.<br><br>Se a sua aplicação utilizar WebSockets para se ligar, todos os conectores do grupo tem de ser versão 1.5.612.0 ou posterior.|

5. Se for necessário, configure **definições adicionais**. Para a maioria dos aplicativos, deve manter estas definições em seus Estados de predefinição. 

    | Campo | Descrição |
    | :---- | :---------- |
    | **Tempo limite da aplicação de back-end** | Definir este valor como **longo** apenas se a sua aplicação está lenta autenticar e ligar. |
    | **Utilizar Cookie somente de HTTP** | Definir este valor como **Sim** para que o Proxy de aplicações cookies incluem o sinalizador de HTTPOnly no cabeçalho de resposta HTTP. Se utilizar os serviços de ambiente de trabalho remoto, definir este valor como **não**.|
    | **Utilizar Cookie seguro**| Definir este valor como **Sim** transmitir cookies através de um canal seguro, como um pedido HTTPS encriptado.
    | **Utilizar Cookie persistente**| Manter este valor definido como **não**. Esta definição só deve ser utilizada para aplicações que não é possível partilhar cookies entre processos. Para obter mais informações sobre as definições de cookie consulte [as definições de Cookie para aceder a aplicações no local no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)
    | **Traduzir URLs em cabeçalhos** | Manter este valor como **Sim** , a menos que seu aplicativo necessário o cabeçalho de anfitrião original no pedido de autenticação. |
    | **Traduzir URLs no corpo de aplicação** | Manter este valor como **não** a menos que tenha inserido no código HTML links para outras aplicações no local e não utilizar domínios personalizados. Para obter mais informações, consulte [vincular a tradução com Proxy de aplicações](application-proxy-configure-hard-coded-link-translation.md).<br><br>Definir este valor como **Sim** se pretender monitorizar esta aplicação com o Microsoft Cloud App Security (MCAS). Para obter mais informações, consulte [configurar a monitorização de acesso de aplicações em tempo real com o Microsoft Cloud App Security e Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md) |
   
6. Selecione **Adicionar**.

## <a name="test-the-application"></a>Testar a aplicação

Está pronto para testar o aplicativo é adicionado corretamente. Nos passos seguintes, irá adicionar uma conta de utilizador à aplicação e tente iniciar sessão.

### <a name="add-a-user-for-testing"></a>Adicionar um utilizador para fins de teste

Antes de adicionar um utilizador para a aplicação, certifique-se de que a conta de utilizador já tem permissões para aceder à aplicação a partir de dentro da rede empresarial.

Para adicionar um utilizador de teste:

1. Volta a **início rápido** painel, selecione **atribuir um utilizador para fins de teste**.

    ![Atribuir um utilizador para fins de teste](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Sobre o **utilizadores e grupos** painel, selecione **adicionar utilizador**.

    ![Adicionar um utilizador ou grupo](./media/application-proxy-publish-azure-portal/add-user.png)

3. Sobre o **adicionar atribuição** painel, selecione **utilizadores e grupos**e, em seguida, escolha a conta que pretende adicionar. 
4. Selecione **Atribuir**.

### <a name="test-the-sign-on"></a>Testar o início de sessão

Para testar o início de sessão na aplicação:

1. No seu browser, navegue para o URL externo que configurou durante a etapa de publicação. 
2. Deverá ver a tela inicial.
3. Tente iniciar sessão como o utilizador que criou na secção anterior.

    ![Testar a aplicação publicada](./media/application-proxy-publish-azure-portal/test-app.png)

Para resolução de problemas, consulte [problemas de resolução de problemas de Proxy de aplicações e mensagens de erro](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, preparou seu ambiente no local para trabalhar com o Proxy de aplicações e, em seguida, instalado e registado o conector do Proxy de aplicações. Em seguida, adicionou uma aplicação ao seu inquilino do Azure AD. Verificar a que um utilizador pode iniciar sessão para a aplicação utilizando uma conta do Azure AD.

Fez tudo isto:
> [!div class="checklist"]
> * Portas abertas para tráfego de saída e ter permissão para aceder a URLs específicos
> * Instalou o conector no servidor do Windows e o registrei proxy de aplicações
> * Verificar o conector instalado e registado corretamente
> * Adicionar um aplicativo no local com o seu inquilino do Azure AD
> * Verificar que um utilizador de teste pode iniciar sessão na aplicação utilizando uma conta do Azure AD.

Está pronto para configurar a aplicação para início de sessão único. Utilize a seguinte hiperligação para escolher um método de início de sessão único e para obter tutoriais de início de sessão únicos. 

> [!div class="nextstepaction"]
>[Configure single sign-on](what-is-single-sign-on.md#choosing-a-single-sign-on-method) (Configurar o início de sessão único)
