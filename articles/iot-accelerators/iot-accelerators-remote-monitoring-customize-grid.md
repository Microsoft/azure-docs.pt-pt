---
title: Adicione uma grelha à solução de Monitorização Remota UI - Azure / Microsoft Docs
description: Este artigo mostra-lhe como adicionar um novo gid numa página na UI do acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 7fc878e0c9e099b201264c1c3981c603668214d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017768"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione uma grelha personalizada à UI do acelerador de solução de monitorização remota

Este artigo mostra-lhe como adicionar uma nova grelha numa página na UI do acelerador de solução de monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar uma nova grelha a uma página na UI web.

A grelha de exemplo neste artigo exibe os dados do serviço que o Add um serviço personalizado ao artigo [web UI](iot-accelerators-remote-monitoring-customize-service.md) como adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa do seguinte software instalado na sua máquina de desenvolvimento local:

- [Rio Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Deve completar os passos nos seguintes artigos antes de continuar:

- [Adicione uma página personalizada à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-page.md).
- [Adicione um serviço personalizado à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Adicionar uma grelha

Para adicionar uma grelha à UI web, é necessário adicionar os ficheiros de origem que definem a grelha e modificar alguns ficheiros existentes para sensibilizar a UI web para o novo componente.

### <a name="add-the-new-files-that-define-the-grid"></a>Adicione os novos ficheiros que definem a grelha

Para começar, a pasta **src/walkthrough/componentes/pages/pageWithGrid/exemploGrid** contém os ficheiros que definem uma grelha:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Copie a pasta **src/walkthrough/componentes/pages/pageWithGrid/exemploGrid** para a pasta **src/componentes/páginas/exemplo.**

### <a name="add-the-grid-to-the-page"></a>Adicione a grelha à página

Modificar o **src/componentes/páginas/exemplo/basicPage.container.js** da seguinte forma para importar as definições de serviço:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Modificar o **src/componentes/páginas/exemplo/basicPage.js** da seguinte forma para adicionar a grelha:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Modificar o **src/componentes/páginas/exemplo/basicPage.test.js** da seguinte forma para atualizar os testes:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Teste a grelha

Se a UI web ainda não estiver a funcionar localmente, executa o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior dirige a UI localmente em `http://localhost:3000/dashboard` . Navegue na página **Exemplo** para ver os dados do ecrã da grelha a partir do serviço.

## <a name="select-rows"></a>Selecione linhas

Existem duas opções para permitir que um utilizador selecione linhas na grelha:

### <a name="hard-select-rows"></a>Linhas duras selecionadas

Se um utilizador precisar de agir em várias linhas ao mesmo tempo, utilize caixas de verificação em linhas:

1. Ativar a seleção dura das linhas adicionando uma **caixa de verificaçãoColumn** às **colunasSDefs** fornecidos à grelha. **checkboxColumn** é definido em **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Para aceder aos itens selecionados, obtém uma referência à API da rede interna:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Fornecer botões de contexto à página quando uma linha na grelha for selecionada duramente:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Quando um botão de contexto é clicado, obtenha os itens selecionados para fazer o seu trabalho em:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Linhas de seleção suave

Se o utilizador apenas precisar de agir numa única linha, configurar um link de seleção suave para uma ou mais colunas nas **colunasDeefs**.

1. Em **exampleGridConfig.js**, adicione **SoftSelectLinkRenderer** como **o cellRendererFramework** para uma **colunaDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Quando um link de seleção suave é clicado, ativa o evento **onSoftSelectChange.** Execute qualquer ação desejada para essa linha, como abrir um flyout de detalhes. Este exemplo simplesmente escreve para a consola:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a adicionar ou personalizar páginas na UI web no acelerador de solução de Monitorização Remota.

Agora que definiu uma grelha, o próximo passo é [adicionar um flyout personalizado à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-flyout.md) que aparece na página de exemplo.

Para obter mais informações conceptuais sobre o acelerador de solução de monitorização remota, consulte [a arquitetura de monitorização remota.](iot-accelerators-remote-monitoring-sample-walkthrough.md)
