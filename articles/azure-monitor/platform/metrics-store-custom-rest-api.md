---
title: Enviar métricas para o banco de dados de métricas Azure Monitor usando a API REST
description: Enviar métricas personalizadas para um recurso do Azure para o repositório de métrica Azure Monitor usando uma API REST
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: a19b59c758f31ff1ef3416b59031202193d50522
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285950"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Enviar métricas personalizadas para um recurso do Azure para o repositório de métrica Azure Monitor usando uma API REST

Este artigo mostra como enviar métricas personalizadas para recursos do Azure para o armazenamento de métricas Azure Monitor por meio de uma API REST. Depois que as métricas estiverem em Azure Monitor, você poderá fazer todas as coisas com elas que você faz com as métricas padrão. Os exemplos são gráficos, alertas e roteamentos para outras ferramentas externas.  

>[!NOTE]  
>A API REST só permite o envio de métricas personalizadas para recursos do Azure. Para enviar métricas para recursos em ambientes diferentes ou locais, você pode usar [Application insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Criar e autorizar uma entidade de serviço para emitir métricas 

Crie uma entidade de serviço em seu locatário Azure Active Directory usando as instruções encontradas em [criar uma entidade de serviço](../../active-directory/develop/howto-create-service-principal-portal.md). 

Observe o seguinte ao percorrer este processo: 

- Você pode inserir qualquer URL para a URL de entrada.  
- Crie um novo segredo do cliente para este aplicativo.  
- Salve a chave e a ID do cliente para uso em etapas posteriores.  

Dê ao aplicativo criado como parte da etapa 1, monitorando métricas de monitoramento, permissões para o recurso do qual você deseja emitir métricas. Se você planeja usar o aplicativo para emitir métricas personalizadas em vários recursos, você pode conceder essas permissões no nível do grupo de recursos ou da assinatura. 

## <a name="get-an-authorization-token"></a>Obter um token de autorização
Abra um prompt de comando e execute o seguinte comando:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Salve o token de acesso da resposta.

![Token de acesso](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Emitir a métrica por meio da API REST 

1. Cole o JSON a seguir em um arquivo e salve-o como **custommetric. JSON** em seu computador local. Atualize o parâmetro de tempo no arquivo JSON: 
    
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

1. Na janela do prompt de comando, poste os dados da métrica: 
   - **azureRegion**. Deve corresponder à região de implantação do recurso para o qual você está emitindo métricas. 
   - **resourceID**.  ID de recurso do recurso do Azure no qual você está acompanhando a métrica.  
   - **AccessToken**. Cole o token que você adquiriu anteriormente.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Altere o carimbo de data/hora e os valores no arquivo JSON. 
1. Repita as duas etapas anteriores algumas vezes, para que você tenha dados por vários minutos.

## <a name="troubleshooting"></a>Resolução de problemas 
Se você receber uma mensagem de erro com alguma parte do processo, considere as seguintes informações de solução de problemas:

1. Você não pode emitir métricas em relação a uma assinatura ou grupo de recursos como seu recurso do Azure. 
1. Você não pode colocar uma métrica na loja com mais de 20 minutos de idade. O repositório de métricas é otimizado para alertas e gráficos em tempo real. 
2. O número de nomes de dimensão deve corresponder aos valores e vice-versa. Verifique os valores. 
2. Você pode estar emitindo métricas em uma região que não oferece suporte a métricas personalizadas. Consulte [regiões com suporte](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Exibir suas métricas 

1. Inicie sessão no Portal do Azure. 

1. No menu à esquerda, selecione **Monitor**. 

1. Na página **monitorar** , selecione **métricas**. 

   ![Selecionar métricas](./media/metrics-store-custom-rest-api/metrics.png) 

1. Altere o período de agregação para os **últimos 30 minutos**.  

1. No menu suspenso de **recursos** , selecione o recurso em relação ao qual você emitiu a métrica.  

1. No menu suspenso **namespaces** , selecione **QueueProcessing**. 

1. No menu suspenso **métricas** , selecione **QueueDepth**.  

 
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas](../../azure-monitor/platform/metrics-custom-overview.md).

