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
ms.openlocfilehash: b3a08eb351d29fd71889807c9a21d03b564117a7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673753"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Data Discovery & Classificação para Base de Dados Azure SQL e Azure Synapse Analytics

Data Discovery & Classification fornece capacidades avançadas incorporadas na Base de Dados Azure SQL para **descobrir,** **classificar**, **rotulagem** & **de dados** sensíveis nas suas bases de dados.

Descobrir e classificar os seus dados mais sensíveis (negócios, financeiros, cuidados de saúde, dados pessoalmente identificáveis, etc.) pode desempenhar um papel fundamental na sua estatura organizacional de proteção de informação. Pode funcionar como infraestrutura para:

- Ajudar a cumprir as normas de privacidade dos dados e os requisitos de conformidade regulamentares.
- Vários cenários de segurança, como a monitorização (auditoria) e o alerta sobre o acesso anómalo a dados sensíveis.
- Controlar o acesso e reforçar a segurança das bases de dados que contêm dados altamente confidenciais.

A Data Discovery & Classification faz parte da oferta avançada de segurança de [dados](sql-database-advanced-data-security.md) (ADS), que é um pacote unificado para capacidades avançadas de segurança SQL. a descoberta de dados & classificação pode ser acedida e gerida através do portal Central SQL ADS.

