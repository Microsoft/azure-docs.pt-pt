---
title: Integre o armazenamento do Azure para notificações e backup de modelos
titleSuffix: Azure Cognitive Services
description: Saiba como integrar o armazenamento do Azure para receber notificações push quando treina ou exporta modelos Custom Vision. Também pode guardar uma cópia de segurança dos modelos exportados.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fd4ad1443f466a78abfc569d5f52f6bdeff2d5be
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048889"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Integre o armazenamento do Azure para notificações e backup

Pode integrar o seu projeto Custom Vision com uma fila de armazenamento de blob Azure para obter notificações push da atividade de treino/exportação de projetos e cópias de backup de modelos publicados. Esta funcionalidade é útil para evitar que o serviço seja continuamente sondada para obter resultados quando as operações prolongadas estiverem a decorrer. Em vez disso, pode integrar as notificações da fila de armazenamento no seu fluxo de trabalho.

Este guia mostra-lhe como utilizar estas APIs REST com cURL. Também pode utilizar um serviço de pedido HTTP como o Carteiro para emitir os pedidos.

> [!NOTE]
> As notificações push dependem do parâmetro opcional _de notificaçãoQueueUri_ na **API CreateProject,** e as cópias de segurança do modelo exigem que também utilize o parâmetro _opcional de exportaçãoModelContainerUri._ Este guia utilizará ambos para o conjunto completo de funcionalidades.

## <a name="prerequisites"></a>Pré-requisitos

- Um recurso de visão personalizada em Azure. Se não tiver um, vá ao portal Azure e [crie um novo recurso de Visão Personalizada](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). Esta funcionalidade não suporta atualmente o recurso Serviço Cognitivo (tudo numa só chave).
- Uma conta de armazenamento Azure com um recipiente de bolhas. Siga [os Exercícios 1 do Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) se precisar de ajuda com este passo.
* [Versão PowerShell 6.0+](/powershell/scripting/install/installing-powershell-core-on-windows)ou uma aplicação de linha de comando semelhante.

## <a name="set-up-azure-storage-integration"></a>Configurar a integração de armazenamento Azure

Aceda ao seu recurso de formação Visão Personalizada no portal Azure, selecione a página **Identidade** e ative a identidade gerida do sistema atribuído.

Em seguida, vá ao seu recurso de armazenamento no portal Azure. Aceda à página **do controlo de acesso (IAM)** e adicione uma atribuição de funções para cada funcionalidade de integração:
* Selecione o seu recurso de treino de visão personalizada e atribua a **função de Contribuinte de Dados blob de armazenamento** se pretender utilizar a função de backup do modelo. 
* Em seguida, selecione o seu recurso de treino de visão personalizada e atribua o **Colaborador de Dados de Fila de Armazenamento** se pretender utilizar a função de fila de notificações.

> [!div class="mx-imgBorder"]
> ![Página de atribuição de função de adicionar conta de armazenamento](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>Obtenha URLs de integração

Em seguida, você receberá os URLs que permitem que o seu recurso De Visão Personalizada aceda a estes pontos finais.

Para o URL de integração da fila de notificações, vá à página **de Filas** da sua conta de armazenamento, adicione uma nova fila e guarde o seu URL para uma localização temporária.

> [!div class="mx-imgBorder"]
> ![Página de fila de armazenamento Azure](./media/storage-integration/queue-url.png) 

Para o URL de integração de backup do modelo, aceda à página **de Contentores** da sua conta de armazenamento e crie um novo recipiente. Em seguida, selecione-o e vá para a página **Propriedades.** Copie o URL para um local temporário.
 
> [!div class="mx-imgBorder"]
> ![Página de propriedades de recipiente de armazenamento Azure](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Integrar projeto de Visão Personalizada

Agora que tem os URLs de integração, pode criar um novo projeto de Visão Personalizada que integra as funcionalidades de Armazenamento Azure. Também pode atualizar um projeto existente para adicionar as funcionalidades.

### <a name="create-new-project"></a>Criar novo projeto

Quando ligar para a [API CreateProject,](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) adicione os parâmetros opcionais _exportarModelContainerUri_ e _notificationQueueUri_. Atribua os valores de URL que obteve na secção anterior. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

Se receber uma `200/OK` resposta, significa que os URLs foram configurado com sucesso. Deve ver os seus valores de URL na resposta JSON também:

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Atualizar o projeto existente

Para atualizar um projeto existente com integração de recursos de armazenamento Azure, ligue para a [API do UpdateProject,](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) utilizando o ID do projeto que pretende atualizar. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

Desafie o organismo de pedido `body` () para o seguinte formato JSON, preenchendo os valores adequados para _exportaçãoModelContainerUri_ e _notificaçãoQueueUri:_

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

Se receber uma `200/OK` resposta, significa que os URLs foram configurado com sucesso.

## <a name="verify-the-connection"></a>Verificar a ligação 

A sua chamada da API na secção anterior já deveria ter desencadeado novas informações na sua conta de armazenamento Azure. 

No seu recipiente designado, deve haver uma bolha de teste dentro de uma pasta **CustomVision-TestPermission.** Esta bolha só existirá temporariamente.

Na sua fila de notificações, deverá ver uma notificação de teste no seguinte formato:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Receber notificações de eventos

Quando estiver pronto, ligue para a API [do TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) no seu projeto para fazer uma operação de treino normal.

Na sua fila de notificação de armazenamento, receberá uma notificação assim que terminar o treino:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

O `"trainingStatus"` campo pode ser `"TrainingCompleted"` `"TrainingFailed"` ou. O `"iterationId"` campo é a identificação do modelo treinado.

## <a name="get-model-export-backups"></a>Obter backups de exportação de modelos

Quando estiver pronto, ligue para a [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API para exportar um modelo treinado para uma plataforma especificada.

No seu recipiente de armazenamento designado, aparecerá uma cópia de reserva do modelo exportado. O nome blob terá o formato:

```
{projectId} - {iterationId}.{platformType}
```

Além disso, receberá uma notificação na sua fila quando a exportação terminar. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

O `"exportStatus"` campo pode ser `"ExportCompleted"` `"ExportFailed"` ou. O `"modelUri"` campo conterá o URL do modelo de backup armazenado no seu recipiente, assumindo que integrou as notificações de fila no início. Caso não o tenha feito, o `"modelUri"` campo mostrará o URL SAS para a sua bolha do modelo De Visão Personalizada.

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a copiar e mover um projeto entre os recursos da Visão Personalizada. Em seguida, explore os docs de referência da API para ver o que mais pode fazer com a Visão Personalizada.
* [Documentação de referência da API REST (formação)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [Documentação de referência da API REST (previsão)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)