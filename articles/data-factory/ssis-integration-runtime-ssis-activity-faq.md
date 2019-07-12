---
title: Resolver problemas de execução do pacote no runtime de integração de SSIS | Documentos da Microsoft
description: Este artigo fornece orientações de resolução de problemas para execução de pacotes do SSIS no integration runtime do SSIS
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
ms.openlocfilehash: 05723a90725992e6b955524a2d35c82d3378ee3d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621843"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Resolver problemas de execução do pacote no runtime de integração de SSIS

Este artigo inclui os erros mais comuns que poderão ser quando que estiver executando o runtime de integração de SSIS pacotes do SQL Server Integration Services (SSIS). Ele descreve as possíveis causas e ações para resolver os erros.

## <a name="where-to-find-logs-for-troubleshooting"></a>Onde encontrar registos para resolução de problemas

Utilize o portal do Azure Data Factory para verificar a saída da atividade de execução do pacote SSIS. A saída inclui o resultado da execução, mensagens de erro e ID de operação. Para obter detalhes, consulte [monitorizar o pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Utilize o catálogo SSIS (SSISDB) para verificar os registos de detalhes para a execução. Para obter detalhes, consulte [pacotes de execução do Monitor e outras operações](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Erros comuns, as causas e soluções

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Mensagem de erro: "O tempo limite de ligação foi excedido" ou "o serviço encontrou um erro ao processar o pedido. Tente novamente."

Aqui estão as causas em potencial e as ações recomendadas:
* A origem de dados ou de destino está sobrecarregado. Verifique a carga na sua origem de dados ou de destino e ver se ele tem capacidade suficiente. Por exemplo, se utilizou uma base de dados do Azure SQL, considere aumentar se a base de dados é suscetível de tempo limite.
* A rede entre o runtime de integração do SSIS e a origem de dados ou de destino é instável, especialmente quando a ligação está entre regiões ou entre no local e o Azure. Aplica o padrão de repetição no pacote do SSIS através dos seguintes passos:
  * Certifique-se de que seus pacotes do SSIS podem voltar a executar em caso de falha sem efeitos colaterais (por exemplo, a perda de dados ou a duplicação de dados).
  * Configurar **repita** e **intervalo entre tentativas** de **executar pacotes do SSIS** atividade no **geral** separador. ![Definir as propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Para um componente origem ou destino ADO.NET e OLE DB definido **ConnectRetryCount** e **ConnectRetryInterval** no Gerenciador de conexões no pacote do SSIS ou atividade SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Mensagem de erro: "ADO NET origem falhou ao obter a ligação"..."" com "Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação para o SQL Server. O servidor não foi encontrado ou não estava acessível."

Este problema, normalmente, significa que a origem de dados ou de destino não está acessível a partir do runtime de integração do SSIS. Os motivos pelos quais podem variar. Experimente estas ações:
* Certifique-se de que está passando a origem de dados ou o destino nome/IP corretamente.
* Certifique-se de que a firewall está definida corretamente.
* Certifique-se de que a rede virtual está configurada corretamente se a sua origem de dados ou de destino está no local:
  * Pode verificar se o problema é de configuração de rede virtual através do aprovisionamento de uma VM do Azure na mesma rede virtual. Em seguida, verifique se a origem de dados ou de destino pode ser acessado de VM do Azure.
  * Pode encontrar mais detalhes sobre como utilizar uma rede virtual com um runtime de integração de SSIS no [associar um runtime de integração Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Mensagem de erro: "ADO NET origem falhou ao obter a ligação"..."" com "Não foi possível criar uma conexão gerenciada manager."

A causa potencial é que o fornecedor ADO.NET utilizado no pacote não está instalado no tempo de execução de integração do SSIS. Pode instalar o fornecedor através de uma configuração personalizada. Pode encontrar mais detalhes sobre a configuração personalizada no [personalizar a configuração para o runtime de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Mensagem de erro: "A ligação"..." Não foi encontrado"

Um problema conhecido em versões mais antigas do SQL Server Management Studio (SSMS) pode causar este erro. Se o pacote contiver um componente personalizado (por exemplo, componentes de Feature Pack do SSIS do Azure ou de parceiro) não está instalado na máquina onde o SSMS é utilizado para efetuar a implementação, o SSMS remove o componente e fazer com que o erro. Atualize [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para a versão mais recente que tenha o problema foi corrigido.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Mensagem de erro: "código de saída do Executor do SSIS:-1073741819."

* Causa potencial & ação recomendada:
  * Este erro pode ser devido a limitação para a origem do Excel e de destino ao executar várias origens de Excel ou destinos em paralelo em vários threads. Pode resolver essa limitação ao alterar os componentes do Excel são executadas em seqüência ou separá-los em diferentes pacotes e acionador por meio de "Executar tarefa do pacote" com a propriedade de ExecuteOutOfProcess definida como True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Mensagem de erro: "Não existe espaço suficiente no disco"

Este erro significa que o nó do integration runtime do SSIS se esgotar o disco local. Verifique se o pacote ou a configuração personalizada está a consumir muito espaço em disco:
* Se o disco é consumido ao seu pacote, ele será liberado cópia de segurança depois de concluída a execução do pacote.
* Se o disco for consumido pela sua configuração personalizada, irá precisar para parar o runtime de integração do SSIS, modificar o seu script e iniciar o integration runtime novamente. O contentor de BLOBs totalmente do Azure especificada para a configuração personalizada será copiada para o nó do runtime de integração de SSIS, por isso, verifique se existe qualquer conteúdo desnecessário nesse contentor.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Mensagem de erro: "Falha ao obter o recurso do principal. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Código: 300004. Descrição: carregar ficheiro "***" falhou. "

* Causa potencial & ação recomendada:
  * Se a atividade de SSIS está em execução pacote do sistema de arquivos (ficheiro de pacote ou arquivo de projeto), este erro ocorrerá se o ficheiro de projeto, pacote ou de configuração não está acessível com a credencial de acesso do pacote fornecido na atividade SSIS
    * Se estiver a utilizar ficheiros do Azure:
      * O caminho do ficheiro deve começar com \\ \\ \<nome da conta de armazenamento\>. file.core.windows.net\\\<caminho da partilha de ficheiros\>
      * O domínio deve ser "Azure"
      * O nome de utilizador deve ser \<nome da conta de armazenamento\>
      * A palavra-passe deve ser \<chave de acesso de armazenamento\>
    * Se está a utilizar ficheiros no local, verifique se VNet, a credencial de acesso do pacote e a permissão estão configurados corretamente para que o runtime de integração Azure-SSIS pode aceder a partilha de ficheiros no local

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Mensagem de erro: "O nome de ficheiro"..." especificado na ligação não era válida "

* Causa potencial & ação recomendada:
  * For especificado um nome de ficheiro inválido
  * Certifique-se de que está a utilizar o FQDN (nome completamente qualificado domínio) em vez de curto período de tempo no seu Gerenciador de conexões

### <a name="error-message-cannot-open-file-"></a>Mensagem de erro: "Não é possível abrir o ficheiro"...""

Este erro ocorre quando a execução do pacote não é possível localizar um ficheiro no disco local em que o runtime de integração do SSIS. Experimente estas ações:
* Não utilize o caminho absoluto no pacote que está a ser executado no runtime de integração do SSIS. Utilizar o atual diretório de trabalho de execução (.) ou a pasta temporária (% TEMP %) em vez disso.
* Se precisar de manter alguns ficheiros em nós de runtime de integração do SSIS, preparar os arquivos conforme descrito em [personalizar a configuração](how-to-configure-azure-ssis-ir-custom-setup.md). Todos os ficheiros no diretório de trabalho serão limpos após a conclusão da execução.
* Utilize ficheiros do Azure em vez de armazenar o ficheiro no nó do runtime de integração do SSIS. Para obter detalhes, consulte [partilhas de ficheiros do Azure utilize](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Mensagem de erro: "A base de dados 'SSISDB' atingiu a quota de tamanho"

Uma causa potencial é que a base de dados do SSISDB criado na base de dados SQL do Azure ou uma instância gerida quando estiver a criar um runtime de integração do SSIS, atingiu a quota. Experimente estas ações:
* Considere aumentar a DTU da base de dados. Pode encontrar detalhes [limites de recursos de base de dados SQL para um servidor de base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Verifique se o seu pacote irá gerar muitos registos. Nesse caso, pode configurar uma tarefa elástica para limpar estes registos. Para obter detalhes, consulte [limpeza dos registos SSISDB com tarefas de bases de dados elásticas Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Mensagem de erro: "O limite de pedido para a base de dados é... e foi atingido."

Se estiver a executar muitos pacotes em paralelo no runtime de integração do SSIS, este erro pode acontecer porque o SSISDB atingiu o limite de pedido. Considere aumentar o DTC de SSISDB para resolver este problema. Pode encontrar detalhes [limites de recursos de base de dados SQL para um servidor de base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Mensagem de erro: "Falha na operação de SSIS com o estado de operação inesperado:..."

O erro é frequentemente causado por um problema transitório, por isso, tente executar novamente a execução do pacote. Aplica o padrão de repetição no pacote do SSIS através dos seguintes passos:

* Certifique-se de que seus pacotes do SSIS podem voltar a executar em caso de falha sem efeitos colaterais (por exemplo, a perda de dados ou a duplicação de dados).
* Configurar **repita** e **intervalo entre tentativas** de **executar pacotes do SSIS** atividade no **geral** separador. ![Definir as propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Para um componente origem ou destino ADO.NET e OLE DB definido **ConnectRetryCount** e **ConnectRetryInterval** no Gerenciador de conexões no pacote do SSIS ou atividade SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Mensagem de erro: "Não existe nenhuma função de trabalho ativa."

Este erro normalmente significa que o runtime de integração de SSIS tem um Estado de mau estado de funcionamento. Verifique o portal do Azure para o estado e os erros detalhados. Para obter mais informações, consulte [runtime de integração Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Mensagem de erro: "O runtime de integração não é possível atualizar e, eventualmente, deixará de funcionar, uma vez que não podemos acessar o contentor de Blobs do Azure fornecidas para configuração personalizada."

Este erro ocorre quando o runtime de integração de SSIS não é possível acessar o armazenamento configurado para a configuração personalizada. Verifique se a assinatura de acesso partilhado (SAS) URI que forneceu é válida e ainda não tiver expirado.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Mensagem de erro: "Fornecedor Microsoft OLE DB para Analysis Services. 'Hresult: 0x80004005 Description:' Erro COM: Erro COM: mscorlib; Exceção foi emitida pelo destino de uma invocação"

Uma causa potencial é que o nome de utilizador ou palavra-passe com o Azure multi-factor Authentication ativada está configurado para autenticação do Azure Analysis Services. Esta autenticação não é suportada no tempo de execução de integração do SSIS. Tente utilizar um principal de serviço para a autenticação do Azure Analysis Services:
1. Preparar um principal de serviço, conforme descrito em [automação com principais de serviço](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. No Gestor de ligação, configure **utilizar um nome de utilizador específico e a palavra-passe**: definir **AppID** como o nome de utilizador e **clientSecret** como a palavra-passe.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Mensagem de erro: "ADONET origem não conseguiu obter a ligação de {GUID} com a seguinte mensagem de erro: Falha de início de sessão do utilizador 'Início de sessão do NT AUTHORITY\ANONYMOUS' "ao utilizar uma identidade gerida

Certifique-se de não configurar o método de autenticação do Gerenciador de conexões como **autenticação de palavra-passe do Active Directory** quando o parâmetro *ConnectUsingManagedIdentity* é **verdadeiro** . Pode configurá-la **autenticação de SQL** em vez disso, que é ignorado se *ConnectUsingManagedIdentity* está definido.

### <a name="package-execution-takes-too-long"></a>Execução de pacotes leva muito tempo

Aqui estão as causas em potencial e as ações recomendadas:
* Demasiados execuções do pacote tiverem sido agendadas no SSIS integration runtime. Todas essas execuções estarão esperando, numa fila, por sua vez.
  * Determine o número máximo com esta fórmula: 
    
    Contagem de execução máximo paralelo por IR = contagem de nós * paralelo de máximo de execução por nó
  * Para saber como definir a contagem de nós e a execução paralela máxima por nó, veja [criar um runtime de integração Azure-SSIS no Azure Data Factory](create-azure-ssis-integration-runtime.md).
* O runtime de integração de SSIS está parado ou tem um Estado de mau estado de funcionamento. Para saber como verificar o estado de runtime de integração do SSIS e erros, veja [runtime de integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Também recomendamos que defina um tempo limite no **gerais** separador: ![Definir as propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png).

### <a name="poor-performance-in-package-execution"></a>Um mau desempenho na execução do pacote

Experimente estas ações:

* Certificar-se de que o runtime de integração de SSIS está na mesma região que a origem de dados e de destino.

* Definir o nível de registo de execução do pacote **desempenho** para recolher informações de duração de cada componente na execução. Para obter detalhes, consulte [registo de Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Verificar o desempenho de nó do Runtime de integração no portal do Azure:
  * Para obter informações sobre como monitorizar o runtime de integração do SSIS, veja [runtime de integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Pode encontrar o histórico de CPU/memória para o runtime de integração de SSIS ao visualizar as métricas do data factory no portal do Azure.
    ![Monitorizar as métricas do runtime de integração de SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
