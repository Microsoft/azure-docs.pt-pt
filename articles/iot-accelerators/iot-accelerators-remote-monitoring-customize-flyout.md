---
title: Adicione um flyout à solução de monitorização remota UI - Azure [ Microsoft Docs
description: Este artigo mostra-lhe como adicionar um novo flyout numa página na UI web de aceleração da solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3820b34a9c79f3b514327fa45a05eebaf8a2a1ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193178"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione um flyout personalizado ao UI web de solução de monitorização remota

Este artigo mostra-lhe como adicionar um novo flyout a uma página na UI web de aceleração da solução de monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo flyout a uma página na Web UI.

O exemplo deste artigo mostra na página com a grelha que a Adicionar uma grelha personalizada ao acelerador de [soluções de monitorização remota ui](iot-accelerators-remote-monitoring-customize-grid.md) como fazer mostra como adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como orientar, precisa do seguinte software instalado na sua máquina de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Deve completar os passos nos seguintes artigos antes de continuar:

- [Adicione uma página personalizada ao uI web de aceleração da solução de monitorização remota](iot-accelerators-remote-monitoring-customize-page.md).
- [Adicione um serviço personalizado ao uI web de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-service.md)
- [Adicione uma grelha personalizada ao uI web de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Adicionar uma lista de opções

Para adicionar um flyout ao UI web, você precisa adicionar os ficheiros de origem que definem o flyout, e modificar alguns ficheiros existentes para tornar a Web UI ciente do novo componente.

### <a name="add-the-new-files-that-define-the-flyout"></a>Adicione os novos ficheiros que definem o flyout

Para começar, a pasta **src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout** contém os ficheiros que definem um flyout:

**exemploFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exemploFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Copie a pasta **src/walkthrough/components/pages/pageWithFlyout/flyouts** para a pasta **src/components/pages/exemplo.**

### <a name="add-the-flyout-to-the-page"></a>Adicione o flyout à página

Modifique o **src/componentes/páginas/exemplo/basicPage.js** para adicionar o flyout.

Adicione **btn** às importações de **componentes/partilhados** e adicione importações para **svgs** e **ExampleFlyoutContainer:**

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

Adicione uma definição **de const** para **o FlyoutState fechado** e adicione-a ao estado no construtor:

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

Adicione as seguintes definições de **const** à função **renderização:**

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

Adicione um pouco de texto e o recipiente de flyout ao conteúdo da página:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Teste o voo para fora

Se a UI web ainda não estiver a funcionar localmente, execute o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior executa `http://localhost:3000/dashboard`a UI localmente em . Navegue para a página **Exemplo** e clique em **Open Flyout**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a adicionar ou personalizar páginas na UI web no acelerador de soluções de Monitorização Remota.

Agora que definiu um flyout numa página, o próximo passo é adicionar um painel ao painel no acelerador de [soluções remotas UI](iot-accelerators-remote-monitoring-customize-panel.md).

Para obter informações mais conceptuais sobre o acelerador de soluções de monitorização remota, consulte [a arquitetura de Monitorização Remota.](iot-accelerators-remote-monitoring-sample-walkthrough.md)
