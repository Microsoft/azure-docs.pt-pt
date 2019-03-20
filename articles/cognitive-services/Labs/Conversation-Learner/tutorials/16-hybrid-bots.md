---
title: Como utilizar o aprendiz de conversação com outras tecnologias - serviços cognitivos da Microsoft de criação de bot | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar o aprendiz de conversação com outras tecnologias de criação de bot.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: b90fc42aa56bfc813ec464670336dea75cff0f0e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117443"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Como utilizar o aprendiz de conversação com outras tecnologias de criação de bot

Este tutorial abrange como utilizar o aprendiz de conversação com outras tecnologias de criação de bot e como a memória (ou estado) pode ser partilhado entre essas tecnologias. 

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do híbrida Bots](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Requisitos
Este tutorial requer a utilizar o emulador de bot para criar caixas de diálogo de registo, não a IU da Web do registo de caixa de diálogo. Estão disponíveis mais informações sobre como configurar o emulador do Bot Framework [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Este tutorial requer que o bot tutorial híbrido está em execução:

    npm run tutorial-hybrid

## <a name="details"></a>Detalhes

Enquanto o aprendiz de conversação está no controle, todos os Estados em relação a sessão de aprendiz de conversação devem ser armazenado no Gestor de memória do Aprendiz de conversação. Isso é necessário, como o machine learning utiliza o estado para determinar como incentivar a conversa. Estado externo pode passar para o aprendiz de conversação o OnSessionStartCallback que é chamado quando a sessão é iniciada. Estado interno horizontalmente pode ser devolvido pelo OnSessionEndCallback quando a sessão é encerrada.

Quase pode pensar Aprendiz de conversação como uma chamada de função que usa algum Estado inicial e devolve valores.

Neste exemplo, irá criar um bot de híbrida através de dois sistemas diferentes:
1. Um modelo de aprendiz de conversação <br/>
    Utiliza o modelo de aprendiz de conversação para determinar a próxima ação do bot com base na sessão atual. Esta parte do bot usa uma parte do Estado inicial `isOpen` (que indica se um arquivo é aberto ou fechado) e retorna outra parte do estado `purchaseItem` (o nome de um item, o utilizador compra).

2. Correspondência de texto <br />
    Basta analisa o texto de entrada para cadeias de caracteres específicas e responde. Esta parte do bot gere dos Bots outros mecanismos de armazenamento e é responsável por iniciar a sessão de CL. Especificamente gere três variáveis: `usingConversationLearner`, `storeIsOpen`, e `purchaseItem`.

Vamos começar dando uma olhada no modelo usada nesta demonstração.

### <a name="open-the-demo"></a>Abra a demonstração

Na IU da web, clique em "Importar tutorial" e selecione o modelo com o nome "Tutorial-16-HybridBot".

## <a name="entities"></a>Entidades

Abra a página de entidades e observe duas entidades: `isOpen` e `purchaseItem`

Para compreender como essas entidades são usadas, abra o ficheiro: `C:\<installedpath>\src\demos\tutorialHybrid.ts` para examinar os retornos de chamada.

Tenha em atenção que o código na `OnSessionStartCallback` copia o valor de `storeIsOpen` do armazenamento de conversação BotBuilder como o valor do `isOpen` entidade, de modo está disponível para o aprendiz de conversação. Consulte o seguinte código:

![](../media/tutorial17_sessionstart.PNG)

Da mesma forma, o código na `OnSessionEndCallback` (se a sessão foi terminada devido a uma atividade de aprender e não simplesmente um tempo limite) copia o valor de entidade `purchaseItem` fora para o armazenamento de BotBuilder `purchaseItem`. Consulte o seguinte código:

![](../media/tutorial17_sessionend.PNG)

Agora vamos examinar as ações.

## <a name="actions"></a>Ações

Tenha em atenção que o modelo tem quatro ações.

As regras pretendidas para as ações são os seguintes:

- Se o `isOpen` entidade está definida, o Bot irá perguntar "O que deseja comprar?" e armazená-lo no `puchaseItem` ranhura.
- Se `isOpen` não está definido, o Bot dirão "Desculpe, está a ser fechadas".
- As outras duas ações são do tipo `END_SESSION`.
- A ação de END_SESSION indica para ConversationLearner que a conversa foi concluída.

### <a name="overall-bot-logic"></a>Lógica de Bot geral

Primeiro veja que, se o estado de Bot `usingConversationLearner` sinalizador foi definido, podemos passar controlo para Aprendiz de conversação. Caso contrário, passamos o controle para algo mais.  Neste exemplo, vamos mostrar a correspondência de texto simples, mas isso poderia ser qualquer outra tecnologia de Bot incluindo LUIS, a ferramenta QnA maker e ainda outra instância do Aprendiz de conversação.

Precisamos encontrar uma maneira para o utilizador abrir e fechar o arquivo de, pelo que podemos fazer uma cadeia de caracteres comparar com "store aberta" e "fecho de armazenamento" e defina o sinalizador "storeIsOpen".

Em seguida, temos uma forma para disparar o controle de distribuição sobre ao nosso modelo de aprendiz de conversação. Quando podemos corresponder para a cadeia de caracteres de "departamento", fazemos o seguinte:
- Definir o `usingConversationLearner` sinalizador na memória do Bot.
- Chame o método "StartSession" em nosso modelo de aprendiz de conversação.  Isto irá acionar o "onSessionStartCallback" que irá inicializar o `isOpen` valor de entidade

Veja a seguir:

![](../media/tutorial17_useConversationLearner.PNG)

Fazemos também uma correspondência de texto para "histórico de" que exibirá esse último item de compra.
Por fim, se qualquer outra coisa é escrita, apresentamos os comandos de usuário disponíveis

## <a name="train-dialog"></a>Caixa de diálogo preparar

Para este tutorial, o modelo já está com formação prévia.  Iremos testar o bot completo para ver o efeito de início e fim retornos de chamada de sessão na prática.

## <a name="testing-the-bot"></a>Teste o Bot

Ao contrário do único bots de modelo de conversação Leaner não será capaz de testar isso na IU do Aprendiz de conversação, como pode mostrar apenas o que é processado pelo modelo de aprendiz de conversação.

### <a name="install-the-bot-framework-emulator"></a>Instale o emulador do Bot framework

- Vá para [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Baixe e instale o emulador.

### <a name="configure-the-emulator"></a>Configurar o emulador

- Abra o emulador e certifique-se de que o URL está direcionado para a mesma porta que o seu bot está a ser executada. Provavelmente: `http://localhost:3978/api/messages`

### <a name="test"></a>Teste 

#### <a name="scenario-1-store-is-closed"></a>Cenário 1: Store está fechado
1. Introduza "departamento". Isso é manipulado pela correspondência de texto e fornecem um controlo para o modelo de aprendiz de conversação.
2. Introduza "hello".  Uma vez que `isOpen` valor não está definido, o bot dirão "Desculpe, está a ser fechadas" e terminar a sessão.

#### <a name="scenario-2-store-is-open"></a>Cenário 2: Store está aberta
1. Introduza "abrir o arquivo".  Isso definirá o `isOpen` como true.
1. Introduza "departamento".
1. Introduza "hello".  Uma vez que `isOpen` valor está definido como true, o bot será apresentada a mensagem "O que deseja comprar?"
1. Introduza "cadeira". "cadeira" será guardada na memória CL como a entidade `purchaseItem`. O retorno de chamada de sessão final é invocado para copia este valor de para o arquivo de conversação.
1. Introduza 'Histórico'.  O bot será apresentada a mensagem "comprasse uma cadeira" como isso foi a última `purchaseItem`.

## <a name="conclusion"></a>Conclusão

Com o que aprendeu acima deve ser capaz de combinar Aprendiz de conversação com qualquer outro Bot compilação tecnologia.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ramificação e desfazer](./17-branch-undo.md)
