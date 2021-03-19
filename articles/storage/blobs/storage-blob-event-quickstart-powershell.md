---
title: Envie eventos de armazenamento Azure Blob para o ponto final da Web - PowerShell | Microsoft Docs
description: Use a Grelha de Eventos Azure para subscrever eventos de armazenamento Blob, desencadear um evento e ver o resultado. Utilize o Azure PowerShell para encaminhar eventos de armazenamento para um ponto final web.
author: normesta
ms.author: normesta
ms.reviewer: dastanfo
ms.date: 08/23/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 06be168ff9dfd55a56578b3afcdab8d984416756
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078015"
---
# <a name="quickstart-route-storage-events-to-web-endpoint-with-powershell"></a>Quickstart: Route storage events to web endpoint with PowerShell

O Azure Event Grid é um serviço de eventos para a cloud. Neste artigo, você usa a Azure PowerShell para subscrever eventos de armazenamento Blob, desencadear um evento e ver o resultado. 

Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este artigo, vai enviar eventos para uma aplicação Web que recolhe e apresenta as mensagens.

Quando tiver terminado, verá que os dados do evento foram enviados para a aplicação Web.

![Ver resultados](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>Configuração

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo requer que estejas a executar a versão mais recente do Azure PowerShell. Se precisar de instalar ou atualizar, consulte [instalar e configurar a Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na sua subscrição Azure com o `Connect-AzAccount` comando e siga as instruções no ecrã para autenticar.

```powershell
Connect-AzAccount
```

Este exemplo usa **westus2** e armazena a seleção numa variável para uso em toda.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos do Event Grid são recursos do Azure e têm de ser colocados num grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

O exemplo seguinte cria um grupo de recursos com o nome **gridResourceGroup** na localização **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Os eventos de armazenamento de blobs estão disponíveis em contas de armazenamento para fins gerais v2 e contas de armazenamento de Blobs. As contas de armazenamento de **Fins gerais v2** suportam todas as funcionalidades de todos os serviços de armazenamento, incluindo Blobs, Ficheiros, Filas e Tabelas. Uma **conta de armazenamento Blob** é uma conta de armazenamento especializada para armazenar os seus dados não estruturados como bolhas (objetos) no Azure Storage. As contas de armazenamento de Blobs são semelhantes às contas de armazenamento para fins gerais e partilham todas as excelentes características de durabilidade, disponibilidade, escalabilidade e desempenho que utiliza atualmente, incluindo 100% de consistência com a API dos blobs de blocos e dos blobs de acréscimo. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

Crie uma conta de armazenamento Blob com replicação LRS utilizando [o New-AzStorageAccount,](/powershell/module/az.storage/New-azStorageAccount)em seguida, recupere o contexto da conta de armazenamento que define a conta de armazenamento a ser utilizada. Ao efetuar ações em contas de armazenamento, referencia o contexto em vez de fornecer repetidamente as credenciais. Este exemplo cria uma conta de armazenamento chamada **gridstorage** com armazenamento localmente redundante (LRS). 

> [!NOTE]
> Os nomes das contas de armazenamento estão num espaço de nome global, pelo que precisa de anexar alguns caracteres aleatórios ao nome fornecido neste script.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever o tópico, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para a aplicação Web. O nome da aplicação Web deve ser exclusivo, porque faz parte da entrada DNS.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Deverá ver o site sem mensagens atualmente apresentadas.

[!INCLUDE [event-grid-register-provider-powershell.md](../../../includes/event-grid-register-provider-powershell.md)]

## <a name="subscribe-to-your-storage-account"></a>Subscrever à sua conta de armazenamento

Você subscreve um tópico para dizer à Grade de Eventos quais eventos você quer rastrear. O exemplo seguinte subscreve a conta de armazenamento que criou e passa o URL da sua aplicação web como ponto final para notificação de eventos. O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

![Ver evento da subscrição](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Acionar um evento a partir do armazenamento de Blobs

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Primeiro, vamos criar um contentor e um objeto. Então, vamos carregar o objeto para o contentor.

```powershell
$containerName = "gridcontainer"
New-AzStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Verifique a aplicação Web para ver o evento que acabámos de enviar.

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Limpar os recursos
Se pretender continuar a trabalhar com esta conta de armazenamento e subscrição de eventos, não limpe os recursos criados neste artigo. Se não pretende continuar, use o seguinte comando para apagar os recursos criados neste artigo.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos e subscrições de eventos, saiba mais sobre os Eventos de armazenamento de Blobs o que o Event Grid pode ajudá-lo a fazer:

- [Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)
- [Sobre o Event Grid](../../event-grid/overview.md)
