---
title: Como criar um modelo de Conversation Learner usando node. js-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um modelo de Conversation Learner usando o Node. js.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7ab32fb421a2c0db72652d1bbf12d312bffd5d1e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706542"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Criar um modelo de Conversation Learner usando node. js

Conversation Learner reduz a complexidade da criação de bots. Ele permite um fluxo de trabalho de desenvolvimento híbrido, permitindo que o código escrito à mão e o aprendizado de máquina reduzam a quantidade de código necessária para escrever bots. Determinadas partes fixas de seu modelo, como verificar se o usuário está conectado ou fazer uma solicitação de API para verificar o inventário de armazenamento, ainda podem ser codificadas. No entanto, outras alterações na seleção de estado e ação podem ser aprendidas por meio de caixas de diálogo de exemplo fornecidas pelo desenvolvedor ou especialista em domínio.

## <a name="invitation-required"></a>Convite necessário

*É necessário um convite para acessar o Conversation Learner do projeto.*

O Conversation Learner de projeto consiste em um SDK que você adiciona ao bot e um serviço de nuvem que o SDK acessa para o Machine Learning.  No momento, o acesso ao serviço de nuvem mais enxuto de conversa do projeto requer um convite.  Se você ainda não tiver convidado, [solicite um convite](https://aka.ms/conversation-learner-request-invite).  Se você não recebeu um convite, não poderá acessar a API de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

- Nó 8.5.0 ou superior e NPM 5.3.0 ou superior. Instalar do [https://nodejs.org](https://nodejs.org).
  
- Chave de criação do LUIS:

  1. Faça logon [https://www.luis.ai](https://www.luis.ai)no.

  2. Clique em seu nome no canto superior direito e, em seguida, em "configurações"

  3. A chave de criação é mostrada na página resultante

  (Sua chave de criação do LUIS serve 2 funções.  Primeiro, ele servirá como sua chave de criação de Conversation Learner.  Em segundo lugar, Conversation Learner usa LUIS para extração de entidade; a chave de criação LUIS é usada para criar modelos de LUIS em seu nome)

- Navegador da Web do Google Chrome. Instalar do [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html).

- git. Instalar do [https://git-scm.com/downloads](https://git-scm.com/downloads).

- VSCode. Instalar do [https://code.visualstudio.com/](https://code.visualstudio.com/). Observe que isso é recomendado, não obrigatório.

## <a name="quick-start"></a>Início rápido 

1. Instalar e compilar:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Durante `npm install`o, você pode ignorar esse erro se ocorrer:`gyp ERR! stack Error: Can't find Python executable`

2. Configure:

   Crie um arquivo chamado `.env` no diretório. `cl-bot-01`  O conteúdo do arquivo deve ser:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Iniciar bot:

    ```
    npm start
    ```

    Isso executa o bot genérico vazio no `cl-bot-01/src/app.ts`.

3. Abra o navegador para`http://localhost:3978`

Agora você está usando Conversation Learner e pode criar e ensinar um modelo de Conversation Learner.  

> [!NOTE]
> Na inicialização, o projeto Conversation Learner está disponível por convite.  Se `http://localhost:3978/ui` o mostrar um `403` erro http, isso significa que sua conta não foi convidada.  [Solicite um convite](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>TUTORIAIS, demonstrações e alternância entre bots

As instruções acima iniciaram o bot vazio genérico.  Para executar um tutorial ou bot de demonstração, em vez disso:

1. Se você tiver a interface do usuário da Web do Conversation Learner aberta, retorne à `http://localhost:3978/ui/home`lista de modelos em.
    
2. Se outro bot estiver em execução ( `npm start` como `npm run demo-pizza`ou), interrompa-o.  Você não precisa interromper o processo de interface do usuário ou fechar o navegador da Web.

3. Execute um bot de demonstração na linha de comando (etapa 2 acima).  As demonstrações incluem:

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

4. Se você ainda não tiver feito isso, alterne para a interface do usuário da Web `http://localhost:3978/ui/home`do Conversation Learner no Chrome carregando. 

5. Clique em "importar tutoriais" e selecione o modelo de demonstração na interface do usuário do Conversation Learner que corresponde à demonstração iniciada.

Os arquivos de origem das demonstrações estão em`cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Criar um bot que inclua código de back-end

1. Se você tiver a interface do usuário da Web do Conversation Learner aberta, retorne à `http://localhost:3978/ui/home`lista de modelos em.
    
2. Se um bot estiver em execução ( `npm run demo-pizza`como), pare-o.  Você não precisa interromper o processo de interface do usuário ou fechar o navegador da Web.

3. Se desejar, edite o `cl-bot-01/src/app.ts`código em.

4. Recriar e reiniciar o bot:

    ```bash    
    npm run build
    npm start
    ```

5. Se você ainda não tiver feito isso, alterne para a interface do usuário da Web `http://localhost:3978/ui/home`do Conversation Learner no Chrome carregando. 

6. Crie um novo modelo de Conversation Learner na interface do usuário e comece a ensinar.

7. Para fazer alterações de código `cl-bot-01/src/app.ts`no, repita as etapas acima, começando na etapa 2.

## <a name="vscode"></a>VSCode

No VSCode, há configurações de execução para cada demonstração e para o "bot vazio" no `cl-bot-01/src/app.ts`.  Abra a `cl-bot-01` pasta em VSCode.

## <a name="advanced-configuration"></a>Configuração avançada

Há um arquivo de `.env.example` modelo que mostra quais variáveis de ambiente você pode definir para configurar os exemplos.

Você pode ajustar essas portas para evitar conflitos entre outros serviços em execução em seu computador adicionando um `.env` arquivo à raiz do projeto:

```bash
cp .env.example .env
```

Isso usa a configuração padrão, que permite executar o bot localmente e começar a usar o Conversation Learner.  (Posteriormente, para implantar o bot no bot Framework, algumas edições nesse arquivo serão necessárias.)

## <a name="support"></a>Suporte

- Marque as perguntas em [Stack Overflow](https://stackoverflow.com) com "Microsoft cognitiva"
- Solicitar um recurso em nossa [página de voz do usuário](https://aka.ms/conversation-learner-uservoice)
- Abrir um problema em nosso [repositório GitHub](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Que contribuem

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
