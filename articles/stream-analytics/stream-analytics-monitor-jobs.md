---
title: Monitorizar e gerir tarefas do Azure Stream Analytics através de programação
description: Este artigo descreve como monitorizar através de programação de tarefas do Stream Analytics criadas através de REST APIs, o SDK do Azure ou o PowerShell.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: be86287f8341b6b86064e51f8a26a8c7f97e867e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100807"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Criar programaticamente um monitor de tarefa do Stream Analytics

Este artigo demonstra como ativar a monitorização de uma tarefa do Stream Analytics. As tarefas do Stream Analytics que são criadas através de REST APIs, o SDK do Azure ou o PowerShell não têm monitorização ativada por predefinição. Pode ativá-lo manualmente no portal do Azure ao aceder à página de Monitor da tarefa e clicar no botão Ativar ou pode automatizar esse processo ao seguir os passos neste artigo. Os dados de monitorização serão apresentados na área de métricas do portal do Azure para a sua tarefa do Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este processo, tem de ter o seguinte:

* Visual Studio 2017 ou 2015
* [Azure SDK de .NET](https://azure.microsoft.com/downloads/) baixado e instalado
* Uma tarefa de Stream Analytics existente, que tem de ter monitorização ativada

## <a name="create-a-project"></a>Criar um projeto

1. Crie uma aplicação de consola do Visual Studio c# .NET.
2. Na consola do Gestor de pacotes, execute os seguintes comandos para instalar os pacotes de NuGet. A primeira é o SDK de .NET de gestão do Azure Stream Analytics. A segunda é o SDK de Monitor do Azure que será utilizado para ativar a monitorização. O último é o cliente do Azure Active Directory que será utilizado para autenticação.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Adicione a seguinte secção appSettings ao ficheiro App. config.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Substituir valores para *SubscriptionId* e *ActiveDirectoryTenantId* com os seus IDs de subscrição e de inquilino do Azure. Pode obter estes valores, executando o seguinte cmdlet do PowerShell:
   
   ```powershell
   Get-AzureAccount
   ```
4. Adicione as seguintes instruções "using" para o ficheiro de origem (Program.cs) no projeto.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Adicione um método de programa auxiliar de autenticação.

```csharp   
     public static string GetAuthorizationHeader()
   
         {
             AuthenticationResult result = null;
             var thread = new Thread(() =>
             {
                 try
                 {
                     var context = new AuthenticationContext(
                         ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                         ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
   
                     result = context.AcquireToken(
                         resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                         clientId: ConfigurationManager.AppSettings["AsaClientId"],
                         redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                         promptBehavior: PromptBehavior.Always);
                 }
                 catch (Exception threadEx)
                 {
                     Console.WriteLine(threadEx.Message);
                 }
             });
   
             thread.SetApartmentState(ApartmentState.STA);
             thread.Name = "AcquireTokenThread";
             thread.Start();
             thread.Join();
   
             if (result != null)
             {
                 return result.AccessToken;
             }
   
             throw new InvalidOperationException("Failed to acquire token");
     }
```

## <a name="create-management-clients"></a>Criar clientes de gestão

O código a seguir configurar os clientes de gestão e as variáveis necessárias.

```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Ative a monitorização para uma tarefa do Stream Analytics existente

O código a seguir ativa a monitorização para um **existente** tarefa do Stream Analytics. A primeira parte do código realiza um pedido GET para o serviço do Stream Analytics para obter informações sobre a tarefa de Stream Analytics específica. Ele usa o *Id* propriedade (obtida a partir do pedido de obtenção) como um parâmetro para o método Put na segunda metade do código, que envia um PUT do pedido para o serviço de informações para ativar a monitorização para a tarefa do Stream Analytics.

> [!WARNING]
> Se ativou anteriormente para uma tarefa de Stream Analytics diferente, através do portal do Azure ou através de programação através de monitorização a abaixo o código, **, recomendamos que forneça o mesmo nome de conta de armazenamento que utilizou quando anteriormente Ativar a monitorização.**
> 
> A conta de armazenamento está ligada à região que criou a tarefa de Stream Analytics, não especificamente para o trabalho propriamente dito.
> 
> Análise de Stream todas as tarefas (e todos os outros recursos do Azure) nessa mesma região partilharem esta conta de armazenamento para armazenar dados de monitorização. Se fornecer uma conta de armazenamento diferentes, ele poderá causar efeitos colaterais indesejados na monitorização de suas outras tarefas do Stream Analytics ou outros recursos do Azure.
> 
> O nome da conta de armazenamento que utilizar para substituir `<YOUR STORAGE ACCOUNT NAME>` no código a seguir deve ser uma conta de armazenamento que está na mesma subscrição que a tarefa de Stream Analytics que pretende ativar a monitorização para.
> 
> 
> ```csharp
>     // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
```


## Get support

For further assistance, try our [Azure Stream Analytics forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## Next steps

* [Introduction to Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)

