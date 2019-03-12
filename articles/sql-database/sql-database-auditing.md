---
title: Introdução à auditoria de base de dados SQL do Azure | Documentos da Microsoft
description: Utilize a auditoria de base de dados SQL do Azure para controlar eventos de base de dados num log de auditoria.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 7069114a8cb63b8b166bc29e92d8f355c49824bb
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727149"
---
# <a name="get-started-with-sql-database-auditing"></a>Introdução à auditoria da base de dados SQL

Auditoria do Azure [base de dados SQL](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) faixas de base de dados eventos e escreve-os para uma auditoria registo na sua conta de armazenamento do Azure, a área de trabalho do OMS ou a Hubs de eventos. Auditoria também:

- Ajuda-o a manter a conformidade regulamentar, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações empresariais ou suspeitas de violações de segurança.

- Ativa e facilita o cumprimento das normas de conformidade, embora ele não garante a conformidade. Para obter mais informações sobre o Azure programas de conformidade de padrões esse suporte, consulte a [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/compliance/).


> [!NOTE] 
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Base de dados do SQL do Azure a auditoria de descrição geral

Pode utilizar a auditoria de base de dados SQL para:

- **Reter** uma trilha de auditoria de eventos selecionados. Pode definir categorias de ações de base de dados para serem auditados.
- **Relatório** na atividade de base de dados. Pode utilizar um dashboard e relatórios pré-configurados para começar rapidamente com a atividade e relatórios de eventos.
- **Analisar** relatórios. Pode encontrar eventos suspeitos, atividade invulgar e tendências.

> [!IMPORTANT]
> Registos de auditoria são escritos **Blobs de acréscimo** no armazenamento de Blobs do Azure na sua subscrição do Azure.
>
> - Todos os tipos de armazenamento (v1, v2, blob) são suportados.
> - Todas as configurações de replicação de armazenamento são suportadas.
> - **O armazenamento Premium** está atualmente **nepodporuje**.
> - **Armazenamento na VNet** está atualmente **nepodporuje**.
> - **Armazenamento protegido por uma Firewall** está atualmente **não suportado**

## <a id="subheading-8"></a>Definir o nível de servidor vs. a política de auditoria ao nível do banco de dados

Pode ser definida uma política de auditoria para uma base de dados específica ou como uma política de servidor predefinida:

- Uma política de servidor aplica-se às bases de dados recém-criado e todas as existentes no servidor.

- Se *auditoria de Blobs do servidor estiver ativada*, ele *sempre aplica-se à base de dados*. A base de dados vai ser auditada, independentemente das definições de auditoria da base de dados.

- Ativar a auditoria de BLOBs no armazém de dados ou base de dados, além de ativá-la no servidor, faz *não* substituir ou alterar quaisquer das definições de auditoria de BLOBs de servidor. Ambas as auditorias existirão lado a lado. Em outras palavras, a base de dados é auditada duas vezes em paralelo; vez pela política de servidor e uma vez pela política de base de dados.

   > [!NOTE]
   > Deve evitar a ativar a auditoria de Blobs do servidor e a base de dados blob auditoria em conjunto, a menos que:
    > - Pretende usar outra *conta de armazenamento* ou *período de retenção* para uma base de dados específico.
    > - Quer categorias para um banco de dados específico que diferem do restante dos bancos de dados no servidor ou de tipos de eventos de auditoria. Por exemplo, poderá ter inserções de tabela que precisam de ser auditadas apenas para uma base de dados específico.
   >
   > Caso contrário, recomendamos que habilite a auditoria de BLOBs apenas ao nível do servidor e deixe a auditoria ao nível do banco de dados desativada para todas as bases de dados.

## <a id="subheading-2"></a>Configurar a auditoria da base de dados

A seguinte secção descreve a configuração de auditoria no portal do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Navegue para **auditoria** sob o cabeçalho de segurança no seu painel de servidor/base de dados SQL.

    <a id="auditing-screenshot"></a> ![Painel de navegação][1]

3. Se preferir configurar uma política de auditoria de servidor, pode selecionar o **ver definições do servidor** link na página de auditoria de base de dados. Pode, em seguida, ver ou modificar o definições de auditoria de servidor. Políticas de auditoria de servidor aplicam-se às bases de dados recém-criado e todas as existentes neste servidor.

    ![Painel de navegação][2]

4. Se preferir ativar a auditoria no nível da base de dados, mude **auditoria** ao **ON**.

    Se a auditoria do servidor estiver ativada, a auditoria de base de dados configurada existirão lado a lado com a auditoria de servidor.

    ![Painel de navegação][3]

