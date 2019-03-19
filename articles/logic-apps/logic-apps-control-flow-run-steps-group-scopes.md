---
title: Adicionar âmbitos que executam ações com base no estado do grupo - Azure Logic Apps | Documentos da Microsoft
description: Como criar âmbitos que executam ações de fluxo de trabalho com base no estado da ação de grupo no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: d73a43aedde9a88e009ddca1f0363dbcd92e1379
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080459"
---
# <a name="run-actions-based-on-group-status-with-scopes-in-azure-logic-apps"></a>Executar ações com base no estado de grupo com âmbitos no Azure Logic Apps

Para executar ações apenas depois de outro grupo de ações com êxito ou falha, agrupar essas ações dentro de um *âmbito*. Esta estrutura é útil quando quer organizar ações como um grupo lógico, avaliar o estado desse grupo e executar ações que são baseadas no estado de âmbito. Depois de todas as ações num âmbito termine a execução, o âmbito também obtém seu próprio Estado. Por exemplo, pode utilizar âmbitos quando pretender implementar [exceção e tratamento de erros](../logic-apps/logic-apps-exception-handling.md#scopes). 

Para verificar o estado de um âmbito, pode utilizar os mesmos critérios que utilizar para determinar uma lógica de estado, como "Com êxito", "Falha", "Cancelada" e assim por diante de execução de aplicações. Por predefinição, quando as ações de âmbito tiver êxito, estado de âmbito está marcada como "Com êxito." Mas quando qualquer ação no âmbito da falha ou é cancelada, estado de âmbito está marcada como "Falhado". Para limites em âmbitos, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md). 

Por exemplo, aqui está uma aplicação de lógica de alto nível que utiliza um âmbito para executar ações específicas e uma condição para verificar o estado de âmbito. Se todas as ações no âmbito não ou terminam inesperadamente, o âmbito está marcado "Falhado" ou "Aborted", respectivamente, e a aplicação lógica envia uma mensagem "Falha de âmbito". Se tiver êxito a todas as ações de âmbito, a aplicação lógica envia uma mensagem de "Escopo foi concluída com êxito".

