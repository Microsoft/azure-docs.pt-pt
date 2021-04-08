---
title: Monitorize e gere os empregos do Azure Stream Analytics programáticamente
description: Este artigo descreve como monitorizar programáticamente os trabalhos do Stream Analytics criados através de REST APIs, Azure SDK ou PowerShell.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: dda1b6b652870440e7228e6640bb555f4c2e4a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015081"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Criar programáticamente um monitor de trabalho stream Analytics

Este artigo demonstra como permitir a monitorização para um trabalho stream Analytics. Os trabalhos de Stream Analytics que são criados através de REST APIs, Azure SDK ou PowerShell não têm monitorização ativada por padrão. Pode capacitá-lo manualmente no portal Azure indo para a página Monitor do trabalho e clicando no botão Ativar ou pode automatizar este processo seguindo os passos deste artigo. Os dados de monitorização aparecerão na área métrica do portal Azure para o seu trabalho stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este processo, deve ter os seguintes pré-requisitos:

* Estúdio Visual 2019 ou 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) descarregado e instalado
* Um trabalho de Stream Analytics existente que precisa de ter monitorização ativada

## <a name="create-a-project"></a>Criar um projeto

1. Criar uma aplicação de consola Visual Studio C# .NET.
2. Na Consola Gestor de Pacotes, execute os seguintes comandos para instalar os pacotes NuGet. O primeiro é o Azure Stream Analytics Management .NET SDK. O segundo é o Azure Monitor SDK que será utilizado para permitir a monitorização. O último é o cliente Azure Ative Directory que será usado para autenticação.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Adicione a seguinte secção de desaparas na secção App.config.
   
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
   Substitua os valores *de SubscriptionId* e *ActiveDirectoryTenantId* pela subscrição Azure e IDs de inquilino. Pode obter estes valores executando o seguinte cmdlet PowerShell:
   
   ```powershell
   Get-AzureAccount
   ```
4. Adicione o seguinte ao utilizar instruções para o ficheiro de origem (Program.cs) no projeto.
   
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
5. Adicione um método de ajuda à autenticação.

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

O seguinte código irá configurar as variáveis e clientes de gestão necessários.

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

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Permitir a monitorização de um trabalho de Stream Analytics existente

O código seguinte permite a monitorização de um trabalho stream analytics **existente.** A primeira parte do código executa um pedido GET contra o serviço Stream Analytics para obter informações sobre o trabalho específico do Stream Analytics. Utiliza a propriedade *ID* (recuperada do pedido GET) como parâmetro para o método Put na segunda metade do código, que envia um pedido PUT ao serviço Insights para permitir a monitorização para o trabalho stream Analytics.

> [!WARNING]
> Se já ativou a monitorização para um trabalho diferente do Stream Analytics, seja através do portal Azure ou programáticamente através do código abaixo, **recomendamos que forneça o mesmo nome de conta de armazenamento que utilizou quando previamente ativou a monitorização.**
> 
> A conta de armazenamento está ligada à região em que criou o seu trabalho stream Analytics, não especificamente ao trabalho em si.
> 
> Todos os trabalhos da Stream Analytics (e todos os outros recursos Azure) nessa mesma região partilham esta conta de armazenamento para armazenar dados de monitorização. Se fornecer uma conta de armazenamento diferente, pode causar efeitos colaterais não intencionais na monitorização dos seus outros trabalhos stream Analytics ou outros recursos Azure.
> 
> O nome da conta de armazenamento que usa para substituir `<YOUR STORAGE ACCOUNT NAME>` no seguinte código deve ser uma conta de armazenamento que esteja na mesma subscrição que o trabalho stream Analytics para o qual está a permitir a monitorização.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Obter suporte

Para obter mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para a Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)