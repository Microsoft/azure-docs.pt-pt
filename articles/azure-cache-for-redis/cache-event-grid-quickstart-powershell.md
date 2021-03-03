---
title: 'Quickstart: Route Azure Cache para eventos redis para o ponto final da web com PowerShell'
description: Use a Azure Event Grid para subscrever a Azure Cache para eventos Redis, envie os eventos para um Webhook e trate dos eventos numa aplicação web.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 0ab959d531ff1080e5e6a640410b4955e4cfeff7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652203"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Quickstart: Route Azure Cache para eventos redis para o ponto final da web com PowerShell

O Azure Event Grid é um serviço de eventos para a cloud. Neste arranque rápido, você usará a Azure PowerShell para subscrever a Azure Cache para eventos Redis, desencadear um evento e ver os resultados. 

Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este arranque rápido, irá enviar eventos para uma aplicação web que irá recolher e exibir as mensagens. Quando completar os passos descritos neste quickstart, verá que os dados do evento foram enviados para a aplicação web.

## <a name="setup"></a>Configuração

Este quickstart requer que estejas a executar a versão mais recente do Azure PowerShell. Se precisar de instalar ou atualizar, consulte [instalar e configurar a Azure PowerShell](/powershell/azure/install-Az-ps).

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

Os tópicos da Grelha de Eventos são implantados como recursos individuais da Azure e devem ser aprovisionados sob um grupo de recursos Azure. Um grupo de recursos é uma coleção lógica na qual os recursos da Azure são implantados e geridos.

Criar um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

O exemplo seguinte cria um grupo de recursos com o nome **gridResourceGroup** na localização **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma Cache Azure para a instância Redis 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
Para obter mais informações sobre a criação de uma instância de cache no PowerShell, consulte a [referência Azure PowerShell](/powershell/module/az.rediscache/new-azrediscache?view=azps-5.2.0). 

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

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Site vazio do espectador de grelha de evento.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Subscreva o seu Azure Cache para o evento Redis

Neste passo, você subscreverá um tópico para dizer à Grade de Eventos quais os eventos que pretende acompanhar. O exemplo a seguir subscreve a instância de cache que criou e passa o URL da sua aplicação web como ponto final para notificação de eventos. O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Espectador de grelha de eventos Azure.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Desencadear um evento de Azure Cache para Redis

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
Para obter mais informações sobre a importação em PowerShell, consulte a [referência Azure PowerShell](/powershell/module/az.rediscache/import-azrediscache?view=azps-5.2.0). 

Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Verifique a aplicação Web para ver o evento que acabámos de enviar.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Limpar os recursos
Se planeia continuar a trabalhar com este Azure Cache para a redis instância e subscrição de eventos, não limpe os recursos criados neste quickstart. Se não pretende continuar, use o seguinte comando para eliminar os recursos criados neste arranque rápido.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos e subscrições de eventos, saiba mais sobre a Azure Cache para eventos Redis e o que a Grade de Eventos pode ajudá-lo a fazer:

- [Reagir a Azure Cache para eventos redis](cache-event-grid.md)
- [Sobre o Event Grid](../event-grid/overview.md)