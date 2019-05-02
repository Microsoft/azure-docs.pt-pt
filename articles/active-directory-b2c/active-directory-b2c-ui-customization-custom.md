---
title: Personalizar a interface de utilizador da sua aplicação utilizando uma política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre como personalizar uma interface do usuário com uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5dc60c1fbdbd04653160db4d7794f8887305859d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696888"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalizar a interface de utilizador da sua aplicação utilizando uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir este artigo, terá uma política personalizada de inscrição e início de sessão com a marca e do aspeto. Com o Azure Active Directory B2C (Azure AD B2C), obtém controle quase total do conteúdo HTML e CSS que é apresentado aos utilizadores. Quando utilizar uma política personalizada, configurar personalização da interface do Usuário em XML em vez de usar controles no portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos na [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md). Deve ter uma política personalizada do trabalho para inscrição e início de sessão com contas locais.

## <a name="page-ui-customization"></a>Personalização da IU da página

Ao utilizar a funcionalidade de personalização da interface do Usuário de página, pode personalizar o aspeto e funcionalidade de qualquer política personalizada. Também pode manter a consistência visual e de marca entre a sua aplicação e o Azure AD B2C.

Eis como funciona: O Azure AD B2C executa o código no browser do seu cliente e utiliza uma abordagem moderna chamada [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/). Em primeiro lugar, especifique um URL a política personalizada com conteúdo HTML personalizado. O Azure AD B2C une os elementos de IU com o conteúdo HTML que é carregado a partir do URL e, em seguida, apresenta a página ao cliente.

## <a name="create-your-html5-content"></a>Criar o HTML5 conteúdo

Crie o HTML conteúdo com o nome da marca do seu produto no título.

1. Copie o seguinte fragmento HTML. É bem formado HTML5 com um elemento vazio denominado *\<div id = "api"\>\</div\>* localizada dentro do *\<corpo\>* etiquetas. Este elemento indica onde o conteúdo do Azure AD B2C está a ser inserido.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

2. Cole o fragmento copiado num editor de texto e, em seguida, guarde o ficheiro como *ui.html personalizar*.

## <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de Blobs do Azure

