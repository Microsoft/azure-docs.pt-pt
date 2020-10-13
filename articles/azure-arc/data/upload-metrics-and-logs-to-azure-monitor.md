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
ms.openlocfilehash: d508e2e24ac35171d87cdba9cb79981dfb8764e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757474"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Faça upload de dados de utilização, métricas e registos para o Azure Monitor

Periodicamente, pode exportar informações de utilização para efeitos de faturação, métricas de monitorização e registos e, em seguida, enviá-la para Azure.  A exportação e upload de qualquer um destes três tipos de dados também criará e atualizará o controlador de dados, a instância gerida pelo SQL e os recursos do grupo de servidores de hiperescala PostgreSQL em Azure.

> [!NOTE] 
Durante o período de pré-visualização, não há qualquer custo para a utilização de serviços de dados ativados pelo Azure Arc.

## <a name="prerequisites"></a>Pré-requisitos

Necessitará do Azure CLI (az) e do Azure Data CLI (azdata) instalados.  [Instale ferramentas](./install-client-tools.md).

Antes de enviar dados para o Azure, tem de garantir que a sua subscrição do Azure tem o fornecedor de recursos Microsoft.AzureData registado.

Pode verificar isto executando o seguinte comando:

```console
az provider show -n Microsoft.AzureData -o table
```

Se o fornecedor de recursos não estiver atualmente registado na sua subscrição, pode registá-lo executando o seguinte comando.  Este comando leva um minuto ou dois para ser concluído.

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>Carregar dados de utilização

As informações de utilização, tais como o inventário e a utilização de recursos, podem ser enviadas para a Azure da seguinte forma:

1. Exportar os dados de utilização utilizando ```azdata export``` o comando, da seguinte forma:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   Este comando cria um `usage.json` ficheiro com todos os recursos de dados ativados pelo Arco Azure, tais como instâncias geridas pelo SQL e instâncias de hiperescala postgresQL, etc. que são criadas no controlador de dados.

2. Faça o upload dos dados de utilização usando ```azdata upload``` o comando

   > [!NOTE]
   > Por favor, aguarde pelo menos 24 horas após a criação do controlador de dados Azure Arc antes de executar o upload

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>Carregar métricas e registos

Com os serviços de dados do Azure Arc, pode enviar opcionalmente as suas métricas e registos para o Azure Monitor para que possa agregar e analisar métricas, registos, levantar alertas, enviar notificações ou desencadear ações automatizadas. 

O envio dos seus dados para o Azure Monitor também permite armazenar dados de monitorização e registos fora do site e em grande escala, permitindo o armazenamento a longo prazo dos dados para análises avançadas.

Se tiver vários sites que tenham serviços de dados do Azure Arc, pode utilizar o Azure Monitor como localização central para recolher todos os seus registos e métricas em todos os seus sites.

### <a name="before-you-begin"></a>Antes de começar

Existem algumas etapas de configuração únicas necessárias para ativar os cenários de upload de registos e métricas:

1. Crie uma aplicação de Diretório Ativo De Serviço/Azure, incluindo a criação de um segredo de acesso ao cliente e atribua o principal do serviço à função de "Editor de Métricas de Monitorização" na(s) subscrição(s) onde estão localizados os recursos da sua caixa de dados.
2. Crie um espaço de trabalho de análise de log analytics e obtenha as chaves e desaprote a informação em variáveis ambientais.

O primeiro item é necessário para carregar métricas e o segundo é necessário para carregar registos.

Siga estes comandos para criar o seu principal de serviço de upload de métricas e atribua-o às funções de 'Monitoring Metrics Publisher' e 'Colaborador' para que o diretor de serviço possa carregar métricas e realizar operações de criação e upload.

## <a name="create-service-principal-and-assign-roles"></a>Criar funções principais de serviço e atribuir funções

Siga estes comandos para criar o seu principal de serviço de upload de métricas e atribua-o à função "Monitoring Metrics Publisher":

Para criar um principal de serviço, executar este comando:

