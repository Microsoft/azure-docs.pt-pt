---
title: Criar a aplicação Angular UI com API da Azure Cosmos DB para a MongoDB (Parte3)
description: Parte 3 da série do tutorial sobre como criar uma aplicação MongoDB com Angular e Node no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 181af5cf26d19a9f51e8d456e777badf7efa224d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097826"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---build-the-ui-with-angular"></a>Criar uma app angular com API da Azure Cosmos DB para a MongoDB - Construa a UI com a Angular
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este tutorial multi-partes demonstra como criar uma nova app escrita em Node.js com o Express e angular e, em seguida, conectá-la à sua [conta Cosmos configurada com a API da Cosmos DB para a MongoDB.](mongodb-introduction.md)

A Parte 3 do tutorial é a continuação da [Parte 2](tutorial-develop-mongodb-nodejs-part2.md) e abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar a IU do Angular
> * Utilizar CSS para definir o aspeto e a funcionalidade
> * Testar a aplicação localmente

## <a name="video-walkthrough"></a>Instruções de vídeo

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, certifique-se de que concluiu os passos na [Parte 2](tutorial-develop-mongodb-nodejs-part2.md) do tutorial.

> [!TIP]
> Este tutorial orienta-o ao longo dos passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="build-the-ui"></a>Criar a IU

1. No Código do Estúdio Visual, clique no botão Stop :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png"::: para parar a aplicação Nó. 

2. Na Janela da Linha de Comandos do Windows ou na janela de Terminal do Mac, introduza o comando seguinte para gerar um componente heroes. Neste código, g=gerar, c=componente, heroes=nome do componente e utiliza uma estrutura de ficheiros simples (--flat), para que seja criada uma subpasta para o mesmo.

    ```
    ng g c heroes --flat 
    ```

    A janela de terminal apresenta a confirmação dos componentes novos.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png" alt-text="Instalar componente do hero":::

    Vamos ver os ficheiros que foram criados e atualizados. 

3. No Visual Studio Code, no painel **Explorer** , navegue para a pasta **src\app** nova e abra o ficheiro **heroes.component.ts** novo, gerado na pasta da aplicação. Este ficheiro de componente TypeScript foi criado pelo comando anterior.

    > [!TIP]
    > Se não vir a pasta da aplicação no Visual Studio Code, introduza CMD + SHIFT P em Mac ou Ctrl + Shift + P no Windows para abrir a Paleta de Comandos e introduza *Reload Window* , para obter a alteração do sistema.

4. Na mesma pasta, abra o ficheiro **app.module.ts** e repare que este adicionou `HeroesComponent` à declaração na linha 5 e que também o importou na linha 10.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png" alt-text="Instalar componente do hero":::

5. Regresse ao ficheiro **heroes.component.html** e copie este código. O `<div>` é o contentor para a página inteira. Dentro do contentor, há uma lista de heroes que temos de criar para que, quando clicar num, pode selecioná-lo e editá-lo ou eliminá-lo na IU. Em seguida, no HTML, temos alguns estilos, para que saiba qual deles está selecionado. Há também uma área de edição, que lhe permite adicionar um hero novo ou editar um já existente. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. Agora que o HTML já está a postos, temos de adicioná-lo ao ficheiro **heroes.component.ts** , de modo a podermos interagir com o modelo. O código seguinte adiciona o modelo ao ficheiro de componente. Foi adicionado um construtor que obtém alguns heroes e inicializa o componente do serviço hero para adquirir todos os dados. Este código também adiciona todos os métodos necessários para processar eventos na IU. Pode copiar o código seguinte por cima do que já existe em **heroes.component.ts** . Espera-se que veja erros nas áreas do Hero e do HeroService, uma vez que os componentes correspondentes ainda não foram importados, mas irá corrigir estes erros na secção seguinte. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. No **Explorer** , abra o ficheiro **app/app.module.ts** e atualize a secção de importação para adicionar uma importação para um `FormsModule`. A secção de importação deve ter agora o seguinte aspeto:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. No ficheiro **app/app.module.ts** , adicione uma importação para o módulo FormsModule novo, na linha 3. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>Utilizar CSS para definir o aspeto e a funcionalidade

1. No painel do Explorer, abra o ficheiro **src/styles.scss** .

2. Copie o código seguinte para o ficheiro **styles.scss** , substituindo o conteúdo existente do mesmo.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Guarde o ficheiro. 

## <a name="display-the-component"></a>Apresentar o componente

Agora que temos o componente, como podemos proceder para que apareça no ecrã? Vamos modificar os componentes predefinidos em **app.component.ts** .

