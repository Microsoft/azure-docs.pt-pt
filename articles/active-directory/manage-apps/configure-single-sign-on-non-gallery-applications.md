---
title: Início de sessão único - aplicações externas à Galeria - plataforma de identidade da Microsoft | Documentos da Microsoft
description: Configurar o início de sessão único (SSO) para aplicações externas à galeria na plataforma de identidade da Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34e6257b4800387470cdc1b7d624bf3ebd1d3e6
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989297"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Configurar o início de sessão único para aplicações externas à galeria na plataforma de identidade da Microsoft

Este artigo é sobre um recurso que permite aos administradores configurar início de sessão único para aplicações em falta na Galeria de aplicações do Microsoft identity platform *sem escrever código*.

Se estiver em vez disso, procurando orientação para programadores sobre como integrar aplicações personalizadas com o Azure AD através de código, consulte [cenários de autenticação do Azure AD](../develop/authentication-scenarios.md).

A Galeria de aplicações de plataforma do Microsoft identity fornece uma listagem dos aplicativos que são conhecidos para oferecer suporte a um formulário de início de sessão único com plataforma de identidade da Microsoft, conforme descrito em [este artigo](what-is-single-sign-on.md). Depois de (como um IT especialista ou sistema integrador na sua organização) encontrar a aplicação que pretende ligar, pode começar a utilizar, seguindo as instruções passo a passo apresentadas no portal do Azure para ativar o início de sessão único.

Os seguintes recursos também estão disponíveis, de acordo com seu contrato de licença. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/active-directory/).

