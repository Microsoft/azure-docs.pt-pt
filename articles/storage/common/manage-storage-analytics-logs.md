---
title: Ativar e gerir os registos Azure Storage Analytics (clássico) | Microsoft Docs
description: Saiba como monitorizar uma conta de armazenamento em Azure utilizando o Azure Storage Analytics.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: bc6632b55ba8fd90317a8b5046a3e84d863bf0ef
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221719"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Ativar e gerir registos Azure Storage Analytics (clássico)

[A Azure Storage Analytics](storage-analytics.md) fornece registos para bolhas, filas e mesas. Pode utilizar o [portal Azure](https://portal.azure.com) para configurar registos para a sua conta. Este artigo mostra-lhe como ativar e gerir registos. Para aprender a ativar métricas, consulte [Ativar e gerir as métricas Azure Storage Analytics (clássica)](storage-monitor-storage-account.md).  Existem custos associados à análise e armazenamento de dados de monitorização no portal Azure. Para obter mais informações, veja [Análise de Armazenamento](storage-analytics.md).

> [!NOTE]
> Recomendamos que utilize registos de armazenamento Azure no Monitor Azure em vez de registos de Storage Analytics. Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para bolhas (que inclui Azure Data Lake Storage Gen2), ficheiros, filas e tabelas. Para saber mais, consulte qualquer um dos seguintes artigos:
>
> - [Monitorização do armazenamento do blob Azure](../blobs/monitor-blob-storage.md)
> - [Monitorar ficheiros Azure](../files/storage-files-monitoring.md)
> - [Monitorização do armazenamento da fila Azure](../queues/monitor-queue-storage.md)
> - [Monitorização do armazenamento da mesa Azure](../tables/monitor-table-storage.md)

Para obter um guia aprofundado sobre a utilização de Storage Analytics e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o Armazenamento Azure, consulte [Monitor, diagnóstico e resolução de problemas do Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Ativar registos

Pode instruir o Azure Storage para guardar registos de diagnósticos para ler, escrever e apagar pedidos para os serviços de bolha, mesa e fila. A política de retenção de dados que definiu também se aplica a estes registos.

> [!NOTE]
> A Azure Files suporta atualmente métricas de Storage Analytics, mas não suporta o registo de Storage Analytics.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure](https://portal.azure.com), selecione **Contas de Armazenamento**, em seguida, o nome da conta de armazenamento para abrir a lâmina da conta de armazenamento.

2. Selecione **as definições de diagnóstico (clássica)** na secção **de Monitorização (clássica)** da lâmina do menu.

    ![Artigo de menu de diagnóstico em MONITORIZAÇÃO no portal Azure.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Certifique-se de que o **Estado** está definido para **On**, e selecione os **serviços** para os quais pretende ativar a sessão.

   > [!div class="mx-imgBorder"]
   > ![Configurar o registo no portal Azure.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Certifique-se de que a caixa de verificação **de dados Delete** está selecionada.  Em seguida, desafine o número de dias que pretende que os dados de registo sejam retidos movendo o controlo do slider por baixo da caixa de verificação, ou modificando diretamente o valor que aparece na caixa de texto ao lado do controlo do deslizador. O incumprimento das novas contas de armazenamento é de sete dias. Se não quiser definir uma política de retenção, insira zero. Se não houver uma política de retenção, cabe-lhe a si eliminar os dados de registo.

   > [!WARNING]
   > Os registos são armazenados como dados na sua conta. os dados de registo podem acumular-se na sua conta ao longo do tempo, o que pode aumentar o custo de armazenamento. Se necessitar de registar dados por apenas um pequeno período de tempo, pode reduzir os seus custos modificando a política de retenção de dados. Os dados de registo stale (dados mais antigos do que a sua política de retenção) são eliminados pelo sistema. Recomendamos a definição de uma política de retenção com base no tempo que pretende reter os dados de registo da sua conta. Consulte [a Faturação nas métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.

4. Clique em **Guardar**.

   Os registos de diagnóstico são guardados num recipiente de bolhas denominado *$logs* na sua conta de armazenamento. Pode visualizar os dados de registo utilizando um explorador de armazenamento como o [Microsoft Azure Storage Explorer,](https://storageexplorer.com)ou utilizar programáticamente a biblioteca do cliente de armazenamento ou o PowerShell.

   Para obter informações sobre o acesso ao recipiente $logs, consulte [o registo de análises de armazenamento](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra uma janela de comando Windows PowerShell.

2. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

3. Se a sua identidade estiver associada a mais de uma subscrição, então desa estale a sua subscrição ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que pretende utilizar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

   * Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento. 

6. Utilize o **Set-AzStorageServiceLoggingProperty** para alterar as definições de registo atual. Os cmdlets que controlam o registo de armazenamento utilizam um parâmetro **de Exploração de Exploração madeireira** que é uma cadeia que contém uma lista separada de vírgulas de tipos de pedido para registar. Os três tipos de pedidos possíveis são **lidos,** **escritos** e **apagados.** Para desligar o registo, utilize o valor **nenhum** para o parâmetro **'Operações de Registo'.**  

   O seguinte comando muda a sessão de registo para ler, escrever e apagar pedidos no serviço Queue na sua conta de armazenamento predefinida com retenção definida para cinco dias:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > Os registos são armazenados como dados na sua conta. os dados de registo podem acumular-se na sua conta ao longo do tempo, o que pode aumentar o custo de armazenamento. Se necessitar de registar dados por apenas um pequeno período de tempo, pode reduzir os seus custos modificando a política de retenção de dados. Os dados de registo stale (dados mais antigos do que a sua política de retenção) são eliminados pelo sistema. Recomendamos a definição de uma política de retenção com base no tempo que pretende reter os dados de registo da sua conta. Consulte [a Faturação nas métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.
   
   O seguinte comando desliga a sessão de registo para o serviço de tabela na sua conta de armazenamento predefinido:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Para obter informações sobre como configurar os cmdlets Azure PowerShell para trabalhar com a sua subscrição Azure e como selecionar a conta de armazenamento predefinida para utilizar, consulte: [Como instalar e configurar a Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Modificar o período de retenção de dados de registo

Os dados de registo podem acumular-se na sua conta ao longo do tempo, o que pode aumentar o custo de armazenamento. Se necessitar de dados de registo por apenas um pequeno período de tempo, pode reduzir os seus custos modificando o período de retenção de dados de registo. Por exemplo, se necessitar de registos por apenas três dias, desa um prazo de retenção de dados de registo para um valor de `3` . Desta forma, os registos serão automaticamente eliminados da sua conta após 3 dias. Esta secção mostra-lhe como visualizar o período atual de retenção de dados de registo e, em seguida, atualizar esse período se é isso que pretende fazer.

> [!NOTE]
> Estes passos aplicam-se apenas a contas que não têm a definição **hierárquica do espaço** de identificação ativada. Se ativou essa definição na sua conta, então a definição para dias de retenção ainda não está suportada. Em vez disso, terá de eliminar os registos manualmente utilizando qualquer ferramenta suportada, como o Azure Storage Explorer, REST ou um SDK. Para encontrar esses registos na sua conta de armazenamento, consulte [como os registos são armazenados](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure](https://portal.azure.com), selecione **Contas de Armazenamento**, em seguida, o nome da conta de armazenamento para abrir a lâmina da conta de armazenamento.
2. Selecione **as definições de diagnóstico (clássica)** na secção **de Monitorização (clássica)** da lâmina do menu.

    ![Artigo do menu de diagnósticos no âmbito do MONITORING no portal Azure](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Certifique-se de que a caixa de verificação **de dados Delete** está selecionada.  Em seguida, desafine o número de dias que pretende que os dados de registo sejam retidos movendo o controlo do slider por baixo da caixa de verificação, ou modificando diretamente o valor que aparece na caixa de texto ao lado do controlo do deslizador.

   > [!div class="mx-imgBorder"]
   > ![Modifique o período de retenção no portal Azure](./media/manage-storage-analytics-logs/modify-retention-period.png)

   O número predefinido de dias para novas contas de armazenamento é de sete dias. Se não quiser definir uma política de retenção, insira zero. Se não houver uma política de retenção, cabe-lhe a si eliminar os dados de monitorização.
   
4. Clique em **Guardar**.

   Os registos de diagnóstico são guardados num recipiente de bolhas denominado *$logs* na sua conta de armazenamento. Pode visualizar os dados de registo utilizando um explorador de armazenamento como o [Microsoft Azure Storage Explorer,](https://storageexplorer.com)ou utilizar programáticamente a biblioteca do cliente de armazenamento ou o PowerShell.

   Para obter informações sobre o acesso ao recipiente $logs, consulte [o registo de análises de armazenamento](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra uma janela de comando Windows PowerShell.

2. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

3. Se a sua identidade estiver associada a mais de uma subscrição, então desa estale a sua subscrição ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

   * Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento. 

6. Utilize o [Get-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageserviceloggingproperty) para visualizar a política atual de retenção de registos. O exemplo a seguir imprime à consola o período de retenção para serviços de armazenamento de bolhas e filas.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   Na saída da consola, o período de retenção aparece por baixo do título da `RetentionDays` coluna.

   > [!div class="mx-imgBorder"]
   > ![Política de retenção na produção powerShell](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Utilize o [Set-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageserviceloggingproperty) para alterar o período de retenção. O exemplo a seguir altera o período de retenção para 4 dias.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Para obter informações sobre como configurar os cmdlets Azure PowerShell para trabalhar com a sua subscrição Azure e como selecionar a conta de armazenamento predefinida para utilizar, consulte: [Como instalar e configurar a Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

O exemplo a seguir imprime à consola o período de retenção para serviços de armazenamento de bolhas e filas.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

O exemplo a seguir altera o período de retenção para 4 dias. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

O exemplo a seguir imprime à consola o período de retenção para serviços de armazenamento de bolhas e filas.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

O exemplo a seguir altera o período de retenção dos registos para os serviços de armazenamento de bolhas e filas para 4 dias. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Verifique se os dados de registo estão a ser eliminados

Pode verificar se os registos estão a ser apagados visualizando o conteúdo do `$logs` recipiente da sua conta de armazenamento. A imagem a seguir mostra o conteúdo de uma pasta no `$logs` recipiente. A pasta corresponde a janeiro de 2021 e cada pasta contém registos para um dia. Se o dia de hoje era 29 de janeiro de 2021, e a sua política de retenção está definida para apenas um dia, então esta pasta deve conter registos por apenas um dia.

> [!div class="mx-imgBorder"]
> ![Lista de pastas de registo no Portal Azure](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Ver dados de registo

 Para visualizar e analisar os seus dados de registo, deverá descarregar as bolhas que contêm os dados de registo que lhe interessam para uma máquina local. Muitas ferramentas de navegação de armazenamento permitem-lhe descarregar bolhas da sua conta de armazenamento; também pode utilizar a equipa de Armazenamento Azure, fornecida pela linha de comando Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) para descarregar os seus dados de registo.  
 
>[!NOTE]
> O `$logs` contentor não está integrado com a Grade de Eventos, pelo que não receberá notificações quando os ficheiros de registo estiverem escritos. 

 Para se certificar de que descarrega os dados de registo em que está interessado e para evitar descarregar os mesmos dados de registo mais de uma vez:  

-   Utilize a convenção de nomeação de data e hora para as bolhas que contenham dados de registo para rastrear quais as bolhas que já descarregou para análise para evitar o refiamento dos mesmos dados mais de uma vez.  

-   Utilize os metadados nas bolhas que contêm dados de registo para identificar o período específico para o qual a bolha detém dados de registo para identificar a bolha exata que precisa de descarregar.  

Para começar com a AzCopy, consulte [Começar com a AzCopy](storage-use-azcopy-v10.md) 

O exemplo a seguir mostra como pode descarregar os dados de registo do serviço de fila para as horas a partir das 09:00, 10:00 e 11:00 no dia 20 de maio de 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Para saber mais sobre como descarregar ficheiros [específicos, consulte descarregar blobs do armazenamento Azure Blob utilizando a AzCopy v10](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Quando tiver descarregado os seus dados de registo, pode visualizar as entradas de registo nos ficheiros. Estes ficheiros de registo utilizam um formato de texto delimitado que muitas ferramentas de leitura de registos são capazes de analisar (para mais informações, consulte o guia [Monitor, Diagnóstico e Resolução de Problemas do Microsoft Azure Storage).](storage-monitoring-diagnosing-troubleshooting.md) Diferentes ferramentas têm diferentes instalações para formatação, filtragem, triagem, anúncio de pesquisa do conteúdo dos seus ficheiros de registo. Para obter mais informações sobre o formato e conteúdo do ficheiro de registo de armazenamento, consulte o formato de registo de [armazenamento Analytics](/rest/api/storageservices/storage-analytics-log-format) e o armazenamento de [analítica operações registadas e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre Storage Analytics, consulte [Storage Analytics](storage-analytics.md) para Storage Analytics.
* [Configure métricas de Storage Analytics](storage-monitor-storage-account.md).
* Para obter mais informações sobre a utilização de um idioma .NET para configurar o registo de armazenamento, consulte [o Índice da Biblioteca do Cliente de Armazenamento.](/previous-versions/azure/dn261237(v=azure.100)) 
* Para obter informações gerais sobre a configuração do registo de armazenamento utilizando a API REST, consulte [Ativar e Configurar o Analytics de Armazenamento](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* Saiba mais sobre o formato dos registos Storage Analytics. Consulte [o formato de registo de analítica de armazenamento](/rest/api/storageservices/storage-analytics-log-format).