1. No painel do Explorer, abra **/app/app.component.ts** , altere o título para Heroes e ponha o nome do componente que criámos em **heroes.components.ts** (app-heroes), para referenciar esse componente novo. O conteúdo do ficheiro deve ter agora o seguinte aspeto: 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. Vamos referenciar outros componentes em **heroes.components.ts** , como o componente heroes, pelo que também temos de o criar. Na linha de comandos da IU do Angular, utilize o seguinte comando para criar um modelo de hero e um ficheiro denominado **hero.ts** , em que g = gerar cl = classe e hero =nome da classe.

    ```bash
    ng g cl hero
    ```

3. No painel do Explorer, abra **src\app\hero.ts** . Em **hero.ts** , substitua o conteúdo do ficheiro pelo código seguinte, que acrescenta uma classe Hero com um ID, um nome e uma indicação.

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. Regresse a **heroes.components.ts** e repare que, na linha `selectedHero: Hero;` (linha 10), `Hero` tem uma linha vermelha por baixo. 

5. Clique com o botão esquerdo do rato no termo `Hero` e o Visual Studio mostra um ícone de lâmpada no lado esquerdo do bloco de código. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png" alt-text="Instalar componente do hero":::

6. Clique na lâmpada e clique em **Importar Hero de "/app/hero.** ou em **Import Hero from "./hero"** (Importar Hero de "./hero"). (A mensagem é diferente consoante a sua configuração).

    É apresentada uma nova linha de código na linha 2. Se a linha 2 referenciar /app/hero, modifique-a de modo a que referencie o ficheiro hero da pasta local (./hero). A linha 2 deve ter o seguinte aspeto:

   ```
   import { Hero } from "./hero";
   ``` 

    O modelo fica assim terminado, mas ainda temos de criar o serviço.

## <a name="create-the-service"></a>Criar o Serviço

1. Na linha de comandos da IU do Angular, introduza o comando seguinte para criar um serviço hero em **app.module.ts** , em que g=gerar, s=serviço, hero=nome do serviço, -m=colocar em app.module.

    ```bash
    ng g s hero -m app.module
    ```

2. No Visual Studio Code, regresse a **heroes.components.ts** . Repare que, na linha `constructor(private heroService: HeroService) {}` (linha 13), `HeroService` tem uma linha vermelha por baixo. Clique em `HeroService` e obterá a lâmpada no lado esquerdo do bloco de código. Clique na lâmpada e, em seguida, clique em **Import HeroService from "./hero.service ".** (Importar HeroService de "./hero.service ".) ou em **(Importar HeroService de "/app/hero.service".**

    Clicar na lâmpada insere uma linha de código nova na linha 2. Se a linha 2 referenciar a pasta /app/herói.service, modifique-a de modo a que faça referência ao ficheiro herói da pasta local (./hero.service). A linha 2 deve ter o seguinte aspeto:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. No Visual Studio Code, abra **hero.service.ts** e copie o código seguinte, substituindo o conteúdo do ficheiro.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Este código utiliza a versão mais recente do HttpClient que o Angular oferece, que é um módulo que tem de ser fornecido, o que vamos fazer de seguida.

4. No Visual Studio Code, abra **app.module.ts** e importe HttpClientModule mediante a atualização da secção de importação, para que inclua HttpClientModule.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. Em **app.module.ts** , adicione a declaração de importação HttpClientModule à lista de importações.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Guarde todos os ficheiros no Visual Studio Code.

## <a name="build-the-app"></a>Criar a aplicação

1. Na linha de comandos, introduza o comando seguinte para criar a aplicação Angular. 

    ```bash
    ng b
    ``` 

    Em caso de problemas, a janela do terminal apresenta informações sobre os ficheiros que têm de ser corrigidos. Quando tiver concluído a compilação, os ficheiros novos são adicionados na pasta **dist** . Se quiser, pode rever os ficheiros novos na pasta **dist** .

    Agora vamos executar a aplicação.

2. No Código do Estúdio Visual, clique no botão **Debug** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: no lado esquerdo e, em seguida, clique no botão **Iniciar depuração** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png"::: .

3. Agora, abra um browser e navegue para **localhost:3000** e veja a aplicação a ser executada localmente.

     :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png" alt-text="Instalar componente do hero":::

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, fez o seguinte:

> [!div class="checklist"]
> * Criou a IU do Angular
> * Testou a aplicação localmente

Pode avançar para a parte seguinte do tutorial para criar uma conta do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Create an Azure Cosmos DB account using the Azure CLI](tutorial-develop-mongodb-nodejs-part4.md) (Criar uma conta do Azure Cosmos DB com a CLI do Azure)
