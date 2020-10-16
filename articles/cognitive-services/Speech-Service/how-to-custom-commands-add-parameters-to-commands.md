---
title: 'Como: Adicionar comandos simples à aplicação comandos personalizados - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, aprende-se a adicionar parâmetros aos Comandos Personalizados
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0ed237debc2395ed307658b2d57a541574f9478a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284154"
---
# <a name="add-parameters-to-commands"></a>Adicionar parâmetros aos comandos

Neste artigo, aprende-se a adicionar parâmetros aos Comandos Personalizados. Os parâmetros são informações necessárias pelos comandos para completar uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que desencadeiam ações personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * [Como: Criar aplicação com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>Configurar parâmetros para o comando TurnOn

Edite o comando **TurnOn** existente para ligar e desligar vários dispositivos.

1. Como o comando irá agora lidar com o cenário dentro e fora, renomear o Comando para **TurnOnOff**.
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
       | Tipo               | Cadeia          | O tipo de parâmetro, como Número, Cadeia, Hora da Data ou Geografia   |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | Para cordas, isto limita as entradas a um conjunto de valores possíveis |
       | Valores de entrada predefinidos     | `on`, `off`           | Conjunto de valores possíveis e seus pseudónimos         |
       
        
   1. Para adicionar valores de entrada predefinidos, **selecione Adicione uma entrada predefinida** e na janela New **Item,**  digite **o nome** conforme indicado na tabela acima. Neste caso, não estamos a usar pseudónimos, por isso podes deixá-lo em branco.
   
      > [!div class="mx-imgBorder"]
      > ![Criar parâmetro](media/custom-commands/create-on-off-parameter.png)

   1. **Selecione Guardar** para guardar todas as configurações do parâmetro.
 
 ### <a name="add-subjectdevice-parameter"></a>Adicionar o parâmetro SubjectDevice 

   1. Em seguida, **selecione Adicione** novamente para adicionar um segundo parâmetro para representar o nome dos dispositivos que podem ser controlados usando este comando. Utilize a seguinte configuração.
   

       | Definição            | Valor sugerido       |
       | ------------------ | --------------------- |
       | Nome               | `SubjectDevice`         |
       | É Global          | descontrolado             |
       | Necessário           | verificado               |
       | Resposta para parâmetro requerido     | Editor simples > `Which device do you want to control?`    | 
       | Tipo               | Cadeia                |          |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | 
       | Valores de entrada predefinidos | `tv`, `fan`               |
       | Pseudónimos `tv`      | `television`, `telly`     |

   1. Selecione **Guardar**

### <a name="modify-example-sentences"></a>Modificar frases de exemplo

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

### <a name="modify-completion-rules-to-include-parameters"></a>Modificar as regras de conclusão para incluir parâmetros

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

### <a name="try-it-out"></a>Experimente
1. Selecione o ícone **do comboio** em cima do painel direito.

1. Quando o treino terminar, selecione **Teste**. Aparecerá uma janela **de teste.**
 Tente algumas interações.

    - Entrada: desligue a televisão
    - Saída: Ok, desligar a televisão
    - Entrada: desligue a televisão
    - Saída: Ok, desligar a televisão
    - Entrada: desligá-lo
    - Saída: Que dispositivo pretende controlar?
    - Entrada: a televisão
    - Saída: Ok, desligar a televisão

## <a name="configure-parameters-for-settemperature-command"></a>Configurar parâmetros para o comando SetTemperature

Modificar o comando **SetTemperature** para lhe permitir definir a temperatura como direcionado pelo utilizador.

Adicionar novo parâmetro **Temperatura** com a seguinte configuração

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| Nome               | `Temperature`           |
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

### <a name="try-it-out"></a>Experimente

**Treine** e **teste** as mudanças com algumas interações.

- Entrada: Definir temperatura
- Saída: Que temperatura quer?
- Entrada: 50 graus
- Saída: Ok, ajustar a temperatura para 50 graus

## <a name="configure-parameters-to-the-setalarm-command"></a>Configurar parâmetros para o comando SetAlarm

Adicione um parâmetro chamado **DateTime** com a seguinte configuração.

   | Definição                           | Valor sugerido                     | 
   | --------------------------------- | ----------------------------------------|
   | Nome                              | `DateTime`                               |
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


### <a name="try-it-out"></a>Experimente

**Treine** e **teste** as mudanças.
- Entrada: Definir o alarme para amanhã ao meio-dia
- Saída: Ok, alarme definido para 2020-05-02 12:00:00
- Entrada: Definir um alarme
- Saída: A que horas?
- Entrada: 17h
- Saída: Ok, alarme definido para 2020-05-01 17:00:00


## <a name="try-out-all-the-commands"></a>Experimente todos os comandos

Teste os três comandos juntos usando expressões relacionadas com diferentes comandos. Note que pode alternar entre os diferentes comandos.

- Entrada: Definir um alarme
- Saída: Para que horas?
- Entrada: Ligue a televisão
- Saída: Ok, ligar a televisão
- Entrada: Definir um alarme
- Saída: Para que horas?
- Entrada: 17h
- Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar configurações aos parâmetros de comandos](./how-to-custom-commands-add-parameter-configuration.md)
