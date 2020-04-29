---
title: 'Como: Adicionar uma confirmação a um comando personalizado (Pré-visualização)'
titleSuffix: Azure Cognitive Services
description: Neste artigo, como implementar confirmações para um comando em Comandos Personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456500"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Como: Adicionar uma confirmação a um Comando Personalizado (Pré-visualização)

Neste artigo, aprenderá a adicionar uma confirmação a um comando.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter completado os passos nos seguintes artigos:

- [Quickstart: Criar um Comando Personalizado (Pré-visualização)](./quickstart-custom-speech-commands-create-new.md)
- [Quickstart: Criar um comando personalizado com parâmetros (pré-visualização)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Criar um comando SetAlarm

Para demonstrar validações, vamos criar um novo Comando que permita ao utilizador definir um alarme.

1. Abra a sua aplicação de Comandos Personalizados previamente criada no [Estúdio de Discurso](https://speech.microsoft.com/)
1. Criar um novo **SetAlarm** de Comando
1. Adicione um parâmetro chamado DateTime

   | Definição           | Valor sugerido                                          | Descrição                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Nome              | DateTime                                                 | Um nome descritivo para o seu parâmetro de comando                                                    |
   | Necessário          | true                                                     | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o Comando |
   | Modelo de resposta | "- A que horas?"                                           | Um pedido para pedir o valor deste parâmetro quando não é conhecido                              |
   | Tipo              | DateTime                                                 | O tipo de parâmetro, como número, corda ou hora da data                                      |
   | Incumprimentos da data     | Se a data faltar hoje                             |                                                                                                  |
   | Predefinições de tempo     | Se o tempo faltar ao uso início do dia                      |                                                                                                  | 

1. Adicione algumas frases de amostra
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Adicione uma regra de conclusão para confirmar o resultado

   | Definição    | Valor sugerido                                         | Descrição                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra  | Definir alarme                                               | Um nome que descreve o propósito da regra          |
   | Ações    | SpeechResponse - "- Ok, conjunto de alarme para {DateTime}"       | A ação a tomar quando a condição da regra é verdadeira |

## <a name="try-it-out"></a>Experimente

Selecione o painel de teste e experimente algumas interações.

- Entrada: Desema o alarme para amanhã ao meio-dia
- Saída: "Ok, alarme definido para 12/06/2019 12:00:00"

- Entrada: Desabote um alarme
- Saída: "A que horas?"
- Entrada: 17h00
- Saída: "Ok, alarme definido para 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Adicione as regras avançadas para confirmação

1. Adicione uma regra avançada para confirmação. 

    Esta regra irá pedir ao utilizador que confirme a data e a hora do alarme e espera uma confirmação (sim/não) para a próxima volta.

   | Definição               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmar a data de data                                                                | Um nome que descreve o propósito da regra          |
   | Condições            | Parâmetro obrigatório - DataTime                                                    | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | SpeechResponse - "- Tem certeza de que quer definir um alarme para {DateTime}?"       | A ação a tomar quando a condição da regra é verdadeira |
   | Estado após execução | Aguarde a entrada                                                                   | Estado para o utilizador após a volta                  |
   | Expectativas          | Confirmação                                                                     | Expectativa para a próxima volta                      |

1. Adicione uma regra avançada para lidar com uma confirmação bem sucedida (o utilizador disse que sim)

   | Definição               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmação aceite                                                            | Um nome que descreve o propósito da regra          |
   | Condições            | Com sucesso confirmação & parâmetro necessário - DataTime                           | Condições que determinam quando a regra pode ser executada    |   
   | Estado após execução | Pronto para a conclusão                                                             | Estado do utilizador após a volta                   |

1. Adicione uma regra avançada para lidar com uma confirmação negada (o utilizador disse que não)

   | Definição               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Negado confirmar                                                                   | Um nome que descreve o propósito da regra          |
   | Condições            | Negação de confirmação & parâmetro obrigatório - DataTime                               | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | ClearParameter - DataTime & SpeechResponse - "Sem problema, a que horas?"     | A ação a tomar quando a condição da regra é verdadeira |
   | Estado após execução | Aguarde a entrada                                                                   | Estado do utilizador após a volta                   |
   | Expectativas          | ElicitParameters - DataTime                                                      | Expectativa para a próxima volta                      |

## <a name="try-it-out"></a>Experimente

Selecione o painel de teste e experimente algumas interações.

- Entrada: Desema o alarme para amanhã ao meio-dia
- Saída: "Tem certeza de que quer definir um alarme para 12/07/2019 12:00:00?"
- Entrada: Não
- Saída: "Sem problema, a que horas então?"
- Entrada: 17h00
- Saída: "Tem certeza de que quer definir um alarme para 12/06/2019 17:00:00?"
- Entrada: Sim
- Saída: "Ok, alarme definido para 12/06/2019 17:00:00"

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar uma correção de um passo a um Comando Personalizado (Pré-visualização)](./how-to-custom-speech-commands-one-step-correction.md)