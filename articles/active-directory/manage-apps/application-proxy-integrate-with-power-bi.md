---
title: Habilitar o acesso remoto para Power BI com o Proxy de Aplicativo do AD do Azure | Microsoft Docs
description: Aborda as noções básicas sobre como integrar um Power BI local com o Proxy de Aplicativo do AD do Azure.
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
ms.openlocfilehash: 2148d6ea869a87571008c1f84c5b1000d4030bbb
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175943"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Habilitar o acesso remoto para Power BI Mobile com o Azure Proxy de Aplicativo do AD

Este artigo discute como usar o Azure Proxy de Aplicativo do AD para habilitar o Power BI aplicativo móvel para se conectar ao Servidor de Relatórios do Power BI (PBIRS) e SQL Server Reporting Services (SSRS) 2016 e posterior. Por meio dessa integração, os usuários que estão fora da rede corporativa podem acessar seus relatórios de Power BI do aplicativo Power BI Mobile e ser protegidos pela autenticação do Azure AD. Essa proteção inclui [benefícios de segurança](application-proxy-security.md#security-benefits) como o acesso condicional e a autenticação multifator.  

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já implantou os serviços de relatório e o [proxy de aplicativo habilitado](application-proxy-add-on-premises-application.md).

- Habilitar o proxy de aplicativo requer a instalação de um conector em um Windows Server e a conclusão dos [pré-requisitos](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) para que o conector possa se comunicar com os serviços do Azure AD.  
- Ao publicar Power BI, recomendamos que você use os mesmos domínios internos e externos. Para saber mais sobre domínios personalizados, consulte [trabalhando com domínios personalizados no proxy de aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Essa integração está disponível para o **Power bi Mobile aplicativo Ios e Android** .

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Etapa 1: configurar a delegação restrita de Kerberos (KCD)

Para aplicativos locais que usam a autenticação do Windows, você pode obter SSO (logon único) com o protocolo de autenticação Kerberos e um recurso chamado KCD (delegação restrita de Kerberos). Quando configurado, o KCD permite que o conector do proxy de aplicativo obtenha um token do Windows para um usuário, mesmo que o usuário não tenha entrado no Windows diretamente. Para saber mais sobre o KCD, confira [visão geral da delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx) e [delegação restrita de Kerberos para logon único em seus aplicativos com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

Não há muito a ser configurada no lado do Reporting Services. Apenas certifique-se de ter um SPN (nome da entidade de serviço) válido para permitir que a autenticação Kerberos adequada ocorra. Verifique também se o servidor de Reporting Services está habilitado para autenticação de negociação.

Para configurar o KCD para o Reporting Services, continue com as etapas a seguir.

### <a name="configure-the-service-principal-name-spn"></a>Configurar o SPN (nome da entidade de serviço)

O SPN é um identificador exclusivo para um serviço que usa a autenticação Kerberos. Você precisará verificar se tem um SPN HTTP adequado presente para seu servidor de relatório. Para obter informações sobre como configurar o SPN (nome da entidade de serviço) adequado para o servidor de relatório, consulte [registrar um SPN (nome da entidade de serviço) para um servidor de relatório](https://msdn.microsoft.com/library/cc281382.aspx).
Você pode verificar se o SPN foi adicionado executando o comando setspn com a opção-L. Para saber mais sobre esse comando, consulte [setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Habilitar autenticação de negociação

Para habilitar um servidor de relatório para usar a autenticação Kerberos, configure o tipo de autenticação do servidor de relatório para ser RSWindowsNegotiate. Defina essa configuração usando o arquivo RSReportServer. config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Para obter mais informações, consulte [modificar um arquivo de configuração de Reporting Services](https://msdn.microsoft.com/library/bb630448.aspx) e [Configurar a autenticação do Windows em um servidor de relatório](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Verifique se o conector é confiável para delegação para o SPN adicionado à conta do pool de aplicativos Reporting Services
Configure o KCD para que o serviço de Proxy de Aplicativo do AD do Azure possa delegar identidades de usuário para a conta do pool de aplicativos Reporting Services. Configure o KCD habilitando o conector de proxy de aplicativo para recuperar tíquetes Kerberos para seus usuários que foram autenticados no Azure AD. Em seguida, esse servidor passa o contexto para o aplicativo de destino ou Reporting Services nesse caso.

Para configurar o KCD, repita as seguintes etapas para cada computador do conector:

1. Entre em um controlador de domínio como um administrador de domínio e, em seguida, abra **Active Directory usuários e computadores**.
2. Localize o computador no qual o conector está sendo executado.  
3. Clique duas vezes no computador e selecione a guia **delegação** .
4. Defina as configurações de delegação para **confiar neste computador para delegação somente para os serviços especificados**. Em seguida, selecione **usar qualquer protocolo de autenticação**.
5. Selecione **Adicionar**e, em seguida, selecione **usuários ou computadores**.
6. Insira a conta de serviço que você está usando para Reporting Services. Essa é a conta à qual você adicionou o SPN dentro da configuração de Reporting Services.
7. Clique em **OK**. Para salvar as alterações, clique em **OK** novamente.

Para obter mais informações, consulte [delegação restrita de Kerberos para logon único em seus aplicativos com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Etapa 2: publicar serviços de relatório por meio do Azure Proxy de Aplicativo do AD

Agora você está pronto para configurar o Azure Proxy de Aplicativo do AD.

1. Publique os serviços de relatório por meio do proxy de aplicativo com as configurações a seguir. Para obter instruções detalhadas sobre como publicar um aplicativo por meio do proxy de aplicativo, consulte [Publicando aplicativos usando o Azure proxy de aplicativo do AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **URL interna**: Insira a URL para o servidor de relatório que o conector pode acessar na rede corporativa. Verifique se essa URL está acessível no servidor em que o conector está instalado. Uma prática recomendada é usar um domínio de nível superior, como `https://servername/` para evitar problemas com subcaminhos (por exemplo, `https://servername/reports/` e `https://servername/reportserver/`) não publicados por meio do proxy de aplicativo.
     > [!NOTE]
     > É recomendável usar uma conexão HTTPS segura para o servidor de relatório. Consulte [Configurar conexões SSL em um servidor de relatório no modo nativo](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) para obter informações sobre como.
   - **URL externa**: Insira a URL pública à qual o aplicativo móvel Power bi se conectará. Por exemplo, pode parecer `https://reports.contoso.com` se um domínio personalizado for usado. Para usar um domínio personalizado, carregue um certificado para o domínio e aponte um registro DNS para o domínio padrão do msappproxy.net para seu aplicativo. Para obter etapas detalhadas, consulte [trabalhando com domínios personalizados no Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md).

   - **Método de pré-autenticação**: Azure Active Directory

2. Depois que seu aplicativo for publicado, configure as configurações de logon único com as seguintes etapas:

   a. Na página aplicativo no portal, selecione **logon único**.

   b. Para o **modo de logon único**, selecione **autenticação integrada do Windows**.

   c. Defina **SPN do aplicativo interno** para o valor que você definiu anteriormente.  

   d. Escolha a **identidade de logon delegada** para o conector a ser usado em nome dos usuários. Para obter mais informações, consulte [trabalhando com diferentes identidades locais e na nuvem](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Clique em **salvar** para salvar as alterações.

Para concluir a configuração do aplicativo, vá para **a seção usuários e grupos** e atribua usuários para acessar este aplicativo.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Etapa 3: modificar as URI de resposta para o aplicativo

Antes que o aplicativo móvel Power BI possa se conectar e acessar os serviços de relatório, você deve configurar o registro do aplicativo que foi criado automaticamente para você na etapa 2. 

1. Na página **visão geral** do Azure Active Directory, selecione **registros de aplicativo**.
2. Na guia **todos os aplicativos** , pesquise o aplicativo criado na etapa 2.
3. Selecione o aplicativo e, em seguida, selecione **autenticação**.
4. Adicione os seguintes URIs de redirecionamento com base em qual plataforma você está usando.

   Ao configurar o aplicativo para Power BI Mobile **Ios**, adicione os seguintes URIs de redirecionamento do tipo cliente público (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Ao configurar o aplicativo para Power BI Mobile **Android**, adicione os seguintes URIs de redirecionamento do tipo cliente público (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Os URIs de redirecionamento devem ser adicionados para que o aplicativo funcione corretamente. Se você estiver configurando o aplicativo para o Power BI Mobile iOS e Android, adicione o seguinte URI de redirecionamento do tipo cliente público (Mobile & Desktop) à lista de URIs de redirecionamento configurados para iOS: `urn:ietf:wg:oauth:2.0:oob`.

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Etapa 4: conectar-se do aplicativo Power BI Mobile

1. No aplicativo móvel Power BI, conecte-se à sua instância do Reporting Services. Para fazer isso, insira a **URL externa** para o aplicativo que você publicou por meio do proxy de aplicativo.

   ![Power BI aplicativo móvel com a URL externa](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Selecione **Ligar**. Você será direcionado para a página de entrada do Azure Active Directory.

3. Insira credenciais válidas para o usuário e selecione **entrar**. Você verá os elementos do seu servidor de Reporting Services.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Etapa 5: configurar a política do Intune para dispositivos gerenciados (opcional)

> [!NOTE]
> Atualmente, essa funcionalidade está disponível apenas no iOS.

Você pode usar Microsoft Intune para gerenciar os aplicativos cliente que a força de uso da sua empresa usa. O Intune permite que você use recursos como criptografia de dados e requisitos de acesso adicionais. Para saber mais sobre o gerenciamento de aplicativos por meio do Intune, confira gerenciamento de aplicativos do Intune. Para habilitar o Power BI aplicativo móvel para trabalhar com a política do Intune, use as etapas a seguir.

1. Vá para **Azure Active Directory** e, em seguida, **registros do aplicativo**.
2. Selecione o aplicativo configurado na etapa 3 ao registrar seu aplicativo cliente nativo.
3. Na página do aplicativo, selecione **permissões de API**.
4. Clique em **Adicionar uma permissão**. 
5. Em **APIs que minha organização usa**, pesquise "gerenciamento de aplicativos móveis da Microsoft" e selecione-o.
6. Adicione a permissão **DeviceManagementManagedApps. ReadWrite** ao aplicativo
7. Clique em **conceder consentimento do administrador** para conceder a permissão de acesso ao aplicativo.
8. Configure a política do Intune desejada consultando [como criar e atribuir políticas de proteção de aplicativo](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Resolução de problemas

Se o aplicativo retornar uma página de erro depois de tentar carregar um relatório por mais de alguns minutos, talvez seja necessário alterar a configuração de tempo limite. Por padrão, o proxy de aplicativo dá suporte a aplicativos que levam até 85 segundos para responder a uma solicitação. Para aumentar essa configuração para 180 segundos, selecione o tempo limite de back-end para **longo** na página de configurações de proxy de aplicativo para o aplicativo. Para obter dicas sobre como criar relatórios rápidos e confiáveis, consulte [práticas recomendadas de Power bi relatórios](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Passos seguintes

- [Permitir que aplicativos cliente nativos interajam com aplicativos de proxy](application-proxy-configure-native-client-application.md)
- [Exibir KPIs e relatórios locais do servidor de relatório no Power BI aplicativos móveis](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
