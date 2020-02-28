---
title: Envie métricas para a base de dados métrica do Monitor Azure utilizando a API REST
description: Envie métricas personalizadas para um recurso Azure para a loja métrica Do Monitor Azure utilizando uma API REST
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 84709c022631543101889f784231158ebb96b6f3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662269"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Envie métricas personalizadas para um recurso Azure para a loja métrica Do Monitor Azure utilizando uma API REST

Este artigo mostra-lhe como enviar métricas personalizadas para os recursos do Azure para a loja de métricas Do Monitor Azure através de uma API REST. Depois das métricas estarem no Monitor Azure, pode fazer todas as coisas com elas que faz com métricas padrão. Exemplos são cartografar, alertar e encaminhar para outras ferramentas externas.  

>[!NOTE]  
>A API REST apenas permite o envio de métricas personalizadas para os recursos Azure. Para enviar métricas para recursos em diferentes ambientes ou no local, pode utilizar insights de [aplicação](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Criar e autorizar um principal de serviço para emitir métricas 

Crie um diretor de serviço no seu inquilino de Diretório Ativo Azure utilizando as instruções encontradas na [Create a service principal](../../active-directory/develop/howto-create-service-principal-portal.md). 

Note o seguinte durante este processo: 

- Pode introduzir qualquer URL para o URL de entrada.  
- Crie um novo segredo de cliente para esta aplicação.  
- Guarde a chave e o ID do cliente para utilização em etapas posteriores.  

Dê a app criada como parte do passo 1, Monitoring Metrics Publisher, permissões para o recurso contra o que pretende emitir métricas contra. Se planeia utilizar a app para emitir métricas personalizadas contra muitos recursos, pode conceder essas permissões ao nível de recursos ou subscrição. 

## <a name="get-an-authorization-token"></a>Obter um sinal de autorização
Abra um pedido de comando e execute o seguinte comando:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Guarde o sinal de acesso da resposta.

![ficha de acesso](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Emita a métrica através da API REST 

1. Colhe o seguinte JSON num ficheiro e guarde-o como **custommetric.json** no seu computador local. Atualize o parâmetro de tempo no ficheiro JSON: 
    
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

1. Na janela de solicitação do seu comando, publique os dados métricos: 
   - **azureRegion.** Deve corresponder à região de implantação do recurso para o qual está a emitir métricas. 
   - **recursoID**.  Identificação de recursos do recurso Azure contra o qual está a seguir a métrica.  
   - **AccessToken**. Cola o símbolo que adquiriste anteriormente.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Alterar a marca de tempo e os valores no ficheiro JSON. 
1. Repita os dois passos anteriores algumas vezes, para que tenha dados durante vários minutos.

## <a name="troubleshooting"></a>Resolução de problemas 
Se receber uma mensagem de erro com alguma parte do processo, considere as seguintes informações de resolução de problemas:

1. Não pode emitir métricas contra uma subscrição ou grupo de recursos como o seu recurso Azure. 
1. Não se pode colocar uma métrica na loja com mais de 20 minutos de idade. A loja métrica está otimizada para alerta e gráfico em tempo real. 
2. O número de nomes de dimensão deve corresponder aos valores e vice-versa. Verifique os valores. 
2. Pode sê-lo contra uma região que não suporta métricas personalizadas. Ver [regiões apoiadas.](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>Veja as suas métricas 

1. Inicie sessão no Portal do Azure. 

1. No menu à esquerda, selecione **Monitor**. 

1. Na página **Monitor,** selecione **Métricas**. 

   ![Selecione Métricas](./media/metrics-store-custom-rest-api/metrics.png) 

1. Mude o período de agregação para **30 minutos**.  

1. No menu de desistência de **recursos,** selecione o recurso contra o que emitia a métrica.  

1. No menu de lançamento dos espaços de **nome,** selecione **QueueProcessing**. 

1. No menu de entrega de **métricas,** selecione **QueueDepth**.  

 
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](../../azure-monitor/platform/metrics-custom-overview.md)

