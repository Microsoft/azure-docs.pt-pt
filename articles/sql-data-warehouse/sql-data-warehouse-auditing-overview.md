---
title: Auditoria no armazém de dados SQL do Azure | Documentos da Microsoft
description: Saiba mais sobre a auditoria e como configurar a auditoria no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d2093c5b4c07e6e62df4d1f52a7fbe6e12a91ea0
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217058"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Auditoria no armazém de dados SQL do Azure

Saiba mais sobre a auditoria e como configurar a auditoria no Azure SQL Data Warehouse.

## <a name="what-is-auditing"></a>O que é a auditoria?
Auditoria de SQL Data Warehouse permite-lhe a registo de registo de eventos na base de dados para uma auditoria na sua conta de armazenamento do Azure. A auditoria pode ajudá-lo a manter a conformidade regulamentar, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações empresariais ou suspeitas de violações de segurança.

Ferramentas de auditoria permitem e facilitam a aderência aos padrões de conformidade, mas não garantem a conformidade. Para obter mais informações sobre o Azure programas de conformidade de padrões esse suporte, consulte a [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/compliance/).

## <a id="subheading-1"></a>Noções básicas de auditoria
A auditoria de base de dados SQL Data Warehouse permite-lhe:

* **Reter** uma trilha de auditoria de eventos selecionados. Pode definir categorias de ações de base de dados para serem auditados.
* **Relatório** na atividade de base de dados. Pode utilizar um dashboard e relatórios pré-configurados para começar rapidamente com a atividade e relatórios de eventos.
* **Analisar** relatórios. Pode encontrar eventos suspeitos, atividade invulgar e tendências.

Registos de auditoria são armazenados na sua conta de armazenamento do Azure. Pode definir um período de retenção do registo de auditoria.


## <a id="subheading-4"></a>Definir o nível de servidor vs. a política de auditoria ao nível do banco de dados

Pode ser definida uma política de auditoria para uma base de dados específica ou como uma política de servidor predefinida:

* Uma política de servidor **aplica-se a bases de dados de contas existentes e recentemente criados** no servidor.

* Se *auditoria de Blobs do servidor estiver ativada*, ele *sempre aplica-se à base de dados*. A base de dados vai ser auditada, independentemente das definições de auditoria da base de dados.

* Ativar a auditoria na base de dados, além de ativá-la no servidor, faz *não* substituir ou alterar quaisquer das definições de auditoria de BLOBs de servidor. Ambas as auditorias existirão lado a lado. Em outras palavras, a base de dados é auditada duas vezes em paralelo; vez pela política de servidor e uma vez pela política de base de dados.

> [!NOTE]
> Recomendamos que ative **auditoria de BLOBs apenas ao nível do servidor** e deixar a auditoria ao nível do banco de dados desativada para todas as bases de dados.
> Deve evitar a ativar a auditoria de servidor e base de dados de auditoria em conjunto, a menos que:
> * Pretende usar outra *conta de armazenamento* ou *período de retenção* para uma base de dados específico.
> * Quer categorias para um banco de dados específico que diferem do restante dos bancos de dados no servidor ou de tipos de eventos de auditoria. Por exemplo, poderá ter inserções de tabela que precisam de ser auditadas apenas para uma base de dados específico.
> * Que pretende utilizar a deteção de ameaças, o que está atualmente apenas suportado com a auditoria ao nível da base de dados.

> [!IMPORTANT]
>Ativação da auditoria num Azure SQL Data Warehouse, ou num servidor que tenha um Azure SQL Data Warehouse, **irá resultar no armazém de dados que está sendo continuado**, mesmo no caso em que ele foi anteriormente colocada em pausa. **Certifique-se de colocar em pausa o armazém de dados novamente depois de ativar a auditoria**.

## <a id="subheading-5"></a>Configurar a auditoria ao nível do servidor para todas as bases de dados

Uma política de auditoria de servidor aplica-se ao **bases de dados de contas existentes e recentemente criados** no servidor.

A seguinte secção descreve a configuração de auditoria no portal do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Vá para o **do SQL server** que deseja auditar (importante, certifique-se de que está a visualizar o SQL server, não um específica da base de dados/armazém de dados). Na **Security** menu, selecione **deteção de ameaças e auditoria**.

    ![Painel de navegação][6]
4. Na *deteção de ameaças e auditoria* painel, para **auditoria** selecionar **ON**. Esta política de auditoria se aplica às bases de dados recém-criado e todas as existentes neste servidor.

    ![Painel de navegação][7]