- Integração personalizada de uma aplicação que utilize um protocolo modernos, como [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) para autenticar os utilizadores e obter os tokens do [Microsoft Graph](https://graph.microsoft.com).
- Integração personalizada de qualquer aplicação que suporta [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) fornecedores de identidade (iniciado por SP ou iniciadas pelo IdP)
- Integração personalizada de qualquer aplicativo da web que tem uma página baseada em HTML início de sessão utilizando [SSO baseado em palavra-passe](what-is-single-sign-on.md#password-based-sso)
- Ligação de autoatendimento de aplicativos que usam o [sistema para o protocolo de gestão de identidade entre domínios (SCIM) para aprovisionamento de utilizadores](use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links para todos os aplicativos no [iniciador de aplicações do Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou o [painel de acesso do Azure AD](what-is-single-sign-on.md#linked-sign-on)

Estas capacidades podem incluir a integração personalizada de um software como uma aplicação de serviço (SaaS) que utiliza, mesmo que ninguém tenha incluído a aplicação na Galeria de aplicações do Azure AD ainda. Outro recurso é a integração personalizada de uma aplicação web de terceiros que sua organização implantou a servidores que controlar, seja na cloud ou no local.

Também conhecido como *modelos de integração de aplicações*, esses recursos oferecem pontos de ligação baseada em normas para aplicações que suportam SAML, SCIM ou a autenticação baseada em formulários. As capacidades incluem opções flexíveis e as definições de compatibilidade com um amplo número de aplicativos.

## <a name="adding-an-unlisted-application"></a>Adicionar uma aplicação não listada

A plataforma de identidade da Microsoft fornece dois mecanismos para registar aplicações.

Uma aplicação que utiliza um protocolo modernos, como [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) autenticar seus usuários é registrado usando o [portal de registo de aplicação](../develop/quickstart-register-app.md).

Para registar aplicações com todos os outros tipos de [suportado mecanismos de autenticação](what-is-single-sign-on.md), como o [SAML](../develop/single-sign-on-saml-protocol.md) de protocolo, utilize o **aplicações empresariais** painel para ligar -los com a plataforma de identidade da Microsoft.

Para ligar uma aplicação não listada com um modelo de integração de aplicações, siga estes passos:

1. Inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com/) com a conta de administrador do Microsoft identity platform.
2. Selecione **aplicações empresariais** > **novo aplicativo**.
3. (Opcional mas recomendado) Na **adicionar a partir da galeria** caixa de pesquisa, introduza o nome a apresentar da aplicação. Se a aplicação aparece nos resultados da pesquisa, selecione-o e ignore o resto deste procedimento.
4. Selecione **aplicação da Galeria não**. O **adicionar seu próprio aplicativo** é apresentada a página.

   ![Adicionar aplicação](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Introduza o nome a apresentar para a sua nova aplicação.
6. Selecione **Adicionar**.

Ao adicionar uma aplicação desta forma, é possível fornecer uma experiência semelhante das disponível para aplicações previamente integradas. Primeiro selecione **início de sessão único** da barra lateral do aplicativo. A página seguinte (**selecionar um método de início de sessão único**) apresenta as opções para configurar o SSO:

- **SAML**
- **Com base em palavra-passe**
- **Ligado**

![Selecione um método de início de sessão único](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Para obter mais informações sobre estas opções, consulte as secções seguintes deste artigo.

## <a name="saml-based-single-sign-on"></a>Início de sessão único baseado em SAML

Selecione o **SAML** opção para configurar a autenticação baseada no SAML para o aplicativo. (Esta opção requer que a aplicação suporta SAML 2.0). O **definir a segurança de início de sessão único com o SAML** é apresentada a página.

![Configurar o início de sessão único com SAML](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Esta página tem cinco cabeçalhos diferentes:

| Número de cabeçalho | Nome de cabeçalho | Para obter um resumo deste título, consulte: |
| --- | --- | --- |
| 1 | **Configuração básica de SAML** | [Introduza a configuração básica de SAML](#enter-basic-saml-configuration) |
| 2 | **Atributos de utilizador e afirmações** | [Rever ou personalizar afirmações emitidas no SAML token](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **Certificado de assinatura SAML** | [Data de expiração do certificado de revisão, o estado e notificação por e-mail](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **Configurar \<nome da aplicação >** | [Configurar a aplicação de destino](#set-up-target-application) |
| 5 | **Testar início de sessão único com \<nome da aplicação >** | [Testar a aplicação de SAML](#test-the-saml-application) |

Agora recolha informações sobre como utilizar as capacidades SAML da aplicação antes de continuar. Conclua as secções seguintes para configurar o SSO entre o aplicativo e do Azure AD.

### <a name="enter-basic-saml-configuration"></a>Introduza a configuração básica de SAML

Para configurar o Azure AD, vá para o **configuração básica de SAML** cabeçalho e selecione seu **editar** ícone (um lápis). Pode introduzir os valores manualmente ou carregar um ficheiro de metadados para extrair o valor dos campos.

![Configuração básica de SAML](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Os dois campos seguintes são necessários:

- **Identificador**. Este valor deve identificar exclusivamente o aplicativo para o qual o início de sessão único está a ser configurado. Pode encontrar este valor como o **emissor** elemento no **AuthnRequest** (pedido SAML) enviado pela aplicação. Este valor também aparece como o **ID de entidade** em quaisquer metadados SAML fornecido pela aplicação. Consulte a documentação de SAML da aplicação para obter detalhes sobre o que seu **ID de entidade** ou **público-alvo** é de valor.

  O código seguinte mostra como o **identificador** ou **emissor** aparece no pedido de SAML que a aplicação envia para o Azure AD:

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **URL de resposta**. O URL de resposta é onde a aplicação espera receber o token SAML. Este URL é também referido como o URL de serviço (ACS) do consumidor de asserção. Verifique a documentação de SAML da aplicação para obter detalhes sobre o que é a sua resposta de token SAML URL ou URL do ACS.

  Para configurar várias URLs de resposta, pode usar o seguinte script do PowerShell.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

Os seguintes três campos são opcionais:

- **Inicie sessão no URL (iniciado por SP apenas)**. Este valor indica onde o utilizador acede para iniciar sessão nesta aplicação. Se o aplicativo é executado SSO iniciado por SP, em seguida, quando um utilizador navega para este URL, o SP faz o redirecionamento necessário para o Azure AD para autenticar e iniciar a sessão do utilizador. Se especificar este campo, o Azure AD utiliza este URL para iniciar o aplicativo do Office 365 e o painel de acesso do Azure AD. Se omitir este campo, do Azure AD em vez disso, realiza IdP iniciada pelo início de sessão durante inicializações de aplicações do Office 365, o painel de acesso do Azure AD ou o URL de SSO do Azure AD (que pode copiar a partir da **Dashboard** página).

- **Estado de reencaminhamento**. Pode especificar um Estado de reencaminhamento no SAML para instruir o aplicativo para onde redirecionar os utilizadores após a autenticação. O valor é, normalmente, um URL ou URL do caminho que direciona os utilizadores para uma localização específica no aplicativo.

- **URL de fim de sessão**. Este valor é utilizado para enviar a resposta de fim de sessão SAML para a aplicação.

Para obter mais informações, consulte [único início de sessão do protocolo SAML](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Rever ou personalizar afirmações emitidas no SAML token

Quando um utilizador efetua a autenticação à aplicação, o Azure AD emite a aplicação de um token SAML com informações (ou afirmações) sobre o utilizador que identifica exclusivamente. Por predefinição, estas informações incluem o nome de utilizador, endereço de e-mail, nome próprio e apelido do utilizador.

Para ver ou editar as afirmações enviadas no token SAML para o aplicativo:

- Vá para o **atributos de utilizador e afirmações** cabeçalho e selecione o **editar** ícone. O **atributos de utilizador e afirmações** é apresentada a página.

![Atributos e afirmações de utilizador](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

Poderá ter de editar as declarações emitidas no SAML token por dois motivos:

- A aplicação requer um conjunto diferente de URIs de afirmação ou afirmação valores.
- Seu aplicativo requer o **nomear o valor do identificador** alegam ser algo que não seja o nome de utilizador (também conhecido como o nome principal de utilizador) armazenado na plataforma de identidade da Microsoft.

Para obter mais informações, consulte [como: Personalizar afirmações emitidas no token SAML para aplicações empresariais](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Data de expiração do certificado de revisão, o estado e notificação por e-mail

Quando criar uma galeria ou de um aplicativo de externas à galeria, do Azure AD cria um certificado específico do aplicativo que expira a três anos a partir da data de criação. Precisa este certificado para configurar a confiança entre o Azure AD e a aplicação. Para obter detalhes sobre o formato de certificado, consulte a documentação de SAML do aplicativo.

Do Azure AD, pode transferir o certificado ativo no formato Base64 ou Raw diretamente a partir de principal **definir a segurança de início de sessão único com o SAML** página. Em alternativa, pode obter o certificado do Active Directory ao transferir o ficheiro de XML de metadados de aplicação ou ao utilizar o URL de metadados de Federação da aplicação.

Para ver, criar ou transferir os certificados (ativos ou inativos), vá para o **certificado de assinatura SAML** cabeçalho e selecione o **editar** ícone. O **certificado de assinatura SAML** aparece.

![Certificado de assinatura de SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Certifique-se de que tem do certificado:

- *A data de expiração pretendido.* Pode configurar a data de expiração para até três anos no futuro.
- *Estado do Active Directory para o certificado desejado.* Se o estado for **Inactive**, altere o estado para **Active Directory**. Para alterar o estado, clique com o botão direito linha do certificado pretendido e selecione **ativar o certificado**.
- *A opção de assinatura correta e o algoritmo.*
- *Os endereços de e-mail de notificação correto.* Quando o certificado do Active Directory está perto da data de expiração, o Azure AD envia uma notificação para o endereço de e-mail configurado neste campo.  

Para obter mais informações, consulte [gerir certificados para início de sessão único federado](manage-certificates-for-federated-single-sign-on.md) e [opções no SAML token de assinatura de certificado avançadas](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Configurar a aplicação de destino

Para configurar a aplicação para SSO, localize a documentação do aplicativo. Para encontrar a documentação, vá para o **configurar \<nome da aplicação >** cabeçalho e selecione **ver instruções passo a passo**. A documentação é apresentada no **configurar o início de sessão** página. Essa página orienta-o no preenchimento do **URL de início de sessão**, **do Azure AD identificador**, e **URL de fim de sessão** valores no **configurar \<nome da aplicação >** cabeçalho.

Os valores necessários variam de acordo com a aplicação. Para obter detalhes, consulte a documentação de SAML do aplicativo. O **URL de início de sessão** e **URL de fim de sessão** valores ambos resolver para o mesmo ponto de final, o que é o ponto de final de processamento de pedido SAML para a sua instância do Azure AD. O **do Azure AD identificador** é o valor da **emissor** no SAML token emitido para a aplicação.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Atribuir utilizadores e grupos para a sua aplicação de SAML

Depois de configurar a sua aplicação para utilizar o Azure AD como fornecedor de identidade baseada no SAML, está quase pronto para testar. Como um controle de segurança, o Azure AD apenas emite um token de permitir que um utilizador iniciar sessão na aplicação se do Azure AD lhe concedeu acesso ao utilizador. Os utilizadores podem obter acesso diretamente ou através de uma associação de grupo.

Para atribuir um novo utilizador ou grupo à sua aplicação:

1. Na barra lateral do aplicativo, selecione **utilizadores e grupos**. O  **\<nome da aplicação >-os utilizadores e grupos** é apresentada a página, que mostra a lista atual dos utilizadores e grupos atribuídos.
2. Selecione **adicionar utilizadores**. O **atribuições adicionar** é apresentada a página.
3. Selecione **utilizadores e grupos (\<número > selecionados)**. O **utilizadores e grupos** é apresentada a página, que mostra uma lista de utilizadores e grupos.
4. Tipo ou desloque-se para encontrar o utilizador ou grupo que pretende atribuir a partir da lista.
5. Selecione cada utilizador ou grupo que pretende adicionar e, em seguida, selecione o **selecione** botão. O **utilizadores e grupos** desaparece da página.
6. Na **adicionar atribuições** página, selecione **atribuir**. O  **<application name> -os utilizadores e grupos** é apresentada a página com os utilizadores adicionais apresentados na lista.

   ![Aplicação de utilizadores e grupos](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

A partir desta lista, pode:

- Remova um utilizador.
- Edite a respetiva função.
- Atualize as credenciais (nome de utilizador e palavra-passe), para que o usuário possa autenticar a aplicação no painel de acesso do utilizador.

Pode editar ou remover vários utilizadores ou grupos de cada vez.

Atribuir um utilizador permite que o Azure AD para emitir um token de utilizador. Também faz com que um mosaico para esta aplicação para que sejam apresentadas no painel de acesso do utilizador. Um mosaico da aplicação também é apresentado no iniciador de aplicações do Office 365 se o utilizador está a utilizar o Office 365.

> [!NOTE]
> Pode carregar um logótipo de mosaico para a aplicação utilizando o **carregar o logótipo** botão a **configurar** separador para a aplicação.

### <a name="test-the-saml-application"></a>Testar a aplicação de SAML

Antes de testar a aplicação de SAML, tem de ter já configurar a aplicação com o Azure AD e atribuída a utilizadores ou grupos à aplicação. Para testar a aplicação de SAML, selecione **início de sessão único**, que retorna ao **baseado em SAML logon** página. (Se um método diferente de SSO foi em vigor, selecione **alterar modos de início de sessão únicos** > **SAML** demasiado.) Em seguida, no **testar início de sessão único com \<nome da aplicação >** cabeçalho, selecione **teste**. Para obter mais informações, consulte [baseado em SAML depurar início de sessão único para aplicações no Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Palavra-passe início de sessão único

Selecione esta opção para configurar [baseado em palavra-passe de início de sessão único](what-is-single-sign-on.md) para uma aplicação web com uma página de início de sessão HTML. SSO de palavra-passe, também referido como palavra-passe vaulting, permite-lhe gerir o acesso de utilizador e palavras-passe para aplicações web que não suportam o Federação de identidades. Também é útil para cenários onde vários utilizadores tem de partilhar uma única conta, por exemplo, para contas de aplicação de comunicação social da sua organização.

Depois de selecionar **baseado em palavra-passe**, lhe for pedido para introduzir o URL baseada na web e página de entrada da aplicação.

![Início de sessão único baseado em palavra-passe](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Em seguida, efetue estes passos:

1. Introduza o URL. Esta cadeia tem de ser a página que inclua o campo de entrada de nome de utilizador.
2. Selecione **Guardar**. O Azure AD tenta analisar a página de início de sessão para um nome de utilizador de entrada e uma palavra-passe de entrada.
3. Se o Azure AD da análise tentativa falhar, selecione **configurar \<nome da aplicação > definições de início de sessão único de palavra-passe** para apresentar o **configurar início de sessão** página. (Se a tentativa for bem sucedida, pode ignorar o resto deste procedimento).
4. Selecione **manualmente detetar campos de início de sessão**. Instruções adicionais que descrevem a detecção manual de início de sessão campos são apresentados.

   ![Configuração manual de baseado em palavra-passe de início de sessão único](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. Selecione **recolher campos de início de sessão**. É aberta uma página de estado de captura num novo separador, que mostra a mensagem **captura de metadados está atualmente em curso**.
6. Se o **necessário da extensão de painel de acesso** é apresentada a caixa num novo separador, selecione **instalar agora** para instalar o **segura de aplicações minha extensão de início de sessão** extensão do browser. (Requer a extensão do browser Microsoft Edge, Chrome ou Firefox.) Em seguida, instalar, iniciar e ativar a extensão e atualize a página de estado de captura.

   A extensão do browser, em seguida, é aberta noutro separador, que apresenta o URL introduzido.
7. No separador com o URL introduzido, passar pelo processo de início de sessão. Preencha os campos de nome de utilizador e palavra-passe e tente iniciar sessão. (Não tem de fornecer a palavra-passe correta.)

   Uma linha de comandos pede-lhe para guardar os campos de início de sessão capturados.
8. Selecione **OK**. A guia fecha, a extensão do browser atualiza a página de estado de captura com a mensagem **metadados foi atualizado para a aplicação**e esse navegador separador também fecha.
9. No Azure AD **configurar o início de sessão** página, selecione **Ok, fui capaz de início de sessão para a aplicação com êxito**.
10. Selecione **OK**.

Após a captura de página de início de sessão, pode atribuir utilizadores e grupos, e pode definir políticas de credencial como regular [aplicativos de SSO de palavra-passe](what-is-single-sign-on.md).

> [!NOTE]
> Pode carregar um logótipo de mosaico para a aplicação utilizando o **carregar o logótipo** botão a **configurar** separador para a aplicação.

## <a name="existing-single-sign-on"></a>Existente início de sessão único

Selecione esta opção para adicionar uma ligação para a aplicação no portal de painel de acesso do Azure AD ou Office 365 da sua organização. Pode utilizar este método para adicionar links para aplicativos da web personalizada que a utilizam atualmente os serviços de Federação do Active Directory (ou outro serviço de Federação) em vez do Azure AD para autenticação. Em alternativa, pode adicionar ligações avançadas para páginas específicas do SharePoint ou outras páginas da web que pretende são apresentadas nos painéis de acesso dos seus utilizadores.

Depois de selecionar **ligado**, lhe for pedido para introduzir o URL da aplicação para ligar a. Escreva o URL e selecione **guardar**. Pode atribuir utilizadores e grupos para o aplicativo, o que faz com que as aplicações apareçam no [iniciador de aplicações do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou o [painel de acesso do Azure AD](end-user-experiences.md) para esses utilizadores.

> [!NOTE]
> Pode carregar um logótipo de mosaico para a aplicação utilizando o **carregar o logótipo** botão a **configurar** separador para a aplicação.

## <a name="related-articles"></a>Artigos relacionados

- [How to: Personalizar afirmações emitidas no token SAML para aplicações empresariais](../develop/active-directory-saml-claims-customization.md)
- [Depurar baseado em SAML início de sessão único para aplicações no Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
- [Plataforma de identidade da Microsoft (anteriormente conhecido como Azure Active Directory para programadores)](../develop/index.yml)
