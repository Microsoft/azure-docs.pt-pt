---
title: Auditoria Azure SQL para Azure SQL Database e Azure Synapse Analytics
description: Utilize a auditoria da Base de Dados Azure SQL para rastrear eventos de base de dados num registo de auditoria.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/03/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: 1c587705041b8fb3d37e02142fa10ae5a1d73f9f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173852"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Auditoria para Azure SQL Database e Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A auditoria ao [Azure SQL Database](sql-database-paas-overview.md) e [ao Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rastreia eventos de base de dados e escreve-os para um registo de auditoria na sua conta de armazenamento Azure, Log Analytics ou Centros de Eventos.

A auditoria também:

- Ajuda a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.

- Ativa e facilita o cumprimento das normas de conformidade, apesar de não garantir a conformidade. Para obter mais informações sobre os programas Azure que suportam o cumprimento das normas, consulte o [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde pode encontrar a lista mais atual de certificações de conformidade Azure SQL.

> [!NOTE]
> Para obter informações sobre a auditoria de instância gerida Azure SQL, consulte o seguinte artigo, [Inicie com a auditoria sql Managed Instance](../managed-instance/auditing-configure.md).

## <a name="overview"></a><a id="overview"></a>Descrição geral

Pode utilizar a auditoria da Base de Dados SQL para:

- **Mantenha** um rasto de auditoria de eventos selecionados. Pode definir categorias de ações de bases de dados para serem auditadas.
- **Relatório** sobre a atividade da base de dados. Pode utilizar relatórios pré-configurados e um dashboard para começar rapidamente a utilizar relatórios de eventos e de atividade.
- **Analise** relatórios. Pode encontrar eventos suspeitos, atividades invulgares e tendências.

> [!IMPORTANT]
> A auditoria para a Azure SQL Database e a Azure Synapse está otimizada para disponibilidade e desempenho. Durante uma atividade muito elevada, ou alta carga de rede, a Base de Dados Azure SQL e a Azure Synapse permitem que as operações prossigam e não possam registar alguns eventos auditados..

### <a name="auditing-limitations"></a>Limitações de auditoria

- **O armazenamento premium** não é suportado **atualmente.**
- **O espaço hierárquico** para **a conta de armazenamento de Azure Data Lake Gen2** **não** é suportado atualmente.
- Não é apoiado o financiamento da auditoria a um **Azure Synapse** pausado. Para ativar a auditoria, retome o Azure Synapse.
- A auditoria para **piscinas Azure Synapse SQL** suporta apenas grupos de ação de auditoria **predefinidos**.


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definir a política de auditoria ao nível do servidor vs. nível de base de dados

Uma política de auditoria pode ser definida para uma base de dados específica ou como uma política de [servidor](logical-servers.md) padrão em Azure (que acolhe base de dados SQL ou Azure Synapse):

- Uma política de servidor aplica-se a todas as bases de dados existentes e recém-criadas no servidor.

- Se *a auditoria do servidor estiver ativada,* *aplica-se sempre à base de dados*. A base de dados será auditada, independentemente das definições de auditoria da base de dados.

- Quando a política de auditoria for definida ao nível da base de dados para um espaço de trabalho Log Analytics ou um destino Desaquise, as seguintes operações não manterão a política de auditoria ao nível da base de dados de origem:
    - [Cópia da base de dados](database-copy.md)
    - [Restauro para um ponto anterior no tempo](recovery-using-backups.md)
    - [Geo-replicação](active-geo-replication-overview.md) (Base de dados secundária não terá auditoria ao nível da base de dados)

- Ativar a auditoria na base de dados, além de a permitir no servidor, *não* substitui nem altera nenhuma das definições da auditoria do servidor. Ambas as auditorias existirão lado a lado. Por outras palavras, a base de dados é auditada duas vezes em paralelo; uma vez pela política do servidor e uma vez pela política de base de dados.

   > [!NOTE]
   > Deve evitar permitir a auditoria do servidor e a auditoria de blob de base de dados em conjunto, a menos que:
    >
    > - Pretende utilizar uma *conta de armazenamento* diferente, *período de retenção* ou *log analytics workspace* para uma base de dados específica.
    > - Pretende auditar tipos ou categorias de eventos para uma base de dados específica que difere do resto das bases de dados do servidor. Por exemplo, pode ter inserções de mesa que precisam de ser auditadas apenas para uma base de dados específica.
   >
   > Caso contrário, recomendamos que permita apenas uma auditoria ao nível do servidor e deixe a auditoria ao nível da base de dados desativada para todas as bases de dados.

#### <a name="remarks"></a>Observações

- Os registos de auditoria são escritos para **Append Blobs** num armazenamento Azure Blob na sua subscrição Azure
- Os registos de auditoria estão em formato .xel e podem ser abertos utilizando [o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- Para configurar uma loja de registos imutável para os eventos de auditoria ao nível do servidor ou da base de dados, siga as [instruções fornecidas pelo Azure Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). Certifique-se de que selecionou **Deixe os apêndices adicionais** quando configurar o armazenamento de bolhas imutáveis.
- Pode escrever registos de auditoria numa conta de Armazenamento Azure por trás de um VNet ou firewall. Para obter instruções específicas, [escreva a auditoria para uma conta de armazenamento por trás do VNet e da firewall](audit-write-storage-account-behind-vnet-firewall.md).
- Para obter mais informações sobre o formato de registo, hierarquia da pasta de armazenamento e convenções de nomeação, consulte a [Referência do Formato do Registo de Auditoria blob](./audit-log-format.md).
- A auditoria em [Réplicas Só de Leitura](read-scale-out.md) é ativada automaticamente. Para mais detalhes sobre a hierarquia das pastas de armazenamento, convenções de nomeação e formato de registo, consulte o Formato de [Registo de Auditoria de Base de Dados SQL](audit-log-format.md).
- Ao utilizar a autenticação AD AD Azure, os registos de logins falhados *não* aparecerão no registo de auditoria SQL. Para visualizar registos de auditoria de login falhados, é necessário visitar o [portal Azure Ative Directory](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), que regista detalhes destes eventos.
- Os inícios de sessão são encaminhados pelo gateway para a instância específica onde a base de dados está localizada.  No caso dos inícios de sessão do AAD, as credenciais são verificadas antes da tentativa de utilizar esse utilizador para iniciar sessão na base de dados pedida.  Em caso de falha, a base de dados pedida nunca é acedida, pelo que não ocorre qualquer auditoria.  No caso dos logins SQL, as credenciais são verificadas nos dados solicitados, pelo que neste caso podem ser auditadas.  Os logins realizados com êxito, que, obviamente, alcançam a base de dados, são auditados em ambos os casos.
- Depois de configurar as definições de auditoria, pode ligar a nova funcionalidade de deteção de ameaças e configurar e-mails para receber alertas de segurança. Quando utiliza a deteção de ameaças, recebe alertas proactivos sobre atividades anómalas de bases de dados que podem indicar potenciais ameaças à segurança. Para obter mais informações, consulte [Começar com a deteção de ameaças.](threat-detection-overview.md)

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Configurar a auditoria para o seu servidor

A política de auditoria por defeito inclui todas as ações e o seguinte conjunto de grupos de ação, que auditarão todas as consultas e procedimentos armazenados executados na base de dados, bem como logins bem sucedidos e falhados:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
Pode configurar a auditoria para diferentes tipos de ações e grupos de ação utilizando o PowerShell, conforme descrito na auditoria da Manage SQL Database utilizando a secção [Azure PowerShell.](#manage-auditing)

A Azure SQL Database e a Azure Synapse Audit armazenam 4000 caracteres de dados para campos de caracteres num registo de auditoria. Quando a **declaração** ou os valores **data_sensitivity_information** devolvidos de uma ação auditável contenham mais de 4000 caracteres, quaisquer dados para além dos primeiros 4000 caracteres serão **truncados e não auditados.**
A secção seguinte descreve a configuração da auditoria utilizando o portal Azure.

  > [!NOTE]
  > - Não é possível permitir a auditoria a uma piscina SQL dedicada a uma pausa. Para permitir a auditoria, desempaco fique desmesumos na piscina dedicada SQL. Saiba mais sobre [a piscina SQL dedicada.](../..//synapse-analytics/sql/best-practices-sql-pool.md)
  > - Quando a auditoria é configurada para um espaço de trabalho Log Analytics ou para um destino Even Hub através do portal Azure ou cmdlet PowerShell, é criada uma [Definição de Diagnóstico](../../azure-monitor/essentials/diagnostic-settings.md) com a categoria "SQLSecurityAuditEvents" ativada.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Navegue para **a Auditoria** sob o título de Segurança na sua base **de dados SQL** ou painel **de servidor SQL.**
3. Se preferir configurar uma política de auditoria do servidor, pode selecionar o link de definições do servidor Ver na página de auditoria da base de **dados.** Pode então visualizar ou modificar as definições de auditoria do servidor. As políticas de auditoria do servidor aplicam-se a todas as bases de dados existentes e recentemente criadas neste servidor.

    ![Screenshot que mostra o link de definições do servidor Ver realçado na página de auditoria da base de dados.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Se preferir ativar a auditoria ao nível da base de dados, **altere a Auditoria** para **ON**. Se a auditoria do servidor estiver ativada, a auditoria configurada pela base de dados existirá lado a lado com a auditoria do servidor.

5. Tem várias opções para configurar onde os registos de auditoria serão escritos. Pode escrever registos numa conta de armazenamento Azure, num espaço de trabalho Log Analytics para consumo por registos Azure Monitor ou para o centro de eventos para consumo utilizando o centro de eventos. Pode configurar qualquer combinação destas opções, e os registos de auditoria serão escritos a cada um.
  
   ![opções de armazenamento](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Auditoria das operações de Suporte do Microsoft

A auditoria das operações de Suporte do Microsoft para o Azure SQL Server permite-lhe auditar as operações dos engenheiros de suporte da Microsoft quando precisam de aceder ao seu servidor durante um pedido de suporte. A utilização desta capacidade, juntamente com a sua auditoria, permite uma maior transparência na sua força de trabalho e permite a deteção de anomalias, visualização de tendências e prevenção de perda de dados.

Para permitir a auditoria das operações de suporte do Microsoft, navegue para **a Auditoria** sob a rubrica De Segurança no seu painel de **servidor Azure SQL** e **altere a auditoria das operações de suporte da Microsoft** para **ON**.

  > [!IMPORTANT]
  > A auditoria das operações de suporte da Microsoft não suporta o destino da conta de armazenamento. Para ativar a capacidade, um espaço de trabalho log Analytics ou um destino Event Hub tem de ser configurado.

![Screenshot das operações de suporte da Microsoft](./media/auditing-overview/support-operations.png)

Para rever os registos de auditoria das operações de Suporte do Microsoft no seu espaço de trabalho Log Analytics, utilize a seguinte consulta:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Auditoria ao destino de armazenamento

Para configurar registos de auditoria de escrita numa conta de armazenamento, selecione **Storage** e abra **detalhes de Armazenamento**. Selecione a conta de armazenamento Azure onde os registos serão guardados e, em seguida, selecione o período de retenção. Em seguida, clique em **OK**. Os registos mais antigos do que o período de retenção são eliminados.

- O valor predefinido para o período de retenção é 0 (retenção ilimitada). Pode alterar este valor movendo o slider **de Retenção (Dias)** nas **definições de Armazenamento** ao configurar a conta de armazenamento para auditoria.
  - Se alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, note que a retenção só se aplicará aos registos escritos após a alteração do valor da retenção (os registos escritos durante o período em que a retenção foi definida para ilimitada são preservados, mesmo após a retenção ser ativada).

  ![conta de armazenamento](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Auditoria ao destino Log Analytics
  
Para configurar registos de auditoria de escrita num espaço de trabalho do Log Analytics, selecione **Log Analytics** e abra detalhes do **Log Analytics**. Selecione ou crie o espaço de trabalho Log Analytics onde os registos serão escritos e, em seguida, clique em **OK**.

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Para obter mais detalhes sobre o espaço de trabalho do Azure Monitor Log Analytics, consulte [a implementação de Registos do Monitor Azure](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Auditoria ao destino Event Hub

Para configurar registos de auditoria de escrita para um centro de eventos, selecione **Event Hub** e abra detalhes do **Event Hub**. Selecione o centro de eventos onde os registos serão escritos e, em seguida, clique em **OK**. Certifique-se de que o centro de eventos está na mesma região que a sua base de dados e servidor.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analisar registos e relatórios de auditoria

Se optar por escrever registos de auditoria para os registos do Azure Monitor:

- Utilize o [portal do Azure](https://portal.azure.com). Abra a base de dados relevante. No topo da página de **Auditoria** da base de **dados,** selecione Ver registos de auditoria .

    ![ver registos de auditoria](./media/auditing-overview/auditing-view-audit-logs.png)

- Então, tem duas maneiras de ver os registos:

    Clicar no **Log Analytics** no topo da página de **registos** de auditoria abrirá a vista 'Logs' no espaço de trabalho Log Analytics, onde pode personalizar o intervalo de tempo e a consulta de pesquisa.

    ![aberto no espaço de trabalho Log Analytics](./media/auditing-overview/auditing-log-analytics.png)

    Clicar no painel de instrumentos de **visualização** no topo da página de **registos** de auditoria abrirá um painel de instrumentos que exibe informações de registos de auditoria, onde pode perfurar em Informações de Segurança, Acesso a Dados Sensíveis e muito mais. Este dashboard foi concebido para o ajudar a obter informações de segurança para os seus dados.
    Também pode personalizar o intervalo de tempo e consulta de pesquisa.
    ![Ver painel de analítica de registo](media/auditing-overview/auditing-view-dashboard.png)

    ![Painel de registo analytics](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Insights de Segurança de Log Analytics](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Em alternativa, também pode aceder aos registos de auditoria da lâmina Log Analytics. Abra o seu espaço de trabalho Log Analytics e na secção **Geral,** clique em **Registars**. Pode começar com uma consulta simples, como: *pesquisar "SQLSecurityAuditEvents"* para ver os registos de auditoria.
    A partir daqui, também pode utilizar [registos do Azure Monitor](../../azure-monitor/logs/log-query-overview.md)  para executar pesquisas avançadas nos dados do seu registo de auditoria. Os registos do Azure Monitor dão-lhe informações operacionais em tempo real utilizando painéis de pesquisa integrados e dashboards personalizados para analisar facilmente milhões de registos em todas as suas cargas de trabalho e servidores. Para obter informações úteis adicionais sobre o Azure Monitor regista linguagem e comandos de pesquisa, consulte a referência de pesquisa de registos do [Azure Monitor](../../azure-monitor/logs/log-query-overview.md).

Se optar por escrever registos de auditoria ao Event Hub:

- Para consumir dados de registos de auditoria do Event Hub, terá de configurar um fluxo para consumir eventos e escrevê-los para um alvo. Para mais informações, consulte [a Documentação dos Hubs de Eventos Azure](../index.yml).
- Os registos de auditoria no Event Hub são capturados no corpo de eventos [Apache Avro](https://avro.apache.org/) e armazenados usando a formatação JSON com codificação UTF-8. Para ler os registos de auditoria, pode utilizar as [Ferramentas Avro](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas semelhantes que processem este formato.

Se optar por escrever registos de auditoria numa conta de armazenamento Azure, existem vários métodos que pode utilizar para visualizar os registos:

- Os registos de auditoria são agregados na conta que escolheu durante a configuração. Pode explorar registos de auditoria utilizando uma ferramenta como [o Azure Storage Explorer.](https://storageexplorer.com/) No armazenamento Azure, os registos de auditoria são guardados como uma coleção de ficheiros blob dentro de um recipiente chamado **sqldbauditlogs**. Para mais detalhes sobre a hierarquia das pastas de armazenamento, convenções de nomeação e formato de registo, consulte o Formato de [Registo de Auditoria de Base de Dados SQL](./audit-log-format.md).

- Utilize o [portal do Azure](https://portal.azure.com).  Abra a base de dados relevante. No topo da página de **Auditoria** da base de **dados,** clique em Ver registos de auditoria .

    ![Screenshot que mostra o botão de registos de auditoria Do View realçado na página de auditoria da base de dados.](./media/auditing-overview/7_auditing_get_started_blob_view_audit_logs.png)

    **Os registos** de auditoria abrem, a partir dos quais poderá ver os registos.

  - Pode ver datas específicas clicando em **Filtro** no topo da página de registos de **auditoria.**
  - Pode alternar entre os registos de auditoria que foram criados pela *política de auditoria* do servidor e a política de auditoria da base de *dados* através da toggling **Audit Source**.
  - Só pode ver os registos de auditoria relacionados com a injeção DE SQL verificando apenas os registos de auditoria do Show para a caixa de verificação **de injeções SQL.**

       ![Screenshot que mostra as opções para visualizar os registos de auditoria.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Utilize a função do sistema **sys.fn_get_audit_file** (T-SQL) para devolver os dados de registo de auditoria em formato tabular. Para obter mais informações sobre a utilização desta função, consulte [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Use **ficheiros de auditoria de fusão** no SQL Server Management Studio (a começar pelo SSMS 17):
    1. A partir do menu SSMS, selecione **Ficheiros** de Auditoria de Fusão  >  **Aberta**  >  **de Ficheiros.**

        ![Screenshot que mostra a opção do menu De Ficheiros de Auditoria de Fusão.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Abre a caixa de diálogo **'Add Audit Files'.** Selecione uma das opções **Add** para escolher se funde ficheiros de auditoria a partir de um disco local ou os importe do Azure Storage. É-lhe exigido que forneça os seus dados de Armazenamento Azure e a chave da conta.

    3. Depois de todos os ficheiros a fundir terem sido adicionados, clique **em OK** para concluir a operação de fusão.

    4. O ficheiro fundido abre em SSMS, onde pode vê-lo e analisá-lo, bem como exportá-lo para um ficheiro XEL ou CSV, ou para uma mesa.

- Use Power BI. Pode visualizar e analisar dados de registo de auditoria no Power BI. Para obter mais informações e aceder a um modelo transferível, consulte [os dados de registo de auditoria da análise no Power BI](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895).
- Descarregue ficheiros de registo do seu recipiente de blob de armazenamento Azure através do portal ou utilizando uma ferramenta como [o Azure Storage Explorer](https://storageexplorer.com/).
  - Depois de ter descarregado um ficheiro de registo localmente, clique duas vezes no ficheiro para abrir, visualizar e analisar os registos em SSMS.
  - Também pode descarregar vários ficheiros simultaneamente através do Azure Storage Explorer. Para tal, clique com o botão direito de uma sub-dobradeira específica e **selecione Guardar como** guardar numa pasta local.

- Métodos adicionais:

  - Depois de descarregar vários ficheiros ou uma sub-dobragem que contenha ficheiros de registo, pode fundi-los localmente como descrito nas instruções de Ficheiros de Auditoria de Fusão SSMS descritos anteriormente.
  - Ver registos de auditoria de blob programáticamente: [Consulta ficheiros de eventos estendidos](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) utilizando o PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Práticas de produção


### <a name="auditing-geo-replicated-databases"></a>Auditoria de bases de dados geo-replicadas

Com bases de dados geo-replicadas, quando permitir a auditoria na base de dados primária, a base de dados secundária terá uma política de auditoria idêntica. Também é possível configurar a auditoria na base de dados secundária, permitindo a auditoria no **servidor secundário**, independentemente da base de dados primária.

- Nível do servidor **(recomendado**): Ligue a auditoria tanto no **servidor primário** como no **servidor secundário** - as bases de dados primárias e secundárias serão auditadas independentemente com base na respetiva política de nível do servidor.
- Nível de base de dados: A auditoria ao nível da base de dados para bases de dados secundárias só pode ser configurada a partir das definições de auditoria da base de dados primária.
  - A auditoria deve ser ativada na *própria base de dados primária*, e não no servidor.
  - Após a auditoria ser ativada na base de dados primária, também será ativada na base de dados secundária.

    > [!IMPORTANT]
    > Com a auditoria ao nível da base de dados, as definições de armazenamento da base de dados secundária serão idênticas às da base de dados primária, causando tráfego trans-regional. Recomendamos que permita apenas uma auditoria ao nível do servidor e deixe a auditoria ao nível da base de dados desativada para todas as bases de dados.

### <a name="storage-key-regeneration"></a>Regeneração da chave de armazenamento

Na produção, é provável que refresque as chaves de armazenamento periodicamente. Ao escrever registos de auditoria para o armazenamento do Azure, tem de ressatar a sua política de auditoria ao refrescar as suas chaves. O processo é o seguinte:

1. Detalhes **de armazenamento abertos**. Na caixa chave de acesso ao **armazenamento,** selecione **Secondary**, e clique **em OK**. Em seguida, clique em **Guardar** no topo da página de configuração de auditoria.

    ![Screenshot que mostra o processo de seleção de uma chave de acesso de armazenamento secundário.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Vá à página de configuração de armazenamento e regenera a chave de acesso primário.

    ![Painel de navegação](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Volte para a página de configuração de auditoria, altere a chave de acesso ao armazenamento de secundário para primário e, em seguida, clique **em OK**. Em seguida, clique em **Guardar** no topo da página de configuração de auditoria.
4. Volte para a página de configuração de armazenamento e regenerar a chave de acesso secundário (em preparação para o ciclo de atualização da próxima chave).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Gerir a auditoria da Base de Dados Azure SQL

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

**Cmdlets PowerShell (incluindo suporte à cláusula WHERE para filtragem adicional)**:

- [Criar ou atualizar a política de auditoria da base de dados (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Obtenha a Política de Auditoria da Base de Dados (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Obtenha a política de auditoria do servidor (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Remover a política de auditoria da base de dados (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Remover a política de auditoria do servidor (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Para um exemplo de script, consulte [a auditoria de Configuração e a deteção de ameaças utilizando o PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

### <a name="using-rest-api"></a>Com a API REST

**REST API:**

- [Criar ou atualizar política de auditoria de base de dados](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Criar ou atualizar a política de auditoria do servidor](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obtenha a política de auditoria da base de dados](/rest/api/sql/database%20auditing%20settings/get)
- [Obter política de auditoria de servidores](/rest/api/sql/server%20auditing%20settings/get)

Política alargada com suporte à cláusula WHERE para filtragem adicional:

- [Criar ou atualizar política de auditoria *alargada* da base de dados](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Criar ou atualizar a política de auditoria *estendida* do servidor](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obtenha a política de auditoria *alargada* da base de dados](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Obtenha a política de auditoria *alargada* do servidor](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

- [Gerir a política de auditoria de um servidor](/cli/azure/sql/server/audit-policy)
- [Gerir a política de auditoria de uma base de dados](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Utilizar modelos do Azure Resource Manager

Pode gerir a auditoria da Base de Dados Azure SQL utilizando modelos [do Gestor de Recursos Azure,](../../azure-resource-manager/management/overview.md) como mostra estes exemplos:

- [Implementar uma Base de Dados Azure SQL com Auditoria habilitada a escrever registos de auditoria para a conta de armazenamento Azure Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implementar uma Base de Dados Azure SQL com Auditoria habilitada a escrever registos de auditoria para Registar Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implementar uma Base de Dados Azure SQL com Auditoria habilitada a escrever registos de auditoria para Os Centros de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> As amostras ligadas encontram-se num repositório público externo e são fornecidas "como está", sem garantia, e não são suportadas ao abrigo de qualquer programa/serviço de suporte da Microsoft.
