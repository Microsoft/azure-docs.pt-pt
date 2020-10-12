---
title: 'Como fazer: Criar aplicação com comandos simples - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, aprende-se a criar e testar uma aplicação de Comandos Personalizados hospedados utilizando comandos simples.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d166257dd28773d89a4f1fd56de3cb1a22242523
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284171"
---
# <a name="create-application-with-simple-commands"></a>Criar uma aplicação com comandos simples

Neste artigo, vai aprender a:
 - Criar uma aplicação vazia
 - Atualizar recursos LUIS
 - Adicione alguns comandos básicos à sua aplicação de Comandos Personalizados

## <a name="create-empty-application"></a>Criar aplicação vazia
Crie uma aplicação de Comandos Personalizados vazios. Para mais detalhes, consulte o [arranque rápido](quickstart-custom-commands-application.md). Desta vez, em vez de importar um projeto, cria-se um projeto em branco.

1. Na caixa **Nome,** insira o nome do projeto como `Smart-Room-Lite` (ou outra coisa à sua escolha).
1. Na lista **de línguas,** selecione **Inglês (Estados Unidos)**.
1. Selecione ou crie um recurso LUIS à sua escolha.

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>Atualizar os recursos LUIS (opcional)

Pode atualizar o recurso de autoria que selecionou na janela do **projeto Novo** e definir um recurso de previsão. O recurso de previsão é utilizado para reconhecimento quando a sua aplicação de Comandos Personalizados é publicada. Não precisa de um recurso de previsão durante as fases de desenvolvimento e teste.

## <a name="add-turnon-command"></a>Adicionar comando TurnOn

Na aplicação de Comandos Personalizados **Smart-Room-Lite** vazia que acabou de criar, adicione um simples comando que processa uma `turn on the tv` expressão, e responde com a mensagem `Ok, turning the tv on` .

1. Crie um novo Comando selecionando **Novo comando** na parte superior do painel esquerdo. A nova janela **de comando** abre-se.
1. Fornecer valor para o campo **Nome** como **TurnOn**.
1. Selecione **Criar**.

O painel do meio lista as diferentes propriedades do comando. Configurar as seguintes propriedades do comando. Para obter explicação de todas as propriedades de configuração de um comando, vá a [referências](./custom-commands-references.md).

| Configuração            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Frases de exemplo** | Palavras de exemplo que o utilizador pode dizer para desencadear este Comando                                                                 |
| **Parâmetros**       | Informações necessárias para completar o Comando                                                                                |
| **Regras de conclusão** | As ações a tomar para cumprir o Comando. Por exemplo, para responder ao utilizador ou comunicar com outro serviço web. |
| **Regras de interação**   | Regras adicionais para lidar com situações mais específicas ou complexas                                                              |


> [!div class="mx-imgBorder"]
> ![Criar um comando](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>Adicionar frases de exemplo

Vamos começar com a secção **de frases exemplo,** e fornecer um exemplo do que o utilizador pode dizer.

1. Selecione A secção **de frases** exemplo no painel central.
1. No painel mais direito, adicione exemplos:

    ```
    turn on the tv
    ```

1.  **Selecione Guarde** na parte superior do painel.

Por enquanto, não temos parâmetros, por isso podemos passar para a secção **de regras de conclusão.**

### <a name="add-a-completion-rule"></a>Adicione uma regra de conclusão

Em seguida, o comando precisa ter uma regra de conclusão. Esta regra diz ao utilizador que está a ser tomada uma ação de cumprimento. Para ler mais sobre regras e regras de conclusão, vá a [referências.](./custom-commands-references.md)

1. Selecione a regra de conclusão por defeito **Concluída** e edite-a da seguinte forma:

    
    | Definição    | Valor sugerido                          | Descrição                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nome**       | ConfirmaçãoResponse                  | Um nome que descreve o propósito da regra          |
    | **Condições** | Nenhum                                     | Condições que determinam quando a regra pode ser executada    |
    | **Ações**    | Envie resposta de fala > editor simples > Primeira variação > `Ok, turning the tv on` | A ação a tomar quando a condição de regra é verdadeira |
    



   > [!div class="mx-imgBorder"]
   > ![Criar uma resposta de discurso](media/custom-commands/create-speech-response-action.png)

1. **Selecione Guardar** para guardar a ação.
1. De volta à secção **de regras de Conclusão,** selecione **Guardar** para guardar todas as alterações. 


    > [!NOTE]
    > Não é necessário usar a regra de conclusão padrão que vem com o comando. Se necessário, pode eliminar a regra de conclusão por defeito existente e adicionar a sua própria regra.

### <a name="try-it-out"></a>Experimente

Teste o comportamento usando o painel de chat test
1. Selecione o ícone **do comboio** presente no topo do painel direito.
1. Uma vez concluído o treino, selecione **Teste**. Experimente a seguinte expressão através de voz/texto:
    - Entrada: ligue a televisão
    - Saída: Ok, ligar a televisão


> [!div class="mx-imgBorder"]
> ![Teste com web chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> No painel de teste, pode selecionar **Dar informações** sobre como esta entrada de voz/texto foi processada.  

## <a name="add-settemperature-command"></a>Adicionar comando SetTemperature

Agora, adicione mais um comando **SetTemperature** que levará uma única expressão, `set the temperature to 40 degrees` e responderá com a mensagem `Ok, setting temperature to 40 degrees` .

Siga os passos como ilustrado para o comando **TurnOn** para criar um novo comando usando a frase exemplo, "**definir a temperatura para 40 graus**".

Em seguida, edite as regras de conclusão **existentes:**

| Definição    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| Nome  | ConfirmaçãoResponse                  |
| Condições | Nenhum                                     |
| Ações    | Envie resposta de fala > editor simples > Primeira variação > `Ok, setting temperature to 40 degrees` |

**Selecione Guardar** para guardar todas as alterações no comando.

## <a name="add-setalarm-command"></a>Adicionar comando SetAlarm
Crie um novo **Comando SetAlarm** usando a frase exemplo",**acende um alarme para as 9 da manhã de amanhã**". Em seguida, edite as regras de conclusão **existentes:**

| Definição    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| Nome da Regra  | ConfirmaçãoResponse                  |
| Condições | Nenhum                                     |
| Ações    | Envie resposta à fala > editor simples > Primeira variação >`Ok, setting an alarm for 9 am tomorrow` |

**Selecione Guardar** para guardar todas as alterações no comando.

## <a name="try-it-out"></a>Experimente

Teste o comportamento usando o painel de chat test
1. Selecione **Train** (Preparar). Após o sucesso do treino, selecione **Teste** e experimente:
    - Você escreve: definir a temperatura para 40 graus
    - Resposta esperada: Ok, ajustar a temperatura para 40 graus
    - Escreve: ligue a televisão
    - Resposta esperada: Ok, ligar a televisão
    - Escreve: desemote um alarme para as 9:00 de amanhã
    - Resposta esperada: Ok, a definir um alarme para as 9:00 de amanhã

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar parâmetros aos comandos](./how-to-custom-commands-add-parameters-to-commands.md)
