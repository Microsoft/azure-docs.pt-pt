---
title: Faça upload do inventário de recursos, dados de utilização, métricas e registos para o Azure Monitor
description: Faça upload do inventário de recursos, dados de utilização, métricas e registos para o Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939107"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Faça upload do inventário de recursos, dados de utilização, métricas e registos para o Azure Monitor

Com os serviços de dados do Azure Arc pode enviar *opcionalmente* as suas métricas e registos para o Azure Monitor para que possa agregar e analisar métricas, registos, levantar alertas, enviar notificações ou desencadear ações automatizadas. O envio dos seus dados para o Azure Monitor também permite armazenar dados de monitorização e registos fora do site e em grande escala permitindo o armazenamento a longo prazo dos dados para análises avançadas.  Se tiver vários sites que possuam serviços de dados do Azure Arc, pode utilizar o Azure Monitor como localização central para recolher todos os seus registos e métricas em todos os seus sites.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Before you begin

Existem algumas etapas de configuração únicas necessárias para ativar os cenários de upload de registos e métricas:

1) Crie uma aplicação principal de serviço/Azure Ative Directory, incluindo a criação de um segredo de acesso ao cliente e atribua o principal do serviço à função de "Editor de Métricas de Monitorização" na(s) subscrição(s) onde estão localizados os recursos da sua caixa de dados.
2) Crie um espaço de trabalho de análise de log analytics e obtenha as chaves e desaprote a informação em variáveis ambientais.

O primeiro item é necessário para carregar métricas e o segundo é necessário para carregar registos.

Siga estes comandos para criar o seu principal de serviço de upload de métricas e atribua-o às funções de 'Monitoring Metrics Publisher' e 'Colaborador' para que o diretor de serviço possa carregar métricas e realizar operações de criação e upload.

## <a name="create-service-principal-and-assign-roles"></a>Criar funções principais de serviço e atribuir funções

Siga estes comandos para criar o seu principal de serviço de upload de métricas e atribua-o à função "Monitoring Metrics Publisher":

Para criar um principal de serviço, executar este comando:

> [!NOTE]
> A criação de um principal de serviço requer [certas permissões em Azure.](/active-directory/develop/howto-create-service-principal-portal#required-permissions)

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Exemplo de saída:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Guarde os valores de appId e inquilino numa variável ambiental para utilização posterior:

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Executar este comando para atribuir o principal de serviço à função "Monitoring Metrics Publisher" na subscrição onde estão localizados os recursos da sua caixa de dados:

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Exemplo de saída:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho de análise de log analytics

Em seguida, execute estes comandos para criar um Log Analytics Workspace e definir a informação de acesso em variáveis ambientais.

> [!NOTE]
> Ignore este passo se já tiver um espaço de trabalho.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Exemplo de saída:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Atribuir ID e chave partilhada para variáveis ambientais

Guarde o customerId (ID do espaço de trabalho) como uma variável ambiental a ser usada posteriormente:

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

Este comando imprimirá as teclas de acesso necessárias para ligar ao seu espaço de trabalho de análise de registo:

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

Exemplo de saída:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Guarde a chave primária numa variável ambiental a utilizar mais tarde:

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Definir variáveis de ambiente final e confirmar

Definir o URL de autoridade SPN numa variável ambiental:

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Verifique se todas as variáveis ambientais necessárias são definidas se quiser:

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Carregar métricas para o Monitor Azure

Para carregar as métricas para as suas instâncias geridas Azure SQL e Azure Database para grupos de servidores de hiperescala postgresQL executados, os seguintes comandos CLI:

Esta ação vai exportar todas as métricas para o ficheiro especificado:

```console
azdata arc dc export -t metrics --path metrics.json
```

Esta ação vai carregar as métricas para o Azure Monitor:

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>Ver as métricas no Portal

Após as métricas estarem carregadas, deverá ser capaz de as visualizar no portal do Azure.

Para ver as suas métricas no portal, utilize este link especial para abrir o portal: Em seguida, procure a <https://portal.azure.com> sua base de dados por exemplo na barra de pesquisa:

Pode ver a utilização do CPU na página 'Vista Geral' ou se quiser métricas mais detalhadas, pode clicar em métricas a partir do painel de navegação à esquerda

Escolha o servidor sql como o espaço de nome métrico:

Selecione a métrica que pretende visualizar (também pode selecionar vários):

Altere a frequência para durar 30 minutos:

> [!NOTE]
> Só é possível carregar métricas nos últimos 30 minutos. O Azure Monitor rejeita métricas com mais de 30 minutos.

## <a name="upload-logs-to-azure-monitor"></a>Carregar registos para o Azure Monitor

 Para carregar registos para as suas instâncias geridas Azure SQL e Azure Database para grupos de servidores de hiperescala PostgreSQL executam os seguintes comandos CLI-

Esta ação vai exportar todos os registos para o ficheiro especificado:

```console
azdata arc dc export -t logs --path logs.json
```

Isto irá enviar registos para um espaço de trabalho de analítica de registo de registos do monitor Azure:

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Veja os seus registos no portal Azure

Após os registos serem carregados, deverá ser capaz de consultar os mesmos com o explorador de consultas de registo da seguinte forma:

1. Abra o portal Azure e, em seguida, procure o seu espaço de trabalho pelo nome na barra de pesquisa no topo e, em seguida, selecione-o
2. Clique em Registos no painel esquerdo
3. Clique em 'Iniciar') (ou clique nos links da página 'Iniciar-se' para saber mais sobre o 'Registar Analytics' se é novo na página)
4. Siga o tutorial para saber mais sobre Log Analytics se é a sua primeira vez
5. Expanda os Registos Personalizados na parte inferior da lista de tabelas e verá uma tabela com o nome "sql_instance_logs_CL".
6. Clique no ícone de "olho" ao lado do nome da tabela
7. Clique no botão "Ver no editor de consultas"
8. Agora vai ter uma consulta no editor de consultas que vai apresentar os 10 eventos mais recentes no registo
9. A partir daqui, pode experimentar a consulta de registos, com o editor de consultas, definir alertas, etc.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Automatização de métricas e uploads de registos (opcional)

Se quiser carregar constantemente métricas e registos, pode criar um script e executá-lo num temporizador a cada poucos minutos.  Abaixo está um exemplo de automatização dos uploads usando um script linux shell.

No seu editor de texto/código favorito, adicione o seguinte ao conteúdo do script no ficheiro e guarde como um ficheiro executável de scripts como .sh (Linux/Mac) ou .cmd, .bat, .ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Tornar o ficheiro de script executável

```console
chmod +x myuploadscript.sh
```

Execute o guião a cada 2 minutos:

```console
watch -n 120 ./myuploadscript.sh
```

Você também pode usar um programador de trabalho como cron ou Windows Task Scheduler ou um orquestrador como Ansible, Puppet ou Chef.
