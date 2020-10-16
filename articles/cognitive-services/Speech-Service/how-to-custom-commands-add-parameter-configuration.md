---
title: 'Como: Configurar o parâmetro como entidade de entidades externas'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como configurar um parâmetro de corda para se referir ao catálogo exposto sobre um ponto final web.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284188"
---
# <a name="add-configurations-to-commands-parameters"></a>Adicionar configurações aos parâmetros dos comandos

Neste artigo, aprende-se mais sobre a configuração avançada dos parâmetros, incluindo:

 - Como os valores dos parâmetros podem pertencer a um conjunto definido externamente à aplicação de comandos personalizados
 - Como adicionar cláusulas de validação sobre o valor dos parâmetros

## <a name="prerequisites"></a>Pré-requisitos

Deve ter completado os passos nos seguintes artigos:

> [!div class="checklist"]
> * [Como: Criar aplicação com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Como: Adicionar parâmetros aos comandos](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>Configure o parâmetro como entidade de catálogo externo

Nesta secção, configura parâmetros do tipo de corda para se referir a catálogos externos hospedados sobre um ponto final web. Isto permite-lhe atualizar o catálogo externo de forma independente sem fazer edições para a aplicação Comandos Personalizados. Esta abordagem é útil nos casos em que as entradas no catálogo podem ser grandes em número.

Reutilizar o parâmetro **SubjectDevice** a partir do comando **TurnOnOff.** A configuração atual para este parâmetro é **Aceitar entradas predefinidas do catálogo interno**. Isto refere-se à lista estática de dispositivos tal como definidos na configuração do parâmetro. Queremos transferir este conteúdo para uma fonte de dados externa que possa ser atualizada de forma independente.

Para isso, comece por adicionar um novo ponto final web. Vá à secção **de pontos finais** da Web no painel esquerdo e adicione um novo ponto final web com a seguinte configuração.

| Definição | Valor sugerido |
|----|----|
| Nome | `getDevices` |
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

### <a name="try-it-out"></a>Experimente

Selecione **Train** e aguarde a conclusão do treino. Uma vez concluído o treino, selecione **Teste** e experimente algumas interações.

* Entrada: ligar
* Saída: Que dispositivo pretende controlar?
* Entrada: luzes
* Saída: Ok, acendendo as luzes

> [!NOTE]
> Note como pode controlar todos os dispositivos alojados no ponto final da web agora. Ainda precisa de treinar a aplicação para testar as novas alterações e voltar a publicar a aplicação.

## <a name="add-validation-to-parameters"></a>Adicionar validação aos parâmetros

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

### <a name="try-it-out"></a>Experimente

1. Selecione o ícone **do comboio** presente no topo do painel direito.

1. Uma vez concluído o treino, selecione **Teste** e experimente algumas interações:

    - Entrada: Definir a temperatura para 72 graus
    - Saída: Ok, ajustar a temperatura para 72 graus
    - Entrada: Definir a temperatura para 45 graus
    - Saída: Desculpe, só posso definir temperatura entre 60 e 80 graus
    - Entrada: faça 72 graus em vez
    - Saída: Ok, ajustar a temperatura para 72 graus

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar regras de interação](./how-to-custom-commands-add-interaction-rules.md)
