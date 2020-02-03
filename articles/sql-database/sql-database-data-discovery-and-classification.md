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
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: dda65c94671044f3c5a569a3f9753951de9eee3a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717678"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Classificação & do banco de dados SQL do Azure e SQL Data Warehouse data Discovery

A descoberta e classificação de dados fornece capacidades avançadas incorporadas na Base de Dados Azure SQL para **descobrir,** **classificar,** **rotulação** & **reportar** os dados sensíveis nas suas bases de dados.

Descobrindo e classificando seus dados mais confidenciais (negócios, financeiros, de saúde, PII (dados de identificação pessoal) e assim por diante.) pode desempenhar uma função dinâmica em sua estatura de proteção de informações organizacionais. Pode servir de infraestrutura para:

- Ajudar a cumprir as normas de privacidade dos dados e os requisitos de conformidade regulamentar.
- Vários cenários de segurança, como monitoramento (auditoria) e alertas de acesso anormal a dados confidenciais.
- Controlar o acesso e reforçar a segurança das bases de dados que contenham dados altamente sensíveis.

A descoberta e classificação de dados faz parte da oferta avançada de segurança de [dados](sql-database-advanced-data-security.md) (ADS), que é um pacote unificado para capacidades avançadas de segurança SQL. a classificação de & de descoberta de dados pode ser acessada e gerenciada por meio do portal de anúncios do SQL central.

