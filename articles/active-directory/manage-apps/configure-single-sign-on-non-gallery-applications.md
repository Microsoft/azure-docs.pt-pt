---
title: Único início de sessão - aplicações externas à Galeria - Azure Active Directory | Documentos da Microsoft
description: Configurar o início de sessão único (SSO) para aplicações externas à galeria no Azure Active Directory (Azure AD)
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 01/08/2019
ms.author: celested
ms.reviewer: asmalser,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: eefde6173156ea86f210ecc196c848fd97aaa0c8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435786"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-azure-active-directory"></a>Configurar o início de sessão único para aplicações externas à galeria no Azure Active Directory

Este artigo é sobre um recurso que permite aos administradores configurar início de sessão único para aplicações não estão presentes na Galeria de aplicações do Azure Active Directory *sem escrever código*. Se estiver em vez disso, procurando orientação para programadores sobre como integrar aplicações personalizadas com o Azure AD através de código, consulte [cenários de autenticação do Azure AD](../develop/authentication-scenarios.md).

A Galeria de aplicações do Azure Active Directory fornece uma listagem dos aplicativos que são conhecidos para oferecer suporte a um formulário de início de sessão único com o Azure Active Directory, conforme descrito em [este artigo](what-is-single-sign-on.md). Depois de (como um IT especialista ou sistema integrador na sua organização) encontrar a aplicação que pretende ligar, pode começar a utilizar, seguindo as instruções passo a passo apresentadas no portal do Azure para ativar o início de sessão único.

Esses recursos também estão disponíveis, de acordo com seu contrato de licença. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/active-directory/). 

