---
title: Grupo e executar ações por âmbito
description: Criar ações de âmbito que funcionam com base no estado do grupo em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 08c7fa6abac7ed369347f1f496c70174b06edf02
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83831590"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Executar ações baseadas no estado do grupo utilizando âmbitos em Aplicações Lógicas Azure

Para executar ações apenas após outro grupo de ações ter sucesso ou falhar, agrupar essas ações dentro de um *âmbito*. Esta estrutura é útil quando se pretende organizar ações como um grupo lógico, avaliar o estatuto desse grupo e realizar ações baseadas no estado do âmbito. Depois de todas as ações num final de âmbito, o âmbito também obtém o seu próprio estatuto. Por exemplo, pode utilizar os âmbitos quando pretender implementar [a exceção e o manuseamento](../logic-apps/logic-apps-exception-handling.md#scopes)de erros . 

Para verificar o estado de um âmbito, pode utilizar os mesmos critérios que utiliza para determinar o estado de execução de uma aplicação lógica, como "Succeeded", "Failed", "Cancelado", e assim por diante. Por padrão, quando todas as ações do âmbito são bem sucedidas, o estatuto do âmbito está marcado como "Bem sucedido". Mas quando qualquer ação no âmbito falha ou é cancelada, o estado do âmbito está marcado como "Falhado". Para limites de alcances, consulte [Limites e config](../logic-apps/logic-apps-limits-and-config.md). 

Por exemplo, aqui está uma aplicação lógica de alto nível que usa um âmbito para executar ações específicas e uma condição para verificar o estado do âmbito. Se alguma ação no âmbito falhar ou terminar inesperadamente, o âmbito está marcado como "Falhado" ou "Abortado" respectivamente, e a aplicação lógica envia uma mensagem "Scope failed". Se todas as ações de âmbito forem bem sucedidas, a aplicação lógica envia uma mensagem "Scope conseguida".

![Configurar o gatilho "Agenda - Recorrência"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir o exemplo neste artigo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma conta de e-mail de qualquer fornecedor de e-mail suportado por Apps Lógicas. Este exemplo usa Outlook.com. Se utilizar um fornecedor diferente, o fluxo geral permanece o mesmo, mas o seu UI parece diferente.

* Uma chave bing maps. Para obter esta chave, consulte <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Obter uma chave Bing Maps</a>.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Criar app lógica de amostra

Primeiro, crie esta aplicação lógica de amostra para que possa adicionar um âmbito mais tarde:

![Criar app lógica de amostra](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Um **gatilho de agenda - Recorrência** que verifica o serviço Bing Maps num intervalo que especifica
* A **Bing Maps - Obtenha** ação de rota que verifique o tempo de viagem entre dois locais
* Uma declaração condicional que verifica se o tempo de viagem excede o tempo de viagem especificado
* Uma ação que lhe envia e-mail que o tempo de viagem atual excede o seu tempo especificado

Pode guardar a sua aplicação lógica a qualquer momento, por isso guarde o seu trabalho com frequência.

1. Inscreva-se no <a href="https://portal.azure.com" target="_blank">portal Azure,</a>se ainda não o fez. Criar uma aplicação lógica em branco.

1. Adicione o **Horário - Gatilho de Recorrência** com estas definições: **Intervalo** = "1" e **Frequência** = "Minuto"

   ![Configurar o gatilho "Agenda - Recorrência"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Para simplificar visualmente a sua visão e esconder os detalhes de cada ação no designer, destrua a forma de cada ação à medida que progrida através destes passos.

1. Adicione os Mapas Bing - Obtenha ação de **rota.** 

   1. Se ainda não tem uma ligação ao Bing Maps, é-lhe pedido que crie uma ligação.

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Nome de ligação** | BingMapsConnection | Indique um nome para a ligação. | 
      | **Chave de API** | <*sua chave-bing-maps*> | Introduza a chave do Mapas Bing que recebeu anteriormente. | 
      ||||  

   1. Configurar a ação de **rota Get** como mostrado a tabela abaixo desta imagem:

      ![Configurar a ação "Bing Maps - Get route"](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Para obter mais informações sobre estes parâmetros, veja [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Calcular um percurso).

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Waypoint 1** | <*começar*> | Insira a origem da sua rota. | 
      | **Waypoint 2** | <*fim*> | Entre no destino da sua rota. | 
      | **Avoid** | Nenhum | Insira itens para evitar no seu percurso, tais como autoestradas, portagens, e assim por diante. Para obter valores possíveis, consulte [Calcular uma rota](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Otimizar** | timeWithTraffic | Selecione um parâmetro para otimizar o seu percurso, como distância, tempo com informações de tráfego atuais, e assim por diante. Este exemplo usa este valor: "timeWithTraffic" | 
      | **Distance unit** | <*sua preferência*> | Introduza a unidade de distância para calcular o seu percurso. Este exemplo usa este valor: "Mile" | 
      | **Travel mode** | Driving | Insira o modo de viagem para o seu percurso. Este exemplo usa este valor "Driving" | 
      | **Transit Date-Time** | Nenhum | Aplica-se apenas ao modo de trânsito. | 
      | **Tipo de data de trânsito** | Nenhum | Aplica-se apenas ao modo de trânsito. | 
      ||||  

1. [Adicione uma condição](../logic-apps/logic-apps-control-flow-conditional-statement.md) que verifica se o tempo de viagem atual com tráfego excede um tempo especificado. 
   Para este exemplo, siga estes passos:

   1. Mude o nome da condição com esta descrição: Se o tempo de **tráfego for mais do que o tempo especificado**

   1. Na coluna mais à esquerda, clique no interior da caixa **de valor Para** que a lista de conteúdos dinâmicos apareça. A partir dessa lista, selecione o campo de tráfego de **duração de viagem,** que é em segundos. 

      ![Criar condição](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Na caixa média, selecione este operador: **é maior do que**

   1. Na coluna mais à direita, introduza este valor de comparação, que é em segundos e equivalente a 10 minutos: **600**

      Quando estiver pronto, a condição terá o aspeto deste exemplo:

      ![Condição terminada](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. No ramo **If true,** adicione uma ação de "enviar e-mail" para o seu fornecedor de e-mail. 
   Instale esta ação seguindo os passos sob esta imagem:

   ![Adicione ação "Enviar um e-mail" para o ramo "Se for verdade"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. No campo **Para,** insira o seu endereço de e-mail para efeitos de teste.

   1. No campo **Assunto,** insira este texto:

      ```Time to leave: Traffic more than 10 minutes```

   1. No campo **Corpo,** insira este texto com um espaço de trailing: 

      ```Travel time:```

      Enquanto o cursor aparece no campo **Body,** a lista de conteúdos dinâmicos permanece aberta para que possa selecionar quaisquer parâmetros disponíveis neste momento.

   1. Na lista de conteúdo dinâmico, escolha **Expressão**.

   1. Encontre e selecione a função **de div().** 
      Coloque o cursor dentro dos parênteses da função.

   1. Enquanto o seu cursor estiver dentro dos parênteses da função, escolha **conteúdo Dinâmico** para que a lista de conteúdos dinâmicos apareça. 
   
   1. A partir da secção **Get route,** selecione o campo de tráfego de duração do **tráfego.**

      ![Selecione "Tráfego de Duração do Tráfego"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Depois de o campo se resolver ao formato JSON, adicione uma **vírvia** ( ) seguida do número para converter o valor no Tráfego de Duração do ```,``` ```60``` **Tráfego** de segundos a minutos. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      A sua expressão agora parece com este exemplo:

      ![Expressão de acabamento](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Quando tiver terminado, selecione **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Depois de a expressão resolver, adicione este texto com um espaço de liderança:``` minutes```
  
       O seu campo **de corpo** agora parece este exemplo:

       ![Campo "Body" acabado](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Guarde a aplicação lógica.

Em seguida, adicione um âmbito para que possa agrupar ações específicas e avaliar o seu estado.

## <a name="add-a-scope"></a>Adicione um âmbito

1. Se ainda não o fez, abra a sua aplicação lógica no Logic App Designer. 

1. Adicione uma mira no local de fluxo de trabalho que deseja. Por exemplo, para adicionar um espaço entre os passos existentes no fluxo de trabalho de aplicações lógicas, siga estes passos: 

   1. Mova o ponteiro sobre a seta onde pretende adicionar o âmbito. 
   Escolha o **sinal de mais** ( ) > Adicionar uma **+** **ação**.

      ![Adicione um âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Na caixa de pesquisa, introduza "scope" como filtro. 
   Selecione a ação **Scope.**

## <a name="add-steps-to-scope"></a>Adicionar passos ao âmbito

1. Adicione agora os passos ou arraste os passos existentes que pretende executar dentro do âmbito. Para este exemplo, arraste estas ações para o âmbito:
      
   * **Obtenha rota**
   * **Se o tempo de tráfego for mais do que o tempo especificado,** o que inclui tanto os ramos **verdadeiros** como os **falsos**

   A sua aplicação lógica agora se parece com este exemplo:

   ![Âmbito adicionado](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. No âmbito, adicione uma condição que verifique o estado do âmbito. Mude o nome da condição com esta descrição: **Se o âmbito falhar**

   ![Adicionar condição para verificar o estado do âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Na condição, adicione estas expressões que verificam se o estado do âmbito é igual a "Falhado" ou "Abortado". 

   1. Para adicionar outra linha, escolha **Adicionar**. 

   1. Em cada linha, clique no interior da caixa esquerda para que a lista de conteúdos dinâmicos apareça. 
   Da lista de conteúdos dinâmicos, escolha **Expressão**. Na caixa de edição, introduza esta expressão e, em seguida, escolha **OK:** 
   
      `result('Scope')[0]['status']`

      ![Adicione expressão que verifique o estado do âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Para ambas as linhas, selecione **é igual ao** do operador. 
   
   1. Para os valores de comparação, na primeira fila, `Failed` entre. 
   Na segunda fila, `Aborted` entre. 

      Quando estiver pronto, a condição terá o aspeto deste exemplo:

      ![Adicione expressão que verifique o estado do âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Agora, defina a propriedade da condição para que `runAfter` a condição verifique o estado de âmbito e execute a ação correspondente que você define em etapas posteriores.

   1. Na condição se o **âmbito falhar,** escolha o botão **elipse** (...) e, em seguida, escolha **configurar correr atrás**de .

      ![Configurar propriedade 'runAfter'](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Selecione todos estes estatutos de âmbito: **é bem sucedido,** **falhou,** **é ignorado,** e **tem tempo esgotado**

      ![Selecione estados de âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Quando tiver terminado, selecione **Concluído**. 
   A condição agora mostra um ícone de "informação".

1. No **Se for verdade** e **se forem falsos,** adicione as ações que pretende executar com base em cada estado de âmbito, por exemplo, envie um e-mail ou mensagem.

   ![Adicionar ações a tomar com base no estado do âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Guarde a aplicação lógica.

A sua aplicação lógica acabada agora parece com este exemplo:

![App lógica acabada com âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Teste o seu trabalho

Na barra de ferramentas de design, escolha **Run**. Se todas as ações de âmbito forem bem sucedidas, recebes uma mensagem "Scope bem sucedida". Se alguma ação de âmbito não for bem sucedida, recebes uma mensagem "Scope failed". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definição JSON

Se estiver a trabalhar em visualização de código, pode definir uma estrutura de âmbito na definição JSON da sua aplicação lógica. Por exemplo, aqui está a definição JSON para gatilho e ações na aplicação lógica anterior:

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

* Para perguntas, visite o [Microsoft Q&Uma página de perguntas para aplicações lógicas do Azure](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em funcionalidades e sugestões, visite o site de feedback do utilizador das [Aplicações Lógicas Azure.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>Passos seguintes

* [Passos de execução com base numa condição (declarações condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar passos com base em diferentes valores (declarações de switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir passos (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou fundir passos paralelos (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
