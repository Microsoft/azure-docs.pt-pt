---
title: Como implantar um bot de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como implantar um bot de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 05fd83506aac26df33f18bec83dcadac8dee2d90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705273"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Como implantar um bot de Conversation Learner

Este documento explica como implantar um bot de Conversation Learner – seja localmente ou no Azure.

## <a name="prerequisite-determine-the-model-id"></a>Pré-requisito: determinar a ID do modelo 

Para executar um bot fora da interface do usuário do Conversation Learner, você deve definir a ID do modelo de Conversation Learner que o bot usará, ou seja, a ID do modelo de Machine Learning na nuvem do Conversation Learner.  (Por outro lado, ao executar o bot por meio da interface do usuário do Conversation Learner, a interface do usuário escolhe qual ID do modelo.).  

Veja como obter a ID do modelo:

1. Inicie o bot e a interface do usuário do Conversation Learner.  Consulte o guia de início rápido para obter instruções completas; para resumir:

    Em uma janela de comando:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    Na janela de comando do outro

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Abra o navegador para`http://localhost:5050` 

3. Clique no modelo de Conversation Learner para o qual você deseja obter a ID

4. Clique em "configurações" na barra de navegação à esquerda.

5. O GUID "ID do modelo" é exibido próximo à parte superior da página.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Opção 1: Implantando um bot de Conversation Learner para ser executado localmente

Isso implanta um bot em seu computador local e mostra como você pode acessá-lo usando o emulador do bot Framework.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Configurar o bot para acesso fora da interface do usuário do Conversation Learner

Ao executar um bot localmente, adicione a ID do aplicativo ao arquivo do `.env` bot:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Em seguida, inicie o bot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

O bot agora está em execução localmente.  Você pode acessá-lo com o emulador do bot Framework.

### <a name="download-and-install-the-emulator"></a>Baixar e instalar o emulador

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Conectar o emulador ao bot

1. No canto superior esquerdo do emulador, na caixa "Insira a URL do ponto de extremidade" `http://127.0.0.1:3978/api/messages`, digite.  Deixe os outros campos em branco e clique em "conectar".

2. Agora você está convertida com o bot.

## <a name="option-2-deploy-to-azure"></a>Opção 2: Implementar no Azure

Publique seu bot de Conversation Learner semelhante ao mesmo modo que você publicaria qualquer outro bot. Em um nível alto, você carrega seu código em um site hospedado, define os valores de configuração apropriados e, em seguida, registra o bot com vários canais. Instruções detalhadas estão neste vídeo mostrando como publicar o bot usando o serviço de bot do Azure.

Depois que o bot for implantado e executado, você poderá conectar diferentes canais a ele, como Facebook, Teams, Skype etc. usando um registro de canal do Azure bot. Para obter a documentação sobre esse processo, consulte: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Veja abaixo as instruções passo a passo para implantar um bot de Conversation Learner no Azure.  Essas instruções pressupõem que sua fonte de bot esteja disponível de uma fonte baseada em nuvem, como Azure DevOps Services, GitHub, BitBucket ou OneDrive, e configurará seu bot para implantação contínua.

1. Faça logon no portal do Azure em https://portal.azure.com

2. Criar um novo recurso "bot do aplicativo Web" 

    1. Dê um nome ao bot
    2. Clique em "modelo de bot", escolha "node. js", escolha "básico" e clique no botão "selecionar"
    3. Clique em "criar" para criar o bot do aplicativo Web.
    4. Aguarde até que o recurso de bot do aplicativo Web seja criado.

3. No portal do Azure, edite o recurso de bot do aplicativo Web que você acabou de criar.

   1. Clique no item de navegação "configurações do aplicativo" à esquerda
   1. Role para baixo até a seção "configurações do aplicativo"
   2. Adicione estas configurações:

       Variável de ambiente | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | GUID de ID do aplicativo, obtido na interface do usuário do Conversation Learner nas "configurações" do modelo >
       LUIS_AUTHORING_KEY               | Chave de criação de LUIS para este modelo
       LUIS_SUBSCRIPTION_KEY            | Não obrigatório, mas recomendado para bots publicados para evitar o uso de sua cota de criação.
    
   4. Clique em "salvar" próximo à parte superior da página
   5. Abra o item de navegação "Build" à esquerda
   6. Clique em "configurar a implantação contínua" 
   7. Clique no ícone de "instalação" em implantações
   8. Clique em "configurações necessárias"
   9. Selecione a origem onde o código de bot está disponível e configure a origem.