>[!NOTE]
> Neste artigo, utilizamos o armazenamento de Blobs do Azure para alojar o nosso conteúdo. Pode optar por hospedar o seu conteúdo num servidor web, mas deve [ativar o CORS no seu servidor web](https://enable-cors.org/server.html).

Para alojar este conteúdo HTML no armazenamento de BLOBs, faça o seguinte:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Sobre o **Hub** menu, selecione **New** > **armazenamento** > **conta de armazenamento**.
3. Introduza uma única **nome** para a sua conta de armazenamento.
4. **Modelo de implementação** pode permanecer **do Resource Manager**.
5. Alteração **tipo de conta** ao **armazenamento de BLOBs**.
6. **Desempenho** pode permanecer **padrão**.
7. **A replicação** pode permanecer **RA-GRS**.
8. **Camada de acesso** pode permanecer **frequente**.
9. **Encriptação do serviço de armazenamento** pode permanecer **desativado**.
10. Selecione um **subscrição** para a sua conta de armazenamento.
11. Criar uma **grupo de recursos** ou selecione um existente.
12. Selecione o **localização geográfica** para a sua conta de armazenamento.
13. Clique em **Criar** para criar a conta do Storage.  
    Depois de concluída a implementação, o **conta de armazenamento** painel abre-se automaticamente.

## <a name="create-a-container"></a>Criar um contentor

Para criar um contentor público no armazenamento de BLOBs, faça o seguinte:

1. Clique nas **descrição geral** separador.
2. Clique em **contentor**.
3. Para **Name**, tipo **$root**.
4. Definir **tipo de acesso** ao **Blob**.
5. Clique em **$root** para abrir o contêiner do novo.
6. Clique em **Carregar**.
7. Clique no ícone de pasta junto a **selecionar um ficheiro**.
8. Aceda a **ui.html personalizar**, que criou anteriormente na secção de personalização da IU da página.
9. Clique em **Carregar**.
10. Selecione o blob de personalizar ui.html que carregou.
11. Junto a **URL**, clique em **cópia**.
12. Num browser, cole o URL copiado e vá para o site. Se o site não estiver acessível, certifique-se de que o tipo de acesso do contentor está definido como **BLOBs**.

## <a name="configure-cors"></a>Configurar o CORS

Configure o armazenamento de BLOBs para a partilha de recursos de várias origens, fazendo o seguinte:

1. No menu, selecione **CORS**.
2. Para **permitido origens**, introduza `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Tem de utilizar todas as letras minúsculas, ao introduzir o nome do seu inquilino.
3. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
4. Para **cabeçalhos permitidos**, introduza um asterisco (*).
5. Para **cabeçalhos expostos**, introduza um asterisco (*).
6. Para **idade máxima**, introduza a 200.
7. Clique em **Guardar**.

## <a name="test-cors"></a>Testar o CORS

Valide a que está pronto, fazendo o seguinte:

1. Vá para o [www.test-cors.org](https://www.test-cors.org/) Web site e, em seguida, cole o URL na **URL remoto** caixa.
2. Clique em **enviar pedido**.  
    Se receber um erro, certifique-se de que sua [definições de CORS](#configure-cors) estão corretos. Também poderá ter de limpar a cache do browser ou abra uma sessão de navegação privada ao premir Ctrl + Shift + P.

## <a name="modify-the-extensions-file"></a>Modificar o arquivo de extensões

Para configurar a personalização da interface do Usuário, copie os **ContentDefinition** e os respetivos elementos filho do ficheiro de base para o arquivo de extensões.

1. Abra o ficheiro de base da sua política. Por exemplo, *TrustFrameworkBase.xml*.
2. Procure e copie todo o conteúdo do **ContentDefinitions** elemento.
3. Abra o ficheiro de extensão. Por exemplo, *TrustFrameworkExtensions.xml*. Procure o **BuildingBlocks** elemento. Se o elemento não existir, adicioná-lo.
4. Cole o conteúdo inteiro dos **ContentDefinitions** elemento que copiou como subordinado da **BuildingBlocks** elemento. 
5. Procure o **ContentDefinition** elemento que contém `Id="api.signuporsignin"` no XML que copiou.
6. Alterar o valor de **LoadUri** para o URL do arquivo HTML que carregou para o armazenamento. Por exemplo, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
    A diretiva personalizada deve ter um aspeto semelhante ao seguinte:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

7. Guarde o ficheiro de extensões.

## <a name="upload-your-updated-custom-policy"></a>Carregar a política personalizada atualizada

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **arquitetura de experiências de identidade**.
2. Clique em **todas as políticas**.
3. Clique em **carregar política**.
4. Carregue o ficheiro de extensões que alterou anteriormente.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada com **executar agora**

1. Sobre o **do Azure AD B2C** painel, aceda à **todas as políticas**.
2. Selecione a política personalizada que carregou e clique nas **executar agora** botão.
3. Deverá conseguir inscrever-se utilizando um endereço de e-mail.

## <a name="reference"></a>Referência

Pode encontrar modelos de exemplo para a personalização da interface do Usuário aqui:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

A pasta de sample_templates/wingtip contém os seguintes ficheiros HTML:

| Modelo de HTML5 | Descrição |
|----------------|-------------|
| *phonefactor.html* | Utilize este ficheiro como um modelo para uma página de autenticação multifator. |
| *resetpassword.html* | Utilizar este ficheiro como um modelo para um Esqueceu-se a página de palavra-passe. |
| *selfasserted.html* | Utilize este ficheiro como um modelo para uma página de inscrição de conta de redes sociais, uma página de inscrição de conta local ou uma página de início de sessão da conta local. |
| *unified.html* | Utilize este ficheiro como um modelo para uma página de inscrição ou início de sessão unificada. |
| *updateprofile.html* | Utilize este ficheiro como um modelo para uma página de atualização de perfil. |

Modificar a secção de política personalizada de inscrição ou início de sessão, configurou a definição de conteúdo para `api.idpselections`. O conjunto completo de conteúdo são de IDs de definição reconhecidas pelo framework de experiência de identidade do Azure AD B2C e suas descrições na seguinte tabela:

| ID de definição de conteúdo | Descrição | 
|-----------------------|-------------|
| *api.error* | **Página de erro**. Esta página é apresentada quando é encontrado uma exceção ou um erro. |
| *api.idpselections* | **Página de seleção do fornecedor de identidade**. Esta página contém uma lista de fornecedores de identidade que o utilizador pode escolher durante o início de sessão. Estas opções são a fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook e Google + ou contas locais. |
| *api.idpselections.signup* | **Seleção de fornecedor de identidade para inscrição**. Esta página contém uma lista de fornecedores de identidade que o utilizador pode escolher entre durante a inscrição. Estas opções são a fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook e Google + ou contas locais. |
| *api.localaccountpasswordreset* | **Esqueceu-se a página de palavra-passe**. Esta página contém um formulário que o utilizador deve realizar para iniciar uma reposição de palavra-passe.  |
| *api.localaccountsignin* | **Página de início de sessão de conta local**. Esta página contém um formulário de início de sessão para iniciar sessão com uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. O formulário pode conter uma caixa de entrada de texto e a caixa de entrada de palavra-passe. |
| *api.localaccountsignup* | **Página de inscrição de conta local**. Esta página contém um formulário de inscrição para inscrever-se de uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. O formulário pode conter vários controles de entrada, como uma caixa de entrada de texto, uma caixa de entrada de palavra-passe, um botão de rádio, caixas de lista pendente de seleção única e caixas de verificação de seleção múltipla. |
| *api.phonefactor* | **Página do multi-factor authentication**. Nesta página, os usuários podem verificar seus números de telefone (através da utilização de texto ou voz) durante a inscrição ou início de sessão. |
| *api.selfasserted* | **Página de inscrição de conta de redes sociais**. Esta página contém um formulário de inscrição tem de concluir os utilizadores quando se inscrevem, utilizando uma conta existente de um fornecedor de identidade de redes sociais como o Facebook ou o Google +. Esta página é semelhante ao anterior conta social página de inscrição, exceto os campos de entrada de palavra-passe. |
| *api.selfasserted.profileupdate* | **Página de atualização de perfil**. Esta página contém um formulário que os utilizadores podem utilizar para atualizar seu perfil. Esta página é semelhante à página de inscrição de conta de redes sociais, exceto os campos de entrada de palavra-passe. |
| *api.signuporsignin* | **Página de inscrição ou início de sessão unificada**. Esta página lida com a inscrição e início de sessão de utilizadores, que podem utilizar fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook ou Google + ou contas locais.  |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os elementos de interface do Usuário que pode ser personalizado, consulte [guia de referência para a personalização da interface do Usuário para as políticas incorporadas](active-directory-b2c-reference-ui-customization.md).
