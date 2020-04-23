---
title: Auditoria de SQL do Azure
description: Utilize a auditoria da base de dados Azure SQL para rastrear os eventos da base de dados num registo de auditoria.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 48cdbc8188604ce1992a1cb15289576ba92902a3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086152"
---
# <a name="azure-sql-auditing"></a>Auditoria de SQL do Azure

A auditoria da Azure [SQL Database](sql-database-technical-overview.md) e [da Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rastreia eventos de base de dados e escreve-os num registo de auditoria na sua conta de armazenamento Azure, log Analytics espaço de trabalho ou Hubs de Eventos. 

A auditoria também:

- Ajuda a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.

- Ativa e facilita o cumprimento das normas de conformidade, apesar de não garantir a conformidade. Para obter mais informações sobre os programas Azure que suportam a conformidade das normas, consulte o [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde pode encontrar a lista mais atual das certificações de conformidade da Base de Dados SQL.

> [!NOTE] 
> Este tópico aplica-se tanto às bases de dados Azure SQL e Azure Synapse Analytics. Para a simplicidade, a Base de Dados SQL é usada quando se refere tanto à Base de Dados Azure SQL como à Azure Synapse Analytics.

## <a name="overview"></a><a id="overview"></a>Descrição geral

Pode utilizar a auditoria da base de dados SQL para:

- **Mantenha** um rasto de auditoria de eventos selecionados. Pode definir categorias de ações de bases de dados para serem auditadas.
- **Relatório** sobre a atividade da base de dados. Pode utilizar relatórios pré-configurados e um dashboard para começar rapidamente a utilizar relatórios de eventos e de atividade.
- **Analise** relatórios. Pode encontrar eventos suspeitos, atividades invulgares e tendências.

> [!IMPORTANT]
> - A auditoria da Base de Dados Azure SQL está otimizada para disponibilidade & desempenho. Durante a atividade muito elevada, a Base de Dados Azure SQL permite que as operações prossigam e não possam registar alguns eventos auditados.

#### <a name="auditing-limitations"></a>Limitações de auditoria

- **Atualmente,** o armazenamento premium não é **suportado.**
- **O espaço hierárquico** para a conta de armazenamento do **Lago Azure Gen2** não é **suportado**atualmente.
- Não é suportada a auditoria a um Armazém de **Dados Azure SQL** pausado. Para permitir a auditoria, retome o Data Warehouse.

## <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definir política de auditoria ao nível do servidor vs. base de dados

Uma política de auditoria pode ser definida para uma base de dados específica ou como uma política de servidor padrão:

- Uma política de servidor aplica-se a todas as bases de dados existentes e recém-criadas no servidor.

- Se a auditoria do *servidor blob estiver ativada,* *aplica-se sempre à base*de dados . A base de dados será auditada, independentemente das definições de auditoria da base de dados.

- Permitir a auditoria blob na base de dados ou no armazém de dados, além de o permitir no servidor, *não* sobrepor-se ou alterar nenhuma das definições da auditoria blob do servidor. Ambas as auditorias existirão lado a lado. Por outras palavras, a base de dados é auditada duas vezes em paralelo; uma vez pela política do servidor e uma vez pela política de base de dados.

   > [!NOTE]
   > Deve evitar permitir a auditoria de blob do servidor e a auditoria da bolha de bases de dados em conjunto, a menos que:
    > - Pretende utilizar uma *conta*de armazenamento diferente, período de *retenção* ou *log analytics workspace* para uma base de dados específica.
    > - Deseja auditar tipos ou categorias de eventos para uma base de dados específica que difere das restantes bases de dados do servidor. Por exemplo, pode ter inserções de mesa que precisam de ser auditadas apenas para uma base de dados específica.
   >
   > Caso contrário, recomendamos que apenas ative a auditoria blob ao nível do servidor e deixe a auditoria ao nível da base de dados desativada para todas as bases de dados.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Configurar auditorias para o seu servidor

A política de auditoria por defeito inclui todas as ações e o seguinte conjunto de grupos de ação, que auditarão todas as consultas e procedimentos armazenados executados na base de dados, bem como logins bem sucedidos e falhados:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
Pode configurar a auditoria para diferentes tipos de ações e grupos de ação utilizando o PowerShell, conforme descrito na auditoria da base de dados Manage SQL utilizando a secção [Azure PowerShell.](#manage-auditing)

A Auditoria de Base de Dados Azure SQL armazena 4000 caracteres de dados para campos de caracteres num registo de auditoria. Quando a **declaração** ou os valores **data_sensitivity_information** devolvidos de uma ação auditável contiverem mais de 4000 caracteres, quaisquer dados para além dos primeiros 4000 caracteres serão **truncados e não auditados.**
A secção seguinte descreve a configuração da auditoria utilizando o portal Azure.

1. Vá ao [portal Azure.](https://portal.azure.com)
2. Navegue para **Auditoria** sob a rubrica Segurança na sua base de dados/painel de servidor SQL.
3. Se preferir configurar uma política de auditoria do servidor, pode selecionar o link de definições do **servidor 'Ver'** na página de auditoria da base de dados. Em seguida, pode visualizar ou modificar as definições de auditoria do servidor. As políticas de auditoria do servidor aplicam-se a todas as bases de dados existentes e recém-criadas neste servidor.

    ![Painel de navegação](./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png)

4. Se preferir ativar a auditoria ao nível da base de dados, mude **a Auditoria** para **ON**. Se a auditoria do servidor estiver ativada, a auditoria configurada pela base de dados existirá lado a lado com a auditoria do servidor.

5. Tem várias opções para configurar onde serão escritos registos de auditoria. Pode escrever registos numa conta de armazenamento Azure, num espaço de trabalho de Log Analytics para consumo por registos do Azure Monitor (pré-visualização), ou para o centro de eventos para consumo utilizando o hub de eventos (pré-visualização). Pode configurar qualquer combinação destas opções, e os registos de auditoria serão escritos a cada um.
  
   ![opções de armazenamento](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Auditoria ao destino de armazenamento

Para configurar registos de auditoria de escrita numa conta de armazenamento, selecione **detalhes de Armazenamento** e **armazenamento**aberto. Selecione a conta de armazenamento Azure onde os registos serão guardados e, em seguida, selecione o período de retenção. Em seguida, clique em **OK**. Os registos mais antigos do que o período de retenção são eliminados.

- O valor predefinido para o período de retenção é 0 (retenção ilimitada). Pode alterar este valor movendo o slider **de Retenção (Dias)** nas **definições** de Armazenamento ao configurar a conta de armazenamento para auditoria.
  - Se alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, por favor note que a retenção só se aplicará aos registos escritos após a alteração do valor de retenção (os registos escritos durante o período em que a retenção foi definida para ilimitado são preservados, mesmo após a retenção ser ativada).

  ![conta de armazenamento](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="remarks"></a>Observações

- Os registos de auditoria são escritos para **Append Blobs** num armazenamento Azure Blob na sua subscrição Azure
- Para configurar uma loja de registoiveível imutável para os eventos de auditoria ao nível do servidor ou da base de dados siga as [instruções fornecidas pelo Armazenamento Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (certifique-se de que selecionou **Permitir apêndices adicionais** quando configurar o armazenamento de bolhas imutáveis).
- Pode escrever registos de auditoria numa conta de Armazenamento Azure atrás de uma VNet ou firewall. Para obter instruções específicas consulte, Escreva auditoria a uma conta de [armazenamento atrás de VNet e firewall](create-auditing-storage-account-vnet-firewall.md).
- Depois de configurar as definições de auditoria, pode ligar a nova funcionalidade de deteção de ameaças e configurar e-mails para receber alertas de segurança. Quando utiliza a deteção de ameaças, recebe alertas pró-ativos sobre atividades anómalas na base de dados que podem indicar potenciais ameaças à segurança. Para mais informações, consulte [Começar com a deteção de ameaças.](sql-database-threat-detection-get-started.md)
- Para mais detalhes sobre o formato de registo, a hierarquia da pasta de armazenamento e as convenções de nomeação, consulte a Referência do Formato de Registo de [Auditoria Blob](https://go.microsoft.com/fwlink/?linkid=829599).
- Ao utilizar a Autenticação AAD, os registos de logins falhados *não* aparecerão no registo de auditoria sQL. Para ver registos de auditoria de login falhados, é necessário visitar o [portal azure Ative Diretório,]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)que regista detalhes destes eventos.
- A auditoria às [réplicas apenas](sql-database-read-scale-out.md) de leitura está ativada automaticamente. Para mais detalhes sobre a hierarquia das pastas de armazenamento, nomeação de convenções e formato de registo, consulte o Formato de Registo de Auditoria da Base de [Dados SQL](sql-database-audit-log-format.md). 

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Auditoria ao destino Log Analytics
  
Para configurar os registos de auditoria de escrita para um espaço de trabalho de Log Analytics, selecione **Log Analytics (Pré-visualização)** e abra **detalhes do Log Analytics**. Selecione ou crie o espaço de trabalho do Log Analytics onde os registos serão escritos e, em seguida, clique em **OK**.
   
   ![LogAnalyticsworkspace](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Auditoria ao destino Event Hub

> [!WARNING]
> Permitir a auditoria num servidor que tenha um pool SQL nele **resulta no reinício do pool SQL e novamente interrompido,** o que pode incorrer em taxas de faturação.
> Não é possível permitir a auditoria a um pool SQL pausado. Para o ativar, desfaça a piscina SQL.

Para configurar registos de auditoria de escrita para um centro de eventos, selecione **Event Hub (Pré-visualização)** e abra detalhes do **Event Hub**. Selecione o centro de eventos onde os registos serão escritos e, em seguida, clique EM **OK**. Certifique-se de que o centro de eventos está na mesma região que a sua base de dados e servidor.

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analisar registos e relatórios de auditoria

Se optou por escrever registos de auditoria nos registos do Monitor Azure:

- Utilize o [portal Azure.](https://portal.azure.com)  Abra a base de dados relevante. No topo da página de **Auditoria** da base de dados, clique em **Ver registos**de auditoria .

    ![ver registos de auditoria](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Depois, tem duas formas de ver os registos:
    
    Clicar no **Log Analytics** no topo da página de **registos** da Auditoria abrirá a vista De Registos no espaço de trabalho do Log Analytics, onde pode personalizar o intervalo de tempo e a consulta de pesquisa.
    
    ![aberto no espaço de trabalho Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Clicar no painel de **instrumentos de visualização** na parte superior da página de **registos** da Auditoria abrirá um dashboard exibindo informações de registos de auditoria, onde poderá perfurar insights de segurança, acesso a dados sensíveis e muito mais. Este dashboard foi concebido para o ajudar a obter informações de segurança para os seus dados.
    Também pode personalizar o intervalo de tempo e pesquisa. 
    ![Ver painel de análise de log analytics](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Painel de análise de log analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics Security Insights](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Em alternativa, também pode aceder aos registos de auditoria da lâmina Log Analytics. Abra o seu espaço de trabalho Log Analytics e sob secção **geral,** clique em **Registos**. Pode começar com uma consulta simples, como: *pesquisar "SQLSecurityAuditEvents"* para ver os registos de auditoria.
    A partir daqui, também pode utilizar registos do [Monitor Azure](../log-analytics/log-analytics-log-search.md) para executar pesquisas avançadas nos seus dados de registo de auditoria. Os registos do Azure Monitor dão-lhe insights operacionais em tempo real utilizando instrumentos de pesquisa integrados e personalizados para analisar facilmente milhões de registos em todas as suas cargas de trabalho e servidores. Para obter informações adicionais úteis sobre o idioma de pesquisa de registos do Monitor Azure e comandos, consulte a referência de pesquisa de [registos do Monitor Azure](../log-analytics/log-analytics-log-search.md).

Se optou por escrever registos de auditoria ao Event Hub:

- Para consumir dados de registos de auditoria do Event Hub, terá de configurar um fluxo para consumir eventos e escrevê-los para um alvo. Para mais informações, consulte a [Documentação do Azure Event Hubs](../event-hubs/index.yml).
- Os registos de auditoria no Event Hub são capturados no corpo de eventos [Apache Avro](https://avro.apache.org/) e armazenados usando formatação JSON com codificação UTF-8. Para ler os registos de auditoria, pode utilizar [ferramentas Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas semelhantes que processem este formato.

Se optou por escrever registos de auditoria numa conta de armazenamento Azure, existem vários métodos que pode utilizar para visualizar os registos:

- Os registos de auditoria são agregados na conta que escolheu durante a configuração. Pode explorar registos de auditoria utilizando uma ferramenta como o [Azure Storage Explorer](https://storageexplorer.com/). No armazenamento do Azure, os registos de auditoria são guardados como uma coleção de ficheiros blob dentro de um recipiente chamado **sqldbauditlogs**. Para mais detalhes sobre a hierarquia das pastas de armazenamento, nomeação de convenções e formato de registo, consulte o Formato de Registo de Auditoria da Base de [Dados SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Utilize o [portal Azure.](https://portal.azure.com)  Abra a base de dados relevante. No topo da página de **Auditoria** da base de dados, clique em **Ver registos**de auditoria .

    ![Painel de navegação](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

    **Os registos** de auditoria abrem,a partir do qual poderá ver os registos.

  - Pode visualizar datas específicas clicando em **Filtro** no topo da página de registos da **Auditoria.**
  - Pode alternar entre os registos de auditoria que foram criados pela política de auditoria do *servidor* e a política de auditoria da base de *dados,* toggling **Audit Source**.
  - Só pode ver registos de auditoria relacionados com injeção SQL verificando **apenas registos de auditoria para a caixa de verificação de injeções SQL.**

       ![Painel de navegação]( ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png)

- Utilize a função do sistema **sys.fn_get_audit_file** (T-SQL) para devolver os dados de registo de auditoria em formato tabular. Para obter mais informações sobre a utilização desta função, consulte [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Utilize **ficheiros de auditoria** de fusão no Estúdio de Gestão de Servidores SQL (começando com SSMS 17):
    1. No menu SSMS, selecione **Ficheiros** > de Auditoria de Fusão**Abertas** > de**Ficheiros**.

        ![Painel de navegação](./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png)
    2. A caixa de diálogo **Add Audit Files** abre. Selecione uma das opções **Add** para escolher se deve fundir ficheiros de auditoria a partir de um disco local ou importá-los a partir do Armazenamento Azure. É-lhe exigido que forneça os seus dados de Armazenamento Azure e a chave da conta.

    3. Depois de todos os ficheiros a fundir terem sido adicionados, clique em **OK** para completar a operação de fusão.

    4. O ficheiro fundido abre no SSMS, onde pode vê-lo e analisá-lo, bem como exportá-lo para um ficheiro XEL ou CSV, ou para uma tabela.

- Use Power BI. Pode visualizar e analisar dados de registo de auditoria no Power BI. Para mais informações e para aceder a um modelo descarregado, consulte analisar dados de registo de [auditoria no Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Descarregue ficheiros de registo do seu recipiente de blob Azure Storage através do portal ou utilizando uma ferramenta como [o Azure Storage Explorer](https://storageexplorer.com/).
  - Depois de ter descarregado um ficheiro de registo localmente, clique duas vezes no ficheiro para abrir, visualizar e analisar os registos em SSMS.
  - Também pode descarregar vários ficheiros simultaneamente através do Azure Storage Explorer. Para tal, clique numa subpasta específica e selecione **Guardar de modo** a guardar numa pasta local.

- Métodos adicionais:

  - Depois de descarregar vários ficheiros ou uma subpasta que contenha ficheiros de registo, pode fundi-los localmente, conforme descrito nas instruções de Ficheiros de Auditoria ssms merge descritos anteriormente.
  - Ver registos de auditoria blob programáticamente:

    - [Consulta Ficheiros de Eventos Estendidos](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) utilizando powerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Práticas de produção

<!--The description in this section refers to preceding screen captures.-->

### <a name="auditing-geo-replicated-databases"></a>Auditoria de bases de dados geo-replicadas

Com bases de dados geo-replicadas, quando permitir a auditoria na base de dados primária, a base de dados secundária terá uma política de auditoria idêntica. Também é possível configurar auditorias na base de dados secundária, permitindo a auditoria no **servidor secundário,** independentemente da base de dados primária.

- Nível de servidor **(recomendado**): Ligue a auditoria tanto no **servidor primário** como no **servidor secundário** - as bases de dados primárias e secundárias serão cada uma auditada independentemente com base na respetiva política de nível de servidor.
- Nível de base de dados: A auditoria ao nível da base de dados para bases de dados secundárias só pode ser configurada a partir de definições de auditoria de bases de dados primárias.
  - A auditoria deve ser ativada na *própria base de dados primária,* não no servidor.
  - Após a auditoria ser ativada na base de dados primária, esta será também ativada na base de dados secundária.

    >[!IMPORTANT]
    >Com a auditoria ao nível da base de dados, as definições de armazenamento da base de dados secundária serão idênticas às da base de dados primária, causando tráfego inter-regional. Recomendamos que ative apenas a auditoria ao nível do servidor e deixe a auditoria ao nível da base de dados desativada para todas as bases de dados.

### <a name="storage-key-regeneration"></a>Regeneração da chave de armazenamento

Em produção, é provável que refresque periodicamente as suas chaves de armazenamento. Ao escrever registos de auditoria para o armazenamento do Azure, precisa de reguardar a sua política de auditoria ao refrescar as suas chaves. O processo é o seguinte:

1. Detalhes de **armazenamento**abertos. Na caixa de acesso ao **armazenamento,** selecione **Secundário**, e clique **EM OK**. Em seguida, clique em **Guardar** no topo da página de configuração de auditoria.

    ![Painel de navegação](./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png)
2. Vá à página de configuração de armazenamento e regeneraa a chave de acesso primária.

    ![Painel de navegação](./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png)
3. Volte para a página de configuração de auditoria, mude a chave de acesso de armazenamento de secundário para primário e, em seguida, clique EM **OK**. Em seguida, clique em **Guardar** no topo da página de configuração de auditoria.
4. Volte para a página de configuração de armazenamento e regeneraa a chave de acesso secundário (em preparação para o ciclo de atualização da próxima chave).

## <a name="manage-azure-sql-server-and-database-auditing"></a><a id="manage-auditing"></a>Gerir a auditoria do Servidor EQL azure e da base de dados

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

**Cmdlets PowerShell (incluindo o suporte da cláusula WHERE para filtragem adicional)**:

- [Criar ou atualizar política de auditoria da base de dados (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Obtenha política de auditoria de base de dados (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Obtenha a Política de Auditoria do Servidor (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Remover política de auditoria da base de dados (Remover-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Remover a Política de Auditoria do Servidor (Remover-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Para um exemplo de script, consulte a deteção de [auditoria e ameaça de Configuração utilizando powerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

### <a name="using-rest-api"></a>Com a API REST

**API DE REPOUSO:**

- [Criar ou atualizar política de auditoria de bases de dados](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Criar ou atualizar a política de auditoria do servidor](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter Política de Auditoria de Base de Dados](/rest/api/sql/database%20auditing%20settings/get)
- [Obter Política de Auditoria do Servidor](/rest/api/sql/server%20auditing%20settings/get)

Política alargada com apoio à cláusula WHERE para filtragem adicional:

- [Criar ou atualizar a política de auditoria alargada da base *de* dados](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Criar ou atualizar política de auditoria *alargada* do servidor](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter Política de Auditoria *Alargada de* Base de Dados](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Obtenha política de auditoria *alargada do* servidor](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-resource-manager-templates"></a>Utilizar modelos do Azure Resource Manager

Pode gerir a auditoria da base de dados Azure SQL utilizando modelos do Gestor de [Recursos Azure,](../azure-resource-manager/management/overview.md) como mostram estes exemplos:

- [Implemente um Servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria na conta de armazenamento da Blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implemente um Servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria para Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implemente um Servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria para Centros de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> As amostras ligadas estão num repositório público externo e são fornecidas 'como está', sem garantia, e não são suportadas ao abrigo de qualquer programa/serviço de suporte da Microsoft.
