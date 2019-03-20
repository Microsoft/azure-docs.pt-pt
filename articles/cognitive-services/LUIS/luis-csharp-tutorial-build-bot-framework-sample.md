---
title: Bot - C# -v3
titleSuffix: Language Understanding - Azure Cognitive Services
description: Com o C#, crie um chatbot integrado com compreensão de idiomas (LUIS). Este chatbot utiliza o domínio de HomeAutomation criados previamente para implementar rapidamente uma solução de bot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: ca4cd1aa074353914f5187e353c233f8b45140eb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091686"
---
# <a name="luis-bot-in-c-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot de LUIS em C# com o Bot Framework 3.x e bot de aplicação Web do Azure

Com o C#, crie um chatbot integrado com compreensão de idiomas (LUIS). Este chatbot utiliza o domínio de HomeAutomation criados previamente para implementar rapidamente uma solução de bot. O bot baseia-se com o Bot Framework 3.x e bot de aplicação Web do Azure.

## <a name="prerequisite"></a>Pré-requisito

* [Aplicação do LUIS HomeAutomation](luis-get-started-create-app.md). Os objetivos a partir desse mapa de aplicação do LUIS aos manipuladores de caixa de diálogo do bot. 

## <a name="luis-homeautomation-intents"></a>LUIS HomeAutomation intenções

| Intenção | Expressão de exemplo | Funcionalidade de bot |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Ligar as luzes. | Quando a intenção de LUIS `HomeAutomation.TurnOn` é detectado, o bot invoca o `OnIntent` manipulador de caixa de diálogo. Esta caixa de diálogo é onde chamaria um serviço de IoT para ativar um dispositivo e informar ao usuário que o dispositivo foi ativado. |
| HomeAutomation.TurnOff | Desative as luzes Carvalho. | Quando a intenção de LUIS `HomeAutomation.TurnOff` é detectado, o bot invoca o `OffIntent` manipulador de caixa de diálogo. Esta caixa de diálogo é onde chamaria um serviço de IoT para desativar um dispositivo e informar ao usuário que o dispositivo foi desativado. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Criar um bot de compreensão de idiomas com o serviço de Bot

