---
title: Tutorial para configurar Azure Ative Directory B2C com Arkose Labs
titleSuffix: Azure AD B2C
description: Tutorial para configurar O Azure Ative Directory B2C com a Arkose Labs para identificar utilizadores arriscados e fraudulentos
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c10a39b050fa66192f762ba642b4c8ac2e080250
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258147"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Tutorial: Configurar arkose Labs com Azure Ative Directory B2C

Neste tutorial de amostra, aprenda a integrar a autenticação do Azure Ative Directory (AD) B2C com [a Arkose Labs.](https://www.arkoselabs.com/) A Arkose Labs ajuda organizações contra ataques de bots, ataques de aquisição de contas e aberturas fraudulentas de contas.  

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma subscrição do Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- [Um inquilino Azure AD B2C](tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

- Uma conta [da Arkose Labs.](https://www.arkoselabs.com/book-a-demo/)

## <a name="scenario-description"></a>Descrição do cenário

A integração da Arkose Labs inclui os seguintes componentes:

- **Arkose Labs** - Um serviço de fraude e abuso para proteger contra bots e outros abusos automatizados.

- **Fluxo de utilizador de inscrição Azure AD B2C** - A experiência de inscrição que utilizará o serviço Arkose Labs. Utilizará os conectores html e JavaScript personalizados e API para integrar-se com o serviço Arkose Labs.

- **Funções Azure** - Ponto final da API hospedado por si que trabalha com a função de conectores API. Esta API é responsável por fazer a validação do lado do servidor do token da sessão arkose Labs.

O diagrama seguinte descreve como a Arkose Labs se integra com a Azure AD B2C.

![Imagem mostra diagrama de arquitetura Arkose Labs](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Passo  | Descrição |
|---|---|
|1     | Um utilizador inscreve-se e cria uma conta. Quando o utilizador seleciona submeter, aparece um desafio de aplicação da Arkose Labs.         |
|2     |  Após o utilizador completar o desafio, a Azure AD B2C envia o estatuto à Arkose Labs para gerar um token. |
|3     |  Arkose Labs gera um símbolo e envia-o de volta para Azure AD B2C.   |
|4     |  Azure AD B2C chama uma API web intermédia para passar o formulário de inscrição.      |
|5     |  A API da web intermediária envia o formulário de inscrição para o Arkose Lab para verificação de fichas.    |
|6     | Arkose Lab processa e envia os resultados de verificação de volta para a API da web intermediária.|
|7     | A API da web intermédia envia o resultado do sucesso ou falha do desafio para Azure AD B2C. |
|8     | Se o desafio for concluído com sucesso, é submetido um formulário de inscrição ao Azure AD B2C e o Azure AD B2C completa a autenticação.|

## <a name="onboard-with-arkose-labs"></a>A bordo com arkose Labs

1. Contacte [arkose](https://www.arkoselabs.com/book-a-demo/) e crie uma conta.

2. Uma vez criada a conta, navegue para https://dashboard.arkoselabs.com/login  

3. Dentro do painel de instrumentos, navegue nas definições do local para encontrar a sua chave pública e chave privada. Estas informações serão necessárias mais tarde para configurar a Azure AD B2C. Os valores das chaves públicas e privadas são referidos como `ARKOSE_PUBLIC_KEY` e no código de `ARKOSE_PRIVATE_KEY` [amostra](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose).

## <a name="integrate-with-azure-ad-b2c"></a>Integrar com o Azure AD B2C

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>Parte 1 - Criar um atributo personalizado ArkoseSessionToken

Para criar um atributo personalizado, siga estes passos:  

1. Ir ao **portal Azure**  >  **Azure AD B2C**

2. Selecione **atributos do Utilizador**

3. Selecione **Adicionar**

4. **Insira ArkoseSessionToken** como o nome do atributo

5. Selecione **Criar**

Saiba mais sobre [atributos personalizados.](./user-flow-custom-attributes.md?pivots=b2c-user-flow)

### <a name="part-2---create-a-user-flow"></a>Parte 2 - Criar um fluxo de utilizador

O fluxo do utilizador pode ser para **inscrição** e **inscrição** ou apenas **inscrição**. O fluxo de utilizador da Arkose Labs só será mostrado durante a inscrição.

1. Consulte as [instruções](./tutorial-create-user-flows.md) para criar um fluxo de utilizador. Se utilizar um fluxo de utilizador existente, deve ser do tipo de versão **recomendada (pré-visualização da próxima geração).**

2. Nas definições de fluxo do utilizador, aceda aos **atributos do Utilizador** e selecione a **reivindicação ArkoseSessionToken.**

![A imagem mostra como selecionar atributos personalizados](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>Parte 3 - Configurar layouts personalizados HTML, JavaScript e páginas

Aceda ao [script HTML](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)fornecido. O ficheiro contém um modelo HTML com tags JavaScript `<script>` que farão três coisas:

1. Carregue o script Arkose Labs, que torna o widget Arkose Labs e faz a validação da Arkose Labs do lado do cliente.

2. Ocultar o `extension_ArkoseSessionToken` elemento de entrada e a etiqueta, correspondente ao atributo `ArkoseSessionToken` personalizado, da UI mostrada ao utilizador.

3. Quando um utilizador completa o desafio Arkose Labs, a Arkose Labs verifica a resposta do utilizador e gera um símbolo. A chamada `arkoseCallback` no JavaScript personalizado define o valor do `extension_ArkoseSessionToken` valor simbólico gerado. Este valor será submetido ao ponto final da API, tal como descrito na secção seguinte.

    Veja [este artigo](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) para saber sobre a validação do lado do cliente da Arkose Labs.

Siga os passos mencionados para utilizar o HTML e o JavaScript personalizados para o fluxo do utilizador.

1. ModifiqueselfAsserted.htmficheiro [ L](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) de modo `<ARKOSE_PUBLIC_KEY>` a corresponder ao valor que gerou para a validação do lado do cliente, e usado para carregar o script arkose Labs para a sua conta.

2. Hospedar a página HTML num ponto final de partilha de recursos de origem cruzada (CORS) ativado na web. [Crie uma conta de armazenamento de bolhas Azure](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e [configuure CORS](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Se tiver o seu próprio HTML personalizado, copie e cole os `<script>` elementos na sua página HTML.

3. Siga estes passos para configurar os layouts da página

   a. Do portal Azure, vá ao **Azure AD B2C**

   b. Navegue para os **fluxos do Utilizador** e selecione o fluxo do seu utilizador

   c. Selecione **layouts de página**

   d. Selecione **O layout da página** local

   e. Toggle **Use conteúdo de página personalizada** para **SIM**

   f. Cole o URI onde o seu HTML personalizado vive no **Use conteúdo de página personalizada**

   exemplo, Se estiver a utilizar fornecedores de identidade social, repita **o passo e** f para o layout da página de  **inscrição da conta social.**

   ![imagem mostrando layouts de página](media/partner-arkose-labs/page-layouts.png)

4. A partir do fluxo do seu utilizador, vá a **Propriedades** e selecione **Ative JavaScript** a impor o layout da página (pré-visualização). Veja este [artigo](./javascript-and-page-layout.md?pivots=b2c-user-flow) para saber mais.

### <a name="part-4---create-and-deploy-your-api"></a>Parte 4 - Criar e implementar a sua API

Instale a [extensão de Funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual.

>[!Note]
>Os passos mencionados nesta secção pressupõe que está a utilizar o Código do Estúdio Visual para implementar a Função Azure. Também pode utilizar o portal Azure, o terminal ou o comando, ou qualquer outro editor de código para implementar.

#### <a name="run-the-api-locally"></a>Executar a API localmente

1. Navegue para a extensão Azure no código Visual Studio na barra de navegação esquerda. Selecione a pasta **de projeto local** representando a sua Função Azure local.

2. Prima **F5** ou utilize o menu **Debug**  >  **Start Debugging** para lançar o depurador e ligar ao anfitrião Azure Functions. Este comando utiliza automaticamente a configuração de depuração única criada pelo Azure Function.

3. A extensão Azure Function gerará automaticamente alguns ficheiros para o desenvolvimento local, instalará dependências e instalará as ferramentas 'Núcleo de Função' se ainda não estiver presente. Estas ferramentas ajudam com a experiência de depuragem.

4. A saída da ferramenta Núcleo de Função aparece no painel **do Terminal de Código** do Estúdio Visual. Uma vez iniciado o anfitrião, **o Alt+clique** no URL local mostrado na saída para abrir o navegador e executar a função. No explorador de Funções Azure, clique à direita na função para ver o url da função alojada localmente.

Para recolocar a instância local durante os testes, repita os passos 1 a 4.

#### <a name="add-environment-variables"></a>Adicionar variáveis ambientais

Esta amostra protege o ponto final da API web utilizando [a autenticação básica HTTP](https://tools.ietf.org/html/rfc7617).

O nome de utilizador e a palavra-passe são armazenados como variáveis ambientais e não como parte do repositório. Consulte [local.settings.jsno](../azure-functions/functions-run-local.md?tabs=macos%2ccsharp%2cbash#local-settings-file) arquivo para mais informações.

1. Crie um local.settings.jsno ficheiro na sua pasta raiz

2. Copiar e colar o código abaixo no ficheiro:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
Os **valores BASIC_AUTH_USERNAME** e **BASIC_AUTH_PASSWORD** serão as credenciais utilizadas para autenticar a chamada da API para a sua Função Azure. Escolha os valores desejados.

O `<ARKOSE_PRIVATE_KEY>` segredo do lado do servidor que gerou no serviço Arkose Labs. É usado para chamar a [API de validação do lado do servidor Arkose Labs](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) para validar o valor do `ArkoseSessionToken` gerado pela parte frontal.

O `<B2C_EXTENSIONS_APP_ID>` iD da aplicação utilizada pelo Azure AD B2C para armazenar atributos personalizados no diretório. Pode encontrar este ID de aplicação navegando para os registos da App, procurando a aplicação de extensões b2c e copiando o ID da Aplicação (cliente) a partir do painel **de visão** geral. Remova os `-` caracteres.

![Imagem mostra pesquisa por id de aplicativo](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Implementar a aplicação para a web

1. Siga os passos mencionados [neste](/azure/javascript/tutorial-vscode-serverless-node-04) guia para implementar a sua Função Azure na nuvem. Copie o URL web de ponto final da sua Função Azure.

2. Uma vez implementado, selecione a opção **definições de upload.** Irá enviar as variáveis ambientais para as definições de [Aplicação](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure) do serviço App. Estas configurações de aplicação também podem ser configuradas ou [geridas através do portal Azure.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)

Consulte [este artigo](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files) para saber mais sobre o desenvolvimento do Código do Estúdio Visual para Funções Azure.

#### <a name="configure-and-enable-the-api-connector"></a>Configurar e ativar o conector API

[Crie um conector API](./add-api-connector.md) e ative-o para o fluxo do utilizador. A configuração do conector API deve ser:

![Imagem mostra como configurar conector api](media/partner-arkose-labs/configure-api-connector.png)

- **URL de ponto final** - é o URL de função que copiou anteriormente enquanto implementou a Função Azure.

- **Username e Password** - são o nome de utilizador e palavra-passe que definiu como variáveis ambientais anteriormente.

Para ativar o conector API, nas definições do **conector API** para o fluxo do utilizador, selecione o conector API a ser invocado no Antes de criar o passo **do utilizador.** Isto invocará a API quando um utilizador selecionar **Criar** no fluxo de inscrição. A API fará uma validação do valor do lado do `ArkoseSessionToken` servidor, que foi definida pela chamada do widget Arkose `arkoseCallback` .

![A imagem mostra o conector api ativador](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e em Políticas selecione **fluxos de utilizador**.

2. Selecione o seu Fluxo de Utilizador previamente criado.

3. Selecione **Executar o fluxo do utilizador** e selecione as definições:

   a. Aplicação: selecione a aplicação registada (a amostra é JWT)

   b. URL de resposta: selecione o URL de redirecionamento

   c. Selecione **Executar o fluxo do utilizador**.

4. Vá através do fluxo de inscrição e crie uma conta

5. Terminar sessão

6. Vá através do fluxo de entrada  

7. Um puzzle arkose Labs aparecerá depois de selecionar **continuar**.

## <a name="additional-resources"></a>Recursos adicionais

- [Códigos de amostra](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) para fluxo de utilizador de inscrição Azure AD B2C

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)