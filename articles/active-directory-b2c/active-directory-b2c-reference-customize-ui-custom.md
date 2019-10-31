---
title: Personalizar a IU de um percurso do usuário com políticas personalizadas | Microsoft Docs
description: Saiba mais sobre Azure Active Directory B2C políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7e4714de9868dbd540e2e662b22a22da6df6514b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147530"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Personalizar a IU de um percurso do usuário com políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Este artigo é uma descrição avançada de como funciona a personalização da interface do usuário e como habilitar o com Azure AD B2C políticas personalizadas, usando a estrutura de experiência de identidade.


Uma experiência de usuário tranqüila é a chave para qualquer solução de empresa para consumidor. Uma experiência de usuário tranqüila é uma experiência, seja no dispositivo ou navegador, onde a jornada de um usuário através do serviço é indistinguíveis do serviço de atendimento ao cliente que estão usando.

## <a name="understand-the-cors-way-for-ui-customization"></a>Entender a maneira CORS para personalização da interface do usuário

Azure AD B2C permite que você personalize a aparência da experiência do usuário (UX) nas várias páginas servidas e exibidas por Azure AD B2C usando suas políticas personalizadas.

Para essa finalidade, Azure AD B2C executa o código no navegador do seu consumidor e usa a abordagem moderna e padrão do [compartilhamento de recursos entre origens (CORS)](https://www.w3.org/TR/cors/) para carregar o conteúdo personalizado de uma URL específica que você especifica em uma política personalizada para apontar para o HTML5/CSS modelo. O CORS é um mecanismo que permite que recursos restritos, como fontes, em uma página da Web sejam solicitados de outro domínio fora do domínio do qual o recurso foi originado.

Comparado à antiga maneira tradicional, em que as páginas de modelo são de propriedade da solução em que você forneceu texto limitado e imagens, em que o controle limitado de layout e sensação foi oferecido levando a mais do que dificuldades para obter uma experiência tranqüila, a maneira CORS dá suporte a HTML5 e CSS e permite que você:

- Hospede o conteúdo e a solução injeta seus controles usando o script do lado do cliente.
- Tenha controle total sobre cada pixel de layout e sensação.

Você pode fornecer quantas páginas de conteúdo desejar ao criar arquivos HTML5/CSS conforme apropriado.

> [!NOTE]
> Por motivos de segurança, o uso do JavaScript está bloqueado no momento para personalização. 

Em cada um dos modelos HTML5/CSS, você fornece um elemento *Anchor* , que corresponde ao elemento `<div id="api">` necessário no HTML ou na página de conteúdo como ilustrar daqui em diante. Azure AD B2C requer que todas as páginas de conteúdo tenham essa div específica.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

O conteúdo relacionado ao Azure AD B2C da página é injetado nessa div, enquanto o restante da página é seu para controlar. O Azure AD B2C código JavaScript efetua pull em seu conteúdo e injeta o HTML nesse elemento div específico. Azure AD B2C injeta os seguintes controles conforme apropriado: controle do seletor de conta, controles de logon, controles multifator (baseados no telefone) e controles de coleção de atributos. Azure AD B2C garante que todos os controles sejam compatíveis com HTML5 e acessíveis, todos os controles podem ser totalmente estilizados e que uma versão de controle não seja regressiva.

O conteúdo mesclado é eventualmente exibido como o documento dinâmico para seu consumidor.

Para garantir que tudo funcione conforme o esperado, você deve:

- Verifique se o conteúdo está em conformidade com o HTML5 e está acessível
- Verifique se o servidor de conteúdo está habilitado para CORS.
- Fornecer conteúdo por HTTPS.
- Use URLS absolutas, como `https://yourdomain/content` para todos os links e conteúdo CSS.

> [!TIP]
> Para verificar se o site no qual você está hospedando o conteúdo tem CORS habilitado e testar solicitações CORS, você pode usar o site https://test-cors.org/. Graças a esse site, você pode enviar a solicitação de CORS para um servidor remoto (para testar se há suporte para CORS) ou enviar a solicitação de CORS para um servidor de teste (para explorar determinados recursos do CORS).

> [!TIP]
> O site https://enable-cors.org/ também constitui mais do que recursos úteis no CORS.

Graças a essa abordagem baseada em CORS, os usuários finais têm experiências consistentes entre seu aplicativo e as páginas servidas por Azure AD B2C.

## <a name="create-a-storage-account"></a>Create a storage account

Como pré-requisito, você precisa criar uma conta de armazenamento. Você precisa de uma assinatura do Azure para criar uma conta de armazenamento de BLOBs do Azure. Você pode se inscrever em uma avaliação gratuita no [site do Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Abra uma sessão de navegação e navegue até a [portal do Azure](https://portal.azure.com).
2. Entre com suas credenciais administrativas.
3. Clique em **criar um recurso** > **armazenamento** > **conta de armazenamento**.  Um painel **criar conta de armazenamento** é aberto.
4. Em **nome**, forneça um nome para a conta de armazenamento, por exemplo, *, contoso369b2c*. Esse valor é mencionado posteriormente como *storageAccountName*.
5. Escolha as seleções apropriadas para o tipo de preço, o grupo de recursos e a assinatura. Certifique-se de que você tenha a opção **fixar na quadro inicial** marcada. Clique em **Criar**.
6. Volte para o quadro inicial e clique na conta de armazenamento que você criou.
7. Na seção **Serviços** , clique em **BLOBs**. Um **painel de serviço blob** é aberto.
8. Clique em **+ contêiner**.
9. Em **nome**, forneça um nome para o contêiner, por exemplo, *B2C*. Esse valor é posteriormente referido como *ContainerName*.
9. Selecione **blob** como o **tipo de acesso**. Clique em **Criar**.
10. O contêiner que você criou aparece na lista no **painel serviço blob**.
11. Feche o painel **BLOBs** .
12. No **painel da conta de armazenamento**, clique no ícone de **chave** . Um **painel chaves de acesso** é aberto.  
13. Anote o valor de **key1**. Esse valor é referenciado posteriormente como *key1*.

## <a name="downloading-the-helper-tool"></a>Baixando a ferramenta auxiliar

1.  Baixe a ferramenta auxiliar do [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Salve o arquivo *B2C-AzureBlobStorage-Client-Master. zip* em seu computador local.
3.  Extraia o conteúdo do arquivo B2C-AzureBlobStorage-Client-master. zip no disco local, por exemplo, na pasta **UI-Customization-Pack** , que cria uma pasta *B2C-AzureBlobStorage-Client-Master* abaixo.
4.  Abra essa pasta e extraia o conteúdo do arquivo morto *B2CAzureStorageClient. zip* dentro dele.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Carregar os arquivos de exemplo da interface do usuário-personalização de pacote

1.  Usando o Windows Explorer, navegue até a pasta *B2C-AzureBlobStorage-Client-mestre* localizada na pasta *UI-Customization Pack* criada na seção anterior.
2.  Execute o arquivo *B2CAzureStorageClient. exe* . Esse programa carrega todos os arquivos no diretório que você especifica para sua conta de armazenamento e habilita o acesso CORS para esses arquivos.
3.  Quando solicitado, especifique: a.  O nome da sua conta de armazenamento, *storageAccountName*, por exemplo, *, contoso369b2c*.
    b.  A chave de acesso primária do seu armazenamento de BLOBs do Azure, *key1*, por exemplo *, contoso369b2c*.
    c.  O nome do seu contêiner de armazenamento de blob de armazenamento, *ContainerName*, por exemplo *B2C*.
    d.  O caminho dos arquivos de exemplo do *pacote inicial* , por exemplo *.. \B2CTemplates\wingtiptoys*.

Se você seguiu as etapas anteriores, os arquivos HTML5 e CSS do *UI-Customization Pack* para a empresa fictícia **wingtiptoys** agora estão apontando para sua conta de armazenamento.  Você pode verificar se o conteúdo foi carregado corretamente abrindo o painel de contêiner relacionado na portal do Azure. Como alternativa, você pode verificar se o conteúdo foi carregado corretamente acessando a página de um navegador. Para obter mais informações, consulte [Azure Active Directory B2C: uma ferramenta auxiliar usada para demonstrar o recurso de personalização da interface do usuário da página](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Verifique se a conta de armazenamento tem CORS habilitado

O CORS (compartilhamento de recursos entre origens) deve estar habilitado no ponto de extremidade para Azure AD B2C carregar o conteúdo. Isso ocorre porque o conteúdo está hospedado em um domínio diferente do domínio no qual o Azure AD B2C servirá a página.

Para verificar se o armazenamento no qual você está hospedando o conteúdo tem CORS habilitado, prossiga com as seguintes etapas:

1. Abra uma sessão de navegação e navegue até a página *Unified. html* usando a URL completa de seu local em sua conta de armazenamento, `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Por exemplo, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navegue até https://test-cors.org. Este site permite que você verifique se a página que você está usando tem CORS habilitado.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. Em **URL remota**, insira a URL completa para o conteúdo unificado. html e clique em **Enviar solicitação**.
4. Verifique se a saída na seção **resultados** contém o *status XHR: 200*, que indica que o CORS está habilitado.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   A conta de armazenamento agora deve conter um contêiner de BLOBs chamado *B2C* na ilustração que contém os seguintes modelos de wingtiptoys do *pacote inicial*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

A tabela a seguir descreve a finalidade das páginas anteriores do HTML5.

| Modelo HTML5 | Descrição |
|----------------|-------------|
| *PhoneFactor. html* | Essa página pode ser usada como um modelo para uma página de autenticação multifator. |
| *ResetPassword. html* | Esta página pode ser usada como um modelo para uma página esquecida de senha. |
| *selfasserted. html* | Esta página pode ser usada como um modelo para uma página de inscrição de conta social, uma página de inscrição de conta local ou uma página de entrada de conta local. |
| *Unified. html* | Esta página pode ser usada como um modelo para uma página de inscrição ou entrada unificada. |
| *updateProfile. html* | Esta página pode ser usada como um modelo para uma página de atualização de perfil. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Adicionar um link aos modelos HTML5/CSS para o percurso do usuário

Você pode adicionar um link para seus modelos HTML5/CSS para o percurso do usuário editando uma política personalizada diretamente.

Os modelos HTML5/CSS personalizados a serem usados no percurso do usuário devem ser especificados em uma lista de definições de conteúdo que podem ser usadas nesses percursos do usuário. Para essa finalidade, um elemento XML opcional de *\<ContentDefinitions >* deve ser declarado na seção *\<> do BUILDINGBLOCKS* do arquivo XML da política personalizada.

A tabela a seguir descreve o conjunto de IDs de definição de conteúdo reconhecido pelo mecanismo de experiência de identidade Azure AD B2C e o tipo de páginas relacionadas a eles.

| ID de definição de conteúdo | Descrição |
|-----------------------|-------------|
| *API. Error* | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. |
| *API. idpselections* | **Página de seleção do provedor de identidade**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a entrada. Esses provedores são provedores de identidade corporativa, provedores de identidade social, como Facebook e Google +, ou contas locais (com base no endereço de email ou nome de usuário). |
| *API. idpselections. signup* | **Seleção do provedor de identidade para inscrição**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Esses provedores são provedores de identidade corporativa, provedores de identidade social, como Facebook e Google +, ou contas locais (com base no endereço de email ou nome de usuário). |
| *API. localaccountpasswordreset* | **Página esqueci a senha**. Esta página contém um formulário que o usuário precisa preencher para iniciar a redefinição de senha.  |
| *API. localaccountsignin* | **Página de entrada da conta local**. Esta página contém um formulário de entrada que o usuário precisa preencher ao entrar com uma conta local baseada em um endereço de email ou um nome de usuário. O formulário pode conter uma caixa de entrada de texto e uma caixa de entrada de senha. |
| *API. localaccountsignup* | **Página de inscrição da conta local**. Esta página contém um formulário de inscrição que o usuário precisa preencher ao se inscrever em uma conta local baseada em um endereço de email ou um nome de usuário. O formulário pode conter diferentes controles de entrada, como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas suspensas de seleção única e caixas de seleção de várias seleções. |
| *API. PhoneFactor* | **Página de autenticação multifator**. Nessa página, os usuários podem verificar seus números de telefone (usando texto ou voz) durante a inscrição ou entrada. |
| *API. selfasserted* | **Página de inscrição de conta social**. Esta página contém um formulário de inscrição que o usuário precisa preencher ao se inscrever usando uma conta existente de um provedor de identidade social, como o Facebook ou o Google +. Esta página é semelhante à página de inscrição de conta social anterior, com a exceção dos campos de entrada de senha. |
| *API. selfasserted. profileUpdate* | **Página de atualização de perfil**. Esta página contém um formulário que o usuário pode usar para atualizar seu perfil. Esta página é semelhante à página de inscrição de conta social anterior, com a exceção dos campos de entrada de senha. |
| *API. signuporsignin* | **Página de inscrição ou entrada unificada**.  Esta página lida com a inscrição & entrada de usuários, que podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook ou Google +, ou contas locais.

## <a name="next-steps"></a>Passos seguintes
[Referência: entenda como as políticas personalizadas funcionam com a estrutura de experiência de identidade no B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