> [!NOTE]
> Este documento diz respeito à Base de Dados Azure SQL e ao Azure Synapse. Para a simplicidade, a Base de Dados SQL é utilizada quando se refere tanto à Base de Dados SQL como ao Synapse Azure. Para o Servidor SQL (nas instalações), consulte A Descoberta e Classificação de [Dados SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>O que é a descoberta de dados & classificação

Data Discovery & Classification introduz um conjunto de serviços avançados e novas capacidades SQL, formando um novo paradigma de Proteção de Informação SQL destinado a proteger os dados, e não apenas a base de dados:

- **Recomendações de & de descoberta**

  O motor de classificação digitaliza a sua base de dados e identifica colunas que contêm dados potencialmente sensíveis. Em seguida, proporciona-lhe uma forma fácil de analisar e aplicar as recomendações de classificação adequadas através do portal do Azure.

- **Rotulagem**

  As etiquetas de classificação de sensibilidade podem ser marcadas persistentemente nas colunas utilizando novos atributos de metadados de classificação introduzidos no Motor SQL. Em seguida, estes metadados podem ser utilizados em cenários avançados de proteção e auditoria baseados em confidencialidade.

- **Sensibilidade do conjunto de resultados de consulta**

  A sensibilidade do conjunto de resultados de consulta é calculada em tempo real para efeitos de auditoria.

- **Visibilidade**

  O estado de classificação da base de dados pode ser visto num painel detalhado no portal. Adicionalmente, pode transferir um relatório (no formato do Excel) para ser utilizado para fins de conformidade e auditoria, bem como para outras necessidades.

## <a name="discover-classify--label-sensitive-columns"></a><a id="discover-classify-columns"></a>Descubra, classifique & colunas sensíveis ao rótulo

A secção seguinte descreve os passos para descobrir, classificar e rotular colunas contendo dados sensíveis na sua base de dados, bem como visualizar o estado de classificação atual da sua base de dados e relatórios de exportação.

A classificação inclui dois atributos de metadados:

- **Etiquetas** – Os principais atributos de classificação, utilizados para definir o nível de sensibilidade dos dados armazenados na coluna.  
- Tipos de **Informação** – Forneça granularidade adicional ao tipo de dados armazenados na coluna.

## <a name="define-and-customize-your-classification-taxonomy"></a>Defina e personalize a sua taxonomia de classificação

Data Discovery & Classification vem com um conjunto incorporado de etiquetas de sensibilidade e um conjunto incorporado de tipos de informação e lógica de descoberta. Agora tem a capacidade de personalizar esta taxonomia e definir um conjunto e classificação de construções de classificação especificamente para o seu ambiente.

A definição e personalização da sua taxonomia de classificação é feita num lugar central para todo o seu inquilino Azure. Essa localização é no Centro de [Segurança Azure,](https://docs.microsoft.com/azure/security-center/security-center-intro)como parte da sua Política de Segurança. Só alguém com direitos administrativos sobre o grupo de gestão de raiz do Arrendatário pode executar esta tarefa.

Como parte da gestão da política de proteção de informação SQL, pode definir etiquetas personalizadas, classificá-las e associá-las a um conjunto selecionado de tipos de informação. Também pode adicionar os seus próprios tipos de informações personalizados e configurá-los com padrões de cadeia, que são adicionados à lógica de deteção para identificar este tipo de dados nas bases de dados.
Saiba mais sobre personalizar e gerir a sua política na política de proteção de [informação SQL como orientar](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Uma vez definida a política em todo o arrendamento, pode continuar com a classificação de bases de dados individuais utilizando a sua política personalizada.

## <a name="classify-your-sql-database"></a>Classifique a sua Base de Dados SQL

1. Vá ao [portal Azure.](https://portal.azure.com)

2. Navegue para **a Segurança Avançada** de Dados sob a direção de Segurança no painel de base de dados Azure SQL. Clique para ativar a segurança avançada de dados e, em seguida, clique na descoberta de dados & cartão de **classificação.**

   ![Digitalizar uma base de dados](./media/sql-data-discovery-and-classification/data_classification.png)

3. O separador **Overview** inclui um resumo do estado de classificação atual da base de dados, incluindo uma lista detalhada de todas as colunas classificadas, que também pode filtrar para visualizar apenas peças de esquemas específicas, tipos de informação e etiquetas. Se ainda não classificou nenhuma coluna, [salte para o passo 5](#step-5).

   ![Resumo do estado de classificação atual](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Para fazer o download de um relatório em formato Excel, clique na opção **Export** no menu superior da janela.

5. <a id="step-5"></a>Para começar a classificar os seus dados, clique no **separador Classificação** na parte superior da janela.

6. O motor de classificação verifica a sua base de dados para colunas que contenham dados potencialmente sensíveis e fornece uma lista de **classificações recomendadas**de colunas . Ver e aplicar recomendações de classificação:

   - Para ver a lista de classificações recomendadas da coluna, clique no painel de recomendações na parte inferior da janela

   - Reveja a lista de recomendações – para aceitar uma recomendação para uma coluna específica, verifique a caixa de verificação na coluna esquerda da linha relevante. Também pode marcar *todas as recomendações aceites,* verificando a caixa de verificação no cabeçalho da tabela de recomendações.

       ![Lista de recomendações de revisão](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Para aplicar as recomendações selecionadas, clique no botão de **recomendações do Aceito** azul.

7. Também pode **classificar manualmente** as colunas como uma alternativa, ou além disso, à classificação baseada em recomendações:

   - Clique em **Adicionar classificação** no menu superior da janela.

   - Na janela de contexto que se abre, selecione o esquema > tabela > coluna que pretende classificar, e o tipo de informação e etiqueta de sensibilidade. Em seguida, clique no botão de **classificação** azul Adicionar na parte inferior da janela de contexto.

      ![Selecione coluna para classificar](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Para completar a sua classificação e rotular persistentemente (etiquetar) as colunas de base de dados com os novos metadados de classificação, clique em **Guardar** no menu superior da janela.

## <a name="auditing-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Auditoria ao acesso a dados sensíveis

Um aspeto importante do paradigma da proteção da informação é a capacidade de monitorizar o acesso a dados sensíveis. A auditoria da Base de [Dados Azure SQL](sql-database-auditing.md) foi melhorada para incluir um novo campo no registo de auditoria denominado *data_sensitivity_information,* que regista as classificações de sensibilidade (etiquetas) dos dados reais que foram devolvidos pela consulta.

![Registo de auditoria](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Permissões

As seguintes funções incorporadas podem ler a classificação `Owner`de `Reader` `Contributor`dados `SQL Security Manager` `User Access Administrator`de uma base de dados Azure SQL: , , e .

As seguintes funções incorporadas podem modificar a classificação `Owner`de `Contributor` `SQL Security Manager`dados de uma base de dados Azure SQL: , .

Saiba mais sobre [rBAC para recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gerir classificações

### <a name="using-t-sql"></a>Utilização de T-SQL
Pode utilizar o T-SQL para adicionar/remover classificações de colunas, bem como recuperar todas as classificações para toda a base de dados.

> [!NOTE]
> Ao utilizar o T-SQL para gerir os rótulos, não existe qualquer validação de que existam rótulos adicionados a uma coluna na política organizacional de proteção da informação (o conjunto de rótulos que aparecem nas recomendações do portal). Cabe-lhe, portanto, validar isto.

- Adicione/atualize a classificação de uma ou mais colunas: [CLASSIFICAÇÃO](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql) DE SENSIBILIDADE ADD
- Retire a classificação de uma ou mais colunas: [CLASSIFICAÇÃO DE SENSIBILIDADE DE QUEDA](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Ver todas as classificações na base de dados: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="using-rest-api"></a>Usando a API de descanso
Pode utilizar a API REST para gerir programáticamente classificações e recomendações. A API rest publicada suporta as seguintes operações:

- [Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - Cria ou atualiza o rótulo de sensibilidade de uma determinada coluna
- [Excluir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) - Elimina o rótulo de sensibilidade de uma determinada coluna
- [Recomendação de desativação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) - Desativa recomendações de sensibilidade numa determinada coluna
- [Ativar recomendação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) - Permite recomendações de sensibilidade numa determinada coluna (as recomendações são ativadas por padrão em todas as colunas)
- Get - [Obtém](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) o rótulo de sensibilidade de uma dada coluna
- [Lista Current By Database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) - Obtém os rótulos de sensibilidade atuais de uma determinada base de dados
- [Lista Recomendada por Base](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) de Dados - Obtém os rótulos de sensibilidade recomendados de uma determinada base de dados

### <a name="using-powershell-cmdlet"></a>Usando o PowerShell Cmdlet
Pode utilizar o PowerShell para gerir classificações e recomendações para a Base de Dados Azure SQL e Para a Instância Gerida.

#### <a name="powershell-cmdlet-for-azure-sql-database"></a>PowerShell Cmdlet para Base de Dados SQL Azure
- [Classificação de sensibilidade à base de dados Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Classificação de sensibilidade à base de dados set-AzSql](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Eliminação-AzSqlDatabaseSensibilidade](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Recomendação de Sensibilidade à Base de Dados Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Recomendação de sensibilidade Enable-AzSqlDatabaSe](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Recomendação de sensibilidade à base de dados de si-AzSql](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instance"></a>PowerShell Cmdlets para Instância Gerida
- [Classificação de sensibilidade à base de dados get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Classificação de sensibilidade à base de dados set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Eliminação-AzSqlInstanceDatabaseClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Recomendação de sensibilidade à base de dados Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Recomendação de sensibilidade à base de dados Enable-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Recomendação de sensibilidade acasos](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Segurança avançada de dados.](sql-database-advanced-data-security.md)
- Considere configurar a Auditoria de Base de [Dados Azure SQL](sql-database-auditing.md) para monitorização e auditoria de acesso aos seus dados confidenciais confidenciais.
- Para uma apresentação que inclui data Discovery & Classification, consulte [Descobrir, classificar, rotular & proteger dados DaQL [ Dados Expostos](https://www.youtube.com/watch?v=itVi9bkJUNc).
