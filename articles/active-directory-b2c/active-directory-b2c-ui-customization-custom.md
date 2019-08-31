---
title: Personalizar a interface do usuário do seu aplicativo usando uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como personalizar uma interface do usuário usando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e79d7a4b97f010b035f5c864682b4d3882a21393
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171922"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário do seu aplicativo usando uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir este artigo, você terá uma política personalizada de inscrição e entrada com sua marca e aparência. Com o Azure Active Directory B2C (Azure AD B2C), você obtém controle quase total do conteúdo HTML e CSS que é apresentado aos usuários. Ao usar uma política personalizada, você configura a personalização da interface do usuário em XML em vez de usar controles na portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas](active-directory-b2c-get-started-custom.md). Você deve ter uma política personalizada de trabalho para inscrever-se e entrar com contas locais.

## <a name="page-ui-customization"></a>Personalização da IU da página

Usando o recurso de personalização da interface do usuário da página, você pode personalizar a aparência de qualquer política personalizada. Também pode manter a consistência visual e de marca entre a sua aplicação e o Azure AD B2C.

Eis como funciona: Azure AD B2C executa o código no navegador do cliente e usa uma abordagem moderna chamada [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/). Primeiro, especifique uma URL na política personalizada com conteúdo HTML personalizado. O Azure AD B2C une os elementos de IU com o conteúdo HTML que é carregado a partir do URL e, em seguida, apresenta a página ao cliente.

## <a name="create-your-html5-content"></a>Criar seu conteúdo HTML5

Crie conteúdo HTML com o nome da marca do produto no título.

1. Copie o trecho de código HTML a seguir. Ele é um HTML5 bem formado com um elemento vazio chamado *\<div id = "API"\>\</div\>* localizado nas *\<marcas body\>* . Esse elemento indica onde Azure AD B2C conteúdo deve ser inserido.

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

2. Cole o trecho copiado em um editor de texto e, em seguida, salve o arquivo como *Customize-UI. html*.

> [!NOTE]
> Os elementos de formulário HTML serão removidos devido a restrições de segurança se você usar login.microsoftonline.com. Use b2clogin.com se você quiser usar elementos de formulário HTML em seu conteúdo HTML personalizado. Consulte [usar b2clogin.com](b2clogin.md) para obter outros benefícios.

## <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de Blobs do Azure

