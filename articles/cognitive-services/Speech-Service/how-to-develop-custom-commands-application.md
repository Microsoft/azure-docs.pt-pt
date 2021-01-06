---
title: 'Como fazer: Desenvolver aplicações de comandos personalizados - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a desenvolver e personalizar aplicações de Comandos Personalizados. Estas aplicações de comando de voz são mais adequadas para a conclusão de tarefas ou cenários de comando e controlo.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 1a002b6efbe2603ae254c19f9e3cc7377198cea2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935823"
---
# <a name="develop-custom-commands-applications"></a>Desenvolver aplicações de comandos personalizados

Neste artigo de como fazer, aprende-se a desenvolver e configurar aplicações de Comandos Personalizados. A funcionalidade Comandos Personalizados ajuda-o a construir aplicações ricas de comando de voz que são otimizadas para experiências de interação de voz.first. A funcionalidade é mais adequada para a conclusão de tarefas ou cenários de comando e controlo. É particularmente adequado para dispositivos Internet of Things (IoT) e para dispositivos ambiente e sem cabeça.

Neste artigo, cria-se uma aplicação que pode ligar e desligar uma TV, definir a temperatura e definir um alarme. Depois de criar estes comandos básicos, aprenderá sobre as seguintes opções para personalizar comandos:

* Adicionar parâmetros aos comandos
* Adicionar configurações aos parâmetros de comando
* Regras de interação de edifícios
* Criação de modelos de geração de linguagem para respostas de fala
* Usando ferramentas de voz personalizadas

## <a name="create-an-application-by-using-simple-commands"></a>Criar uma aplicação utilizando comandos simples

Comece por criar uma aplicação de Comandos Personalizados vazios. Para mais detalhes, consulte o [arranque rápido](quickstart-custom-commands-application.md). Nesta aplicação, em vez de importar um projeto, cria-se um projeto em branco.

