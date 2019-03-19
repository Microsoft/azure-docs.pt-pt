---
title: Personalizar a interface de utilizador (IU) do Azure Active Directory B2C dinamicamente ao utilizar políticas personalizadas | Documentos da Microsoft
description: Suporta várias experiências de identidade visual com conteúdo HTML5/CSS que muda dinamicamente no tempo de execução.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: bf7391ea83adf852be0f989a82e89d7f316f2f85
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090547"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Configurar a interface do Usuário com conteúdo dinâmico ao utilizar políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao utilizar o Azure Active Directory B2C (Azure AD B2C) as políticas personalizadas, pode enviar um parâmetro de uma cadeia de consulta. Ao transmitir o parâmetro para o ponto final HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou de início de sessão do Azure AD B2C, com base num parâmetro que transmite a partir da sua aplicação Web ou móvel. 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo se concentra em como personalizar a interface de utilizador do Azure AD B2C com *conteúdo dinâmico* ao utilizar políticas personalizadas. Para começar a utilizar, veja [personalização da interface do Usuário numa política personalizada](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>O artigo do Azure AD B2C [personalização da interface do Usuário configurar numa política personalizada](active-directory-b2c-ui-customization-custom.md), aborda os fundamentos do seguintes:
> * Funcionalidade de personalização de interface (IU) do utilizador de página.
> * Ferramentas essenciais para a funcionalidade de personalização da interface do Usuário de página de teste com o nosso conteúdo de exemplo.
> * Os elementos de interface do Usuário de núcleo de cada tipo de página.
> * Melhores práticas para aplicar esta funcionalidade.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Adicionar uma ligação para modelos de HTML5/CSS para o seu percurso do utilizador

Numa política personalizada, uma definição de conteúdo define a página de HTML5 URI que é utilizado para um passo da interface do Usuário especificado (por exemplo, as páginas de início de sessão ou Inscreva-se). Política de base define o aspeto e funcionalidade do padrão ao apontar para um URI de HTML5 ficheiros (CSS). Na política de extensão, é possível modificar o aspeto e funcionalidade substituindo LoadUri para o ficheiro do HTML5. Definições de conteúdo contêm os URLs para conteúdo externo que é definido por criar arquivos de HTML5/CSS, conforme apropriado. 

O `ContentDefinitions` seção contém uma série de `ContentDefinition` elementos XML. O atributo ID do `ContentDefinition` elemento Especifica o tipo de página que está relacionado com a definição de conteúdo. Ou seja, o elemento define o contexto no qual vai aplicar um modelo personalizado do HTML5/CSS. A tabela seguinte descreve o conjunto de IDs que são reconhecidos pelo motor de IEF e os tipos de página relacionados de definição de conteúdo.

| ID de definição de conteúdo | Modelo de HTML5 predefinido| Descrição | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Esta página é apresentada quando é encontrado uma exceção ou um erro. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção do fornecedor de identidade**. Esta página apresenta uma lista de fornecedores de identidade que os utilizadores escolherem durante o início de sessão. As opções são, normalmente, fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook e Google + ou contas locais. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção de fornecedor de identidade para inscrição**. Esta página apresenta uma lista de fornecedores de identidade que os utilizadores escolherem durante a inscrição. As opções são a fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook e Google + ou contas locais. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Esqueceu-se a página de palavra-passe**. Esta página contém um formulário que os utilizadores tem de concluir para iniciar uma reposição de palavra-passe.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de início de sessão de conta local**. Esta página contém um formulário para iniciar sessão com uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. O formulário pode conter uma caixa de entrada de texto e a caixa de entrada de palavra-passe. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de início de sessão de conta local**. Esta página contém um formulário para se inscrever para uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. O formulário pode conter vários controles de entrada, tais como: um texto de entrada caixa, uma caixa de entrada de palavra-passe, um botão de rádio, caixas de lista pendente de seleção única e caixas de verificação de seleção múltipla. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página do multi-factor authentication**. Nesta página, os usuários podem verificar seus números de telefone (através da utilização de texto ou voz) durante a inscrição ou início de sessão. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta de redes sociais**. Esta página contém um formulário que os utilizadores tem de concluir quando se inscrevem, utilizando uma conta existente de um fornecedor de identidade de redes sociais. Esta página é semelhante ao anterior conta social página de inscrição, exceto os campos de entrada de palavra-passe. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os utilizadores podem aceder ao atualizar o respetivo perfil. Esta página é semelhante à página de inscrição de conta de redes sociais, exceto os campos de entrada de palavra-passe. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou início de sessão unificada**. Esta página processa o processo de inscrição e início de sessão do utilizador. Os utilizadores podem utilizar fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook ou Google + ou contas locais.  |

