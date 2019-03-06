---
title: Como implementar um bot de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como implementar um bot de aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bc04afead8742c9f384287ecb8d33c54770456b6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447392"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Como implementar um bot de aprendiz de conversação

Este documento explica como implementar um bot de aprendiz de conversação – localmente ou no Azure.

## <a name="prerequisite-determine-the-model-id"></a>Pré-requisito: determinar o ID de modelo 

Para executar um bot fora da interface do Usuário de aprendiz de conversação, tem de definir o aprendiz de conversação ID de modelo que irá utilizar o bot – ou seja, o ID do modelo de aprendizagem automática, na cloud Aprendiz de conversação.  (Por outro lado, ao executar o bot através da IU de aprendiz de conversação, a interface do Usuário escolhe o ID de modelo.).  

Eis como obter o ID de modelo:

1. Inicie o seu bot e a interface do Usuário de aprendiz de conversação.  Consulte o guia de início rápido para obter instruções completas; para resumir:

    Na janela de comando:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    Na janela de comando noutro

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Browser aberto para http://localhost:5050 

3. Clique em que pretende obter o ID para o modelo de aprendiz de conversação

4. Clique em "Definições" na barra de navegação à esquerda.

5. O GUID de "ID de modelo" é apresentado junto à parte superior da página.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Opção 1: Implementar um bot de aprendiz de conversação para executar localmente

Isto implementa um bot de seu computador local e mostra como pode acessá-lo a utilizar o emulador do Bot Framework.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Configurar o seu bot para acesso fora da interface do Usuário de aprendiz de conversação

Ao executar localmente um bot, adicione o ID da aplicação para o bot `.env` ficheiro:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Em seguida, inicie o seu bot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

O bot está agora em execução localmente.  Pode acessá-lo com o emulador de Bot Framework.

### <a name="download-and-install-the-emulator"></a>Transfira e instale o emulador

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Conectar o emulador ao seu bot

1. No canto superior esquerdo do emulador, na caixa "Introduza o URL de ponto final", introduza `http://127.0.0.1:3978/api/messages`.  Deixe os outros campos em branco e clique em "Ligar".

2. É agora conversação com o seu bot.

## <a name="option-2-deploy-to-azure"></a>Opção 2: Implementar no Azure

Publica o seu bot de aprendiz de conversação semelhante da mesma forma que pretende publicar quaisquer outros bot. Num alto nível, carregue seu código para um Web site alojado, definir os valores de configuração apropriada e, em seguida, registe o bot com vários canais. Instruções detalhadas são neste vídeo que mostra como publicar o seu bot com o Azure Bot Service.

Depois do bot é implementado e em execução pode ligar-se diferentes canais ao mesmo, como o Facebook, Teams, Skype etc. utilizar um registo de canal de Bot do Azure. Para obter a documentação sobre esse processo, veja: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Seguem-se as instruções passo a passo para implementar um Bot de aprendiz de conversação para o Azure.  Estas instruções partem do princípio de que a sua origem de bot está disponível a partir de uma origem com base na cloud, como os serviços do Azure DevOps, GitHub, BitBucket ou o OneDrive e irá configurar o seu bot para a implementação contínua.

1. Inicie sessão no portal do Azure em https://portal.azure.com

2. Criar um novo recurso de "Web App Bot" 

    1. Dê um nome ao bot
    2. Clique em "Modelo do Bot", escolha "Node. js", escolha "Basic", e clique no botão "Selecionar"
    3. Clique em "Criar" para criar a Web App Bot.
    4. Aguarde que o recurso de bot de funções a ser criada.

3. No portal do Azure, edite o recurso de bot de funções que acabou de criar.

    1. Clique no item de barra de navegação de "Definições da aplicação" à esquerda
    1. Desloque para baixo para a secção "Definições de aplicação"
    2. Adicione estas definições:

        Variável de ambiente | valor
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
        CONVERSATION_LEARNER_MODEL_ID      | GUID de Id de aplicação, obtido a partir da interface do Usuário de aprendiz de conversação, em "definições" do modelo >
        LUIS_AUTHORING_KEY               | Criação de chave para este modelo do LUIS
        LUIS_SUBSCRIPTION_KEY            | Não é necessário, mas recomendado para bots publicados evitar o uso de sua quota de criação de conteúdos.
    
    4. Clique em "Guardar" junto à parte superior da página
    5. Abrir "Build" item de barra de navegação à esquerda
    6. Clique em "Configurar a implementação contínua" 
    7. Clique no ícone de "Configuração" em implementações
    8. Clique em "Definições necessárias"
    9. Selecione a origem de onde o seu código de bot está disponível e configurar a origem.
