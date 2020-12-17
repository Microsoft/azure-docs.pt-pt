---
title: 'Como fazer: Desenvolver aplicações de comandos personalizados - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste como fazer, aprende-se a desenvolver e personalizar aplicações de Comandos Personalizados. Os Comandos Personalizados facilitam a construção de aplicações de comando de voz ricas otimizadas para experiências de interação de voz e é mais adequada para a conclusão de tarefas ou cenários de comando e controlo, particularmente bem combinados para dispositivos internet of things (IoT), dispositivos ambiente e sem cabeça.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632959"
---
# <a name="develop-custom-commands-applications"></a>Desenvolver aplicações de comandos personalizados

Neste como fazer, aprende-se a desenvolver e configurar aplicações de Comandos Personalizados. Os Comandos Personalizados facilitam a construção de aplicações de comando de voz ricas otimizadas para experiências de interação de voz e é mais adequada para a conclusão de tarefas ou cenários de comando e controlo, particularmente bem combinados para dispositivos internet of things (IoT), dispositivos ambiente e sem cabeça.

Neste artigo, cria-se uma aplicação que pode ligar e desligar uma TV, definir a temperatura e definir um alarme. Depois de criar estes comandos básicos, as seguintes opções para personalizar comandos são cobertas:

* Adicionar parâmetros aos comandos
* Adicionar configurações aos parâmetros de comando
* Regras de interação de edifícios
* Criação de modelos de geração de linguagem para respostas de fala
* Usando a voz personalizada 

## <a name="create-application-with-simple-commands"></a>Criar uma aplicação com comandos simples

Primeiro, comece por criar uma aplicação de Comandos Personalizados vazios. Para mais detalhes, consulte o [arranque rápido](quickstart-custom-commands-application.md). Desta vez, em vez de importar um projeto, cria-se um projeto em branco.

1. Na caixa **Nome,** insira o nome do projeto como `Smart-Room-Lite` (ou outra coisa à sua escolha).
1. Na lista **de línguas,** selecione **Inglês (Estados Unidos)**.
1. Selecione ou crie um recurso LUIS à sua escolha.

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Atualizar os recursos LUIS (opcional)

Pode atualizar o recurso de autoria que selecionou na janela do **projeto Novo** e definir um recurso de previsão. O recurso de previsão é utilizado para reconhecimento quando a sua aplicação de Comandos Personalizados é publicada. Não precisa de um recurso de previsão durante as fases de desenvolvimento e teste.

### <a name="add-turnon-command"></a>Adicionar comando TurnOn

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

#### <a name="add-example-sentences"></a>Adicionar frases de exemplo

Vamos começar com a secção **de frases exemplo,** e fornecer um exemplo do que o utilizador pode dizer.

1. Selecione A secção **de frases** exemplo no painel central.
1. No painel mais direito, adicione exemplos:

    ```
    turn on the tv
    ```

1.  **Selecione Guarde** na parte superior do painel.

Por enquanto, não temos parâmetros, por isso podemos passar para a secção **de regras de conclusão.**

#### <a name="add-a-completion-rule"></a>Adicione uma regra de conclusão

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

### <a name="add-settemperature-command"></a>Adicionar comando SetTemperature

Agora, adicione mais um comando **SetTemperature** que levará uma única expressão, `set the temperature to 40 degrees` e responderá com a mensagem `Ok, setting temperature to 40 degrees` .

Siga os passos como ilustrado para o comando **TurnOn** para criar um novo comando usando a frase exemplo, "**definir a temperatura para 40 graus**".

Em seguida, edite as regras de conclusão **existentes:**

| Definição    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| Name  | ConfirmaçãoResponse                  |
| Condições | Nenhum                                     |
| Ações    | Envie resposta de fala > editor simples > Primeira variação > `Ok, setting temperature to 40 degrees` |

**Selecione Guardar** para guardar todas as alterações no comando.

### <a name="add-setalarm-command"></a>Adicionar comando SetAlarm

Crie um novo **Comando SetAlarm** usando a frase exemplo",**acende um alarme para as 9 da manhã de amanhã**". Em seguida, edite as regras de conclusão **existentes:**

| Definição    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| Nome da Regra  | ConfirmaçãoResponse                  |
| Condições | Nenhum                                     |
| Ações    | Envie resposta à fala > editor simples > Primeira variação >`Ok, setting an alarm for 9 am tomorrow` |

