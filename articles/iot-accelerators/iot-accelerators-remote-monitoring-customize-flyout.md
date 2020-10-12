---
title: Adicione um flyout à solução de monitorização remota UI - Azure / Microsoft Docs
description: Este artigo mostra-lhe como adicionar um novo flyout numa página na UI do acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: afd8c5b01b9f36606bf6bd43fbed747ca83ba320
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318420"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione um flyout personalizado à UI do acelerador de solução de monitorização remota

Este artigo mostra-lhe como adicionar um novo flyout a uma página na UI do acelerador de solução de monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo flyout a uma página na UI web.

O exemplo flyout neste artigo mostra na página com a grelha que o Add uma grelha personalizada ao artigo [web UI](iot-accelerators-remote-monitoring-customize-grid.md) como adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa do seguinte software instalado na sua máquina de desenvolvimento local:

- [Rio Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Deve completar os passos nos seguintes artigos antes de continuar:

- [Adicione uma página personalizada à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-page.md).
- [Adicione um serviço personalizado à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-service.md)
- [Adicione uma grelha personalizada à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Adicionar uma lista de opções

Para adicionar um flyout à UI web, é necessário adicionar os ficheiros de origem que definem o flyout e modificar alguns ficheiros existentes para sensibilizar a Web UI para o novo componente.

### <a name="add-the-new-files-that-define-the-flyout"></a>Adicione os novos ficheiros que definem o flyout

Para começar, a pasta **src/walkthrough/componentes/pages/pageWithFlyout/flyouts/exemploFlyouts** contém os ficheiros que definem um flyout:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Copie a pasta **src/walkthrough/componentes/pages/pageWithFlyout/flyouts** para a pasta **src/componentes/páginas/exemplo.**

### <a name="add-the-flyout-to-the-page"></a>Adicione o flyout à página

Modifique o **src/componentes/páginas/exemplo/basicPage.js** para adicionar o flyout.

Adicione **a Btn** às importações de **componentes/partilhados** e adicione importações de **svgs** e **ExemploFlyoutContainer:**

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

Adicione uma definição **de const** para **closedFlyoutState** e adicione-a ao estado do construtor:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Adicione as seguintes funções à classe **BasicPage:**

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Adicione as seguintes definições **de const** à função **de renderização:**

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Adicione um botão para abrir o flyout ao menu de contexto:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Adicione um pouco de texto e o recipiente de voo ao conteúdo da página:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Teste o flyout

Se a UI web ainda não estiver a funcionar localmente, executa o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior dirige a UI localmente em `http://localhost:3000/dashboard` . Navegue na página **Exemplo** e clique **em Open Flyout**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a adicionar ou personalizar páginas na UI web no acelerador de solução de Monitorização Remota.

Agora que definiu um flyout numa página, o próximo passo é [adicionar um painel ao painel no painel do acelerador de solução de monitorização remota UI](iot-accelerators-remote-monitoring-customize-panel.md).

Para obter mais informações conceptuais sobre o acelerador de solução de monitorização remota, consulte [a arquitetura de monitorização remota.](iot-accelerators-remote-monitoring-sample-walkthrough.md)
