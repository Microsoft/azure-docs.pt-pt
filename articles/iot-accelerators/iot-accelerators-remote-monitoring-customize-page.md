---
title: Adicionar uma página para a solução de monitorização remota da interface do Usuário - Azure | Documentos da Microsoft
description: Este artigo mostra-lhe como adicionar uma nova página em monitorização remota solution accelerator da IU da web.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 95830cdffb232e16f9fbae51cfa11fbd18172c3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447085"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicionar uma página personalizada para monitorização remota solution accelerator da IU da web

Este artigo mostra-lhe como adicionar uma nova página em monitorização remota solution accelerator da IU da web. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar uma nova página para a IU da web.

Outros guias de procedimentos expandir este cenário, para adicionar mais funcionalidades para a página que adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos, terá o seguinte software instalado no seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparar um ambiente de desenvolvimento local para a interface do Usuário

O acelerador de solução de monitorização remota código de interface do Usuário é implementado com o [reagir](https://reactjs.org/) estrutura JavaScript. Pode encontrar o código-fonte no [serem de monitorização remota](https://github.com/Azure/pcs-remote-monitoring-webui) repositório do GitHub.

Para efetuar e testar as alterações na interface do usuário, pode executá-lo no seu computador de desenvolvimento local. Opcionalmente, a cópia local pode ligar a uma instância implementada do solution accelerator para ativá-la interagir com os seus dispositivos reais ou simulados.

Para preparar o ambiente de desenvolvimento local, utilize o Git para clonar o [serem de monitorização remota](https://github.com/Azure/pcs-remote-monitoring-webui) repositório no seu computador local:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Adicionar uma página

Para adicionar uma página da IU da web, terá de adicionar os ficheiros de origem que definem a página e modificar alguns ficheiros existentes conscientizá da nova página da IU da web.

### <a name="add-the-new-files-that-define-the-page"></a>Adicionar os novos ficheiros que definem a página

Para começar, o **src/instruções/componentes/páginas/basicPage** pasta contém quatro arquivos que definem uma página simples:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Crie uma nova pasta **src/componentes/páginas/exemplo** e copie esses quatro arquivos para o mesmo.

### <a name="add-the-new-page-to-the-web-ui"></a>Adicionar a nova página para a IU da web

Para adicionar a nova página da IU da web, efetue as seguintes alterações aos ficheiros existentes:

1. Adicionar o novo contentor de página para o **src/components/pages/index.js** ficheiro:

    ```js
    export * from './example/basicPage.container';
    ```

1. (Opcional)  Adicione um ícone SVG para a nova página. Para obter mais informações, consulte [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Pode utilizar um ficheiro SVG existente.

1. Adicionar o nome da página para o ficheiro de traduções **public/locales/en/translations.json**. A web utiliza a interface do Usuário [i18next](https://www.i18next.com/) para internacionalização.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Abra o **src/components/app.js** ficheiro que define a página de aplicativo de nível superior. Adicione a nova página à lista de importações:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. No mesmo ficheiro, adicione a nova página para o `pagesConfig` matriz. Definir o `to` de endereços para a rota, referenciar o ícone SVG e traduções adicionadas anteriormente e defina o `component` para o contentor da página:

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

1. Adicionar qualquer estrutura de ligações novas para o `crumbsConfig` matriz:

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

    Esta página de exemplo tem apenas uma trilha, mas algumas páginas podem ter muito mais.

Guarde todas as alterações. Está pronto para executar a IU da web com sua nova página adicionada.

### <a name="test-the-new-page"></a>Testar a nova página

No prompt de comando navegue para a raiz da sua cópia local do repositório e execute os seguintes comandos para instalar as bibliotecas necessárias e executar localmente a IU da web:

```cmd/sh
npm install
npm start
```

O comando anterior é a interface do Usuário localmente em executado [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard).

Sem precisar se conectar a instância local da web da interface do Usuário para uma instância implementada do solution accelerator, consulte os erros no dashboard. Estes erros não afetam sua capacidade de testar a sua nova página.

Agora pode editar o código, enquanto o site está em execução localmente e veja o web atualizar dinamicamente a interface do Usuário.

## <a name="optional-connect-to-deployed-instance"></a>[Opcional] Ligar à instância implementada

Opcionalmente, pode ligar a sua cópia local em execução da IU da web para o acelerador de solução de monitorização remota na cloud:

1. Implementar um **básica** instância do acelerador de solução utilizando o **pcs** CLI. Tome nota do nome da sua implementação e as credenciais fornecidas para a máquina virtual. Para obter mais informações, consulte [implementar com a CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Utilizar o portal do Azure ou o [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para ativar o acesso SSH à máquina virtual que aloja os microsserviços na sua solução. Por exemplo:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Só deverá ativar o acesso SSH durante o desenvolvimento e teste. Se ativar o SSH, [deve desabilitá-la novamente logo que possível](../security/azure-security-network-security-best-practices.md).

1. Utilizar o portal do Azure ou o [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para encontrar o nome e endereço IP público da sua máquina virtual. Por exemplo:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Utilizar o SSH para ligar à máquina virtual com o endereço IP do passo anterior e as credenciais que forneceu quando executou **pcs** para implantar a solução.

1. Para permitir que a experiência do Usuário local ligar, execute os seguintes comandos na shell do bash na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de confirmar o comando for concluído e o web site é iniciado, pode desligar da máquina virtual.

1. Na sua cópia local dos [serem de monitorização remota](https://github.com/Azure/pcs-remote-monitoring-webui) repositório, editar o **. env** ficheiro para adicionar o URL da sua solução implementada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para o ajudar a personalizar a IU da web no solution accelerator monitorização remota.

Agora que definiu uma página, a próxima etapa é [adicionar um serviço personalizado para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-service.md) que obtém dados a serem exibidos na interface do Usuário.

Para obter mais informações concetuais sobre o acelerador de solução de monitorização remota, consulte [arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md).
