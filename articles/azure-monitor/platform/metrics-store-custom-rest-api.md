---
title: Envie métricas para a base de dados métrica do Monitor Azure utilizando a API REST
description: Envie métricas personalizadas para um recurso Azure para a loja métrica Azure Monitor utilizando uma API REST
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: c315cc8c9dfcfa66999ff263fab95f414061e54e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321263"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Envie métricas personalizadas para um recurso Azure para a loja métrica Azure Monitor utilizando uma API REST

Este artigo mostra-lhe como enviar métricas personalizadas para recursos Azure para a loja de métricas Azure Monitor através de uma API REST. Depois de as métricas estarem no Azure Monitor, podes fazer todas as coisas com eles que fazes com as métricas padrão. Exemplos são o gráfico, o alerta e o encaminhamento para outras ferramentas externas.  

>[!NOTE]  
>A API REST apenas permite o envio de métricas personalizadas para recursos Azure. Para enviar métricas de recursos em diferentes ambientes ou no local, pode utilizar [o Application Insights](../app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Criar e autorizar um principal de serviço a emitir métricas 

Crie um diretor de serviço no seu inquilino Azure Ative Directory utilizando as instruções encontradas na [Create a service principal](../../active-directory/develop/howto-create-service-principal-portal.md). 

Observe o seguinte enquanto passa por este processo: 

- Pode introduzir qualquer URL para o URL de entrada.  
- Crie um novo segredo de cliente para esta aplicação.  
- Guarde a chave e o ID do cliente para utilização em etapas posteriores.  

Dê a app criada como parte do passo 1, Monitoring Metrics Publisher, permissões para o recurso contra o quais deseja emitir métricas. Se pretender utilizar a app para emitir métricas personalizadas contra muitos recursos, pode conceder essas permissões ao nível do grupo de recursos ou da subscrição. 

## <a name="get-an-authorization-token"></a>Obter um sinal de autorização
Abra um pedido de comando e executar o seguinte comando:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Guarde o sinal de acesso da resposta.

![Ficha de acesso](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Emite a métrica através da API REST 

1. Cole o JSON seguinte num ficheiro e guarde-o à medida ** quecustommetric.jsno** computador local. Atualize o parâmetro de tempo no ficheiro JSON: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. Na janela de pedido de comando do seu comando, regisque os dados métricos: 
   - **AzureRegion**. Deve coincidir com a região de implantação do recurso para o qual está a emitir métricas. 
   - **resourceID**.  Identificação de recursos do recurso Azure contra o qual está a seguir a métrica.  
   - **AccessToken**. Cole o símbolo que adquiriu anteriormente.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Altere a hora e os valores no ficheiro JSON. 
1. Repita os dois passos anteriores algumas vezes, para que tenha dados durante vários minutos.

## <a name="troubleshooting"></a>Resolução de problemas 
Se receber uma mensagem de erro com alguma parte do processo, considere as seguintes informações de resolução de problemas:

1. Não pode emitir métricas contra uma subscrição ou grupo de recursos como o seu recurso Azure. 
1. Não se pode pôr uma métrica na loja com mais de 20 minutos. A loja métrica está otimizada para alertar e fazer gráficos em tempo real. 
2. O número de nomes de dimensão deve corresponder aos valores e vice-versa. Verifique os valores. 
2. Pode estar a emitir métricas contra uma região que não suporta métricas personalizadas. Ver [regiões apoiadas.](./metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>Veja as suas métricas 

1. Inicie sessão no Portal do Azure. 

1. No menu da esquerda, selecione **Monitor**. 

1. Na página **Monitor,** selecione **Métricas.** 

   ![Selecione Métricas](./media/metrics-store-custom-rest-api/metrics.png) 

1. Altere o período de agregação para **durar 30 minutos**.  

1. No menu suspenso de **recursos,** selecione o recurso contra o quais emitia a métrica.  

1. No menu drop-down dos **espaços de nome,** selecione **QueueProcessing**. 

1. No menu suspenso de **métricas,** selecione **QueueDepth**.  

 
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](./metrics-custom-overview.md)

