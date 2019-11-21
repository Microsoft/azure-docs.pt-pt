---
title: 'Troubleshoot package execution in the SSIS integration runtime '
description: This article provides troubleshooting guidance for SSIS package execution in the SSIS integration runtime
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 9692c754e59eba02d3d483d44430150107d703a4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217529"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Troubleshoot package execution in the SSIS integration runtime

This article includes the most common errors that you might find when you're executing SQL Server Integration Services (SSIS) packages in the SSIS integration runtime. It describes the potential causes and actions to solve the errors.

## <a name="where-to-find-logs-for-troubleshooting"></a>Where to find logs for troubleshooting

Use the Azure Data Factory portal to check the output of the SSIS package execution activity. The output includes the execution result, error messages, and operation ID. For details, see [Monitor the pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Use the SSIS catalog (SSISDB) to check the detail logs for the execution. For details, see [Monitor Running Packages and Other Operations](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Erros comuns, causas e soluções

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Error message: "Connection Timeout Expired" or "The service has encountered an error processing your request. Please try again."

Here are potential causes and recommended actions:
* The data source or destination is overloaded. Check the load on your data source or destination and see whether it has enough capacity. For example, if you used Azure SQL Database, consider scaling up if the database is likely to time out.
* The network between the SSIS integration runtime and the data source or destination is unstable, especially when the connection is cross-region or between on-premises and Azure. Apply the retry pattern in the SSIS package by following these steps:
  * Make sure your SSIS packages can rerun on failure without side effects (for example, data loss or data duplication).
  * Configure **Retry** and **Retry interval** of **Execute SSIS Package** activity on the **General** tab. ![Set properties on the General tab](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * For an ADO.NET and OLE DB source or destination component, set **ConnectRetryCount** and **ConnectRetryInterval** in Connection Manager in the SSIS package or SSIS activity.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Error message: "ADO NET Source has failed to acquire the connection '...'" with "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."

Normalmente, este problema significa que a origem ou o destino de dados está inacessível a partir do runtime de integração do SSIS. Os motivos podem variar. Tente estas ações:
* Make sure you're passing the data source or destination name/IP correctly.
* Make sure the firewall is set properly.
* Make sure your virtual network is configured properly if your data source or destination is on-premises:
  * You can verify whether the issue is from virtual network configuration by provisioning an Azure VM in the same virtual network. Then check whether the data source or destination can be accessed from the Azure VM.
  * You can find more details about using a virtual network with an SSIS integration runtime in [Join an Azure-SSIS integration runtime to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Error message: "ADO NET Source has failed to acquire the connection '...'" with "Could not create a managed connection manager."

The potential cause is that the ADO.NET provider used in the package isn't installed in the SSIS integration runtime. You can install the provider by using a custom setup. You can find more details about custom setup in [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Error message: "The connection '...' is not found"

Um problema conhecido em versões mais antigas do SQL Server Management Studio (SSMS) pode causar este erro. Se o pacote contiver um componente personalizado (por exemplo, Pacote de Funcionalidades do Azure para SSIS ou componentes de parceiros) que não esteja instalado no computador onde o SSMS é utilizado para fazer a implementação, o SSMS vai remover o componente e provocar o erro. Atualize o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para a versão mais recente na qual o problema foi corrigido.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Error message：“SSIS Executor exit code: -1073741819.”

* Causa potencial e ação recomendada:
  * This error may be because of the limitation for Excel source and destination when multiple Excel sources or destinations are executing in parallel in multi-thread. You can workaround this limitation by change your Excel components to execute in sequence, or separate them into different packages and trigger through "Execute Package Task" with ExecuteOutOfProcess property set as True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Error message: "There is not enough space on the disk"

This error means the local disk is used up in the SSIS integration runtime node. Check whether your package or custom setup is consuming a lot of disk space:
* If the disk is consumed by your package, it will be freed up after the package execution finishes.
* If the disk is consumed by your custom setup, you'll need to stop the SSIS integration runtime, modify your script, and start the integration runtime again. The whole Azure blob container that you specified for custom setup will be copied to the SSIS integration runtime node, so check whether there's any unnecessary content under that container.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Error message: "Failed to retrieve resource from master. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Description:Load file "***" failed."

* Causa potencial e ação recomendada:
  * If the SSIS Activity is executing package from file system (package file or project file), this error will occur if the project, package or configuration file is not accessible with the package access credential you provided in the SSIS Activity
    * If you are using Azure File:
      * The file path should start with \\\\\<storage account name\>.file.core.windows.net\\\<file share path\>
      * The domain should be "Azure"
      * The username should be \<storage account name\>
      * The password should be \<storage access key\>
    * If your are using on-premises file, please check if VNet, package access credential and permission are configured properly so that your Azure-SSIS integration runtime can access your on-premises file share

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Error message: "The file name '...' specified in the connection was not valid"

* Causa potencial e ação recomendada:
  * An invalid file name is specified
  * Make sure you are using FQDN (Fully Qualified Domain Name) instead of short time in your connection manager

### <a name="error-message-cannot-open-file-"></a>Error message: "Cannot open file '...'"

This error occurs when package execution can't find a file in the local disk in the SSIS integration runtime. Tente estas ações:
* Don't use the absolute path in the package that's being executed in the SSIS integration runtime. Use the current execution working directory (.) or the temp folder (%TEMP%) instead.
* If you need to persist some files on SSIS integration runtime nodes, prepare the files as described in [Customize setup](how-to-configure-azure-ssis-ir-custom-setup.md). All the files in the working directory will be cleaned up after the execution is finished.
* Use Azure Files instead of storing the file in the SSIS integration runtime node. For details, see [Use Azure file shares](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Error message: "The database 'SSISDB' has reached its size quota"

Uma causa potencial pode ser quando a base de dados SSISDB criada na base de dados SQL do Azure, ou uma instância gerida quando está a criar um runtime de integração do SSIS, atingiu a sua quota. Tente estas ações:
* Considere aumentar a DTU da base de dados. Pode encontrar detalhes em [SQL Database resource limits for an Azure SQL Database server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) (Limites de recursos da Base de Dados SQL para um servidor da Base de Dados SQL do Azure).
* Verifique se o pacote iria gerar muitos registos. Se for o caso, pode configurar uma tarefa elástica para limpar esses registos. Para obter detalhes, veja [Clean up SSISDB logs with Azure Elastic Database jobs](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) (Limpar registos SSISDB com tarefas de Base de Dados Elástica do Azure)

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Error message: "The request limit for the database is ... and has been reached."

If many packages are running in parallel in the SSIS integration runtime, this error might occur because SSISDB has hit its request limit. Consider increasing the DTC of SSISDB to resolve this issue. Pode encontrar detalhes em [SQL Database resource limits for an Azure SQL Database server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) (Limites de recursos da Base de Dados SQL para um servidor da Base de Dados SQL do Azure).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Error message: "SSIS Operation failed with unexpected operation status: ..."

The error is mostly caused by a transient problem, so try to rerun the package execution. Apply the retry pattern in the SSIS package by following these steps:

* Make sure your SSIS packages can rerun on failure without side effects (for example, data loss or data duplication).
* Configure **Retry** and **Retry interval** of **Execute SSIS Package** activity on the **General** tab. ![Set properties on the General tab](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* For an ADO.NET and OLE DB source or destination component, set **ConnectRetryCount** and **ConnectRetryInterval** in Connection Manager in the SSIS package or SSIS activity.

### <a name="error-message-there-is-no-active-worker"></a>Error message: "There is no active worker."

This error usually means the SSIS integration runtime has an unhealthy status. Check the Azure portal for the status and detailed errors. For more information, see [Azure-SSIS integration runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Error message: "Your integration runtime cannot be upgraded and will eventually stop working, since we cannot access the Azure Blob container you provided for custom setup."

This error occurs when the SSIS integration runtime can't access the storage configured for custom setup. Check whether the shared access signature (SAS) URI that you provided is valid and hasn't expired.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Error message: "Microsoft OLE DB Provider for Analysis Services. 'Hresult: 0x80004005 Description:' COM error: COM error: mscorlib; Exception has been thrown by the target of an invocation"

One potential cause is that the username or password with Azure Multi-Factor Authentication enabled is configured for Azure Analysis Services authentication. This authentication isn't supported in the SSIS integration runtime. Try to use a service principal for Azure Analysis Services authentication:

1. Prepare a service principal as described in [Automation with service principals](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. In Connection Manager, configure **Use a specific user name and password**: set **AppID** as the username and **clientSecret** as the password.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Error message: "ADONET Source has failed to acquire the connection {GUID} with the following error message: Login failed for user 'NT AUTHORITY\ANONYMOUS LOGON'" when using a managed identity

Make sure you don't configure the authentication method of Connection Manager as **Active Directory Password Authentication** when the parameter *ConnectUsingManagedIdentity* is **True**. You can configure it as **SQL Authentication** instead, which is ignored if *ConnectUsingManagedIdentity* is set.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Error message: "0xC020801F at ..., OData Source [...]: Cannot acquire a managed connection from the run-time connection manager"

One potential cause is that the Transport Layer Security (TLS) is not enable in SSIS integration runtime which is required by your OData source. You can enable TLS in SSIS integration runtime by using Customize setup. More detail can be found at [Can't connect Project Online Odata from SSIS](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) and [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Error message: "Request staging task with operation guid ... fail since error: Failed to dispatch staging operation with error message: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Failed to load data proxy."

Make sure your Azure-SSIS integration runtime is configured with Self-Hosted integration runtime. More detail can be found at [Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Error message: "Staging task status: Failed. Staging task error: ErrorCode: 2010, ErrorMessage: The Self-hosted Integration Runtime ... is offline"

Make sure your Self-Hosted integration runtime is installed and started. More detail can be found at [Create and configure a self-hosted integration runtime](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Error message: "Staging task error: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "Error: The requested OLE DB provider ... is not registered. If the 64-bit driver is not installed, run the package in 32-bit mode..."

Make sure the corresponding provider used by your OLE DB connectors in your package are installed on Self-Hosted integration runtime machine properly. More detail can be found at [Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Error message: "Staging task error: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "Error: System.IO.FileLoadException: Could not load file or assembly 'Microsoft.WindowsAzure.Storage, Version=..., Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.'..."

One potential cause is your Self-Hosted integration runtime is not installed or upgraded properly. Suggest to download and reinstall the latest Self-hosted integration runtime. More detail can be found at [Create and configure a self-hosted integration runtime](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Error message: "A connection is required when requesting metadata. If you are working offline, uncheck Work Offline on the SSIS menu to enable the connection"

* Causa potencial e ação recomendada:
  * If there is also a warning message "The component does not support using connection manager with ConnectByProxy value setting true“ in the execution log, this means a connection manager is used on a component which hasn't supported "ConnectByProxy" yet. The supported components can be found at [Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Execution log can be found in [SSMS report](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) or in the log folder you specified in SSIS package execution activity.
  * vNet can also be used to access on-premises data as an alternative. More detail can be found at [Join an Azure-SSIS integration runtime to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Error message: "Staging task status: Failed. Staging task error: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "SSIS Executor exit code: -1.\n", "LogLocation": "...\\SSISTelemetry\\ExecutionLog\\...", "effectiveIntegrationRuntime": "...", "executionDuration": ..., "durationInQueue": { "integrationRuntimeQueue": ... }}"

Make sure Visual C++ runtime is installed on Self-Hosted integration runtime machine. More detail can be found at [Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Multiple Package executions are triggered unexpectedly

* Causa potencial e ação recomendada:
  * ADF stored procedure activity or Lookup activity are used to trigger SSIS package execution. The t-sql command may hit transient issue and trigger the rerun which would cause multiple package executions.
  * Use ExecuteSSISPackage activity instead which ensures package execution won’t rerun unless user set retry count in activity. Detail can be found at [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * Refine your t-sql command to be able to rerun by checking if an execution has already been triggered

### <a name="package-execution-takes-too-long"></a>Package execution takes too long

Here are potential causes and recommended actions:

* Too many package executions have been scheduled on the SSIS integration runtime. All these executions will be waiting in a queue for their turn.
  * Determine the maximum by using this formula:

    Max Parallel Execution Count per IR = Node Count * Max Parallel Execution per Node
  * To learn how to set the node count and maximum parallel execution per node, see [Create an Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md).
* The SSIS integration runtime is stopped or has an unhealthy status. To learn how to check the SSIS integration runtime status and errors, see [Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

We also recommend that you set a timeout on the **General** tab: ![Set properties on the General tab](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png).

### <a name="poor-performance-in-package-execution"></a>Poor performance in package execution

Tente estas ações:

* Make sure the SSIS integration runtime is in the same region as the data source and destination.

* Set the logging level of package execution to **Performance** to collect duration information for each component in the execution. For details, see [Integration Services (SSIS) logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Check IR node performance in the Azure portal:
  * For information about how to monitor the SSIS integration runtime, see [Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * You can find CPU/memory history for the SSIS integration runtime by viewing the metrics of the data factory in the Azure portal.
    ![Monitor metrics of the SSIS integration runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
