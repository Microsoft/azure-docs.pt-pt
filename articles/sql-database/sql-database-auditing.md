---
title: Começar com a auditoria
description: Utilize a auditoria da base de dados Azure SQL para rastrear os eventos da base de dados num registo de auditoria.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/11/2020
ms.custom: azure-synapse
ms.openlocfilehash: 1cac52dcee91e57a22b6d18595b067de888aba73
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082075"
---
# <a name="get-started-with-sql-database-auditing"></a>Começar com a auditoria da base de dados SQL

A auditoria da Azure [SQL Database](sql-database-technical-overview.md) e [da Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rastreia eventos de base de dados e escreve-os num registo de auditoria na sua conta de armazenamento Azure, log Analytics espaço de trabalho ou Hubs de Eventos. Auditoria também:

- Ajuda-o a manter o cumprimento regulamentar, a compreender a atividade da base de dados e a obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.

- Permite e facilita a adesão às normas de conformidade, embora não garanta o cumprimento. Para obter mais informações sobre os programas Azure que suportam a conformidade das normas, consulte o [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde pode encontrar a lista mais atual das certificações de conformidade da Base de Dados SQL.


> [!NOTE] 
> Este tópico aplica-se ao servidor Azure SQL e tanto às bases de dados SQL como à Azure Synapse Analytics que são criadas no servidor Azure SQL. Para a simplicidade, a Base de Dados SQL é utilizada quando se refere tanto à Base de Dados SQL como ao Synapse Azure.

## <a id="subheading-1"></a>Visão geral de auditoria da base de dados Azure SQL

Pode utilizar a auditoria da base de dados SQL para:

- **Mantenha** um rasto de auditoria de eventos selecionados. Pode definir categorias de ações de base de dados a auditar.
- **Relatório** sobre a atividade da base de dados. Pode utilizar relatórios pré-configurados e um dashboard para começar rapidamente com relatórios de atividade e eventos.
- **Analise** relatórios. Você pode encontrar eventos suspeitos, atividade incomum, e tendências.

> [!IMPORTANT]
> Os registos de auditoria são escritos para **Append Blobs** no armazenamento Azure Blob na sua assinatura Azure.
>
> - Todos os tipos de armazenamento (v1, v2, blob) são suportados.
> - Todas as configurações de replicação de armazenamento são suportadas.
> - O armazenamento atrás de uma rede virtual e firewall é suportado.
> - **Atualmente,** o armazenamento premium não é **suportado.**
> - **O espaço hierárquico** para a conta de armazenamento do **Lago Azure Gen2** não é **suportado**atualmente.
> - Não é suportada a auditoria a um Armazém de **Dados Azure SQL** pausado. Para permitir a auditoria, retome o Data Warehouse.
   
## <a id="subheading-8"></a>Definir política de auditoria ao nível do servidor vs. base de dados

Uma política de auditoria pode ser definida para uma base de dados específica ou como uma política de servidor padrão:

- Uma política de servidor aplica-se a todas as bases de dados existentes e recém-criadas no servidor.

- Se a auditoria do *servidor blob estiver ativada,* *aplica-se sempre à base*de dados . A base de dados será auditada, independentemente das definições de auditoria da base de dados.

- Permitir a auditoria blob na base de dados ou no armazém de dados, além de o permitir no servidor, *não* sobrepor-se ou alterar nenhuma das definições da auditoria blob do servidor. Ambas as auditorias existirão lado a lado. Por outras palavras, a base de dados é auditada duas vezes em paralelo; uma vez pela política do servidor e uma vez pela política de base de dados.

   > [!NOTE]
   > Deve evitar permitir a auditoria de blob do servidor e a auditoria da bolha de bases de dados em conjunto, a menos que:
    > - Pretende utilizar uma *conta* de armazenamento diferente ou um período de *retenção* para uma base de dados específica.
    > - Deseja auditar tipos ou categorias de eventos para uma base de dados específica que difere das restantes bases de dados do servidor. Por exemplo, pode ter inserções de mesa que precisam de ser auditadas apenas para uma base de dados específica.
   >
   > Caso contrário, recomendamos que apenas ative a auditoria blob ao nível do servidor e deixe a auditoria ao nível da base de dados desativada para todas as bases de dados.

## <a id="subheading-2"></a>Configurar auditorias para o seu servidor

A secção seguinte descreve a configuração da auditoria utilizando o portal Azure.

  > [!NOTE]
   >Tem agora várias opções para configurar onde os registos de auditoria estão escritos. Pode escrever registos numa conta de armazenamento Azure, num espaço de trabalho de Log Analytics para consumo por registos do Azure Monitor ou para o centro de eventos para consumo através do hub de eventos. Pode configurar qualquer combinação destas opções, e os registos de auditoria serão escritos a cada um.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Navegue para **Auditoria** sob a rubrica Segurança na sua base de dados/painel de servidor SQL.
3. Se preferir configurar uma política de auditoria do servidor, pode selecionar o link de definições do **servidor 'Ver'** na página de auditoria da base de dados. Em seguida, pode visualizar ou modificar as definições de auditoria do servidor. As políticas de auditoria do servidor aplicam-se a todas as bases de dados existentes e recém-criadas neste servidor.

    ![Painel de navegação][2]

4. Se preferir ativar a auditoria ao nível da base de dados, mude **a Auditoria** para **ON**. Se a auditoria do servidor estiver ativada, a auditoria configurada pela base de dados existirá lado a lado com a auditoria do servidor.

    ![Painel de navegação][3]

5. **Novo** - Tem agora várias opções para configurar onde serão escritos registos de auditoria. Pode escrever registos numa conta de armazenamento Azure, num espaço de trabalho de Log Analytics para consumo por registos do Azure Monitor ou para o centro de eventos para consumo através do hub de eventos. Pode configurar qualquer combinação destas opções, e os registos de auditoria serão escritos a cada um.
  
   > [!NOTE]
   > O cliente que pretenda configurar uma loja de registoiveível imutável para os seus eventos de auditoria ao nível do servidor ou da base de dados deve seguir as [instruções fornecidas pelo Armazenamento Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes)
  
   > [!WARNING]
   > Permitir a auditoria ao Log Analytics incorrerá em custos baseados em taxas de ingestão. Tenha em conta o custo associado com a utilização desta [opção,](https://azure.microsoft.com/pricing/details/monitor/)ou considere armazenar os registos de auditoria numa conta de armazenamento Azure.

   ![opções de armazenamento](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a id="audit-storage-destination">Auditoria ao destino de armazenamento</a>

Para configurar registos de auditoria de escrita numa conta de armazenamento, selecione **detalhes de Armazenamento** e **armazenamento**aberto. Selecione a conta de armazenamento Azure onde os registos serão guardados e, em seguida, selecione o período de retenção. Em seguida, clique em **OK**. Os registos mais antigos do que o período de retenção são eliminados.

   > [!IMPORTANT]
   > - O valor predefinido para o período de retenção é 0 (retenção ilimitada). Pode alterar este valor movendo o slider **de Retenção (Dias)** nas **definições** de Armazenamento ao configurar a conta de armazenamento para auditoria.
   > - Se alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, por favor note que a retenção só se aplicará aos registos escritos após a alteração do valor de retenção (os registos escritos durante o período em que a retenção foi definida para ilimitado são preservados, mesmo depois de alteradoo valor de retenção (os registos escritos durante o período em que a retenção foi definida para ilimitado são preservados, mesmo depois de alteradoo valor de retenção (os registos escritos durante o período em que a retenção foi definida para ilimitado são preservados, mesmo depois de alterado o valor da retenção (os registos escritos durante o período em que a retenção foi definida para ilimitado são preservados, mesmo depois de preserva a retenção está ativada)

   ![conta de armazenamento](./media/sql-database-auditing-get-started/auditing_select_storage.png)

Para configurar uma conta de armazenamento sob uma rede virtual ou firewall, precisará de um [administrador de Diretório Ativo](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#provision-an-azure-active-directory-administrator-for-your-managed-instance) no servidor, permitir que os **serviços da Microsoft fidedignos acedam a esta conta** de armazenamento na conta de armazenamento. Além disso, precisa de ter a permissão 'Microsoft.Authorization/roleAssignments/write' na conta de armazenamento selecionada.

Recomendamos que seja Administrador de Acesso ao [Utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator) para que conceda à identidade gerida a função 'contribuinte de dados blob de armazenamento'. Para saber mais sobre permissões e controlo de acesso baseado em papéis, veja O [](../role-based-access-control/role-assignments-portal.md) [que é o controlo de acesso baseado em papéis (RBAC) para recursos Azure?](../role-based-access-control/overview.md)

### <a id="audit-log-analytics-destination">Auditoria ao destino Log Analytics</a>
  
Para configurar os registos de auditoria de escrita para um espaço de trabalho de Log Analytics, selecione **Log Analytics (Pré-visualização)** e abra **detalhes do Log Analytics**. Selecione ou crie o espaço de trabalho do Log Analytics onde os registos serão escritos e, em seguida, clique em **OK**.

   ![LogAnalyticsworkspace](./media/sql-database-auditing-get-started/auditing_select_oms.png)
    
  > [!WARNING]
   > Permitir a auditoria ao Log Analytics incorrerá em custos baseados em taxas de ingestão. Tenha em conta o custo associado com a utilização desta [opção,](https://azure.microsoft.com/pricing/details/monitor/)ou considere armazenar os registos de auditoria numa conta de armazenamento Azure.

### <a id="audit-event-hub-destination">Auditoria ao destino Event Hub</a>

> [!IMPORTANT]
> Não é possível permitir a auditoria a um pool SQL pausado. Para o ativar, desfaça a piscina SQL.

> [!WARNING]
> Permitir a auditoria num servidor que tenha um pool SQL nele **resulta no reinício do pool SQL e novamente interrompido,** o que pode incorrer em taxas de faturação.

Para configurar registos de auditoria de escrita para um centro de eventos, selecione **Event Hub (Pré-visualização)** e abra detalhes do **Event Hub**. Selecione o centro de eventos onde os registos serão escritos e, em seguida, clique EM **OK**. Certifique-se de que o centro de eventos está na mesma região que a sua base de dados e servidor.

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a id="subheading-3"></a>Analisar registos e relatórios de auditoria

Se optou por escrever registos de auditoria nos registos do Monitor Azure:

- Utilize o [portal Azure.](https://portal.azure.com)  Abra a base de dados relevante. No topo da página de **Auditoria** da base de dados, clique em **Ver registos**de auditoria .

    ![ver registos de auditoria](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Depois, tem duas formas de ver os registos:
    
    Clicar no **Log Analytics** no topo da página de **registos** da Auditoria abrirá a vista De Registos no espaço de trabalho do Log Analytics, onde pode personalizar o intervalo de tempo e a consulta de pesquisa.
    
    ![aberto no espaço de trabalho Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Clicar no painel de **instrumentos de visualização** na parte superior da página de **registos** da Auditoria abrirá um dashboard exibindo informações de registos de auditoria, onde poderá perfurar insights de segurança, acesso a dados sensíveis e muito mais. Este dashboard foi concebido para o ajudar a obter informações de segurança para os seus dados.
    Também pode personalizar o intervalo de tempo e pesquisa. 
    ![ver o painel de instrumentos de análise de registo](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Painel de análise de log analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics Security Insights](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Em alternativa, também pode aceder aos registos de auditoria da lâmina Log Analytics. Abra o seu espaço de trabalho Log Analytics e sob secção **geral,** clique em **Registos**. Pode começar com uma consulta simples, como: *pesquisar "SQLSecurityAuditEvents"* para ver os registos de auditoria.
    A partir daqui, também pode utilizar registos do [Monitor Azure](../log-analytics/log-analytics-log-search.md) para executar pesquisas avançadas nos seus dados de registo de auditoria. Os registos do Azure Monitor dão-lhe insights operacionais em tempo real utilizando instrumentos de pesquisa integrados e personalizados para analisar facilmente milhões de registos em todas as suas cargas de trabalho e servidores. Para obter informações adicionais úteis sobre o idioma de pesquisa de registos do Monitor Azure e comandos, consulte a referência de pesquisa de [registos do Monitor Azure](../log-analytics/log-analytics-log-search.md).

Se optou por escrever registos de auditoria ao Event Hub:

- Para consumir dados de registos de auditoria do Event Hub, terá de configurar um fluxo para consumir eventos e escrevê-los para um alvo. Para mais informações, consulte a [Documentação do Azure Event Hubs](../event-hubs/index.yml).
- Os registos de auditoria no Event Hub são capturados no corpo de eventos [Apache Avro](https://avro.apache.org/) e armazenados usando formatação JSON com codificação UTF-8. Para ler os registos de auditoria, pode utilizar [ferramentas Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas semelhantes que processem este formato.

Se optou por escrever registos de auditoria numa conta de armazenamento Azure, existem vários métodos que pode utilizar para visualizar os registos:

> [!NOTE] 
> A auditoria às [réplicas apenas](sql-database-read-scale-out.md) de leitura está ativada automaticamente. Para mais detalhes sobre a hierarquia das pastas de armazenamento, nomeação de convenções e formato de registo, consulte o Formato de Registo de Auditoria da Base de [Dados SQL](sql-database-audit-log-format.md). 

- Os registos de auditoria são agregados na conta que escolheu durante a configuração. Pode explorar registos de auditoria utilizando uma ferramenta como o [Azure Storage Explorer](https://storageexplorer.com/). No armazenamento do Azure, os registos de auditoria são guardados como uma coleção de ficheiros blob dentro de um recipiente chamado **sqldbauditlogs**. Para mais detalhes sobre a hierarquia das pastas de armazenamento, nomeação de convenções e formato de registo, consulte o Formato de Registo de Auditoria da Base de [Dados SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Utilize o [portal Azure.](https://portal.azure.com)  Abra a base de dados relevante. No topo da página de **Auditoria** da base de dados, clique em **Ver registos**de auditoria .

    ![Painel de navegação][7]

    **Os registos** de auditoria abrem,a partir do qual poderá ver os registos.

  - Pode visualizar datas específicas clicando em **Filtro** no topo da página de registos da **Auditoria.**
  - Pode alternar entre os registos de auditoria que foram criados pela política de auditoria do *servidor* e a política de auditoria da base de *dados,* toggling **Audit Source**.
  - Só pode ver registos de auditoria relacionados com injeção SQL verificando **apenas registos de auditoria para a caixa de verificação de injeções SQL.**

       ![Painel de navegação][8]

- Utilize a função do sistema **sys.fn_get_audit_file** (T-SQL) para devolver os dados de registo de auditoria em formato tabular. Para obter mais informações sobre a utilização desta função, consulte [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Utilize **ficheiros de auditoria** de fusão no Estúdio de Gestão de Servidores SQL (começando com SSMS 17):
    1. No menu SSMS, selecione **File** > **Abrir** > **Ficheiros de Auditoria**de Fusão .

        ![Painel de navegação][9]
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

## <a id="subheading-5"></a>Práticas de produção

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditoria de bases de dados geo-replicadas</a>

Com bases de dados geo-replicadas, quando permitir a auditoria na base de dados primária, a base de dados secundária terá uma política de auditoria idêntica. Também é possível configurar auditorias na base de dados secundária, permitindo a auditoria no **servidor secundário,** independentemente da base de dados primária.

- Nível de servidor **(recomendado**): Ligue a auditoria tanto no **servidor primário** como no **servidor secundário** - as bases de dados primárias e secundárias serão cada uma auditada independentemente com base na respetiva política de nível de servidor.
- Nível de base de dados: A auditoria ao nível da base de dados para bases de dados secundárias só pode ser configurada a partir de definições de auditoria de bases de dados primárias.
  - A auditoria deve ser ativada na *própria base de dados primária,* não no servidor.
  - Após a auditoria ser ativada na base de dados primária, esta será também ativada na base de dados secundária.

    >[!IMPORTANT]
    >Com a auditoria ao nível da base de dados, as definições de armazenamento da base de dados secundária serão idênticas às da base de dados primária, causando tráfego inter-regional. Recomendamos que ative apenas a auditoria ao nível do servidor e deixe a auditoria ao nível da base de dados desativada para todas as bases de dados.

### <a id="subheading-6">Regeneração da chave de armazenamento</a>

Em produção, é provável que refresque periodicamente as suas chaves de armazenamento. Ao escrever registos de auditoria para o armazenamento do Azure, precisa de reguardar a sua política de auditoria ao refrescar as suas chaves. O processo é o seguinte:

1. Detalhes de **armazenamento**abertos. Na caixa de acesso ao **armazenamento,** selecione **Secundário**, e clique **EM OK**. Em seguida, clique em **Guardar** no topo da página de configuração de auditoria.

    ![Painel de navegação][5]
2. Vá à página de configuração de armazenamento e regeneraa a chave de acesso primária.

    ![Painel de navegação][6]
3. Volte para a página de configuração de auditoria, mude a chave de acesso de armazenamento de secundário para primário e, em seguida, clique EM **OK**. Em seguida, clique em **Guardar** no topo da página de configuração de auditoria.
4. Volte para a página de configuração de armazenamento e regeneraa a chave de acesso secundário (em preparação para o ciclo de atualização da próxima chave).

## <a name="additional-information"></a>Informações adicionais

- Se quiser personalizar os eventos auditados, pode fazê-lo através de [cmdlets PowerShell](#subheading-7) ou da [Rest API](#subheading-9).

- Depois de configurar as definições de auditoria, pode ligar a nova funcionalidade de deteção de ameaças e configurar e-mails para receber alertas de segurança. Quando utiliza a deteção de ameaças, recebe alertas pró-ativos sobre atividades anómalas na base de dados que podem indicar potenciais ameaças à segurança. Para mais informações, consulte [Começar com a deteção de ameaças.](sql-database-threat-detection-get-started.md)
- Para mais detalhes sobre o formato de registo, a hierarquia da pasta de armazenamento e as convenções de nomeação, consulte a Referência do Formato de Registo de [Auditoria Blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > A Auditoria de Base de Dados Azure SQL armazena 4000 caracteres de dados para campos de caracteres num registo de auditoria. Quando a **declaração** ou os valores **data_sensitivity_information** devolvidos de uma ação auditável contiverem mais de 4000 caracteres, quaisquer dados para além dos primeiros 4000 caracteres serão **truncados e não auditados.**

- Os registos de auditoria são escritos para **Append Blobs** num armazenamento Azure Blob na sua subscrição Azure:
  - **O Armazenamento Premium** não é **atualmente suportado** por Append Blobs.

- A política de auditoria por defeito inclui todas as ações e o seguinte conjunto de grupos de ação, que auditarão todas as consultas e procedimentos armazenados executados na base de dados, bem como logins bem sucedidos e falhados:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Pode configurar a auditoria para diferentes tipos de ações e grupos de ação utilizando o PowerShell, conforme descrito na auditoria da base de dados Manage SQL utilizando a secção [Azure PowerShell.](#subheading-7)

- Ao utilizar a Autenticação AAD, os registos de logins falhados *não* aparecerão no registo de auditoria sQL. Para ver registos de auditoria de login falhados, é necessário visitar o [portal azure Ative Diretório,]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)que regista detalhes destes eventos.

- A auditoria da Base de Dados Azure SQL está otimizada para disponibilidade e desempenho. Durante a atividade muito elevada, a Base de Dados Azure SQL permite que as operações prossigam e não possam registar alguns eventos auditados.

- Para configurar auditoriaivel imutável na conta de armazenamento, consulte Permitir que as bolhas de [apêndice protegidos escrevam](../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes). Por favor, note que o nome do recipiente para Auditoria é **sqldbauditlogs**.


## <a id="subheading-7"></a>Gerir a auditoria do Servidor E Base de Dados Azure SQL utilizando o Azure PowerShell

**Cmdlets PowerShell (incluindo o suporte da cláusula WHERE para filtragem adicional)** :

- [Criar ou atualizar política de auditoria da base de dados (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Obtenha política de auditoria de base de dados (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Obtenha a Política de Auditoria do Servidor (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Remover política de auditoria da base de dados (Remover-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Remover a Política de Auditoria do Servidor (Remover-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Para um exemplo de script, consulte a deteção de [auditoria e ameaça de Configuração utilizando powerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-8"></a>Gerir a auditoria do Servidor E QQL azure e da base de dados utilizando a API REST

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

## <a id="subheading-9"></a>Gerir a auditoria do Servidor EQL Azur e da Base de Dados utilizando modelos de Gestor de Recursos Azure

Pode gerir a auditoria da base de dados Azure SQL utilizando modelos do Gestor de [Recursos Azure,](../azure-resource-manager/management/overview.md) como mostram estes exemplos:

- [Implemente um Servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria na conta de armazenamento da Blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implemente um Servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria para Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implemente um Servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria para Centros de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> As amostras ligadas estão num repositório público externo e são fornecidas 'como está', sem garantia, e não são suportadas ao abrigo de qualquer programa/serviço de suporte da Microsoft.

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