5. **Novo** -agora tem várias opções de configuração onde os registos de auditoria serão escritos. Pode escrever os registos para uma conta de armazenamento do Azure, para uma área de trabalho do Log Analytics para consumo pelos registos do Azure Monitor ou para hub de eventos para consumo com o hub de eventos. Pode configurar qualquer combinação destas opções e registos de auditoria serão escritos para cada um.

    ![Opções de armazenamento](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Para configurar a auditoria de escrever registos para uma conta de armazenamento, selecione **armazenamento** e abra **detalhes de armazenamento**. Selecione a conta de armazenamento do Azure onde serão guardados os registos e, em seguida, selecione o período de retenção. Os registos antigos serão eliminados. Em seguida, clique em **OK**.

    ![conta de armazenamento](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Para configurar a auditoria de escrever registos para um trabalho do Log Analytics, selecione **Log Analytics (pré-visualização)** e abra **detalhes do Log Analytics**. Selecione ou crie a área de trabalho do Log Analytics para onde os registos serão escritos e, em seguida, clique em **OK**.

    ![Área de trabalho do Log Analytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Para configurar a auditoria de escrever registos para um hub de eventos, selecione **Hub de eventos (pré-visualização)** e abra **detalhes do Hub de eventos**. Selecione o hub de eventos em que os registos serão escritos e, em seguida, clique em **OK**. Certifique-se de que o hub de eventos está na mesma região que a sua base de dados e o servidor.

    ![Hub de eventos](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Clique em **Guardar**.
10. Se pretender personalizar os eventos auditados, pode fazê-lo via [cmdlets do PowerShell](#subheading-7) ou o [REST API](#subheading-9).
11. Depois de configurar as definições de auditorias, pode ativar a nova funcionalidade de deteção de ameaças e configurar os e-mails para receber alertas de segurança. Ao utilizar a deteção de ameaças, recebe alertas pró-ativos relativamente a atividades anómalas da base de dados que podem indicar a potenciais ameaças de segurança. Para obter mais informações, consulte [introdução à deteção de ameaças](sql-database-threat-detection-get-started.md).


> [!IMPORTANT]
>Ativação da auditoria num Azure SQL Data Warehouse, ou num servidor que tenha um Azure SQL Data Warehouse, **irá resultar no armazém de dados que está sendo continuado**, mesmo no caso em que ele foi anteriormente colocada em pausa. **Certifique-se de colocar em pausa o armazém de dados novamente depois de ativar a auditoria**. "


## <a id="subheading-3"></a>Analisar registos de auditoria e relatórios

Se optar por escrever registos de auditoria nos registos de Monitor do Azure:

- Utilize o [portal do Azure](https://portal.azure.com).  Abra a base de dados relevante. No topo da base de dados **auditoria** página, clique em **ver registos de auditoria**.

    ![Ver registos de auditoria](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- Em seguida, clicar em **abra no OMS** na parte superior a **registos de auditoria** página irá abrir a vista de registos no Log Analytics, onde pode personalizar o intervalo de tempo e a consulta de pesquisa.

    ![abrir no Log Analytics](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- Em alternativa, também pode aceder os registos de auditoria no painel do Log Analytics. Abra a área de trabalho do Log Analytics e, em **gerais** secção, clique em **registos**. Pode começar com uma consulta simples, tais como: *pesquisar "SQLSecurityAuditEvents"* para ver a auditoria de registos.
    A partir daqui, pode também usar [registos do Azure Monitor](../log-analytics/log-analytics-log-search.md) para executar pesquisas avançadas nos seus dados de registo de auditoria. Registos de Monitor do Azure dá-lhe as informações operacionais em tempo real através da pesquisa integrada e dashboards personalizados para analisar, prontamente, milhões de registos em todas as suas cargas de trabalho e servidores. Para obter informações adicionais úteis sobre comandos e linguagem de pesquisa de registos do Azure Monitor, consulte [referência de pesquisa de registos do Azure Monitor](../log-analytics/log-analytics-log-search.md).

Se optar por escrever registos de auditoria para o Hub de eventos:

- Para consumir dados de registos de auditoria do Hub de eventos, terá de configurar um fluxo para consumir eventos e escrevê-los para um destino. Para obter mais informações, consulte [documentação de Hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/).
- Registos de auditoria no Hub de eventos são capturados no corpo da [Apache Avro](http://avro.apache.org/) eventos e armazenados através de formatação do JSON com codificação UTF-8. Para ler os registos de auditoria, pode usar [Avro ferramentas](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) ou ferramentas similares que processam neste formato.

Se optar por escrever registos de auditoria numa conta de armazenamento do Azure, existem vários métodos que pode utilizar para ver os registos:

- Registos de auditoria são agregados na conta que escolheu durante a configuração. Pode explorar os registos de auditoria utilizando uma ferramenta como [Explorador de armazenamento do Azure](http://storageexplorer.com/). No armazenamento do Azure, os registos de auditoria são guardados como uma coleção de ficheiros de blob num contentor com o nome **sqldbauditlogs**. Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, as convenções de nomenclatura e formato de registo, consulte a [referência de formato de registo de auditoria de Blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Utilize o [portal do Azure](https://portal.azure.com).  Abra a base de dados relevante. No topo da base de dados **auditoria** página, clique em **ver registos de auditoria**.

    ![Painel de navegação][7]

    **Registos de auditoria** abre-se, a partir da qual vai conseguir ver os registos.

  - Pode exibir datas específicas ao clicar em **filtro** na parte superior a **registos de auditoria** página.
  - Pode alternar entre os registos de auditoria que foram criados pelos *política de auditoria de servidor* e o *política de auditoria de base de dados* alternando **origem de auditoria**.
  - Pode ver apenas a injeção de SQL relacionadas com registos de auditoria, verificando **Show só de auditoria registos para injeções SQL** caixa de verificação.

       ![Painel de navegação][8]

- Utilize a função de sistema **sys.fn_get_audit_file** (T-SQL) para retornar os dados de registo de auditoria num formato tabular. Para obter mais informações sobre como utilizar esta função, veja [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Uso **intercalar ficheiros de auditoria** no SQL Server Management Studio (começando com o SSMS 17):
    1. No menu do SSMS, selecione **arquivo** > **abrir** > **intercalar ficheiros de auditoria**.

        ![Painel de navegação][9]
    2. O **adicionar ficheiros de auditoria** é aberta a caixa de diálogo. Selecione uma da **adicionar** opções de escolha se pretende intercalar os ficheiros de auditoria a partir de um disco local ou importá-los do armazenamento do Azure. Tem de fornecer os detalhes de armazenamento do Azure e a chave da conta.

    3. Depois de todos os ficheiros para intercalar tiverem sido adicionados, clique em **OK** para concluir a operação de intercalação.

    4. O ficheiro mesclado é aberta no SSMS, onde pode ver e analisá-lo, bem como exportá-lo para um ficheiro XEL ou CSV ou para uma tabela.

- Utilize o Power BI. Pode ver e analisar dados de registo de auditoria no Power BI. Para obter mais informações e para aceder a um modelo que pode ser baixado, veja [analisar dados de registo de auditoria no Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Transferir os ficheiros de registo do contentor de blob de armazenamento do Azure através do portal ou com uma ferramenta como [Explorador de armazenamento do Azure](http://storageexplorer.com/).
  - Depois de transferir um ficheiro de registo localmente, clique duas vezes o arquivo para abrir, visualizar e analisar os registos no SSMS.
  - Também pode transferir vários arquivos simultaneamente através do Explorador de armazenamento do Azure. Para fazer isso, uma subpasta específica com o botão direito e selecione **guardar como** salvar numa pasta local.

- Métodos adicionais:

  - Depois de baixar vários ficheiros ou numa subpasta que contém ficheiros de registo, é possível intercalá-los localmente conforme descrito nas instruções de ficheiros de auditoria de intercalação do SSMS descritas anteriormente.
  - Auditoria de BLOBs de modo de exibição aceder programaticamente aos registos:

    - Utilize o [leitor de eventos expandidos](https://blogs.msdn.microsoft.com/extended_events/20../../introducing-the-extended-events-reader/) biblioteca do c#.
    - [Ficheiros de eventos expandidos de consulta](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) com o PowerShell.

## <a id="subheading-5"></a>Práticas de produção

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditoria de bases de dados de georreplicação</a>

Georreplicado bancos de dados, quando ativar a auditoria na base de dados primária a base de dados secundária terão uma política de auditoria idêntica. Também é possível configurar a auditoria na base de dados secundário ao ativar a auditoria no **servidor secundário**, independentemente da base de dados primária.

- Ao nível do servidor (**recomendado**): Ativar a auditoria em ambos os **servidor primário** , bem como a **servidor secundário** -as bases de dados primárias e secundárias serão cada auditadas independentemente com base em seus respectiva política de ao nível do servidor.
- Nível de base de dados: Auditoria ao nível da base de dados para bases de dados secundárias só pode ser configurada da base de dados primária, definições de auditoria.
  - Auditoria deve ser habilitada no *base de dados primária em si*, não o servidor.
  - Depois de auditoria estiver ativada na base de dados primária, ele também ficará ativo na base de dados secundário.

    >[!IMPORTANT]
    >Com a auditoria ao nível da base de dados, as definições de armazenamento para a base de dados secundário será idênticas de base de dados primária, fazendo com que o tráfego entre regiões. Recomendamos que ativar a auditoria apenas ao nível do servidor e deixe a auditoria ao nível do banco de dados desativada para todas as bases de dados.
    > [!WARNING]
    > Com o hub de eventos ou registos do Azure Monitor como destinos para registos de auditoria ao nível do servidor não é suportada atualmente para bases de dados de georreplicação secundárias.

### <a id="subheading-6">Regeneração da chave de armazenamento</a>

Na produção, é provável que atualizar as chaves de armazenamento periodicamente. Ao escrever os registos de auditoria para o armazenamento do Azure, terá de volte a guardar a política de auditoria ao atualizar as suas chaves. O processo é o seguinte:

1. Open **detalhes de armazenamento**. Na **chave de acesso de armazenamento** caixa, selecione **secundário**e clique em **OK**. Em seguida, clique em **guardar** no topo da página de configuração da auditoria.

    ![Painel de navegação][5]
2. Vá para a página de configuração de armazenamento e voltar a gerar a chave de acesso primária.

    ![Painel de navegação][6]
3. Vá para a página de configuração de auditoria, mude a chave de acesso de armazenamento secundária para primária de e, em seguida, clique em **OK**. Em seguida, clique em **guardar** no topo da página de configuração da auditoria.
4. Volte para a página de configuração de armazenamento e voltar a gerar a chave de acesso secundária (em preparação para o ciclo de atualização da chave seguinte).

## <a name="additional-information"></a>Informação Adicional

- Para obter detalhes sobre o registo de formatar, hierarquia da pasta de armazenamento e convenções de nomenclatura, consulte a [referência de formato de registo de auditoria de Blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Auditoria de base de dados SQL do Azure armazena a 4000 carateres de dados para campos de caracteres num registo de auditoria. Quando o **instrução** ou o **data_sensitivity_information** valores devolvidos por uma ação auditada contenham mais de 4000 carateres, quaisquer dados para além dos primeiro 4000 carateres serão  **truncada e não auditada**.

- Registos de auditoria são escritos **Blobs de acréscimo** num armazenamento de Blobs do Azure na sua subscrição do Azure:
  - **O armazenamento Premium** está atualmente **nepodporuje** por Blobs de acréscimo.
  - **Armazenamento na VNet** está atualmente **nepodporuje**.

- A política de auditoria predefinida inclui todas as ações e o seguinte conjunto de grupos de ação, o que vai auditar todas as consultas e procedimentos armazenados, executados em relação a base de dados, bem como os inícios de sessão com êxito ou falhados:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Pode configurar a auditoria para diferentes tipos de ações e grupos de ação com o PowerShell, conforme descrito no [auditoria de base de dados de SQL gerir com o Azure PowerShell](#subheading-7) secção.

- Quando utilizar a autenticação do AAD, falha será de registos de inícios de sessão *não* aparecem no registo de auditoria de SQL. Para ver registos de auditoria de início de sessão, tem de visitar o [portal do Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), que regista os detalhes desses eventos.


## <a id="subheading-7"></a>Gerir a auditoria de base de dados SQL com o Azure PowerShell

**Cmdlets do PowerShell (incluindo suporte de cláusula WHERE para filtrar adicionais)**:

- [Criar ou atualizar a base de dados (Set-AzSqlDatabaseAuditing) de política de auditoria](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseauditing)
- [Criar ou atualizar a política de auditoria de servidor (conjunto-AzSqlServerAuditing)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserverauditing)
- [Obter política de auditoria de base de dados (Get-AzSqlDatabaseAuditing)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseauditing)
- [Obter política de auditoria (Get-AzSqlServerAuditing) de servidor](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverauditing)

Para obter um exemplo de script, consulte [configurar a deteção de ameaças e auditoria com o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Gerir a auditoria de base de dados SQL com a REST API

**REST API**:

- [Criar ou atualizar a política de auditoria de base de dados](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Criar ou atualizar o servidor de política de auditoria](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter política de auditoria de base de dados](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Obter política de auditoria de servidor](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Obter política expandida com em que suporta a cláusula para filtragem adicionais:

- [Criar ou atualizar a base de dados *expandido* diretiva de auditoria](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Criar ou atualizar o servidor *expandido* diretiva de auditoria](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter a base de dados *expandido* diretiva de auditoria](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Obtenha o Server *expandido* diretiva de auditoria](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Gerir a auditoria de base de dados SQL através de modelos ARM

Pode gerenciar a auditoria de base de dados de SQL do Azure através de [do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelos, conforme mostrado nestes exemplos:

- [Implementar um servidor de SQL do Azure com auditoria ativada para escrever registos de auditoria para a conta de armazenamento de Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implementar um servidor de SQL do Azure com auditoria ativada para escrever registos de auditoria nos registos do Azure Monitor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implementar um servidor de SQL do Azure com auditoria ativada para escrever registos de auditoria para os Hubs de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

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