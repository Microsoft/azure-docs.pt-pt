---
title: Adicione funções CRUD a uma aplicação angular com API da Azure Cosmos DB para a MongoDB
description: Parte 6 da série do tutorial sobre como criar uma aplicação MongoDB com Angular e Node no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: c8e2c707566b08219b495e76be7f6f6130d876ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081319"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---add-crud-functions-to-the-app"></a>Criar uma aplicação angular com API da Azure Cosmos DB para MongoDB - Adicionar funções CRUD à app
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este tutorial multi-partes demonstra como criar uma nova app escrita em Node.js com o Express e angular e, em seguida, conectá-la à sua [conta Cosmos configurada com a API da Cosmos DB para a MongoDB.](mongodb-introduction.md) A Parte 6 do tutorial é a continuação da [Parte 5](tutorial-develop-mongodb-nodejs-part5.md) e abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar as funções Post, Put e Delete para o serviço hero
> * Executar a aplicação

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, certifique-se de que concluiu os passos na [Parte 5](tutorial-develop-mongodb-nodejs-part5.md) do tutorial.

> [!TIP]
> Este tutorial orienta-o ao longo dos passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="add-a-post-function-to-the-hero-service"></a>Adicionar uma função Post ao serviço hero

1. No Código do Estúdio Visual, abra **routes.js** e **hero.service.js** lado a lado premindo o botão **Split Editor** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png"::: .

    Veja se a linha 7 de routes.js chama a função `getHeroes` na linha 5 de **hero.service.js**.  Temos de criar este emparelhamento para as funções post, put e delete. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png" alt-text="routes.js e hero.service.js no Visual Studio Code":::
    
    Vamos começar por programar o serviço hero. 

2. Copie o código seguinte para **hero.service.js** a seguir à função `getHeroes` e antes de `module.exports`. Este código:  
   * Utiliza o modelo de hero para publicar um hero novo.
   * Verifica as respostas para ver se existe um erro e devolve o valor de estado de 500.

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. Em **hero.service.js**, atualize o `module.exports` para incluir a função `postHero` nova. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. Em **routes.js**, adicione um router para a função `post` a seguir ao router `get`. Este router publica um hero de cada vez. Estruturar o ficheiro do router desta forma mostra claramente todos os pontos finais da API disponíveis e deixa o trabalho real a cargo do ficheiro **hero.service.js**.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Execute a aplicação para confirmar que está tudo a funcionar. No Código do Estúdio Visual, guarde todas as alterações, selecione o botão **Debug** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png"::: no lado esquerdo e, em seguida, selecione o botão Iniciar de **Depuração** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png"::: .

6. Agora, regresse ao browser e prima F12, na maioria dos computadores, para abrir o separador Ferramentas do programador e Rede. Navegue para `http://localhost:3000` para ver as chamadas efetuadas através da rede.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png" alt-text="Separador Redes no Chrome que mostra a atividade de rede":::

7. Selecione o botão **Adicionar Hero Novo** para adicionar um hero novo. Introduza o ID “999”, o nome “Pedro” e a indicação “Olá” e, em seguida, selecione **Guardar**. Deverá ver, no separador Rede, que enviou um pedido POST para um hero novo. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png" alt-text="Separador Rede no Chrome que mostra a atividade de rede para as funções Get e Post":::

    Agora, vamos voltar atrás e adicionar as funções Put e Delete à aplicação.

## <a name="add-the-put-and-delete-functions"></a>Adicionar as funções Put e Delete

1. Em **routes.js**, adicione os routers `put` e `delete` a seguir ao router post.

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Copie o código seguinte para **hero.service.js** a seguir à função `checkServerError`. Este código:
   * Cria as funções `put` e `delete`
   * Verifica se o hero foi encontrado
   * Procede ao processamento de erros 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. Em **hero.service.js**, exporte os módulos novos:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Agora que atualizámos o código, selecione o botão **Reiniciar** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png"::: no Código do Estúdio Visual.

5. Atualize a página do browser e selecione o botão **Adicionar novo hero**. Adicione um hero novo com o ID "9", o nome "Starlord" e a indicação "Hi". Selecione o botão **Guardar** para guardar o hero novo.

6. Agora, selecione o hero **Starlord**, mude a indicação de “Olá” para “Tchau” e, em seguida, selecione o botão **Guardar**. 

    Agora pode selecionar o ID no separador Rede para mostrar o payload. Pode ver, no payload, que a indicação está agora definida como “Bye”.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png" alt-text="Aplicação heroes e separador Rede que mostra o payload"::: 

    Também pode eliminar um dos heroes na IU e ver quanto tempo é que a operação delete demora a ser concluída. Experimente ao selecionar o botão “Eliminar” no hero com o nome “Pedro".

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/times.png" alt-text="Aplicação heroes e separador Rede que mostra o tempo que demora a concluir as funções"::: 

    Se atualizar a página, o separador Rede mostra o tempo que demora a obter os heroes. Embora estes tempos sejam rápidos, muito está dependente de onde os seus dados estão localizados no mundo e da sua capacidade de georreplicá-los numa zona perto dos seus utilizadores. Pode encontrar mais informações sobre a georreplicação no tutorial seguinte, que vai ser lançado em breve.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, fez o seguinte:

> [!div class="checklist"]
> * Adicionou as funções Post, Put e Delete à aplicação 

Regresse aqui para ver vídeos adicionais nesta série de tutoriais.

