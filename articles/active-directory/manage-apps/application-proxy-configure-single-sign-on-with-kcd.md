---
title: Sign-on único baseado em Kerberos (SSO) em Azure Ative Directory com Application Proxy
description: Cobre como fornecer um único sinal de inscrição usando o Azure Ative Directory Application Proxy.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: e43ad9dedf4212e9b30a08f0c978cb8d1a86776c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657419"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>Kerberos Restrita Delegação para um único sign-on (SSO) para as suas apps com Proxy de aplicação

Pode fornecer um único sinal de acesso para aplicações no local publicadas através do Application Proxy que são protegidos com autenticação integrada do Windows. Estas aplicações requerem um bilhete Kerberos para acesso. A Application Proxy utiliza a Delegação Restrita Kerberos (KCD) para apoiar estas aplicações. 

Pode ativar um único sinal de acesso às suas aplicações utilizando a Autenticação Integrada do Windows (IWA), dando permissão aos conectores Proxy da Aplicação em Ative Directory para personificar os utilizadores. Os conectores utilizam esta permissão para enviar e receber fichas em seu nome.

## <a name="how-single-sign-on-with-kcd-works"></a>Como funciona um único sinal com O KCD
Este diagrama explica o fluxo quando um utilizador tenta aceder a uma aplicação no local que utiliza a IWA.

