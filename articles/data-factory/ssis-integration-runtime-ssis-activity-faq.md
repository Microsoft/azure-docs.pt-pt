---
title: Resolução de problemas de execução de pacotes do SSIS integration Runtime | Documentos da Microsoft
description: Este artigo fornece a orientação de resolução de problemas para execução de pacotes do SSIS no Integration Runtime do SSIS
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
ms.openlocfilehash: 7789970b47f0e55adee5bbe9da9f303aee6cdb25
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190128"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>Resolução de problemas de execução de pacotes do SSIS integration Runtime

Este artigo inclui os erros mais comuns que pode usar quando o SSIS em execução de pacotes no Runtime de integração de SSIS, o possíveis causas e ações para resolver os erros.

## <a name="where-can-i-find-logs-for-troubleshoot"></a>Onde posso encontrar registos para resolver problemas

* O Portal do ADF pode ser utilizado para verificar a saída da atividade de execução de pacotes de SSIS incluindo o resultado da execução, mensagens de erro e ID de operação. Detalhes podem ser encontrados em [monitorizar o pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* O catálogo de SSIS (SSISDB) pode ser usado para verificar os registos de detalhes para a execução. Detalhes podem ser encontrados em [pacotes em execução do Monitor e outras operações](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>Erros comuns, as causas e soluções

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Mensagem de erro: `"Connection Timeout Expired."` ou `"The service has encountered an error processing your request. Please try again."`

* Causa potencial & ação recomendada:
  * O dados de origem/destino está sobrecarregado. Verifique a carga nos seus dados de origem/destino e ver se ele tem capacidade suficiente. Por exemplo, se for utilizado o SQL do Azure, é recomendável considerar o dimensionamento cópia de segurança se a base de dados é suscetível de tempo limite.
  * A rede entre o Runtime de integração do SSIS e o destino/origem de dados é instável, especialmente quando a ligação está entre regiões ou entre no local e o azure. Recomenda-se para aplicar o padrão de repetição no pacote do SSIS ao seguir os passos:
    * Certifique-se de que seus pacotes do SSIS pode voltar a executar em caso de falha sem efeito colateral (por exemplo. perda de dados, dup. de dados...)
    * Configurar o **repita** e **intervalo entre tentativas** de executar atividades de pacote do SSIS no separador Geral ![definir as propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Para o componente do ADO.NET e OLEDB origem/destino, ConnectRetryCount e ConnectRetryInterval podem ser definidas no Gerenciador de conexões no pacote do SSIS ou atividade SSIS

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Mensagem de erro: `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* Causa potencial & ação recomendada:
  * Este problema, normalmente, significa que os dados de origem/destino está acessível a partir do Runtime de integração de SSIS, o que pode ser causado por diferentes motivos:
    * Certifique-se de que está passando o dados de origem/destino nome/IP corretamente
    * Certifique-se de que a firewall está definida corretamente
    * Certifique-se de que a vNet está configurada corretamente se seus dados de origem/destino estão no local.
      * Pode verificar se o problema é de configuração de vNet através do aprovisionamento de uma VM do Azure na mesma vNet. E, em seguida, verifique se os dados de origem/destino pode ser acessado da VM do Azure
      * Pode encontrar mais detalhes sobre como utilizar a vNet com o Runtime de integração de SSIS no [associar um runtime de integração Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>Mensagem de erro: "`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`"

* Causa potencial & ação recomendada:
  * O fornecedor ADO.NET utilizado no pacote não está instalado no Runtime de integração do SSIS. Pode instalar o fornecedor utilizando o programa de configuração personalizado. Podem encontrar mais detalhes sobre a configuração personalizada no [personalizar a configuração para o runtime de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Mensagem de erro: "`The connection '...' is not found`"

* Causa potencial & ação recomendada:
  * Este erro pode acontecer porque um problema conhecido no antiga versão do SSMS. Se o pacote contiver um componente personalizado (por exemplo, o Feature Pack do SSIS do Azure ou o 3º componentes de terceiros) que não está instalado na máquina onde o SSMS é utilizado para efetuar a implementação, o componente será removido até o SSMS e fazer com que o erro. Atualize [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para a versão mais recente que tenha o problema foi corrigido.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Mensagem de erro: "Não existe espaço suficiente no disco"

* Causa potencial & ação recomendada:
  * Este erro significa que o disco local é utilizado a cópia de segurança no nó do Runtime de integração do SSIS. Verifique se o seu pacote ou a configuração personalizada consumiria muitos espaços de disco.
    * Se o disco é consumido ao seu pacote, ele será liberado cópia de segurança depois de concluída a execução do pacote.
    * Se o disco for consumido pela sua configuração personalizada, terá de parar o Runtime de integração do SSIS, modificar o seu Script e iniciar novamente o Runtime de integração do SSIS. O contentor de Blobs do Azure inteira que especificou para a configuração personalizada sejam copiadas para o nó do Runtime de integração do SSIS, pelo que deve verificar se há qualquer conteúdo desnecessário nesse contentor.

### <a name="error-message-cannot-open-file-"></a>Mensagem de erro: "Não é possível abrir o ficheiro"...""

* Causa potencial & ação recomendada:
  * Este erro ocorre quando a execução do pacote não é possível localizar o ficheiro no disco local no Integration Runtime do SSIS.
    * Não é recomendável utilizar o caminho absoluto no pacote em execução no Runtime de integração do SSIS. Utilize o atual diretório de trabalho de execução (.) ou a pasta temporária (% TEMP %) em vez disso.
    * Se for necessária para manter alguns ficheiros em nós do Runtime de integração do SSIS, recomenda-se para preparar os ficheiros através de [personalizar a configuração](how-to-configure-azure-ssis-ir-custom-setup.md). Todos os ficheiros no diretório de trabalho de execução serão limpos após a conclusão da execução.
    * Outra opção consiste em utilizar os ficheiros do Azure em vez de armazenar o ficheiro no nó do Runtime de integração do SSIS. Mais detalhes podem ser encontrados em [ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Mensagem de erro: "A base de dados 'SSISDB' atingiu a quota de tamanho"

* Causa potencial & ação recomendada:
  * O SSISDB criado no SQL do Azure ou na instância gerida, ao criar o Runtime de integração de SSIS atingiu a quota.
    * Considere aumentar a DTU da base de dados para resolver este problema. Detalhes podem ser encontrados em [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * Verifique se o seu pacote irá gerar muitos registos. Se assim for, a tarefa elástica pode ser configurada para limpar estes registos. Consulte a [limpeza dos registos SSISDB tarefas de base de dados elásticas Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) para obter detalhes.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Mensagem de erro: "O limite de pedido para a base de dados é... e foi atingido."

* Causa potencial & ação recomendada:
  * Se muitos pacotes estão em execução em paralelo no Runtime de integração do SSIS, este erro pode ocorrer porque a limitação de pedido de SSISDB for atingida. Considere aumentar o DTC de sua SSISDB para resolver este problema. Detalhes podem ser encontrados em [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Mensagem de erro: "Falha na operação de SSIS com o estado de operação inesperado:..."

* Causa potencial & ação recomendada:
  * O erro é frequentemente causado um erro transitório, por isso, tente executar novamente a execução do pacote. Recomenda-se para aplicar o padrão de repetição no pacote do SSIS ao seguir os passos:
    * Certifique-se de que seus pacotes do SSIS pode voltar a executar em caso de falha sem efeito colateral (por exemplo, perda de dados, dup. de dados...)
    * Configurar o **repita** e **intervalo entre tentativas** de executar atividades de pacote do SSIS no separador Geral ![definir as propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Para o componente do ADO.NET e OLEDB origem/destino, ConnectRetryCount e ConnectRetryInterval podem ser definidas no Gerenciador de conexões no pacote do SSIS ou atividade SSIS

### <a name="error-message-there-is-no-active-worker"></a>Mensagem de erro: "Não existe nenhuma função de trabalho ativa."

* Causa potencial & ação recomendada:
  * Este erro normalmente significa que o Runtime de integração de SSIS está num Estado de mau estado de funcionamento. Verifique o portal do Azure para os erros de estado e os detalhes: [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Mensagem de erro: "O runtime de integração não é possível atualizar e, eventualmente, deixará de funcionar, uma vez que não podemos acessar o contentor de Blobs do Azure fornecidas para configuração personalizada."

* Este erro ocorre quando o Runtime de integração de SSIS não é possível acessar o armazenamento configurado para a configuração personalizada. Verifique se o Uri de SAS que forneceu é válido e ainda não tiver expirado.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Mensagem de erro: "Fornecedor Microsoft OLE DB para Analysis Services. 'Hresult: 0x80004005 Description:' Erro COM: Erro COM: mscorlib; Exceção foi emitida pelo destino de uma invocação"

* Causa potencial & ação recomendada:
  * Uma causa potencial é esse nome de utilizador/palavra-passe com a MFA ativada está configurada para autenticação do Azure Analysis Services, o que ainda não é suportada no runtime de integração do SSIS. Tente utilizar o Principal de serviço para a autenticação do serviço de análise do Azure:
    1. Preparar o principal de serviço para AAS [https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
    2. No Gestor de ligação, configure "Utilizar um nome de utilizador específico e uma palavra-passe": definir "AppID" como nome de utilizador e "clientSecret" como palavra-passe

### <a name="package-takes-unexpected-long-time-to-execute"></a>Pacote usa inesperado muito tempo a executar

* Causa potencial & ação recomendada:
  * Demasiados execuções do pacote tiverem sido agendadas no SSIS Integration Runtime. Neste caso, todas essas execuções estarão esperando numa fila sua vez executar.
    * Contagem de execução máximo paralelo por IR = contagem de nós * paralelo de máximo de execução por nó
    * Consulte a [criar Runtime de integração Azure-SSIS no Azure Data Factory](create-azure-ssis-integration-runtime.md) sobre como definir a contagem de nó e a execução paralela de máxima por nó.
  * Runtime de integração de SSIS está parado ou num Estado de mau estado de funcionamento. Verifique [runtime de integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime) para saber como verificar o estado de Runtime de integração do SSIS e erros.
  * Recomenda-se para definir o tempo limite, se tiver a certeza, que a execução do pacote deve ser concluída dentro de um determinado período de tempo: ![Definir as propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>Um mau desempenho na execução do pacote

* Causa potencial & ação recomendada:

  * Verifique se o Runtime de integração de SSIS está na mesma região que a origem de dados e de destino.

  * Ativar o nível de registo "Desempenho"

      Pode definir o nível de registo de execução do pacote "Desempenho" para recolher informações de duração mais detalhadas para cada componente na execução. Detalhes podem ser encontrados em: [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * Verificar o desempenho de nó do Runtime de integração na página de monitor de Runtime de integração no portal do Azure.
    * Como monitorizar o Runtime de integração de SSIS: [Runtime de integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * O histórico de utilização da CPU/memória do Runtime de integração de SSIS está disponível nas métricas do Data Factory no portal do Azure ![monitorizar as métricas do Runtime de integração de SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
