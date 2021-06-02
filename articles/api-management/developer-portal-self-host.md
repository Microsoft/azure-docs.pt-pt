---
title: Auto-anfitrião do portal de desenvolvedores
titleSuffix: Azure API Management
description: Saiba como se auto-hospedar o portal de desenvolvimento da API Management.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e63a329a10699102802af6d544ab55aa19513f17
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741889"
---
# <a name="self-host-the-api-management-developer-portal"></a>Auto-anfitrião do portal de desenvolvimento da API Management

Este tutorial descreve como se auto-hospedar o [portal de desenvolvimento da API Management](api-management-howto-developer-portal.md). O self-hosting dá-lhe flexibilidade para estender o portal do desenvolvedor com lógica personalizada e widgets que personalizam dinamicamente páginas no tempo de execução. Pode hospedar vários portais para a sua instância de Gestão de API, com diferentes funcionalidades. Quando se auto-hospeda um portal, torna-se o seu criador e é responsável pelas suas atualizações. 

Os passos seguintes mostram como configurar o seu ambiente de desenvolvimento local, realizar alterações no portal do desenvolvedor e publicá-los e implantá-los numa conta de armazenamento Azure.

Se já fez o upload ou modificado ficheiros de mídia no portal gerido, consulte [Move from managed to self-hosted](#move-from-managed-to-self-hosted-developer-portal), mais tarde neste artigo.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para criar um ambiente de desenvolvimento local, você precisa ter:

- Uma instância de serviço de gestão da API. Se não tiver um, consulte [Quickstart - Crie uma instância de Gestão API Azure](get-started-create-service-instance.md).
- Uma conta de armazenamento Azure com [a funcionalidade de websites estáticos](../storage/blobs/storage-blob-static-website.md) ativada. Ver [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).
- Git na sua máquina. Instale-o seguindo [este tutorial de Git.](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- Node.js (versão LTS, `v10.15.0` ou mais tarde) e npm na sua máquina. Consulte [o Download e a instalação Node.js e npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
- CLI do Azure. Siga [os passos de instalação do Azure CLI](/cli/azure/install-azure-cli-windows).

## <a name="step-1-set-up-local-environment"></a>Passo 1: Configurar o ambiente local

Para configurar o seu ambiente local, terá de clonar o repositório, mudar para o mais recente lançamento do portal de desenvolvimento e instalar pacotes npm.

1. Clone o repo [api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) do GitHub:

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Vá à sua cópia local do repo:

    ```console
    cd api-management-developer-portal
    ```

1. Confira a última versão do portal.

    Antes de executar o seguinte código, verifique a etiqueta de desbloqueio atual na [secção Versões do repositório](https://github.com/Azure/api-management-developer-portal/releases) e substitua `<current-release-tag>` o valor pela etiqueta de lançamento mais recente.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Instale quaisquer pacotes npm disponíveis:

    ```console
    npm install
    ```

> [!TIP]
> Utilize sempre a [versão mais recente](https://github.com/Azure/api-management-developer-portal/releases) do portal e mantenha o seu portal forcado atualizado. Os Engenheiros de Software usam o `master` ramo deste repositório para fins de desenvolvimento diário. Tem versões instáveis do software.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>Passo 2: Configurar ficheiros JSON, website estático e configurações CORS

O portal de desenvolvedores requer a API Management REST API para gerir o conteúdo.

### <a name="configdesignjson-file"></a>config.design.json arquivo

Vá à `src` pasta e abra o `config.design.json` ficheiro.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Configure o ficheiro:

1. No `managementApiUrl` valor, `<service-name>` substitua-o pelo nome da sua instância de Gestão API. Se configurar um [domínio personalizado,](configure-custom-domain.md)use-o (por exemplo, `https://management.contoso.com` ).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [Crie manualmente um token SAS para](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) permitir o acesso direto da API rest à sua instância de Gestão de API.

1. Copie o símbolo gerado e cole-o como `managementApiAccessToken` o valor.

1. No `backendUrl` valor, `<service-name>` substitua-o pelo nome da sua instância de Gestão API. Se configurar um [domínio personalizado,](configure-custom-domain.md)use-o (por exemplo, `https://portal.contoso.com` ).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Se quiser ativar o CAPTCHA no seu portal de desenvolvedores, consulte [Enable CAPTCHA](#enable-captcha).

### <a name="configpublishjson-file"></a>config.publish.jsno arquivo

Vá à `src` pasta e abra o `config.publish.json` ficheiro.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Configure o ficheiro:

1. Copiar e colar os `managementApiUrl` valores e `managementApiAccessToken` valores do ficheiro de configuração anterior.

1. Se quiser ativar o CAPTCHA no seu portal de desenvolvedores, consulte [Enable CAPTCHA](#enable-captcha).

### <a name="configruntimejson-file"></a>config.runtime.jsno arquivo

Vá à `src` pasta e abra o `config.runtime.json` ficheiro.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Configure o ficheiro:

1. Copiar e colar o `managementApiUrl` valor do ficheiro de configuração anterior.

1. No `backendUrl` valor, `<service-name>` substitua-o pelo nome da sua instância de Gestão API. Se configurar um [domínio personalizado,](configure-custom-domain.md)use-o (por exemplo). `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>Configure o site estático

Configure a funcionalidade **do website Estático** na sua conta de armazenamento, fornecendo rotas para as páginas de índice e erro:

1. Vá à sua conta de armazenamento no portal Azure e selecione **site estático** a partir do menu à esquerda.

1. Na página **do site Estática,** selecione **Ativado**.

1. No campo **de nome do documento Índice,** introduzaindex.htm *l*.

1. No campo do percurso do **documento Error,** introduza *404/index.html*.

1. Selecione **Guardar**.

### <a name="configure-the-cors-settings"></a>Configure as definições CORS

Configure as definições de Partilha de Recursos de Origem Cruzada (CORS):

1. Vá à sua conta de armazenamento no portal Azure e selecione **CORS** do menu à esquerda.

1. No separador **de serviço Blob,** configuure as seguintes regras:

    | Regra | Valor |
    | ---- | ----- |
    | Origens permitidas | * |
    | Métodos permitidos | Selecione todos os verbos HTTP. |
    | Cabeçalhos permitidos | * |
    | Cabeçalhos expostos | * |
    | Idade máxima | 0 |

1. Selecione **Guardar**.

## <a name="step-3-run-the-portal"></a>Passo 3: Executar o portal

Agora pode construir e executar uma instância de portal local no modo de desenvolvimento. No modo de desenvolvimento, todas as otimizações são desligadas e os mapas de origem são ligados.

Execute o seguinte comando:

```console
npm start
```

Após um curto período de tempo, o navegador predefinido abre automaticamente com a instância do portal do desenvolvedor local. O endereço predefinido é `http://localhost:8080` , mas a porta pode mudar se já estiver `8080` ocupada. Quaisquer alterações à base de código do projeto desencadearão uma reconstrução e refrescará a janela do seu navegador.

## <a name="step-4-edit-through-the-visual-editor"></a>Passo 4: Editar através do editor visual

Utilize o editor visual para executar estas tarefas:

- Personalize o seu portal
- Conteúdo do autor
- Organizar a estrutura do site
- Estilizar a sua aparência

Ver [Tutorial: Aceda e personalize o portal do desenvolvedor.](api-management-howto-developer-portal-customize.md) Abrange os fundamentos da interface administrativa do utilizador e enumera as alterações recomendadas ao conteúdo predefinido. Guarde todas as alterações no ambiente local e pressione **Ctrl+C** para fechá-lo.

## <a name="step-5-publish-locally"></a>Passo 5: Publicar localmente

Os dados do portal têm origem na forma de objetos de tipo forte. O seguinte comando traduz-os em ficheiros estáticos e coloca a saída no `./dist/website` diretório:

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>Passo 6: Carregar ficheiros estáticos para uma bolha

Use o CLI do Azure para enviar os ficheiros estáticos gerados localmente para uma bolha, e certifique-se de que os seus visitantes podem chegar até eles:

1. Abra o pedido de comando do Windows, PowerShell ou outra concha de comando.

1. Executar o seguinte comando Azure CLI.
   
    `<account-connection-string>`Substitua-a pela cadeia de ligação da sua conta de armazenamento. Pode obtê-lo na secção **de chaves de acesso** da sua conta de armazenamento.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>Passo 7: Vá ao seu site

O seu website está agora ao vivo sob o nome de anfitrião especificado nas suas propriedades de Armazenamento Azure **(Ponto final principal** em **websites Estáticos).**

## <a name="step-8-change-api-management-notification-templates"></a>Passo 8: Alterar modelos de notificação de gestão da API

Substitua o URL do portal do desenvolvedor nos modelos de notificação da API Management para indicar o seu portal auto-hospedado. Ver [Como configurar notificações e modelos de email na Azure API Management](api-management-howto-configure-notifications.md).

Em particular, efetuar as seguintes alterações aos modelos predefinidos:

> [!NOTE]
> Os valores nas seguintes secções **atualizadas** pressupõem que está a hospedar o portal em **https: \/ /portal.contoso.com/**. 

### <a name="email-change-confirmation"></a>Confirmação de alteração de e-mail

Atualize o URL do portal do desenvolvedor no modelo de notificação **de confirmação de alteração de email:**

**Conteúdo original**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Atualizado**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Nova confirmação da conta do programador

Atualize o URL do portal do desenvolvedor no novo modelo de notificação **de confirmação de conta do desenvolvedor:**

**Conteúdo original**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Atualizado**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Convidar utilizador

Atualizar o URL do portal do desenvolvedor no modelo de notificação do **utilizador convidar:**

**Conteúdo original**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Atualizado**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Nova subscrição ativada

Atualizar o URL do portal do desenvolvedor no novo modelo de notificação **ativado por subscrição:**

**Conteúdo original**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Atualizado**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Conteúdo original**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Atualizado**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Conteúdo original**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Atualizado**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Conteúdo original**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Atualizado**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Confirmação de alteração de senha

Atualizar o URL do portal do desenvolvedor no modelo de notificação **de confirmação de alteração de palavra-passe:**

**Conteúdo original**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Atualizado**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Todos os modelos

Atualize o URL do portal do desenvolvedor em qualquer modelo que tenha uma ligação no rodapé:

**Conteúdo original**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Atualizado**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Passar de gerido para o portal de desenvolvimento auto-hospedado

Com o tempo, os requisitos do seu negócio podem mudar. Pode acabar numa situação em que a versão gerida do portal de desenvolvimento da API Management já não satisfaz as suas necessidades. Por exemplo, um novo requisito pode forçá-lo a construir um widget personalizado que se integra com um fornecedor de dados de terceiros. Ao contrário da versão manged, a versão auto-hospedada do portal oferece-lhe total flexibilidade e extensibilidade.

### <a name="transition-process"></a>Processo de transição

Pode transitar da versão gerida para uma versão auto-hospedada dentro da mesma instância de serviço de Gestão API. O processo preserva as modificações que realizou na versão gerida do portal. Certifique-se de que faz o back-up do conteúdo do portal previamente. Pode encontrar o script de cópia de segurança na `scripts` pasta do portal de desenvolvimento da API Management [GitHub repo](https://github.com/Azure/api-management-developer-portal).

O processo de conversão é quase idêntico à criação de um portal genérico auto-hospedado, como mostram os passos anteriores neste artigo. Há uma exceção no passo de configuração. A conta de armazenamento no `config.design.json` ficheiro tem de ser a mesma que a conta de armazenamento da versão gerida do portal. Ver [Tutorial: Utilize uma identidade atribuída ao sistema Linux VM para aceder ao Azure Storage através de uma credencial SAS](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls) para obter instruções sobre como recuperar o URL SAS.

> [!TIP]
> Recomendamos a utilização de uma conta de armazenamento separada no `config.publish.json` ficheiro. Esta abordagem dá-lhe mais controlo e simplifica a gestão do serviço de hospedagem do seu portal.

## <a name="enable-captcha"></a>Ativar o CAPTCHA

Ao configurar o portal auto-hospedado, poderá ter desativado o CAPTCHA através da `useHipCaptcha` definição. A comunicação com o CAPTCHA acontece através de um ponto final, que permite que a partilha de recursos de origem cruzada (CORS) aconteça apenas para o nome de anfitrião do portal de desenvolvimento gerido. Se o seu portal de desenvolvedores for auto-hospedado, ele usa um nome de anfitrião diferente e o CAPTCHA não permitirá a comunicação.

### <a name="update-the-json-config-files"></a>Atualizar os ficheiros config JSON

Para ativar o CAPTCHA no seu portal auto-hospedado:

1. Atribua um domínio personalizado (por exemplo, `api.contoso.com` ) ao ponto final do portal **Developer** do seu serviço de Gestão API.

    Este domínio aplica-se à versão gerida do seu portal e ao ponto final do CAPTCHA. Para etapas, consulte [configurar um nome de domínio personalizado para a sua instância de Gestão API Azure](configure-custom-domain.md).

1. Vá à `src` pasta do ambiente [local](#step-1-set-up-local-environment) para o seu portal auto-hospedado.

1. Atualizar os ficheiros de `json` configuração:

    | Ficheiro | Valor novo | Nota |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | `<custom-domain>`Substitua-o pelo domínio personalizado que configurar no primeiro passo. |
    |  | `"useHipCaptcha": true` | Alterar o valor para `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | `<custom-domain>`Substitua-o pelo domínio personalizado que configurar no primeiro passo. |
    |  | `"useHipCaptcha": true` | Alterar o valor para `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | `<custom-domain>`Substitua-o pelo domínio personalizado que configurar no primeiro passo. |

1. [Publique](#step-5-publish-locally) o portal.

1. [Faça upload](#step-6-upload-static-files-to-a-blob) e apresente o portal recém-publicado.

1. Exponha o portal auto-hospedado através de um domínio personalizado.

Os primeiros e segundos níveis do domínio do portal precisam de corresponder ao domínio configurado no primeiro passo. Por exemplo, `portal.contoso.com`. Os passos exatos dependem da sua plataforma de hospedagem de eleição. Se usou uma conta de armazenamento Azure, pode consultar [um domínio personalizado para um ponto final de armazenamento Azure Blob](../storage/blobs/storage-custom-domain-name.md) para obter instruções.

## <a name="next-steps"></a>Passos seguintes

- Conheça as [abordagens alternativas ao auto-alojamento](developer-portal-alternative-processes-self-host.md)