- Integração personalizada de qualquer aplicação que suporta os fornecedores de identidade de SAML 2.0 (iniciado por SP ou iniciadas pelo IdP)
- Integração personalizada de qualquer aplicativo da web que tem uma página baseada em HTML início de sessão utilizando [SSO baseado em palavra-passe](what-is-single-sign-on.md#password-based-sso)
- Ligação de autoatendimento de aplicativos que utilizam o protocolo SCIM para aprovisionamento de utilizadores ([descrito aqui](use-scim-to-provision-users-and-groups.md))
- Capacidade de adicionar links para todos os aplicativos no [iniciador de aplicações do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou o [painel de acesso do Azure AD](what-is-single-sign-on.md#linked-sso)

Isto pode incluir não apenas as aplicações de SaaS que utilizam mas ter não ainda foi integrado para a Galeria de aplicações do Azure AD, mas aplicativos web de terceiros que sua organização implantou a servidores que controlar, seja na cloud ou no local.

Estas capacidades, também conhecidas como *modelos de integração de aplicações*, fornecer pontos de ligação baseada em normas para aplicações que suportam SAML, SCIM ou a autenticação baseada em formulários e incluem opções flexíveis e definições para compatibilidade com um amplo número de aplicativos. 

## <a name="adding-an-unlisted-application"></a>Adicionar uma aplicação não listada
Para ligar uma aplicação com um modelo de integração de aplicações, inicie sessão no portal do Azure com a sua conta de administrador do Azure Active Directory. Navegue para o **do Active Directory > aplicações empresariais > novo aplicativo > aplicação da Galeria não** secção, selecione **Add**e, em seguida, **adicionar uma aplicação a partir da Galeria** .

  ![Adicionar aplicação](./media/configure-single-sign-on-non-gallery-applications/customapp1.png)

Na Galeria de aplicações, pode adicionar uma aplicação não listada, selecionando o **aplicação da Galeria não** mosaico que mostra os resultados da pesquisa se a aplicação pretendida não foi encontrada. Depois de introduzir um nome para a sua aplicação, pode configurar o comportamento e as opções de início de sessão único. 

**Dica rápida**:  Como melhor prática, utilize a função de pesquisa para verificar se a aplicação já existe na Galeria de aplicações. Se a aplicação for encontrada e sua descrição menciona início de sessão único, a aplicação já é suportada para o início de sessão único federado.

  ![Pesquisa](./media/configure-single-sign-on-non-gallery-applications/customapp2.png)

Adicionar uma aplicação desta forma, fornece uma experiência semelhante das disponível para aplicações previamente integradas. Para começar, selecione **configurar o início de sessão único** ou clique em **início de sessão único** no menu de navegação do lado esquerdo da aplicação. O ecrã seguinte apresenta as opções de configuração de início de sessão único. As opções são descritas nas próximas seções deste artigo.
  
![Opções de configuração](./media/configure-single-sign-on-non-gallery-applications/customapp3.png)

## <a name="saml-based-single-sign-on"></a>Início de sessão único baseado em SAML
Selecione esta opção para configurar a autenticação baseada no SAML para o aplicativo. Isto requer que a aplicação suporta SAML 2.0. Deve coletar informações sobre como utilizar as capacidades SAML da aplicação antes de continuar. Conclua as secções seguintes para configurar o início de sessão único entre o aplicativo e do Azure AD.

### <a name="enter-basic-saml-configuration"></a>Introduza a configuração básica de SAML

Para configurar o Azure AD, introduza a configuração básica de SAML. Pode introduzir os valores manualmente ou carregar um ficheiro de metadados para extrair o valor dos campos.

  ![Litware domínio e URLs](./media/configure-single-sign-on-non-gallery-applications/customapp4.png)

- **URL de início de sessão (iniciado por SP apenas)** – onde o utilizador acede para iniciar sessão nesta aplicação. Se a aplicação está configurada para executar o serviço inicializado pelo fornecedor início de sessão único, em seguida, quando um utilizador navega para este URL, o fornecedor de serviço fará o redirecionamento necessário para o Azure AD para autenticar e iniciar sessão do utilizador no. Se este campo é preenchido, Azure AD irá utilizar este URL para iniciar o aplicativo do Office 365 e o painel de acesso do Azure AD. Se este campo for omitido, o Azure AD em vez disso, executará o fornecedor de identidade-iniciada pelo início de sessão quando a aplicação é iniciada a partir do Office 365, o painel de acesso do Azure AD, ou a partir do URL do Azure AD único início de sessão (podem ser copiados do separador Dashboard).
- **Identificador** -deve identificar exclusivamente o aplicativo para o qual o início de sessão único está a ser configurado. Pode encontrar este valor como o elemento de emissor no AuthRequest (pedido SAML) enviado pela aplicação. Este valor também aparece como o **ID de entidade** em quaisquer metadados SAML fornecido pela aplicação. Verifique a documentação de SAML da aplicação para obter detalhes sobre o que é o seu valor de ID de entidade ou audiência. 

    Segue-se um exemplo de como o identificador ou o emissor é apresentado no pedido SAML enviado pela aplicação para o Azure AD:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **URL de resposta** -o URL de resposta é onde a aplicação espera receber o token SAML. Isto é também referido como o URL do serviço de consumidor de asserção (ACS). Verifique a documentação de SAML da aplicação para obter detalhes sobre o que é a sua resposta de token SAML URL ou URL do ACS. 

    Para configurar várias replyURLs pode utilizar o seguinte script do PowerShell.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Para obter mais informações, consulte [pedidos de autenticação SAML 2.0 e as respostas que suporte o Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Rever ou personalizar afirmações emitidas no SAML token

Quando um utilizador efetua a autenticação à aplicação, do Azure AD irá emitir um token SAML para a aplicação que contém informações (ou afirmações) sobre o utilizador que identifica exclusivamente. Por padrão inclui o nome de utilizador, endereço de e-mail, nome próprio e apelido do utilizador. 

Pode ver ou editar as afirmações enviadas no token SAML para o aplicativo sob o **atributos** separador.

  ![Atributos](./media/configure-single-sign-on-non-gallery-applications/customapp7.png)

Existem dois motivos por que razão poderá ter de editar as declarações emitidas no SAML token:

- O aplicativo foi escrito para solicitar a um conjunto diferente de URIs de declaração ou valores de afirmação.
- A aplicação foi implementada de forma que requer a afirmação NameIdentifier ser algo que não seja o nome de utilizador (nome da principal de utilizador também conhecidas como) armazenado no Azure Active Directory. 

Para obter mais informações, consulte [personalizar afirmações emitidas no token SAML para aplicações empresariais](./../develop/../develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Data de expiração do certificado de revisão, o estado e notificação por e-mail

Quando criar uma galeria ou de um aplicativo de Galeria não, do Azure AD irá criar um certificado específico do aplicativo com uma data de expiração de 3 anos a contar da data de criação. Precisa este certificado para configurar a confiança entre o Azure AD e a aplicação. Para obter detalhes sobre o formato de certificado, consulte a documentação de SAML do aplicativo. 

Do Azure AD, pode transferir o certificado em Base64 ou formato não processado. Além disso, pode obter o certificado ao transferir o ficheiro de XML de metadados de aplicação ou ao utilizar o URL de metadados de Federação da aplicação.

  ![Certificado](./media/configure-single-sign-on-non-gallery-applications/certificate.png)

Certifique-se de que tem do certificado:

- A data de expiração pretendido. Pode configurar a data de expiração para um máximo de 3 anos.
- Estado do Active Directory. Se o estado estiver inativo, altere o estado para o Active Directory. Para alterar o estado, verifique **Active Directory** e, em seguida, guarde a configuração. 
- O e-mail de notificação correto. Quando o certificado do Active Directory está perto da data de expiração, o Azure AD irá enviar uma notificação para o endereço de e-mail configurado neste campo.  

Para obter mais informações, consulte [gerir certificados para início de sessão único federado no Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Configurar a aplicação de destino

Para configurar a aplicação para início de sessão único, localize a documentação do aplicativo. Para localizar a documentação, desloque-se ao fim da página configuração baseada no SAML início de sessão e, em seguida, clique em **configurar \<nome da aplicação >**. 

Os valores necessários variam de acordo com a aplicação. Para obter detalhes, consulte a documentação de SAML do aplicativo. O início de sessão e o URL do serviço fim de sessão ambos resolver para o mesmo ponto de final, o que é o ponto de final de processamento de pedido SAML para a sua instância do Azure AD. O ID de entidade de SAML é o valor que é apresentado como o emissor no token SAML emitido para o aplicativo.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Atribuir utilizadores e grupos para a sua aplicação de SAML

Assim que a sua aplicação tiver sido configurada para utilizar o Azure AD como fornecedor de identidade baseada no SAML, está quase pronto para testar. Como um controle de segurança do Azure AD não irá emitir um token que permite que um utilizador iniciar sessão na aplicação, a menos que o do Azure AD lhe concedeu acesso ao utilizador. Podem ser concedido aos utilizadores acesso diretamente ou através de uma associação de grupo. 

Para atribuir um utilizador ou grupo à sua aplicação, clique nas **atribuir utilizadores** botão. Selecione o utilizador ou grupo que pretende atribuir e, em seguida, selecione o **atribuir** botão.

  ![Atribuir utilizadores](./media/configure-single-sign-on-non-gallery-applications/customapp6.png)

Atribuir um utilizador permitirá que o Azure AD para emitir um token para o utilizador. Também faz com que um mosaico para esta aplicação para que sejam apresentadas no painel de acesso do utilizador. Um mosaico da aplicação também será apresentado no iniciador de aplicações do Office 365, se o utilizador estiver a utilizar o Office 365. 

> [!NOTE] 
> Pode carregar um logótipo de mosaico para a aplicação utilizando o **carregar o logótipo** botão a **configurar** separador para a aplicação. 


### <a name="test-the-saml-application"></a>Testar a aplicação de SAML

Antes de testar a aplicação de SAML, tem de ter configurado a aplicação com o Azure AD e atribuídos a utilizadores ou grupos à aplicação. Para testar a aplicação de SAML, veja [como depurar baseado em SAML início de sessão único para aplicações no Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Palavra-passe início de sessão único

Selecione esta opção para configurar [baseado em palavra-passe de início de sessão único](what-is-single-sign-on.md) para uma aplicação web que tem uma página de início de sessão HTML. SSO de palavra-passe, também referido como palavra-passe vaulting, permite-lhe gerir o acesso de utilizador e palavras-passe para aplicações web que não suportam o Federação de identidades. Também é útil para cenários onde vários utilizadores tem de partilhar uma única conta, por exemplo, para contas de aplicação de comunicação social da sua organização. 

Depois de selecionar **seguinte**, será solicitado para introduzir o URL baseada na web e página de entrada da aplicação. Tenha em atenção que tem de ser a página que contém os campos de entrada de nome de utilizador e palavra-passe. Uma vez introduzidas, do Azure AD inicia um processo para analisar a página de início de sessão para um nome de utilizador de entrada e uma palavra-passe de entrada. Se o processo não for bem-sucedida, em seguida, ele orienta por meio de um processo alternativo de instalação de uma extensão de browser (requer o Internet Explorer, Chrome ou Firefox) que irá permitir-lhe capturar manualmente os campos.

Assim que a página de início de sessão é capturada, podem ser atribuídos a utilizadores e grupos e políticas de credencial podem ser definidas como regular [aplicações SSO de palavra-passe](what-is-single-sign-on.md).

> [!NOTE] 
> Pode carregar um logótipo de mosaico para a aplicação utilizando o **carregar o logótipo** botão a **configurar** separador para a aplicação. 
>

## <a name="existing-single-sign-on"></a>Existente início de sessão único
Selecione esta opção para adicionar uma ligação para uma aplicação ao portal de painel de acesso do Azure AD ou Office 365 da sua organização. Pode utilizar este para adicionar ligações a aplicações web personalizado que a utilizam atualmente os serviços de Federação do Active Directory (ou outro serviço de Federação) em vez do Azure AD para autenticação. Em alternativa, pode adicionar ligações avançadas para páginas específicas do SharePoint ou outras páginas da web que pretende são apresentadas nos painéis de acesso dos seus utilizadores. 

Depois de selecionar **seguinte**, será solicitado que introduza o URL da aplicação para ligar a. Depois de concluído, os utilizadores e grupos podem ser atribuídos à aplicação, que faz com que as aplicações apareçam no [iniciador de aplicações do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou o [painel de acesso do Azure AD](end-user-experiences.md) para esses utilizadores.

> [!NOTE] 
> Pode carregar um logótipo de mosaico para a aplicação utilizando o **carregar o logótipo** botão a **configurar** separador para a aplicação. 
>

## <a name="related-articles"></a>Artigos relacionados
- [Como personalizar afirmações emitidas no Token SAML para aplicações previamente integradas](../develop/active-directory-saml-claims-customization.md)
- [Resolução de problemas com base em SAML início de sessão único](../develop/howto-v1-debug-saml-sso-issues.md)

