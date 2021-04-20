---
title: Implementar widgets no portal do desenvolvedor
titleSuffix: Azure API Management
description: Saiba como implementar widgets que consomem dados de APIs externos e os exiba no portal de desenvolvimento da API Management.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 4bda136a1abe8f9ffb443973731ebbe13b56ac3b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741854"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Implementar widgets no portal do desenvolvedor

Neste tutorial, implementa-se um widget que consome dados a partir de uma API externa e exibe-os no portal de desenvolvimento da API Management.

O widget irá obter descrições da sessão da amostra [API da Conferência](https://conferenceapi.azurewebsites.net/?format=json). O identificador da sessão será definido através de um editor de widget designado.

Para o ajudar no processo de desenvolvimento, consulte o widget completo localizado na `examples` pasta do [repositório](https://github.com/Azure/api-management-developer-portal/)do portal de desenvolvimento da API Management GitHub: `/examples/widgets/conference-session` .

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="Screenshot do widget publicado":::

## <a name="prerequisites"></a>Pré-requisitos

* Crie um [ambiente local](developer-portal-self-host.md#step-1-set-up-local-environment) para o mais recente lançamento do portal de desenvolvedores.

* Deve entender a [anatomia do widget Paperbits.](https://paperbits.io/wiki/widget-anatomy)


## <a name="copy-the-scaffold"></a>Copiar o andaime

Utilize um `widget` andaime da pasta como ponto de partida para construir o novo `/scaffolds` widget.

1. Copie a pasta `/scaffolds/widget` para `/community/widgets` .
1. Mude o nome da pasta para `conference-session` .

## <a name="rename-exported-module-classes"></a>Renomear classes de módulos exportados

Rebatize as classes de módulos exportados substituindo o `Widget` prefixo `ConferenceSession` por nestes ficheiros:

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
Por exemplo, no `widget.design.module.ts` ficheiro, altere `WidgetDesignModule` `ConferenceSessionDesignModule` para:
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
para 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>Registar o widget

Registe os módulos do widget nos módulos de raiz do portal adicionando as seguintes linhas nos respetivos ficheiros:

1. `src/apim.design.module.ts` - um módulo que regista dependências do tempo de conceção.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` - um módulo que registe dependências de tempo de publicação.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` - dependências de tempo de execução.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>Coloque o widget no portal

Agora está pronto para ligar o andaime duplicado e usá-lo no portal do desenvolvedor.

1. Execute o comando `npm start`.

1. Quando a aplicação estiver carregada, coloque o novo widget numa página. Pode encontrá-lo com o nome `Your widget` na categoria no seletor `Community` widget.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Screenshot do seletor de widget":::

1. Guarde a página premindo **Ctrl** + **S** (ou **⌘** + **S** no macOS).

    > [!NOTE]
    > Em tempo de conceção, ainda pode interagir com o site segurando a tecla **Ctrl** (ou **⌘).**

## <a name="add-custom-properties"></a>Adicionar propriedades personalizadas

Para que o widget procure descrições da sessão, ele precisa estar ciente do identificador da sessão. Adicione o `Session ID` imóvel às respetivas interfaces e classes:

Para que o widget consiga a descrição da sessão, tem de estar atento ao identificador da sessão. Adicione a propriedade de ID de sessão às respetivas interfaces e classes:

1. `widgetContract.ts` - contrato de dados (camada de dados) que defina como a configuração do widget é persistiu.

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` - modelo (camada de negócio) - uma representação primária do widget no sistema. É atualizado pelos editores e renderizado pela camada de apresentação.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` - viewmodel (camada de apresentação) - um objeto específico da UI que o portal do desenvolvedor presta com o modelo HTML.

    > [!NOTE]
    > Não precisas de mudar nada neste ficheiro.

## <a name="configure-binders"></a>Ligadores de configuração

Ativar o fluxo `sessionNumber` da fonte de dados para a apresentação do widget. Editar as `ModelBinder` `ViewModelBinder` entidades e entidades:

1. `widgetModelBinder.ts` ajuda a preparar o modelo usando dados descritos no contrato.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` sabe como o portal de desenvolvedores precisa apresentar o modelo (como um modelo de visualização) num quadro específico da UI.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Ajuste o modelo de widget tempo de design

Os componentes de cada âmbito são executados de forma independente. Têm recipientes de injeção de dependência separados, a sua própria configuração, ciclo de vida, etc. Podem até ser alimentados por diferentes quadros de UI (neste exemplo é Knockout JS).

Do ponto de vista do tempo de conceção, qualquer componente de tempo de execução é apenas uma etiqueta HTML com determinados atributos e/ou conteúdo. A configuração, se necessário, é passada com uma marcação simples. Em casos simples, como neste exemplo, o parâmetro é passado no atributo. Se a configuração for mais complexa, poderá utilizar um identificador da configuração(s) necessária, recolhida por um fornecedor de configuração designado (por exemplo, `ISettingsProvider` ).

1. Atualizar o `ko/widgetView.html` ficheiro:

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    Quando o portal do desenvolvedor executa a `attr` ligação em *tempo de conceção* ou tempo de *publicação,* o HTML resultante é:

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    Em seguida, em tempo de execução, `widget-runtime` o componente irá `sessionNumber` lê-lo e usá-lo no código de inicialização (ver abaixo).

1. Atualizar o `widgetHandlers.ts` ficheiro para atribuir o ID da sessão na criação:

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>Rever modelo de visualização de runtime

Os componentes de tempo de execução são o código em execução no próprio website. Por exemplo, no portal de desenvolvimento da API Management, são todos os scripts por trás de componentes dinâmicos (por exemplo, *detalhes da API,* *consola API),* operações de manuseamento como geração de amostras de código, pedidos de envio, etc.

O modelo de visão do seu componente de tempo de execução precisa de ter os seguintes métodos e propriedades:

- A `sessionNumber` propriedade (marcada com `Param` decorador) utilizada como parâmetro de entrada de componente passado do exterior (a marcação gerada em tempo de conceção; ver o passo anterior).
- A `sessionDescription` propriedade ligada ao modelo de widget (ver mais tarde neste `widget-runtime.html` artigo).
- O `initialize` método (com `OnMounted` decorador) invocado após a criação do widget e todos os seus parâmetros são atribuídos. É um bom lugar para ler `sessionNumber` e invocar a API usando o `HttpClient` . `HttpClient`Trata-se de uma dependência injetada pelo recipiente IoC (Inversão de Controlo).

- Em primeiro lugar, o portal do desenvolvedor cria o widget e atribui todos os seus parâmetros. Depois invoca o `initialize` método.

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>Ajuste o modelo de widget

Atualize o seu widget para mostrar a descrição da sessão.

Utilize uma etiqueta de parágrafo e uma `markdown` `text` (ou) ligação no `ko/runtime/widget-runtime.html` ficheiro para tornar a descrição:

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>Adicione o editor de widget

O widget está agora configurado para obter a descrição da sessão `107` . Especificou `107` no código como a sessão predefinida. Para verificar se fez tudo certo, corra `npm start` e confirme que o portal do desenvolvedor mostra a descrição na página.

Agora, realize estas etapas para permitir ao utilizador configurar o ID da sessão através de um editor de widget:

1. Atualizar o `ko/widgetEditorViewModel.ts` ficheiro:

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    O modelo de visualização do editor usa a mesma abordagem que já viu anteriormente, mas há uma nova `onChange` propriedade, decorada `@Event()` com. Liga a chamada para notificar os ouvintes (neste caso - um editor de conteúdo) das alterações ao modelo.

1. Atualizar o `ko/widgetEditorView.html` ficheiro:

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. Execute novamente `npm start`. Deves poder mudar `sessionNumber` no editor de widgets. Altere o ID `108` para, guarde as alterações e refresque o separador do navegador. Se estiver com problemas, poderá ter de voltar a adicionar o widget à página.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Screenshot do editor de widget":::

## <a name="rename-the-widget"></a>Mude o nome do widget

Alterar o nome do widget no `constants.ts` ficheiro:

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Se estiver a contribuir com o widget para o repositório, `widgetName` o caso tem de ser o mesmo que o seu nome de pasta e precisa de ser derivado do nome do visor (minúscula e espaços substituídos por traços). A categoria deve `Community` permanecer.

## <a name="next-steps"></a>Passos seguintes


Saiba mais sobre o portal de desenvolvedores:

- [Descrição geral do portal do programador da Gestão de API do Azure](api-management-howto-developer-portal.md)

- [Contribua com widgets](developer-portal-widget-contribution-guidelines.md) - congratulamo-nos e encorajamos as contribuições da comunidade.

- Consulte [widgets comunitários](developer-portal-use-community-widgets.md) para aprender a usar widgets contribuídos pela comunidade.
