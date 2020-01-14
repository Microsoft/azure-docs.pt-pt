---
title: Personalizar a interface do usuário (IU) dinamicamente com políticas personalizadas
titleSuffix: Azure AD B2C
description: Dê suporte a várias experiências de identidade visual com conteúdo HTML5/CSS que é alterado dinamicamente no tempo de execução.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ae29a068ef29898c3fa27d3620d1e6be0be4bf3b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931207"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: configurar a interface do usuário com conteúdo dinâmico usando políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usando políticas personalizadas Azure Active Directory B2C (Azure AD B2C), você pode enviar um parâmetro em uma cadeia de caracteres de consulta. Ao transmitir o parâmetro para o ponto final HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou de início de sessão do Azure AD B2C, com base num parâmetro que transmite a partir da sua aplicação Web ou móvel.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo se concentra em como personalizar a interface do usuário do Azure AD B2C com *conteúdo dinâmico* usando políticas personalizadas. Para começar, consulte [personalização da interface do usuário em uma política personalizada](active-directory-b2c-ui-customization-custom.md).

>[!NOTE]
>O artigo Azure AD B2C, [Configurar a personalização da interface do usuário em uma política personalizada](active-directory-b2c-ui-customization-custom.md), discute os seguintes conceitos básicos:
> * O recurso de personalização da interface do usuário da página.
> * Ferramentas essenciais para testar o recurso de personalização da interface do usuário da página usando nosso conteúdo de exemplo.
> * Os principais elementos da interface do usuário de cada tipo de página.
> * Práticas recomendadas para aplicar esse recurso.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Adicionar um link para modelos HTML5/CSS para o percurso do usuário

Em uma política personalizada, uma definição de conteúdo define o URI da página HTML5 que é usado para uma etapa de interface do usuário especificada (por exemplo, as páginas de entrada ou inscrição). A política de base define a aparência padrão apontando para um URI de arquivos HTML5 (no CSS). Na política de extensão, você pode modificar a aparência substituindo o LoadUri para o arquivo HTML5. As definições de conteúdo contêm URLs para o conteúdo externo que é definido pela criação de arquivos HTML5/CSS, conforme apropriado.

A seção `ContentDefinitions` contém uma série de elementos `ContentDefinition` XML. O atributo ID do elemento `ContentDefinition` especifica o tipo de página relacionado à definição de conteúdo. Ou seja, o elemento define o contexto que um modelo HTML5/CSS personalizado irá aplicar. A tabela a seguir descreve o conjunto de IDs de definição de conteúdo que são reconhecidos pelo mecanismo IEF e os tipos de página relacionados a eles.

| ID de definição de conteúdo | Modelo HTML5 padrão| Descrição |
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção do provedor de identidade**. Esta página lista os provedores de identidade que os usuários podem escolher durante a entrada. As opções geralmente são provedores de identidade empresarial, provedores de identidade social, como Facebook e Google +, ou contas locais. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção do provedor de identidade para inscrição**. Esta página lista os provedores de identidade que os usuários podem escolher durante a inscrição. As opções são provedores de identidade Enterprise, provedores de identidade social, como Facebook e Google +, ou contas locais. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página esqueci a senha**. Esta página contém um formulário que os usuários devem concluir para iniciar uma redefinição de senha.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de entrada da conta local**. Esta página contém um formulário para entrar com uma conta local com base em um endereço de email ou um nome de usuário. O formulário pode conter uma caixa de entrada de texto e uma caixa de entrada de senha. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição da conta local**. Esta página contém um formulário para se inscrever em uma conta local com base em um endereço de email ou um nome de usuário. O formulário pode conter vários controles de entrada, como: uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de opção, caixas suspensas de seleção única e caixas de seleção de várias seleções. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multifator**. Nessa página, os usuários podem verificar seus números de telefone (usando texto ou voz) durante a inscrição ou entrada. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta social**. Esta página contém um formulário que os usuários devem concluir ao se inscreverem usando uma conta existente de um provedor de identidade social. Esta página é semelhante à página de inscrição de conta social anterior, exceto para os campos de entrada de senha. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os usuários podem acessar para atualizar seu perfil. Esta página é semelhante à página de inscrição de conta social, exceto para os campos de entrada de senha. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou entrada unificada**. Esta página manipula o processo de inscrição e entrada do usuário. Os usuários podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook ou Google +, ou contas locais.  |

## <a name="serving-dynamic-content"></a>Atendendo ao conteúdo dinâmico
No artigo [Configurar a personalização da interface do usuário em uma política personalizada](active-directory-b2c-ui-customization-custom.md) , você carrega arquivos HTML5 para o armazenamento de BLOBs do Azure. Esses arquivos HTML5 são estáticos e renderizam o mesmo conteúdo HTML para cada solicitação.

