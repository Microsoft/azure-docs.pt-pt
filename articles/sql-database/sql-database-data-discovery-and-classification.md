---
title: Deteção e classificação de dados
description: Classificação & do banco de dados SQL do Azure e data Discovery
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: b5f839cc6216eb12bfd0a86009ec49e987279d6e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889840"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Classificação & do banco de dados SQL do Azure e SQL Data Warehouse data Discovery

A classificação de & de descoberta de dados fornece recursos avançados incorporados ao banco de dado SQL do Azure para **descobrir**, **classificar**, **rotular** & **proteger** os dados confidenciais em seus bancos.

Descobrindo e classificando seus dados mais confidenciais (negócios, financeiros, de saúde, PII (dados de identificação pessoal) e assim por diante.) pode desempenhar uma função dinâmica em sua estatura de proteção de informações organizacionais. Ele pode servir como uma infraestrutura para:

- Ajudando a atender aos padrões de privacidade de dados e aos requisitos de conformidade regulatória.
- Vários cenários de segurança, como monitoramento (auditoria) e alertas de acesso anormal a dados confidenciais.
- Controlando o acesso e protegendo a segurança de bancos de dados que contenham um dado altamente confidencial.

A classificação de & de descoberta de dados faz parte da oferta do ADS ( [segurança de dados avançada](sql-database-advanced-data-security.md) ), que é um pacote unificado para recursos avançados de segurança do SQL. a classificação de & de descoberta de dados pode ser acessada e gerenciada por meio do portal de anúncios do SQL central.