> [!NOTE]
> Este documento está relacionado ao banco de dados SQL do Azure e SQL Data Warehouse do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse. Para o Servidor SQL (nas instalações), consulte A Descoberta e Classificação de [Dados SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>O que é a descoberta e classificação de dados

A classificação de & de descoberta de dados apresenta um conjunto de serviços avançados e novos recursos do SQL, formando um novo paradigma da proteção de informações do SQL destinado à proteção dos dados, não apenas ao banco de dado:

- **Descobertae recomendações**

  O mecanismo de classificação verifica o banco de dados e identifica as colunas que contenham os possíveis confidenciais. Em seguida, fornece-lhe uma maneira fácil de rever e aplicar as recomendações de classificação apropriadas através do portal Azure.

- **Rotulagem**

  Os rótulos de classificação de confidencialidade podem ser marcados persistentemente em colunas usando novos atributos de metadados de classificação introduzidos no mecanismo do SQL. Estes metadados podem então ser utilizados para cenários avançados de auditoria e proteção baseados em sensibilidade.

- **Sensibilidade do conjunto de resultados de consulta**

  A sensibilidade do conjunto de resultados da consulta é calculada em tempo real para fins de auditoria.

- **Visibility** (Visibilidade)

  O estado de classificação do banco de dados pode ser exibido em um painel detalhado no Portal. Além disso, pode fazer o download de um relatório (em formato Excel) para ser utilizado para fins de conformidade e auditoria, bem como outras necessidades.

## <a id="subheading-2"></a>Descubra, classifique e classifique colunas sensíveis

A seção a seguir descreve as etapas para descobrir, classificar e rotular colunas que contêm dados confidenciais em seu banco de dados, bem como exibir o estado atual de classificação do banco de dados e exportar relatórios.

A classificação inclui dois atributos de metadados:

- Rótulos – os principais atributos de classificação, usados para definir o nível de sensibilidade dos dados armazenados na coluna.  
- Tipos de informações – fornecem granularidade adicional ao tipo de dados armazenados na coluna.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definir e personalizar sua taxonomia de classificação

A classificação de & do SQL Data Discovery vem com um conjunto interno de rótulos de sensibilidade e um conjunto interno de tipos de informações e lógica de descoberta. Agora você tem a capacidade de personalizar essa taxonomia e definir um conjunto e uma classificação de constructos de classificação especificamente para seu ambiente.

A definição e a personalização de sua taxonomia de classificação são feitas em um local central para todo o seu locatário do Azure. Essa localização é no Centro de [Segurança Azure,](https://docs.microsoft.com/azure/security-center/security-center-intro)como parte da sua Política de Segurança. Somente alguém com direitos administrativos no grupo de gerenciamento raiz do locatário pode executar essa tarefa.

Como parte do gerenciamento de políticas da proteção de informações, você pode definir rótulos personalizados, classificá-los e associá-los a um conjunto selecionado de tipos de informações. Também pode adicionar os seus próprios tipos de informação personalizados e configurá-los com padrões de cordas, que são adicionados à lógica de descoberta para identificar este tipo de dados nas suas bases de dados.
Saiba mais sobre personalizar e gerir a sua política na [política de Proteção de Informação como orientar](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Depois que a política de todo o locatário tiver sido definida, você poderá continuar com a classificação de bancos de dados individuais usando sua política personalizada.

## <a name="classify-your-sql-database"></a>Classificar seu banco de dados SQL

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Navegue para **a Segurança Avançada** de Dados sob a direção de Segurança no painel de base de dados Azure SQL. Clique para ativar a segurança avançada de dados e, em seguida, clique na **discovery & classification** card.

   ![Verificar um banco de dados](./media/sql-data-discovery-and-classification/data_classification.png)

3. O separador **Overview** inclui um resumo do estado de classificação atual da base de dados, incluindo uma lista detalhada de todas as colunas classificadas, que também pode filtrar para visualizar apenas peças de esquemas específicas, tipos de informação e etiquetas. Se ainda não classificou nenhuma coluna, [salte para o passo 5](#step-5).

   ![Resumo do estado de classificação atual](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Para fazer o download de um relatório em formato Excel, clique na opção **Export** no menu superior da janela.

   ![Exportação para Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Para começar a classificar os seus dados, clique no **separador Classificação** na parte superior da janela.

    ![Classificar dados](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. O motor de classificação verifica a sua base de dados para colunas que contenham dados potencialmente sensíveis e fornece uma lista de **classificações recomendadas**de colunas . Para exibir e aplicar as recomendações de classificação:

   - Para exibir a lista de classificações de coluna recomendadas, clique no painel recomendações na parte inferior da janela:

      ![Classifique os seus dados](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Examine a lista de recomendações – para aceitar uma recomendação para uma coluna específica, marque a caixa de seleção na coluna à esquerda da linha relevante. Também pode marcar *todas as recomendações aceites,* verificando a caixa de verificação no cabeçalho da tabela de recomendações.

       ![Examinar lista de recomendações](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Para aplicar as recomendações selecionadas, clique no botão de **recomendações do Aceito** azul.

      ![Aplicar recomendações](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Também pode **classificar manualmente** as colunas como uma alternativa, ou além disso, à classificação baseada em recomendações:

   - Clique em **Adicionar classificação** no menu superior da janela.

      ![Adicionar classificação manualmente](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - Na janela de contexto que é aberta, selecione o esquema > tabela > coluna que você deseja classificar e o tipo de informação e o rótulo de sensibilidade. Em seguida, clique no botão de **classificação** azul Adicionar na parte inferior da janela de contexto.

      ![Selecionar coluna a ser classificada](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Para completar a sua classificação e rotular persistentemente (etiquetar) as colunas de base de dados com os novos metadados de classificação, clique em **Guardar** no menu superior da janela.

   ![Guardar](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Auditoria ao acesso a dados sensíveis

Um aspecto importante do paradigma da proteção de informações é a capacidade de monitorar o acesso a dados confidenciais. A auditoria da Base de [Dados Azure SQL](sql-database-auditing.md) foi melhorada para incluir um novo campo no registo de auditoria denominado *data_sensitivity_information,* que regista as classificações de sensibilidade (etiquetas) dos dados reais que foram devolvidos pela consulta.

![Log de auditoria](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Permissões

As seguintes funções incorporadas podem ler a classificação de dados de uma base de dados Azure SQL: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` e `User Access Administrator`.

As seguintes funções incorporadas podem modificar a classificação de dados de uma base de dados Azure SQL: `Owner`, `Contributor`, `SQL Security Manager`.

Saiba mais sobre [rBAC para recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Gerir classificações

# <a name="t-sqltabazure-t-sql"></a>[T-SQL](#tab/azure-t-sql)
Você pode usar o T-SQL para adicionar/remover classificações de coluna, bem como recuperar todas as classificações para todo o banco de dados.

> [!NOTE]
> Ao usar o T-SQL para gerenciar rótulos, não há nenhuma validação que os rótulos adicionados a uma coluna existam na política de proteção de informações organizacionais (o conjunto de rótulos que aparecem nas recomendações do Portal). Portanto, cabe a você validar isso.

- Adicione/atualize a classificação de uma ou mais colunas: [CLASSIFICAÇÃO](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql) DE SENSIBILIDADE ADD
- Retire a classificação de uma ou mais colunas: [CLASSIFICAÇÃO DE SENSIBILIDADE DE QUEDA](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Ver todas as classificações na base de dados: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apistabazure-rest-api"></a>[Afídeos de descanso](#tab/azure-rest-api)
Você pode usar as APIs REST para gerenciar programaticamente as classificações e recomendações. As APIs REST publicadas dão suporte às seguintes operações:

- [Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - Cria ou atualiza o rótulo de sensibilidade de uma determinada coluna
- [Excluir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) - Elimina o rótulo de sensibilidade de uma determinada coluna
- [Recomendação de desativação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) - Desativa recomendações de sensibilidade numa determinada coluna
- [Ativar recomendação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) - Permite recomendações de sensibilidade numa determinada coluna (as recomendações são ativadas por padrão em todas as colunas)
- Get - [Obtém](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) o rótulo de sensibilidade de uma dada coluna
- [Lista Current By Database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) - Obtém os rótulos de sensibilidade atuais de uma determinada base de dados
- [Lista Recomendada por Base](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) de Dados - Obtém os rótulos de sensibilidade recomendados de uma determinada base de dados

# <a name="powershell-cmdlettabazure-powelshell"></a>[PowerShell Cmdlet](#tab/azure-powelshell)
Você pode usar o PowerShell para gerenciar classificações e recomendações para o banco de dados SQL do Azure e Instância Gerenciada.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>Cmdlet do PowerShell para banco de dados SQL do Azure
- [Classificação de sensibilidade à base de dados Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Classificação de sensibilidade à base de dados set-AzSql](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Eliminação-AzSqlDatabaseSensibilidade](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Recomendação de Sensibilidade à Base de Dados Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Recomendação de sensibilidade Enable-AzSqlDatabaSe](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Recomendação de sensibilidade à base de dados de si-AzSql](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>Cmdlets do PowerShell para Instância Gerenciada
- [Classificação de sensibilidade à base de dados get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Classificação de sensibilidade à base de dados set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Eliminação-AzSqlInstanceDatabaseClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Recomendação de sensibilidade à base de dados Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Recomendação de sensibilidade à base de dados Enable-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Recomendação de sensibilidade acasos](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a id="subheading-6"></a>Passos seguintes

- Saiba mais sobre [a segurança avançada de dados.](sql-database-advanced-data-security.md)
- Considere configurar a Auditoria de Base de [Dados Azure SQL](sql-database-auditing.md) para monitorização e auditoria de acesso aos seus dados confidenciais confidenciais.
- Para uma apresentação no YouTube que inclua Data Discovery & Classification, consulte [Descobrir, classificar, rotular e proteger dados SQL  Dados Expostos](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