## <a name="serving-dynamic-content"></a>Servir conteúdo dinâmico
Na [personalização da interface do Usuário configurar numa política personalizada](active-directory-b2c-ui-customization-custom.md) artigo, carregar ficheiros do HTML5 para o armazenamento de Blobs do Azure. Esses arquivos de HTML5 são estáticos e renderizar o HTML mesmo conteúdo para cada solicitação. 

Neste artigo, vai utilizar uma aplicação web do ASP.NET que pode aceitar parâmetros de cadeia de caracteres de consulta e resposta adequada. 

Nestas instruções,:
* Crie uma aplicação web ASP.NET Core que aloja os seus modelos de HTML5. 
* Adicionar um modelo personalizado do HTML5 _unified.cshtml_. 
* Publicar a aplicação web no App Service do Azure. 
* Definir os recursos de várias origens (CORS) de partilha para a sua aplicação web.
* Substituir o `LoadUri` elementos para apontar para o ficheiro do HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Passo 1: Criar uma aplicação Web ASP.NET

1. No Visual Studio, crie um projeto, selecionando **arquivo** > **New** > **projeto**.

2. Na **novo projeto** janela, selecione **Visual c#** > **Web** > **aplicação Web do ASP.NET Core (.NET Core)**.

3. Dê o nome (por exemplo, *Contoso.AADB2C.UI*) e, em seguida, selecione **OK**.

    ![Criar novo projeto do Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Selecione o **aplicação Web** modelo.

5. Definir a autenticação **sem autenticação**.

    ![Selecione o modelo de aplicativo Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Selecione **OK** para criar o projeto.

## <a name="step-2-create-mvc-view"></a>Passo 2: Criar a exibição do MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Passo 2.1: Transferir o modelo HTML5 interno do B2C
Seu modelo personalizado do HTML5 baseia-se o modelo HTML5 interno do Azure AD B2C. Pode baixar o [ficheiro unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) ou transferir o modelo a partir [pacote de iniciante](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Utilize este ficheiro de HTML5 para criar uma página de inscrição ou início de sessão unificada.

### <a name="step-22-add-the-mvc-view"></a>Passo 2.2: Adicionar o modo de exibição do MVC
1. Com o botão direito na pasta Views/Home e, em seguida **Add** > **Novo Item**.

    ![Adicionar novo item do MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. Na **adicionar o novo Item - Contoso.AADB2C.UI** janela, selecione **Web > ASP.NET**.

3. Selecione **MVC exibir página**.

4. Na **Name** caixa, altere o nome para **unified.cshtml**.

5. Selecione **Adicionar**.

    ![Adicionar a exibição do MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Se o *unified.cshtml* ficheiro já não está aberto, faça duplo clique no ficheiro para abri-lo e, em seguida, desmarque o conteúdo do ficheiro.

7. Neste passo a passo, removemos a referência à página de layout. Adicione o seguinte fragmento de código para _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Abra o _unified.cshtml_ ficheiro transferido a partir do modelo de HTML5 interno do Azure AD B2C.

9. Substitua o único arroba (@) com o valor de duplo arroba (@).

10. Copie o conteúdo do ficheiro e cole-o abaixo a definição de Layout. Seu código deve ser semelhante:

    ![ficheiro de Unified.cshtml depois de adicionar o HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Passo 2.3: Alterar a imagem de fundo

Localize a `<img>` elemento que contém o `ID` valor *background_background_image*e, em seguida, substitua o `src` valor com **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** ou qualquer outro imagem de fundo que pretende utilizar.

![Alterar o plano de fundo de página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Passo 2.4: Adicionar a sua vista para o controlador MVC

1. Open **Controllers\HomeController.cs**e adicionar o seguinte método: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Esse código especifica que o método deve usar uma *vista* ficheiro de modelo para processar uma resposta no browser. Porque não podemos especificar explicitamente o nome da *exibição* ficheiro de modelo MVC por predefinição, utiliza o _unified.cshtml_ ver o ficheiro no */Views/Home* pasta.
    
    Depois de adicionar o _unificada_ método, o seu código deverá ser semelhante:
    
    ![Alterar o controlador para renderizar a exibição](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Depurar a sua aplicação web e certifique-se de que o _unificada_ página está acessível (por exemplo, `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Etapa 2.5: Publicar no Azure
1. Na **Explorador de soluções**, clique com botão direito a **Contoso.AADB2C.UI** projeto e, em seguida, selecione **publicar**.

    ![Publicar no serviço de aplicações do Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Selecione o **serviço de aplicações do Microsoft Azure** mosaico e, em seguida, selecione **Publish**.

    ![Criar novo serviço de aplicações do Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    O **criar serviço de aplicações** é aberta a janela. No mesmo pode começar a criar todos os recursos do Azure necessários para executar a sua aplicação web ASP.NET no Azure.

    > [!NOTE]
    > Para obter mais informações sobre a publicação, consulte [criar uma aplicação web ASP.NET no Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. Na **nome da aplicação Web** , escreva um nome de aplicação exclusivo (os carateres válidos são-z, A-Z, 0-9 e hífen (-). O URL da aplicação Web é `http://<app_name>.azurewebsites.NET`, onde `<app_name>` é o nome da aplicação Web. Também pode aceitar o nome gerado automaticamente, que já é exclusivo.

4. Selecione **Criar** para começar a criar os recursos do Azure.

    ![Forneça as propriedades do serviço de aplicações](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Após concluir o processo de criação, o assistente publica a sua aplicação web ASP.NET no Azure e, em seguida, inicia a aplicação no browser predefinido.

5. Copie o URL dos _unificada_ página (por exemplo, _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Passo 3: Configurar a CORS no App Service do Azure
1. Na [portal do Azure](https://portal.azure.com/), selecione **dos serviços de aplicações**e, em seguida, selecione o nome da sua aplicação API.

    ![Selecionar a aplicação API no portal do Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. Na **configurações** secção, em **API** secção, selecione **CORS**.

    ![Selecione as definições de CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Na **CORS** janela, na **origens permitidas** caixa, faça o seguinte:

    * Introduza o URL ou URLs que pretende receber chamadas JavaScript são provenientes. Tem de utilizar todas as letras minúsculas nos URLs que introduzir.
    * Introduza um asterisco (*) para especificar que todos os domínios de origem são aceites.

4. Selecione **Guardar**.

    ![A janela CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Depois de selecionar **guardar**, a aplicação API aceita chamadas JavaScript a partir dos URLs especificados. 

## <a name="step-4-html5-template-validation"></a>Passo 4: Validação do modelo HTML5
O modelo de HTML5 estiver pronto para utilizar. No entanto, não está disponível no `ContentDefinition` código. Antes de poder adicionar `ContentDefinition` a política personalizada, certifique-se de que:
* O conteúdo é HTML5 acessível e não compatíveis.
* Seu servidor de conteúdos está ativado para CORS.

    >[!NOTE]
    >Para verificar se o site em que estiver hospedando o conteúdo tiver ativado o CORS e que pode testar solicitações CORS, vá para o [teste cors.org](https://test-cors.org/) Web site. 

* O seu conteúdo servido está seguro através de **HTTPS**.
* Estiver a utilizar *URLS absolutos*, tal como `https://yourdomain/content`, para todas as ligações, conteúdo CSS e imagens.

## <a name="step-5-configure-your-content-definition"></a>Passo 5: Configurar a definição de conteúdo
Para configurar `ContentDefinition`, efetue o seguinte procedimento:
1. Abra o ficheiro de base da sua política (por exemplo, *TrustFrameworkBase.xml*).

2. Procure o `<ContentDefinitions>` elemento e, em seguida, copie o conteúdo inteiro do `<ContentDefinitions>` nó.

3. Abra o ficheiro de extensão (por exemplo, *TrustFrameworkExtensions.xml*) e, em seguida, procure o `<BuildingBlocks>` elemento. Se o elemento não existir, adicioná-lo.

4. Cole o conteúdo inteiro dos `<ContentDefinitions>` nó que copiou como subordinado do `<BuildingBlocks>` elemento. 

5. Procure o `<ContentDefinition>` nó que contém `Id="api.signuporsignin"` no XML que copiou.

6. Alterar o valor de `LoadUri` partir _~/tenant/default/unified_ para _https://<app_name>.azurewebsites.net/home/unified_.  
    A diretiva personalizada deve ter um aspeto semelhante ao seguinte:
    
    ![A definição de conteúdo](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Passo 6: Carregar a política para o seu inquilino
1. Na [portal do Azure](https://portal.azure.com), mude para o [contexto do inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, selecione **do Azure AD B2C**.

2. Selecione **arquitetura de experiências de identidade**.

3. Selecione **todas as políticas**.

4. Selecione **carregar política**.

5. Selecione o **substituir a política, se existir** caixa de verificação.

6. Carregar o *TrustFrameworkExtensions.xml* de ficheiros e certifique-se de que ele passa a validação.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Passo 7: Testar a política personalizada com executar agora
1. Selecione **definições do Azure AD B2C**e, em seguida, selecione **Framework de experiência de identidade**.

    >[!NOTE]
    >Executar agora requer, pelo menos, um aplicativo para ser foi pré-registado no inquilino. Para saber como registar aplicações, veja o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.

2. Open **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) da entidade confiadora que carregou e, em seguida, selecione **executar agora**.  
    Deve conseguir ver o HTML5 personalizado com o plano de fundo que criou anteriormente.

    ![A política de inscrição ou início de sessão](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Passo 8: Adicionar conteúdo dinâmico
Alterar o plano de fundo com base no parâmetro de cadeia de caracteres de consulta com o nome _campaignId_. Seu aplicativo da RP (aplicações móveis e web) envia o parâmetro para o Azure AD B2C. A política lê o parâmetro e envia seu valor para o modelo HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Passo 8.1: Adicione um parâmetro de definição de conteúdo

Adicionar o `ContentDefinitionParameters` elemento ao fazer o seguinte:
1. Abra o *SignUpOrSignin* ficheiro da política (por exemplo, *SignUpOrSignin.xml*).

2. Sob o `<DefaultUserJourney>` nó, adicione o `UserJourneyBehaviors` nó:  

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Passo 8.2: Alterar o seu código aceite um parâmetro de cadeia de caracteres de consulta e substitua a imagem de fundo
Modificar o HomeController `unified` método para aceitar o parâmetro campaignId. O método verifica, em seguida, o parâmetro de valor e define o `ViewData["background"]` variável em conformidade.

1. Abra o *Controllers\HomeController.cs* do ficheiro e, em seguida, altere o `unified` método adicionando o seguinte trecho de código:

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Localize a `<img>` elemento com o ID `background_background_image`e substitua o `src` valor com `@ViewData["background"]`.

    ![Alterar o plano de fundo de página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: Carregar as alterações e publicar a política
1. Publicar o seu projeto do Visual Studio App Service do Azure.

2. Carregar o *SignUpOrSignin.xml* política para o Azure AD B2C.

3. Open **B2C_1A_signup_signin**, a política personalizada de RP que carregou e, em seguida, selecione **executar agora**.  
    Deverá ver a mesma imagem em segundo plano que foi apresentada anteriormente.

4. Copie o URL da barra de endereços do navegador.

5. Adicionar a _campaignId_ parâmetro de cadeia de caracteres para o URI de consulta. Por exemplo, adicionar `&campaignId=hawaii`, conforme mostrado na imagem seguinte:

    ![Alterar o plano de fundo de página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Selecione **Enter** para exibir a imagem de fundo do Havai.

    ![Alterar o plano de fundo de página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Altere o valor para *Tóquio*e, em seguida, selecione **Enter**.  
    O browser apresenta o plano de fundo de Tóquio.

    ![Alterar o plano de fundo de página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Passo 9: Alterar o resto do percurso do utilizador
Se selecionar a **Inscreva-se agora** link na página início de sessão, o navegador exibe a imagem de fundo padrão, não a imagem que definiu. Este comportamento surge porque alterou-se apenas a página de inscrição ou início de sessão. Para alterar o resto das definições de conteúdo de declaração personalizada:
1. Voltar para o "Passo 2" e efetue o seguinte:

    a. Transfira o *selfasserted* ficheiro.

    b. Copie o conteúdo do ficheiro.

    c. Criar uma nova vista *selfasserted*.

    d. Adicione *selfasserted* para o **home page** controlador.

2. Voltar para o "Passo 4" e faça o seguinte: 

    a. Na sua política de extensão, localize a `<ContentDefinition>` nó que contém `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, e `Id="api.localaccountpasswordreset"`.

    b. Definir o `LoadUri` atributo a seu *selfasserted* URI.

3. Voltar ao "Passo 8.2" e altere o código para aceitar os parâmetros de cadeia de caracteres de consulta, mas desta vez para o *selfasserted* função. 

4. Carregar o *TrustFrameworkExtensions.xml* política e certifique-se de que ele passa a validação.

5. Execute o teste de política e, em seguida, selecione **Inscreva-se agora** para ver o resultado.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Transferir os ficheiros de política concluída e o código
* Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) passo a passo, é recomendável que criar seu cenário com seus próprios arquivos de política personalizada. Para referência, nós fornecemos [arquivos de diretiva de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Pode baixar o código completo da [solução do Visual Studio de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