1. Na [portal do Azure](https://portal.azure.com), selecione **criar novo recurso** no menu à esquerda superior.

    ![Criar novo recurso no portal do Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. Na caixa de pesquisa, procure **Web App Bot**. 

    ![Selecione o bot de funções como tipo de recurso](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. Na janela do bot de funções, clique em **criar**.

4. Na **Bot Service**, forneça as informações necessárias e clique em **criar**. Esta ação cria e implementa o serviço de bot e aplicação LUIS para o Azure. Se quiser usar [priming de voz](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), reveja [requisitos de região](troubleshooting.md#what-luis-regions-support-bot-framework-speech-priming) antes de criar o seu bot. 
   * Definir **nome da aplicação** ao nome do seu bot. O nome é utilizado como o subdomínio ao seu bot é implementado na cloud (por exemplo, mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
   * Selecione a subscrição [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), o plano de serviço de aplicações, e [localização](https://azure.microsoft.com/regions/).
   * Para **modelo de Bot**, selecione:
       * **SDK v3**
       * **C#**
       * **Compreensão de idiomas**
   * Selecione o **localização da aplicação LUIS**. Este é o processo de criação [região](luis-reference-regions.md) a aplicação é criada no.
   * Selecione a caixa de verificação confirmação para o aviso legal. São os termos do aviso legal a caixa de verificação abaixo.

     ![Serviço de Bot](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Confirme que o serviço de bot foi implementado.
    * Clicar em notificações (o ícone de sino que esteja localizado ao longo da margem superior do portal do Azure). A notificação é alterado de **implementação iniciada** ao **implementação concluída com êxito**.
    * Depois da notificação muda para **implementação concluída com êxito**, clique em **Ir para recurso** em que a notificação.

> [!Note]
> Este processo de criação do bot de aplicação web também criou uma nova aplicação LUIS para. Foi treinado e publicada para si. 

## <a name="try-the-default-bot"></a>Experimente o bot de predefinição

Confirmar que foi implementado o bot, selecionando o **notificações** caixa de verificação. As notificações é alterado de **implementação em curso...**  para **implementação concluída com êxito**. Clique em **Ir para recurso** botão para abrir os recursos do bot.

Assim que o bot é implementado, clique em **teste na Web Chat** para abrir o painel de Web Chat. Escreva "Olá" na Web Chat.

  ![Testar o bot em Web Chat](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

O bot responde, dizendo "atingiu uma saudação. Disse: hello ".  Esta resposta confirma que o bot tem recebido sua mensagem e passou-lo a uma aplicação do LUIS que ele criou a predefinição. Este padrão de aplicação LUIS detetou uma intenção de saudação. No próximo passo, vai ligar o bot para a aplicação do LUIS que criou anteriormente em vez do padrão de aplicação LUIS.

## <a name="connect-your-luis-app-to-the-bot"></a>Ligar a sua aplicação LUIS para o bot

Open **as configurações do aplicativo** e editar os **LuisAppId** campo para conter o ID da sua aplicação LUIS. Se tiver criado a sua aplicação HomeAutomation LUIS numa região diferente E.U.A. oeste, tem de alterar o **LuisAPIHostName** também. O **LuisAPIKey** está atualmente configurado para a sua chave de criação. Alterar isso para a sua chave de ponto final quando o tráfego excede a quota do escalão gratuito. 

  ![Atualizar o ID da aplicação LUIS no Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Se não tiver o ID da aplicação LUIS do [aplicação de home page de automatização](luis-get-started-create-app.md), inicie sessão no [LUIS](luis-reference-regions.md) Web site com a mesma conta que utiliza para iniciar sessão no Azure. 
> 1. Clique em **as minhas aplicações**. 
> 2. Localize a aplicação do LUIS que criou anteriormente, que contém as intenções e entidades do domínio HomeAutomation.
> 3. Na **definições** página da aplicação do LUIS, localize e copie o ID da aplicação. Certifique-se de que é [treinado](luis-interactive-test.md) e [publicado](luis-how-to-publish-app.md). 
> 
> [!WARNING]
> Se eliminar a chave de LUIS ou ID de aplicação, o bot deixarão de funcionar.

## <a name="modify-the-bot-code"></a>Modificar o código de bot

1. Clique em **crie** e, em seguida, clique em **editor de código online aberto**.

   ![Editor de código online aberto](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Clique com botão direito `build.cmd` e escolha **executar consola** para criar a aplicação. Existem vários passos de compilação, que o serviço for concluída automaticamente por si. A compilação estiver concluída, quando ele termina com "Concluído com êxito".

3. No editor de código, abra `/Dialogs/BasicLuisDialog.cs`. Contém o código a seguir:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Alterar o código para HomeAutomation intenções


1. Remover a intenção atributos e métodos para três **saudação**, **Cancelar**, e **ajudar**. Estes objetivos não são utilizados no domínio pré-criado HomeAutomation. Certifique-se de manter os **None** atributo intenção e o método. 

2. Adicione as dependências na parte superior do arquivo, com as outras dependências:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Adicione constantes para gerir as cadeias de caracteres na parte superior do `BasicLuisDialog ` classe:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Adicione o código para os novo intenções de `HomeAutomation.TurnOn` e `HomeAutomation.TurnOff` dentro do `BasicLuisDialog ` classe:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Adicione o código para obter qualquer entidades encontradas pelo LUIS dentro do `BasicLuisDialog ` classe:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Alteração **ShowLuisResult** método no `BasicLuisDialog ` classe para arredondar a pontuação, recolher as entidades e apresentar a mensagem de resposta no chatbot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Criar o bot
No editor de código, faça duplo clique em `build.cmd` e selecione **executar consola**.

![Criar bot de Web](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

A visualização de código é substituída por uma janela de terminal que mostra o progresso e os resultados da compilação.

![Criar bot da Web com êxito](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Um método alternativo para criar o bot é selecionar o nome do bot na barra azul superior e selecionar **abra a consola Kudu**. A consola será aberta para **D:\home**. 
> 
> Altere o diretório para **D:\home\site\wwwroot** digitando: `cd site\wwwroot`
>
> Execute o script de compilação ao escrever: `build.cmd`

## <a name="test-the-bot"></a>Testar o bot

No portal do Azure, clique em **teste na Web Chat** para testar o bot. Escreva mensagens like "ligar as luzes" e "desativar a minha heater" invocar dos objetivos que adicionou à mesma.

   ![Testar HomeAutomation bot em Web Chat](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> Pode voltar a preparar a aplicação do LUIS sem quaisquer modificações ao código de seu bot. Ver [adicionar expressões de exemplo](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) e [treinar e testar a aplicação do LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test). 

## <a name="download-the-bot-to-debug"></a>Transferir o bot para depurar
Se o seu bot não está a funcionar, transfira o projeto no seu computador local e continuar [depuração](https://docs.microsoft.com/bot-framework/bot-service-debug-bot). 

## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework
Saiba mais sobre [Bot Framework](https://dev.botframework.com/) e o [3.x](https://github.com/Microsoft/BotBuilder) e [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDKs.

## <a name="next-steps"></a>Passos Seguintes

Adicionar os objetivos de LUIS e caixas de diálogo de serviço de Bot de manipulação **ajudar**, **Cancelar**, e **saudação** intenções. Lembre-se de preparar, publicar e, a [criar](#build-the-bot) bot de aplicação web. LUIS e o bot deve ter as mesmo intenções.

Ver mais [amostras](https://github.com/Microsoft/AI) com conversacionais bots. 

> [!div class="nextstepaction"]
> [Adicionar intenções](./luis-how-to-add-intents.md)
> [priming de voz](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)

<!-- tested on Win10 -->