> [!NOTE]
> A criação de um principal de serviço requer [certas permissões em Azure.](/azure/active-directory/develop/howto-create-service-principal-portal#permissions-required-for-registering-an-app)

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Exemplo de saída:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Guarde os valores de appId e inquilino numa variável ambiental para utilização posterior. 

Para guardar os valores de appId e inquilino com PowerShell, siga este exemplo:

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

Em alternativa, em Linux ou macOS, pode guardar os valores appId e inquilinos com este exemplo:

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

Executar este comando para atribuir o principal de serviço à função "Monitoring Metrics Publisher" na subscrição onde estão localizados os recursos da sua caixa de dados:


> [!NOTE]
> Tem de utilizar cotações duplas para nomes de papéis quando correr a partir de um ambiente Windows.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Exemplo de saída:

```console
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

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho de análise de log analytics

Em seguida, execute estes comandos para criar um Log Analytics Workspace e definir a informação de acesso em variáveis ambientais.

> [!NOTE]
> Ignore este passo se já tiver um espaço de trabalho.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Exemplo de saída:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

Para carregar métricas para o seu arco Azure habilitado a sql gerido instâncias e Azure Arc ativado grupos de servidores de hiperescala postgreSQL funcionam, os seguintes comandos CLI:

1. Exportar todas as métricas para o ficheiro especificado:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Carregar métricas para o monitor Azure:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Aguarde pelo menos 30 minutos após a criação de instâncias de dados ativadas pelo Arco Azure para o primeiro upload
   >
   >Certifique-se de que `upload` as métricas imediatamente a `export` seguir, uma vez que o Azure Monitor só aceita métricas durante os últimos 30 minutos. [Saiba mais](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Se vir algum erro que indique "Falha na obter métricas" durante a exportação, verifique se a recolha de dados está definida ```true``` para executar o seguinte comando:

```console
azdata arc dc config show
```

e olhar em "secção de segurança"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Verifique se as `allowNodeMetricsCollection` propriedades e `allowPodMetricsCollection` propriedades estão definidas para `true` .

## <a name="view-the-metrics-in-the-portal"></a>Ver as métricas no Portal

Uma vez que as suas métricas são carregadas, você pode vê-las a partir do portal Azure.
> [!NOTE]
> Por favor, note que pode levar alguns minutos para que os dados carregados sejam processados antes de poder ver as métricas no portal.


Para ver as suas métricas no portal, utilize este link para abrir o portal: Em seguida, procure a <https://portal.azure.com> sua caixa de dados por exemplo na barra de pesquisa:

Pode ver a utilização do CPU na página 'Vista Geral' ou se quiser métricas mais detalhadas, pode clicar em métricas a partir do painel de navegação à esquerda

Escolha o servidor sql como o espaço de nome métrico:

Selecione a métrica que pretende visualizar (também pode selecionar vários):

Altere a frequência para durar 30 minutos:

> [!NOTE]
> Só é possível carregar métricas nos últimos 30 minutos. O Azure Monitor rejeita métricas com mais de 30 minutos.

## <a name="upload-logs-to-azure-monitor"></a>Carregar registos para o Azure Monitor

 Para carregar registos para o seu Arco Azure ativou instâncias geridas pelo SQL e os grupos de servidores de hiperescala pós-SQL ativados executam os seguintes comandos CLI-

1. Exporte todos os registos para o ficheiro especificado:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Faça upload de registos para um espaço de trabalho de analítica de registo de monitores Azure:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Veja os seus registos no portal Azure

Após os registos serem carregados, deverá ser capaz de consultar os mesmos com o explorador de consultas de registo da seguinte forma:

1. Abra o portal Azure e, em seguida, procure o seu espaço de trabalho pelo nome na barra de pesquisa no topo e, em seguida, selecione-o
2. Clique em Registos no painel esquerdo
3. Clique em 'Iniciar') (ou clique nos links da página 'Iniciar-se' para saber mais sobre o 'Registar Analytics' se é novo na página)
4. Siga o tutorial para saber mais sobre o Log Analytics se esta é a sua primeira vez usando o Log Analytics
5. Expanda os Registos Personalizados na parte inferior da lista de tabelas e verá uma tabela com o nome "sql_instance_logs_CL".
6. Clique no ícone de "olho" ao lado do nome da tabela
7. Clique no botão "Ver no editor de consultas"
8. Você terá agora uma consulta no editor de consulta que mostrará os mais recentes 10 eventos no log
9. A partir daqui, pode experimentar a consulta de registos, com o editor de consultas, definir alertas, etc.

## <a name="automating-uploads-optional"></a>Automatização de uploads (opcional)

Se quiser carregar métricas e registos numa base programada, pode criar um script e executá-lo num temporizador a cada poucos minutos. Abaixo está um exemplo de automatização dos uploads usando um script linux shell.

No seu editor de texto/código favorito, adicione o seguinte script ao ficheiro e guarde como um ficheiro executável de scripts como .sh (Linux/Mac) ou .cmd, .bat, .ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Tornar o ficheiro de script executável

```console
chmod +x myuploadscript.sh
```

Execute o guião a cada 20 minutos:

```console
watch -n 1200 ./myuploadscript.sh
```

Você também pode usar um programador de trabalho como cron ou Windows Task Scheduler ou um orquestrador como Ansible, Puppet ou Chef.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Orientações gerais sobre exportação e upload de utilização, métricas

Criar, ler, atualizar e eliminar (CRUD) operações em Azure Arc os serviços de dados habilitados são registados para efeitos de faturação e monitorização. Existem serviços de fundo que monitorizam estas operações crud e calculam o consumo adequadamente. O cálculo real da utilização ou consumo ocorre numa base programada e é feito em segundo plano. 

Durante a pré-visualização, este processo acontece todas as noites. A orientação geral é fazer o upload do uso apenas uma vez por dia. Quando as informações de utilização são exportadas e carregadas várias vezes no mesmo período de 24 horas, apenas o inventário de recursos é atualizado no portal Azure, mas não no uso do recurso.

Para carregar métricas, o monitor Azure só aceita os últimos 30 minutos de dados[(Saiba mais).](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting) A orientação para o upload das métricas é carregar as métricas imediatamente após a criação do ficheiro de exportação para que possa ver todo o conjunto de dados no portal Azure. Por exemplo, se exportasse as métricas às 14:00 e executasse o comando de upload às 14:50. Uma vez que o Azure Monitor só aceita dados durante os últimos 30 minutos, poderá não ver quaisquer dados no portal. 

## <a name="next-steps"></a>Passos seguintes

[Faça upload de dados de faturação para a Azure e veja-os no portal Azure](view-billing-data-in-azure.md)

[Ver recurso do controlador de dados Azure Arc no portal Azure](view-data-controller-in-azure-portal.md)
