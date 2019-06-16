---
title: Adicionar um submenu para a solução de monitorização remota da interface do Usuário - Azure | Documentos da Microsoft
description: Este artigo mostra-lhe como adicionar uma nova lista de opções numa página da web de acelerador de solução da interface do Usuário da monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447119"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicionar uma lista de opções personalizada para monitorização remota solution accelerator da IU da web

Este artigo mostra-lhe como adicionar uma nova lista de opções numa página web de acelerador de solução da interface do Usuário da monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um submenu de novo a uma página na IU da web.

O submenu de exemplo neste artigo apresenta a página e a grade que o [adicionar uma grade personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-grid.md) procedimento artigo mostra-lhe como adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos, terá o seguinte software instalado no seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Antes de continuar, deve de concluir os passos nos seguintes artigos:

- [Adicionar uma página personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-page.md).
- [Adicionar um serviço personalizado para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-service.md)
- [Adicionar uma grade personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Adicionar uma lista de opções

Para adicionar um submenu para a IU da web, terá de adicionar os ficheiros de origem que definem o submenu e modificar alguns ficheiros existentes para tornar a IU da web em consideração o novo componente.

### <a name="add-the-new-files-that-define-the-flyout"></a>Adicionar os novos ficheiros que definem o submenu

Para começar, o **src/instruções/componentes/páginas/pageWithFlyout/flyouts/exampleFlyout** pasta contém os ficheiros que definem um submenu:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Copiar o **passo a passo/src/componentes/pageWithFlyout/páginas/flyouts** pasta para o **src/componentes/páginas/exemplo** pasta.

### <a name="add-the-flyout-to-the-page"></a>Adicionar a lista de opções para a página

Modificar a **src/components/pages/example/basicPage.js** para adicionar o submenu.

Adicione **Btn** para as importações de **componentes/partilhados** e adicione importações para **svgs** e **ExampleFlyoutContainer**:

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Adicionar uma **const** definição para **closedFlyoutState** e adicioná-lo para o estado no construtor:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Adicione as seguintes funções para o **BasicPage** classe:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Adicione as seguintes **const** definições para o **renderizar** função:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Adicione um botão para abrir a lista de opções ao menu de contexto:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Adicione algum texto e o contentor de lista de opções para o conteúdo da página:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testar a lista de opções

Se o web interface do Usuário não está já em execução localmente, execute o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior é a interface do Usuário localmente em executado [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Navegue para o **exemplo** página e clique em **flutuante aberto**.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para o ajudar a adicionar ou personalizar páginas na IU da web no solution accelerator monitorização remota.

Agora que definiu um submenu numa página, a próxima etapa é [adicionar um painel para o dashboard de monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-panel.md).

Para obter mais informações concetuais sobre o acelerador de solução de monitorização remota, consulte [arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md).
