---
title: Agrupar e executar ações por escopo – aplicativos lógicos do Azure
description: Criar ações com escopo executadas com base no status do grupo nos aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: b0f53d1dbcd5b8bbbe38ffe3dd9ba62087ed3432
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680010"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Executar ações com base no status do grupo usando escopos em aplicativos lógicos do Azure

Para executar ações somente depois que outro grupo de ações for bem-sucedida ou falhar, agrupe essas ações dentro de um *escopo*. Essa estrutura é útil quando você deseja organizar ações como um grupo lógico, avaliar o status desse grupo e executar ações baseadas no status do escopo. Depois que todas as ações em um escopo terminarem de ser executadas, o escopo também obterá seu próprio status. Por exemplo, você pode usar escopos quando desejar implementar a [manipulação de exceção e erro](../logic-apps/logic-apps-exception-handling.md#scopes). 

Para verificar o status de um escopo, você pode usar os mesmos critérios usados para determinar o status de execução de um aplicativo lógico, como "êxito", "falha", "cancelado" e assim por diante. Por padrão, quando todas as ações do escopo forem bem-sucedidas, o status do escopo será marcado como "êxito". Mas quando qualquer ação no escopo falha ou é cancelada, o status do escopo é marcado como "falha". Para limites em escopos, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md). 

Por exemplo, aqui está um aplicativo lógico de alto nível que usa um escopo para executar ações específicas e uma condição para verificar o status do escopo. Se alguma ação no escopo falhar ou terminar inesperadamente, o escopo será marcado como "falha" ou "anulado" respectivamente, e o aplicativo lógico enviará uma mensagem "falha no escopo". Se todas as ações com escopo forem bem-sucedidas, o aplicativo lógico enviará uma mensagem de "escopo bem-sucedido".