Neste artigo, você usa um aplicativo Web ASP.NET, que pode aceitar parâmetros de cadeia de caracteres de consulta e responder de acordo.

Neste tutorial, você:
* Crie um aplicativo Web ASP.NET Core que hospede seus modelos HTML5.
* Adicione um modelo HTML5 personalizado, _Unified. cshtml_.
* Publique seu aplicativo Web no serviço Azure App.
* Defina o CORS (compartilhamento de recursos entre origens) para seu aplicativo Web.
* Substitua os elementos de `LoadUri` para apontar para o arquivo HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Etapa 1: criar um aplicativo Web ASP.NET

1. No Visual Studio, crie um projeto selecionando **arquivo** > **novo** > **projeto**.

2. Na janela **novo projeto** , selecione **Visual C#**  > **Web** > **ASP.NET Core aplicativo Web (.NET Core)** .

3. Nomeie o aplicativo (por exemplo, *contoso. AADB2C. UI*) e, em seguida, selecione **OK**.

    ![Criar novo projeto do Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Selecione o modelo de **aplicativo Web** .

5. Defina a autenticação como **sem autenticação**.

    ![Selecionar modelo de aplicativo Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Selecione **OK** para criar o projeto.

## <a name="step-2-create-mvc-view"></a>Etapa 2: criar a exibição do MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Etapa 2,1: baixar o modelo de HTML5 interno do B2C
O modelo HTML5 personalizado se baseia no modelo HTML5 interno Azure AD B2C. Você pode baixar o [arquivo Unified. html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) ou baixar o modelo do [pacote inicial](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Use esse arquivo HTML5 para criar uma página de inscrição ou entrada unificada.

### <a name="step-22-add-the-mvc-view"></a>Etapa 2,2: adicionar a exibição do MVC
1. Clique com o botão direito do mouse na pasta views/Home e **adicione** > **novo item**.

    ![Adicionar item de menu novo item no Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. Na janela **Adicionar novo item-contoso. AADB2C. UI** , selecione **Web > ASP.net**.

3. Selecione a **página exibição do MVC**.

4. Na caixa **nome** , altere o nome para **Unified. cshtml**.

5. Selecione **Adicionar**.

    ![Caixa de diálogo Adicionar novo item no Visual Studio com a página exibição do MVC realçada](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Se o arquivo *Unified. cshtml* ainda não estiver aberto, clique duas vezes no arquivo para abri-lo e, em seguida, limpe o conteúdo do arquivo.

7. Para esta explicação, removemos a referência à página layout. Adicione o seguinte trecho de código a _Unified. cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Abra o arquivo _Unified. cshtml_ que você baixou de Azure ad B2C modelo de HTML5 interno.

9. Substitua os sinais de arroba únicos (@) por sinais duplos de arroba (@ @).

10. Copie o conteúdo do arquivo e cole-o abaixo da definição de layout. Seu código deve ter a seguinte aparência:

    ![arquivo. cshtml unificado após a adição do HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Etapa 2,3: alterar a imagem de plano de fundo

Localize o elemento `<img>` que contém o valor de `ID` *background_background_image*e, em seguida, substitua o valor de `src` por **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** ou qualquer outra imagem de plano de fundo que você deseja usar.

![elemento img com valor de src background_background_image personalizado](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Etapa 2,4: adicionar sua exibição ao controlador MVC

1. Abra **Controllers\HomeController.cs**e adicione o seguinte método:

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Esse código especifica que o método deve usar um arquivo de modelo de *exibição* para renderizar uma resposta para o navegador. Como não especificamos explicitamente o nome do arquivo de modelo de *exibição* , o MVC usa como padrão o arquivo de exibição _Unified. cshtml_ na pasta */views/Home*

    Depois de adicionar o método _unificado_ , seu código deverá ter a seguinte aparência:

    ![Alterar o controlador para renderizar a exibição](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Depure seu aplicativo Web e certifique-se de que a página _unificada_ esteja acessível (por exemplo, `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Etapa 2,5: publicar no Azure
1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **contoso. AADB2C. UI** e selecione **publicar**.

    ![Publish to Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Selecione o bloco **Microsoft Azure serviço de aplicativo** e, em seguida, selecione **publicar**.

    ![Criar novo serviço de aplicativo Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    A janela **Criar serviço de aplicativo** é aberta. Nele, você pode começar a criar todos os recursos do Azure necessários para executar o aplicativo Web ASP.NET no Azure.

    > [!NOTE]
    > Para obter mais informações sobre publicação, consulte [criar um aplicativo web ASP.net no Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. Na caixa **nome do aplicativo Web** , digite um nome de aplicativo exclusivo (os caracteres válidos são a-z, a-z, 0-9 e o hífen (-). O URL da aplicação Web é `http://<app_name>.azurewebsites.NET`, onde `<app_name>` é o nome da aplicação Web. Também pode aceitar o nome gerado automaticamente, que já é exclusivo.

4. Selecione **Criar** para começar a criar os recursos do Azure.

    ![Fornecer propriedades do serviço de aplicativo](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Depois que o processo de criação for concluído, o assistente publicará o aplicativo Web ASP.NET no Azure e, em seguida, iniciará o aplicativo no navegador padrão.

5. Copie a URL da página _unificada_ (por exemplo, _https://< app_name >. azurewebsites. net/Home/Unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Etapa 3: configurar o CORS no serviço Azure App
1. Na [portal do Azure](https://portal.azure.com/), selecione **serviços de aplicativos**e, em seguida, selecione o nome do aplicativo de API.

    ![Selecione aplicativo de API no portal do Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. Na seção **configurações** , na seção **API** , selecione **CORS**.

    ![Item de menu CORS realçado no menu serviço de aplicativo no portal do Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Na janela **CORS** , na caixa **origens permitidas** , siga um destes procedimentos:

    * Insira a URL ou URLs das quais você deseja permitir que as chamadas JavaScript venham. Você precisa usar todas as letras minúsculas nas URLs que você inserir.
    * Insira um asterisco (*) para especificar que todos os domínios de origem sejam aceitos.

4. Selecione **Guardar**.

    ![Página de configurações de CORS com asterisco realçado em origens permitidas](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Depois de selecionar **salvar**, o aplicativo de API aceita chamadas JavaScript das URLs especificadas.

## <a name="step-4-html5-template-validation"></a>Etapa 4: validação do modelo HTML5
Seu modelo HTML5 está pronto para uso. No entanto, ele não está disponível no código de `ContentDefinition`. Para poder adicionar `ContentDefinition` à política personalizada, verifique se:
* Seu conteúdo está em conformidade com o HTML5 e está acessível.
* O servidor de conteúdo está habilitado para CORS.

    >[!NOTE]
    >Para verificar se o site em que você está hospedando o conteúdo habilitou CORS e pode testar as solicitações de CORS, vá para o site do [Test-CORS.org](https://test-cors.org/) .

* Seu conteúdo servido é seguro por **https**.
* Você está usando *URLs absolutas*, como `https://yourdomain/content`, para todos os links, conteúdo CSS e imagens.

## <a name="step-5-configure-your-content-definition"></a>Etapa 5: configurar sua definição de conteúdo
Para configurar `ContentDefinition`, faça o seguinte:
1. Abra o arquivo base da sua política (por exemplo, *TrustFrameworkBase. xml*).

2. Pesquise o elemento `<ContentDefinitions>` e copie todo o conteúdo do nó `<ContentDefinitions>`.

3. Abra o arquivo de extensão (por exemplo, *TrustFrameworkExtensions. xml*) e, em seguida, procure o elemento `<BuildingBlocks>`. Se o elemento não existir, adicione-o.

4. Cole todo o conteúdo do `<ContentDefinitions>` nó que você copiou como um filho do elemento `<BuildingBlocks>`.

5. Procure o nó `<ContentDefinition>` que contém `Id="api.signuporsignin"` no XML que você copiou.

6. Altere o valor de `LoadUri` de _~/Tenant/default/Unified_ para _https://< app_name >. azurewebsites. net/Home/Unified_.
    Sua política personalizada deve ser parecida com a seguinte:

    ![Exemplo de trecho XML com elemento LoadUri realçado](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Etapa 6: carregar a política para seu locatário
1. Na [portal do Azure](https://portal.azure.com), selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o locatário Azure ad B2C.

1. Na portal do Azure, procure e selecione **Azure ad B2C**.

1. Selecione **Identity Experience Framework**.

1. Selecione **todas as políticas**.

1. Selecione **carregar política**.

1. Marque a caixa de seleção **substituir a política se ela existir** .

1. Carregue o arquivo *TrustFrameworkExtensions. xml* e certifique-se de que ele passe na validação.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Etapa 7: testar a política personalizada usando executar agora
1. Selecione **configurações de Azure ad B2C**e, em seguida, selecione **estrutura de experiência de identidade**.

    >[!NOTE]
    >Executar agora exige que pelo menos um aplicativo seja preregistrado no locatário. Para saber como registrar aplicativos, consulte o artigo Azure AD B2C [introdução](active-directory-b2c-get-started.md) ou o artigo [registro de aplicativo](active-directory-b2c-app-registration.md) .

2. Abra **B2C_1A_signup_signin**, a política personalizada de RP (terceira parte confiável) que você carregou e, em seguida, selecione **executar agora**.
    Você deve ser capaz de ver seu HTML5 personalizado com o plano de fundo que você criou anteriormente.

    ![Sua política de inscrição ou entrada](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Etapa 8: adicionar conteúdo dinâmico
Altere o plano de fundo com base no parâmetro de cadeia de caracteres de consulta chamado _campaignid_. Seu aplicativo RP (aplicativos Web e móveis) envia o parâmetro para Azure AD B2C. Sua política lê o parâmetro e envia seu valor para o modelo HTML5.

### <a name="step-81-add-a-content-definition-parameter"></a>Etapa 8,1: adicionar um parâmetro de definição de conteúdo

Adicione o elemento `ContentDefinitionParameters` fazendo o seguinte:
1. Abra o arquivo *SignUpOrSignin* da sua política (por exemplo, *SignUpOrSignin. xml*).

2. No nó `<DefaultUserJourney>`, adicione o nó `UserJourneyBehaviors`:

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

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Etapa 8,2: alterar seu código para aceitar um parâmetro de cadeia de caracteres de consulta e substituir a imagem de plano de fundo
Modifique o método HomeController `unified` para aceitar o parâmetro campaignid. Em seguida, o método verifica o valor do parâmetro e define a variável de `ViewData["background"]` de acordo.

1. Abra o arquivo *Controllers\HomeController.cs* e, em seguida, altere o método `unified` adicionando o seguinte trecho de código:

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

2. Localize o elemento `<img>` com a ID `background_background_image`e substitua o valor `src` por `@ViewData["background"]`.

    ![elemento img com valor src realçado ](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8,3: carregar as alterações e publicar sua política
1. Publique seu projeto do Visual Studio no serviço Azure App.

2. Carregue a política *SignUpOrSignin. xml* para Azure ad B2C.

3. Abra **B2C_1A_signup_signin**, a política personalizada do RP que você carregou e, em seguida, selecione **executar agora**.
    Você deve ver a mesma imagem de plano de fundo exibida anteriormente.

4. Copie a URL da barra de endereços do navegador.

5. Adicione o parâmetro de cadeia de caracteres de consulta _campaignid_ ao URI. Por exemplo, adicione `&campaignId=hawaii`, conforme mostrado na imagem a seguir:

    ![URI com o parâmetro de cadeia de caracteres de consulta campaignid realçado](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Selecione **Enter** para exibir a imagem de tela de fundo do Havaí.

    ![Página de entrada de inscrição com plano de fundo personalizado de imagem do Havaí](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Altere o valor para *Tóquio*e, em seguida, selecione **Enter**.
    O navegador exibe o plano de fundo de Tokyo.

    ![Página de entrada de inscrição com plano de fundo personalizado da imagem de Tokyo](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Etapa 9: alterar o restante do percurso do usuário
Se você selecionar o link **inscrever** -se agora na página de entrada, o navegador exibirá a imagem de plano de fundo padrão, não a imagem que você definiu. Esse comportamento ocorre porque você alterou apenas a página de inscrição ou de entrada. Para alterar o restante das definições de conteúdo de autodeclaração:
1. Volte para a "etapa 2" e faça o seguinte:

    a. Baixe o arquivo *selfasserted* .

    b. Copie o conteúdo do arquivo.

    c. Crie uma nova exibição, *selfasserted*.

    d. Adicione *selfasserted* ao controlador **inicial** .

2. Volte para a "etapa 4" e faça o seguinte:

    a. Em sua política de extensão, localize o `<ContentDefinition>` nó que contém `Id="api.selfasserted"`, `Id="api.localaccountsignup"`e `Id="api.localaccountpasswordreset"`.

    b. Defina o atributo `LoadUri` para o URI *selfasserted* .

3. Volte para a "etapa 8,2" e altere seu código para aceitar parâmetros de cadeia de caracteres de consulta, mas desta vez para a função *selfasserted* .

4. Carregue a política *TrustFrameworkExtensions. xml* e certifique-se de que ela passe na validação.

5. Execute o teste de política e, em seguida, selecione **inscrever-se agora** para ver o resultado.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Adicional Baixar o código e os arquivos de política completos
* Depois de concluir as instruções introdução [às políticas personalizadas](active-directory-b2c-get-started-custom.md) , recomendamos que você crie seu cenário usando seus próprios arquivos de política personalizados. Para sua referência, fornecemos [arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Você pode baixar o código completo da [solução de exemplo do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