1. Na caixa **Nome,** insira o nome do projeto *Smart-Room-Lite* (ou outro nome à sua escolha).
1. Na lista **de línguas,** selecione **Inglês (Estados Unidos)**.
1. Selecione ou crie um recurso LUIS.

   > [!div class="mx-imgBorder"]
   > ![Screenshot mostrando a janela do "Novo projeto".](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Atualizar os recursos LUIS (opcional)

Pode atualizar o recurso de autoria que selecionou na janela do **novo projeto.** Também pode definir um recurso de previsão. 

Um recurso de previsão é usado para reconhecimento quando a sua aplicação De Comandos Personalizados é publicada. Não precisa de um recurso de previsão durante as fases de desenvolvimento e teste.

### <a name="add-a-turnon-command"></a>Adicione um comando TurnOn

Na aplicação de Comandos Personalizados Smart-Room-Lite vazia que criou, adicione um comando. O comando processará uma `Turn on the tv` expressão, . Responderá com a `Ok, turning the tv on` mensagem.

1. Crie um novo comando selecionando **Novo comando** na parte superior do painel esquerdo. A nova janela **de comando** abre-se.
1. Para o campo **Nome,** forneça o valor `TurnOn` .
1. Selecione **Criar**.

O painel do meio lista as propriedades do comando. 

A tabela seguinte explica as propriedades de configuração do comando. Para mais informações, consulte [conceitos e definições de Comandos Personalizados.](./custom-commands-references.md)

| Configuração            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Frases de exemplo | Palavras de exemplo que o utilizador pode dizer para desencadear este comando.                                                                 |
| Parâmetros       | Informação necessária para completar o comando.                                                                                |
| Regras de conclusão | Ações a serem tomadas para cumprir o comando. Exemplos: responder ao utilizador ou comunicar com um serviço web. |
| Regras de interação   | Outras regras para lidar com situações mais específicas ou complexas.                                                              |


> [!div class="mx-imgBorder"]
> ![Screenshot mostrando onde criar um comando.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Adicionar frases de exemplo

Na secção **frases Exemplo,** fornece um exemplo do que o utilizador pode dizer.

1. No painel do meio, selecione **frases exemplo**.
1. No painel à direita, adicione exemplos:

    ```
    Turn on the tv
    ```

1.  Na parte superior do painel, **selecione Save**.

Ainda não tens parâmetros, por isso podes passar para a secção **de regras de conclusão.**

#### <a name="add-a-completion-rule"></a>Adicione uma regra de conclusão

Em seguida, o comando precisa de uma regra de conclusão. Esta regra diz ao utilizador que está a ser tomada uma ação de cumprimento. 

Para obter mais informações sobre regras e regras de conclusão, consulte [conceitos e definições de Comandos Personalizados.](./custom-commands-references.md)

1. Selecione a regra de conclusão padrão **Concluída**. Em seguida, editá-lo da seguinte forma:

    
    | Definição    | Valor sugerido                          | Descrição                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nome**       | `ConfirmationResponse`                  | Um nome que descreve o propósito da regra          |
    | **Condições** | Nenhum                                     | Condições que determinam quando a regra pode ser executada    |
    | **Ações**    | **Enviar resposta de**  >  discurso **Editor simples**  >  **Primeira variação** > `Ok, turning the tv on` | A ação a tomar quando a condição de regra é verdadeira |

   > [!div class="mx-imgBorder"]
   > ![Screenshot mostrando onde criar uma resposta de fala.](media/custom-commands/create-speech-response-action.png)

1. **Selecione Guardar** para guardar a ação.
1. De volta à secção **de regras de Conclusão,** selecione **Guardar** para guardar todas as alterações. 

    > [!NOTE]
    > Não tens de usar a regra de conclusão padrão que vem com o comando. Pode eliminar a regra de conclusão por defeito e adicionar a sua própria regra.

### <a name="add-a-settemperature-command"></a>Adicione um comando SetTemperature

Agora adicione mais um comando, `SetTemperature` . Este comando tomará uma única `Set the temperature to 40 degrees` expressão, e responderá com a mensagem `Ok, setting temperature to 40 degrees` .

Para criar o novo comando, siga os passos utilizados para o `TurnOn` comando, mas use a frase exemplo `Set the temperature to 40 degrees` .

Em seguida, edite as regras de conclusão **de Done** existentes da seguinte forma:

| Definição    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| **Nome**  | `ConfirmationResponse`                  |
| **Condições** | Nenhum                                     |
| **Ações**    | **Enviar resposta de**  >  discurso **Editor simples**  >  **Primeira variação** > `Ok, setting temperature to 40 degrees` |

**Selecione Guardar** para guardar todas as alterações no comando.

### <a name="add-a-setalarm-command"></a>Adicione um comando SetAlarm

Criar um novo `SetAlarm` comando. Use a frase exemplo `Set an alarm for 9 am tomorrow` . Em seguida, edite as regras de conclusão **de Done** existentes da seguinte forma:

| Definição    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| **Nome**  | `ConfirmationResponse`                  |
| **Condições** | Nenhum                                     |
| **Ações**    | **Enviar resposta de**  >  discurso **Editor simples**  >  **Primeira variação** > `Ok, setting an alarm for 9 am tomorrow` |

**Selecione Guardar** para guardar todas as alterações no comando.

### <a name="try-it-out"></a>Experimente

Teste o comportamento da aplicação utilizando o painel de teste: 

1. No canto superior direito do painel, selecione o ícone **Train.** 
1. Quando o treino terminar, selecione **Teste**. 

Experimente os seguintes exemplos de expressão utilizando voz ou texto:

- Você escreve: *definir a temperatura para 40 graus*
- Resposta esperada: Ok, ajustar a temperatura para 40 graus
- Escreve: *ligue a televisão*
- Resposta esperada: Ok, ligar a televisão
- Escreve: *desemote um alarme para as 9:00 de amanhã*
- Resposta esperada: Ok, a definir um alarme para as 9:00 de amanhã

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando o teste numa interface de chat web.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> No painel de teste, pode selecionar **Detalhes de Turn** para obter informações sobre como esta entrada de voz ou entrada de texto foi processada.

## <a name="add-parameters-to-commands"></a>Adicionar parâmetros aos comandos

Nesta secção, aprende-se a adicionar parâmetros aos seus comandos. Os comandos requerem parâmetros para completar uma tarefa. Em cenários complexos, os parâmetros podem ser usados para definir condições que desencadeiam ações personalizadas.

### <a name="configure-parameters-for-a-turnon-command"></a>Configurar parâmetros para um comando TurnOn

Comece por editar o comando existente `TurnOn` para ligar e desligar vários dispositivos.

1. Agora que o comando irá lidar com cenários dentro e fora, rebatize o comando como *TurnOnOff*.
   1. No painel à esquerda, selecione o comando **TurnOn.** Em seguida, ao lado do **novo comando** na parte superior do painel, selecione o botão elipse **(...**)
   
   1. Selecione **Renomear**. Na janela **de comando Do renomear,** altere o nome para *TurnOnOff*.

1. Adicione um novo parâmetro ao comando. O parâmetro representa se o utilizador quer ligar ou desligar o dispositivo.
   1. No topo do painel do meio,  **selecione Adicionar**. A partir do menu suspenso, selecione **Parâmetro**.
   1. No painel à direita, na secção **Parâmetros,** na caixa **Nome,** adicione `OnOff` .
   1. **Selecione Necessário**. Na resposta Add para uma janela **de parâmetros necessária,** selecione **Editor Simples**. No campo **first variation,** adicionar *On or Off?*. .
   1. Selecione **Atualizar**.

       > [!div class="mx-imgBorder"]
       > ![Screenshot que mostra a secção 'Adicionar resposta para um parâmetro necessário' com o separador 'Editor simples' selecionado.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Configure as propriedades do parâmetro utilizando a tabela seguinte. Para obter informações sobre todas as propriedades de configuração de um comando, consulte [conceitos e definições de Comandos Personalizados.](./custom-commands-references.md)
      

       | Configuração      | Valor sugerido     | Descrição                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Nome**               | `OnOff`           | Um nome descritivo para o parâmetro                                                                           |
       | **É Global**          | Não selecionado       | Caixa de verificação indicando se um valor para este parâmetro é aplicado globalmente a todos os comandos da aplicação.|
       | **Obrigatório**           | Selecionado         | Caixa de verificação indicando se é necessário um valor para este parâmetro antes do fim do comando. |
       | **Resposta para parâmetro requerido**      |**Editor simples** > `On or Off?`      | Um pedido de pedido do valor deste parâmetro quando não é conhecido. |
       | **Tipo**               | **String**          | Tipo de parâmetro, como Número, Cadeia, Hora da Data ou Geografia.   |
       | **Configuração**      | **Aceite valores de entrada predefinidos de um catálogo interno** | Para as cordas, esta definição limita as entradas a um conjunto de valores possíveis. |
       | **Valores de entrada predefinidos**     | `on`, `off`           | Conjunto de possíveis valores e seus pseudónimos.         |
       
        
   1. Para adicionar valores de entrada predefinidos, **selecione Adicione uma entrada predefinida**. Na janela **New Item,**  escreva *o nome* como indicado na tabela anterior. Neste caso, não estás a usar pseudónimos, por isso podes deixar este campo em branco.
   
      > [!div class="mx-imgBorder"]
      > ![Screenshot mostrando como criar um parâmetro.](media/custom-commands/create-on-off-parameter.png)

   1. **Selecione Guardar** para guardar todas as configurações do parâmetro.
 
#### <a name="add-a-subjectdevice-parameter"></a>Adicione um parâmetro SubjectDevice

1. Para adicionar um segundo parâmetro para representar o nome dos dispositivos que podem ser controlados utilizando este comando, selecione **Adicionar**. Utilize a seguinte configuração.


    | Definição            | Valor sugerido       |
    | ------------------ | --------------------- |
    | **Nome**               | `SubjectDevice`         |
    | **É Global**          | Não selecionado             |
    | **Obrigatório**           | Selecionado               |
    | **Resposta para parâmetro requerido**     | **Editor simples** > `Which device do you want to control?`    | 
    | **Tipo**               | **String**                |          |
    | **Configuração**      | **Aceite valores de entrada predefinidos de um catálogo interno** | 
    | **Valores de entrada predefinidos** | `tv`, `fan`               |
    | **Pseudónimos** `tv`      | `television`, `telly`     |

1. Selecione **Guardar**.

#### <a name="modify-example-sentences"></a>Modificar frases de exemplo

Para comandos que usam parâmetros, é útil adicionar frases de exemplo que cobrem todas as combinações possíveis. Por exemplo:

* Informação completa dos parâmetros: `turn {OnOff} the {SubjectDevice}`
* Informação parcial do parâmetro: `turn it {OnOff}`
* Sem informação sobre parâmetros: `turn something`

As frases exemplo que utilizam diferentes graus de informação permitem que a aplicação de Comandos Personalizados resolva resoluções de um tiro e resoluções de várias voltas utilizando informações parciais.

Com essa informação em mente, edite as frases de exemplo para usar estes parâmetros sugeridos:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Selecione **Guardar**.

> [!TIP]
> No editor de frases-exemplo, use aparelhos encaracolados para se referir aos seus parâmetros. Por exemplo, `turn {OnOff} the {SubjectDevice}`.
> Utilize um separador para a conclusão automática apoiado por parâmetros previamente criados.

#### <a name="modify-completion-rules-to-include-parameters"></a>Modificar as regras de conclusão para incluir parâmetros

Modifique a regra de conclusão `ConfirmationResponse` existente.

1. Na secção **Condições,** **selecione Adicionar uma condição**.
1. Na janela **New Condition,** na lista **Tipo,** selecione **os parâmetros necessários**. Na lista que se segue, selecione **OnOff** e **SubjectDevice**.
1. Deixe **isGlobal** não selecionado.
1. Selecione **Criar**.
1. Na secção **Ações,** edite a ação **de resposta da fala Enviar** pairando sobre ela e selecionando o botão de edição. Desta vez, use os novos `OnOff` e `SubjectDevice` parâmetros:

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Selecione **Guardar**.

Experimente as alterações selecionando o ícone **Train** na parte superior do painel à direita. 

Quando o treino terminar, selecione **Teste**. Aparece uma janela **de teste.** Experimente as seguintes interações:

- Entrada: *desligue a televisão*
- Saída: Ok, desligar a televisão
- Entrada: *desligue a televisão*
- Saída: Ok, desligar a televisão
- Entrada: *desligá-lo*
- Saída: Que dispositivo pretende controlar?
- Entrada: *a televisão*
- Saída: Ok, desligar a televisão

### <a name="configure-parameters-for-a-settemperature-command"></a>Configurar parâmetros para um comando SetTemperature

Modifique o `SetTemperature` comando para lhe permitir definir a temperatura à medida que o utilizador dirige.

Adicione um `Temperature` parâmetro. Utilize a seguinte configuração:

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| **Nome**               | `Temperature`           |
| **Obrigatório**           | Selecionado         |
| **Resposta para parâmetro requerido**      | **Editor simples** > `What temperature would you like?`
| **Tipo**               | `Number`          |


Edite as expressões de exemplo para utilizar os seguintes valores.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Editar as regras de conclusão existentes. Utilize a seguinte configuração.

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| **Condições**         | **Parâmetro**  >  necessário **Temperatura**           |
| **Ações**           | **Enviar resposta de discurso** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Configurar parâmetros para um comando SetAlarm

Adicione um parâmetro chamado `DateTime` . Utilize a seguinte configuração.

   | Definição                           | Valor sugerido                     | 
   | --------------------------------- | ----------------------------------------|
   | **Nome**                              | `DateTime`                               |
   | **Obrigatório**                          | Selecionado                                 |
   | **Resposta para parâmetro requerido**   | **Editor simples** > `For what time?`            | 
   | **Tipo**                              | **DateTime**                                |
   | **Predefinições de data**                     | Se faltar a data, use hoje.            |
   | **Incumprimentos do tempo**                     | Se faltar a hora, use o início do dia.     |


> [!NOTE]
> Este artigo utiliza principalmente os tipos de parâmetros String, Number e DateTime. Para obter uma lista de todos os tipos de parâmetros suportados e suas propriedades, consulte [conceitos e definições de Comandos Personalizados.](./custom-commands-references.md)


Editar as expressões de exemplo. Utilize os seguintes valores.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Editar as regras de conclusão existentes. Utilize a seguinte configuração.

   | Definição    | Valor sugerido                               |
   | ---------- | ------------------------------------------------------- |
   | **Ações**    | **Enviar resposta de discurso** > `Ok, alarm set for {DateTime}`  |

Teste os três comandos juntos usando expressões relacionadas com diferentes comandos. (Pode alternar entre os diferentes comandos.)

- Entrada: *Definir um alarme*
- Saída: Para que horas?
- Entrada: *Ligue a televisão*
- Saída: Ok, ligar a televisão
- Entrada: *Definir um alarme*
- Saída: Para que horas?
- Entrada: *17h00*
- Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Adicionar configurações aos parâmetros de comando

Nesta secção, aprende-se mais sobre a configuração avançada dos parâmetros, incluindo:

 - Como os valores dos parâmetros podem pertencer a um conjunto definido fora da aplicação Comandos Personalizados.
 - Como adicionar cláusulas de validação nos valores dos parâmetros.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Configurar um parâmetro como entidade de catálogo externo

A funcionalidade Comandos Personalizados permite-lhe configurar parâmetros do tipo de corda para se referir a catálogos externos hospedados num ponto final web. Para que possa atualizar o catálogo externo de forma independente sem editar a aplicação Comandos Personalizados. Esta abordagem é útil nos casos em que as entradas no catálogo são numerosas.

Reutilizar o `SubjectDevice` parâmetro do `TurnOnOff` comando. A configuração atual para este parâmetro é **Aceitar entradas predefinidas do catálogo interno**. Esta configuração refere-se a uma lista estática de dispositivos na configuração do parâmetro. Transloque este conteúdo para uma fonte de dados externa que possa ser atualizada de forma independente.

Para mover o conteúdo, comece por adicionar um novo ponto final web. No painel à esquerda, vá para a secção **de pontos finais** da Web. Ali, adicione um novo ponto final da web. Utilize a seguinte configuração.

| Definição | Valor sugerido |
|----|----|
| **Nome** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Método** | **GET** |


Se o valor sugerido para o URL não funcionar para si, configuure e acolhia um ponto final web que devolve um ficheiro JSON que consiste na lista dos dispositivos que podem ser controlados. O ponto final da web deve devolver um ficheiro JSON formatado da seguinte forma:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Em seguida, aceda à página de definições de parâmetros **SubjectDevice.** Configurar as seguintes propriedades.

| Definição | Valor sugerido |
| ----| ---- |
| **Configuração** | **Aceitar entradas pré-indefinidas de catálogo externo** |                               
| **Ponto final do catálogo** | `getDevices` |
| **Método** | **GET** |

Em seguida, selecione **Guardar**.

> [!IMPORTANT]
> Não verá uma opção para configurar um parâmetro para aceitar entradas de um catálogo externo, a menos que tenha o ponto final web definido na secção **de ponto final** da Web no painel à esquerda.

Experimente selecionando **Train.** Após o final do treino, selecione **Teste** e experimente algumas interações.

* Entrada: *ligar*
* Saída: Que dispositivo pretende controlar?
* Entrada: *luzes*
* Saída: Ok, acendendo as luzes

> [!NOTE]
> Agora pode controlar todos os dispositivos alojados no ponto final da web. Mas ainda precisa de treinar a aplicação para testar as novas alterações e depois reeditar a aplicação.

### <a name="add-validation-to-parameters"></a>Adicionar validação aos parâmetros

*As validações* são construções que se aplicam a certos tipos de parâmetros que lhe permitem configurar restrições no valor do parâmetro. Pedem-lhe correções se os valores não se enquadrarem nos constrangimentos. Para obter uma lista de tipos de parâmetros que alargam a construção de validação, consulte [conceitos e definições de Comandos Personalizados](./custom-commands-references.md).

Teste as validações utilizando o `SetTemperature` comando. Utilize os seguintes passos para adicionar uma validação para o `Temperature` parâmetro.

1. No painel à esquerda, selecione o comando **SetTemperature.**
1. No painel central, selecione **Temperatura**.
1. No painel à direita, **selecione Adicione uma validação**.
1. Na nova janela **de validação,** configurar a validação como indicado na tabela seguinte. Em seguida, selecione **Criar**.


    | Configuração do parâmetro | Valor sugerido | Descrição |
    | ---- | ---- | ---- |
    | **Valor Min** | `60` | Para os parâmetros número, o valor mínimo que este parâmetro pode assumir |
    | **Valor Máximo** | `80` | Para os parâmetros número, o valor máximo que este parâmetro pode assumir |
    | **Resposta de falha** |  **Editor simples**  >  **Primeira variação** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Um pedido para pedir um novo valor se a validação falhar |

    > [!div class="mx-imgBorder"]
    > ![Screenshot mostrando como adicionar uma validação de alcance.](media/custom-commands/add-validations-temperature.png)

Experimente selecionar o ícone **train** na parte superior do painel à direita. Após o final do treino, selecione **Teste**. Experimente algumas interações:

- Entrada: *Definir a temperatura para 72 graus*
- Saída: Ok, ajustar a temperatura para 72 graus
- Entrada: *Definir a temperatura para 45 graus*
- Saída: Desculpe, só posso definir temperatura entre 60 e 80 graus
- Entrada: *faça 72 graus em vez*
- Saída: Ok, ajustar a temperatura para 72 graus

## <a name="add-interaction-rules"></a>Adicionar regras de interação

As regras de interação são regras *adicionais* que lidam com situações específicas ou complexas. Embora seja livre de autorar as suas próprias regras de interação, neste exemplo utiliza regras de interação para os seguintes cenários:

* Confirmando comandos
* Adicionar uma correção de um passo aos comandos

Para obter mais informações sobre regras de interação, consulte [conceitos e definições de Comandos Personalizados.](./custom-commands-references.md)

### <a name="add-confirmations-to-a-command"></a>Adicionar confirmações a um comando

Para adicionar uma confirmação, use o `SetTemperature` comando. Para obter confirmação, crie regras de interação utilizando os seguintes passos:

1. No painel à esquerda, selecione o comando **SetTemperature.**
1. No painel central, adicione regras de interação selecionando **Add**. Em seguida, selecione **regras de interação**  >  **Confirme o comando**.

    Esta ação adiciona três regras de interação. As regras pedem ao utilizador que confirme a data e a hora do alarme. Esperam uma confirmação (sim ou não) para a próxima curva.

    1. Modificar a regra de interação **do comando Confirmar** utilizando a seguinte configuração:
        1. Altere o nome para Confirmar a **temperatura**.
        1. Adicione uma nova condição: **Parâmetros necessários**  >  **Temperatura**.
        1. Adicione uma nova ação:  >  **Escreva Enviar a resposta** da fala Tem a certeza de que pretende definir a temperatura como graus  >  **{Temperatura} ?**
        1. Na secção **Expectativas,** deixe o valor padrão de **Confirmação esperada do utilizador**.
      
         > [!div class="mx-imgBorder"]
         > ![Screenshot mostrando como criar a resposta do parâmetro necessário.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modificar a regra **de interação de Confirmação para** lidar com uma confirmação bem sucedida (o utilizador disse que sim).
      
          1. Alterar o nome para **temperatura de confirmação conseguiu**.
          1. Deixar a **confirmação** existente foi uma condição de sucesso.
          1. Adicione uma nova condição: **Tipo**  >  **parâmetros exigidos**  >  **Temperatura**.
          1. Deixe o valor **de estado pós-execução** predefinido como **regras de conclusão executar**.

    1. Modifique a regra **de interação negada** por Confirmação para lidar com cenários quando a confirmação é negada (o utilizador disse não).

          1. Altere o nome para **temperatura de confirmação negada**.
          1. Deixar a **confirmação** existente foi negada condição.
          1. Adicione uma nova condição: **Tipo**  >  **parâmetros exigidos**  >  **Temperatura**.
          1. Adicione uma nova ação:  >  **Digite Enviar resposta de fala** Sem  >  **problema. Que temperatura então?**
          1. Altere o valor **de estado de pós-execução** predefinido **para esperar pela entrada do utilizador**.

> [!IMPORTANT]
> Neste artigo, utiliza-se a capacidade de confirmação incorporada. Também pode adicionar manualmente regras de interação uma a uma.
   
Experimente as alterações selecionando **Train**. Quando o treino terminar, selecione **Teste**.

- **Entrada**: *Definir temperatura a 80 graus*
- **Saída**: tem a certeza de que pretende definir a temperatura como 80 graus?
- **Entrada**: *Não*
- **Saída**: Sem problema. Que temperatura então?
- **Entrada**: *72 graus*
- **Saída**: tem a certeza de que pretende definir a temperatura como 72 graus?
- **Entrada**: *Sim*
- **Saída**: OK, temperatura de fixação de 72 graus

### <a name="implement-corrections-in-a-command"></a>Implementar correções num comando

Nesta secção, vai configurar uma correção de um passo. Esta correção é utilizada após a execução da ação de cumprimento. Também verá um exemplo de como uma correção é ativada por padrão se o comando ainda não estiver cumprido. Para adicionar uma correção quando o comando não estiver terminado, adicione o novo parâmetro `AlarmTone` .

No painel esquerdo, selecione o comando **SetAlarm.** Em seguida, adicione o novo parâmetro **AlarmTone**.
        
- **Nome** > `AlarmTone`
- **Tipo**  >  **Corda**
- **Valor Predefinido**  >  **Sinos**
- **Configuração**  >  **Aceitar valores de entrada predefinidos do catálogo interno**
- **Valores de entrada predefinidos**  >  **Chimes,** **Jingle** e **Echo** (Estes valores são entradas predefinidas individuais.)


Em seguida, atualize a resposta para o parâmetro **DateTime** **para Ready para definir o alarme com o tom como {AlarmTone}. Para que horas?** Em seguida, modifique a regra de conclusão da seguinte forma:

1. Selecione a regra de conclusão existente **ConfirmationResponse**.
1. No painel à direita, paire sobre a ação existente e **selecione Editar.**
1. Atualize a resposta da fala a `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` .

> [!IMPORTANT]
> O tom de alarme pode mudar sem qualquer configuração explícita num comando em curso. Por exemplo, pode mudar quando o comando ainda não terminou. Uma correção é ativada *por padrão* para todos os parâmetros de comando, independentemente do número de turno, se o comando ainda estiver por cumprir.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Implementar uma correção quando um comando estiver terminado

A plataforma Comandos Personalizados permite uma correção de um passo mesmo quando o comando está terminado. Esta funcionalidade não é ativada por defeito. Deve ser explicitamente configurado. 

Utilize os seguintes passos para configurar uma correção de um passo:

1. No comando **SetAlarm,** adicione uma regra de interação do **comando anterior** do tipo Update para atualizar o alarme previamente definido. Rebatize a regra de interação como **alarme anterior de Atualização**.
1. Deixe a condição predefinida: **O comando anterior tem de ser atualizado**.
1. Adicione uma nova condição: **Tipo**  >  **de Data de Parâmetro Exigido**  >  .
1. Adicione uma nova ação: **Tipo**  >  **Enviar resposta de discurso** Simples  >  **editor** Atualizar  >  **o tempo de alarme anterior para {DateTime}**.
1. Deixe o valor **de estado pós-execução** predefinido como **comando concluído**.

Experimente as alterações selecionando **Train**. Aguarde que o treino termine e, em seguida, selecione **Test**.

- **Entrada**: *Desemote um alarme.*
- **Saída**: Pronto para definir o alarme com tom como Chimes. Para que horas?
- **Entrada**: *Defina um alarme com o tom como Jingle para amanhã às 9 h.*
- **Saída**: OK, alarme definido para 2020-05-21 09:00:00. O tom de alarme é Jingle.
- **Entrada**: *Não, 8 da manhã.*
- **Saída**: Atualização do tempo de alarme anterior para 2020-05-29 08:00.

> [!NOTE]
> Numa aplicação real, na secção **Ações** desta regra de correção, também terá de enviar uma atividade ao cliente ou chamar um ponto final HTTP para atualizar o tempo de alarme no seu sistema. Esta ação deve ser a única responsável pela atualização da hora de alarme. Não deve ser responsável por qualquer outro atributo do comando. Neste caso, esse atributo seria o tom de alarme.

## <a name="add-language-generation-templates-for-speech-responses"></a>Adicione modelos de geração de linguagem para respostas de fala

Os modelos de geração linguística (LG) permitem-lhe personalizar as respostas enviadas ao cliente. Introduzem variação nas respostas. Pode alcançar a geração de linguagens utilizando:

* Modelos de geração de linguagem.
* Expressões adaptativas.

Os modelos de comandos personalizados são baseados nos [modelos LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)do Quadro Bot. Como a funcionalidade Comandos Personalizados cria um novo modelo LG quando necessário (para respostas de fala em parâmetros ou ações), não tem de especificar o nome do modelo LG. 

Então não precisa definir o seu modelo assim:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Em vez disso, pode definir o corpo do modelo sem o nome, assim:

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando um exemplo de editor de modelo.](./media/custom-commands/template-editor-example.png)


Esta alteração introduz variação nas respostas de fala que são enviadas ao cliente. Para uma expressão, a resposta de fala correspondente é escolhida aleatoriamente das opções fornecidas.

Ao tirar partido dos modelos LG, também pode definir respostas complexas de fala para comandos usando expressões adaptativas. Para obter mais informações, consulte o [formato dos modelos LG.](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) 

Por predefinição, a funcionalidade Comandos Personalizados suporta todas as capacidades, com as seguintes pequenas diferenças:

* Nos modelos LG, as entidades são representadas como `${entityName}` . A funcionalidade Comandos Personalizados não utiliza entidades. Mas pode usar parâmetros como variáveis com a `${parameterName}` representação ou a `{parameterName}` representação.
* A funcionalidade Comandos Personalizados não suporta a composição e expansão do modelo, porque nunca edita diretamente o ficheiro *.lg.* Edita apenas as respostas de modelos criados automaticamente.
* A função Comandos Personalizados não suporta funções personalizadas que a LG injeta. As funções predefinidas são suportadas.
* A funcionalidade Comandos Personalizados não suporta opções, tais `strict` `replaceNull` como, e `lineBreakStyle` .

### <a name="add-template-responses-to-a-turnonoff-command"></a>Adicionar respostas de modelo a um comando TurnOnOff

Modifique o `TurnOnOff` comando para adicionar um novo parâmetro. Utilize a seguinte configuração.

| Definição            | Valor sugerido       | 
| ------------------ | --------------------- | 
| **Nome**               | `SubjectContext`         | 
| **É Global**          | Não selecionado             | 
| **Obrigatório**           | Não selecionado               | 
| **Tipo**               | **String**                |
| **Valor predefinido**      | `all` |
| **Configuração**      | **Aceitar valores de entrada predefinidos do catálogo interno** | 
| **Valores de entrada predefinidos** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Modificar uma regra de conclusão

Editar a secção **Ações** da regra de conclusão existente **ConfirmationResponse**. Na janela **de ação editar,** mude para **Editor de Modelos.** Em seguida, substitua o texto pelo seguinte exemplo.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Treine e teste a sua aplicação utilizando a seguinte entrada e saída. Reparem na variação das respostas. A variação é criada por múltiplas alternativas ao valor do modelo e também pelo uso de expressões adaptativas.

* Entrada: *ligue a televisão*
* Saída: Ok, ligar a televisão
* Entrada: *ligue a televisão*
* Saída: Feito, ligado na televisão
* Entrada: *apare pelas luzes*
* Saída: Ok, apagar todas as luzes
* Entrada: *desligue as luzes do quarto*
* Saída: Ok, desligar as luzes do quarto

## <a name="use-a-custom-voice"></a>Use uma voz personalizada

Outra forma de personalizar as respostas de Comandos Personalizados é selecionar uma voz de saída. Utilize os seguintes passos para mudar a voz predefinida para uma voz personalizada:

1. Na sua aplicação Comandos Personalizados, no painel à esquerda, selecione **Definições**.
1. No painel do meio, selecione **Custom Voice**.
1. Na tabela, selecione uma voz personalizada ou uma voz pública.
1. Selecione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando frases e parâmetros da amostra.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> Para vozes públicas, os tipos neurais estão disponíveis apenas para regiões específicas. Para obter mais informações, consulte [as regiões apoiadas pelo serviço speech.](./regions.md#standard-and-neural-voices)
>
> Pode criar vozes personalizadas na página do projeto **Custom Voice.** Para mais informações, consulte [Começar com a Voz Personalizada.](./how-to-custom-voice.md)

Agora a aplicação responderá na voz selecionada, em vez da voz predefinido.

## <a name="next-steps"></a>Passos seguintes

* Saiba como integrar a [sua aplicação De Comandos Personalizados](how-to-custom-commands-setup-speech-sdk.md) com uma aplicação de clientes utilizando o Speech SDK.
* [Confiúva a implementação contínua](how-to-custom-commands-deploy-cicd.md) para a sua aplicação de Comandos Personalizados utilizando Azure DevOps. 
                      
