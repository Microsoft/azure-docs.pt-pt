---
title: Deteção e Classificação de Dados
description: Data Discovery & Classificação para Base de Dados Azure SQL e Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 599b2a280e386e49eb114f448f55b17ed7e823d7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616755"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Data Discovery & Classificação para Base de Dados Azure SQL e Azure Synapse Analytics

A Data Discovery & Classification está incorporada na Base de Dados Azure SQL. Fornece capacidades avançadas para descobrir, classificar, rotular e reportar os dados sensíveis nas suas bases de dados.

Os seus dados mais sensíveis podem incluir informações pessoais, financeiras, de saúde ou pessoais. Descobrir e classificar estes dados pode desempenhar um papel fundamental na abordagem de proteção de informação da sua organização. Pode funcionar como infraestrutura para:

- Ajudando a cumprir as normas de privacidade de dados e requisitos para o cumprimento regulamentar.
- Vários cenários de segurança, como a monitorização (auditoria) e o alerta sobre o acesso anómalo a dados sensíveis.
- Controlar o acesso e reforçar a segurança das bases de dados que contêm dados altamente sensíveis.

A Data Discovery & Classification faz parte da oferta avançada de Segurança de [Dados,](sql-database-advanced-data-security.md) que é um pacote unificado para capacidades avançadas de segurança SQL. Pode aceder e gerir a Data Discovery & Classificação através da secção central de **Segurança de Dados Avançados SQL** do portal Azure.

