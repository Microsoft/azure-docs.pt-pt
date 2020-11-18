---
title: Permitir o acesso remoto ao Power BI com Proxy de Aplicação AD Azure
description: Abrange o básico sobre como integrar um Power BI no local com Proxy de aplicação AD Ad Azure.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c3188571e9188add7bc8f4f4d07ea5a562a79b3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658116"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Ativar o acesso remoto ao Power BI Mobile com o Proxy de Aplicações do Azure Active Directory

Este artigo discute como usar o Proxy de aplicação AD AD para permitir que a aplicação móvel Power BI se conecte ao Power BI Report Server (PBIRS) e ao SQL Server Reporting Services (SSRS) 2016 e posteriormente. Através desta integração, os utilizadores que estão afastados da rede corporativa podem aceder aos seus relatórios Power BI a partir da aplicação móvel Power BI e ser protegidos pela autenticação AZURE AD. Esta proteção inclui benefícios de [segurança,](application-proxy-security.md#security-benefits) tais como acesso condicional e autenticação de vários fatores.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já implementou serviços de relatório e [ativou o Proxy de aplicação](application-proxy-add-on-premises-application.md).

- Ativar a aplicação Proxy requer a instalação de um conector num servidor windows e completar os [pré-requisitos](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) para que o conector possa comunicar com os serviços AD do Azure.
- Ao publicar Power BI, recomendamos que utilize os mesmos domínios internos e externos. Para saber mais sobre domínios personalizados, consulte [Trabalhar com domínios personalizados no Application Proxy](./application-proxy-configure-custom-domain.md).
- Esta integração está disponível para a aplicação **Power BI Mobile iOS e Android.**

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Passo 1: Configurar a delegação restrita kerberos (KCD)

Para aplicações no local que utilizam a autenticação do Windows, pode obter uma única sinteção (SSO) com o protocolo de autenticação Kerberos e uma funcionalidade chamada delegação restrita Kerberos (KCD). Quando configurado, o KCD permite que o conector Proxy da aplicação obtenha um token Windows para um utilizador, mesmo que o utilizador não tenha assinado diretamente no Windows. Para saber mais sobre o KCD, consulte [kerberos Constrained Delegation Overview](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) e [Kerberos Constrained Delegation para um único sign-on para suas apps com Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

A configuração do Reporting Services é muito simples. Certifique-se apenas de ter um nome principal de serviço válido (SPN) para permitir a autenticação adequada de Kerberos. Certifique-se também de que o servidor 'Serviços de Relato' está ativado para a autenticação do Negociar.

Para criar o KCD para serviços de reporte, continue com os seguintes passos.

### <a name="configure-the-service-principal-name-spn"></a>Configure o nome principal do serviço (SPN)

O SPN é um identificador exclusivo para um serviço que utiliza a autenticação Kerberos. Terá de se certificar de que tem um HTTP SPN adequado para o seu servidor de relatórios. Para obter informações sobre como configurar o Nome do Principal do Serviço (SPN) adequado para o servidor de relatórios, veja [Register a Service Principal Name (SPN) for a Report Server (Registar um Nome do Principal do Serviço (SPN) para um Servidor de Relatórios)](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server).
Pode verificar se o SPN foi adicionado executando o comando Setspn com a opção -L. Para saber mais sobre este comando, consulte [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Ativar a autenticação do Negociar

Para permitir que um servidor de relatório utilize a autenticação Kerberos, configuure o Tipo de Autenticação do servidor de relatórios para ser RSWindowsNegotiate. Configure esta definição utilizando o ficheiro rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Para obter mais informações, veja [Modify a Reporting Services Configuration File (Modificar um Ficheiro de Configuração do Reporting Services)](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config) e [Configure Windows Authentication on a Report Server (Configurar a Autenticação do Windows num Servidor de Relatórios)](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Certifique-se de que o Conector é confiável para a delegação da SPN adicionada à conta de grupo de pedidos de relatórios
Configure o KCD para que o serviço de procuração de aplicações AZure AD possa delegar identidades dos utilizadores na conta do pool de aplicações de serviços de relatório. Configure a KCD ao ativar o conector do Proxy de Aplicações para obter permissões Kerberos para os utilizadores que foram autenticados no Azure Active Directory. Em seguida, esse servidor passa o contexto para a aplicação alvo, ou Serviços de Relatório neste caso.

Para configurar o KCD, repita os seguintes passos para cada máquina de conector:

1. Inscreva-se num controlador de domínio como administrador de domínio e, em seguida, abra **Utilizadores e Computadores de Diretório Ativo**.
2. Localize o computador onde o conector está a ser executado.
3. Faça duplo clique no computador e selecione o separador **Delegação**.
4. Configure as definições de delegação como **Confiar neste computador para delegação apenas aos serviços especificados**. Em seguida, **selecione Utilize qualquer protocolo de autenticação**.
5. Selecione **Adicionar** e, em seguida, **Utilizadores ou Computadores**.
6. Insira a conta de serviço que está a utilizar para serviços de reporte. Esta é a conta que adicionou o SPN à configuração do Reporting Services.
7. Clique em **OK**. Para guardar as alterações, clique novamente em **OK**.

Para mais informações, consulte [a Delegação Restrita Kerberos para um único s-on para as suas aplicações com o Application Proxy.](application-proxy-configure-single-sign-on-with-kcd.md)

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Passo 2: Publicar serviços de relatório através do Azure AD Application Proxy

Agora está pronto para configurar o Proxy de Aplicações do Azure Active Directory.

1. Publicar Serviços de Relatório através de Application Proxy com as seguintes definições. Para obter as instruções passo a passo sobre como publicar uma aplicação através do Proxy de Aplicações, veja [Publicar aplicações com o Proxy de Aplicações do Azure Active Directory](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **URL interno**: Introduza o URL no Servidor de Relatório que o conector pode alcançar na rede corporativa. Confirme que este URL está acessível a partir do servidor no qual está instalado o conector. Uma das melhores práticas consiste em utilizar um domínio de nível superior como o `https://servername/` para evitar problemas com os subcaminhos publicados através do Proxy de Aplicações. Por exemplo, utilize `https://servername/` e não `https://servername/reports/` ou `https://servername/reportserver/`.
     > [!NOTE]
     > Recomendamos a utilização de uma ligação HTTPS segura ao Servidor de Relatórios. Para obter informações, veja [Configure SSL connections on a native mode report server](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) (Configurar ligações SSL num servidor de relatórios em modo nativo).
   - **URL externo**: Introduza o URL público a que a aplicação móvel Power BI se ligará. Por exemplo, pode ser semelhante a `https://reports.contoso.com` caso um domínio personalizado esteja a ser utilizado. Para utilizar um domínio personalizado, carregue um certificado para o domínio e aponte um registo DNS para o domínio msappproxy.net predefinido da aplicação. Para obter os passos detalhados, veja [Trabalhar com domínios personalizados no Proxy de Aplicações do Azure Active Directory](application-proxy-configure-custom-domain.md).

   - **Método de pré-autenticação**: Diretório Ativo Azure

2. Uma vez publicada a aplicação, configure as definições de início de sessão único com estes passos:

   a. Na página da aplicação no portal, selecione **Início de sessão único**.

   b. Em **Modo de Início de Sessão Único**, selecione **Autenticação Integrada do Windows**.

   c. Defina **SPN da Aplicação Interna** para o valor que definiu anteriormente.

   d. Escolha a **Identidade Delegada de Início de Sessão** do conector para utilizar em nome dos utilizadores. Para obter mais informações, veja [Trabalhar com identidades no local e na cloud diferentes](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Clique em **Guardar** para guardar as alterações.

Para terminar a configuração da sua aplicação, aceda à secção **de Utilizadores e grupos** e atribua aos utilizadores o acesso a esta aplicação.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Passo 3: Modificar os URI de resposta para o pedido

Antes que a aplicação móvel Power BI possa ligar e aceder aos Serviços de Relatório, tem de configurar o Registo de Aplicações que foi automaticamente criado para si no passo 2.

1. Na página **Descrição geral** do Azure Active Directory, selecione **Registos de aplicações**.
2. No âmbito do separador **Todas as aplicações,** procure a aplicação que criou no passo 2.
3. Selecione a aplicação e, em seguida, **Autenticação**.
4. Adicione os seguintes URIs de Redirecionamento com base na plataforma que está a utilizar.

   Ao configurar a aplicação para Power BI Mobile **iOS,** adicione os seguintes URIs de redirecionamento do tipo Cliente Público (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Ao configurar a aplicação para Power BI Mobile **Android,** adicione os seguintes URIs de redirecionamento do tipo Cliente Público (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Os URIs de Redirecionamento têm de ser adicionados para que a aplicação possa funcionar corretamente. Se estiver a configurar a aplicação tanto para Power BI Mobile iOS como Android, adicione o seguinte Redirect URI do tipo Cliente Público (Mobile & Desktop) à lista de URIs redirecionantes configurados para iOS: `urn:ietf:wg:oauth:2.0:oob` .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Passo 4: Ligar a partir da Aplicação Móvel Power BI

1. Na aplicação móvel Power BI, ligue-se à sua instância de Serviços de Relato. Para isso, insira o **URL Externo** para a aplicação que publicou através do Application Proxy.

   ![Aplicativo móvel Power BI com URL externo](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Selecione **Connect** (Ligar). Você será direcionado para o sinal do Diretório Ativo Azure na página.

3. Introduza as credenciais válidas do utilizador e selecione **Iniciar sessão**. Verá os elementos do seu servidor de Serviços de Informação.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Passo 5: Configurar a política de Intune para dispositivos geridos (opcional)

Pode utilizar o Microsoft Intune para gerir as aplicações de clientes que a força de trabalho da sua empresa utiliza. O Intune permite-lhe utilizar capacidades como encriptação de dados e requisitos de acesso adicionais. Para saber mais sobre a gestão de aplicações através do Intune, consulte a Intune App Management. Para ativar a aplicação móvel Power BI para funcionar com a política Intune, utilize os seguintes passos.

1. Vá ao **Azure Ative Directory** e, em seguida, **registos de aplicações.**
2. Selecione a aplicação configurada no Passo 3 ao registar a sua aplicação de cliente nativo.
3. Na página da aplicação, selecione **Permissões API**.
4. Clique **em Adicionar uma permissão**.
5. Nos **APIs a minha organização utiliza,** procure por "Microsoft Mobile Application Management" e selecione-a.
6. Adicione o **deviceManagementManagedApps.ReadWrite** permissão à aplicação
7. Clique **em Grant consentimento administrativo** para conceder o acesso à permissão ao pedido.
8. Configure a política de Intune que pretende, referindo-se a [Como criar e atribuir políticas de proteção de aplicações.](/intune/app-protection-policies)

## <a name="troubleshooting"></a>Resolução de problemas

Se a aplicação retornar uma página de erro depois de tentar carregar um relatório por mais de alguns minutos, poderá ter de alterar a definição de tempo limite. Por predefinição, o Application Proxy suporta aplicações que demoram até 85 segundos para responder a um pedido. Para alongar esta definição para 180 segundos, selecione o tempo limite de back-end para **Long** na página de definições de Procuração de Aplicações para a aplicação. Para obter dicas sobre como criar relatórios rápidos e fiáveis consulte [Power BI Reports Best Practices](/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Próximos passos

- [Permitir que as aplicações de clientes nativos interajam com aplicações proxy](application-proxy-configure-native-client-application.md)
- [Ver KPIs e relatórios no local do servidor de relatórios nas aplicações móveis do Power BI](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)