![Configurar o acionador "– a periodicidade da agenda"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir o exemplo neste artigo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma conta de e-mail de qualquer fornecedor de e-mail suportada pelo Logic Apps. Este exemplo utiliza o Outlook.com. Se utilizar outro fornecedor, o fluxo geral permanece o mesmo, mas sua interface do Usuário aparece diferente.

* Uma chave do mapas Bing. Para obter esta chave, consulte <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">obter uma chave do mapas Bing</a>.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Criar aplicação de lógica de exemplo

Primeiro, crie esta aplicação de lógica de exemplo para que pode adicionar um escopo mais tarde:

![Criar aplicação de lógica de exemplo](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* R **agenda - periodicidade** acionador que verifica o serviço Bing Maps num intervalo que especificou
* R **Bing Maps - Get route** ação que verifica o tempo de deslocação entre duas localizações
* Uma instrução condicional que verifica se o tempo de deslocação excede o tempo de deslocação especificado
* Uma ação que envia um que e-mail para esse tempo de deslocação atual excede o período de tempo especificado

Pode guardar a aplicação lógica em qualquer altura, por isso, muitas vezes a guardar o seu trabalho.

1. Inicie sessão para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, se ainda não o fez. Criar uma aplicação lógica em branco.

1. Adicionar a **agenda - periodicidade** acionador com estas definições: **Intervalo** = "1" e **frequência** = "Minuto"

   ![Configurar o acionador "– a periodicidade da agenda"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Para visualmente simplificar a vista e ocultar os detalhes de cada ação no designer, fechar forma de cada ação conforme avança nestes passos.

1. Adicionar a **Bing Maps - Get route** ação. 

   1. Se ainda não tiver uma ligação ao mapas Bing, é-lhe pedido para criar uma ligação.

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Nome da Ligação** | BingMapsConnection | Indique um nome para a ligação. | 
      | **Chave de API** | <*your-Bing-Maps-key*> | Introduza a chave do Mapas Bing que recebeu anteriormente. | 
      ||||  

   1. Configurar a sua **Get route** ação, como mostra a tabela a seguir esta imagem:

      ![Configurar "Bing Maps - Get route" ação](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Para obter mais informações sobre estes parâmetros, veja [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Calcular um percurso).

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Waypoint 1** | <*start*> | Introduza a origem do percurso. | 
      | **Waypoint 2** | <*end*> | Introduza o destino do percurso. | 
      | **Avoid** | Nenhuma | Introduza os itens para evitar no percurso, como autoestradas, portagens e assim por diante. Para os valores possíveis, consulte [calcular uma rota](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimize** | timeWithTraffic | Selecione um parâmetro para otimizar o percurso, como a distância, tempo com informações de tráfego atuais e assim por diante. Este exemplo utiliza este valor: "timeWithTraffic" | 
      | **Distance unit** | <*your-preference*> | Introduza a unidade de distância para calcular a rota. Este exemplo utiliza este valor: "Milha" | 
      | **Travel mode** | Driving | Introduza o modo de viagem para a sua rota. Este exemplo utiliza este valor "Driving" | 
      | **Transit Date-Time** | Nenhuma | Aplica-se ao modo de tráfego apenas. | 
      | **Tipo de Data-Type de trânsito** | Nenhuma | Aplica-se ao modo de tráfego apenas. | 
      ||||  

1. [Adicionar uma condição](../logic-apps/logic-apps-control-flow-conditional-statement.md) que verifica se o tempo de deslocação atual com tráfego excede um período de tempo especificado. 
   Neste exemplo, siga estes passos:

   1. Mudar o nome da condição com a descrição: **Se o tempo de tráfego é mais do que o período de tempo especificado**

   1. Na coluna mais à esquerda, clique no interior da **escolher um valor** caixa para que a lista de conteúdo dinâmico apareça. A partir dessa lista, selecione o **Travel Duration Traffic** campo, o que está em segundos. 

      ![Criar condição](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Na caixa do meio, selecione o operador: **for superior a**

   1. Na coluna mais à direita, introduza o valor de comparação, que está em segundos e o equivalente a 10 minutos: **600**

      Quando estiver pronto, a condição terá o aspeto deste exemplo:

      ![Condição concluída](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Na **se for verdadeiro** ramo, adicionar uma ação "enviar e-mail" para o seu fornecedor de e-mail. 
   Configure esta ação ao seguir os passos abaixo desta imagem:

   ![Adicionar ação "Enviar e-mail" para "Se verdadeiro" ramo](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Na **para** , insira seu endereço de e-mail para fins de teste.

   1. Na **assunto** campo, introduza este texto:

      ```Time to leave: Traffic more than 10 minutes```

   1. Na **corpo** campo, introduza este texto com um espaço à direita: 

      ```Travel time: ```

      Enquanto o cursor é apresentado na **corpo** campo, a lista de conteúdo dinâmico permanece aberta para que possa selecionar todos os parâmetros que estão disponíveis neste momento.

   1. Na lista de conteúdo dinâmico, escolha **Expressão**.

   1. Localize e selecione o **div()** função. 
      Coloque o cursor no dentro de parênteses da função.

   1. Embora seja o cursor dentro de parênteses da função, escolha **conteúdo dinâmico** para que a lista de conteúdo dinâmico apareça. 
   
   1. Partir do **Get route** secção, selecione a **tráfego Duration Traffic** campo.

      ![Selecione "Tráfego Duration Traffic"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Depois do campo é resolvido para o formato JSON, adicione uma **vírgula** (```,```) seguido do número ```60``` , para que converter o valor na **tráfego Duration Traffic** de segundos para minutos. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Sua expressão agora este aspeto:

      ![Expressão de conclusão](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Quando tiver terminado, escolha **OK**.

   1. Depois da expressão é resolvido, adicione este texto com um espaço à esquerda: ``` minutes```
  
       Sua **corpo** campo agora este aspeto:

       ![Campo de "Corpo" concluído](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

1. Guarde a aplicação lógica.

Em seguida, adicione um âmbito para que possa agrupar ações específicas e avaliar o respetivo estado.

## <a name="add-a-scope"></a>Adicionar âmbito

1. Se ainda não o tiver feito, abra a aplicação lógica no Estruturador da aplicação lógica. 

1. Adicione um âmbito na localização de fluxo de trabalho que pretende. Por exemplo, para adicionar um âmbito entre passos existentes no fluxo de trabalho de aplicação lógica, siga estes passos: 

   1. Mova o ponteiro do mouse sobre a seta para onde pretende adicionar o âmbito. 
   Escolha o **sinal** (**+**) > **adicionar uma ação**.

      ![Adicionar âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Na caixa de pesquisa, introduza "escopo" como o filtro. 
   Selecione o **âmbito** ação.

## <a name="add-steps-to-scope"></a>Adicione passos ao âmbito

1. Agora, adicionar os passos ou arraste passos existentes que pretende executar dentro do escopo. Neste exemplo, arraste estas ações para o âmbito:
      
   * **Obter a rota**
   * **Se o tempo de tráfego é mais do que o período de tempo especificado**, que inclui ambos os **verdadeira** e **falso** ramos

   A aplicação lógica agora este aspeto:

   ![Âmbito adicionado](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Sob o escopo, adicione uma condição que verifica o estado de âmbito. Mudar o nome da condição com a descrição: **Se a falha de âmbito**

   ![Adicionar condição para verificar o estado de âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. A condição, adicione essas expressões que se verificar se o estado de âmbito é igual a "Falhado" ou "Aborted". 

   1. Para adicionar outra linha, escolha **adicionar**. 

   1. Em cada linha, clique dentro da caixa esquerda para que a lista de conteúdo dinâmico apareça. 
   Na lista de conteúdo dinâmico, escolha **expressão**. Na caixa de edição, introduza a expressão e, em seguida, escolha **OK**: 
   
      `result('Scope')[0]['status']`

      ![Adicionar a expressão que verifica o estado de âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Para as duas linhas, selecione **é igual a** como o operador. 
   
   1. Para os valores de comparação, na primeira linha, introduza `Failed`. 
   Na segunda linha, introduza `Aborted`. 

      Quando estiver pronto, a condição terá o aspeto deste exemplo:

      ![Adicionar a expressão que verifica o estado de âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Agora, defina a condição `runAfter` propriedade para que a condição verifica o estado de escopo e executa a ação correspondente que definir em passos posteriores.

   1. Na **se a falha de âmbito** condição, escolha a **reticências** (...) botão e, em seguida, escolha **configurar execução posterior**.

      ![Configurar a propriedade "runAfter"](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Selecione todos os Estados de âmbito: **for concluída com êxito**, **falhou**, **é ignorada**, e **foi excedido**

      ![Selecionar Estados de âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Quando tiver terminado, selecione **Concluído**. 
   A condição agora mostra um ícone de "informações".

1. Na **se for true** e **se for FALSO** ramos, adicionar as ações que deseja realizar com base em cada Estado de escopo, por exemplo, enviar um e-mail ou a mensagem.

   ![Adicionar ações a realizar com base no estado de âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Guarde a aplicação lógica.

A aplicação lógica concluída agora este aspeto:

![Aplicação lógica concluída com âmbito](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Teste seu trabalho

Na barra de ferramentas da estruturador, escolha **executar**. Se todas as ações com âmbito tiver êxito, receberá uma mensagem de "Escopo foi concluída com êxito". Se todas as ações com âmbito não tiver êxito, receberá uma mensagem "Falha de âmbito". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definição de JSON

Se estiver a trabalhar na vista de código, pode definir uma estrutura de âmbito na definição de JSON da sua aplicação lógica em vez disso. Por exemplo, eis a definição de JSON para o acionador e ações na aplicação lógica anterior:

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

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em recursos e sugestões, visite o [site de comentários de utilizadores do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Execute os passos com base numa condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Execute os passos com base nos valores diferentes (declarações do comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repita os passos (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou unir a passos paralelos (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
