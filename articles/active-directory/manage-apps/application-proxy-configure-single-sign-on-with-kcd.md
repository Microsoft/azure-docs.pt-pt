---
title: Início de sessão único com o Proxy de aplicações | Documentos da Microsoft
description: Aborda como fornecer início de sessão único com o Proxy de aplicações do Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5948fba67d3f071d77192f9ad89bc696fdc0c3cc
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668941"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Delegação restrita de Kerberos para início de sessão único às suas aplicações com o Proxy de aplicações

Pode fornecer início de sessão único para on-premises aplicações publicadas através do Proxy de aplicações que são protegidas com a autenticação integrada do Windows. Estas aplicações requerem um tíquete Kerberos para o acesso. Proxy de aplicações utiliza a delegação restrita de Kerberos (KCD) para suportar estas aplicações. 

Pode ativar o início de sessão único para as aplicações com autenticação integrada do Windows (IWA) ao dar permissão dos conectores do Proxy de aplicações no Active Directory para representar os utilizadores. Os conectores de utilizam esta permissão para enviar e receber tokens em seu nome.

## <a name="how-single-sign-on-with-kcd-works"></a>Como início de sessão único com KCD funciona
Este diagrama explica o fluxo quando um utilizador tenta aceder a uma aplicação no local que utiliza o IWA.

