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
ms.openlocfilehash: 7d6c0928196c9e8e1abf6aa7f724a58753ce3d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289040"
---
# <a name="add-interaction-rules"></a>Adicionar regras de interação

Neste artigo, você vai aprender sobre regras de **interação**. Estas são regras adicionais para lidar com situações mais específicas ou complexas. Enquanto você é livre de autorizar suas próprias regras de interação personalizada, neste artigo, você faz uso de regras de interação para os seguintes cenários direcionados:

* Confirmando comandos
* Adicionar uma correção de um passo aos comandos

Para saber mais sobre regras de interação, aceda à secção [de referências.](./custom-commands-references.md)

## <a name="prerequisites"></a>Pré-requisitos

Deve ter terminado os passos nos seguintes artigos:
> [!div class="checklist"]
> * [Como: Criar aplicação com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Como: Adicionar parâmetros aos comandos](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Adicionar confirmações a um comando

Para adicionar uma confirmação, utilize o comando **SetTemperature.** Para obter confirmação, cria regras de interação utilizando os seguintes passos.

1. Selecione o **comando SetTemperature** no painel esquerdo.
1. Adicione as regras de interação selecionando **Adicione** no painel central. Em seguida, selecione **regras de interação**  >  **Confirme o comando**.

    Esta ação adiciona três regras de interação que pedirão ao utilizador que confirme a data e hora do alarme e espera uma confirmação (sim/não) para a próxima volta.

    1. Modificar a regra de interação **do comando Confirmar** de acordo com a seguinte configuração:
        1. **Nome** do nome para **confirmar a temperatura**.
        1. Adicione uma nova condição como **parâmetros necessários**  >  **Temperatura**.
        1. Adicione uma nova ação como resposta **de**fala Type  >  **Send**  >  **Tem a certeza de que pretende definir a temperatura como graus {Temperatura} ?**
        1. Deixe o valor padrão de **Espera de confirmação do utilizador** na secção **Expectativas.**
      
         > [!div class="mx-imgBorder"]
         > ![Criar resposta de parâmetros necessária](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modificar a regra **de interação de Confirmação conseguiu** lidar com uma confirmação bem sucedida (o utilizador disse que sim).
      
          1. Modificar **Nome** para **a temperatura de confirmação conseguiu**.
          1. Deixar a confirmação já existente foi uma condição **de sucesso.**
          1. Adicione uma nova **Type**condição como  >  **parâmetros tipo exigidos**  >  **Temperatura**.
          1. Deixe o valor padrão do **estado pós-execução** como regras de **conclusão executar**.

    1. Modifique a regra **de interação negada** por Confirmação para lidar com cenários quando a confirmação é negada (o utilizador disse não).

          1. Modificar **Nome** para **a temperatura de confirmação negada**.
          1. Deixe a **confirmação** já existente foi negada condição.
          1. Adicione uma nova **Type**condição como  >  **parâmetros tipo exigidos**  >  **Temperatura**.
          1. Adicione uma nova ação como **Tipo**  >  **Enviar resposta de fala**Sem  >  **problema. Que temperatura então?**
          1. Deixe o valor predefinido do **estado de pós-execução** como **Esperar pela entrada do utilizador**.

> [!IMPORTANT]
> Neste artigo, usou a capacidade de confirmação incorporada. Também pode adicionar manualmente regras de interação uma a uma.
   

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **Train,** aguarde que o treino termine e selecione **Teste**.

- **Entrada**: Definir temperatura a 80 graus
- **Saída**: tem a certeza de que pretende definir a temperatura como 80 graus?
- **Entrada**: Não
- **Saída**: Sem problema. Que temperatura então?
- **Entrada**: 72 graus
- **Saída**: tem a certeza de que pretende definir a temperatura como 72 graus?
- **Entrada**: Sim
- **Saída**: OK, temperatura de fixação de 83 graus


## <a name="implement-corrections-in-a-command"></a>Implementar correções num comando

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

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **Train,** aguarde que o treino termine e selecione **Teste**.
Experimente as seguintes declarações:

- **Entrada**: Desemote um alarme.
- **Saída**: Pronto para definir o alarme com tom como Chimes. Para que horas?
- **Entrada**: Defina um alarme com o tom como Jingle para amanhã às 9 h.
- **Saída**: OK, alarme definido para 2020-05-30 09:00:00. O tom de alarme é Jingle.

> [!IMPORTANT]
> O tom de alarme poderia ser alterado sem qualquer configuração explícita num comando contínuo, por exemplo, quando o comando ainda não estava terminado. *Uma correção é ativada por padrão para todos os parâmetros de comando, independentemente do número de turno se o comando ainda estiver por cumprir.*

### <a name="correction-when-command-is-completed"></a>Correção quando o comando estiver concluído

A plataforma Comandos Personalizados também fornece a capacidade para uma correção de um passo, mesmo quando o comando foi concluído. Esta funcionalidade não é ativada por defeito. Deve ser explicitamente configurado. Utilize os seguintes passos para configurar uma correção de um passo.

1. No comando **SetAlarm,** adicione uma regra de interação do **comando anterior** do tipo Update para atualizar o alarme previamente definido. Mude o **Name** nome predefinido da regra de interação para **atualizar o alarme anterior**.
1. Deixar a condição **predefinida O comando anterior tem de ser atualizado** como está.
1. Adicione uma nova condição como hora de data do parâmetro exigido do **tipo**  >  **Required Parameter**  >  **DateTime**.
1. Adicione uma nova ação como **Tipo**  >  **Enviar resposta de discurso**Simples  >  **editor**Atualizar o  >  **tempo de alarme anterior para {DateTime}.**
1. Deixe o valor padrão do **estado de pós-execução** como **comando concluído**.

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **Train,** aguarde que o treino termine e selecione **Teste**.

- **Entrada**: Desemote um alarme.
- **Saída**: Pronto para definir o alarme com tom como Chimes. Para que horas?
- **Entrada**: Defina um alarme com o tom como Jingle para amanhã às 9 h.
- **Saída**: OK, alarme definido para 2020-05-21 09:00:00. O tom de alarme é Jingle.
- **Entrada**: Não, 8 da manhã.
- **Saída**: Atualização do tempo de alarme anterior para 2020-05-29 08:00.

> [!NOTE]
> Numa aplicação real, na secção **Ações** desta regra de correção, também terá de enviar uma atividade ao cliente ou chamar um ponto final HTTP para atualizar o tempo de alarme no seu sistema. Esta ação deve ser exclusivamente responsável pela atualização da hora de alarme e não de qualquer outro atributo do comando. Neste caso, seria o sinal de alarme.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar modelos de geração de linguagem para respostas de fala](./how-to-custom-commands-add-language-generation-templates.md)
