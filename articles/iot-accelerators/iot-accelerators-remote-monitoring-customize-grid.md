---
title: Adicionar uma grade para a solução de monitorização remota da interface do Usuário - Azure | Documentos da Microsoft
description: Este artigo mostra-lhe como adicionar um novo gid numa página da web de acelerador de solução da interface do Usuário da monitorização remota.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/04/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447102"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicionar uma grade personalizada para monitorização remota solution accelerator da IU da web

Este artigo mostra-lhe como adicionar uma nova grade numa página web de acelerador de solução da interface do Usuário da monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar uma nova grade a uma página na IU da web.

A grade de exemplo neste artigo apresenta os dados do serviço que o [adicionar um serviço personalizado para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-service.md) procedimento artigo mostra-lhe como adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos, terá o seguinte software instalado no seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Antes de continuar, deve de concluir os passos nos seguintes artigos:

- [Adicionar uma página personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-page.md).
- [Adicionar um serviço personalizado para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Adicionar uma grelha

Para adicionar uma grade para a IU da web, terá de adicionar os ficheiros de origem que definem a grade e modificar alguns ficheiros existentes para tornar a IU da web em consideração o novo componente.

### <a name="add-the-new-files-that-define-the-grid"></a>Adicionar os novos ficheiros que definem a grade

Para começar, o **passo a passo/src/componentes/pageWithGrid/páginas/exampleGrid** pasta contém os ficheiros que definem uma grade:

**exampleGrid.js**



**exampleGridConfig.js**



Copiar o **passo a passo/src/componentes/pageWithGrid/páginas/exampleGrid** pasta para o **src/componentes/páginas/exemplo** pasta.

### <a name="add-the-grid-to-the-page"></a>Adicionar a grade à página

Modificar a **src/components/pages/example/basicPage.container.js** da seguinte forma para importar as definições de serviço:

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

Modificar a **src/components/pages/example/basicPage.js** da seguinte forma para adicionar a grade:

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

Modificar a **src/components/pages/example/basicPage.test.js** da seguinte forma para atualizar os testes:

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

## <a name="test-the-grid"></a>Testar a grade

Se o web interface do Usuário não está já em execução localmente, execute o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior é a interface do Usuário localmente em executado [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Navegue para o **exemplo** página para ver a grade exibir dados do serviço.

## <a name="select-rows"></a>Selecionar as linhas

Existem duas opções para habilitar um usuário para selecionar as linhas da grade:

### <a name="hard-select-rows"></a>Linhas de disco rígido seleção

Se um usuário precisa tomar decisões sobre várias linhas ao mesmo tempo, utilize as caixas de verificação nas linhas:

1. Ative o disco rígido-seleção de linhas ao adicionar um **checkboxColumn** para o **columnDefs** fornecido à grade. **checkboxColumn** está definido no **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Para acessar os itens selecionados, obtém uma referência para a API de grade interno:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Fornece botões de contexto para a página, quando uma linha na grelha é difícil selecionado:

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

1. Quando um botão de contexto é clicado, obtém os itens de disco rígido selecionado para fazer seu trabalho em:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Linhas de seleção de forma recuperável

Se o utilizador necessita apenas de agir numa única linha, configurar uma ligação de forma recuperável-selecione um ou mais colunas na **columnDefs**.

1. Na **exampleGridConfig.js**, adicione **SoftSelectLinkRenderer** como o **cellRendererFramework** para um **columnDef**.

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

1. Quando se clica numa ligação de seleção de forma recuperável, aciona o **onSoftSelectChange** eventos. Execute a ação que for o pretendido para essa linha, como abrir um submenu de detalhes. Este exemplo simplesmente escreve para a consola:

    ```js
    onSoftSelectChange = (rowId, rowEvent) => {
      const { onSoftSelectChange } = this.props;
      const obj = (this.gridApi.getDisplayedRowAtIndex(rowId) || {}).data;
      if (obj) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', obj);
        this.setState({ softSelectedObj: obj });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(obj, rowEvent);
      }
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para o ajudar a adicionar ou personalizar páginas na IU da web no solution accelerator monitorização remota.

Agora que definiu uma grade, a próxima etapa é [adicionar uma lista de opções personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-flyout.md) que apresenta a página de exemplo.

Para obter mais informações concetuais sobre o acelerador de solução de monitorização remota, consulte [arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md).
