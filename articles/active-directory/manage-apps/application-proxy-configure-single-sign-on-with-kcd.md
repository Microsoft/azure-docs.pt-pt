---
title: Inscrição única com Procuração de Aplicação / Microsoft Docs
description: Cobre como fornecer um único sinal utilizando o Proxy de Aplicação AD Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253458"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Delegação Limitada Kerberos para inscrição única nas suas apps com Procuração de Aplicação

Pode fornecer um único sessão para aplicações no local publicadas através do Application Proxy que estão protegidos com autenticação integrada do Windows. Estas aplicações requerem um bilhete Kerberos para acesso. Application Proxy usa a Delegação Limitada kerberos (KCD) para apoiar estas aplicações. 

Pode ativar um único sessão nas suas aplicações utilizando a Autenticação Integrada do Windows (IWA), dando permissão aos conectores proxy da aplicação no Diretório Ativo para personificar os utilizadores. Os conectores usam esta permissão para enviar e receber fichas em seu nome.

## <a name="how-single-sign-on-with-kcd-works"></a>Como funciona um único sinal com a KCD
Este diagrama explica o fluxo quando um utilizador tenta aceder a uma aplicação no local que utiliza o IWA.

![Diagrama de fluxo de autenticação DaAD da Microsoft](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. O utilizador introduz o URL para aceder à aplicação no local através do Proxy de Aplicação.
2. Aplicação Proxy redireciona o pedido para os serviços de autenticação Da Azure para pré-autenticação. Neste momento, a Azure AD aplica quaisquer políticas de autenticação e autorização aplicáveis, tais como a autenticação multifactor. Se o utilizador for validado, o Azure AD cria um símbolo e envia-o para o utilizador.
3. O utilizador passa o símbolo para o Application Proxy.
4. Aplicação Proxy valida o símbolo e recupera o Nome Principal do Utilizador (UPN) do mesmo, e em seguida o Conector puxa a UPN, e o Nome Principal de Serviço (SPN) através de um canal seguro dualmente autenticado.
5. O Connector realiza a negociação da Delegação Limitada kerberos (KCD) com a AD no local, fazendo-se passar pelo utilizador para obter um símbolo kerberos para a aplicação.
6. O Diretório Ativo envia o símbolo Kerberos para a aplicação ao Connector.
7. O Connector envia o pedido original para o servidor de aplicações, utilizando o símbolo Kerberos que recebeu da AD.
8. A aplicação envia a resposta ao Conector, que é depois devolvido ao serviço Proxy de Aplicação e, finalmente, ao utilizador.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar com um único sinal para aplicações IWA, certifique-se de que o seu ambiente está pronto com as seguintes configurações e configurações:

* As suas aplicações, como as aplicações SharePoint Web, estão preparadas para utilizar a Autenticação Integrada do Windows. Para mais informações, consulte [Suporte de Habilitação para a Autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx), ou para SharePoint ver [Plano para a autenticação Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Todas as suas aplicações têm [Nomes Principais](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)de Serviço.
* O servidor que executa o Connector e o servidor que executa a aplicação são unidos e parte do mesmo domínio ou domínios de confiança. Para obter mais informações sobre o domínio, consulte [Juntar um Computador a um Domínio](https://technet.microsoft.com/library/dd807102.aspx).
* O servidor que executa o Connector tem acesso a ler o atributo TokenGroupsGlobalAndUniversal para os utilizadores. Esta definição padrão pode ter sido impactada pelo endurecimento da segurança no ambiente.

### <a name="configure-active-directory"></a>Configurar o Active Directory
A configuração do Diretório Ativo varia, dependendo se o conector Proxy de Aplicação e o servidor de aplicação estão ou não no mesmo domínio.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Conector e servidor de aplicação no mesmo domínio
1. No Diretório Ativo, vá a **Ferramentas** > **Utilizadores e Computadores.**
2. Selecione o servidor que executa o conector.
3. Clique à direita e selecione **Properties** > **Delegação**.
4. Selecione **Confiar no computador p/ delegação apenas p/ serviços especificados**. 
5. Selecione Utilize qualquer protocolo de **autenticação**.
6. No âmbito **dos Serviços aos quais esta conta pode apresentar credenciais delegadas,** adicione o valor para a identidade SPN do servidor de aplicações. Isto permite que o Conector proxy da aplicação se personime os utilizadores em AD contra as aplicações definidas na lista.

   ![Screenshot da janela Do Conector-SVR Properties](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Conector e servidor de aplicações em diferentes domínios
1. Para obter uma lista de pré-requisitos para trabalhar com a KCD em domínios, consulte a [Delegação Limitada kerberos em domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. Utilize `principalsallowedtodelegateto` a propriedade da conta de serviço (conta de utilizador de domínio de computador ou de domínio dedicado) da aplicação web para permitir a delegação de autenticação Kerberos a partir do Proxy de Aplicação (conector). O servidor de aplicação `webserviceaccount` está a funcionar `connectorcomputeraccount`no contexto de e o servidor de delegação é . Executar os comandos abaixo num Controlador de Domínio (executando o Windows `webserviceaccount`Server 2012 R2 ou posterior) no domínio de . Utilize nomes planos (não UPN) para ambas as contas.

   Se `webserviceaccount` for uma conta de computador, utilize estes comandos:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Se `webserviceaccount` for uma conta de utilizador, utilize estes comandos:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Configurar o início de sessão único 
1. Publique a sua aplicação de acordo com as instruções descritas nas [aplicações da Publicação com procuração de aplicações](application-proxy-add-on-premises-application.md). Certifique-se de selecionar o **Diretório Ativo Azure** como método de **pré-autenticação**.
2. Depois da sua aplicação aparecer na lista de aplicações da empresa, selecione-a e clique em iniciar um **único sinal**.
3. Detete o modo de início de sessão único para **a autenticação integrada**do Windows .  
4. Introduza o **SPN** de Aplicação Interna do servidor de aplicações. Neste exemplo, o SPN para a nossa aplicação publicada é http/www.contoso.com. Este SPN precisa de estar na lista de serviços aos quais o conector pode apresentar credenciais delegadas. 
5. Escolha a Identidade de **Login Delegada** para que o conector utilize em nome dos seus utilizadores. Para mais informações, consulte [Trabalhar com diferentes no local e identidades na nuvem](#working-with-different-on-premises-and-cloud-identities)

   ![Configuração de aplicação avançada](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>SSO para aplicações não Windows

O fluxo da delegação Kerberos em Azure AD Application Proxy começa quando a Azure AD autentica o utilizador na nuvem. Assim que o pedido chegar ao local, o conector De procuração de aplicação da AD Azure emite um bilhete Kerberos em nome do utilizador interagindo com o Diretório Ativo local. Este processo é referido como Delegação Limitada kerberos (KCD). Na fase seguinte, um pedido é enviado para o pedido de backend com este bilhete Kerberos. 

Existem vários protocolos que definem como enviar tais pedidos. A maioria dos servidores não Windows espera negociar com o SPNEGO. Este protocolo é suportado no Proxy de Aplicação AD Azure, mas é desativado por padrão. Um servidor pode ser configurado para SPNEGO ou KCD padrão, mas não ambos.

Se configurar uma máquina de conector para SPNEGO, certifique-se de que todos os outros conectores desse grupo de Conectores também estão configurados com SPNEGO. As aplicações que esperam o KCD padrão devem ser encaminhadas através de outros conectores que não estejam configurados para SPNEGO.
 

Para ativar o SPNEGO:

1. Abra um pedido de comando que corre como administrador.
2. A partir do pedido de comando, execute os seguintes comandos nos servidores do conector que necessitem de SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Para mais informações sobre kerberos, consulte Tudo o que quer saber sobre a [Delegação Limitada kerberos (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

As aplicações não Windows normalmente usuários ou nomes de contas SAM em vez de endereços de e-mail de domínio. Se essa situação se aplicar às suas aplicações, é necessário configurar o campo de identidade de login delegado para ligar as identidades da nuvem às identidades da sua aplicação. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Trabalhar com diferentes no local e identidades na nuvem
Aplicação Proxy assume que os utilizadores têm exatamente a mesma identidade na nuvem e no local. Mas em alguns ambientes, devido a políticas corporativas ou dependências de aplicações, as organizações podem ter de usar iDs alternativos para iniciar sessão. Nesses casos, ainda pode utilizar o KCD para uma única inscrição. Configure uma identidade de **login delegada** para cada aplicação para especificar qual a identidade que deve ser utilizada ao realizar uma única inscrição.  

Esta capacidade permite que muitas organizações que têm diferentes identidades no local e na nuvem tenham SSO da nuvem para aplicações no local sem exigir que os utilizadores introduzam diferentes nomes de utilizadores e palavras-passe. Isto inclui organizações que:

* Ter vários domíniosjoe@us.contoso.com joe@eu.contoso.cominternamente ( ( )joe@contoso.come um único domínio na nuvem ( ).
* Tenha um nome de domíniojoe@contoso.usanão-repreensível internamente ( ) e um legal na nuvem.
* Não utilize nomes de domínio internamente (joe)
* Use diferentes pseudónimos nas instalações e na nuvem. Por exemplo, joe-johns@contoso.com vs.joej@contoso.com  

Com procuração de aplicação, pode selecionar qual a identidade a usar para obter o bilhete Kerberos. Esta definição é por aplicação. Algumas destas opções são adequadas para sistemas que não aceitam o formato de endereço de e-mail, outros são projetados para login alternativo.

![Screenshot de parâmetro de identidade de login delegado](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Se a identidade de login delegada for utilizada, o valor pode não ser único em todos os domínios ou florestas da sua organização. Pode evitar este problema publicando estas aplicações duas vezes utilizando dois grupos de Conector diferentes. Uma vez que cada aplicação tem um público de utilizador diferente, pode juntar os seus Conectores a um domínio diferente.

### <a name="configure-sso-for-different-identities"></a>Configure SSO para diferentes identidades
1. Configure as definições de Ligação AD Azure para que a identidade principal seja o endereço de e-mail (correio). Isto é feito como parte do processo de personalização, alterando o campo nome principal do **utilizador** nas definições de sincronização. Estas definições também determinam como os utilizadores iniciam sessão no Office365, dispositivos Windows10 e outras aplicações que utilizam o Azure AD como loja de identidade.  
   ![Identificação da imagem dos utilizadores - Dropdown de Nome Principal do Utilizador](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nas definições de Configuração de Aplicação para a aplicação que pretende modificar, selecione a Identidade de **Login Delegada** a utilizar:

   * Nome principal do utilizador joe@contoso.com(por exemplo, )
   * Nome principal do utilizador joed@contoso.localalternativo (por exemplo, )
   * Username parte do Nome Principal do Utilizador (por exemplo, joe)
   * Username parte do Nome Principal do Utilizador Alternativo (por exemplo, joed)
   * No local, o nome da conta SAM (depende da configuração do controlador de domínio)

### <a name="troubleshooting-sso-for-different-identities"></a>Resolução de problemas SSO para diferentes identidades
Se houver um erro no processo SSO, aparece no registo do evento da máquina de conector, tal como explicado na Resolução de [Problemas](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Mas, em alguns casos, o pedido é enviado com sucesso para o pedido de backend enquanto este pedido responde em várias outras respostas http. A resolução de problemas destes casos deve começar por examinar o evento número 24029 na máquina de conector no registo de eventos da sessão de procuração de aplicações. A identidade do utilizador utilizada para a delegação aparece no campo "utilizador" dentro dos detalhes do evento. Para ligar o registo da sessão, selecione **Mostrar registos analíticos e depurados** no menu de visualização do espectador do evento.

## <a name="next-steps"></a>Passos seguintes

* [Como configurar um pedido de procuração de aplicação para usar a Delegação Limitada kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Resolver problemas com o Proxy da Aplicação](application-proxy-troubleshoot.md)


Para obter as notícias e atualizações mais recentes, consulte o [blogue do Proxy da Aplicação](https://blogs.technet.com/b/applicationproxyblog/)