![Configurar o gatilho "Agenda-recorrência"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir o exemplo neste artigo, você precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma conta de email de qualquer provedor de email com suporte dos aplicativos lógicos. Este exemplo usa Outlook.com. Se você usar um provedor diferente, o fluxo geral permanecerá o mesmo, mas sua interface do usuário aparecerá diferente.

* Uma chave do Bing Maps. Para obter essa chave, consulte <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">obter uma chave do Bing Maps</a>.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Criar aplicativo lógico de exemplo

Primeiro, crie este aplicativo lógico de exemplo para que você possa adicionar um escopo posteriormente:

![Criar aplicativo lógico de exemplo](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Um gatilho **Schedule-recorrência** que verifica o serviço Bing Maps em um intervalo especificado por você
* Uma ação do **Bing Maps-obter rota** que verifica o tempo de viagem entre dois locais
* Uma instrução condicional que verifica se o tempo de viagem excede o tempo de viagem especificado
* Uma ação que envia um email de que o tempo de viagem atual excede o tempo especificado

Você pode salvar seu aplicativo lógico a qualquer momento, portanto, salve seu trabalho com frequência.

1. Entre no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, se ainda não tiver feito isso. Criar uma aplicação lógica em branco.

1. Adicione o gatilho **agenda-recorrência** com estas configurações: **intervalo** = "1" e **frequência** = "minuto"

   ![Configurar o gatilho "Agenda-recorrência"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Para simplificar visualmente a exibição e ocultar os detalhes de cada ação no designer, recolha a forma de cada ação ao progredir por meio dessas etapas.

1. Adicione a ação **Bing Maps-obter rota** . 

   1. Se você ainda não tiver uma conexão do Bing Maps, será solicitado a criar uma conexão.

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Nome da Ligação** | BingMapsConnection | Indique um nome para a ligação. | 
      | **Chave de API** | <*your-Bing-Maps-key*> | Introduza a chave do Mapas Bing que recebeu anteriormente. | 
      ||||  

   1. Configure sua ação **obter rota** , conforme mostrado na tabela abaixo desta imagem:

      ![Configurar a ação "Bing Maps-obter rota"](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Para obter mais informações sobre estes parâmetros, veja [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Calcular um percurso).

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Waypoint 1** | <*iniciar* > | Insira a origem da rota. | 
      | **Waypoint 2** | >*final* de < | Insira o destino de sua rota. | 
      | **Avoid** | Nenhuma | Insira os itens a serem evitados em sua rota, como rodovias, tarifas e assim por diante. Para obter os valores possíveis, consulte [calcular uma rota](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimize** | timeWithTraffic | Selecione um parâmetro para otimizar a rota, como distância, tempo com as informações de tráfego atuais e assim por diante. Este exemplo usa este valor: "timeWithTraffic" | 
      | **Distance unit** | <*your-preference*> | Insira a unidade de distância para calcular a rota. Este exemplo usa este valor: "milhar" | 
      | **Travel mode** | Driving | Insira o modo de viagem para sua rota. Este exemplo usa esse valor de "condução" | 
      | **Transit Date-Time** | Nenhuma | Aplica-se somente ao modo de trânsito. | 
      | **Tipo de data/tipo de trânsito** | Nenhuma | Aplica-se somente ao modo de trânsito. | 
      ||||  

1. [Adicione uma condição](../logic-apps/logic-apps-control-flow-conditional-statement.md) que verifica se o tempo de viagem atual com o tráfego excede um tempo especificado. 
   Para este exemplo, siga estas etapas:

   1. Renomeie a condição com esta descrição: **se o tempo de tráfego for maior do que o tempo especificado**

   1. Na coluna mais à esquerda, clique dentro da caixa **escolher um valor** para que a lista de conteúdo dinâmico seja exibida. Nessa lista, selecione o campo **tráfego de duração da viagem** , que está em segundos. 

      ![Criar condição](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Na caixa do meio, selecione este operador: **é maior que**

   1. Na coluna mais à direita, insira esse valor de comparação, que é em segundos e equivalente a 10 minutos: **600**

      Quando estiver pronto, a condição terá o aspeto deste exemplo:

      ![Condição concluída](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Na ramificação **If true** , adicione uma ação "enviar email" para seu provedor de email. 
   Configure essa ação seguindo as etapas descritas nesta imagem:

   ![Adicionar uma ação "enviar um email" para o Branch "se verdadeiro"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. No campo **para** , insira seu endereço de email para fins de teste.

   1. No campo **assunto** , digite este texto:

      ```Time to leave: Traffic more than 10 minutes```

   1. No campo **corpo** , insira este texto com um espaço à direita: 

      ```Travel time:```

      Enquanto o cursor aparece no campo **corpo** , a lista de conteúdo dinâmico permanece aberta para que você possa selecionar os parâmetros disponíveis neste ponto.

   1. Na lista de conteúdo dinâmico, escolha **Expressão**.

   1. Localize e selecione a função **div ()** . 
      Coloque o cursor dentro dos parênteses da função.

   1. Enquanto o cursor estiver dentro dos parênteses da função, escolha **conteúdo dinâmico** para que a lista de conteúdo dinâmico seja exibida. 
   
   1. Na seção **obter rota** , selecione o campo **tráfego de duração do tráfego** .

      ![Selecione "tráfego de duração do tráfego"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Depois que o campo for resolvido para o formato JSON, adicione uma **vírgula** (```,```) seguida pelo número ```60``` para que você converta o valor no **tráfego de duração de tráfego** de segundos para minutos. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Sua expressão agora é semelhante a este exemplo:

      ![Expressão de término](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Quando terminar, escolha **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Depois que a expressão for resolvida, adicione este texto com um espaço à esquerda: ``` minutes```
  
       O campo **corpo** agora é semelhante a este exemplo:

       ![Finished campo "corpo" ](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Guarde a aplicação lógica.

Em seguida, adicione um escopo para que você possa agrupar ações específicas e avaliar seu status.

## <a name="add-a-scope"></a>Adicionar um escopo

1. Se você ainda não fez isso, abra seu aplicativo lógico no designer de aplicativo lógico. 

1. Adicione um escopo no local do fluxo de trabalho desejado. Por exemplo, para adicionar um escopo entre as etapas existentes no fluxo de trabalho do aplicativo lógico, siga estas etapas: 

   1. Mova o ponteiro sobre a seta onde você deseja adicionar o escopo. 
   Escolha o **sinal de adição** ( **+** ) > **Adicionar uma ação**.

      ![Adicionar um escopo](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Na caixa de pesquisa, digite "Scope" como filtro. 
   Selecione a ação de **escopo** .

## <a name="add-steps-to-scope"></a>Adicionar etapas ao escopo

1. Agora, adicione as etapas ou arraste as etapas existentes que você deseja executar dentro do escopo. Para este exemplo, arraste essas ações para o escopo:
      
   * **Obter rota**
   * **Se o tempo de tráfego for maior do que o tempo especificado**, o que inclui as ramificações **verdadeiro** e **falso**

   Seu aplicativo lógico agora é semelhante a este exemplo:

   ![Escopo adicionado](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. No escopo, adicione uma condição que verifica o status do escopo. Renomeie a condição com esta descrição: **se o escopo falhou**

   ![Adicionar condição para verificar o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Na condição, adicione essas expressões que verificam se o status do escopo é igual a "falha" ou "anulado". 

   1. Para adicionar outra linha, escolha **Adicionar**. 

   1. Em cada linha, clique dentro da caixa à esquerda para que a lista de conteúdo dinâmico seja exibida. 
   Na lista conteúdo dinâmico, escolha **expressão**. Na caixa de edição, insira esta expressão e, em seguida, escolha **OK**: 
   
      `result('Scope')[0]['status']`

      ![Adicionar expressão que verifica o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Para ambas as linhas, Select **é igual** a como o operador. 
   
   1. Para os valores de comparação, na primeira linha, digite `Failed`. 
   Na segunda linha, digite `Aborted`. 

      Quando estiver pronto, a condição terá o aspeto deste exemplo:

      ![Adicionar expressão que verifica o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Agora, defina a propriedade `runAfter` da condição para que a condição Verifique o status do escopo e execute a ação correspondente que você definir em etapas posteriores.

   1. Na condição **se o escopo falhar** , escolha o botão de **reticências** (...) e escolha **configurar execução após**.

      ![Configurar a propriedade ' runAfter '](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Selecionar todos estes status de escopo: **é bem-sucedido**, **falhou**, **foi ignorado**e atingiu o **tempo limite**

      ![Selecionar status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Quando tiver terminado, selecione **Concluído**. 
   A condição agora mostra um ícone de "informações".

1. Nas ramificações **se verdadeiro** e **se falso** , adicione as ações que você deseja executar com base em cada status de escopo, por exemplo, enviar um email ou mensagem.

   ![Adicionar ações a serem executadas com base no status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Guarde a aplicação lógica.

Seu aplicativo lógico concluído agora se parece com este exemplo:

![Aplicativo lógico concluído com escopo](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Teste seu trabalho

Na barra de ferramentas do designer, escolha **executar**. Se todas as ações com escopo forem bem-sucedidas, você receberá uma mensagem "escopo bem-sucedido". Se alguma ação com escopo não tiver êxito, você receberá uma mensagem "falha no escopo". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definição de JSON

Se você estiver trabalhando no modo de exibição de código, poderá definir uma estrutura de escopo na definição JSON do aplicativo lógico. Por exemplo, aqui está a definição de JSON para gatilho e ações no aplicativo lógico anterior:

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
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário dos aplicativos lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Executar etapas com base em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas com base em valores diferentes (instruções switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir etapas (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
