---
title: 'Como: Adicionar uma confirmação a um comando personalizado'
titleSuffix: Azure Cognitive Services
description: Neste artigo, aprende-se a implementar confirmações para um comando em Comandos Personalizados.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307840"
---
# <a name="add-interaction-rules"></a>Adicionar regras de interação

Neste artigo, aprende-se sobre **regras de interação.** Estas são regras adicionais para lidar com situações mais específicas ou complexas. Enquanto você é livre de autorizar suas próprias regras de interação personalizada, neste artigo, você faz uso de regras de interação para os seguintes cenários direcionados:

* Confirmando comandos
* Adicionar uma correção de um passo aos comandos

Consulte a secção [de referências](./custom-commands-references.md) para saber mais sobre as regras de interação.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter completado os passos nos seguintes artigos:
> [!div class="checklist"]
> * [Como: Criar aplicação com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Como: Adicionar parâmetros aos comandos](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Adicionar confirmações a um comando

Para adicionar uma confirmação, utilize o comando **SetTemperature.** Para obter confirmação, cria regras de interação utilizando os seguintes passos.

1. Selecione **SetTemperature** command a partir do painel esquerdo.
2. Adicione regras de interação selecionando **Adicione** no painel central e, em seguida, selecione **regras de interação**  >  **Confirme o comando**.

    Isto irá adicionar 3 regras de interação Esta regra pedirá ao utilizador que confirme a data e hora do alarme e espera uma confirmação (sim/não) para a próxima volta.

    1. Modificar a regra de interação **do Comando Confirmar** de acordo com a seguinte configuração
        1. **Nome** do renome para **`Confirm Temperature`** .
        1. Adicione uma nova condição como- **Parâmetros necessários > Temperatura**
        1. Adicione uma nova ação **como- Tipo > Enviar `Are you sure you want to set the temperature as {Temperature} degrees?` resposta à fala >**
        1. Deixe o valor padrão de **Espera de confirmação do utilizador** na secção de expectativas.
      
         > [!div class="mx-imgBorder"]
         > ![Criar resposta de parâmetros necessária](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modificar a regra **de interação de Confirmação conseguiu** lidar com uma confirmação bem sucedida (o utilizador disse que sim).
      
          1. Modificar **nome** para **`Confirmation temperature succeeded`** .
          1. Deixar a confirmação já existente foi uma condição **de sucesso.**
          1. Adicione uma nova condição como- **Tipo > Parâmetros necessários > temperatura**
          1. Deixe o valor padrão do **estado pós-execução** como regras de **conclusão executar**.

    1. Modifique a **Confirmação negada** (o utilizador disse não) para lidar com cenários quando a confirmação for negada.

          1. Modificar **nome** para **`Confirmation temperature denied`** .
          1. Deixe a **confirmação** já existente foi negada condição.
          1. Adicione uma nova condição como- **Tipo > Parâmetros necessários > temperatura**
          1. Adicione uma nova ação **como- Tipo > Enviar `No problem. What temperature then?` resposta à fala >**
          1. Deixe o valor predefinido do **estado de pós-execução** como **Esperar pela entrada do utilizador**.

> [!IMPORTANT]
> Neste artigo, usou a capacidade de confirmação incorporada. Em alternativa, também pode conseguir o mesmo adicionando as regras de interação uma a uma, manualmente.
   

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **Train,** aguarde o treino completo e selecione **Teste**.

- Entrada: temperatura definida para 80 graus
- Saída: ok 80?
- Entrada: Não
- Saída: sem problema. qual a temperatura, então?
- Entrada: 83 graus
- Saída: ok 83?
- Entrada: Sim
- Saída: Ok, ajustar a temperatura para 83 graus


## <a name="implementing-corrections-in-a-command"></a>Implementação de correções num comando

Nesta secção, configura-se uma correção de um passo, que é utilizada uma vez que a ação de cumprimento já tenha sido executada. Também vê um exemplo de como a correção é ativada por padrão no caso de o comando ainda não estar cumprido. Para adicionar uma correção quando o comando não estiver concluído, adicione um novo parâmetro **AlarmTone**.

Selecione o comando **SetAlarm** a partir do painel esquerdo e adicione um novo parâmetro **AlarmTone**.
        
- **Nome** > `AlarmTone`
- **Tipo** > Cadeia
- **Valor Predefinido** > `Chimes`
- **Configuração** > Aceitar valores de entrada predefinidos do catálogo interno
- **Valores de entrada predefinidos**  >  `Chimes` , e `Jingle` `Echo` . Cada uma como entradas predefinidas individuais.


Em seguida, atualize a resposta para o parâmetro DataTime para `Ready to set alarm with tone as {AlarmTone}. For what time?` . Em seguida, modifique a regra de conclusão da seguinte forma.

1. Selecione a regra de conclusão existente **ConfirmationResponse**.
1. No painel direito, paire sobre a ação existente e selecione o botão **Editar.**
1. Atualizar a resposta da fala para`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **Train,** aguarde até que o treino termine e selecione **Teste**.
Experimente as seguintes declarações:

- Entrada: definir um alarme
- Saída: Pronto para definir o alarme com tom como Chimes. Para que horas?
- Entrada: defina um alarme com tom como jingle para amanhã às 9:00
- Saída: Ok, alarme definido para 2020-05-30 09:00:00. O tom de alarme é jingle.

> [!IMPORTANT]
> Note como o sinal de alarme poderia ser alterado sem qualquer configuração explícita num comando em curso, ou seja, quando o comando ainda não estava concluído. **A correção é ativada por padrão para todos os parâmetros de comando, independentemente do número de turno se o comando ainda estiver por cumprir.**

### <a name="correction-when-command-is-completed"></a>Correção quando o comando estiver concluído

A plataforma Comandos Personalizados também fornece a capacidade de correção de um passo mesmo quando o comando foi concluído. Mas esta funcionalidade não é ativada por padrão e deve ser explicitamente configurada. Utilize os seguintes passos para configurar uma correção de um passo.

1. No comando **SetAlarm,** adicione uma regra de interação do **comando anterior** do tipo Update para atualizar o alarme previamente definido. Mude o **Name** nome predefinido da regra de interação para **atualizar o alarme anterior**.
1. Deixar a condição **predefinida O comando anterior tem de ser atualizado** como está.
1.  Adicione uma nova condição como **tipo > parâmetro > data.**
1. Adicione uma nova ação como **Type > Enviar resposta de fala `Updating previous alarm time to {DateTime}.` > editor simples >**
1. Deixe o valor padrão do estado de execução pós-execução como **comando concluído**.

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **Train,** aguarde o treino completo e selecione **Teste**.

- Entrada: definir um alarme
- Saída: Pronto para definir o alarme com tom como Chimes. Que horas?
- Entrada: defina um alarme com tom como jingle para amanhã às 9:00
- Saída: Ok, alarme definido para 2020-05-21 09:00:00. O tom de alarme é jingle.
- Entrada: não, 8 da manhã
- Saída: Atualização do tempo de alarme anterior para 2020-05-29 08:00.

> [!NOTE]
> Numa aplicação real, na secção Ações desta regra de correção, também terá de enviar uma atividade ao cliente ou chamar um ponto final HTTP para atualizar o tempo de alarme no seu sistema. Esta ação deve ser singularmente responsável por apenas atualizar o tempo de alarme e não qualquer outro atributo do comando, neste caso o tom de alarme.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar modelos de geração de linguagem para respostas de fala](./how-to-custom-commands-add-language-generation-templates.md)