5. Para abrir o **armazenamento de registos de auditoria** painel, selecione **detalhes de armazenamento**. Selecione ou crie a conta de armazenamento do Azure em que registos serão guardados e, em seguida, selecione o período de retenção (registos antigos serão eliminados). Em seguida, clique em **OK**.

    ![Painel de navegação][8]

    > [!IMPORTANT]
    > Registos de auditoria são escritos **Blobs de acréscimo** no armazenamento de Blobs do Azure na sua subscrição do Azure.
    >
    > - Todos os tipos de armazenamento (v1, v2, blob) são suportados.
    > - Todas as configurações de replicação de armazenamento são suportadas.
    > - **O armazenamento Premium** está atualmente **nepodporuje**.
    > - **Armazenamento na VNet** está atualmente **nepodporuje**.
    > - **Armazenamento protegido por uma Firewall** está atualmente **não suportado**

8. Clique em **Guardar**.



## <a id="subheading-2"></a>Configurar a auditoria de banco de dados para uma base de dados

Pode definir uma política de auditoria para uma base de dados específica ou como uma política de servidor predefinida.

Recomenda-se vivamente a utilização de auditoria ao nível do servidor e não ao nível da base de dados de auditoria, conforme descrito em [definir ao nível do servidor vs. a política de auditoria ao nível do banco de dados](#subheading-4)

Antes de configurar a auditoria, auditoria de verificação se estiver a utilizar um ["Clientes de nível inferior"](sql-data-warehouse-auditing-downlevel-clients.md).


1. Iniciar o [portal do Azure](https://portal.azure.com).
2. Aceda a **definições** para que deseja auditar o SQL Data Warehouse. Selecione **deteção de ameaças e auditoria**.

    ![][1]
3. Em seguida, ativar a auditoria ao clicar o **ON** botão.

    ![][3]
4. No painel de configuração de auditoria, selecione **detalhes de armazenamento** para abrir o painel de armazenamento de registos de auditoria. Selecione a conta de armazenamento do Azure para os registos e o período de retenção.
    >[!TIP]
    >Utilize a mesma conta de armazenamento para todas as bases de dados auditadas para tirar o máximo proveito os modelos de relatórios pré-configurados.

    ![][4]

5. Clique nas **OK** botão para guardar a configuração de detalhes de armazenamento.
6. Sob **registo ao evento**, clique em **êxito** e **falha** para registar todos os eventos ou escolha categorias de eventos individuais.
7. Se estiver a configurar auditoria para uma base de dados, terá de alterar a cadeia de ligação do seu cliente para garantir a auditoria de dados é capturado corretamente. Verifique os [modificar FQDN do servidor na cadeia de ligação](sql-data-warehouse-auditing-downlevel-clients.md) tópico para ligações de cliente de nível inferior.
8. Clique em **OK**.

## <a id="subheading-3"></a>Analisar registos de auditoria e relatórios

### <a name="server-level-policy-audit-logs"></a>Registos de auditoria de política ao nível do servidor
Registos de auditoria ao nível do servidor são escritos **Blobs de acréscimo** num armazenamento de Blobs do Azure na sua subscrição do Azure. Eles são salvos como uma coleção de ficheiros de blob num contentor com o nome **sqldbauditlogs**.

Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, as convenções de nomenclatura e formato de registo, consulte a [referência de formato de registo de auditoria de Blob](https://go.microsoft.com/fwlink/?linkid=829599).

Existem vários métodos que pode utilizar para ver os registos de auditoria de BLOBs:

* Uso **intercalar ficheiros de auditoria** no SQL Server Management Studio (começando com o SSMS 17):
    1. No menu do SSMS, selecione **arquivo** > **abrir** > **intercalar ficheiros de auditoria**.

    2. O **adicionar ficheiros de auditoria** é aberta a caixa de diálogo. Selecione uma da **adicionar** opções de escolha se pretende intercalar os ficheiros de auditoria a partir de um disco local ou importá-los do armazenamento do Azure. Tem de fornecer os detalhes de armazenamento do Azure e a chave da conta.

    3. Depois de todos os ficheiros para intercalar tiverem sido adicionados, clique em **OK** para concluir a operação de intercalação.

    4. O ficheiro mesclado é aberta no SSMS, onde pode ver e analisá-lo, bem como exportá-lo para um ficheiro XEL ou CSV ou para uma tabela.

* Utilize o [sincronizar aplicações](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) que criamos. Ele é executado no Azure e utiliza o log analytics registos de auditoria de APIs públicas para enviar por push SQL sobre os registos do Azure Monitor. A aplicação de sincronização envia registos de auditoria SQL para registos do Azure Monitor para consumo através do dashboard de análise de registo.

* Utilize o Power BI. Pode ver e analisar dados de registo de auditoria no Power BI. Saiba mais sobre [Power BI e o acesso de um modelo que pode ser baixado](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Transferir os ficheiros de registo do contentor de blob de armazenamento do Azure através do portal ou com uma ferramenta como [Explorador de armazenamento do Azure](http://storageexplorer.com/).
    * Depois de transferir um ficheiro de registo localmente, pode clicar duas vezes o arquivo para abrir, visualizar e analisar os registos no SSMS.
    * Também pode transferir vários arquivos simultaneamente através do Explorador de armazenamento do Azure. Uma subpasta específica com o botão direito e selecione **guardar como** salvar numa pasta local.

* Métodos adicionais:
   * Depois de baixar vários ficheiros ou numa subpasta que contém ficheiros de registo, é possível intercalá-los localmente conforme descrito nas instruções de ficheiros de auditoria de intercalação do SSMS descritas anteriormente.

   * Auditoria de BLOBs de modo de exibição aceder programaticamente aos registos:

     * Utilize o [leitor de eventos expandidos](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) biblioteca do c#.
     * [Ficheiros de eventos expandidos de consulta](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) com o PowerShell.



<br>
### <a name="database-level-policy-audit-logs"></a>Registos de auditoria de política ao nível da base de dados
Registos de auditoria ao nível da base de dados são agregados numa coleção de tabelas do Store com um **SQLDBAuditLogs** prefixo na conta de armazenamento do Azure que escolheu durante a configuração. Pode ver ficheiros de registo utilizando uma ferramenta como [Explorador de armazenamento do Azure](http://azurestorageexplorer.codeplex.com).

Um modelo de relatório de dashboard pré-configurada está disponível como um [planilha do Excel que pode ser baixada](https://go.microsoft.com/fwlink/?LinkId=403540) para o ajudar a analisar rapidamente os dados de registo. Para utilizar o modelo nos seus registos de auditoria, terá de Excel 2013 ou posterior e o Power Query, que pode [baixe aqui](https://www.microsoft.com/download/details.aspx?id=39379).

O modelo tem dados de exemplo fictícia nela, e pode configurar o Power Query para importar o seu registo de auditoria diretamente a partir da sua conta de armazenamento do Azure.

## <a id="subheading-4"></a>Regeneração da chave de armazenamento
Na produção, é provável que atualizar as chaves de armazenamento periodicamente. Ao atualizar as suas chaves, tem de guardar a política. O processo é o seguinte:

1. No painel de configuração, o que é descrito na configuração anterior secção de auditoria, a auditoria alterar o **chave de acesso de armazenamento** partir *primário* para *secundário* e  **GUARDAR**.

   ![][4]
2. Aceda ao painel de configuração de armazenamento e **regenerar** a *chave de acesso primária*.
3. Volte ao painel de configuração de auditoria,
4. mudar o **chave de acesso de armazenamento** partir *secundário* para *primário* e prima **guardar**.
4. Volte para o armazenamento da interface do Usuário e **regenerar** a *chave de acesso secundária* (como preparação para a próxima chaves atualizar ciclo.

## <a id="subheading-5"></a>Automatização (PowerShell/REST API)
Também pode configurar a auditoria no Azure SQL Data Warehouse, utilizando as seguintes ferramentas de automatização:

* **Cmdlets do PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Suportam de clientes de nível inferior para máscara de dados de auditorias e dinâmicos
Auditoria funcione com os clientes de SQL que suportem redirecionamento de TDS.

Qualquer cliente que implementa o TDS 7.4 também deve suportar o redirecionamento. As exceções a isso incluem JDBC 4.0 em que a funcionalidade de redirecionamento não é totalmente suportada e Tedious para node. js no qual o redirecionamento não foi implementado.

Para "Clientes de nível inferior" que suportam o TDS versão 7.3 e abaixo, para modificar o FQDN do servidor na cadeia de ligação da seguinte forma:

- Original FQDN do servidor na cadeia de ligação: <*nome do servidor*>. database.windows.net
- Modificado FQDN do servidor na cadeia de ligação: <*nome do servidor*> .database. **seguro**. windows.net

Uma lista parcial de "Clientes de nível inferior" inclui:

* .NET 4.0 e inferior,
* ODBC 10.0 e abaixo.
* JDBC (embora JDBC suporta TDS 7.4, a funcionalidade de redirecionamento de TDS não é totalmente suportada)
* Entediante (para node. js)

**Comentário:** Modificação do FQDN do servidor anterior pode ser útil também para aplicar uma política de auditoria do SQL Server ao nível de mensagens em fila sem a necessidade de um passo de configuração em cada base de dados (atenuação temporária).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
