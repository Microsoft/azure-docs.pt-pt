---
title: Adicione uma página à solução de Monitorização Remota UI - Azure [ Microsoft Docs
description: Este artigo mostra-lhe como adicionar uma nova página no acelerador de soluções de monitorização remota UI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 0228f317e2d3380f2387dd557a27203eb3abc4ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240268"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione uma página personalizada ao uI web de solução de monitorização remota

Este artigo mostra-lhe como adicionar uma nova página no acelerador de soluções de monitorização remota UI. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar uma nova página à UI web.

Outros guias de como-fazer estendem este cenário para adicionar mais funcionalidades à página que adiciona.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como orientar, precisa do seguinte software instalado na sua máquina de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Nó.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Prepare um ambiente de desenvolvimento local para a UI

O código UI da solução de monitorização remota é implementado utilizando a estrutura [React](https://reactjs.org/) JavaScript. Pode encontrar o código fonte no repositório WebUI GitHub de [monitorização remota.](https://github.com/Azure/pcs-remote-monitoring-webui)

Para fazer e testar alterações na UI, pode executá-lo na sua máquina de desenvolvimento local. Opcionalmente, a cópia local pode ligar-se a uma instância implantada do acelerador de solução para permitir que interaja com os seus dispositivos reais ou simulados.

Para preparar o seu ambiente de desenvolvimento local, utilize git para clonar o [repositório WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) de monitorização remota à sua máquina local:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Adicionar uma página

Para adicionar uma página ao UI web, é necessário adicionar os ficheiros de origem que definem a página e modificar alguns ficheiros existentes para sensibilizar a web UI para a nova página.

### <a name="add-the-new-files-that-define-the-page"></a>Adicione os novos ficheiros que definem a página

Para começar, o **src/walkthrough/componentes/páginas/basicPage** contém quatro ficheiros que definem uma página simples:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Crie uma nova pasta **src/components/pages/exemplo** e copie estes quatro ficheiros para dentro.

### <a name="add-the-new-page-to-the-web-ui"></a>Adicione a nova página à Web UI

Para adicionar a nova página à UI web, efelo as seguintes alterações aos ficheiros existentes:

1. Adicione o novo recipiente de página ao ficheiro **src/components/pages/index.js:**

    ```js
    export * from './example/basicPage.container';
    ```

1. (Opcional)  Adicione um ícone SVG para a nova página. Para mais informações, consulte [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Pode utilizar um ficheiro SVG existente.

1. Adicione o nome da página ao ficheiro de traduções, **público/local/en/traduções.json**. A Web UI usa [o i18next](https://www.i18next.com/) para internacionalização.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Abra o ficheiro **src/components/app.js** que define a página de aplicação de alto nível. Adicione a nova página à lista de importações:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. No mesmo ficheiro, adicione a `pagesConfig` nova página à matriz. Delineie o `to` endereço para a rota, refira o ícone `component` SVG e as traduções adicionadas anteriormente e delineie o recipiente da página:

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

1. Adicione quaisquer migalhas `crumbsConfig` novas à matriz:

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

    Esta página de exemplo tem apenas uma migalha de pão, mas algumas páginas podem ter mais.

Guarde todas as alterações. Está pronto para executar a UI web com a sua nova página adicionada.

### <a name="test-the-new-page"></a>Teste a nova página

A uma solicitação de comando, navegue até à raiz da sua cópia local do repositório, e execute os seguintes comandos para instalar as bibliotecas necessárias e executar a Web UI localmente:

```cmd/sh
npm install
npm start
```

O comando anterior executa [http://localhost:3000/dashboard](http://localhost:3000/dashboard)a UI localmente em .

Sem ligar a sua instância local da UI web a uma instância implantada do acelerador de soluções, vê erros no painel de instrumentos. Estes erros não afetam a sua capacidade de testar a sua nova página.

Agora pode editar o código enquanto o site está a funcionar localmente e ver a atualização de UI web dinamicamente.

## <a name="optional-connect-to-deployed-instance"></a>[Opcional] Ligar a instância implantada

Opcionalmente, pode ligar a sua cópia de execução local do UI web ao acelerador de solução de monitorização remota na nuvem:

1. Implemente uma instância **básica** do acelerador de solução utilizando o **CLI pcs.** Tome nota do nome da sua implantação e das credenciais que forneceu para a máquina virtual. Para mais informações, consulte [A utilização do CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Utilize o portal Azure ou o [Az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para permitir o acesso sSH à máquina virtual que acolhe os microserviços na sua solução. Por exemplo:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Só deve permitir o acesso sSH durante o teste e desenvolvimento. Se ativar o SSH, [deve desativá-lo novamente o mais rapidamente possível](../security/fundamentals/network-best-practices.md).

1. Utilize o portal Azure ou o [Az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para encontrar o nome e endereço IP público da sua máquina virtual. Por exemplo:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Utilize o SSH para se ligar à sua máquina virtual utilizando o endereço IP do passo anterior e as credenciais que forneceu quando executou **os PCs** para implementar a solução.

1. Para permitir que o UX local se conecte, execute os seguintes comandos na concha da batida na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de ver o comando completo e o site começar, pode desligar-se da máquina virtual.

1. Na sua cópia local do [repositório WebUI de monitorização remota,](https://github.com/Azure/pcs-remote-monitoring-webui) edite o ficheiro **.env** para adicionar o URL da sua solução implementada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a UI web no acelerador de soluções de Monitorização Remota.

Agora que definiu uma página, o próximo passo é [adicionar um serviço personalizado ao acelerador de soluções de monitorização remota UI](iot-accelerators-remote-monitoring-customize-service.md) que recupera dados para exibir na UI.

Para obter informações mais conceptuais sobre o acelerador de soluções de monitorização remota, consulte [a arquitetura de Monitorização Remota.](iot-accelerators-remote-monitoring-sample-walkthrough.md)