> [!NOTE]
> Este documento está relacionado ao banco de dados SQL do Azure e SQL Data Warehouse do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse. Para SQL Server (no local), consulte [descoberta e classificação de dados SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>O que é descoberta de dados & classificação

A classificação de & de descoberta de dados apresenta um conjunto de serviços avançados e novos recursos do SQL, formando um novo paradigma da proteção de informações do SQL destinado à proteção dos dados, não apenas ao banco de dado:

- **Recomendações de & de descoberta**

  O mecanismo de classificação verifica o banco de dados e identifica as colunas que contenham os possíveis confidenciais. Em seguida, ele fornece uma maneira fácil de revisar e aplicar as recomendações de classificação apropriadas por meio do portal do Azure.

- **Rotulagem**

  Os rótulos de classificação de confidencialidade podem ser marcados persistentemente em colunas usando novos atributos de metadados de classificação introduzidos no mecanismo do SQL. Esses metadados podem ser utilizados para cenários de proteção e auditoria baseados em sensibilidade avançada.

- **Sensibilidade do conjunto de resultados da consulta**

  A sensibilidade do conjunto de resultados da consulta é calculada em tempo real para fins de auditoria.

- **Visibility** (Visibilidade)

  O estado de classificação do banco de dados pode ser exibido em um painel detalhado no Portal. Além disso, você pode baixar um relatório (no formato do Excel) a ser usado para fins de auditoria & de conformidade, bem como outras necessidades.

## <a id="subheading-2"></a>Descobrir, classificar & colunas confidenciais do rótulo

A seção a seguir descreve as etapas para descobrir, classificar e rotular colunas que contêm dados confidenciais em seu banco de dados, bem como exibir o estado atual de classificação do banco de dados e exportar relatórios.

A classificação inclui dois atributos de metadados:

- Rótulos – os principais atributos de classificação, usados para definir o nível de sensibilidade dos dados armazenados na coluna.  
- Tipos de informações – fornecem granularidade adicional ao tipo de dados armazenados na coluna.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definir e personalizar sua taxonomia de classificação

A classificação de & do SQL Data Discovery vem com um conjunto interno de rótulos de sensibilidade e um conjunto interno de tipos de informações e lógica de descoberta. Agora você tem a capacidade de personalizar essa taxonomia e definir um conjunto e uma classificação de constructos de classificação especificamente para seu ambiente.

A definição e a personalização de sua taxonomia de classificação são feitas em um local central para todo o seu locatário do Azure. Esse local está na [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), como parte de sua política de segurança. Somente alguém com direitos administrativos no grupo de gerenciamento raiz do locatário pode executar essa tarefa.

Como parte do gerenciamento de políticas da proteção de informações, você pode definir rótulos personalizados, classificá-los e associá-los a um conjunto selecionado de tipos de informações. Você também pode adicionar seus próprios tipos de informações personalizados e configurá-los com padrões de cadeia de caracteres, que são adicionados à lógica de descoberta para identificar esse tipo de dados em seus bancos.
Saiba mais sobre como personalizar e gerenciar sua política no [Guia de instruções da política de proteção de informações](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Depois que a política de todo o locatário tiver sido definida, você poderá continuar com a classificação de bancos de dados individuais usando sua política personalizada.

## <a name="classify-your-sql-database"></a>Classificar seu banco de dados SQL

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Navegue até **segurança de dados avançada** no título segurança no painel do banco de dados SQL do Azure. Clique para habilitar a segurança de dados avançada e clique no cartão de **classificação & de descoberta de dados** .

   ![Verificar um banco de dados](./media/sql-data-discovery-and-classification/data_classification.png)

3. A guia **visão geral** inclui um resumo do estado de classificação atual do banco de dados, incluindo uma lista detalhada de todas as colunas classificadas, que você também pode filtrar para exibir apenas partes de esquema específicas, tipos de informações e rótulos. Se você ainda não classificou nenhuma coluna, [pule para a etapa 5](#step-5).

   ![Resumo do estado de classificação atual](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Para baixar um relatório no formato do Excel, clique na opção **Exportar** no menu superior da janela.

   ![Exportar para o Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Para começar a classificar seus dados, clique na **guia classificação** na parte superior da janela.

    ![Classificar dados](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. O mecanismo de classificação verifica o banco de dados em busca de colunas que contenham dado potencialmente confidenciais e fornece uma lista de **classificações de coluna recomendadas**. Para exibir e aplicar as recomendações de classificação:

   - Para exibir a lista de classificações de coluna recomendadas, clique no painel recomendações na parte inferior da janela:

      ![Classificar seus dados](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Examine a lista de recomendações – para aceitar uma recomendação para uma coluna específica, marque a caixa de seleção na coluna à esquerda da linha relevante. Você também pode marcar *todas as recomendações* como aceitas marcando a caixa de seleção no cabeçalho da tabela de recomendações.

       ![Examinar lista de recomendações](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Para aplicar as recomendações selecionadas, clique no botão **aceitar recomendações selecionadas** azul.

      ![Aplicar recomendações](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Você também pode **classificar manualmente** as colunas como uma alternativa ou, além disso, para a classificação baseada em recomendação:

   - Clique em **Adicionar classificação** no menu superior da janela.

      ![Adicionar classificação manualmente](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - Na janela de contexto que é aberta, selecione o esquema > tabela > coluna que você deseja classificar e o tipo de informação e o rótulo de sensibilidade. Em seguida, clique no botão azul **Adicionar classificação** na parte inferior da janela de contexto.

      ![Selecionar coluna a ser classificada](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Para concluir sua classificação e rotular de forma persistente (marca) as colunas do banco de dados com os novos metadados de classificação, clique em **salvar** no menu superior da janela.

   ![Guardar](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Auditoria de acesso a dados confidenciais

Um aspecto importante do paradigma da proteção de informações é a capacidade de monitorar o acesso a dados confidenciais. A auditoria do banco de dados [SQL do Azure](sql-database-auditing.md) foi aprimorada para incluir um novo campo no log de auditoria chamado *data_sensitivity_information*, que registra as classificações de sensibilidade (rótulos) dos dados reais retornados pela consulta.

![Log de auditoria](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Gerenciar a classificação de dados usando o T-SQL

Você pode usar o T-SQL para adicionar/remover classificações de coluna, bem como recuperar todas as classificações para todo o banco de dados.

> [!NOTE]
> Ao usar o T-SQL para gerenciar rótulos, não há nenhuma validação que os rótulos adicionados a uma coluna existam na política de proteção de informações organizacionais (o conjunto de rótulos que aparecem nas recomendações do Portal). Portanto, cabe a você validar isso.

- Adicionar/atualizar a classificação de uma ou mais colunas: [Adicionar classificação de sensibilidade](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Remover a classificação de uma ou mais colunas: [remover classificação de sensibilidade](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Exibir todas as classificações no banco de dados: [Sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="manage-classifications-using-rest-apis"></a>Gerenciar classificações usando APIs REST

Você também pode usar as APIs REST para gerenciar programaticamente as classificações. As APIs REST publicadas dão suporte às seguintes operações:

- [Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – cria ou atualiza o rótulo de sensibilidade de uma determinada coluna
- [Excluir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) – exclui o rótulo de sensibilidade de uma determinada coluna
- [Desabilitar recomendação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) -desabilita as recomendações de sensibilidade em uma determinada coluna
- [Habilitar recomendação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) – habilita recomendações de sensibilidade em uma determinada coluna (as recomendações são habilitadas por padrão em todas as colunas)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) -Obtém o rótulo de sensibilidade de uma determinada coluna
- [Listar atual pelo banco de dados](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) -Obtém os rótulos de sensibilidade atuais de um determinado banco de dados

- [Lista recomendada pelo banco de dados](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) -Obtém os rótulos de sensibilidade recomendados de um determinado banco de dados

## <a name="manage-data-discovery-and-classification-using-azure-powershell"></a>Gerenciar a descoberta e a classificação de dados usando o Azure PowerShell

Você pode usar o PowerShell para obter todas as colunas recomendadas em um banco de dados SQL do Azure e uma instância gerenciada.

### <a name="powershell-cmdlets-for-azure-sql-database"></a>Cmdlets do PowerShell para o banco de dados SQL do Azure

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Habilitar-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Desabilitar-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>Cmdlets do PowerShell para instância gerenciada

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Habilitar-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Desabilitar-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

## <a name="permissions"></a>Permissões

As funções internas a seguir podem ler a classificação de dados de um Azure SQL Database: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` e `User Access Administrator`.

As funções internas a seguir podem modificar a classificação de dados de um Azure SQL Database: `Owner`, `Contributor``SQL Security Manager`.

Saiba mais sobre o [RBAC para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Passos seguintes

- Saiba mais sobre a [segurança de dados avançada](sql-database-advanced-data-security.md).
- Considere a configuração da auditoria do banco de dados [SQL do Azure](sql-database-auditing.md) para monitorar e auditar o acesso aos sua classificação confidenciais.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
