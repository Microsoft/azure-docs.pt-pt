---
title: Introdução à auditoria
description: Use a auditoria do banco de dados SQL do Azure para acompanhar eventos de banco de dados em um log de auditoria.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 0266c08052cd3db7d9081d6b617da94703b9ab77
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896324"
---
# <a name="get-started-with-sql-database-auditing"></a>Introdução à auditoria da base de dados SQL

A auditoria do [banco de dados SQL](sql-database-technical-overview.md) do azure e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure, log Analytics espaço de trabalho ou hubs de eventos. A auditoria também:

- Ajuda a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.

- Ativa e facilita o cumprimento das normas de conformidade, apesar de não garantir a conformidade. Para obter mais informações sobre os programas do Azure que dão suporte à conformidade com padrões, consulte a [central de confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.


> [!NOTE] 
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Visão geral da auditoria do banco de dados SQL do Azure

Pode utilizar a auditoria da base de dados SQL para:

- **Manter** uma trilha de auditoria de eventos selecionados. Pode definir categorias de ações de bases de dados para serem auditadas.
- **Relatório** de atividade do banco de dados. Pode utilizar relatórios pré-configurados e um dashboard para começar rapidamente a utilizar relatórios de eventos e de atividade.
- **Analise** os relatórios. Pode encontrar eventos suspeitos, atividades invulgares e tendências.

> [!IMPORTANT]
> Os logs de auditoria são gravados em **blobs de acréscimo** no armazenamento de BLOBs do Azure em sua assinatura do Azure.
>
> - Há suporte para todos os tipos de armazenamento (v1, v2, BLOB).
> - Há suporte para todas as configurações de replicação de armazenamento.
> - Atualmente, **não há suporte para**o **armazenamento Premium** .
> - Atualmente, **não há suporte para** **armazenamento na VNet** .
> - No momento, **não há suporte para** **o armazenamento por trás de um firewall** .
> - O **namespace hierárquico** para **Azure data Lake Storage Gen2 conta** de armazenamento **não tem suporte**no momento.

## <a id="subheading-8"></a>Definir política de auditoria no nível de servidor versus de banco de dados

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política de servidor padrão:

- Uma política de servidor se aplica a todos os bancos de dados existentes e recém-criados no servidor.

- Se a *auditoria de blob do servidor estiver habilitada*, ela *sempre se aplicará ao banco de dados*. O banco de dados será auditado, independentemente das configurações de auditoria do banco de dados.

- Habilitar a auditoria de blob no banco de dados ou data warehouse, além de habilitá-lo no servidor, *não substitui nem* altera as configurações da auditoria de blob do servidor. Ambas as auditorias existirão lado a lado. Em outras palavras, o banco de dados é auditado duas vezes em paralelo; uma vez pela política de servidor e uma vez pela política de banco de dados.

   > [!NOTE]
   > Você deve evitar habilitar a auditoria de blob de servidor e a auditoria de blob de banco de dados, a menos que:
    > - Você deseja usar uma conta de *armazenamento* ou um *período de retenção* diferente para um banco de dados específico.
    > - Você deseja auditar tipos de eventos ou categorias para um banco de dados específico que difere do restante dos bancos de dados no servidor. Por exemplo, você pode ter inserções de tabela que precisam ser auditadas apenas para um banco de dados específico.
   >
   > Caso contrário, recomendamos que você habilite apenas a auditoria de blob no nível do servidor e deixe a auditoria no nível do banco de dados desabilitada para todos os bancos.

## <a id="subheading-2"></a>Configurar a auditoria para seu banco de dados

A seção a seguir descreve a configuração de auditoria usando o portal do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Navegue até **auditoria** no título segurança no painel banco de dados SQL/servidor.

    <a id="auditing-screenshot"></a>![painel de navegação][1]

3. Se preferir configurar uma política de auditoria de servidor, você poderá selecionar o link **exibir configurações do servidor** na página de auditoria do banco de dados. Em seguida, você pode exibir ou modificar as configurações de auditoria do servidor. As políticas de auditoria de servidor se aplicam a todos os bancos de dados existentes e recém-criados neste servidor.

    ![Painel de navegação][2]

4. Se você preferir habilitar a auditoria no nível do banco de dados, alterne a **auditoria** para **ativado**.

    Se a auditoria do servidor estiver habilitada, a auditoria configurada pelo banco de dados existirá lado a lado com a auditoria do servidor.

    ![Painel de navegação][3]

5. **Novo** -agora você tem várias opções para configurar onde os logs de auditoria serão gravados. Você pode gravar logs em uma conta de armazenamento do Azure, em um espaço de trabalho Log Analytics para consumo por Azure Monitor logs ou no Hub de eventos para consumo usando o Hub de eventos. Você pode configurar qualquer combinação dessas opções e os logs de auditoria serão gravados em cada um.
  
  > [!NOTE]
   >O cliente que deseja configurar um repositório de logs imutável para seus eventos de auditoria no nível do servidor ou banco de dados deve seguir as [instruções fornecidas pelo armazenamento do Azure] (https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)
  
  > [!WARNING]
   > Habilitar a auditoria para Log Analytics incorrerá em custo com base nas taxas de ingestão. Esteja ciente do custo associado com o uso dessa [opção](https://azure.microsoft.com/pricing/details/monitor/)ou considere armazenar os logs de auditoria em uma conta de armazenamento do Azure.

    ![opções de armazenamento](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Para configurar a gravação de logs de auditoria em uma conta de armazenamento, selecione **armazenamento** e abra **detalhes do armazenamento**. Selecione a conta de armazenamento do Azure na qual os logs serão salvos e, em seguida, selecione o período de retenção. Os logs antigos serão excluídos. Em seguida, clique em **OK**.

   > [!IMPORTANT]
   > - O valor padrão do período de retenção é 0 (retenção ilimitada). Você pode alterar esse valor movendo o controle deslizante de **retenção (dias)** em **configurações de armazenamento** ao configurar a conta de armazenamento para auditoria.
   > - Se você alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, observe que a retenção será aplicada somente aos logs gravados após a alteração do valor de retenção (logs gravados durante o período em que a retenção foi definida como ilimitada são preservados, mesmo após a retenção está habilitada)

    ![conta de armazenamento](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Para configurar a gravação de logs de auditoria em um espaço de trabalho Log Analytics, selecione **log Analytics (versão prévia)** e abra **log Analytics detalhes**. Selecione ou crie o espaço de trabalho Log Analytics em que os logs serão gravados e clique em **OK**.

    ![Área de trabalho do Log Analytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Para configurar a gravação de logs de auditoria para um hub de eventos, selecione **Hub de eventos (versão prévia)** e abra **detalhes do hub de eventos**. Selecione o Hub de eventos em que os logs serão gravados e clique em **OK**. Certifique-se de que o Hub de eventos esteja na mesma região que o seu banco de dados e servidor.

    ![Hub de eventos](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Clique em **Guardar**.
10. Se você quiser personalizar os eventos auditados, poderá fazer isso por meio de [cmdlets do PowerShell](#subheading-7) ou da [API REST](#subheading-9).
11. Depois de definir as configurações de auditoria, você pode ativar o novo recurso de detecção de ameaças e configurar os emails para receber alertas de segurança. Ao usar a detecção de ameaças, você recebe alertas proativos sobre atividades anormais de banco de dados que podem indicar possíveis ameaças à segurança. Para obter mais informações, consulte [introdução à detecção de ameaças](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> Não é possível habilitar a auditoria em um SQL Data Warehouse do Azure em pausa. Para habilitá-lo, cancele a pausa do data warehouse.

> [!WARNING]
> Habilitar a auditoria em um servidor que tenha um SQL Data Warehouse do Azure sobre ele **resultará na data warehouse retomada e pausada novamente** , o que pode incorrer em encargos de cobrança.

## <a id="subheading-3"></a>Analisar logs e relatórios de auditoria

Se você optar por gravar logs de auditoria em logs de Azure Monitor:

- Utilize o [portal do Azure](https://portal.azure.com).  Abra o banco de dados relevante. Na parte superior da página de **auditoria** do banco de dados, clique em **Exibir logs de auditoria**.

    ![Exibir logs de auditoria](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Em seguida, você tem duas maneiras de exibir os logs:
    
    Clicar em **log Analytics** na parte superior da página de **registros de auditoria** abrirá o modo de exibição logs no espaço de trabalho log Analytics, no qual você pode personalizar o intervalo de tempo e a consulta de pesquisa.
    
    ![abrir no espaço de trabalho Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Clicar em **exibir painel** na parte superior da página de **registros de auditoria** abrirá um painel exibindo informações de logs de auditoria, nos quais você pode fazer uma busca detalhada nos insights de segurança, acessar dados confidenciais e muito mais. Este painel foi projetado para ajudá-lo a obter informações de segurança para seus dados.
    Você também pode personalizar o intervalo de tempo e a consulta de pesquisa. 
    ![Exibir Log Analytics painel](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Painel do Log Analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics informações de segurança](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Como alternativa, você também pode acessar os logs de auditoria na folha Log Analytics. Abra o espaço de trabalho Log Analytics e, na seção **geral** , clique em **logs**. Você pode começar com uma consulta simples, como: *pesquise "SQLSecurityAuditEvents"* para exibir os logs de auditoria.
    A partir daqui, você também pode usar [logs de Azure monitor](../log-analytics/log-analytics-log-search.md) para executar pesquisas avançadas em seus dados de log de auditoria. Os logs de Azure Monitor fornecem informações operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar rapidamente milhões de registros em todas as suas cargas de trabalho e servidores. Para obter informações úteis adicionais sobre os comandos e o idioma de pesquisa de logs do Azure Monitor, consulte [referência de pesquisa de Azure monitor logs](../log-analytics/log-analytics-log-search.md).

Se você optar por gravar logs de auditoria no Hub de eventos:

- Para consumir dados de logs de auditoria do hub de eventos, você precisará configurar um fluxo para consumir eventos e gravá-los em um destino. Para obter mais informações, consulte a [documentação dos hubs de eventos do Azure](../event-hubs/index.yml).
- Os logs de auditoria no Hub de eventos são capturados no corpo de eventos do [Apache Avro](https://avro.apache.org/) e armazenados usando a formatação JSON com codificação UTF-8. Para ler os logs de auditoria, você pode usar [Ferramentas do Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas semelhantes que processam esse formato.

Se você optar por gravar logs de auditoria em uma conta de armazenamento do Azure, há vários métodos que podem ser usados para exibir os logs:

> [!NOTE] 
> A auditoria em [réplicas somente leitura](sql-database-read-scale-out.md) é habilitada automaticamente. Para obter mais detalhes sobre a hierarquia das pastas de armazenamento, convenções de nomenclatura e formato de log, consulte o [formato de log de auditoria do banco de dados SQL](sql-database-audit-log-format.md). 

- Os logs de auditoria são agregados na conta que você escolheu durante a instalação. Você pode explorar os logs de auditoria usando uma ferramenta como o [Gerenciador de armazenamento do Azure](https://storageexplorer.com/). No armazenamento do Azure, os logs de auditoria são salvos como uma coleção de arquivos de blob dentro de um contêiner chamado **sqldbauditlogs**. Para obter mais detalhes sobre a hierarquia das pastas de armazenamento, convenções de nomenclatura e formato de log, consulte o [formato de log de auditoria do banco de dados SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Utilize o [portal do Azure](https://portal.azure.com).  Abra o banco de dados relevante. Na parte superior da página de **auditoria** do banco de dados, clique em **Exibir logs de auditoria**.

    ![Painel de navegação][7]

    Os **registros de auditoria** são abertos, no qual você poderá exibir os logs.

  - Você pode exibir datas específicas clicando em **Filtrar** na parte superior da página de **registros de auditoria** .
  - Você pode alternar entre os registros de auditoria que foram criados pela *política de auditoria de servidor* e a política de auditoria de banco de *dados* alternando a origem da **auditoria**.
  - Você pode exibir somente os registros de auditoria relacionados à injeção de SQL marcando a caixa **de seleção Mostrar somente registros de auditoria para injeção de SQL** .

       ![Painel de navegação][8]

- Use a função de sistema **Sys. fn_get_audit_file** (T-SQL) para retornar os dados do log de auditoria no formato de tabela. Para obter mais informações sobre como usar essa função, consulte [Sys. fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Use **mesclar arquivos de auditoria** no SQL Server Management Studio (a partir do SSMS 17):
    1. No menu do SSMS, selecione **arquivo** > **abrir** > **mesclar arquivos de auditoria**.

        ![Painel de navegação][9]
    2. A caixa de diálogo **Adicionar arquivos de auditoria** é aberta. Selecione uma das opções de **adição** para escolher se deseja mesclar os arquivos de auditoria de um disco local ou importá-los do armazenamento do Azure. Você deve fornecer os detalhes do armazenamento do Azure e a chave de conta.

    3. Depois que todos os arquivos a serem mesclados tiverem sido adicionados, clique em **OK** para concluir a operação de mesclagem.

    4. O arquivo mesclado é aberto no SSMS, no qual você pode exibi-lo e analisá-lo, bem como exportá-lo para um arquivo XEL ou CSV ou para uma tabela.

- Use Power BI. Você pode exibir e analisar dados de log de auditoria em Power BI. Para obter mais informações e acessar um modelo para download, consulte [analisar dados de log de auditoria em Power bi](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Baixe arquivos de log do seu contêiner de blob do armazenamento do Azure por meio do portal ou usando uma ferramenta como o [Gerenciador de armazenamento do Azure](https://storageexplorer.com/).
  - Depois de ter baixado um arquivo de log localmente, clique duas vezes no arquivo para abrir, exibir e analisar os logs no SSMS.
  - Você também pode baixar vários arquivos simultaneamente por meio de Gerenciador de Armazenamento do Azure. Para fazer isso, clique com o botão direito do mouse em uma subpasta específica e selecione **salvar como** para salvar em uma pasta local.

- Métodos adicionais:

  - Depois de baixar vários arquivos ou uma subpasta que contém arquivos de log, você pode mesclá-los localmente conforme descrito nas instruções de arquivos de auditoria de mesclagem do SSMS descritas anteriormente.
  - Exibir logs de auditoria de blob programaticamente:

    - [Consulte arquivos de eventos estendidos](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) usando o PowerShell.

## <a id="subheading-5"></a>Práticas de produção

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditando bancos de dados replicados geograficamente</a>

Com bancos de dados replicados geograficamente, quando você habilita a auditoria no banco de dados primário, o banco de dados secundário terá uma política de auditoria idêntica. Também é possível configurar a auditoria no banco de dados secundário habilitando a auditoria no **servidor secundário**, independentemente do banco de dados primário.

- Nível de servidor (**recomendado**): ativar a auditoria no **servidor primário** , bem como no **servidor secundário** – os bancos de dados primário e secundário serão auditados independentemente com base em sua respectiva política de nível de servidor.
- Nível de banco de dados: a auditoria no nível de banco de dados para bancos de dados secundários só pode ser definida a partir das configurações de auditoria do banco de dados primário.
  - A auditoria deve ser habilitada no *próprio banco de dados primário*, não no servidor.
  - Após a habilitação da auditoria no banco de dados primário, ela também será habilitada no banco de dados secundário.

    >[!IMPORTANT]
    >Com a auditoria no nível do banco de dados, as configurações de armazenamento para o banco de dados secundário serão idênticas às do banco de dados primário, causando o tráfego entre regiões. É recomendável que você habilite apenas a auditoria no nível do servidor e deixe a auditoria no nível do banco de dados desabilitada para todos os bancos.

### <a id="subheading-6">Regeneração da chave de armazenamento</a>

Em produção, é provável que você atualize suas chaves de armazenamento periodicamente. Ao gravar logs de auditoria no armazenamento do Azure, você precisa salvar novamente sua política de auditoria ao atualizar suas chaves. O processo é o seguinte:

1. Abra os **detalhes do armazenamento**. Na caixa **chave de acesso de armazenamento** , selecione **secundário**e clique em **OK**. Em seguida, clique em **salvar** na parte superior da página de configuração de auditoria.

    ![Painel de navegação][5]
2. Vá para a página de configuração de armazenamento e regenere a chave de acesso primária.

    ![Painel de navegação][6]
3. Volte para a página de configuração de auditoria, alterne a chave de acesso de armazenamento de secundária para primária e clique em **OK**. Em seguida, clique em **salvar** na parte superior da página de configuração de auditoria.
4. Volte para a página de configuração de armazenamento e regenere a chave de acesso secundária (em preparação para o ciclo de atualização da próxima chave).

## <a name="additional-information"></a>Informação Adicional

- Para obter detalhes sobre o formato de log, hierarquia da pasta de armazenamento e convenções de nomenclatura, consulte a [referência de formato de log de auditoria de blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > O Azure SQL Database Audit armazena 4000 caracteres de dados para campos de caractere em um registro de auditoria. Quando a **instrução** ou os valores de **data_sensitivity_information** retornados de uma ação auditável contiverem mais de 4000 caracteres, todos os dados que ultrapassarem os primeiros 4000 caracteres serão **truncados e não auditados**.

- Os logs de auditoria são gravados em **blobs de acréscimo** em um armazenamento de BLOBs do Azure em sua assinatura do Azure:
  - No momento, **não há suporte para** o **armazenamento Premium** em blobs de acréscimo.
  - Atualmente, **não há suporte para** **armazenamento na VNet** .

- A política de auditoria padrão inclui todas as ações e o conjunto de grupos de ações a seguir, que auditará todas as consultas e procedimentos armazenados executados no banco de dados, bem como logons com falha e com êxito:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Você pode configurar a auditoria para diferentes tipos de ações e grupos de ação usando o PowerShell, conforme descrito na seção [gerenciar a auditoria do banco de dados SQL usando Azure PowerShell](#subheading-7) .

- Ao usar a autenticação do AAD, os registros de logons com falha *não* serão exibidos no log de auditoria do SQL. Para exibir os registros de auditoria de logon com falha, você precisa visitar o [portal de Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), que registra em log os detalhes desses eventos.

- A auditoria do banco de dados SQL do Azure é otimizada para disponibilidade & desempenho. Durante uma atividade muito alta, o banco de dados SQL do Azure permite que as operações continuem e não registrem alguns eventos auditados.


## <a id="subheading-7"></a>Gerenciar a auditoria de banco de dados e SQL Server do Azure usando Azure PowerShell

**Cmdlets do PowerShell (incluindo suporte a cláusula WHERE para filtragem adicional)** :

- [Criar ou atualizar a política de auditoria do banco de dados (Set-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Obter política de auditoria de banco de dados (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Obter política de auditoria de servidor (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Remover política de auditoria de banco de dados (remove-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Remover política de auditoria de servidor (remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

Para obter um exemplo de script, consulte [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-8"></a>Gerenciar a auditoria de banco de dados e SQL Server do Azure usando a API REST

**API REST**:

- [Criar ou atualizar política de auditoria de banco de dados](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Criar ou atualizar política de auditoria de servidor](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter política de auditoria de banco de dados](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Obter política de auditoria de servidor](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Política estendida com suporte de cláusula WHERE para filtragem adicional:

- [Criar ou atualizar a política de auditoria *estendida* do banco de dados](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Criar ou atualizar a política de auditoria *estendida* do servidor](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter política de auditoria *estendida* do banco de dados](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Obter política de auditoria *estendida* do servidor](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-9"></a>Gerenciar a auditoria de banco de dados e SQL Server do Azure usando modelos de Azure Resource Manager

Você pode gerenciar a auditoria do banco de dados SQL do Azure usando modelos de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) , conforme mostrado nestes exemplos:

- [Implantar um SQL Server do Azure com auditoria habilitada para gravar logs de auditoria na conta de armazenamento de BLOBs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implante um SQL Server do Azure com auditoria habilitada para gravar logs de auditoria em Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implantar um SQL Server do Azure com auditoria habilitada para gravar logs de auditoria em hubs de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Os exemplos vinculados estão em um repositório público externo e são fornecidos ' no estado em que se encontram ', sem garantia e não têm suporte em nenhum programa/serviço de suporte da Microsoft.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage Azure SQL Server and Database auditing using Azure PowerShell]: #subheading-7
[Manage SQL database auditing using REST API]: #subheading-8
[Manage Azure SQL Server and Database auditing using ARM templates]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png