**Selecione Guardar** para guardar todas as alterações no comando.

### <a name="try-it-out"></a>Experimente

Teste o comportamento utilizando o painel de chat test. Selecione o ícone **do comboio** presente no topo do painel direito. Uma vez concluído o treino, selecione **Teste**. Experimente os seguintes exemplos de expressão por voz ou texto:

- Você escreve: definir a temperatura para 40 graus
- Resposta esperada: Ok, ajustar a temperatura para 40 graus
- Escreve: ligue a televisão
- Resposta esperada: Ok, ligar a televisão
- Escreve: desemote um alarme para as 9:00 de amanhã
- Resposta esperada: Ok, a definir um alarme para as 9:00 de amanhã

> [!div class="mx-imgBorder"]
> ![Teste com web chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> No painel de teste, pode selecionar **Dar informações** sobre como esta entrada de voz/texto foi processada.

## <a name="add-parameters-to-commands"></a>Adicionar parâmetros aos comandos

Nesta secção, aprende-se a adicionar parâmetros aos seus comandos. Os parâmetros são informações necessárias pelos comandos para completar uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que desencadeiam ações personalizadas.

### <a name="configure-parameters-for-turnon-command"></a>Configurar parâmetros para o comando TurnOn

Comece por editar o comando **TurnOn** existente para ligar e desligar vários dispositivos.

1. Agora que o comando irá agora lidar com cenários dentro e fora, rebatize o comando para **TurnOnOff**.
   1. No painel esquerdo, selecione o comando **TurnOn** e, em seguida, selecione o botão elipse (...) ao lado do **comando Novo** na parte superior do painel.
   
   1. Selecione **Renomear**. Nas **janelas de comando do Renome,** **altere o Nome** para **TurnOnOff**.

1. Em seguida, adicione um novo parâmetro a este comando que representa se o utilizador quer ligar ou desligar o dispositivo.
   1. **Selecione Adicione** presente na parte superior do painel central. A partir do drop-down, selecione **Parâmetro**.
   1. No painel direito, na secção **Parâmetros,** adicione valor na caixa **Nome** como **OnOff**.
   1. **Selecione Necessário**. Na resposta Add para uma janela **de parâmetros necessária,** selecione **Editor Simples**. Na **Primeira Variação**, adicione
        ```
        On or Off?
        ```
   1. Selecione **Atualizar**.

       > [!div class="mx-imgBorder"]
       > ![Criar resposta de parâmetros necessária](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Agora configuramos as propriedades dos parâmetros. Para obter explicação de todas as propriedades de configuração de um comando, vá a [referências](./custom-commands-references.md). Configure as propriedades do parâmetro da seguinte forma:
      

       | Configuração      | Valor sugerido     | Descrição                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nome               | `OnOff`           | Um nome descritivo para parâmetro                                                                           |
       | É Global          | descontrolado       | Caixa de verificação indicando se um valor para este parâmetro é aplicado globalmente a todos os Comandos na aplicação|
       | Necessário           | verificado         | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o Comando |
       | Resposta para parâmetro requerido      |Editor simples > `On or Off?`      | Um pedido para pedir o valor deste parâmetro quando não se sabe |
       | Tipo               | String          | O tipo de parâmetro, como Número, Cadeia, Hora da Data ou Geografia   |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | Para cordas, isto limita as entradas a um conjunto de valores possíveis |
       | Valores de entrada predefinidos     | `on`, `off`           | Conjunto de valores possíveis e seus pseudónimos         |
       
        
   1. Para adicionar valores de entrada predefinidos, **selecione Adicione uma entrada predefinida** e na janela New **Item,**  digite **o nome** conforme indicado na tabela acima. Neste caso, não estamos a usar pseudónimos, por isso podes deixá-lo em branco.
   
      > [!div class="mx-imgBorder"]
      > ![Criar parâmetro](media/custom-commands/create-on-off-parameter.png)

   1. **Selecione Guardar** para guardar todas as configurações do parâmetro.
 
#### <a name="add-subjectdevice-parameter"></a>Adicionar o parâmetro SubjectDevice 

1. Em seguida, **selecione Adicione** novamente para adicionar um segundo parâmetro para representar o nome dos dispositivos que podem ser controlados usando este comando. Utilize a seguinte configuração.


    | Definição            | Valor sugerido       |
    | ------------------ | --------------------- |
    | Name               | `SubjectDevice`         |
    | É Global          | descontrolado             |
    | Necessário           | verificado               |
    | Resposta para parâmetro requerido     | Editor simples > `Which device do you want to control?`    | 
    | Tipo               | String                |          |
    | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | 
    | Valores de entrada predefinidos | `tv`, `fan`               |
    | Pseudónimos `tv`      | `television`, `telly`     |

1. Selecione **Guardar**

#### <a name="modify-example-sentences"></a>Modificar frases de exemplo

Para comandos com parâmetros, é útil adicionar frases de exemplo que cobrem todas as combinações possíveis. Por exemplo:

* Informação completa do parâmetro - `turn {OnOff} the {SubjectDevice}`
* Informação parcial do parâmetro - `turn it {OnOff}`
* Sem informação sobre parâmetros - `turn something`

Frases de exemplo com diferente grau de informação permitem que a aplicação de Comandos Personalizados resolva resoluções de um tiro e resoluções de várias voltas com informações parciais.

Com isso em mente, edite as frases de exemplo para usar os parâmetros como sugerido abaixo:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Selecione **Guardar**.

> [!TIP]
> No Exemplo, o editor usa aparelhos encaracolados para se referir aos seus parâmetros. - `turn {OnOff} the {SubjectDevice}` Utilize o separador para a conclusão automática apoiado por parâmetros previamente criados.

#### <a name="modify-completion-rules-to-include-parameters"></a>Modificar as regras de conclusão para incluir parâmetros

Modificar a regra de conclusão existente **ConfirmaçãoResponse**.

1. Na secção **Condições,** **selecione Adicionar uma condição**.
1. Na janela **New Condition,** na lista **Tipo,** selecione **os parâmetros necessários**. Na lista de verificação abaixo, verifique o **OnOff** e **o SubjectDevice**.
1. Deixe **isGlobal** como descontrolado.
1. Selecione **Criar**.
1. Na secção **Ações,** edite a ação de **resposta da fala enviar** por parte da ação e selecione o botão de edição. Desta vez, faça uso dos parâmetros **OnOff** e **SubjectDevice** recém-criados

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Selecione **Guardar**.

Experimente as alterações selecionando o ícone **train** em cima do painel direito. Quando o treino terminar, selecione **Teste**. Aparecerá uma janela **de teste.** Experimente as seguintes interações.

- Entrada: desligue a televisão
- Saída: Ok, desligar a televisão
- Entrada: desligue a televisão
- Saída: Ok, desligar a televisão
- Entrada: desligá-lo
- Saída: Que dispositivo pretende controlar?
- Entrada: a televisão
- Saída: Ok, desligar a televisão

### <a name="configure-parameters-for-settemperature-command"></a>Configurar parâmetros para o comando SetTemperature

Modificar o comando **SetTemperature** para lhe permitir definir a temperatura como direcionado pelo utilizador.

Adicionar novo parâmetro **Temperatura** com a seguinte configuração

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| Name               | `Temperature`           |
| Necessário           | verificado         |
| Resposta para parâmetro requerido      | Editor simples > `What temperature would you like?`
| Tipo               | Número          |


Edite as expressões de exemplo para os seguintes valores.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Editar as regras de conclusão existentes de acordo com a seguinte configuração.

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| Condições         | Parâmetro necessário > Temperatura           |
| Ações           | Envie > de resposta à fala `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-to-the-setalarm-command"></a>Configurar parâmetros para o comando SetAlarm

Adicione um parâmetro chamado **DateTime** com a seguinte configuração.

   | Definição                           | Valor sugerido                     | 
   | --------------------------------- | ----------------------------------------|
   | Name                              | `DateTime`                               |
   | Necessário                          | verificado                                 |
   | Resposta para parâmetro requerido   | Editor simples > `For what time?`            | 
   | Tipo                              | DateTime                                |
   | Predefinições de data                     | Se faltar a data de utilização hoje            |
   | Incumprimentos do tempo                     | Se falta tempo, use o início do dia     |


> [!NOTE]
> Neste artigo, fizemos predominantemente uso de tipos de parâmetros de cadeia, número e datatime. Para a lista de todos os tipos de parâmetros suportados e suas propriedades, vá a [referências](./custom-commands-references.md).


Editar palavras de exemplo para os seguintes valores.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Editar as regras de conclusão existentes de acordo com a seguinte configuração.

   | Definição    | Valor sugerido                               |
   | ---------- | ------------------------------------------------------- |
   | Ações    | Enviar resposta de discurso - `Ok, alarm set for {DateTime}`  |

Teste os três comandos juntos usando expressões relacionadas com diferentes comandos. Note que pode alternar entre os diferentes comandos.

- Entrada: Definir um alarme
- Saída: Para que horas?
- Entrada: Ligue a televisão
- Saída: Ok, ligar a televisão
- Entrada: Definir um alarme
- Saída: Para que horas?
- Entrada: 17h
- Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="add-configurations-to-commands-parameters"></a>Adicionar configurações aos parâmetros dos comandos

Nesta secção, aprende-se mais sobre a configuração avançada dos parâmetros, incluindo:

 - Como os valores dos parâmetros podem pertencer a um conjunto definido externamente à aplicação de comandos personalizados
 - Como adicionar cláusulas de validação sobre o valor dos parâmetros

### <a name="configure-parameter-as-external-catalog-entity"></a>Configure o parâmetro como entidade de catálogo externo

Os Comandos Personalizados permitem configurar parâmetros do tipo de cordas para se referir a catálogos externos hospedados num ponto final web. Isto permite-lhe atualizar o catálogo externo de forma independente sem fazer edições para a aplicação Comandos Personalizados. Esta abordagem é útil nos casos em que as entradas no catálogo podem ser grandes em número.

Reutilizar o parâmetro **SubjectDevice** a partir do comando **TurnOnOff.** A configuração atual para este parâmetro é **Aceitar entradas predefinidas do catálogo interno**. Isto refere-se à lista estática de dispositivos tal como definidos na configuração do parâmetro. Queremos transferir este conteúdo para uma fonte de dados externa que possa ser atualizada de forma independente.

Para isso, comece por adicionar um novo ponto final web. Vá à secção **de pontos finais** da Web no painel esquerdo e adicione um novo ponto final web com a seguinte configuração.

| Definição | Valor sugerido |
|----|----|
| Name | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Método | GET |


Se o valor sugerido para URL não funcionar para si, tem de configurar e hospedar um simples ponto final web que devolve um json constituído por uma lista dos dispositivos que podem ser controlados. O ponto final da web deve devolver um json formatado da seguinte forma:
    
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

Em seguida, vá à página de definições de parâmetros **SubjectDevice** e altere as propriedades para as seguintes.

| Definição | Valor sugerido |
| ----| ---- |
| Configuração | Aceitar entradas pré-indefinidas de catálogo externo |                               
| Ponto final do catálogo | obterDevices |
| Método | GET |

Em seguida, **selecione Save**.

> [!IMPORTANT]
> Não verá uma opção para configurar um parâmetro para aceitar entradas de um catálogo externo, a menos que tenha o ponto final web definido na secção **de ponto final** da Web no painel esquerdo.

Experimente-o selecionando **Train** e espere pela conclusão do treino. Uma vez concluído o treino, selecione **Teste** e experimente algumas interações.

* Entrada: ligar
* Saída: Que dispositivo pretende controlar?
* Entrada: luzes
* Saída: Ok, acendendo as luzes

> [!NOTE]
> Note como pode controlar todos os dispositivos alojados no ponto final da web agora. Ainda precisa de treinar a aplicação para testar as novas alterações e voltar a publicar a aplicação.

### <a name="add-validation-to-parameters"></a>Adicionar validação aos parâmetros

**As validações** são construções aplicáveis a certos tipos de parâmetros que lhe permitem configurar restrições no valor do parâmetro, e solicitar a correção se os valores não se enquadrarem nas restrições. Para a lista completa de tipos de parâmetros que alargam a construção de validação, vá a [referências](./custom-commands-references.md)

Teste as validações utilizando o comando **SetTemperature.** Utilize os seguintes passos para adicionar uma validação para o parâmetro **temperatura.**

1. Selecione o comando **SetTemperature** no painel esquerdo.
1. Selecione  **Temperatura** no painel central.
1. **Selecione Adicione uma validação** presente no painel direito.
1. Na nova janela **de validação,** configurar a validação da seguinte forma e selecionar **Criar**.


    | Configuração de parâmetros | Valor sugerido | Descrição |
    | ---- | ---- | ---- |
    | Valor Mínimo | `60` | Para os parâmetros número, o valor mínimo que este parâmetro pode assumir |
    | Valor Máximo | `80` | Para os parâmetros número, o valor máximo que este parâmetro pode assumir |
    | Resposta de falha |  Editor simples > Primeira Variação > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | A prompt para pedir um novo valor se a validação falhar |

    > [!div class="mx-imgBorder"]
    > ![Adicionar uma validação de gama](media/custom-commands/add-validations-temperature.png)

Experimente-o selecionando o ícone **train** presente no topo do painel direito. Uma vez concluído o treino, selecione **Teste** e experimente algumas interações:

- Entrada: Definir a temperatura para 72 graus
- Saída: Ok, ajustar a temperatura para 72 graus
- Entrada: Definir a temperatura para 45 graus
- Saída: Desculpe, só posso definir temperatura entre 60 e 80 graus
- Entrada: faça 72 graus em vez
- Saída: Ok, ajustar a temperatura para 72 graus

## <a name="add-interaction-rules"></a>Adicionar regras de interação

As regras de interação são *regras adicionais* para lidar com situações específicas ou complexas. Enquanto você é livre de autorizar suas próprias regras de interação personalizada, neste exemplo você faz uso de regras de interação para os seguintes cenários direcionados:

* Confirmando comandos
* Adicionar uma correção de um passo aos comandos

Para saber mais sobre regras de interação, aceda à secção [de referências.](./custom-commands-references.md)

### <a name="add-confirmations-to-a-command"></a>Adicionar confirmações a um comando

Para adicionar uma confirmação, utilize o comando **SetTemperature.** Para obter confirmação, cria regras de interação utilizando os seguintes passos.

1. Selecione o **comando SetTemperature** no painel esquerdo.
1. Adicione as regras de interação selecionando **Adicione** no painel central. Em seguida, selecione **regras de interação**  >  **Confirme o comando**.

    Esta ação adiciona três regras de interação que pedirão ao utilizador que confirme a data e hora do alarme e espera uma confirmação (sim/não) para a próxima volta.

    1. Modificar a regra de interação **do comando Confirmar** de acordo com a seguinte configuração:
        1. **Nome** do nome para **confirmar a temperatura**.
        1. Adicione uma nova condição como **parâmetros necessários**  >  **Temperatura**.
        1. Adicione uma nova ação como resposta **de** fala Type  >  **Send**  >  **Tem a certeza de que pretende definir a temperatura como graus {Temperatura} ?**
        1. Deixe o valor padrão de **Espera de confirmação do utilizador** na secção **Expectativas.**
      
         > [!div class="mx-imgBorder"]
         > ![Criar resposta de parâmetros necessária](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modificar a regra **de interação de Confirmação conseguiu** lidar com uma confirmação bem sucedida (o utilizador disse que sim).
      
          1. Modificar **Nome** para **a temperatura de confirmação conseguiu**.
          1. Deixar a confirmação já existente foi uma condição **de sucesso.**
          1. Adicione uma nova condição como  >  **parâmetros tipo exigidos**  >  **Temperatura**.
          1. Deixe o valor padrão do **estado pós-execução** como regras de **conclusão executar**.

    1. Modifique a regra **de interação negada** por Confirmação para lidar com cenários quando a confirmação é negada (o utilizador disse não).

          1. Modificar **Nome** para **a temperatura de confirmação negada**.
          1. Deixe a **confirmação** já existente foi negada condição.
          1. Adicione uma nova condição como  >  **parâmetros tipo exigidos**  >  **Temperatura**.
          1. Adicione uma nova ação como **Tipo**  >  **Enviar resposta de fala** Sem  >  **problema. Que temperatura então?**
          1. Deixe o valor predefinido do **estado de pós-execução** como **Esperar pela entrada do utilizador**.

> [!IMPORTANT]
> Neste artigo, usou a capacidade de confirmação incorporada. Também pode adicionar manualmente regras de interação uma a uma.
   
Experimente as alterações selecionando **Train,** aguarde que o treino termine e selecione **Test**.

- **Entrada**: Definir temperatura a 80 graus
- **Saída**: tem a certeza de que pretende definir a temperatura como 80 graus?
- **Entrada**: Não
- **Saída**: Sem problema. Que temperatura então?
- **Entrada**: 72 graus
- **Saída**: tem a certeza de que pretende definir a temperatura como 72 graus?
- **Entrada**: Sim
- **Saída**: OK, temperatura de fixação de 83 graus

### <a name="implement-corrections-in-a-command"></a>Implementar correções num comando

Nesta secção, configura-se uma correção de um passo, que é utilizada após a execução da ação de cumprimento. Também vê um exemplo de como uma correção é ativada por padrão no caso de o comando ainda não estar cumprido. Para adicionar uma correção quando o comando não estiver concluído, adicione o novo parâmetro **AlarmTone**.

Selecione o comando **SetAlarm** a partir do painel esquerdo e adicione o novo parâmetro **AlarmTone**.
        
- **Nome**  >  **AlarmTone**
- **Tipo**  >  **Corda**
- **Valor Predefinido**  >  **Sinos**
- **Configuração**  >  **Aceitar valores de entrada predefinidos do catálogo interno**
- **Valores de entrada predefinidos**  >  **Chimes,** **Jingle,** e **Echo** como entradas predefinidas individuais


Em seguida, atualize a resposta para o parâmetro **DateTime** **para Ready para definir o alarme com o tom como {AlarmTone}. Para que horas?** Em seguida, modifique a regra de conclusão da seguinte forma:

1. Selecione a regra de conclusão existente **ConfirmationResponse**.
1. No painel direito, paire sobre a ação existente e **selecione Editar.**
1. Atualize a resposta da fala para **OK, definido de alarme para {DateTime}. O sinal de alarme é {AlarmTone}.**

> [!IMPORTANT]
> O tom de alarme poderia ser alterado sem qualquer configuração explícita num comando contínuo, por exemplo, quando o comando ainda não estava terminado. *Uma correção é ativada por padrão para todos os parâmetros de comando, independentemente do número de turno se o comando ainda estiver por cumprir.*

#### <a name="correction-when-command-is-completed"></a>Correção quando o comando estiver concluído

A plataforma Comandos Personalizados também fornece a capacidade para uma correção de um passo, mesmo quando o comando foi concluído. Esta funcionalidade não é ativada por defeito. Deve ser explicitamente configurado. Utilize os seguintes passos para configurar uma correção de um passo.

1. No comando **SetAlarm,** adicione uma regra de interação do **comando anterior** do tipo Update para atualizar o alarme previamente definido. Mude o  nome predefinido da regra de interação para **atualizar o alarme anterior**.
1. Deixar a condição **predefinida O comando anterior tem de ser atualizado** como está.
1. Adicione uma nova condição como hora de data do parâmetro exigido do **tipo**  >    >  .
1. Adicione uma nova ação como **Tipo**  >  **Enviar resposta de discurso** Simples  >  **editor** Atualizar o  >  **tempo de alarme anterior para {DateTime}.**
1. Deixe o valor padrão do **estado de pós-execução** como **comando concluído**.

Experimente as alterações selecionando **Train,** aguarde que o treino termine e selecione **Test**.

- **Entrada**: Desemote um alarme.
- **Saída**: Pronto para definir o alarme com tom como Chimes. Para que horas?
- **Entrada**: Defina um alarme com o tom como Jingle para amanhã às 9 h.
- **Saída**: OK, alarme definido para 2020-05-21 09:00:00. O tom de alarme é Jingle.
- **Entrada**: Não, 8 da manhã.
- **Saída**: Atualização do tempo de alarme anterior para 2020-05-29 08:00.

> [!NOTE]
> Numa aplicação real, na secção **Ações** desta regra de correção, também terá de enviar uma atividade ao cliente ou chamar um ponto final HTTP para atualizar o tempo de alarme no seu sistema. Esta ação deve ser exclusivamente responsável pela atualização da hora de alarme e não de qualquer outro atributo do comando. Neste caso, seria o sinal de alarme.

## <a name="add-language-generation-templates-for-speech-responses"></a>Adicionar modelos de geração de linguagem para respostas de voz

Os modelos de geração de idiomas permitem-lhe personalizar as respostas enviadas ao cliente e introduzir variação nas respostas. A personalização da geração linguística pode ser alcançada por:

* Utilização de modelos de geração de linguagem
* Utilização de expressões adaptativas

Os modelos de comandos personalizados são baseados nos [modelos LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)do BotFramework. Uma vez que os Comandos Personalizados criam um novo modelo LG quando necessário (isto é, para respostas de fala em parâmetros ou ações) não tem de especificar o nome do modelo LG. Então, em vez de definir o seu modelo como:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Basta definir o corpo do modelo sem o nome, da seguinte forma.

> [!div class="mx-imgBorder"]
> ![exemplo de editor de modelo](./media/custom-commands/template-editor-example.png)


Esta alteração introduz variação nas respostas de fala enviadas ao cliente. Assim, para a mesma expressão, a resposta de discurso correspondente seria escolhida aleatoriamente das opções fornecidas.

Tirar partido dos modelos LG também permite definir respostas complexas de fala para comandos usando expressões adaptativas. Pode consultar o [formato dos modelos LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) para mais detalhes. Os Comandos Personalizados por predefinição suportam todas as capacidades com as seguintes diferenças menores:

* Nas entidades de modelos LG as entidades são representadas como ${entityName}. Em Comandos Personalizados não usamos entidades, mas os parâmetros podem ser usados como variáveis com qualquer uma destas representações ${parâmetroName} ou {parâmetroName}
* A composição e expansão do modelo não são suportadas em Comandos Personalizados. Isto porque nunca edita o `.lg` ficheiro diretamente, mas apenas as respostas dos modelos criados automaticamente.
* As funções personalizadas injetadas pela LG não são suportadas em Comandos Personalizados. As funções predefinidas ainda são suportadas.
* As opções (estrita, substituirNull & lineBreakStyle) não são suportadas em Comandos Personalizados.

### <a name="add-template-responses-to-turnonoff-command"></a>Adicionar respostas de modelo ao comando TurnOnOff

Modificar o comando **TurnOnOff** para adicionar um novo parâmetro com a seguinte configuração:

| Definição            | Valor sugerido       | 
| ------------------ | --------------------- | 
| Name               | `SubjectContext`         | 
| É Global          | descontrolado             | 
| Necessário           | descontrolado               | 
| Tipo               | String                |
| Valor predefinido      | `all` |
| Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | 
| Valores de entrada predefinidos | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>Modifique a regra de conclusão

Editar a secção **Ações** da regra de conclusão existente **ConfirmationResponse**. No pop-up de **ação editar,** mude para Editor de **Modelo** e substitua o texto pelo seguinte exemplo.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Treine** e **teste** a sua aplicação da seguinte forma. Note a variação de respostas devido ao uso de múltiplas alternativas do valor do modelo, e também o uso de expressões adaptativas.

* Entrada: ligue a televisão
* Saída: Ok, ligar a televisão
* Entrada: ligue a televisão
* Saída: Feito, ligado na televisão
* Entrada: apare pelas luzes
* Saída: Ok, apagar todas as luzes
* Entrada: desligue as luzes do quarto
* Saída: Ok, desligar as luzes do quarto

## <a name="use-custom-voice"></a>Utilizar a Voz Personalizada

Outra forma de personalizar as respostas de Comandos Personalizados é selecionar uma voz de saída personalizada. Utilize os seguintes passos para mudar a voz predefinida para uma voz personalizada.

1. Na sua aplicação de comandos personalizados, selecione **Definições** a partir do painel esquerdo.
1. Selecione **Voz Personalizada** a partir do painel do meio.
1. Selecione o costume ou a voz pública desejada da mesa.
1. Selecione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Frases de amostra com parâmetros](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Para **vozes públicas,** **os tipos neurais** só estão disponíveis para regiões específicas. Para verificar a disponibilidade, consulte [vozes padrão e neurais por região/ponto final.](./regions.md#standard-and-neural-voices)
> - Para **vozes personalizadas,** podem ser criadas a partir da página do projeto Custom Voice. Ver [Começar com Voz Personalizada.](./how-to-custom-voice.md)

Agora a aplicação responderá na voz selecionada, em vez da voz predefinido.

## <a name="next-steps"></a>Passos seguintes

* Saiba como integrar a [sua aplicação De Comandos Personalizados](how-to-custom-commands-setup-speech-sdk.md) com uma aplicação de cliente utilizando o Speech SDK.
* [Confiem a implementação contínua](how-to-custom-commands-deploy-cicd.md) para a sua aplicação de Comandos Personalizados com Azure DevOps. 
                      