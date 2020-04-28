---
title: Ativar o acesso remoto ao Power BI com procuração de aplicação AD Azure
description: Cobre o básico sobre como integrar um Power BI no local com o Proxy de Aplicação AD Azure.
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
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77111587"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Ativar o acesso remoto ao Power BI Mobile com o Proxy de Aplicações do Azure Active Directory

Este artigo discute como usar o Proxy de Aplicação AD Azure para permitir que a aplicação móvel Power BI se ligue ao Power BI Report Server (PBIRS) e aos Serviços de Relato de Servidores SQL (SSRS) 2016 e posteriormente. Através desta integração, os utilizadores que se encontram afastados da rede corporativa podem aceder aos seus relatórios Power BI a partir da aplicação móvel Power BI e estar protegidos pela autenticação da AD Azure. Esta proteção inclui benefícios de [segurança,](application-proxy-security.md#security-benefits) tais como acesso condicional e autenticação de vários fatores.  

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já implementou os Serviços de Relatório e ativou o Proxy de [Aplicação.](application-proxy-add-on-premises-application.md)

- Ativar o Proxy da Aplicação requer a instalação de um conector num servidor Windows e a conclusão dos [pré-requisitos para](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) que o conector possa comunicar com os serviços da AD Azure.  
- Ao publicar O Power BI, recomendamos que utilize os mesmos domínios internos e externos. Para saber mais sobre domínios personalizados, consulte [Trabalhar com domínios personalizados em Proxy de Aplicação](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Esta integração está disponível para a aplicação **Power BI Mobile iOS e Android.**

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Passo 1: Configure Kerberos Constranged Delegação (KCD)

Para aplicações no local que utilizem a autenticação do Windows, pode obter um único sinal (SSO) com o protocolo de autenticação Kerberos e uma funcionalidade chamada delegação limitada kerberos (KCD). Quando configurado, o KCD permite que o conector Proxy de aplicação obtenha um token windows para um utilizador, mesmo que o utilizador não tenha assinado diretamente no Windows. Para saber mais sobre a KCD, consulte [kerberos Constranged Delegação Overview](https://technet.microsoft.com/library/jj553400.aspx) e [Kerberos Consted Delegação para um único sign-on nas suas apps com Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

A configuração do Reporting Services é muito simples. Certifique-se apenas de ter um nome principal de serviço válido (SPN) para permitir a autenticação kerberos adequada. Certifique-se também de que o servidor de Serviços de Informação está ativado para a autenticação de Negociação.

Para configurar os serviços de Reporte da KCD, continue com os seguintes passos.

### <a name="configure-the-service-principal-name-spn"></a>Configure o nome principal do serviço (SPN)

O SPN é um identificador exclusivo para um serviço que utiliza a autenticação Kerberos. Terá de se certificar de que tem um presente HTTP SPN adequado para o seu servidor de relatório. Para obter informações sobre como configurar o Nome do Principal do Serviço (SPN) adequado para o servidor de relatórios, veja [Register a Service Principal Name (SPN) for a Report Server (Registar um Nome do Principal do Serviço (SPN) para um Servidor de Relatórios)](https://msdn.microsoft.com/library/cc281382.aspx).
Pode verificar se o SPN foi adicionado executando o comando Setspn com a opção -L. Para saber mais sobre este comando, consulte [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Ativar a autenticação de Negociação

Para permitir que um servidor de relatório utilize a autenticação Kerberos, configure o Tipo de Autenticação do servidor de relatório para ser RSWindowsNegotiate. Configure esta definição utilizando o ficheiro rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Para obter mais informações, veja [Modify a Reporting Services Configuration File (Modificar um Ficheiro de Configuração do Reporting Services)](https://msdn.microsoft.com/library/bb630448.aspx) e [Configure Windows Authentication on a Report Server (Configurar a Autenticação do Windows num Servidor de Relatórios)](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Certifique-se de que o Conector é confiável para delegação à SPN adicionada à conta de piscina de pedidos de serviços de reporte
Configure o KCD para que o serviço de proxy de aplicação da AD Azure possa delegar identidades de utilizador na conta de piscina de aplicações de Serviços de Reporte. Configure o KCD permitindo ao conector Proxy de aplicação recuperar bilhetes Kerberos para os seus utilizadores que tenham sido autenticados em Azure AD. Em seguida, esse servidor passa o contexto para a aplicação alvo, ou Serviços de Reporte neste caso.

Para configurar o KCD, repita os seguintes passos para cada máquina de conector:

1. Inscreva-se num controlador de domínio como administrador de domínio e, em seguida, abra **utilizadores e computadores de diretório ativo**.
2. Encontre o computador em que o conector está a funcionar.  
3. Clique duas vezes no computador e, em seguida, selecione o separador **Delegação.**
4. Desloque as definições da delegação para **confiar neste computador apenas para delegação aos serviços especificados**. Em seguida, selecione Utilize qualquer protocolo de **autenticação**.
5. **Selecione Adicionar**, e, em seguida, selecione **Utilizadores ou Computadores**.
6. Insira a conta de serviço que está a usar para serviços de reporte. Esta é a conta que adicionou o SPN à configuração do Reporting Services.
7. Clique em **OK**. Para guardar as alterações, clique novamente **em OK.**

Para mais informações, consulte a [Delegação Limitada kerberos para obter um único sessão nas suas apps com procuração](application-proxy-configure-single-sign-on-with-kcd.md)de aplicação .

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Passo 2: Publicar Serviços de Relatório através do Procurador de Aplicação AD Azure

Agora está pronto para configurar o Procurador de Aplicação AD Azure.

1. Publique serviços de relatório através do Proxy de Aplicação com as seguintes definições. Para obter instruções passo a passo sobre como publicar uma aplicação através do Application Proxy, consulte as aplicações de publicação utilizando o Proxy de [Aplicação AD Azure](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **URL Interno**: Introduza o URL no Servidor de Relatório sem que o conector possa alcançar na rede corporativa. Certifique-se de que este URL está acessível a partir do servidor onde o conector está instalado. Uma boa prática é usar um `https://servername/` domínio de alto nível, como evitar problemas com subpatas publicados através do Application Proxy. Por exemplo, `https://servername/` use `https://servername/reports/` `https://servername/reportserver/`e não ou .
     > [!NOTE]
     > Recomendamos a utilização de uma ligação HTTPS segura ao Servidor de Relatórios. Consulte [as ligações SSL configuradas num servidor](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) de relatório de modo nativo para obter informações sobre como.
   - **URL externo**: Introduza o URL público a que a aplicação móvel Power BI se ligará. Por exemplo, pode `https://reports.contoso.com` parecer que se for utilizado um domínio personalizado. Para utilizar um domínio personalizado, faça o upload de um certificado para o domínio e indique um registo DNS para o domínio de msappproxy.net padrão para a sua aplicação. Para passos detalhados, consulte [Trabalhar com domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md).

   - **Método de pré-autenticação**: Diretório Ativo Azure

2. Assim que a sua aplicação for publicada, configure as definições de início de sessão individuais com os seguintes passos:

   a. Na página de aplicação no portal, selecione **Single sign-on**.

   b. Para **o modo de acesso único,** selecione **autenticação integrada do Windows**.

   c. Detete o **SPN** de Aplicação Interna no valor que definiu anteriormente.  

   d. Escolha a Identidade de **Login Delegada** para que o conector utilize em nome dos seus utilizadores. Para mais informações, consulte [Trabalhar com diferentes no local e identidades na nuvem.](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

   e. Clique em **Guardar** para guardar as suas alterações.

Para terminar a configuração da sua aplicação, vá à secção **utilizadores e grupos** e atribua aos utilizadores o acesso a esta aplicação.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Passo 3: Modificar os URI's de resposta para a aplicação

Antes que a aplicação móvel Power BI possa ligar e aceder aos Serviços de Relatório, tem de configurar o Registo de Aplicação que foi automaticamente criado para si no passo 2. 

1. Na página de **Visão Geral** do Diretório Ativo Azure, selecione registos de **aplicações.**
2. No âmbito do separador **Todas as aplicações,** procure a aplicação que criou no passo 2.
3. Selecione a aplicação e, em seguida, **selecione Autenticação**.
4. Adicione os seguintes URIs redirecionamento com base na plataforma que está a utilizar.

   Ao configurar a aplicação para Power BI Mobile **iOS,** adicione as seguintes URIs redirecionárias do tipo Cliente Público (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Ao configurar a aplicação para Power BI Mobile **Android,** adicione os seguintes URIs redirecionamento do tipo Cliente Público (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > As URIs redirecionais devem ser adicionadas para que a aplicação funcione corretamente. Se estiver a configurar a aplicação tanto para o Power BI Mobile iOS como para android, adicione o seguinte Redirect URI `urn:ietf:wg:oauth:2.0:oob`do tipo Cliente Público (Mobile & Desktop) à lista de URIs Redirecionamento configurados para iOS: .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Passo 4: Ligar a partir da aplicação móvel Power BI

1. Na aplicação móvel Power BI, ligue-se à sua instância de Reporting Services. Para tal, introduza o **URL Externo** para a aplicação que publicou através do Application Proxy.

   ![Aplicação móvel Power BI com URL Externo](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Selecione **Ligar**. Será direcionado para o sinal de Diretório Ativo Azure na página.

3. Introduza credenciais válidas para o utilizador e selecione **Iniciar sessão**. Verá os elementos do seu servidor de Serviços de Informação.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Passo 5: Configure a política intune para dispositivos geridos (opcional)

Pode utilizar o Microsoft Intune para gerir as aplicações de clientes que a força de trabalho da sua empresa utiliza. Intune permite-lhe usar capacidades como encriptação de dados e requisitos de acesso adicionais. Para saber mais sobre a gestão de aplicações através do Intune, consulte intune App Management. Para permitir que a aplicação móvel Power BI funcione com a política Intune, utilize os seguintes passos.

1. Vá ao **Diretório Ativo Azure** e, em seguida, registos de **aplicações.**
2. Selecione a aplicação configurada no Passo 3 ao registar a sua aplicação de cliente nativo.
3. Na página da aplicação, selecione **Permissões API**.
4. Clique **em Adicionar uma permissão**. 
5. Sob **APIs a minha organização usa,** procure "Microsoft Mobile Application Management" e selecione-a.
6. Adicione o **DispositivoManagementManagedApps.ReadWrite** permissão para a aplicação
7. Clique **no consentimento do administrador grant** para conceder o acesso à permissão ao pedido.
8. Configure a política Intune que deseja referindo-se a Como criar e atribuir políticas de [proteção de aplicações](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Resolução de problemas

Se a aplicação devolver uma página de erro depois de tentar carregar um relatório por mais de alguns minutos, poderá ter de alterar a definição de tempo. Por padrão, o Application Proxy suporta aplicações que demoram até 85 segundos a responder a um pedido. Para prolongar esta definição para 180 segundos, selecione o tempo de paragem de back-end para **Long** na página de definições do Proxy da aplicação para a aplicação. Para obter dicas sobre como criar relatórios rápidos e fiáveis consulte [Power BI Reports Best Practices](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Passos seguintes

- [Permitir que as aplicações de clientes nativos interajam com aplicações por procuração](application-proxy-configure-native-client-application.md)
- [Ver KPIs e relatórios no local do servidor de relatórios nas aplicações móveis do Power BI](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
