---
title: Como usar Conversation Learner com outras tecnologias de criação de bot-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar Conversation Learner com outras tecnologias de criação de bot.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c964c62c34f952a547d077e93e7bb4d0eb7b192d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703682"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Como usar Conversation Learner com outras tecnologias de criação de bot

Este tutorial aborda como usar Conversation Learner com outras tecnologias de criação de bot e como a memória (ou o estado) pode ser compartilhada entre essas tecnologias. 

## <a name="video"></a>Vídeo

[![Visualização do tutorial de bots híbridos](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Requisitos
Este tutorial requer o uso do emulador de bot para criar caixas de diálogo de log, não a IU da Web da caixa de diálogo de log. Mais informações sobre como configurar o emulador do bot Framework está disponível [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Este tutorial requer que o bot tutorial híbrido esteja em execução:

    npm run tutorial-hybrid

## <a name="details"></a>Detalhes

Embora Conversation Learner esteja no controle, todo o estado relativo à sessão de Conversation Learner deve ser armazenado no Gerenciador de memória do Conversation Learner. Isso é necessário, pois o Machine Learning usa o estado para determinar como conduzir a conversa. O estado externo pode ser passado para o Conversation Learner no OnSessionStartCallback que é chamado quando a sessão começa. O estado interno pode ser retornado pelo OnSessionEndCallback quando a sessão é encerrada.

Você pode quase considerar Conversation Learner como uma chamada de função que usa um estado inicial e retorna valores.

Neste exemplo, você criará um bot híbrido usando dois sistemas diferentes:
1. Um modelo de Conversation Learner <br/>
    Usa o modelo de aprendiz de conversa para determinar a próxima ação do bot com base na sessão atual. Essa parte do bot usa uma parte do estado `isOpen` inicial (que indica se um repositório está aberto ou fechado) e retorna outra parte do estado `purchaseItem` (o nome de um item que o usuário adquire).

2. Correspondência de texto <br />
    Simplesmente examina o texto de entrada para cadeias de caracteres específicas e responde. Essa parte do bot gerencia os outros mecanismos de armazenamento dos bots e é responsável por iniciar a sessão CL. Especificamente, ele gerencia três variáveis `usingConversationLearner`: `storeIsOpen`, e `purchaseItem`.

Vamos começar examinando o modelo usado nesta demonstração.

### <a name="open-the-demo"></a>Abrir a demonstração

Na interface do usuário da Web, clique em "importar tutoriais" e selecione o modelo chamado "tutorial-16-HybridBot".

## <a name="entities"></a>Entidades

Abra a página entidades e observe duas entidades: `isOpen` e`purchaseItem`

Para entender como essas entidades são usadas, abra o arquivo: `C:\<installedpath>\src\demos\tutorialHybrid.ts` para examinar os retornos de chamada.

Observe que o código em `OnSessionStartCallback` copia o valor de `storeIsOpen` do armazenamento de conversa do BotBuilder `isOpen` como o valor da entidade para que ele esteja disponível para Conversation Learner. Consulte o código a seguir:

![](../media/tutorial17_sessionstart.PNG)

Da mesma forma, o `OnSessionEndCallback` código em (se a sessão foi encerrada devido a uma atividade aprendida e não apenas a um tempo limite) `purchaseItem` copia o valor da `purchaseItem`entidade para o armazenamento BotBuilder. Consulte o código a seguir:

![](../media/tutorial17_sessionend.PNG)

Agora, vamos examinar as ações.

## <a name="actions"></a>Ações

Observe que o modelo tem quatro ações.

As regras pretendidas para as ações são as seguintes:

- Se a `isOpen` entidade estiver definida, o bot perguntará "o que você gostaria de comprar?" e armazená-la `puchaseItem` no slot.
- Se `isOpen` não estiver definido, o bot dirá "desculpas, estamos fechados".
- As outras duas ações são do tipo `END_SESSION`.
- A ação END_SESSION indica ao ConversationLearner que a conversa foi concluída.

### <a name="overall-bot-logic"></a>Lógica de bot geral

Primeiro, você verá que, se o sinalizador `usingConversationLearner` do estado do bot tiver sido definido, passaremos o controle para Conversation Learner. Caso contrário, passamos o controle para outra coisa.  Neste exemplo, estamos mostrando uma correspondência de texto simples, mas pode ser qualquer outra tecnologia de bot, incluindo LUIS, QnA Maker e até mesmo outra instância do Conversation Learner.

Precisamos de uma maneira para o usuário abrir e fechar a loja, então fazemos uma comparação de cadeia de caracteres com "Open Store" e "Close Store" e definimos o sinalizador "storeIsOpen".

Em seguida, precisamos de uma maneira de disparar o controle à mão para nosso modelo de Conversation Learner. Quando correspondermos à cadeia de caracteres "Shop", fazemos o seguinte:
- Defina o `usingConversationLearner` sinalizador na memória do bot.
- Chame o método "StartSession" em nosso modelo de Conversation Learner.  Isso disparará o "onSessionStartCallback", que inicializará o valor da `isOpen` entidade

Veja abaixo:

![](../media/tutorial17_useConversationLearner.PNG)

Também fazemos uma correspondência de texto com "History", que exibirá esse último item de compra.
Por fim, se qualquer outra coisa for digitada, exibiremos os comandos de usuário disponíveis

## <a name="train-dialog"></a>Caixa de diálogo treinar

Para este tutorial, o modelo já foi treinado previamente.  Testaremos o bot completo para ver o efeito dos retornos de chamada de sessão inicial e final na prática.

## <a name="testing-the-bot"></a>Testando o bot

Ao contrário dos bots de modelo mais enxuto de conversa simples, você não poderá testá-lo na interface do usuário do Conversation Learner, pois ele só pode mostrar o que é tratado pelo modelo de Conversation Learner.

### <a name="install-the-bot-framework-emulator"></a>Instalar o emulador do bot Framework

- Vá para [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Baixe e instale o emulador.

### <a name="configure-the-emulator"></a>Configurar o emulador

- Abra o emulador e verifique se a URL está direcionando a mesma porta em que o bot está sendo executado. Podem`http://localhost:3978/api/messages`

### <a name="test"></a>Teste 

#### <a name="scenario-1-store-is-closed"></a>Cenário 1: O repositório está fechado
1. Insira "Shop". Isso é tratado pela correspondência de texto e dará controle ao modelo de Conversation Learner.
2. Insira ' Olá '.  Como `isOpen` o valor não está definido, o bot dirá "desculpas, estamos fechados" e encerramos a sessão.

#### <a name="scenario-2-store-is-open"></a>Cenário 2: O repositório está aberto
1. Insira ' abrir armazenamento '.  Isso definirá `isOpen` como true.
1. Insira "Shop".
1. Insira ' Olá '.  Como `isOpen` o valor é definido como true, o bot dirá "o que você gostaria de comprar?"
1. Insira ' cadeira '. ' cadeira ' será salvo na memória do CL como a entidade `purchaseItem`. O retorno de chamada da sessão final é invocado, que copia esse valor para o repositório de conversa.
1. Insira ' histórico '.  O bot dirá "você comprou a cadeira", pois esta foi a `purchaseItem`sua última.

## <a name="conclusion"></a>Conclusão

Com o que você aprendeu acima, você deve ser capaz de combinar Conversation Learner com qualquer outra tecnologia de criação de bot.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ramificação e desfazer](./17-branch-undo.md)
