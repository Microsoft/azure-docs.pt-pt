---
title: Execução de pacotes de resolução de problemas no tempo de execução da integração do SSIS
description: Este artigo fornece orientações de resolução de problemas para a execução do pacote SSIS no tempo de execução da integração SSIS
ms.service: data-factory
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 2bc56d39de392c9e4c20c25b554e3bdeea048bfb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361881"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Execução de pacotes de resolução de problemas no tempo de execução da integração do SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo inclui os erros mais comuns que poderá encontrar quando estiver a executar pacotes SQL Server Integration Services (SSIS) no tempo de funcionamento da integração SSIS. Descreve as potenciais causas e ações para resolver os erros.

## <a name="where-to-find-logs-for-troubleshooting"></a>Onde encontrar troncos para resolução de problemas

Utilize o portal Azure Data Factory para verificar a saída da atividade de execução do pacote SSIS. A saída inclui o resultado da execução, mensagens de erro e iD de funcionamento. Para mais informações, consulte [o gasoduto .](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

Utilize o catálogo SSIS (SSISDB) para verificar os registos de pormenor para a execução. Para mais informações, consulte [os pacotes de execução do monitor e outras operações.](/sql/integration-services/performance/monitor-running-packages-and-other-operations)

## <a name="common-errors-causes-and-solutions"></a>Erros comuns, causas e soluções

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Error message: "Connection Timeout expired" ou "The service has encountered a error processing your request. Tente novamente.”

Aqui estão as causas potenciais e as ações recomendadas:
* A fonte de dados ou destino está sobrecarregado. Verifique a carga na sua fonte de dados ou destino e veja se tem capacidade suficiente. Por exemplo, se utilizar a Base de Dados Azure SQL, considere aumentar se a base de dados for suscetível de esgotar.
* A rede entre o tempo de integração do SSIS e a fonte ou destino de dados é instável, especialmente quando a ligação é inter-região ou entre as instalações e o Azure. Aplique o padrão de repetição no pacote SSIS seguindo estes passos:
  * Certifique-se de que os seus pacotes SSIS podem ser reexecutados em avarias sem efeitos colaterais (por exemplo, perda de dados ou duplicação de dados).
  * Configure o intervalo **de retíria** e **retagem** da atividade do **Pacote SSIS executado** no separador **Geral.** ![ Definir propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Para um componente de origem ou destino de ADO.NET e OLE DB, descreva **o ConnectRetryCount** e o **ConnectRetryInterval** no Connection Manager no pacote SSIS ou na atividade SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Error message: "ADO NET Source não adquiriu a ligação '...'" com "Ocorreu um erro relacionado com a rede ou por exemplo ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não estava acessível."

Normalmente, este problema significa que a origem ou o destino de dados está inacessível a partir do runtime de integração do SSIS. Os motivos podem variar. Tente estas ações:
* Certifique-se de que está a passar corretamente a fonte de dados ou o nome de destino/IP.
* Certifique-se de que a firewall está corretamente definida.
* Certifique-se de que a sua rede virtual está configurada corretamente se a sua fonte de dados ou destino estiver no local:
  * Pode verificar se o problema é da configuração da rede virtual, apresentando um Azure VM na mesma rede virtual. Em seguida, verifique se a fonte de dados ou destino pode ser acedido a partir do Azure VM.
  * Pode encontrar mais detalhes sobre a utilização de uma rede virtual com um tempo de integração SSIS em [Join a Azure-SSIS integrationtime to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Error message: "ADO NET Source não adquiriu a ligação '...'" com "Não foi possível criar um gestor de conexão gerido."

A causa potencial é que o fornecedor ADO.NET utilizado no pacote não esteja instalado no tempo de funcionação da integração SSIS. Pode instalar o fornecedor utilizando uma configuração personalizada. Pode encontrar mais detalhes sobre a configuração personalizada na [configuração Personale para o tempo de execução da integração Azure-SSIS.](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Error message: "The connection '...' não é encontrado"

Um problema conhecido em versões mais antigas do SQL Server Management Studio (SSMS) pode causar este erro. Se o pacote contiver um componente personalizado (por exemplo, Pacote de Funcionalidades do Azure para SSIS ou componentes de parceiros) que não esteja instalado no computador onde o SSMS é utilizado para fazer a implementação, o SSMS vai remover o componente e provocar o erro. Atualize o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) para a versão mais recente na qual o problema foi corrigido.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Error message:"SSIS Executor exit code: -1073741819."

* Causa potencial e ação recomendada:
  * Este erro pode ser devido à limitação para fonte e destino excel quando várias fontes ou destinos excel estão executando em paralelo em multi-thread. Pode contornar esta limitação alterando os seus componentes Excel para executar em sequência, ou separá-los em diferentes pacotes e desencadear através de "Executar tarefa pacote" com executeOutOfProcess definido como Verdadeiro.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Error message: "Não há espaço suficiente no disco"

Este erro significa que o disco local é utilizado no nó de tempo de execução de integração SSIS. Verifique se o seu pacote ou configuração personalizada está a consumir muito espaço em disco:
* Se o disco for consumido pelo seu pacote, será libertado após o fim da execução do pacote.
* Se o disco for consumido pela sua configuração personalizada, terá de parar o tempo de funcionação da integração SSIS, modificar o seu script e recomeçar o tempo de execução da integração. Todo o recipiente de bolhas Azure que especificou para configuração personalizada será copiado para o nó de tempo de execução de integração SSIS, por isso verifique se há algum conteúdo desnecessário debaixo desse recipiente.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Error message: "Failed to ressarsed resource from master. Microsoft.SqlServer.IntegrationServices.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Descrição:Ficheiro de carga "***" falhou."

* Causa potencial e ação recomendada:
  * Se a Atividade SSIS estiver a executar o pacote a partir do sistema de ficheiros (ficheiro de pacote ou ficheiro de projeto), este erro ocorrerá se o projeto, pacote ou ficheiro de configuração não estiver acessível com a credencial de acesso ao pacote que forneceu na Atividade SSIS
    * Se estiver a utilizar o Ficheiro Azure:
      * O caminho do ficheiro deve começar com \\ \\ \<storage account name\> .file.core.windows.net\\\<file share path\>
      * O domínio deve ser "Azure"
      * O nome de utilizador deve ser \<storage account name\>
      * A senha deve ser \<storage access key\>
    * Se estiver a utilizar o ficheiro no local, verifique se o VNet, a credencial de acesso ao pacote e a permissão estão configuradas corretamente para que o seu tempo de integração Azure-SSIS possa aceder à sua partilha de ficheiros no local

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Error message: "The file name '...' especificado na ligação não era válido"

* Causa potencial e ação recomendada:
  * Um nome de ficheiro inválido é especificado
  * Certifique-se de que está a utilizar fQDN (Nome de Domínio Totalmente Qualificado) em vez de pouco tempo no seu gestor de ligação

### <a name="error-message-cannot-open-file-"></a>Error message: "Can't open file '...'"

Este erro ocorre quando a execução do pacote não consegue encontrar um ficheiro no disco local no tempo de execução da integração SSIS. Tente estas ações:
* Não use o caminho absoluto no pacote que está sendo executado no tempo de integração SSIS. Utilize o diretório de trabalho de execução atual (.) ou a pasta temporária (%TEMP%) em vez disso.
* Se precisar de persistir alguns ficheiros sobre os nós de tempo de execução de integração SSIS, prepare os ficheiros conforme descrito na [configuração Personale](how-to-configure-azure-ssis-ir-custom-setup.md). Todos os ficheiros do diretório de trabalho serão limpos depois da execução estar terminada.
* Utilize ficheiros Azure em vez de armazenar o ficheiro no nó de tempo de execução de integração SSIS. Para mais informações, consulte [as ações de ficheiros Use Azure](/sql/integration-services/lift-shift/ssis-azure-files-file-shares#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Error message: "The database 'SSISDB' atingiu a sua quota de tamanho"

Uma causa potencial é que a base de dados SSISDB criada na Base de Dados Azure SQL ou em SQL Managed Instance atingiu a sua quota. Tente estas ações:
* Considere aumentar a DTU da base de dados. Pode encontrar detalhes nos [limites da Base de Dados SQL para um servidor lógico.](../azure-sql/database/resource-limits-logical-server.md)
* Verifique se o pacote iria gerar muitos registos. Se for o caso, pode configurar uma tarefa elástica para limpar esses registos. Para obter detalhes, veja [Clean up SSISDB logs with Azure Elastic Database jobs](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) (Limpar registos SSISDB com tarefas de Base de Dados Elástica do Azure)

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Error message: "The request limit for the database is ... e foi alcançado.

Se muitos pacotes estiverem a funcionar em paralelo no tempo de funcionamento da integração do SSIS, este erro pode ocorrer porque o SSISDB atingiu o seu limite de pedido. Considere aumentar o DTC do SSISDB para resolver esta questão. Pode encontrar detalhes nos [limites da Base de Dados SQL para um servidor lógico.](../azure-sql/database/resource-limits-logical-server.md)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Error message: "SSIS Operation failed with unexpected operation status: ..."

O erro é causado principalmente por um problema transitório, por isso tente refazer a execução do pacote. Aplique o padrão de repetição no pacote SSIS seguindo estes passos:

* Certifique-se de que os seus pacotes SSIS podem ser reexecutados em avarias sem efeitos colaterais (por exemplo, perda de dados ou duplicação de dados).
* Configure o intervalo **de retíria** e **retagem** da atividade do **Pacote SSIS executado** no separador **Geral.** ![ Definir propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Para um componente de origem ou destino de ADO.NET e OLE DB, descreva **o ConnectRetryCount** e o **ConnectRetryInterval** no Connection Manager no pacote SSIS ou na atividade SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Mensagem de erro: "Não há nenhum trabalhador ativo."

Este erro geralmente significa que o tempo de integração SSIS tem um estado pouco saudável. Verifique o portal Azure para obter o estado e erros detalhados. Para mais informações, consulte [o tempo de integração do Azure-SSIS.](./monitor-integration-runtime.md#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Error message: "Your integration runtime can't be upgrade and will eventualmente stop working, as we can't access the Azure Blob container you providenciado para configuração personalizada."

Este erro ocorre quando o tempo de funcionaamento da integração SSIS não consegue aceder ao armazenamento configurado para configuração personalizada. Verifique se a assinatura de acesso partilhado (SAS) URI que forneceu é válida e não expirou.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Mensagem de erro: "Microsoft OLE DB Provider for Analysis Services. 'Hresult: 0x80004005 Description:' COM error: COM error: mscorlib; A exceção foi lançada pelo alvo de uma invocação"

Uma das causas potenciais é que o nome de utilizador ou palavra-passe com autenticação multi-factor AZure AD ativada está configurado para a autenticação dos Serviços de Análise Azure. Esta autenticação não é suportada no tempo de integração do SSIS. Tente utilizar um principal de serviço para autenticação dos Serviços de Análise Azure:

1. Prepare um diretor de serviço como descrito na [Automação com os principais de serviço.](../analysis-services/analysis-services-service-principal.md)
2. Em Connection Manager, configurar **Utilize um nome de utilizador específico e senha:** desconfie o **AppID** como nome de utilizador e **clienteSecret** como palavra-passe.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Mensagem de erro: "A ADONET Source falhou em adquirir a ligação {GUID} com a seguinte mensagem de erro: O login falhou para o utilizador 'NT AUTHORITY\ANONYMOUS LOGON'" ao utilizar uma identidade gerida

Certifique-se de que não configura o método de autenticação do Connection Manager como **Autenticação de Password do Diretório Ativo** quando o parâmetro *ConnectUsingManagedIdentity* for **Verdadeiro**. Em vez disso, pode configumentá-la como **Autenticação SQL,** o que é ignorado se *o ConnectUsingManagedIdentity* estiver definido.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Error message: "0xC020801F at ..., OData Source [...]: Não é possível adquirir uma ligação gerida a partir do gestor de ligação de tempo de execução"

Uma das causas potenciais é que a Segurança da Camada de Transporte (TLS) não está ativada no tempo de integração do SSIS, o que é exigido pela sua fonte OData. Pode ativar o tempo de execução da integração do SSIS através da configuração Personalize. Mais detalhes podem ser encontrados na [Can't connect Project Online Odata from SSIS](/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) e [Personalizar configuração para o tempo de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Error message: "Request staging task with operation guid ... falha desde erro: Falhou na operação de paragem com mensagem de erro: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Falhou na carga de procuração de dados."

Certifique-se de que o seu tempo de integração Azure-SSIS está configurado com Self-Hosted tempo de integração. Mais detalhes podem ser encontrados na [Configure Self-Hosted IR como um representante da Azure-SSIS IR em ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Error message: "Staging task status: Failed. Erro de tarefa de encenação: ErrorCode: 2010, ErrorMessage: The Self-hosted Integration Runtime ... está offline"

Certifique-se de que o seu tempo de Self-Hosted integração está instalado e iniciado. Mais detalhes podem ser encontrados na [Create e configurar um tempo de integração auto-hospedado](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Error message: "Staging task error: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "Error: The requested OLE DB provider ... não está registado. Se o controlador de 64 bits não estiver instalado, corram a embalagem em modo de 32 bits..."

Certifique-se de que o fornecedor correspondente utilizado pelos seus conectores OLE DB na sua embalagem está instalado Self-Hosted máquina de tempo de funcionamento de integração corretamente. Mais detalhes podem ser encontrados na [Configure Self-Hosted IR como um representante da Azure-SSIS IR em ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Error message: "Staging task error: ErrorCode: 2906, ErrorMessage: A execução do pacote falhou., Saída: {"OperationErrorMessages": "Error: System.IO.FileLoadException: Não foi possível carregar ficheiro ou montagem 'Microsoft.WindowsAzure.Storage, Versão=..., Cultura=neutral, PublicKeyToken=31bf3856ad3633335335' ou uma das suas dependências. A definição manifesta do conjunto localizado não corresponde à referência de montagem.» ..."

Uma das causas potenciais é o seu tempo de Self-Hosted a integração não é instalada ou atualizada corretamente. Sugira baixar e reinstalar o mais recente tempo de integração auto-hospedado. Mais detalhes podem ser encontrados na [Create e configurar um tempo de integração auto-hospedado](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Error message: "É necessária uma ligação ao solicitar metadados. Se estiver a trabalhar offline, desmarque o Trabalho Offline no menu SSIS para ativar a ligação"

* Causa potencial e ação recomendada:
  * Se houver também uma mensagem de aviso "O componente não suporta a utilização do gestor de ligação com a definição de valor ConnectByProxy verdadeira" no registo de execução, isto significa que um gestor de ligação é utilizado num componente que ainda não apoiou "ConnectByProxy". Os componentes suportados podem ser encontrados na [Configure Self-Hosted IR como um representante para Azure-SSIS IR em ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * O registo de execução pode ser encontrado no [relatório SSMS](/sql/integration-services/performance/monitor-running-packages-and-other-operations#reports) ou na pasta de registo que especificou na atividade de execução do pacote SSIS.
  * vNet também pode ser usado para aceder a dados no local como uma alternativa. Mais detalhes podem ser encontrados em [Join a Azure-SSIS integrationtime to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Error message: "Staging task status: Failed. Erro de tarefa: ErrorCode: 2906, ErrorMessage: A execução do pacote falhou., Saída: {"OperationErrorMessages": "SSIS Executor exit code: -1.\n", "LogLocation": "... \\ SSISTelemetry \\ ExecutionLog \\ ...", "effectiveIntegrationRuntime": "...", "executionDuration": ..., "duraçãoInQueue": { "integrationRuntimeQueue": ... }}}

Certifique-se de que o tempo de funcionação Visual C++ está instalado na máquina de execução de integração Self-Hosted. Mais detalhes podem ser encontrados na [Configure Self-Hosted IR como um representante da Azure-SSIS IR em ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Múltiplas execuções de pacotes são desencadeadas inesperadamente

* Causa potencial e ação recomendada:
  * A atividade de procedimento armazenado pela ADF ou atividade de Procura são usadas para desencadear a execução do pacote SSIS. O comando t-sql pode atingir problemas transitórios e desencadear a repetição que causaria múltiplas execuções de pacotes.
  * Utilize a atividade ExecuteSSISPackage, que garante que a execução do pacote não será reexecutada a menos que o utilizador configure a contagem de novo na atividade. Detalhe pode ser encontrado em [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](./how-to-invoke-ssis-package-ssis-activity.md)
  * Refine o seu comando t-sql para ser capaz de ser re-executado verificando se uma execução já foi desencadeada

### <a name="package-execution-takes-too-long"></a>A execução do pacote demora muito tempo

Aqui estão as causas potenciais e as ações recomendadas:

* Foram agendadas demasiadas execuções organizadas no tempo de funcionamento da integração do SSIS. Todas estas execuções estarão à espera numa fila pela sua vez.
  * Determinar o máximo utilizando esta fórmula:

    Contagem de execução paralela max por IR = Contagem de nó * Execução Paralela Máxima por Nó
  * Para aprender a definir a contagem de nós e a execução paralela máxima por nó, consulte [Criar um tempo de integração Azure-SSIS na Azure Data Factory](create-azure-ssis-integration-runtime.md).
* O tempo de integração do SSIS é interrompido ou tem um estatuto pouco saudável. Para aprender a verificar o estado e erros de funcionamento da integração SSIS, consulte [o tempo de execução da integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Recomendamos também que estabeleça um intervalo no separador **Geral:** ![ Desateia propriedades no separador ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) Geral.

### <a name="poor-performance-in-package-execution"></a>Mau desempenho na execução de pacotes

Tente estas ações:

* Certifique-se de que o tempo de integração do SSIS se encontra na mesma região que a fonte de dados e o destino.

* Desabrohe o nível de execução do pacote para **o Desempenho** para recolher informações de duração para cada componente na execução. Para mais informações, consulte a [sessão de registo dos Serviços de Integração (SSIS).](/sql/integration-services/performance/integration-services-ssis-logging)

* Verifique o desempenho do nó IV no portal Azure:
  * Para obter informações sobre como monitorizar o tempo de funcionamento da integração do [SSIS, consulte o tempo de execução da integração do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Pode encontrar o histórico de CPU/memória para o tempo de execução da integração SSIS visualizando as métricas da fábrica de dados no portal Azure.
    ![Métricas de monitorização do tempo de funcionação da integração do SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)