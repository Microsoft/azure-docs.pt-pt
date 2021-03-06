---
title: Configurar configurações de aplicações para aplicações web estáticas Azure
description: Aprenda a configurar as definições de aplicações para Azure Static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 2886cb6433c4f8a65eb0661eded0ddd08248e137
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219187"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Configurar configurar configurações de aplicações para visualização de aplicações web estáticas Azure

As definições de aplicação mantêm as definições de configuração para valores que podem ser alterados, tais como cadeias de ligação de base de dados. A adição de definições de aplicação permite modificar a entrada de configuração da sua aplicação, sem ter de alterar o código da aplicação.

As definições de aplicação são:

- Encriptado em repouso
- Copiado para ambientes [de encenação](review-publish-pull-requests.md) e produção

As definições de aplicação também são por vezes referidas como variáveis ambientais.

> [!IMPORTANT]
> As definições de aplicação descritas neste artigo aplicam-se apenas à API de backend de uma App Web Estática Azure.
>
> Para obter informações sobre a utilização de variáveis ambientais com a sua aplicação web frontend, consulte os docs para a sua [estrutura JavaScript](#javascript-frameworks-and-libraries) ou [gerador de site estático](#static-site-generators).

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação de aplicações web estáticas Azure
- [CLI do Azure](/cli/azure/install-azure-cli)

## <a name="types-of-application-settings"></a>Tipos de configurações de aplicações

Existem normalmente dois aspetos para uma aplicação Azure Static Web Apps. A primeira é a aplicação web, ou conteúdo estático, que é representado por HTML, CSS, JavaScript e imagens. A segunda é a API de back-end, que é alimentada por uma aplicação Azure Functions.

Este artigo demonstra como gerir as definições de aplicação para a API de back-end em Funções Azure.

As definições de aplicação descritas neste artigo não podem ser utilizadas ou referenciadas em aplicações web estáticas. No entanto, muitas estruturas frontais e geradores estáticos permitem a utilização de variáveis ambientais durante o desenvolvimento. No momento da construção, estas variáveis são substituídas pelos seus valores no HTML ou JavaScript gerados. Uma vez que os dados em HTML e JavaScript são facilmente detetáveis pelo visitante do site, pretende evitar colocar informações sensíveis na aplicação frontal. As definições que possuem dados sensíveis estão melhor localizadas na parte API da sua aplicação.

Para obter informações sobre como utilizar variáveis ambientais com a sua estrutura ou biblioteca JavaScript, consulte os seguintes artigos para obter mais detalhes.

### <a name="javascript-frameworks-and-libraries"></a>Estruturas e bibliotecas JavaScript

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Geradores estáticos

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>Sobre as configurações da App API

As APIs em Azure Static Web Apps são alimentadas por Azure Functions, que lhe permite definir definições de aplicações no _local.settings.jsno_ ficheiro. Este ficheiro define as definições de aplicação na `Values` propriedade da configuração.

A amostra que se _seguelocal.settings.jsmostra_ como adicionar um valor para o `DATABASE_CONNECTION_STRING` .

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

As definições definidas na `Values` propriedade podem ser referenciadas a partir do código como variáveis ambientais, disponíveis a partir do `process.env` objeto.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

O `local.settings.json` ficheiro não é rastreado pelo repositório GitHub porque informações sensíveis, como cadeias de ligação de base de dados, são frequentemente incluídas no ficheiro. Uma vez que as definições locais permanecem na sua máquina, tem de carregar manualmente as suas definições para Azure.

Geralmente, o upload das suas definições é feito com pouca frequência, e não é necessário em cada construção.

## <a name="uploading-application-settings"></a>Definições de aplicações de upload

Pode configurar as definições de aplicações através do portal Azure ou com o Azure CLI.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

O portal Azure fornece uma interface para criar, atualizar e eliminar configurações de aplicações.

1. Navegue até ao [portal Azure.](https://portal.azure.com)

1. Na barra de pesquisa, procure e selecione **Aplicações Web Estáticas.**

1. Clique na opção **Configuração** na barra lateral.

1. Selecione o ambiente a que pretende aplicar as definições de aplicação. Os ambientes de encenação são automaticamente criados quando um pedido de puxar é gerado, e são promovidos à produção uma vez que o pedido de puxão é fundido. Pode definir as definições de aplicação por ambiente.

1. Clique no **botão Adicionar** para adicionar uma nova definição de aplicação.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Vista de configuração de aplicativos web estáticos Azure":::

1. Insira um **Nome** e **Valor**.

1. Clique em **OK**.

1. Clique em **Guardar**.

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Pode utilizar o `az rest` comando para fazer uploads a granel das suas definições para Azure. O comando aceita configurações de aplicação como objetos JSON numa propriedade-mãe chamada `properties` .

A forma mais fácil de criar um ficheiro JSON com os valores apropriados é criar uma versão modificada do seu _local.settings.jsem_ ficheiro.

1. Para garantir que o seu novo ficheiro com dados sensíveis não seja exposto publicamente, adicione a seguinte entrada no seu ficheiro _.gitignore._

   ```bash
   local.settings*.json
   ```

2. Em seguida, faça uma cópia do seu _local.settings.jsno_ ficheiro e _nomeie-olocal.settings.properties.js._

3. Dentro do novo ficheiro, remova todos os outros dados do ficheiro, exceto as definições da aplicação e rebatize `Values` para `properties` .

   O seu ficheiro deve agora ser semelhante ao seguinte exemplo:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

O comando Azure CLI requer uma série de valores específicos da sua conta para executar o upload. A partir da janela _De visão geral_ do seu recurso Static Web Apps, tem acesso às seguintes informações:

1. Nome do site estático
2. Nome do grupo de recursos
3. ID da subscrição

:::image type="content" source="media/application-settings/overview.png" alt-text="Visão geral das aplicações web estáticas do Azure":::

4. A partir de um terminal ou linha de comando, execute o seguinte comando. Certifique-se de que substitui os espaços reservados de `<YOUR_STATIC_SITE_NAME>` , e pelos seus `<YOUR_RESOURCE_GROUP_NAME>` `<YOUR_SUBSCRIPTION_ID>` valores da janela _Overview._

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> O ficheiro "local.settings.properties.jsligado" deve estar no mesmo diretório onde este comando é executado. Este ficheiro pode ser nomeado como quiser. O nome não é significativo.

### <a name="view-application-settings-with-the-azure-cli"></a>Ver definições de aplicação com o Azure CLI

As definições de aplicação estão disponíveis para visualização através do CLI Azure.

- A partir de um terminal ou linha de comando, execute o seguinte comando. Certifique-se de que substitui os espaços `<YOUR_SUBSCRIPTION_ID>` reservados, `<YOUR_RESOURCE_GROUP_NAME>` pelos seus `<YOUR_STATIC_SITE_NAME>` valores.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar desenvolvimento local](local-development.md)
