---
title: Adicionar uma página à interface do usuário da solução de monitoramento remoto – Azure | Microsoft Docs
description: Este artigo mostra como adicionar uma nova página à interface do usuário da Web do acelerador de solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: ec0b9fbdfdb96317e1e7f6fe00384ba4f8c42bcc
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607962"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicionar uma página personalizada à interface do usuário Web do acelerador de solução de monitoramento remoto

Este artigo mostra como adicionar uma nova página à interface do usuário da Web do acelerador de solução de monitoramento remoto. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar uma nova página à interface do usuário da Web.

Outros guias de instruções ampliam esse cenário para adicionar mais recursos à página que você adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, você precisará do seguinte software instalado em seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparar um ambiente de desenvolvimento local para a interface do usuário

O código da interface do usuário do acelerador de solução de monitoramento remoto é implementado usando a estrutura de JavaScript [reagir](https://reactjs.org/) . Você pode encontrar o código-fonte no repositório GitHub de monitoramento de software [remoto](https://github.com/Azure/pcs-remote-monitoring-webui) .

Para fazer e testar alterações na interface do usuário, você pode executá-las em seu computador de desenvolvimento local. Opcionalmente, a cópia local pode se conectar a uma instância implantada do Solution Accelerator para permitir que ela interaja com seus dispositivos reais ou simulados.

Para preparar seu ambiente de desenvolvimento local, use o Git para clonar o repositório [WebUI do monitoramento remoto](https://github.com/Azure/pcs-remote-monitoring-webui) para seu computador local:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Adicionar uma página

Para adicionar uma página à interface do usuário da Web, você precisa adicionar os arquivos de origem que definem a página e modificar alguns arquivos existentes para tornar a interface do usuário da Web consciente da nova página.

### <a name="add-the-new-files-that-define-the-page"></a>Adicionar os novos arquivos que definem a página

Para começar, a pasta **src/Walkthrough/Components/Pages/página básica** contém quatro arquivos que definem uma página simples:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Crie uma nova pasta **src/Components/Pages/example** e copie esses quatro arquivos nela.

### <a name="add-the-new-page-to-the-web-ui"></a>Adicionar a nova página à interface do usuário da Web

Para adicionar a nova página à interface do usuário da Web, faça as seguintes alterações nos arquivos existentes:

1. Adicione o novo contêiner de página ao arquivo **src/Components/pages/index. js** :

    ```js
    export * from './example/basicPage.container';
    ```

1. Adicional  Adicione um ícone SVG para a nova página. Para obter mais informações, consulte [WebUI/src/Utilities/README. MD](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Você pode usar um arquivo SVG existente.

1. Adicione o nome da página ao arquivo de traduções, **Public/localings/en/Translations. JSON**. A interface do usuário da Web usa [i18next](https://www.i18next.com/) para internacionalização.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Abra o arquivo **src/Components/app. js** que define a página de aplicativo de nível superior. Adicione a nova página à lista de importações:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. No mesmo arquivo, adicione a nova página à `pagesConfig` matriz. Defina o `to` endereço para a rota, referencie o ícone SVG e as traduções adicionadas anteriormente e `component` defina o como o contêiner da página:

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Adicione novas trilhas à `crumbsConfig` matriz:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Esta página de exemplo tem apenas um breadcrumb, mas algumas páginas podem ter mais.

Guarde todas as alterações. Você está pronto para executar a interface do usuário da Web com a nova página adicionada.

### <a name="test-the-new-page"></a>Testar a nova página

Em um prompt de comando, navegue até a raiz da cópia local do repositório e execute os seguintes comandos para instalar as bibliotecas necessárias e executar a interface do usuário da Web localmente:

```cmd/sh
npm install
npm start
```

O comando anterior executa a interface do usuário [http://localhost:3000/dashboard](http://localhost:3000/dashboard)localmente em.

Sem conectar sua instância local da interface do usuário da Web a uma instância implantada do Solution Accelerator, você verá erros no painel. Esses erros não afetam sua capacidade de testar sua nova página.

Agora você pode editar o código enquanto o site está em execução localmente e ver a interface do usuário da Web atualizar dinamicamente.

## <a name="optional-connect-to-deployed-instance"></a>Adicional Conectar à instância implantada

Opcionalmente, você pode conectar sua cópia em execução local da interface do usuário da Web ao acelerador de solução de monitoramento remoto na nuvem:

1. Implante uma instância **básica** do Solution Accelerator usando a CLI do **PCs** . Anote o nome da sua implantação e as credenciais fornecidas para a máquina virtual. Para obter mais informações, consulte [implantar usando a CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Use o portal do Azure ou a [CLI do AZ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para habilitar o acesso SSH à máquina virtual que hospeda os microserviços em sua solução. Por exemplo:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Você só deve habilitar o acesso SSH durante o teste e o desenvolvimento. Se você habilitar o SSH, [deverá desabilitá-lo novamente](../security/fundamentals/network-best-practices.md)assim que possível.

1. Use o portal do Azure ou o [AZ CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para localizar o nome e o endereço IP público de sua máquina virtual. Por exemplo:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Use o SSH para se conectar à sua máquina virtual usando o endereço IP da etapa anterior e as credenciais fornecidas quando você executou **computadores** para implantar a solução.

1. Para permitir que o UX local se conecte, execute os seguintes comandos no shell bash na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de ver o comando ser concluído e o site for iniciado, você poderá desconectar-se da máquina virtual.

1. Em sua cópia local do repositório [WebUI do monitoramento remoto](https://github.com/Azure/pcs-remote-monitoring-webui) , edite o arquivo **. env** para adicionar a URL da solução implantada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a interface do usuário da Web no acelerador de solução de monitoramento remoto.

Agora que você definiu uma página, a próxima etapa é [Adicionar um serviço personalizado à interface do usuário Web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-service.md) que recupera dados a serem exibidos na interface do usuário.

Para obter mais informações conceituais sobre o acelerador de solução de monitoramento remoto, consulte [arquitetura de monitoramento remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
