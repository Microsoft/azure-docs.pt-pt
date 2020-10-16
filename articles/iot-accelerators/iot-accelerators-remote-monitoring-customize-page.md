---
title: Adicione uma página à solução de monitorização remota UI - Azure / Microsoft Docs
description: Este artigo mostra-lhe como adicionar uma nova página na UI do acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 2e54cd0e3c5b9499a3bc1a499ae4cef62c73160e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070712"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione uma página personalizada à UI do acelerador de solução de monitorização remota

Este artigo mostra-lhe como adicionar uma nova página na UI do acelerador de solução de monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar uma nova página à UI web.

Outros guias de como adicionar este cenário para adicionar mais funcionalidades à página que adiciona.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa do seguinte software instalado na sua máquina de desenvolvimento local:

- [Rio Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparar um ambiente de desenvolvimento local para a UI

O código UI do acelerador de solução de monitorização remota é implementado utilizando a estrutura [React](https://reactjs.org/) JavaScript. Pode encontrar o código fonte no repositório WebUI GitHub [de monitorização remota.](https://github.com/Azure/pcs-remote-monitoring-webui)

Para fazer e testar alterações na UI, pode executá-la na sua máquina de desenvolvimento local. Opcionalmente, a cópia local pode ligar-se a uma instância implantada do acelerador de solução para permitir que interaja com os seus dispositivos reais ou simulados.

Para preparar o seu ambiente de desenvolvimento local, use o Git para clonar o repositório [WebUI de monitorização remota](https://github.com/Azure/pcs-remote-monitoring-webui) para a sua máquina local:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Adicionar uma página

Para adicionar uma página à UI web, é necessário adicionar os ficheiros de origem que definem a página e modificar alguns ficheiros existentes para sensibilizar a UI web para a nova página.

### <a name="add-the-new-files-that-define-the-page"></a>Adicione os novos ficheiros que definem a página

Para começar, a pasta **src/walkthrough/componentes/pages/basicPage** contém quatro ficheiros que definem uma página simples:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Crie uma nova pasta **src/componentes/páginas/exemplo** e copie estes quatro ficheiros nele.

### <a name="add-the-new-page-to-the-web-ui"></a>Adicione a nova página à UI web

Para adicionar a nova página na UI web, faça as seguintes alterações aos ficheiros existentes:

1. Adicione o novo recipiente de página ao ficheiro **src/componentes/páginas/index.js:**

    ```js
    export * from './example/basicPage.container';
    ```

1. (Opcional)  Adicione um ícone SVG para a nova página. Para mais informações, consulte [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Pode utilizar um ficheiro SVG existente.

1. Adicione o nome da página ao ficheiro de traduções, **público/local/pt/translations.jsem**. A UI web usa [o i18next](https://www.i18next.com/) para internacionalização.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Abra o ficheiro **src/componentes/app.js** que define a página de aplicação de nível superior. Adicione a nova página à lista de importações:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. No mesmo ficheiro, adicione a nova página à `pagesConfig` matriz. Desagrega o `to` endereço para a rota, consulte o ícone SVG e as traduções adicionadas anteriormente e deite `component` o no recipiente da página:

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

1. Adicione novas migalhas de pão à `crumbsConfig` matriz:

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

Num comando, navegue pela raiz da sua cópia local do repositório, e execute os seguintes comandos para instalar as bibliotecas necessárias e executar a UI web localmente:

```cmd/sh
npm install
npm start
```

O comando anterior dirige a UI localmente em `http://localhost:3000/dashboard` .

Sem ligar a sua instância local da UI web a uma instância implementada do acelerador de solução, vê erros no painel de instrumentos. Estes erros não afetam a sua capacidade de testar a sua nova página.

Agora pode editar o código enquanto o site está a funcionar localmente e ver a atualização do UI web dinamicamente.

## <a name="optional-connect-to-deployed-instance"></a>[Opcional] Ligar-se à instância implantada

Opcionalmente, pode ligar a sua cópia de execução local do UI web ao acelerador de solução de monitorização remota na nuvem:

1. Implementar uma instância **básica** do acelerador de solução utilizando o **CLI dos computadores.** Tome nota do nome da sua implantação e das credenciais que forneceu para a máquina virtual. Para obter mais informações, consulte [implementar utilizando o CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Utilize o portal Azure ou o [Az CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) para permitir o acesso do SSH à máquina virtual que acolhe os microserviços na sua solução. Por exemplo:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Só deve permitir o acesso ao SSH durante o teste e o desenvolvimento. Se ativar o SSH, [deve desativá-lo o mais rapidamente possível](../security/fundamentals/network-best-practices.md).

1. Utilize o portal Azure ou o [CLI az](/cli/azure/install-azure-cli?view=azure-cli-latest) para encontrar o nome e o endereço IP público da sua máquina virtual. Por exemplo:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Utilize o SSH para ligar à sua máquina virtual utilizando o endereço IP do passo anterior e as credenciais que forneceu quando executou **os computadores** para implementar a solução.

1. Para permitir a ligação do UX local, corram os seguintes comandos na casca de choque na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de ver o comando completo e o web site começar, pode desligar-se da máquina virtual.

1. Na sua cópia local do repositório [WebUI de monitorização remota,](https://github.com/Azure/pcs-remote-monitoring-webui) edite o ficheiro **.env** para adicionar o URL da sua solução implementada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a UI web no acelerador de solução de Monitorização Remota.

Agora que definiu uma página, o próximo passo é [adicionar um serviço personalizado à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-service.md) que recupera dados para exibir na UI.

Para obter mais informações conceptuais sobre o acelerador de solução de monitorização remota, consulte [a arquitetura de monitorização remota.](iot-accelerators-remote-monitoring-sample-walkthrough.md)