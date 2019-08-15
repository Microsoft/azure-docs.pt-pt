---
title: Solucionar problemas de execução de pacote no Integration Runtime do SSIS | Microsoft Docs
description: Este artigo fornece diretrizes de solução de problemas para a execução do pacote SSIS no Integration Runtime do SSIS
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
ms.openlocfilehash: a7ad0f3be754029c654b04d19750aab7bbcd210d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933639"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Solucionar problemas de execução de pacote no Integration Runtime do SSIS

Este artigo inclui os erros mais comuns que você pode encontrar quando estiver executando pacotes do SQL Server Integration Services (SSIS) no Integration Runtime do SSIS. Ele descreve as possíveis causas e ações para resolver os erros.

## <a name="where-to-find-logs-for-troubleshooting"></a>Onde encontrar logs para solução de problemas

Use o portal de Azure Data Factory para verificar a saída da atividade de execução do pacote SSIS. A saída inclui o resultado da execução, as mensagens de erro e a ID da operação. Para obter detalhes, consulte [monitorar o pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Use o catálogo do SSIS (SSISDB) para verificar os logs de detalhes da execução. Para obter detalhes, consulte [monitorar pacotes em execução e outras operações](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Erros, causas e soluções comuns

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Mensagem de erro: "Tempo limite de conexão expirado" ou "o serviço encontrou um erro ao processar sua solicitação. Tente novamente. "

Aqui estão as possíveis causas e as ações recomendadas:
* A fonte de dados ou o destino está sobrecarregado. Verifique a carga na fonte de dados ou no destino e veja se ela tem capacidade suficiente. Por exemplo, se você usou o banco de dados SQL do Azure, considere escalar verticalmente se o banco de dados provavelmente atingir o tempo limite.
* A rede entre o tempo de execução de integração do SSIS e a fonte de dados ou destino é instável, especialmente quando a conexão é entre regiões ou entre o local e o Azure. Aplique o padrão de repetição no pacote do SSIS seguindo estas etapas:
  * Verifique se seus pacotes SSIS podem ser executados novamente em caso de falha sem efeitos colaterais (por exemplo, perda de dados ou duplicação de dados).
  * Configure o **intervalo** de **repetição** e repetição da atividade **Executar Pacote SSIS** na guia **geral** . ![Definir propriedades na guia geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Para um componente de origem ou destino ADO.NET e OLE DB, defina **ConnectRetryCount** e **ConnectRetryInterval** no Gerenciador de conexões no pacote SSIS ou na atividade SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Mensagem de erro: "A origem do ADO NET falhou ao adquirir a conexão '... '" com "ocorreu um erro relacionado à rede ou específico à instância ao estabelecer uma conexão com SQL Server. O servidor não foi encontrado ou não estava acessível. "

Normalmente, este problema significa que a origem ou o destino de dados está inacessível a partir do runtime de integração do SSIS. Os motivos podem variar. Tente estas ações:
* Verifique se você está passando a fonte de dados ou o nome/IP de destino corretamente.
* Verifique se o firewall está definido corretamente.
* Verifique se sua rede virtual está configurada corretamente se sua fonte de dados ou destino é local:
  * Você pode verificar se o problema é da configuração de rede virtual Provisionando uma VM do Azure na mesma rede virtual. Em seguida, verifique se a fonte de dados ou o destino pode ser acessado da VM do Azure.
  * Você pode encontrar mais detalhes sobre como usar uma rede virtual com um tempo de execução de integração do SSIS em [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Mensagem de erro: "A origem do ADO NET falhou ao adquirir a conexão '... '" com "não foi possível criar um Gerenciador de conexões gerenciado".

A causa potencial é que o provedor ADO.NET usado no pacote não está instalado no Integration Runtime do SSIS. Você pode instalar o provedor usando uma instalação personalizada. Você pode encontrar mais detalhes sobre a instalação personalizada em [Personalizar a instalação para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Mensagem de erro: "A conexão '... ' não encontrado "

Um problema conhecido em versões mais antigas do SQL Server Management Studio (SSMS) pode causar este erro. Se o pacote contiver um componente personalizado (por exemplo, Pacote de Funcionalidades do Azure para SSIS ou componentes de parceiros) que não esteja instalado no computador onde o SSMS é utilizado para fazer a implementação, o SSMS vai remover o componente e provocar o erro. Atualize o [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para a versão mais recente que tem o problema corrigido.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Mensagem de erro: "código de saída do executor do SSIS:-1073741819."

* Causa potencial & ação recomendada:
  * Esse erro pode ser devido à limitação da origem e do destino do Excel quando várias fontes ou destinos do Excel estão sendo executados em paralelo em vários threads. Você pode solucionar essa limitação alterando os componentes do Excel para serem executados em sequência ou separá-los em pacotes diferentes e disparar por meio da "tarefa executar pacote" com a Propriedade ExecuteOutOfProcess definida como true.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Mensagem de erro: "Não há espaço suficiente no disco"

Esse erro significa que o disco local é usado no nó do Integration Runtime do SSIS. Verifique se o pacote ou a instalação personalizada está consumindo muito espaço em disco:
* Se o disco for consumido pelo pacote, ele será liberado depois que a execução do pacote for concluída.
* Se o disco for consumido pela instalação personalizada, você precisará parar o tempo de execução de integração do SSIS, modificar o script e iniciar o tempo de execução de integração novamente. Todo o contêiner de blob do Azure especificado para a instalação personalizada será copiado para o nó do tempo de execução de integração do SSIS; portanto, verifique se há algum conteúdo desnecessário sob esse contêiner.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Mensagem de erro: "Falha ao recuperar o recurso do mestre. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Código: 300004. Descrição: falha no carregamento do arquivo "* *". "

* Causa potencial & ação recomendada:
  * Se a atividade do SSIS estiver executando o pacote do sistema de arquivos (arquivo de pacote ou arquivo de projeto), esse erro ocorrerá se o projeto, pacote ou arquivo de configuração não estiver acessível com a credencial de acesso de pacote fornecida na atividade do SSIS
    * Se você estiver usando o arquivo do Azure:
      * O caminho do arquivo deve começar \\com o nome\>da conta\\de armazenamento. File.Core.Windows.NET\<caminho de compartilhamento de \\arquivos \<\>
      * O domínio deve ser "Azure"
      * O nome de usuário \<deve ser de conta de armazenamento\>
      * A senha deve ser \<a chave de acesso de armazenamento\>
    * Se você estiver usando um arquivo local, verifique se a VNet, a credencial de acesso ao pacote e a permissão estão configuradas corretamente para que o tempo de execução de integração do Azure-SSIS possa acessar seu compartilhamento de arquivos local

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Mensagem de erro: "O nome do arquivo '... ' especificado na conexão não era válido "

* Causa potencial & ação recomendada:
  * Um nome de arquivo inválido foi especificado
  * Verifique se você está usando o FQDN (nome de domínio totalmente qualificado) em vez de curto tempo no Gerenciador de conexões

### <a name="error-message-cannot-open-file-"></a>Mensagem de erro: "Não é possível abrir o arquivo '... '"

Esse erro ocorre quando a execução do pacote não consegue localizar um arquivo no disco local no tempo de execução de integração do SSIS. Tente estas ações:
* Não use o caminho absoluto no pacote que está sendo executado no Integration Runtime do SSIS. Use o diretório de trabalho de execução atual (.) ou a pasta temporária (% TEMP%) Stead.
* Se você precisar manter alguns arquivos em nós do tempo de execução de integração do SSIS, prepare os arquivos conforme descrito em [Personalizar configuração](how-to-configure-azure-ssis-ir-custom-setup.md). Todos os arquivos no diretório de trabalho serão limpos após a conclusão da execução.
* Use os arquivos do Azure em vez de armazenar o arquivo no nó do Integration Runtime do SSIS. Para obter detalhes, consulte usar compartilhamentos de [arquivos do Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Mensagem de erro: "O banco de dados ' SSISDB ' atingiu sua cota de tamanho"

Uma causa potencial pode ser quando a base de dados SSISDB criada na base de dados SQL do Azure, ou uma instância gerida quando está a criar um runtime de integração do SSIS, atingiu a sua quota. Tente estas ações:
* Considere aumentar a DTU da base de dados. Pode encontrar detalhes em [SQL Database resource limits for an Azure SQL Database server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) (Limites de recursos da Base de Dados SQL para um servidor da Base de Dados SQL do Azure).
* Verifique se o pacote iria gerar muitos registos. Se for o caso, pode configurar uma tarefa elástica para limpar esses registos. Para obter detalhes, veja [Clean up SSISDB logs with Azure Elastic Database jobs](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) (Limpar registos SSISDB com tarefas de Base de Dados Elástica do Azure)

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Mensagem de erro: "O limite de solicitação para o banco de dados é... e foi atingido ".

Se muitos pacotes estiverem sendo executados em paralelo no tempo de execução de integração do SSIS, esse erro poderá ocorrer porque o SSISDB atingiu seu limite de solicitação. Considere aumentar o DTC do SSISDB para resolver esse problema. Pode encontrar detalhes em [SQL Database resource limits for an Azure SQL Database server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) (Limites de recursos da Base de Dados SQL para um servidor da Base de Dados SQL do Azure).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Mensagem de erro: "Falha na operação do SSIS com status de operação inesperado:..."

O erro é causado principalmente por um problema transitório, portanto, tente executar novamente a execução do pacote. Aplique o padrão de repetição no pacote do SSIS seguindo estas etapas:

* Verifique se seus pacotes SSIS podem ser executados novamente em caso de falha sem efeitos colaterais (por exemplo, perda de dados ou duplicação de dados).
* Configure o **intervalo** de **repetição** e repetição da atividade **Executar Pacote SSIS** na guia **geral** . ![Definir propriedades na guia geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Para um componente de origem ou destino ADO.NET e OLE DB, defina **ConnectRetryCount** e **ConnectRetryInterval** no Gerenciador de conexões no pacote SSIS ou na atividade SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Mensagem de erro: "Não há um trabalho ativo."

Esse erro geralmente significa que o Integration Runtime do SSIS tem um status não íntegro. Verifique a portal do Azure do status e os erros detalhados. Para obter mais informações, consulte [tempo de execução de integração do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Mensagem de erro: "O Integration Runtime não pode ser atualizado e eventualmente deixará de funcionar, já que não podemos acessar o contêiner de blob do Azure fornecido para a instalação personalizada".

Esse erro ocorre quando o Integration Runtime do SSIS não pode acessar o armazenamento configurado para a instalação personalizada. Verifique se o URI de assinatura de acesso compartilhado (SAS) que você forneceu é válido e não expirou.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Mensagem de erro: "Provedor do Microsoft OLE DB para Analysis Services. Resultado Descrição de 0x80004005: ' Erro COM: Erro COM: mscorlib; A exceção foi gerada pelo destino de uma invocação "

Uma causa potencial é que o nome de usuário ou a senha com a autenticação multifator do Azure habilitada está configurado para Azure Analysis Services autenticação. Essa autenticação não tem suporte no Integration Runtime do SSIS. Tente usar uma entidade de serviço para Azure Analysis Services autenticação:
1. Prepare uma entidade de serviço conforme descrito em [automação com entidades de serviço](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. No Gerenciador de conexões, configure **usar um nome de usuário e senha específicos**: defina **AppID** como o username e **clientSecret** como a senha.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Mensagem de erro: "A origem do ADONET falhou ao adquirir a conexão {GUID} com a seguinte mensagem de erro: Falha de logon do usuário ' NT AUTHORITY\ANONYMOUS LOGON ' "ao usar uma identidade gerenciada

Certifique-se de não configurar o método de autenticação do Gerenciador de conexões como **Active Directory autenticação de senha** quando o parâmetro *ConnectUsingManagedIdentity* for **true**. Você pode configurá-lo como **autenticação SQL** , o que será ignorado se *ConnectUsingManagedIdentity* estiver definido.

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Várias execuções de pacote são disparadas inesperadamente

* Causa potencial & ação recomendada:
  * A atividade de procedimento armazenado do ADF é usada para disparar a execução do pacote SSIS. O comando t-SQL pode atingir um problema transitório e disparar a nova execução, o que causaria várias execuções de pacote.
  * Use a atividade ExecuteSSISPackage em vez disso, que garante que a execução do pacote não seja executada novamente, a menos que o usuário defina a contagem Os detalhes podem ser encontrados em[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

### <a name="package-execution-takes-too-long"></a>A execução do pacote leva muito tempo

Aqui estão as possíveis causas e as ações recomendadas:

* Muitas execuções de pacote foram agendadas no Integration Runtime do SSIS. Todas essas execuções estarão aguardando em uma fila por sua vez.
  * Determine o máximo usando esta fórmula:

    Contagem de execução paralela máxima por IR = contagem de nós * execução paralela máxima por nó
  * Para saber como definir a contagem de nós e a execução paralela máxima por nó, confira [criar um tempo de execução de integração do Azure-SSIS no Azure data Factory](create-azure-ssis-integration-runtime.md).
* O tempo de execução de integração do SSIS foi interrompido ou tem um status não íntegro. Para saber como verificar o status e os erros do tempo de execução de integração do SSIS, consulte [tempo de execução de integração do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Também recomendamos que você defina um tempo limite na guia **geral** : ![Defina propriedades na guia](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)geral.

### <a name="poor-performance-in-package-execution"></a>Desempenho insatisfatório na execução do pacote

Tente estas ações:

* Verifique se o Integration Runtime do SSIS está na mesma região que a fonte de dados e o destino.

* Defina o nível de log da execução do pacote como **desempenho** para coletar informações de duração de cada componente na execução. Para obter detalhes, consulte [log do Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Verifique o desempenho do nó IR no portal do Azure:
  * Para obter informações sobre como monitorar o tempo de execução de integração do SSIS, consulte [tempo de execução de integração do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Você pode encontrar o histórico de CPU/memória para o tempo de execução de integração do SSIS exibindo as métricas do data factory no portal do Azure.
    ![Monitorar métricas do Integration Runtime do SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
