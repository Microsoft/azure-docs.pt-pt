---
title: Enviar métricas personalizadas para um recurso do Azure para o arquivo de métrica de Monitor do Azure com uma API REST
description: Enviar métricas personalizadas para um recurso do Azure para o arquivo de métrica de Monitor do Azure com uma API REST
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: c7ec3ba960929250f2d23d09b9a5ab06e3f6cd38
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095420"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Enviar métricas personalizadas para um recurso do Azure para o arquivo de métrica de Monitor do Azure com uma API REST

Este artigo mostra-lhe como enviar métricas personalizadas para recursos do Azure para o armazenamento de métricas do Azure Monitor através de uma API REST. Depois das métricas no Azure Monitor, pode fazer tudo com os mesmos que fazer com métricas padrão. Exemplos são gráficos, alertas e encaminhamento-las para outras ferramentas externas.  

>[!NOTE]  
>A API REST só permite o envio de métricas personalizadas para recursos do Azure. Para enviar métricas para recursos em ambientes diferentes ou no local, pode utilizar [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Criar e autorizar um principal de serviço para emitir métricas 

Criar um principal de serviço no seu inquilino do Azure Active Directory com as instruções em [criar um principal de serviço](../../active-directory/develop/howto-create-service-principal-portal.md). 

Enquanto executar esse processo, tenha em atenção o seguinte: 

- Pode introduzir qualquer URL para o URL de início de sessão.  
- Crie um novo segredo de cliente para esta aplicação.  
- Guarde a chave e o ID de cliente para uso em passos posteriores.  

Atribua a aplicação criada como parte do passo 1, fabricante de métricas de monitorização e permissões para o recurso que pretende emitir métricas contra. Se planeia utilizar a aplicação para emitir métricas personalizadas em relação a muitos recursos, pode conceder estas permissões ao nível de grupo ou uma subscrição do recurso. 

## <a name="get-an-authorization-token"></a>Obter um token de autorização
Abra uma linha de comandos e execute o seguinte comando:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Guarde o token de acesso da resposta.

![Token de acesso](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Emitir a métrica através da API REST 

1. Cole o seguinte JSON num ficheiro e guarde-o como **custommetric.json** no seu computador local. Atualização do parâmetro da hora no ficheiro JSON: 
    
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

1. Na janela do prompt de comando, publicar os dados de métrica: 
   - **azureRegion**. Tem de corresponder à região de implementação do recurso que está a emitir métricas para. 
   - **resourceID**.  ID de recurso do recurso do Azure que está a controlar a métrica em relação a.  
   - **AccessToken**. Cole o token que obteve anteriormente.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Altere os valores no ficheiro JSON e timestamp. 
1. Repita os dois passos anteriores algumas vezes, para que tenha dados durante vários minutos.

## <a name="troubleshooting"></a>Resolução de problemas 
Se receber uma mensagem de erro com alguma parte do processo, considere as seguintes informações de resolução de problemas:

1. Não é possível emitir métricas relativamente a uma subscrição ou grupo de recursos como o seu recurso do Azure. 
1. Não é possível colocar uma métrica para o arquivo que é a mais de 20 minutos de antigos. O arquivo de métrica está otimizado para alertas e criação de gráficos em tempo real. 
2. O número de nomes de dimensão deve correspondem aos valores e vice-versa. Verifique os valores. 
2. Poderá ser emitir métricas relativamente a uma região que não suporta métricas personalizadas. Ver [regiões suportadas](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Veja as suas métricas 

1. Inicie sessão no Portal do Azure. 

1. No menu da esquerda, selecione **Monitor**. 

1. Sobre o **Monitor** página, selecione **métricas**. 

   ![Selecione as métricas](./media/metrics-store-custom-rest-api/metrics.png) 

1. Alterar o período de agregação para **últimos 30 minutos**.  

1. Na **recursos** menu pendente, selecione o recurso é emitida a métrica em relação a.  

1. Na **espaços de nomes** menu pendente, selecione **QueueProcessing**. 

1. Na **métricas** menu pendente, selecione **QueueDepth**.  

 
## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [métricas personalizadas](../../azure-monitor/platform/metrics-custom-overview.md).