![Diagrama de fluxo de autenticação AAD da Microsoft](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. O utilizador entra no URL para aceder à aplicação no local através do Application Proxy.
2. Application Proxy redireciona o pedido para serviços de autenticação Azure AD para pré-autorenticato. Neste momento, a Azure AD aplica quaisquer políticas de autenticação e autorização aplicáveis, como a autenticação multifactor. Se o utilizador for validado, o Azure AD cria um símbolo e envia-o para o utilizador.
3. O utilizador passa o token para Application Proxy.
4. O Application Proxy valida o token e recupera o nome principal do utilizador (UPN) do mesmo, e em seguida, o Conector puxa a UPN, e o Nome Principal de Serviço (SPN) através de um canal seguro duplamente autenticado.
5. O Conector realiza a negociação da Delegação Restrita Kerberos (KCD) com o AD nas instalações, fazendo-se passar pelo utilizador para obter um token Kerberos para a aplicação.
6. O Ative Directory envia o token Kerberos para a aplicação ao Conector.
7. O Conector envia o pedido original para o servidor de aplicações, utilizando o token Kerberos que recebeu da AD.
8. A aplicação envia a resposta para o Conector, que é depois devolvido ao serviço Application Proxy e, finalmente, ao utilizador.

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar com um único sinal de acesso para aplicações IWA, certifique-se de que o seu ambiente está pronto com as seguintes definições e configurações:

* As suas aplicações, como as aplicações Web SharePoint, estão definidas para utilizar a Autenticação Integrada do Windows. Para obter mais informações, consulte [Enable Support for Kerberos Authentication](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759186(v=ws.11)), ou para SharePoint ver [Plano de autenticação Kerberos no SharePoint 2013](/SharePoint/security-for-sharepoint-server/kerberos-authentication-planning).
* Todas as suas aplicações têm [Nomes Principais do Serviço.](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)
* O servidor que executa o Conector e o servidor que executa a aplicação são domínios unidos e parte do mesmo domínio ou domínios de confiança. Para obter mais informações sobre o domínio, consulte [Juntar um Computador a um Domínio](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807102(v=ws.11)).
* O servidor que executa o Conector tem acesso para ler o atributo TokenGroupsGlobalAndUniversal para os utilizadores. Esta definição padrão pode ter sido impactada pelo endurecimento da segurança do ambiente.

### <a name="configure-active-directory"></a>Configurar o Active Directory
A configuração do Ative Directory varia, dependendo se o conector Proxy da aplicação e o servidor de aplicação estão no mesmo domínio ou não.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Conector e servidor de aplicações no mesmo domínio
1. No Ative Directory, **Tools** aceda a  >  **Utilizadores e Computadores de Ferramentas.**
2. Selecione o servidor que executa o conector.
3. Clique com o **Properties** botão direito e selecione  >  **a Delegação de** Propriedades.
4. Selecione **Confiar no computador p/ delegação apenas p/ serviços especificados**. 
5. Selecione **Utilizar qualquer protocolo de autenticação**.
6. Nos **Serviços aos quais esta conta pode apresentar credenciais delegadas** acrescentam o valor para a identidade SPN do servidor de aplicações. Isto permite que o Conector Proxy de aplicação personiem os utilizadores em AD contra as aplicações definidas na lista.

   ![Imagem de janela connector-SVR Properties](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Conector e servidor de aplicações em diferentes domínios
1. Para obter uma lista de pré-requisitos para trabalhar com o KCD em todos os domínios, consulte [a Delegação Restrita kerberos através de domínios.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831477(v=ws.11))
2. Utilize a `principalsallowedtodelegateto` propriedade da conta de serviço (conta de utilizador de computador ou de domínio dedicado) da aplicação web para permitir a delegação de autenticação Kerberos do Application Proxy (conector). O servidor de aplicações está a funcionar no contexto de `webserviceaccount` e o servidor delegado é `connectorcomputeraccount` . Execute os comandos abaixo num Controlador de Domínio (executando o Windows Server 2012 R2 ou mais tarde) no domínio de `webserviceaccount` . Utilize nomes fixos (não UPN) para ambas as contas.

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
1. Publique a sua aplicação de acordo com as instruções descritas em [Publicar aplicações com Aplicação Proxy](application-proxy-add-on-premises-application.md). Certifique-se de selecionar **o Azure Ative Directory** como **método de pré-autorização**.
2. Depois da sua aplicação aparecer na lista de aplicações da empresa, selecione-a e clique **em 'S-on' único.**
3. Descreva o único modo de inscrição para a **autenticação integrada do Windows**.  
4. Introduza a **Aplicação Interna SPN** do servidor de aplicações. Neste exemplo, a SPN para a nossa aplicação publicada é http/www.contoso.com. Este SPN tem de estar na lista de serviços aos quais o conector pode apresentar credenciais delegadas. 
5. Escolha a **Identidade Delegada de Início de Sessão** do conector para utilizar em nome dos utilizadores. Para obter mais informações, consulte [Trabalhar com diferentes identidades no local e na nuvem](#working-with-different-on-premises-and-cloud-identities)

   ![Configuração avançada da aplicação](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>SSO para aplicações não-Windows

O fluxo da delegação Kerberos no Azure AD Application Proxy começa quando a Azure AD autentica o utilizador na nuvem. Uma vez que o pedido chega ao local, o conector de aplicação AD AD Azure emite um bilhete Kerberos em nome do utilizador interagindo com o Diretório Ativo local. Este processo é referido como Kerberos Constraind Delegação (KCD). 

Na fase seguinte, é enviado um pedido de backend com este bilhete Kerberos. 

Existem vários mecanismos que definem como enviar o bilhete Kerberos nesses pedidos. A maioria dos servidores não-Windows espera recebê-lo sob a forma de token SPNEGO. Este mecanismo é suportado no Azure AD Application Proxy, mas é desativado por padrão. Um conector pode ser configurado para SPNEGO ou token Kerberos padrão, mas não ambos.

Se configurar uma máquina de conector para a SPNEGO, certifique-se de que todos os outros conectores desse grupo de conector também estão configurados com o SPNEGO. As aplicações que esperam o token Kerberos padrão devem ser encaminhadas através de outros conectores que não estejam configurados para o SPNEGO. Algumas aplicações web aceitam ambos os formatos sem exigir qualquer alteração na configuração. 
 

Para ativar o SPNEGO:

1. Abra uma solicitação de comando que funciona como administrador.
2. A partir da solicitação de comando, execute os seguintes comandos nos servidores de conector que precisam de SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Aplicações não-Windows normalmente nomes de utilizadores ou nomes de contas SAM em vez de endereços de e-mail de domínio. Se essa situação se aplicar às suas aplicações, tem de configurar o campo de identidade de login delegado para ligar as suas identidades em nuvem às identidades da sua aplicação. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Trabalhar com diferentes no local e identidades em nuvem
O Application Proxy assume que os utilizadores têm exatamente a mesma identidade na nuvem e no local. Mas em alguns ambientes, devido a políticas corporativas ou dependências de aplicações, as organizações podem ter de usar identificações alternativas para iniciar snus. Nesses casos, ainda pode utilizar o KCD para uma única s inscrição. Configure uma **identidade de login delegada** para cada aplicação para especificar qual a identidade a utilizar na realização de um único login.  

Esta capacidade permite que muitas organizações que possuam diferentes identidades no local e em nuvem tenham SSO da nuvem para aplicações no local sem exigir que os utilizadores introduzam diferentes nomes de utilizadores e senhas. Isto inclui organizações que:

* Ter vários domínios internamente joe@us.contoso.com (, joe@eu.contoso.com , ) e um único domínio na nuvem joe@contoso.com ().
* Ter nome de domínio não-encaminhável internamente joe@contoso.usa () e um legal na nuvem.
* Não utilize nomes de domínio internamente (joe)
* Use pseudónimos diferentes nas instalações e na nuvem. Por exemplo, joe-johns@contoso.com vs. joej@contoso.com  

Com o Application Proxy, pode selecionar qual a identidade a usar para obter o bilhete Kerberos. Esta definição é por aplicação. Algumas destas opções são adequadas para sistemas que não aceitam o formato de endereço de e-mail, outros são projetados para login alternativo.

![Screenshot delegado do parâmetro de identidade de login](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Se for utilizada a identidade de login delegada, o valor pode não ser único em todos os domínios ou florestas da sua organização. Pode evitar este problema publicando estas aplicações duas vezes utilizando dois grupos de conector diferentes. Uma vez que cada aplicação tem um público de utilizador diferente, pode juntar os seus Conectores a um domínio diferente.

Se **o nome da conta SAM no local** for utilizado para a identidade do logon, o computador que hospeda o conector deve ser adicionado ao domínio em que a conta de utilizador está localizada.

### <a name="configure-sso-for-different-identities"></a>Configurar SSO para diferentes identidades
1. Configurar as definições de Ad Connect Azure para que a identidade principal seja o endereço de e-mail (e-mail). Isto é feito como parte do processo de personalização, alterando o campo **Nome Principal** do Utilizador nas definições de sincronização. Estas definições também determinam como os utilizadores iniciam sessão no Office365, dispositivos Windows10 e outras aplicações que utilizam o Azure AD como sua loja de identidade.  
   ![Identificação da imagem dos utilizadores - User Principal Name dropdown](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nas definições de Configuração de Aplicação para a aplicação que pretende modificar, selecione a **Identidade de Início de Sessão Delegada** a utilizar:

   * Nome Principal do Utilizador (por exemplo, joe@contoso.com )
   * Nome principal do utilizador alternativo (por exemplo, joed@contoso.local )
   * Nome de utilizador parte do nome principal do utilizador (por exemplo, joe)
   * Nome de utilizador parte do nome principal do utilizador alternativo (por exemplo, joed)
   * Nome da conta SAM no local (depende da configuração do controlador de domínio)

### <a name="troubleshooting-sso-for-different-identities"></a>Resolução de problemas SSO para diferentes identidades
Se houver um erro no processo SSO, aparece no registo de eventos da máquina do conector, conforme explicado na [resolução de problemas](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
No então, em alguns casos, o pedido é enviado com sucesso para o pedido de backend, enquanto esta aplicação responde em várias outras respostas HTTP. A resolução de problemas destes casos deve começar por examinar o número do evento 24029 na máquina de conector no registo de eventos de sessão de procuração de aplicação. A identidade do utilizador que foi utilizada para a delegação aparece no campo "utilizador" dentro dos detalhes do evento. Para ligar o registo de sessão, selecione **Mostrar registos analíticos e depurar** no menu de visualização do espectador do evento.

## <a name="next-steps"></a>Próximos passos

* [Como configurar um pedido de procuração de aplicação para utilizar a delegação restrita kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Resolver problemas com o Proxy da Aplicação](application-proxy-troubleshoot.md)