>[!NOTE]
> Neste artigo, usamos o armazenamento de BLOBs do Azure para hospedar nosso conteúdo. Você pode optar por hospedar o conteúdo em um servidor Web, mas deve [habilitar o CORS em seu servidor Web](https://enable-cors.org/server.html).

Para hospedar esse conteúdo HTML no armazenamento de BLOBs, faça o seguinte:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu **Hub** , selecione **novo** > **armazenamento** > **conta de armazenamento**.
3. Selecione uma **assinatura** para sua conta de armazenamento.
4. Crie um **grupo de recursos** ou selecione um existente.
5. Insira um **nome** exclusivo para sua conta de armazenamento.
6. Selecione a **localização geográfica** da sua conta de armazenamento. 
7. O **modelo de implantação** pode permanecer no **Resource Manager**.
8. O **desempenho** pode permanecer **padrão**.
9. Altere o **tipo de conta** para o armazenamento de **BLOBs**.
10. A **replicação** pode permanecer **ra-grs**.
11. A **camada de acesso** pode permanecer **quente**. 
12. Clique em **examinar + criar** para criar a conta de armazenamento.  
    Depois que a implantação for concluída, a folha **conta de armazenamento** será aberta automaticamente.

## <a name="create-a-container"></a>Criar um contentor

Para criar um contêiner público no armazenamento de BLOBs, faça o seguinte:

1. Em **serviço blob** no menu à esquerda, selecione BLOBs.
2. Clique em **+ contêiner**.
3. Para **nome**, insira *raiz*. Isso pode ser um nome de sua escolha, por exemplo, *wingtiptoys*, mas usamos *root* neste exemplo para simplificar.
4. Para **nível de acesso público**, selecione **blob**e **OK**.
5. Clique em **raiz** para abrir o novo contêiner.
6. Clique em **Carregar**.
7. Clique no ícone de pasta ao lado de **selecionar um arquivo**.
8. Navegue até e selecione **Customize-UI. html** que você criou anteriormente na seção personalização da interface do usuário da página.
9. Se você quiser carregar para uma subpasta, expanda **avançado** e insira um nome de pasta em **carregar para a pasta**.
10. Selecione **Upload**.
11. Selecione o blob **Customize-UI. html** que você carregou.
12. À direita da caixa de texto **URL** , selecione o ícone **copiar para área de transferência** para copiar a URL para a área de transferência.
13. No navegador da Web, navegue até a URL que você copiou para verificar se o blob que você carregou está acessível. Se ele estiver inacessível, por exemplo, se você encontrar `ResourceNotFound` um erro, verifique se o tipo de acesso do contêiner está definido como **blob**.

## <a name="configure-cors"></a>Configurar o CORS

Configure o armazenamento de BLOBs para compartilhamento de recursos entre origens fazendo o seguinte:

1. No menu, selecione **CORS**.
2. Para **origens permitidas**, `https://your-tenant-name.b2clogin.com`insira. Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário.
3. Para **métodos permitidos**, selecione `GET` e `OPTIONS`.
4. Para **cabeçalhos permitidos**, insira um asterisco (*).
5. Para **cabeçalhos expostos**, insira um asterisco (*).
6. Para **idade máxima**, insira 200.
7. Clique em **Guardar**.

## <a name="test-cors"></a>Testar CORS

Valide que você está pronto fazendo o seguinte:

1. Vá para o site do [www.Test-CORS.org](https://www.test-cors.org/) e cole a URL na caixa **URL remota** .
2. Clique em **Enviar solicitação**.  
    Se você receber um erro, verifique se [as configurações de CORS](#configure-cors) estão corretas. Talvez você também precise limpar o cache do navegador ou abrir uma sessão de navegação em particular pressionando Ctrl + Shift + P.

## <a name="modify-the-extensions-file"></a>Modificar o arquivo de extensões

Para configurar a personalização da interface do usuário, você copia o **ContentDefinition** e seus elementos filho do arquivo base para o arquivo de extensões.

1. Abra o arquivo base da sua política. Por exemplo, *TrustFrameworkBase. xml*.
2. Pesquise e copie todo o conteúdo do elemento **ContentDefinitions** .
3. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions. xml*. Procure o elemento **BuildingBlocks** . Se o elemento não existir, adicione-o.
4. Cole todo o conteúdo do elemento **ContentDefinitions** que você copiou como um filho do elemento **BuildingBlocks** . 
5. Procure o elemento **ContentDefinition** que contém `Id="api.signuporsignin"` no XML que você copiou.
6. Altere o valor de **LoadUri** para a URL do arquivo HTML que você carregou no armazenamento. Por exemplo, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
    Sua política personalizada deve ser parecida com a seguinte:

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

7. Salve o arquivo de extensões.

## <a name="upload-your-updated-custom-policy"></a>Carregar sua política personalizada atualizada

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **Identity Experience Framework**.
2. Clique em **todas as políticas**.
3. Clique em **carregar política**.
4. Carregue o arquivo de extensões que você alterou anteriormente.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada usando **executar agora**

1. Na folha **Azure ad B2C** , vá para **todas as políticas**.
2. Selecione a política personalizada que você carregou e clique no botão **executar agora** .
3. Você deve ser capaz de se inscrever usando um endereço de email.

## <a name="reference"></a>Referência

### <a name="sample-templates"></a>Modelos de exemplo
Você pode encontrar modelos de exemplo para personalização da interface do usuário aqui:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

A pasta sample_templates/Wingtip contém os seguintes arquivos HTML:

| Modelo HTML5 | Descrição |
|----------------|-------------|
| *phonefactor.html* | Use esse arquivo como um modelo para uma página de autenticação multifator. |
| *resetpassword.html* | Use esse arquivo como um modelo para uma página esquecida de senha. |
| *selfasserted.html* | Use esse arquivo como um modelo para uma página de inscrição de conta social, uma página de inscrição de conta local ou uma página de entrada de conta local. |
| *unified.html* | Use esse arquivo como um modelo para uma página de inscrição ou entrada unificada. |
| *updateprofile.html* | Use este arquivo como um modelo para uma página de atualização de perfil. |

Aqui estão as etapas sobre como usar o exemplo. 
1. Clone o repositório em seu computador local. Escolha uma pasta de modelo em sample_templates. Você pode usar `wingtip` o `contoso`ou o.
2. Carregue todos os arquivos nas `css`pastas, e `images` no armazenamento de BLOBs, `fonts`conforme descrito nas seções anteriores. 
3. Em seguida, abra \*cada arquivo. html na raiz de ou `wingtip` `contoso` (o que você selecionou na primeira etapa) e substitua todas as instâncias de http://localhost"" pelas URLs do CSS, imagens e arquivos de fontes que você carregou na etapa 2.
4. Salve os \*arquivos. html e carregue-os no armazenamento de BLOBs.
5. Agora, modifique o arquivo de extensões conforme mencionado anteriormente em [Modificar o arquivo de extensões](#modify-the-extensions-file).
6. Se você vir fontes, imagens ou CSS ausentes, verifique suas referências na política de extensões e nos \*arquivos. html.

### <a name="content-defintion-ids"></a>IDs de definição de conteúdo

Na seção modificar sua política personalizada de inscrição ou entrada, você configurou a definição de conteúdo para `api.idpselections`. O conjunto completo de IDs de definição de conteúdo que são reconhecidos pela estrutura de experiência de identidade Azure AD B2C e suas descrições estão na tabela a seguir:

| ID de definição de conteúdo | Descrição | 
|-----------------------|-------------|
| *api.error* | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. |
| *api.idpselections* | **Página de seleção do provedor de identidade**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a entrada. Essas opções são provedores de identidade Enterprise, provedores de identidade social, como Facebook e Google +, ou contas locais. |
| *api.idpselections.signup* | **Seleção do provedor de identidade para inscrição**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Essas opções são provedores de identidade Enterprise, provedores de identidade social, como Facebook e Google +, ou contas locais. |
| *api.localaccountpasswordreset* | **Página esqueci a senha**. Esta página contém um formulário que o usuário deve concluir para iniciar uma redefinição de senha.  |
| *api.localaccountsignin* | **Página de entrada da conta local**. Esta página contém um formulário de entrada para entrar com uma conta local baseada em um endereço de email ou um nome de usuário. O formulário pode conter uma caixa de entrada de texto e uma caixa de entrada de senha. |
| *api.localaccountsignup* | **Página de inscrição da conta local**. Esta página contém um formulário de inscrição para se inscrever em uma conta local baseada em um endereço de email ou um nome de usuário. O formulário pode conter vários controles de entrada, como uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de opção, caixas suspensas de seleção única e caixas de seleção de várias seleções. |
| *api.phonefactor* | **Página de autenticação**multifator. Nessa página, os usuários podem verificar seus números de telefone (usando texto ou voz) durante a inscrição ou entrada. |
| *api.selfasserted* | **Página de inscrição de conta social**. Esta página contém um formulário de inscrição que os usuários devem concluir ao se inscreverem usando uma conta existente de um provedor de identidade social, como Facebook ou Google +. Esta página é semelhante à página de inscrição de conta social anterior, exceto para os campos de entrada de senha. |
| *api.selfasserted.profileupdate* | **Página de atualização de perfil**. Esta página contém um formulário que os usuários podem usar para atualizar seu perfil. Esta página é semelhante à página de inscrição de conta social, exceto para os campos de entrada de senha. |
| *api.signuporsignin* | **Página de inscrição ou entrada**unificada. Esta página manipula a inscrição e a entrada de usuários, que podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook ou Google +, ou contas locais.  |

## <a name="next-steps"></a>Passos Seguintes

Para obter informações adicionais sobre os elementos da interface do usuário que podem ser personalizados, consulte o [Guia de referência para personalização da interface do usuário para políticas internas](active-directory-b2c-reference-ui-customization.md).