> [!NOTE]
> Este artigo diz respeito à Base de Dados Azure SQL e à Azure Synapse Analytics. Para a simplicidade, utilizamos aqui a Base de *Dados SQL* para se referir tanto à Base de Dados SQL como ao Synapse Azure. Para obter informações sobre o Servidor SQL (no local), consulte A Descoberta e Classificação de [Dados SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>O que é a Classificação & descoberta de dados?

Data Discovery & Classification introduz um conjunto de serviços avançados e novas capacidades de Base de Dados SQL. Constitui um novo paradigma de proteção da informação para a Base de Dados SQL, destinado a proteger os dados e não apenas a base de dados. O paradigma inclui:

- **Descoberta e recomendações:** O motor de classificação digitaliza a sua base de dados e identifica colunas que contêm dados potencialmente sensíveis. Em seguida, fornece-lhe uma maneira fácil de rever e aplicar a classificação recomendada através do portal Azure.

- **Rotulagem:** Pode aplicar etiquetas de classificação de sensibilidade persistentemente às colunas utilizando novos atributos de metadados que foram adicionados ao motor de base de dados SQL. Estes metadados podem então ser utilizados para cenários avançados de auditoria e proteção baseados em sensibilidade.

- **Sensibilidade ao resultado da consulta:** A sensibilidade de um conjunto de resultados de consulta é calculada em tempo real para efeitos de auditoria.

- **Visibilidade:** Pode ver o estado de classificação da base de dados num painel detalhado no portal Azure. Além disso, pode descarregar um relatório em formato Excel para utilizar para fins de conformidade e auditoria e outras necessidades.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Descubra, classifique e rotule colunas sensíveis

Esta secção descreve os passos para:

- Descobrir, classificar e rotular colunas que contenham dados sensíveis na sua base de dados.
- Visualizar o estado de classificação atual da sua base de dados e relatórios de exportação.

A classificação inclui dois atributos de metadados:

- **Etiquetas**: Os principais atributos de classificação, utilizados para definir o nível de sensibilidade dos dados armazenados na coluna.  
- Tipos de **informação**: Atributos que fornecem informações mais granulares sobre o tipo de dados armazenados na coluna.

### <a name="define-and-customize-your-classification-taxonomy"></a>Defina e personalize a sua taxonomia de classificação

Data Discovery & Classification vem com um conjunto incorporado de etiquetas de sensibilidade e um conjunto incorporado de tipos de informação e lógica de descoberta. Pode agora personalizar esta taxonomia e definir um conjunto e uma classificação de construções específicas para o seu ambiente.

Você define e personaliza a sua taxonomia de classificação em um lugar central para toda a sua organização Azure. Essa localização é no Centro de [Segurança Azure,](https://docs.microsoft.com/azure/security-center/security-center-intro)como parte da sua política de segurança. Só alguém com direitos administrativos sobre o grupo de gestão de raiz da organização pode fazer esta tarefa.

Como parte da gestão de políticas para a proteção de informação SQL, pode definir etiquetas personalizadas, classificá-las e associá-las a um conjunto selecionado de tipos de informação. Também pode adicionar os seus próprios tipos de informação personalizados e configurá-los com padrões de cordas. Os padrões são adicionados à lógica de descoberta para identificar este tipo de dados nas suas bases de dados.

Saiba mais sobre personalizar e gerir a sua política na política de proteção de [informação SQL como orientar](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Depois de definida a política em toda a organização, pode continuar a classificar bases de dados individuais utilizando a sua política personalizada.

### <a name="classify-your-sql-database"></a>Classifique a sua base de dados SQL

1. Vá ao [portal Azure.](https://portal.azure.com)

2. Dirija-se à **Advanced Data Security** sob a direção de **Segurança** no painel de bases de dados Azure SQL. Selecione **a segurança avançada de dados**e, em seguida, selecione o cartão de **classificação & Dedados.**

   ![Painel avançado de segurança de dados no portal Azure](./media/sql-data-discovery-and-classification/data_classification.png)

3. Na página de **classificação & descoberta** de dados, o separador **Overview** inclui um resumo do estado de classificação atual da base de dados. O resumo inclui uma lista detalhada de todas as colunas classificadas, que também pode filtrar para mostrar apenas peças específicas de esquema, tipos de informação e etiquetas. Se ainda não classificou nenhuma coluna, [salte para o passo 5](#step-5).

   ![Resumo do estado de classificação atual](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Para fazer o download de um relatório em formato Excel, selecione **Exportar** no menu superior do painel.

5. <a id="step-5"></a>Para começar a classificar os seus dados, selecione o separador **Classificação** na página **de classificação & descoberta** de dados.

    O motor de classificação verifica a sua base de dados para colunas que contenham dados potencialmente sensíveis e fornece uma lista de classificações recomendadas de colunas.

6. Ver e aplicar recomendações de classificação:

   - Para visualizar a lista de classificações recomendadas da coluna, selecione o painel de recomendações na parte inferior do painel.

   - Para aceitar uma recomendação para uma coluna específica, selecione a caixa de verificação na coluna esquerda da linha relevante. Para marcar todas as recomendações aceites, selecione a caixa de verificação mais à esquerda no cabeçalho da tabela de recomendações.

       ![Rever e selecionar a partir da lista de recomendações de classificação](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Para aplicar as recomendações selecionadas, **selecione Aceitar recomendações selecionadas**.

7. Também pode classificar as colunas manualmente, como alternativa ou para além da classificação baseada em recomendações:

   1. **Selecione Adicionar classificação** no menu superior do painel.

   1. Na janela de contexto que se abre, selecione o esquema, a tabela e a coluna que pretende classificar, e o tipo de informação e o rótulo de sensibilidade.

   1. **Selecione Adicionar classificação** na parte inferior da janela de contexto.

      ![Selecione uma coluna para classificar](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Para completar a sua classificação e rotular persistentemente (etiquetar) as colunas de base de dados com os novos metadados de classificação, selecione **Guardar** no menu superior da janela.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Acesso de auditoria a dados sensíveis

Um aspeto importante do paradigma da proteção da informação é a capacidade de monitorizar o acesso a dados sensíveis. A auditoria da Base de [Dados Azure SQL](sql-database-auditing.md) foi melhorada `data_sensitivity_information`para incluir um novo campo no registo de auditoria chamado . Este campo regista as classificações de sensibilidade (etiquetas) dos dados que foram devolvidos por uma consulta. Segue-se um exemplo:

![Registo de auditoria](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Permissões

Estas funções incorporadas podem ler a classificação de dados de uma base de dados Azure SQL:

- Proprietário
- Leitor
- Contribuinte
- Gestor de Segurança SQL
- Administrador de Acesso de Utilizador

Estas funções incorporadas podem modificar a classificação de dados de uma base de dados Azure SQL:

- Proprietário
- Contribuinte
- Gestor de Segurança SQL

Saiba mais sobre permissões baseadas em papéis no [RBAC para recursos Azure.](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gerir classificações

Pode utilizar o T-SQL, um REST API ou PowerShell para gerir classificações.

### <a name="use-t-sql"></a>Utilizar T-SQL

Pode utilizar o T-SQL para adicionar ou remover classificações de colunas e para recuperar todas as classificações para toda a base de dados.

> [!NOTE]
> Quando se utiliza o T-SQL para gerir rótulos, não há validação de que existam rótulos que se adicionam a uma coluna na política de proteção de informação da organização (o conjunto de rótulos que aparecem nas recomendações do portal). Então, cabe-lhe a si validar isto.

Para obter informações sobre a utilização do T-SQL para classificações, consulte as seguintes referências:

- Para adicionar ou atualizar a classificação de uma ou mais colunas: [ADICIONAR CLASSIFICAÇÃO DE SENSIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Para remover a classificação de uma ou mais colunas: [CLASSIFICAÇÃO DE SENSIBILIDADE DE QUEDA](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Para ver todas as classificações na base de dados: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Use a API do descanso

Pode utilizar a API REST para gerir programáticamente classificações e recomendações. A API rest publicada apoia as seguintes operações:

- [Criar ou atualizar:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate)Cria ou atualiza o rótulo de sensibilidade da coluna especificada.
- [Eliminar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Elimina a etiqueta de sensibilidade da coluna especificada.
- [Recomendação de desativação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): Desativa recomendações de sensibilidade na coluna especificada.
- [Recomendação ativa :](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)Permite recomendações de sensibilidade na coluna especificada. (As recomendações são ativadas por padrão em todas as colunas.)
- [Obter](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Obtém o rótulo de sensibilidade da coluna especificada.
- [Lista Corrente por Base de Dados](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Obtém os rótulos de sensibilidade atuais da base de dados especificada.
- [Lista Recomendada por Base](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)de Dados : Obtém os rótulos de sensibilidade recomendados da base de dados especificada.

### <a name="use-powershell-cmdlets"></a>Utilizar cmdlets do PowerShell
Pode utilizar o PowerShell para gerir classificações e recomendações para a Base de Dados Azure SQL e instâncias geridas.

#### <a name="powershell-cmdlets-for-sql-database"></a>PowerShell cmdlets para Base de Dados SQL

- [Classificação de sensibilidade à base de dados Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Classificação de sensibilidade à base de dados set-AzSql](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Eliminação-AzSqlDatabaseSensibilidade](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Recomendação de Sensibilidade à Base de Dados Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Recomendação de sensibilidade Enable-AzSqlDatabaSe](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Recomendação de sensibilidade à base de dados de si-AzSql](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>PowerShell cmdlets para casos geridos

- [Classificação de sensibilidade à base de dados get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Classificação de sensibilidade à base de dados set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Eliminação-AzSqlInstanceDatabaseClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Recomendação de sensibilidade à base de dados Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Recomendação de sensibilidade à base de dados Enable-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Recomendação de sensibilidade acasos](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Segurança avançada de dados.](sql-database-advanced-data-security.md)
- Considere configurar a Auditoria de Base de [Dados Azure SQL](sql-database-auditing.md) para monitorização e auditoria de acesso aos seus dados confidenciais confidenciais.
- Para uma apresentação que inclui a descoberta e classificação de dados, consulte [Descobrir, classificar, rotular & proteger dados DaQL [ Dados Expostos](https://www.youtube.com/watch?v=itVi9bkJUNc).
