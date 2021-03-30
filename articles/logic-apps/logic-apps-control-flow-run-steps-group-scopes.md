---
title: Grupo e executar ações por âmbito
description: Criar ações âmbito com base no estado de grupo em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 95b5cc191ac6857bf8e1b09e70b22d928473fe03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92314847"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Executar ações com base no estado de grupo utilizando âmbitos em Azure Logic Apps

Para executar ações apenas depois de outro grupo de ações ter sucesso ou falhar, agrupar essas ações dentro de um *âmbito*. Esta estrutura é útil quando se pretende organizar ações como um grupo lógico, avaliar o estado desse grupo e realizar ações baseadas no estado do âmbito. Depois de todas as ações num âmbito final, o âmbito também obtém o seu próprio estatuto. Por exemplo, pode utilizar âmbitos quando pretende implementar [exceções e tratamento de erros](../logic-apps/logic-apps-exception-handling.md#scopes). 

Para verificar o estado de um âmbito, pode utilizar os mesmos critérios que utiliza para determinar o estado de funcionação de uma aplicação lógica, como **O Sucesso,** **Falhado,** **Cancelado,** e assim por diante. Por defeito, quando todas as ações do âmbito forem bem **sucedidas,** o estado do âmbito é marcado como Bem Sucedido . Mas quando qualquer ação no âmbito falha ou é cancelada, o estado do âmbito está marcado **como Falhado**. Para limites de âmbitos, consulte [Limites e config](../logic-apps/logic-apps-limits-and-config.md). 

Por exemplo, aqui está uma aplicação lógica de alto nível que usa um âmbito para executar ações específicas e uma condição para verificar o estado do âmbito. Se quaisquer ações no âmbito falharem ou terminarem inesperadamente, o âmbito está marcado **Falhado** ou **Abortado** respectivamente, e a aplicação lógica envia uma mensagem "Scope failed". Se todas as ações âmbitodas forem bem sucedidas, a aplicação lógica envia uma mensagem "Scope succeeded".

![O diagrama mostra o fluxo de âmbito da aplicação lógica com exemplos de "Scope failed" e "Scope succeeded".](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir o exemplo neste artigo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma conta de e-mail de qualquer fornecedor de e-mail suportado por Logic Apps. Este exemplo utiliza Outlook.com. Se utilizar um fornecedor diferente, o fluxo geral permanece o mesmo, mas a sua UI parece diferente.

* Uma chave Bing Maps. Para obter esta chave, consulte <a href="/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key" target="_blank">a chave Bing Maps</a>.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Criar app lógica de amostra

Em primeiro lugar, crie esta aplicação lógica de amostra para que possa adicionar um âmbito mais tarde:

![Criar app lógica de amostra](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Um **Programa - Gatilho de recorrência** que verifica o serviço Bing Maps num intervalo que especifica
* A **Bing Maps - Obtenha** ação de rota que verifica o tempo de viagem entre dois locais
* Uma declaração condicional que verifica se o tempo de viagem excede o tempo de viagem especificado
* Uma ação que lhe envia e-mail que o tempo atual de viagem excede o seu tempo especificado

Pode guardar a sua aplicação lógica a qualquer momento, por isso guarde o seu trabalho com frequência.

1. Inscreva-se no <a href="https://portal.azure.com" target="_blank">portal Azure,</a>se ainda não o fez. Criar uma aplicação lógica em branco.

1. Adicione o **Agendamento - Gatilho de recorrência** com estas definições: **Intervalo** = "1" e **Frequência** = "Minuto"

   ![Configurar o gatilho "Agenda - Recorrência"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Para simplificar visualmente a sua visão e esconder os detalhes de cada ação no designer, destrua a forma de cada ação à medida que progride através destes passos.

1. Adicione o **Bing Maps - Obtenha** ação de rota. 

   1. Se ainda não tem uma ligação Bing Maps, é-lhe pedido que crie uma ligação.

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Nome de conexão** | BingMapsConnection | Indique um nome para a ligação. | 
      | **Chave de API** | <*sua-Bing-Maps-chave*> | Introduza a chave do Mapas Bing que recebeu anteriormente. | 
      ||||  

   1. Configurar a sua ação **de rota Get** como mostrado na tabela abaixo desta imagem:

      ![Configurar ação "Bing Maps - Get route"](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Para obter mais informações sobre estes parâmetros, veja [Calculate a route](/bingmaps/rest-services/routes/calculate-a-route) (Calcular um percurso).

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Waypoint 1** | <*começar*> | Insira a origem da sua rota. | 
      | **Waypoint 2** | <*fim*> | Entre no destino da sua rota. | 
      | **Evitação** | Nenhum | Insira itens para evitar no seu percurso, como autoestradas, portagens, e assim por diante. Para obter valores possíveis, consulte [Calcular uma rota.](/bingmaps/rest-services/routes/calculate-a-route) | 
      | **Otimizar** | timeWithTraffic | Selecione um parâmetro para otimizar o seu percurso, como distância, tempo com informações de tráfego correntes, e assim por diante. Este exemplo usa este valor: "timeWithTraffic" | 
      | **Distance unit** | <*sua preferência*> | Introduza a unidade de distância para calcular o seu percurso. Este exemplo usa este valor: "Mile" | 
      | **Travel mode** | Driving | Insira o modo de viagem para a sua rota. Este exemplo usa este valor "Driving" | 
      | **Transit Date-Time** | Nenhum | Aplica-se apenas ao modo de trânsito. | 
      | **Tipo de Date-Type de trânsito** | Nenhum | Aplica-se apenas ao modo de trânsito. | 
      ||||  

1. [Adicione uma condição](../logic-apps/logic-apps-control-flow-conditional-statement.md) que verifique se o tempo de viagem atual com tráfego excede um tempo especificado. 
   Para este exemplo, siga estes passos:

   1. Rebatize a condição com esta descrição: **Se o tempo de tráfego for superior ao tempo especificado**

   1. Na coluna mais à esquerda, clique no interior da caixa **de valor Escolha para** que apareça a lista de conteúdos dinâmicos. Nessa lista, selecione o campo **de tráfego de duração de viagem,** que é em segundos. 

      ![Criar condição](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Na caixa média, selecione este operador: **é maior do que**

   1. Na coluna mais à direita, introduza este valor de comparação, que é em segundos e equivalente a 10 minutos: **600**

      Quando estiver pronto, a condição terá o aspeto deste exemplo:

      ![Estado finalizado](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. No ramo **Se verdadeiro,** adicione uma ação de "enviar e-mail" para o seu fornecedor de e-mail. 
   Configurar esta ação seguindo os passos sob esta imagem:

   ![Adicione a ação "Enviar um e-mail" ao ramo "Se for verdade"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. No campo **Para,** insira o seu endereço de e-mail para efeitos de teste.

   1. No campo **Assunto,** insira este texto:

      ```Time to leave: Traffic more than 10 minutes```

   1. No campo **Corpo,** insira este texto com um espaço de fuga: 

      ```Travel time:```

      Enquanto o cursor aparece no campo **Corpo,** a lista de conteúdos dinâmicos permanece aberta para que possa selecionar quaisquer parâmetros que estejam disponíveis neste ponto.

   1. Na lista de conteúdo dinâmico, escolha **Expressão**.

   1. Encontre e selecione a função **div().** 
      Coloque o cursor dentro dos parênteses da função.

   1. Enquanto o cursor estiver dentro dos parênteses da função, escolha **conteúdo Dinâmico** para que a lista de conteúdos dinâmicos apareça. 
   
   1. A partir da secção **'Obter'** da rota, selecione o campo **de tráfego de duração do tráfego.**

      ![Selecione "Tráfego de Duração de Tráfego"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Depois de o campo se resolver para o formato JSON, adicione uma **vírgula** ( ```,``` ) seguida do número para que ```60``` converta o valor no Tráfego de **Duração** de Tráfego de segundos para minutos. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      A sua expressão agora parece este exemplo:

      ![Fim da expressão](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Quando tiver terminado, selecione **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Após a resolução da expressão, adicione este texto com um espaço líder: ``` minutes```
  
       O seu campo **body** agora se parece com este exemplo:

       ![Campo "Corpo" acabado](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Guarde a sua aplicação lógica.

Em seguida, adicione um âmbito para que possa agrupar ações específicas e avaliar o seu estado.

## <a name="add-a-scope"></a>Adicionar um âmbito

1. Se ainda não o fez, abra a sua aplicação lógica no Logic App Designer. 

1. Adicione uma mira no local de fluxo de trabalho que deseja. Por exemplo, para adicionar um âmbito entre os passos existentes no fluxo de trabalho da aplicação lógica, siga estes passos: 

   1. Desloque o ponteiro sobre a seta onde pretende adicionar o âmbito. 
   Escolha o **sinal de mais** **+** () > Adicione uma **ação**.

      ![Adicionar um âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Na caixa de pesquisa, introduza "scope" como filtro. 
   Selecione a ação **Scope.**

## <a name="add-steps-to-scope"></a>Adicionar passos ao âmbito

1. Agora adicione os passos ou arraste os passos existentes que pretende correr dentro do âmbito. Para este exemplo, arrastar estas ações para o âmbito:
      
   * **Obter rota**
   * **Se o tempo de tráfego for mais do que o tempo especificado,** que inclui tanto os **ramos verdadeiros** como **os falsos**

   A sua aplicação lógica agora parece este exemplo:

   ![Âmbito adicionado](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Sob o âmbito, adicione uma condição que verifique o estado do âmbito. Rebatize a condição com esta descrição: **Se o âmbito falhou**

   ![Adicionar condição para verificar o estado do âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Na condição, adicione estas expressões que verifiquem se o estado do âmbito é igual a "Falhado" ou "Abortado". 

   1. Para adicionar outra linha, escolha **Adicionar**. 

   1. Em cada linha, clique dentro da caixa esquerda para que apareça a lista de conteúdos dinâmicos. 
   Na lista de conteúdos dinâmicos, escolha **Expressão.** Na caixa de edição, insira esta expressão e, em seguida, escolha **OK**: 
   
      `result('Scope')[0]['status']`

      ![Screenshot que mostra a caixa "Expression" com a expressão de resultado realçada.](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Para ambas as linhas, a seleção **é igual à** do operador. 
   
   1. Para os valores de comparação, na primeira linha, insira `Failed` . 
   Na segunda fila, `Aborted` entre. 

      Quando estiver pronto, a condição terá o aspeto deste exemplo:

      ![Adicionar expressão que verifica o estado do âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Agora, defina a propriedade da condição `runAfter` para que a condição verifique o estado do âmbito e executa a ação correspondente que você define em passos posteriores.

   1. Na condição **de se o âmbito falhou,** escolha o botão **elipse** (...) e, em seguida, escolha a execução de **configuração depois**.

      ![Configure propriedade 'runAfter'](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Selecione todos estes status de âmbito: **é bem sucedido,** **falhou,** **é ignorado,** e **tem tempo para fora**

      ![Selecione os status de âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Quando tiver terminado, selecione **Concluído**. 
   A condição agora mostra um ícone de "informação".

1. No **Se verdadeiro** e se os ramos **falsos,** adicione as ações que pretende executar com base em cada estado de âmbito, por exemplo, envie um e-mail ou mensagem.

   ![Adicionar ações a tomar com base no estado de âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Guarde a sua aplicação lógica.

A sua aplicação lógica acabada agora parece este exemplo:

![App lógica acabada com âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Teste o seu trabalho

Na barra de ferramentas do designer, escolha **Executar.** Se todas as ações de âmbito forem bem sucedidas, obtém-se uma mensagem "Scope succeeded". Se alguma ação de âmbito não for bem sucedida, obtém-se uma mensagem "Scope failed". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definição JSON

Se estiver a trabalhar na visão de código, pode definir uma estrutura de âmbito na definição JSON da sua aplicação lógica. Por exemplo, aqui está a definição JSON para gatilho e ações na aplicação lógica anterior:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em funcionalidades e sugestões, visite o site de feedback do [utilizador do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Passos de execução com base numa condição (declarações condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Passos de execução com base em diferentes valores (declarações de switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir passos (laços)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou fundir etapas paralelas (ramos)](../logic-apps/logic-apps-control-flow-branches.md)