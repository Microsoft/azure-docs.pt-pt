---
title: Execução de pacote de resolução de problemas no tempo de execução da integração ssis
description: Este artigo fornece orientações de resolução de problemas para a execução de pacotes SSIS no tempo de execução da integração SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 8c85a652cde840336c51e1a5b5459f9dc591e0be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414676"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Execução de pacote de resolução de problemas no tempo de execução da integração ssis

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo inclui os erros mais comuns que poderá encontrar quando estiver a executar pacotes de Serviços de Integração de Servidores SQL (SSIS) no tempo de execução de integração sSIS. Descreve as potenciais causas e ações para resolver os erros.

## <a name="where-to-find-logs-for-troubleshooting"></a>Onde encontrar registos para resolução de problemas

Utilize o portal Azure Data Factory para verificar a saída da atividade de execução do pacote SSIS. A saída inclui o resultado da execução, as mensagens de erro e o ID da operação. Para mais detalhes, consulte [Monitorize o gasoduto](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Utilize o catálogo SSIS (SSISDB) para verificar os registos de detalhes para a execução. Para mais detalhes, consulte [monitor running packages e outras operações](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Erros comuns, causas e soluções

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Mensagem de erro: "Tempo de ligação expirado" ou "O serviço encontrou um erro processando o seu pedido. Tente novamente.”

Aqui estão as causas potenciais e as ações recomendadas:
* A fonte de dados ou destino está sobrecarregada. Verifique a carga na sua fonte de dados ou destino e veja se tem capacidade suficiente. Por exemplo, se utilizou a Base de Dados Azure SQL, considere escalonar se a base de dados for suscetível de sair.
* A rede entre o tempo de execução de integração SSIS e a fonte de dados ou destino é instável, especialmente quando a ligação é transversal ou entre as instalações e o Azure. Aplique o padrão de retry no pacote SSIS seguindo estes passos:
  * Certifique-se de que os seus pacotes SSIS podem reexecutar-se sem efeitos secundários (por exemplo, perda de dados ou duplicação de dados).
  * Configure o intervalo de **retry** e **retry** da atividade ![do pacote Execute **SSIS** no separador **Geral.**](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Para obter um componente de origem ou destino de ADO.NET e OLE DB, delineie **O ConnectRetryCount** e **ConnectRetryInterval** no Connection Manager no pacote SSIS ou na atividade SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Mensagem de erro: "A ADO NET Source falhou em adquirir a ligação '...'" com "Ocorreu um erro específico de rede ou de instância ao estabelecer uma ligação ao Servidor SQL. O servidor não foi encontrado ou não estava acessível."

Normalmente, este problema significa que a origem ou o destino de dados está inacessível a partir do runtime de integração do SSIS. Os motivos podem variar. Tente estas ações:
* Certifique-se de que está a passar corretamente a fonte de dados ou o nome de destino/IP.
* Certifique-se de que a firewall está corretamente definida.
* Certifique-se de que a sua rede virtual está configurada corretamente se a sua fonte de dados ou destino estiver no local:
  * Pode verificar se o problema é a partir da configuração da rede virtual, disponibilizando um VM Azure na mesma rede virtual. Em seguida, verifique se a fonte de dados ou destino pode ser acedida a partir do VM Azure.
  * Pode encontrar mais detalhes sobre a utilização de uma rede virtual com um tempo de execução de integração SSIS em [Join a Azure-SSIS integration time to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Mensagem de erro: "A ADO NET Source falhou em adquirir a ligação '...'" com "Não podia criar um gestor de ligação gerido."

A causa potencial é que o fornecedor ADO.NET utilizado no pacote não está instalado no tempo de execução de integração SSIS. Pode instalar o fornecedor utilizando uma configuração personalizada. Pode encontrar mais detalhes sobre a configuração personalizada na configuração Customize para o tempo de [execução da integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Mensagem de erro: "A ligação '...' não é encontrado"

Um problema conhecido em versões mais antigas do SQL Server Management Studio (SSMS) pode causar este erro. Se o pacote contiver um componente personalizado (por exemplo, Pacote de Funcionalidades do Azure para SSIS ou componentes de parceiros) que não esteja instalado no computador onde o SSMS é utilizado para fazer a implementação, o SSMS vai remover o componente e provocar o erro. Atualize o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para a versão mais recente na qual o problema foi corrigido.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Error message:"SSIS Executor código de saída: -1073741819."

* Causa potencial e ação recomendada:
  * Este erro pode dever-se à limitação da fonte e destino do Excel quando várias fontes ou destinos do Excel estão a ser executados em paralelo em várias linhas. Pode contornar esta limitação alterando os seus componentes Excel para executar em sequência, ou separá-los em diferentes pacotes e desencadear através de "Executar Tarefa de Pacote" com executar A propriedade ExecuteOutOfProcess definida como True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Mensagem de erro: "Não há espaço suficiente no disco"

Este erro significa que o disco local é utilizado no nó de tempo de execução de integração SSIS. Verifique se o seu pacote ou configuração personalizada está a consumir muito espaço em disco:
* Se o disco for consumido pelo seu pacote, será libertado após o acabamento da execução do pacote.
* Se o disco for consumido pela sua configuração personalizada, terá de parar o tempo de execução da integração sSIS, modificar o seu script e recomeçar o tempo de integração. Todo o recipiente de blob Azure que especificou para configuração personalizada será copiado para o nó de tempo de integração SSIS, por isso verifique se há algum conteúdo desnecessário sob esse recipiente.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Mensagem de erro: "Falhou em recuperar recursos do mestre. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Descrição:Ficheiro de carga "***" falhou."

* Causa potencial e ação recomendada:
  * Se a Atividade SSIS estiver a executar o pacote a partir do sistema de ficheiros (ficheiro de pacote ou ficheiro de projeto), este erro ocorrerá se o ficheiro de projeto, pacote ou configuração não estiver acessível com a credencial de acesso ao pacote que forneceu na Atividade SSIS
    * Se estiver a utilizar o Ficheiro Azure:
      * O caminho do \\ \\ \<ficheiro deve\>começar\\\<com o nome da conta de armazenamento .file.core.windows.net caminho de partilha de ficheiros\>
      * O domínio deve ser "Azure"
      * O nome de \<utilizador deve ser o nome da conta de armazenamento\>
      * A senha \<deve ser a chave de acesso ao armazenamento\>
    * Se estiver a utilizar o ficheiro no local, verifique se o VNet, a credencial de acesso ao pacote e a permissão estão configuradas corretamente para que o seu tempo de funcionamento de integração Azure-SSIS possa aceder à sua partilha de ficheiros no local

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Mensagem de erro: "O nome do ficheiro '...' especificado na ligação não era válido"

* Causa potencial e ação recomendada:
  * Um nome de ficheiro inválido é especificado
  * Certifique-se de que está a utilizar o FQDN (Nome de Domínio Totalmente Qualificado) em vez de pouco tempo no seu gestor de ligação

### <a name="error-message-cannot-open-file-"></a>Mensagem de erro: "Não pode abrir ficheiro '...'"

Este erro ocorre quando a execução do pacote não consegue encontrar um ficheiro no disco local no tempo de execução da integração SSIS. Tente estas ações:
* Não use o caminho absoluto no pacote que está sendo executado no tempo de execução da integração SSIS. Utilize o atual diretório de trabalho de execução (.) ou a pasta temporária (%TEMP%) em vez disso.
* Se precisar de persistir alguns ficheiros sobre os nós de tempo de execução de integração SSIS, prepare os ficheiros conforme descrito na [configuração Personalizar](how-to-configure-azure-ssis-ir-custom-setup.md). Todos os ficheiros do diretório de trabalho serão limpos após a execução estar terminada.
* Utilize ficheiros Azure em vez de armazenar o ficheiro no nó de tempo de execução de integração SSIS. Para mais detalhes, consulte [use as ações de ficheiro sinuosa](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Mensagem de erro: "A base de dados 'SSISDB' atingiu a sua quota de tamanho"

Uma causa potencial pode ser quando a base de dados SSISDB criada na base de dados SQL do Azure, ou uma instância gerida quando está a criar um runtime de integração do SSIS, atingiu a sua quota. Tente estas ações:
* Considere aumentar a DTU da base de dados. Pode encontrar detalhes em [SQL Database resource limits for an Azure SQL Database server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) (Limites de recursos da Base de Dados SQL para um servidor da Base de Dados SQL do Azure).
* Verifique se o pacote iria gerar muitos registos. Se for o caso, pode configurar uma tarefa elástica para limpar esses registos. Para obter detalhes, veja [Clean up SSISDB logs with Azure Elastic Database jobs](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) (Limpar registos SSISDB com tarefas de Base de Dados Elástica do Azure)

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Error message: "O limite de pedido para a base de dados é ... e foi alcançado.

Se muitos pacotes estiverem em funcionamento paralelos no tempo de funcionamento da integração ssis, este erro pode ocorrer porque o SSISDB atingiu o seu limite de pedidos. Considere aumentar o DTC do SSISDB para resolver esta questão. Pode encontrar detalhes em [SQL Database resource limits for an Azure SQL Database server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) (Limites de recursos da Base de Dados SQL para um servidor da Base de Dados SQL do Azure).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Mensagem de erro: "A Operação SSIS falhou com o estado de funcionamento inesperado: ..."

O erro é causado principalmente por um problema transitório, por isso tente reexecutar a execução do pacote. Aplique o padrão de retry no pacote SSIS seguindo estes passos:

* Certifique-se de que os seus pacotes SSIS podem reexecutar-se sem efeitos secundários (por exemplo, perda de dados ou duplicação de dados).
* Configure o intervalo de **retry** e **retry** da atividade ![do pacote Execute **SSIS** no separador **Geral.**](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Para obter um componente de origem ou destino de ADO.NET e OLE DB, delineie **O ConnectRetryCount** e **ConnectRetryInterval** no Connection Manager no pacote SSIS ou na atividade SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Mensagem de erro: "Não há nenhum trabalhador ativo."

Este erro geralmente significa que o tempo de execução da integração SSIS tem um estado pouco saudável. Verifique o portal Azure sobre o estado e erros detalhados. Para mais informações, consulte o tempo de execução da [integração Azure-SSIS.](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Mensagem de erro: "O seu tempo de funcionamento de integração não pode ser atualizado e acabará por deixar de funcionar, uma vez que não podemos aceder ao contentor Azure Blob que forneceu para configuração personalizada."

Este erro ocorre quando o tempo de execução de integração SSIS não consegue aceder ao armazenamento configurado para configuração personalizada. Verifique se a assinatura de acesso partilhado (SAS) URI que forneceu é válida e não expirou.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Error message: "Microsoft OLE DB Provider for Analysis Services. «Hresult: 0x80004005 Descrição:» Erro com: erro com: mscorlib; Exceção foi lançada pelo alvo de uma invocação"

Uma das causas potenciais é que o nome de utilizador ou palavra-passe com autenticação multifactor ativada está configurado para autenticação dos Serviços de Análise Do Azure. Esta autenticação não é suportada no tempo de execução da integração SSIS. Tente utilizar um diretor de serviço para autenticação dos Serviços de Análise Azure:

1. Prepare um diretor de serviço conforme descrito na [Automação com os diretores de serviço.](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
2. No Connection Manager, configure **Utilize um nome de utilizador específico e uma palavra-passe**: detete teo **AppID** como nome de utilizador e **clienteSecret** como palavra-passe.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Mensagem de erro: "A ADONET Source falhou em adquirir a ligação {GUID} com a seguinte mensagem de erro: O login falhou no utilizador 'NT AUTHORITY\ANONYMOUS LOGON'" ao utilizar uma identidade gerida

Certifique-se de que não configura o método de autenticação do Gestor de Ligação como Autenticação de **Palavras-passe** de Diretório Ativo quando o parâmetro *ConnectUsingManagedIdentity* for **True**. Em vez disso, pode configurá-lo como **Autenticação SQL,** que é ignorado se *a ConnectUsingManagedIdentity* estiver definida.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Mensagem de erro: "0xC020801F at ..., OData Source [...]: Não pode adquirir uma ligação gerida do gestor de ligação de tempo de execução"

Uma das causas potenciais é que a Segurança da Camada de Transporte (TLS) não está ativada no tempo de execução da integração do SSIS, o que é exigido pela sua fonte OData. Pode ativar o tempo de execução da integração do SSIS em TLS utilizando a configuração Customize. Mais detalhes podem ser encontrados no [Can't connect Project Online Odata from SSIS](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) and Customize configuração para o tempo de execução de [integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Mensagem de erro: "Solicite tarefa de encenação com guia de operação ... falha desde o erro: Falhou na operação de descarregamento com mensagem de erro: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Failed to load data proxy."

Certifique-se de que o seu tempo de execução de integração Azure-SSIS está configurado com o tempo de execução da integração auto-hospedada. Mais detalhes podem ser encontrados no [Configure Self-Hosted IR como um proxy para o IR Azure-SSIS em ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Mensagem de erro: "Estado de tarefa de preparação: Falhou. Erro de tarefa de encenação: ErrorCode: 2010, ErrorMessage: The Self-Hosted Integration Runtime ... está offline"

Certifique-se de que o seu tempo de execução de integração auto-hospedado está instalado e iniciado. Mais detalhes podem ser encontrados na [Create e configurar um tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Error message: "Error error: ErrorCode: 2906, ErrorMessage: A execução do pacote falhou., Saída: {"OperationErrorMessages": "Error: The requested OLE DB provider ... não está registado. Se o controlador de 64 bits não estiver instalado, execute a embalagem em modo de 32 bits..."

Certifique-se de que o fornecedor correspondente utilizado pelos seus conectores OLE DB no seu pacote está instalado na máquina de tempo de funcionação de integração auto-hospedada corretamente. Mais detalhes podem ser encontrados no [Configure Self-Hosted IR como um proxy para O IR Azure-SSIS em ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Error message: "Error error error: ErrorCode: 2906, ErrorMessage: A execução do pacote falhou., Saída: {"OperationErrorMessages": "Error: System.IO.FileLoadException: Could't load file or assembly 'Microsoft.WindowsAzure.Storage, Version=..., Culture=neutral, PublicKeyToken=31bf3856ad364e35" ou uma das suas dependências. A definição manifesto da montagem não corresponde à referência da montagem.». ..."

Uma das causas potenciais é que o seu tempo de funcionação de integração auto-hospedado não é instalado ou atualizado corretamente. Sugira que descarregue e reinstale o mais recente tempo de integração auto-hospedado. Mais detalhes podem ser encontrados na [Create e configurar um tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Mensagem de erro: "É necessária uma ligação ao solicitar metadados. Se estiver a trabalhar offline, desverifique o trabalho offline no menu SSIS para ativar a ligação"

* Causa potencial e ação recomendada:
  * Se houver também uma mensagem de aviso "O componente não suporta utilizar o gestor de ligação com a definição de valor ConnectByProxy verdadeiramente" no registo de execução, isto significa que um gestor de ligação é usado num componente que ainda não tenha suportado "ConnectByProxy". Os componentes suportados podem ser encontrados no [Configure Self-Hosted IR como um proxy para o IR Azure-SSIS em ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * O registo de execução pode ser encontrado no [relatório SSMS](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) ou na pasta de registo especificada na atividade de execução do pacote SSIS.
  * vNet também pode ser usado para aceder aos dados no local como uma alternativa. Mais detalhes podem ser encontrados em [Join a Azure-SSIS integração tempo de execução para uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Mensagem de erro: "Estado de tarefa de preparação: Falhou. Error de tarefa de encenação: ErrorCode: 2906, ErrorMessage: A execução do pacote falhou., Saída: {"OperationErrorMessages": "Código de saída do Executor SSIS: -1.\n", "LogLocation": "... \\SSISTelemettry\\ExecutionLog...",\\"effectiveIntegrationTimetime": "...", "execuçãoDura": ..., "duraçãoInQueue": { "integrationRuntimeQueue": } }}"

Certifique-se de que o tempo de execução visual C++ está instalado na máquina de tempo de execução de integração auto-hospedada. Mais detalhes podem ser encontrados no [Configure Self-Hosted IR como um proxy para O IR Azure-SSIS em ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>As execuções de pacotes múltiplos são desencadeadas inesperadamente

* Causa potencial e ação recomendada:
  * A df stored procedure activity ou Lookup activity são usados para desencadear a execução do pacote SSIS. O comando t-sql pode atingir um problema transitório e desencadear a reexecução que causaria múltiplas execuções de pacotes.
  * Utilize a atividade ExecuteSSISPackage, o que garante que a execução do pacote não será executada a menos que o utilizador tenha definido a contagem de funções. Detalhes podem ser encontrados em[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * Refine o seu comando t-sql para ser capaz de reexecutar verificando se uma execução já foi desencadeada

### <a name="package-execution-takes-too-long"></a>A execução do pacote demora muito tempo.

Aqui estão as causas potenciais e as ações recomendadas:

* Foram agendadas demasiadas execuções organizadas no prazo de execução da integração ssis. Todas estas execuções estarão à espera numa fila para a sua vez.
  * Determine o máximo utilizando esta fórmula:

    Contagem de execução paralela max por IR = Contagem do nó * Execução paralela max por nó
  * Para aprender a definir a contagem do nó e a máxima execução paralela por nó, consulte Criar um tempo de funcionação de [integração Azure-SSIS na Azure Data Factory](create-azure-ssis-integration-runtime.md).
* O tempo de execução da integração SSIS é interrompido ou tem um estatuto pouco saudável. Para saber como verificar o estado de funcionamento e os erros de funcionamento da integração ssis, consulte o tempo de funcionamento da [integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Recomendamos também que estabeleça um intervalo no ![separador](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) **Geral:** Coloque as propriedades no separador Geral .

### <a name="poor-performance-in-package-execution"></a>Mau desempenho na execução de pacotes

Tente estas ações:

* Certifique-se de que o tempo de execução da integração SSIS se encontra na mesma região que a fonte de dados e o destino.

* Detete o nível de registo da execução do pacote para **o Desempenho** para recolher informações de duração para cada componente na execução. Para mais detalhes, consulte a [exploração madeireira dos Serviços de Integração (SSIS).](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

* Verifique o desempenho do nó IR no portal Azure:
  * Para obter informações sobre como monitorizar o tempo de funcionação da integração do SSIS, consulte o tempo de funcionação da [integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Pode encontrar o histórico de CPU/memória para o tempo de execução da integração SSIS, visualizando as métricas da fábrica de dados no portal Azure.
    ![Monitorizar as métricas do tempo de execução da integração SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
