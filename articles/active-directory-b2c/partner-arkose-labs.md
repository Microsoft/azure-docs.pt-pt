---
title: Arkose Labs com Azure Ative Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com a Arkose Labs para ajudar a proteger contra ataques de bots, ataques de aquisição de contas e aberturas fraudulentas de contas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf0437b985865248393e9f68da264fcae5af7f12
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108557"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Tutorial para configurar arkose Labs com Azure Ative Directory B2C

Neste tutorial, aprenda a integrar a autenticação Azure AD B2C com a Arkose Labs. A Arkose Labs ajuda organizações contra ataques de bots, ataques de aquisição de contas e aberturas fraudulentas de contas.  

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* [Um inquilino Azure AD B2C](tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

## <a name="scenario-description"></a>Descrição do cenário

O diagrama seguinte descreve como a Arkose Labs se integra com a Azure AD B2C.

![Diagrama de arquitetura Arkose Labs](media/partner-arkose-labs/arkose-architecture-diagram.png)

| Passo  | Descrição |
|---|---|
|1     | Um utilizador inscreve-se com uma conta previamente criada. Quando o utilizador seleciona submeter, aparece um desafio arkose Labs Enforcement. Após o utilizador completar o desafio, o estado é enviado para a Arkose Labs para gerar um token.        |
|2     |  Arkose Labs envia o símbolo de volta para Azure AD B2C.       |
|3     |  Antes do formulário de inscrição ser submetido, o token é enviado para arkose Labs para verificação.       |
|4     |  Arkose envia de volta um resultado de sucesso ou fracasso do desafio.       |
|5     |  Se o desafio for concluído com sucesso, um formulário de inscrição é submetido ao Azure AD B2C, e a Azure AD B2C completa a autenticação.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>A bordo com arkose Labs

1. Comece por contactar a [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) e criar uma conta.

2. Assim que a sua conta for criada, navegue para https://dashboard.arkoselabs.com/login .

3. Dentro do painel de instrumentos, navegue nas definições do local para encontrar a sua chave pública e chave privada. Estas informações serão necessárias mais tarde para configurar a Azure AD B2C.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar com o Azure AD B2C

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>Parte 1 - Criar armazenamento de bolhas para armazenar o HTML personalizado

Para criar uma conta de armazenamento, siga estes passos:  

1. Inscreva-se no portal Azure.

2. Certifique-se de que utiliza o diretório que contém a sua assinatura Azure. Selecione o filtro **Diretório + Subscrição** no menu superior e escolha o diretório que contém a sua subscrição. Este diretório é diferente daquele que contém o seu inquilino Azure B2C.

3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione  **contas de Armazenamento**.

4. Selecione  **Adicionar**.

5. No  **grupo de Recursos**, selecione Criar  **novo,** insira um nome para o novo grupo de recursos e, em seguida, selecione **OK**.

6. Introduza um nome para a conta de armazenamento. O nome que escolher tem de ser exclusivo no Azure, deve ter entre 3 e 24 carateres de comprimento e apenas pode conter números e letras minúsculas.

7. Selecione a localização da conta de armazenamento ou aceite a localização predefinitiva.

8. Aceite todos os outros valores predefinidos,  **selecione Rever & criar.**  >  

9. Depois de criar a conta de armazenamento, selecione  **Ir para o recurso**.

#### <a name="create-a-container"></a>Criar um contentor

1. Na página geral da conta de armazenamento, selecione  **Blobs**.

2. Selecione   **O Recipiente**, introduza um nome para o recipiente, escolha   **Blob** (acesso de leitura anónima apenas para bolhas) e, em seguida, selecione **OK**.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>Permitir a partilha de recursos de origem cruzada (CORS)

O código AD B2C do Azure em um navegador usa uma abordagem moderna e padrão para carregar conteúdo personalizado a partir de um URL que você especifica em um fluxo de utilizador. O CORS permite que os recursos restritos numa página web sejam solicitados a partir de outros domínios.

1. No menu, selecione  **CORS**.

2. Para  **origens permitidas,** insira  `https://your-tenant-name.b2clogin.com` . Substitua o seu nome de inquilino pelo nome do seu inquilino Azure AD B2C. Por exemplo,  `https://fabrikam.b2clogin.com`. Use todas as letras minúsculas ao inserir o nome do seu inquilino.

3. Para  **Métodos Permitidos**, selecione  **GET,** **PUT** e  **OPÇÕES**.

4. Para **cabeçalhos permitidos,** introduza um asterisco (*).

5. Para **cabeçalhos expostos,** introduza um asterisco (*).

6. Para **a idade máxima,** insira 200.

   ![Arkose Labs inscrever-se e inscrever-se](media/partner-arkose-labs/signup-signin-arkose.png)

7. Selecione **Guardar**.

### <a name="part-2--set-up-a-back-end-server"></a>Parte 2 - Criar um servidor de back-end

Baixe Git Bash e siga os passos abaixo:

1. Siga as instruções para [criar uma aplicação web,](../app-service/quickstart-php.md)até à mensagem "Parabéns!Implementou a sua primeira aplicação PHP para o Serviço de Aplicações" exibe.

2. Abra a pasta local e mude o nome do ficheiro **.php índice** **para verificar.php**.

3. Abra o ficheiro de verificação de ficheiros recentemente renomeado.php ficheiro e:

   a. Substitua o conteúdo pelo conteúdo do ficheiro check-token.php encontrado no [repositório GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

   b. Substitua <private_key> na linha 3 com a sua chave privada obtida no painel arkose Labs.

4. Na janela do terminal local, cometa as suas alterações em Git e, em seguida, empurre as alterações de código para Azure digitando o seguinte em Git Bash:

   ``git commit -am "updated output"``

   ``git push azure master``  

### <a name="part-3---final-setup"></a>Parte 3 - Configuração final

#### <a name="store-the-custom-html"></a>Armazenar o HTML personalizado

1. Abra o ficheiro index.html armazenado no [repositório GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

2. Substitua todas as instâncias `<tenantname>` do seu nome de inquilino b2C (por outras palavras, `<tenantname>.b2clogin.com` ). Deve haver quatro casos.

3. Substitua o `<appname>` nome da aplicação que criou na Parte 2, passo 1.

   ![Screenshot mostrando Arkose Labs Azure CLI](media/partner-arkose-labs/arkose-azure-cli.png)

4. Substitua a `<public_key>` linha 64 pela chave pública que obteve do painel arkose Labs.

5. Faça o upload do ficheiro index.html para o armazenamento de bolhas acima criado.

6. Vá ao **upload do** contentor  >    >  **de armazenamento.**

#### <a name="set-up-azure-ad-b2c"></a>Configurar Azure AD B2C

> [!NOTE]
> Se ainda não tiver um, [crie um inquilino Azure AD B2C](tutorial-create-tenant.md) que esteja ligado à sua assinatura Azure.

1. Crie um fluxo de utilizador com base nas informações [aqui.](tutorial-create-user-flows.md) Parar quando chegar à secção **Teste o fluxo do utilizador**.

2. Ativar o JavaScript no [fluxo do utilizador.](javascript-and-page-layout.md)

3. Na mesma página de fluxo do utilizador, ative o URL da página personalizada: Vá ao layout da página **de fluxo do utilizador** use conteúdo de página  >    >  **personalizada**  =  **sim**  >  **insira URL de página personalizada**.
Este URL de página personalizada é obtido a partir da localização do ficheiro index.html dentro do armazenamento do blob  

   ![Screenshot mostrando o url de armazenamento arkose Labs](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e, em **Políticas,** selecione **fluxos de utilizador**.

2. Selecione o seu Fluxo de Utilizador previamente criado.

3. Selecione **Executar o fluxo do utilizador** e selecione as definições:

   a. **Aplicação** - Selecione a aplicação registada (a amostra é JWT).

   b. **URL de resposta** - Selecione o URL de redirecionamento.

   c. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta.

5. Termine a sessão.

6. Ando pelo fluxo de entrada.

7. Um puzzle arkose Labs aparecerá depois de selecionar **continuar**.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](custom-policy-get-started.md?tabs=applications)