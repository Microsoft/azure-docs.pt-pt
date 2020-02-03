---
title: Introdução à auditoria
description: Use a auditoria do banco de dados SQL do Azure para acompanhar eventos de banco de dados em um log de auditoria.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 8f82f0539432418f967d51f00e659ce92d1fa9b6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719811"
---
# <a name="get-started-with-sql-database-auditing"></a>Começar com a auditoria da base de dados SQL

A auditoria da Base de Dados Azure [SQL](sql-database-technical-overview.md) e do [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rastreia eventos de base de dados e escreve-os num registo de auditoria na sua conta de armazenamento Azure, log Analytics espaço de trabalho ou Hubs de Eventos. Auditoria também:

- Ajuda-o a manter o cumprimento regulamentar, a compreender a atividade da base de dados e a obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.

- Permite e facilita a adesão às normas de conformidade, embora não garanta o cumprimento. Para obter mais informações sobre os programas Azure que suportam a conformidade das normas, consulte o [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde pode encontrar a lista mais atual das certificações de conformidade da Base de Dados SQL.


> [!NOTE] 
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Visão geral de auditoria da base de dados Azure SQL

Pode utilizar a auditoria da base de dados SQL para:

- **Mantenha** um rasto de auditoria de eventos selecionados. Pode definir categorias de ações de base de dados a auditar.
- **Relatório** sobre a atividade da base de dados. Pode utilizar relatórios pré-configurados e um dashboard para começar rapidamente com relatórios de atividade e eventos.
- **Analise** relatórios. Você pode encontrar eventos suspeitos, atividade incomum, e tendências.

> [!IMPORTANT]
> Os registos de auditoria são escritos para **Append Blobs** no armazenamento Azure Blob na sua assinatura Azure.
>
> - Há suporte para todos os tipos de armazenamento (v1, v2, BLOB).
> - Há suporte para todas as configurações de replicação de armazenamento.
> - **Atualmente,** o armazenamento premium não é **suportado.**
> - **O armazenamento em VNet** não é **suportado**atualmente.
> - **O armazenamento atrás de uma Firewall** não é **suportado**atualmente .
> - **O espaço hierárquico** para a conta de armazenamento do **Lago Azure Gen2** não é **suportado**atualmente.

## <a id="subheading-8"></a>Definir política de auditoria ao nível do servidor vs. base de dados

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política de servidor padrão:

- Uma política de servidor se aplica a todos os bancos de dados existentes e recém-criados no servidor.

- Se a auditoria do *servidor blob estiver ativada,* *aplica-se sempre à base*de dados . O banco de dados será auditado, independentemente das configurações de auditoria do banco de dados.

- Permitir a auditoria blob na base de dados ou no armazém de dados, além de o permitir no servidor, *não* sobrepor-se ou alterar nenhuma das definições da auditoria blob do servidor. Ambas as auditorias existirão lado a lado. Em outras palavras, o banco de dados é auditado duas vezes em paralelo; uma vez pela política de servidor e uma vez pela política de banco de dados.

   > [!NOTE]
   > Você deve evitar habilitar a auditoria de blob de servidor e a auditoria de blob de banco de dados, a menos que:
    > - Pretende utilizar uma *conta* de armazenamento diferente ou um período de *retenção* para uma base de dados específica.
    > - Você deseja auditar tipos de eventos ou categorias para um banco de dados específico que difere do restante dos bancos de dados no servidor. Por exemplo, você pode ter inserções de tabela que precisam ser auditadas apenas para um banco de dados específico.
   >
   > Caso contrário, recomendamos que você habilite apenas a auditoria de blob no nível do servidor e deixe a auditoria no nível do banco de dados desabilitada para todos os bancos.

## <a id="subheading-2"></a>Configurar auditorias para a sua base de dados

A seção a seguir descreve a configuração de auditoria usando o portal do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Navegue para **Auditoria** sob a rubrica Segurança na sua base de dados/painel de servidor SQL.

    <a id="auditing-screenshot"></a>![][1] de painel de navegação

3. Se preferir configurar uma política de auditoria do servidor, pode selecionar o link de definições do **servidor 'Ver'** na página de auditoria da base de dados. Em seguida, você pode exibir ou modificar as configurações de auditoria do servidor. As políticas de auditoria de servidor se aplicam a todos os bancos de dados existentes e recém-criados neste servidor.

    ![Painel de navegação][2]

4. Se preferir ativar a auditoria ao nível da base de dados, mude **a Auditoria** para **ON**.

    Se a auditoria do servidor estiver habilitada, a auditoria configurada pelo banco de dados existirá lado a lado com a auditoria do servidor.

    ![Painel de navegação][3]

5. **Novo** - Tem agora várias opções para configurar onde serão escritos registos de auditoria. Você pode gravar logs em uma conta de armazenamento do Azure, em um espaço de trabalho Log Analytics para consumo por Azure Monitor logs ou no Hub de eventos para consumo usando o Hub de eventos. Você pode configurar qualquer combinação dessas opções e os logs de auditoria serão gravados em cada um.
  
  > [!NOTE]
   >O cliente que pretenda configurar uma loja de registoiveível imutável para os seus eventos de auditoria ao nível do servidor ou da base de dados deve seguir as [instruções fornecidas pelo Armazenamento Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes)
  
  > [!WARNING]
   > Habilitar a auditoria para Log Analytics incorrerá em custo com base nas taxas de ingestão. Tenha em conta o custo associado com a utilização desta [opção,](https://azure.microsoft.com/pricing/details/monitor/)ou considere armazenar os registos de auditoria numa conta de armazenamento Azure.

    ![opções de armazenamento](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Para configurar registos de auditoria de escrita numa conta de armazenamento, selecione **detalhes de Armazenamento** e **armazenamento**aberto. Selecione a conta de armazenamento do Azure na qual os logs serão salvos e, em seguida, selecione o período de retenção. Os logs antigos serão excluídos. Em seguida, clique em **OK**.

   > [!IMPORTANT]
   > - O valor padrão do período de retenção é 0 (retenção ilimitada). Pode alterar este valor movendo o slider **de Retenção (Dias)** nas **definições** de Armazenamento ao configurar a conta de armazenamento para auditoria.
   > - Se você alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, observe que a retenção será aplicada somente aos logs gravados após a alteração do valor de retenção (logs gravados durante o período em que a retenção foi definida como ilimitada são preservados, mesmo após a retenção está habilitada)

    ![conta de armazenamento](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Para configurar os registos de auditoria de escrita para um espaço de trabalho de Log Analytics, selecione **Log Analytics (Pré-visualização)** e abra **detalhes do Log Analytics**. Selecione ou crie o espaço de trabalho do Log Analytics onde os registos serão escritos e, em seguida, clique em **OK**.

    ![Área de trabalho do Log Analytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Para configurar registos de auditoria de escrita para um centro de eventos, selecione **Event Hub (Pré-visualização)** e abra detalhes do **Event Hub**. Selecione o centro de eventos onde os registos serão escritos e, em seguida, clique EM **OK**. Certifique-se de que o Hub de eventos esteja na mesma região que o seu banco de dados e servidor.

    ![Hub de eventos](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Clique em **Guardar**.
10. Se quiser personalizar os eventos auditados, pode fazê-lo através de [cmdlets PowerShell](#subheading-7) ou da [Rest API](#subheading-9).
11. Depois de definir as configurações de auditoria, você pode ativar o novo recurso de detecção de ameaças e configurar os emails para receber alertas de segurança. Ao usar a detecção de ameaças, você recebe alertas proativos sobre atividades anormais de banco de dados que podem indicar possíveis ameaças à segurança. Para mais informações, consulte [Começar com a deteção de ameaças.](sql-database-threat-detection-get-started.md)

> [!IMPORTANT]
> Não é possível habilitar a auditoria em um SQL Data Warehouse do Azure em pausa. Para habilitá-lo, cancele a pausa do data warehouse.

> [!WARNING]
> Permitir a auditoria num servidor que tenha um Armazém de Dados Azure SQL no mesmo **resultará no reinício e nova pausa** do Data Warehouse, o que pode incorrer em taxas de faturação.

## <a id="subheading-3"></a>Analisar registos e relatórios de auditoria

Se você optar por gravar logs de auditoria em logs de Azure Monitor:

- Utilize o [portal Azure.](https://portal.azure.com)  Abra o banco de dados relevante. No topo da página de **Auditoria** da base de dados, clique em **Ver registos**de auditoria .

    ![Exibir logs de auditoria](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Em seguida, você tem duas maneiras de exibir os logs:
    
    Clicar no **Log Analytics** no topo da página de **registos** da Auditoria abrirá a vista De Registos no espaço de trabalho do Log Analytics, onde pode personalizar o intervalo de tempo e a consulta de pesquisa.
    
    ![abrir no espaço de trabalho Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Clicar no painel de **instrumentos de visualização** na parte superior da página de **registos** da Auditoria abrirá um dashboard exibindo informações de registos de auditoria, onde poderá perfurar insights de segurança, acesso a dados sensíveis e muito mais. Este painel foi projetado para ajudá-lo a obter informações de segurança para seus dados.
    Você também pode personalizar o intervalo de tempo e a consulta de pesquisa. 
    ![ver o painel de instrumentos de análise de registo](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Painel do Log Analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics informações de segurança](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Como alternativa, você também pode acessar os logs de auditoria na folha Log Analytics. Abra o seu espaço de trabalho Log Analytics e sob secção **geral,** clique em **Registos**. Pode começar com uma consulta simples, como: *pesquisar "SQLSecurityAuditEvents"* para ver os registos de auditoria.
    A partir daqui, também pode utilizar registos do [Monitor Azure](../log-analytics/log-analytics-log-search.md) para executar pesquisas avançadas nos seus dados de registo de auditoria. Os logs de Azure Monitor fornecem informações operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar rapidamente milhões de registros em todas as suas cargas de trabalho e servidores. Para obter informações adicionais úteis sobre o idioma de pesquisa de registos do Monitor Azure e comandos, consulte a referência de pesquisa de [registos do Monitor Azure](../log-analytics/log-analytics-log-search.md).

Se você optar por gravar logs de auditoria no Hub de eventos:

- Para consumir dados de logs de auditoria do hub de eventos, você precisará configurar um fluxo para consumir eventos e gravá-los em um destino. Para mais informações, consulte a [Documentação do Azure Event Hubs](../event-hubs/index.yml).
- Os registos de auditoria no Event Hub são capturados no corpo de eventos [Apache Avro](https://avro.apache.org/) e armazenados usando formatação JSON com codificação UTF-8. Para ler os registos de auditoria, pode utilizar [ferramentas Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas semelhantes que processem este formato.

Se você optar por gravar logs de auditoria em uma conta de armazenamento do Azure, há vários métodos que podem ser usados para exibir os logs:

> [!NOTE] 
> A auditoria às [réplicas apenas](sql-database-read-scale-out.md) de leitura está ativada automaticamente. Para mais detalhes sobre a hierarquia das pastas de armazenamento, nomeação de convenções e formato de registo, consulte o Formato de Registo de Auditoria da Base de [Dados SQL](sql-database-audit-log-format.md). 

- Os logs de auditoria são agregados na conta que você escolheu durante a instalação. Pode explorar registos de auditoria utilizando uma ferramenta como o [Azure Storage Explorer](https://storageexplorer.com/). No armazenamento do Azure, os registos de auditoria são guardados como uma coleção de ficheiros blob dentro de um recipiente chamado **sqldbauditlogs**. Para mais detalhes sobre a hierarquia das pastas de armazenamento, nomeação de convenções e formato de registo, consulte o Formato de Registo de Auditoria da Base de [Dados SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Utilize o [portal Azure.](https://portal.azure.com)  Abra o banco de dados relevante. No topo da página de **Auditoria** da base de dados, clique em **Ver registos**de auditoria .

    ![Painel de navegação][7]

    **Os registos** de auditoria abrem,a partir do qual poderá ver os registos.

  - Pode visualizar datas específicas clicando em **Filtro** no topo da página de registos da **Auditoria.**
  - Pode alternar entre os registos de auditoria que foram criados pela política de auditoria do *servidor* e a política de auditoria da base de *dados,* toggling **Audit Source**.
  - Só pode ver registos de auditoria relacionados com injeção SQL verificando **apenas registos de auditoria para a caixa de verificação de injeções SQL.**

       ![Painel de navegação][8]

- Utilize a função do sistema **sys.fn_get_audit_file** (T-SQL) para devolver os dados de registo de auditoria em formato tabular. Para obter mais informações sobre a utilização desta função, consulte [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Utilize **ficheiros de auditoria** de fusão no Estúdio de Gestão de Servidores SQL (começando com SSMS 17):
    1. No menu SSMS, selecione **File** > **Abrir** > **Ficheiros de Auditoria**de Fusão .

        ![Painel de navegação][9]
    2. A caixa de diálogo **Add Audit Files** abre. Selecione uma das opções **Add** para escolher se deve fundir ficheiros de auditoria a partir de um disco local ou importá-los a partir do Armazenamento Azure. Você deve fornecer os detalhes do armazenamento do Azure e a chave de conta.

    3. Depois de todos os ficheiros a fundir terem sido adicionados, clique em **OK** para completar a operação de fusão.

    4. O arquivo mesclado é aberto no SSMS, no qual você pode exibi-lo e analisá-lo, bem como exportá-lo para um arquivo XEL ou CSV ou para uma tabela.

- Use Power BI. Você pode exibir e analisar dados de log de auditoria em Power BI. Para mais informações e para aceder a um modelo descarregado, consulte analisar dados de registo de [auditoria no Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Descarregue ficheiros de registo do seu recipiente de blob Azure Storage através do portal ou utilizando uma ferramenta como [o Azure Storage Explorer](https://storageexplorer.com/).
  - Depois de ter baixado um arquivo de log localmente, clique duas vezes no arquivo para abrir, exibir e analisar os logs no SSMS.
  - Você também pode baixar vários arquivos simultaneamente por meio de Gerenciador de Armazenamento do Azure. Para tal, clique numa subpasta específica e selecione **Guardar de modo** a guardar numa pasta local.

- Métodos adicionais:

  - Depois de baixar vários arquivos ou uma subpasta que contém arquivos de log, você pode mesclá-los localmente conforme descrito nas instruções de arquivos de auditoria de mesclagem do SSMS descritas anteriormente.
  - Exibir logs de auditoria de blob programaticamente:

    - [Consulta Ficheiros de Eventos Estendidos](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) utilizando powerShell.

## <a id="subheading-5"></a>Práticas de produção

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditoria de bases de dados geo-replicadas</a>

Com bancos de dados replicados geograficamente, quando você habilita a auditoria no banco de dados primário, o banco de dados secundário terá uma política de auditoria idêntica. Também é possível configurar auditorias na base de dados secundária, permitindo a auditoria no **servidor secundário,** independentemente da base de dados primária.

- Nível de servidor **(recomendado**): Ligue a auditoria tanto no **servidor primário** como no **servidor secundário** - as bases de dados primárias e secundárias serão cada uma auditada independentemente com base na respetiva política de nível de servidor.
- Nível de banco de dados: a auditoria no nível de banco de dados para bancos de dados secundários só pode ser definida a partir das configurações de auditoria do banco de dados primário.
  - A auditoria deve ser ativada na *própria base de dados primária,* não no servidor.
  - Após a habilitação da auditoria no banco de dados primário, ela também será habilitada no banco de dados secundário.

    >[!IMPORTANT]
    >Com a auditoria no nível do banco de dados, as configurações de armazenamento para o banco de dados secundário serão idênticas às do banco de dados primário, causando o tráfego entre regiões. É recomendável que você habilite apenas a auditoria no nível do servidor e deixe a auditoria no nível do banco de dados desabilitada para todos os bancos.

### <a id="subheading-6">Regeneração da chave de armazenamento</a>

Em produção, é provável que você atualize suas chaves de armazenamento periodicamente. Ao gravar logs de auditoria no armazenamento do Azure, você precisa salvar novamente sua política de auditoria ao atualizar suas chaves. O processo é o seguinte:

1. Detalhes de **armazenamento**abertos. Na caixa de acesso ao **armazenamento,** selecione **Secundário**, e clique **EM OK**. Em seguida, clique em **Guardar** no topo da página de configuração de auditoria.

    ![Painel de navegação][5]
2. Vá para a página de configuração de armazenamento e regenere a chave de acesso primária.

    ![Painel de navegação][6]
3. Volte para a página de configuração de auditoria, mude a chave de acesso de armazenamento de secundário para primário e, em seguida, clique EM **OK**. Em seguida, clique em **Guardar** no topo da página de configuração de auditoria.
4. Volte para a página de configuração de armazenamento e regenere a chave de acesso secundária (em preparação para o ciclo de atualização da próxima chave).

## <a name="additional-information"></a>Informações adicionais

- Para mais detalhes sobre o formato de registo, a hierarquia da pasta de armazenamento e as convenções de nomeação, consulte a Referência do Formato de Registo de [Auditoria Blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > O Azure SQL Database Audit armazena 4000 caracteres de dados para campos de caractere em um registro de auditoria. Quando a **declaração** ou os valores **data_sensitivity_information** devolvidos de uma ação auditável contiverem mais de 4000 caracteres, quaisquer dados para além dos primeiros 4000 caracteres serão **truncados e não auditados.**

- Os registos de auditoria são escritos para **Append Blobs** num armazenamento Azure Blob na sua subscrição Azure:
  - **O Armazenamento Premium** não é **atualmente suportado** por Append Blobs.
  - **O armazenamento em VNet** não é **suportado**atualmente.

- A política de auditoria padrão inclui todas as ações e o conjunto de grupos de ações a seguir, que auditará todas as consultas e procedimentos armazenados executados no banco de dados, bem como logons com falha e com êxito:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Pode configurar a auditoria para diferentes tipos de ações e grupos de ação utilizando o PowerShell, conforme descrito na auditoria da base de dados Manage SQL utilizando a secção [Azure PowerShell.](#subheading-7)

- Ao utilizar a Autenticação AAD, os registos de logins falhados *não* aparecerão no registo de auditoria sQL. Para ver registos de auditoria de login falhados, é necessário visitar o [portal azure Ative Diretório,]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)que regista detalhes destes eventos.

- A auditoria do banco de dados SQL do Azure é otimizada para disponibilidade & desempenho. Durante uma atividade muito alta, o banco de dados SQL do Azure permite que as operações continuem e não registrem alguns eventos auditados.

- Para configurar auditoriaivel imutável na conta de armazenamento, consulte Permitir que as bolhas de [apêndice protegidos escrevam](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage#allow-protected-append-blobs-writes). Por favor, note que o nome do recipiente para Auditoria é **sqldbauditlogs**.

    > [!IMPORTANT]
    > A configuração permitir gravações de blobs de acréscimo protegidos na retenção baseada em tempo está disponível no momento e visível somente nas seguintes regiões:
    > - E.U.A. Leste
    > - E.U.A. Centro-Sul
    > - EUA Oeste 2


## <a id="subheading-7"></a>Gerir a auditoria do Servidor E Base de Dados Azure SQL utilizando o Azure PowerShell

**Cmdlets PowerShell (incluindo o suporte da cláusula WHERE para filtragem adicional)** :

- [Criar ou atualizar política de auditoria da base de dados (Set-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Obtenha política de auditoria de base de dados (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Obtenha a Política de Auditoria do Servidor (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Remover política de auditoria da base de dados (Remover-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Remover a Política de Auditoria do Servidor (Remover-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

Para um exemplo de script, consulte a deteção de [auditoria e ameaça de Configuração utilizando powerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-8"></a>Gerir a auditoria do Servidor E QQL azure e da base de dados utilizando a API REST

**API DE REPOUSO:**

- [Criar ou atualizar política de auditoria de bases de dados](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Criar ou atualizar a política de auditoria do servidor](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter Política de Auditoria de Base de Dados](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Obter Política de Auditoria do Servidor](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Política estendida com suporte de cláusula WHERE para filtragem adicional:

- [Criar ou atualizar a política de auditoria alargada da base *de* dados](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Criar ou atualizar política de auditoria *alargada* do servidor](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter Política de Auditoria *Alargada de* Base de Dados](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Obtenha política de auditoria *alargada do* servidor](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-9"></a>Gerir a auditoria do Servidor EQL Azur e da Base de Dados utilizando modelos de Gestor de Recursos Azure

Pode gerir a auditoria da base de dados Azure SQL utilizando modelos do Gestor de [Recursos Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) como mostram estes exemplos:

- [Implemente um Servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria na conta de armazenamento da Blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implemente um Servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria para Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implemente um Servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria para Centros de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

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