![Diagrama de fluxo de autenticação do Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. O utilizador introduz o URL para aceder à aplicação no local através do Proxy de Aplicação.
2. Proxy da aplicação redireciona o pedido para serviços de autenticação do Azure AD para preauthenticate. Neste momento, aplica-se do Azure AD qualquer autenticação aplicável e políticas de autorização, como a autenticação multifator. Se o utilizador for validado, o Azure AD cria um token e envia-os para o utilizador.
3. O utilizador passa o token para o Proxy de aplicações.
4. Aplicação Proxy valida o símbolo e recupera o Nome Principal do Utilizador (UPN) do mesmo, e em seguida o Conector puxa a UPN, e o Nome Principal de Serviço (SPN) através de um canal seguro dualmente autenticado.
5. O Connector realiza a negociação da Delegação Limitada kerberos (KCD) com a AD no local, fazendo-se passar pelo utilizador para obter um símbolo kerberos para a aplicação.
6. Do Active Directory envia o token de Kerberos para a aplicação para o conector.
7. O conector envia a solicitação original para o servidor de aplicações, com o token Kerberos que recebeu do AD.
8. A aplicação envia a resposta para o conector, que, em seguida, é devolvido para o serviço de Proxy da aplicação e, finalmente, para o usuário.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a utilizar com o início de sessão único para aplicações de IWA, certifique-se de que o ambiente está preparado com as seguintes definições e as configurações:

* As aplicações como aplicações Web do SharePoint, são definidas para utilizar a autenticação integrada do Windows. Para mais informações, consulte [Suporte de Habilitação para a Autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx), ou para SharePoint ver [Plano para a autenticação Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Todas as suas aplicações têm [Nomes Principais](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)de Serviço.
* O servidor que executa o conector e o servidor que executa a aplicação são associados a um domínio e parte do mesmo domínio ou domínios fidedignas. Para obter mais informações sobre o domínio, consulte [Juntar um Computador a um Domínio](https://technet.microsoft.com/library/dd807102.aspx).
* O servidor que executa o conector tem acesso para ler o atributo TokenGroupsGlobalAndUniversal para os utilizadores. Essa configuração padrão poderá ter sido afetada pelo ambiente de proteção de segurança.

### <a name="configure-active-directory"></a>Configurar o Active Directory
A configuração do Active Directory varia, dependendo se o servidor de aplicações e o seu conector de Proxy de aplicações são no mesmo domínio ou não.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Conector e servidor de aplicativos no mesmo domínio
1. No Diretório Ativo, vá a **Ferramentas** > **Utilizadores e Computadores.**
2. Selecione o servidor que executa o conector.
3. Clique à direita e selecione **Propriedades** > **Delegação**.
4. Selecione **Trust este computador para delegação apenas para serviços especificados**. 
5. Selecione Utilize qualquer protocolo de **autenticação**.
6. No âmbito **dos Serviços aos quais esta conta pode apresentar credenciais delegadas,** adicione o valor para a identidade SPN do servidor de aplicações. Isto permite que o conector do Proxy de aplicações representar os utilizadores no AD contra os aplicativos definidos na lista.

   ![Captura de ecrã de janela de propriedades do conector-Server](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Conector e servidor de aplicativos em domínios diferentes
1. Para obter uma lista de pré-requisitos para trabalhar com a KCD em domínios, consulte a [Delegação Limitada kerberos em domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. Utilize a propriedade `principalsallowedtodelegateto` da conta de serviço (conta de utilizador de domínio dedicado ou computador) da aplicação web para permitir a delegação de autenticação Kerberos a partir do Proxy de Aplicação (conector). O servidor de aplicação está a funcionar no contexto de `webserviceaccount` e o servidor de delegação está `connectorcomputeraccount`. Execute os comandos abaixo num Controlador de Domínio (executando o Windows Server 2012 R2 ou posterior) no domínio de `webserviceaccount`. Utilize nomes planos (não UPN) para ambas as contas.

   Se o `webserviceaccount` for uma conta de computador, utilize estes comandos:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Se o `webserviceaccount` for uma conta de utilizador, utilize estes comandos:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Configurar o início de sessão único 
1. Publique a sua aplicação de acordo com as instruções descritas nas [aplicações da Publicação com procuração de aplicações](application-proxy-add-on-premises-application.md). Certifique-se de selecionar o **Diretório Ativo Azure** como método de **pré-autenticação**.
2. Depois da sua aplicação aparecer na lista de aplicações da empresa, selecione-a e clique em iniciar um **único sinal**.
3. Detete o modo de início de sessão único para **a autenticação integrada**do Windows .  
4. Introduza o **SPN** de Aplicação Interna do servidor de aplicações. Neste exemplo, o SPN para a nossa aplicação publicada é http/www.contoso.com. Este SPN tem de estar na lista de serviços a que o conector pode apresentar credenciais delegadas. 
5. Escolha a Identidade de **Login Delegada** para que o conector utilize em nome dos seus utilizadores. Para mais informações, consulte [Trabalhar com diferentes no local e identidades na nuvem](#working-with-different-on-premises-and-cloud-identities)

   ![Configuração da aplicação avançado](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>SSO para aplicações de não-Windows

O fluxo de delegação de Kerberos no Proxy de aplicações do Azure AD é iniciado quando o Azure AD autentica o utilizador na cloud. Assim que a solicitação chega no local, o conector do Proxy de aplicações do Azure AD emite um tíquete Kerberos em nome do utilizador ao interagir com o Active Directory local. Este processo é referido como Kerberos Constrained Delegation (KCD). Na fase seguinte, é enviado um pedido para a aplicação de back-end com esta permissão Kerberos. 

Existem vários protocolos que definem como enviar esses pedidos. A maioria dos servidores de não-Windows esperar negociar com SPNEGO. Esse protocolo é suportado no Proxy de aplicações do Azure AD, mas está desabilitado por predefinição. Um servidor de pode ser configuradas para o SPNEGO ou KCD padrão, mas não ambos.

Se configurar uma máquina de conector para SPNEGO, certifique-se de que todos os outros conectores nesse grupo conector também estão configurados com SPNEGO. Era esperado o KCD padrão de aplicativos devem ser encaminhados através de outros conectores que não estão configurados para SPNEGO.
 

Para ativar SPNEGO:

1. Abra uma linha de comando que é executado como administrador.
2. Na linha de comandos, execute os seguintes comandos de servidores do conector que precisam SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Para mais informações sobre kerberos, consulte Tudo o que quer saber sobre a [Delegação Limitada kerberos (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Aplicações de não-Windows, normalmente, nomes de utilizador do utilizador ou nomes de conta SAM em vez do domínio endereços de e-mail. Se essa situação aplica-se às suas aplicações, tem de configurar o campo de identidade delegada de início de sessão para ligar as identidades de cloud para as identidades de aplicação. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Trabalhando com diferentes no local e de identidades de nuvem
Proxy de aplicações parte do princípio de que os utilizadores têm exatamente a mesma identidade na cloud e no local. Mas em alguns ambientes, devido a políticas corporativas ou dependências de aplicações, as organizações podem ter de usar iDs alternativos para iniciar sessão. Nesses casos, ainda pode utilizar o KCD para uma única inscrição. Configure uma identidade de **login delegada** para cada aplicação para especificar qual a identidade que deve ser utilizada ao realizar uma única inscrição.  

Esta capacidade permite que muitas organizações que têm diferentes no local e de identidades de nuvem para terem SSO a partir da cloud para aplicações no local sem a necessidade dos usuários insiram diferentes nomes de utilizador e palavras-passe. Isto inclui as organizações que:

* Ter vários domínios internamente (joe@us.contoso.com, joe@eu.contoso.com) e um único domínio na nuvem (joe@contoso.com).
* Tenha um nome de domínio não repreensível internamente (joe@contoso.usa) e um nome legal na nuvem.
* Não utilize nomes de domínio internamente (joe)
* Use diferentes pseudónimos nas instalações e na nuvem. Por exemplo, joe-johns@contoso.com vs. joej@contoso.com  

Com o Proxy de aplicações, pode selecionar que identidade para utilizar para obter a permissão de Kerberos. Esta definição é por aplicação. Algumas dessas opções são adequadas para sistemas que não aceitamos o formato de endereço de e-mail, outras pessoas foram concebidas para início de sessão alternativo.

![Captura de ecrã de parâmetro de identidade de início de sessão de delegado](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Se for utilizada a identidade delegada de início de sessão, o valor pode não ser exclusivo em todos os domínios ou florestas na sua organização. Pode evitar este problema ao publicar estas duas vezes com dois grupos diferentes do conector de aplicações. Uma vez que cada aplicativo tem um público de utilizador diferente, pode associar seus conectores para outro domínio.

### <a name="configure-sso-for-different-identities"></a>Configurar o SSO para diferentes identidades
1. Configure definições do Azure AD Connect, para a identidade principal é o endereço de e-mail (correio). Isto é feito como parte do processo de personalização, alterando o campo nome principal do **utilizador** nas definições de sincronização. Estas definições também determinam como os utilizadores iniciar sessão no Office 365, dispositivos Windows 10 e outras aplicações que utilizam o Azure AD como respetivo armazenamento de identidade.  
   ![identificando a imagem dos utilizadores -](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png) de dropdown do nome principal do utilizador  
2. Nas definições de Configuração de Aplicação para a aplicação que pretende modificar, selecione a Identidade de **Login Delegada** a utilizar:

   * Nome principal do utilizador (por exemplo, joe@contoso.com)
   * Nome principal do utilizador alternativo (por exemplo, joed@contoso.local)
   * Parte do nome de utilizador do nome do Principal de utilizador (por exemplo, joe)
   * Parte do nome de utilizador do nome do Principal de utilizador alternativo (por exemplo, joed)
   * Nome da conta SAM no local (depende da configuração do controlador de domínio)

### <a name="troubleshooting-sso-for-different-identities"></a>Resolução de problemas de SSO para diferentes identidades
Se houver um erro no processo SSO, aparece no registo do evento da máquina de conector, tal como explicado na Resolução de [Problemas](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
No entanto, em alguns casos, a solicitação é enviada com êxito para a aplicação de back-end enquanto esse aplicativo responde em várias outras respostas HTTP. Resolução de problemas nesses casos, deve começar examinando o número de evento 24029 na máquina do conector no registo de eventos de sessão do Proxy de aplicações. A identidade do utilizador que foi utilizada para a delegação é apresentado no campo "utilizador" dentro os detalhes do evento. Para ligar o registo da sessão, selecione **Mostrar registos analíticos e depurados** no menu de visualização do espectador do evento.

## <a name="next-steps"></a>Passos seguintes

* [Como configurar um pedido de procuração de aplicação para usar a Delegação Limitada kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Problemas de resolução de problemas que está a ter com a Application Proxy](application-proxy-troubleshoot.md)


Para obter as notícias e atualizações mais recentes, consulte o [blogue do Proxy da Aplicação](https://blogs.technet.com/b/applicationproxyblog/)
