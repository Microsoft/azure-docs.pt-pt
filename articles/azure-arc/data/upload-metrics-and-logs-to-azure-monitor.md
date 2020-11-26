---
title: Faça upload de dados de utilização, métricas e registos para o Azure Monitor
description: Faça upload do inventário de recursos, dados de utilização, métricas e registos para o Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 66b10efb6ca93bc6b4dd67d700daaf1f9049de68
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183435"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Faça upload de dados de utilização, métricas e registos para o Azure Monitor

Periodicamente, pode exportar informações de utilização para efeitos de faturação, métricas de monitorização e registos e, em seguida, enviá-la para Azure. A exportação e upload de qualquer um destes três tipos de dados também criará e atualizará o controlador de dados, a instância gerida pelo SQL e os recursos do grupo de servidores de hiperescala PostgreSQL em Azure.

> [!NOTE] 
> Durante o período de pré-visualização, não há qualquer custo para a utilização de serviços de dados ativados pelo Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Antes de poder fazer o upload de dados de utilização, métricas ou registos, precisa de:

* Instalar ferramentas 
* [Registar o `Microsoft.AzureData` fornecedor de recursos](#register-the-resource-provider) 
* [Criar o principal de serviço](#create-service-principal)

## <a name="install-tools"></a>Instalar ferramentas

As ferramentas necessárias incluem: 
* Azure CLI (az) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Consulte [as ferramentas de instalação](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Antes de enviar métricas ou dados do utilizador para o Azure, tem de garantir que a sua subscrição Azure tem o `Microsoft.AzureData` fornecedor de recursos registado.

Para verificar o fornecedor de recursos, executar o seguinte comando:

```azurecli
az provider show -n Microsoft.AzureData -o table
```

Se o fornecedor de recursos não estiver atualmente registado na sua subscrição, pode registá-lo. Para registá-lo, executar o seguinte comando.  Este comando pode demorar alguns minutos para concluir.

```azurecli
az provider register -n Microsoft.AzureData --wait
```

## <a name="create-service-principal"></a>Criar um principal de serviço

O diretor de serviço é utilizado para carregar dados de utilização e métricas.

Siga estes comandos para criar o seu principal de serviço de upload de métricas:

> [!NOTE]
> A criação de um principal de serviço requer [certas permissões em Azure.](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)

Para criar um principal de serviço, atualize o seguinte exemplo. `<ServicePrincipalName>`Substitua-o pelo nome do seu chefe de serviço e execute o comando:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Se criou o principal de serviço mais cedo, e apenas precisa de obter as credenciais atuais, executar o seguinte comando para redefinir a credencial.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Por exemplo, para criar um principal de serviço chamado `azure-arc-metrics` , executar o seguinte comando

```
az ad sp create-for-rbac --name azure-arc-metrics
```

Exemplo de saída:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Guarde o `appId` `password` , e `tenant` valores em uma variável ambiente para uso posterior. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Depois de ter criado o principal de serviço, atribua o principal de serviço à função adequada. 

## <a name="assign-roles-to-the-service-principal"></a>Atribuir funções ao diretor de serviço

Executar este comando para atribuir o principal de serviço à `Monitoring Metrics Publisher` função na subscrição onde estão localizados os recursos da sua caixa de dados:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Tem de utilizar cotações duplas para nomes de papéis quando correr a partir de um ambiente Windows.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

Exemplo de saída:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Com o principal serviço atribuído à função adequada, pode proceder ao upload de métricas ou dados do utilizador. 

## <a name="upload-logs-metrics-or-user-data"></a>Carregar registos, métricas ou dados do utilizador

Os passos específicos para o upload de registos, métricas ou dados do utilizador variam consoante o tipo de informação que está a enviar. 

[Faça upload de registos para O Azure Monitor](upload-logs.md)

[Carregar métricas para o Monitor Azure](upload-metrics.md)

[Faça upload de dados de utilização para o Azure Monitor](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Orientações gerais sobre exportação e upload de utilização, métricas

Criar, ler, atualizar e eliminar (CRUD) operações em Azure Arc os serviços de dados habilitados são registados para efeitos de faturação e monitorização. Existem serviços de fundo que monitorizam estas operações crud e calculam o consumo adequadamente. O cálculo real da utilização ou consumo ocorre numa base programada e é feito em segundo plano. 

Durante a pré-visualização, este processo acontece todas as noites. A orientação geral é fazer o upload do uso apenas uma vez por dia. Quando as informações de utilização são exportadas e carregadas várias vezes no mesmo período de 24 horas, apenas o inventário de recursos é atualizado no portal Azure, mas não no uso do recurso.

Para carregar métricas, o monitor Azure só aceita os últimos 30 minutos de dados[(Saiba mais).](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting) A orientação para o upload das métricas é carregar as métricas imediatamente após a criação do ficheiro de exportação para que possa ver todo o conjunto de dados no portal Azure. Por exemplo, se exportasse as métricas às 14:00 e executasse o comando de upload às 14:50. Uma vez que o Azure Monitor só aceita dados durante os últimos 30 minutos, poderá não ver quaisquer dados no portal. 

## <a name="next-steps"></a>Passos seguintes

[Conheça os diretores de serviços](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Faça upload de dados de faturação para a Azure e veja-os no portal Azure](view-billing-data-in-azure.md)

[Ver recurso do controlador de dados Azure Arc no portal Azure](view-data-controller-in-azure-portal.md)