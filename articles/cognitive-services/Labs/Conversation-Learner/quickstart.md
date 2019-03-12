---
title: Como criar um modelo de aprendiz de conversação com node. js - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como criar um modelo de aprendiz de conversação com node. js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 82738d6d97dc2c14c83ed483c4a1d76604d0cd91
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545338"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Criar um modelo de aprendiz de conversação com node. js

Aprendiz de conversação reduz a complexidade da criação de bots. Permite que um híbrido desenvolvimento fluxo de trabalho que permite que o código escrito à mão e aprendizagem automática reduzir a quantidade de código necessário para escrever bots. Determinadas partes fixas do seu modelo, como a verificar se o utilizador inicia sessão ou a efetuar um pedido de API para verificar o inventário da loja, ainda podem ser codificadas. No entanto, outras alterações no Estado e a ação de seleção podem ser adquiridas a partir de caixas de diálogo de exemplo fornecidas pelo especialista de domínio ou o desenvolvedor.

## <a name="invitation-required"></a>Convite necessário

*Um convite é necessário para aceder ao aprendiz de conversação do projeto.*

Aprendiz de conversação do projeto é constituído por um SDK que adiciona ao seu bot e um serviço cloud que o SDK acessa para machine learning.  No momento, o acesso ao serviço de nuvem do projeto conversação Leaner requer um convite.  Se ainda não foi convidado já, [pedir um convite](https://aka.ms/conversation-learner-request-invite).  Se não tenha recebido um convite, não será possível acessar a API da cloud.

## <a name="prerequisites"></a>Pré-requisitos

- Nó 8.5.0 ou superior e o npm 5.3.0 ou superior. Instalar a partir [ https://nodejs.org ](https://nodejs.org).
  
- Chave de criação de LUIS:

  1. Inicie sessão no [ https://www.luis.ai ](https://www.luis.ai).

  2. Clique em seu nome no canto superior direito, em seguida, em "definições"

  3. Chave de criação é mostrada na página resultante

  (A sua chave de criação de LUIS serve 2 funções.  Em primeiro lugar, ele irá servir como a chave de criação de aprendiz de conversação.  Segundo, o aprendiz de conversação usa o LUIS para extração de entidades; o LUIS criação de chave é utilizado para criar modelos de LUIS em seu nome)

- Browser Google Chrome. Instalar a partir [ https://www.google.com/chrome/index.html ](https://www.google.com/chrome/index.html).

- git. Instalar a partir [ https://git-scm.com/downloads ](https://git-scm.com/downloads).

- VSCode. Instalar a partir [ https://code.visualstudio.com/ ](https://code.visualstudio.com/). Tenha em atenção de que isso é recomendado, não é necessário.

## <a name="quick-start"></a>Início rápido 

1. Instalar e criar:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Durante a `npm install`, pode ignorar este erro se ocorrer: `gyp ERR! stack Error: Can't find Python executable`

2. Configure:

   Crie um ficheiro chamado `.env` no diretório `cl-bot-01`.  O conteúdo do ficheiro deve ser:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Bot de início:

    ```
    npm start
    ```

    Isso é executado o bot vazio genérico em `cl-bot-01/src/app.ts`.

3. Browser aberto para http://localhost:3978

Agora está a utilizar o aprendiz de conversação e pode criar e ensinar um modelo de aprendiz de conversação.  

> [!NOTE]
> No início, o aprendiz de conversação de projeto está disponível por convite.  Se http://localhost:3978/ui mostra um HTTP `403` erro, isso significa que sua conta não foi convidada.  Tente [pedir um convite](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Tutoriais, demonstrações e alternar entre bots

As instruções acima a utilizar o bot vazio genérico.  Para executar um tutorial ou em vez disso, o bot de demonstração:

1. Se tiver web Aprendiz de conversação, abra a interface do Usuário, regressar à lista de modelos em http://localhost:3978/ui/home.
    
2. Se estiver a executar outra bot (como `npm start` ou `npm run demo-pizza`), impedi-lo.  Não é necessário parar o processo de interface do Usuário ou fechar o navegador da web.

3. Execute um bot de demonstração da linha de comando (etapa 2 acima).  Demonstrações incluem:

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run tutorial-hybrid
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  ```

4. Se não estiver já, mude para a web de aprendiz de conversação da interface do Usuário no Chrome carregando http://localhost:3978/ui/home. 

5. Clique em "Tutoriais de importação" e selecione o modelo de demonstração na IU de aprendiz de conversação que corresponde à demonstração iniciou.

Ficheiros de origem para as demonstrações estão em `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Criar um bot, que inclui o código de back-end

1. Se tiver web Aprendiz de conversação, abra a interface do Usuário, regressar à lista de modelos em http://localhost:3978/ui/home.
    
2. Se estiver a executar um bot (como `npm run demo-pizza`), impedi-lo.  Não é necessário parar o processo de interface do Usuário ou fechar o navegador da web.

3. Se assim o desejar, editar o código na `cl-bot-01/src/app.ts`.

4. Reconstruir e voltar a iniciar bot:

    ```bash    
    npm run build
    npm start
    ```

5. Se não estiver já, mude para a web de aprendiz de conversação da interface do Usuário no Chrome carregando http://localhost:3978/ui/home. 

6. Criar um novo modelo de aprendiz de conversação na interface de Usuário e iniciar o ensino.

7. Para fazer alterações de código no `cl-bot-01/src/app.ts`, repita os passos acima, a partir do passo 2.

## <a name="vscode"></a>VSCode

No VSCode, lá são executadas configurações para cada demonstração e para o bot"vazio" no `cl-bot-01/src/app.ts`.  Abra o `cl-bot-01` pasta no VSCode.

## <a name="advanced-configuration"></a>Configuração avançada

Há um modelo `.env.example` ficheiro mostra qual o ambiente pode ser definido para configurar os exemplos de variáveis.

Pode ajustar estas portas para evitar conflitos entre outros serviços em execução no seu computador, adicionando um `.env` ficheiro para a raiz do projeto:

```bash
cp .env.example .env
```

Esta opção utiliza a configuração padrão, o que permite-lhe executar localmente o seu bot e começar a utilizar o aprendiz de conversação.  (Mais tarde no, para implementar o seu bot ao Bot Framework, algumas edições para este ficheiro serão necessária.)

## <a name="support"></a>Suporte

- Marque as perguntas [Stack Overflow](https://stackoverflow.com) com "cognitivos da microsoft"
- Pedir uma funcionalidade no nosso [página de voz do utilizador](https://aka.ms/conversation-learner-uservoice)
- Abra um problema no nosso [repositório do GitHub](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Contribuir

Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Para mais informações, consulte o [FAQ do Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contacte [opencode@microsoft.com](mailto:opencode@microsoft.com) com quaisuqer questões ou comentários adicionais.

## <a name="source-repositories"></a>Repositórios de origem

- [conversationlearner-samples](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Olá, mundo](./tutorials/01-hello-world.md)
