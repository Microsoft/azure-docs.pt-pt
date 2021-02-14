---
title: Deteção e Classificação de Dados
description: Data Discovery & Classificação para Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/11/2021
tags: azure-synapse
ms.openlocfilehash: 254d69c723a97d9007a1f6c0f0eb7b34bcfe3c4e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392770"
---
# <a name="data-discovery--classification"></a>Deteção e Classificação de Dados
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Data Discovery & Classification é incorporado na Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Synapse Analytics. Fornece capacidades básicas para descobrir, classificar, rotular e reportar os dados sensíveis nas suas bases de dados.

Os seus dados mais sensíveis podem incluir informações empresariais, financeiras, de saúde ou pessoais. Descobrir e classificar estes dados pode desempenhar um papel fundamental na abordagem de proteção de informação da sua organização. Pode funcionar como infraestrutura para:

- Ajudando a cumprir as normas de privacidade de dados e requisitos para o cumprimento regulamentar.
- Vários cenários de segurança, como o acesso de monitorização (auditoria) a dados sensíveis.
- Controlar o acesso e endurecer a segurança das bases de dados que contêm dados altamente sensíveis.

> [!NOTE]
> Para obter informações sobre o SQL Server no local, consulte [a Classificação de & de descoberta de dados SQL](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>O que é a classificação & data Discovery?

Data Discovery & Classification introduz um conjunto de serviços básicos e novas capacidades em Azure. Forma um novo paradigma de proteção da informação para a BASE de Dados SQL, SQL Managed Instance e Azure Synapse, destinado a proteger os dados e não apenas a base de dados. O paradigma inclui:

- **Descoberta e recomendações:** O motor de classificação digitaliza a sua base de dados e identifica colunas que contêm dados potencialmente sensíveis. Em seguida, proporciona-lhe uma maneira fácil de rever e aplicar a classificação recomendada através do portal Azure.

- **Rotulagem:** Pode aplicar etiquetas de classificação de sensibilidade persistentemente às colunas utilizando novos atributos de metadados que foram adicionados ao motor de base de dados sql Server. Estes metadados podem então ser utilizados para cenários de auditoria e proteção baseados na sensibilidade.

- **Sensibilidade do conjunto de resultados de consulta:** A sensibilidade de um conjunto de resultados de consulta é calculada em tempo real para efeitos de auditoria.

- **Visibilidade:** Pode ver o estado de classificação da base de dados num painel detalhado no portal Azure. Além disso, você pode baixar um relatório no formato Excel para usar para fins de conformidade e auditoria e outras necessidades.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Descubra, classifique e rotule colunas sensíveis

Esta secção descreve os passos para:

- Descobrir, classificar e rotular colunas que contenham dados sensíveis na sua base de dados.
- Ver o estado de classificação atual da sua base de dados e relatórios de exportação.

A classificação inclui dois atributos de metadados:

- **Etiquetas**: Os principais atributos de classificação, utilizados para definir o nível de sensibilidade dos dados armazenados na coluna.  
- **Tipos de informação**: Atributos que fornecem mais informações granulares sobre o tipo de dados armazenados na coluna.

### <a name="define-and-customize-your-classification-taxonomy"></a>Defina e personalize a sua taxonomia de classificação

Data Discovery & Classification vem com um conjunto incorporado de etiquetas de sensibilidade e um conjunto incorporado de tipos de informação e lógica de descoberta. Pode agora personalizar esta taxonomia e definir um conjunto e uma classificação de construções específicas para o seu ambiente.

Você define e personaliza a sua taxonomia de classificação em um lugar central para toda a sua organização Azure. Essa localização está no [Centro de Segurança Azure,](../../security-center/security-center-introduction.md)como parte da sua política de segurança. Só alguém com direitos administrativos no grupo de gestão de raiz da organização pode fazer esta tarefa.

Como parte da gestão de políticas para a proteção da informação, pode definir etiquetas personalizadas, classificá-las e associá-las a um conjunto selecionado de tipos de informação. Também pode adicionar os seus próprios tipos de informações personalizadas e configurá-los com padrões de cordas. Os padrões são adicionados à lógica de descoberta para identificar este tipo de dados nas suas bases de dados.

Para obter mais informações, consulte [Personalizar a política de proteção de informação SQL no Azure Security Center (Preview)](../../security-center/security-center-info-protection-policy.md).

Depois de definida a política em toda a organização, pode continuar a classificar bases de dados individuais utilizando a sua política personalizada.

### <a name="classify-your-database"></a>Classifique a sua base de dados

> [!NOTE]
> O exemplo abaixo utiliza a Base de Dados Azure SQL, mas deve selecionar o produto apropriado que pretende configurar a Data Discovery & Classificação.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

1. Aceda à **classificação & Data Discovery** sob o título **de Segurança** no seu painel de base de dados Azure SQL. O separador Visão Geral inclui um resumo do estado de classificação atual da base de dados. O resumo inclui uma lista detalhada de todas as colunas classificadas, que também pode filtrar para mostrar apenas peças de esquemas específicos, tipos de informação e etiquetas. Se ainda não classificou nenhuma coluna, [salte para o passo 4](#step-4).

    ![Descrição Geral](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Para descarregar um relatório no formato Excel, selecione **Export** no menu superior do painel.

1. <a id="step-4"></a>Para começar a classificar os seus dados, selecione o **separador Classificação** na página **data Discovery & Classification.**

    O motor de classificação analisa a sua base de dados por colunas que contenham dados potencialmente sensíveis e fornece uma lista de classificações de colunas recomendadas.

1. Ver e aplicar recomendações de classificação:

   - Para visualizar a lista de classificações recomendadas de colunas, selecione o painel de recomendações na parte inferior do painel.

   - Para aceitar uma recomendação para uma coluna específica, selecione a caixa de verificação na coluna esquerda da linha relevante. Para marcar todas as recomendações conforme aceite, selecione a caixa de verificação mais à esquerda no cabeçalho da tabela de recomendações.

   - Para aplicar as recomendações selecionadas, **selecione Aceite recomendações selecionadas**.

   ![Recomendações de classificação](./media/data-discovery-and-classification-overview/recommendation.png)

1. Também pode classificar as colunas manualmente, como alternativa ou para além da classificação baseada em recomendações:

   1. **Selecione Adicionar classificação** no menu superior do painel.

   1. Na janela de contexto que se abre, selecione o esquema, a tabela e a coluna que pretende classificar, e o tipo de informação e o rótulo de sensibilidade.

   1. **Selecione Adicionar classificação** na parte inferior da janela de contexto.

   ![Adicionar manualmente classificação](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. Para completar a sua classificação e etiquetar persistentemente (tag) as colunas de base de dados com os novos metadados de classificação, **selecione Guardar** na página **de Classificação.**

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Auditoria de acesso a dados sensíveis

Um aspeto importante do paradigma da proteção da informação é a capacidade de monitorizar o acesso a dados sensíveis. [A Azure SQL Auditing](../../azure-sql/database/auditing-overview.md) foi reforçada para incluir um novo campo no registo de auditoria chamado `data_sensitivity_information` . Este campo regista as classificações de sensibilidade (etiquetas) dos dados que foram devolvidos por uma consulta. Eis um exemplo:

![Registo de auditoria](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Permissões

Estas funções incorporadas podem ler a classificação de dados de uma base de dados:

- Proprietário
- Leitor
- Contribuinte
- Gestor de Segurança SQL
- Administrador de Acesso dos Utilizadores

Estas funções incorporadas podem modificar a classificação de dados de uma base de dados:

- Proprietário
- Contribuinte
- Gestor de Segurança SQL

Saiba mais sobre permissões baseadas em funções no [Azure RBAC](../../role-based-access-control/overview.md).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gerir classificações

Pode utilizar T-SQL, uma API REST ou PowerShell para gerir classificações.

### <a name="use-t-sql"></a>Use T-SQL

Pode utilizar o T-SQL para adicionar ou remover classificações de colunas e para recuperar todas as classificações para toda a base de dados.

> [!NOTE]
> Quando se utiliza o T-SQL para gerir rótulos, não há validação que os rótulos que adiciona a uma coluna existam na política de proteção de informação da organização (o conjunto de rótulos que aparecem nas recomendações do portal). Então, cabe-lhe a si validar isto.

Para obter informações sobre a utilização do T-SQL para classificações, consulte as seguintes referências:

- Para adicionar ou atualizar a classificação de uma ou mais colunas: [CLASSIFICAÇÃO DE SENSIBILIDADE ADD](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Para remover a classificação de uma ou mais colunas: [CLASSIFICAÇÃO DE SENSIBILIDADE DROP](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Para ver todas as classificações na base de dados: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Utilizar cmdlets do PowerShell
Gerir classificações e recomendações para Azure SQL Database e Azure SQL Managed Instance usando PowerShell.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Cmdlets PowerShell para Base de Dados Azure SQL

- [Receber-AzSqlDatabaseSensibilidadeClassificação](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensibilidadeClassificação](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remoção-AzSqlDatabaseSensibilidadeClassificação](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensibilidadeRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Desativação-AzSqlDatabaseSensibilidadeRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Cmdlets PowerShell para Azure SQL Gestd Instance

- [Get-AzSqlInstanceDatabaseSensibilidadeClassifica](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensibilidadeClassifica](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remoção-AzSqlInstanceDatabaseSensibilidadeClassifica](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensibilidadeRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensibilidadeRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Desativação-AzSqlInstanceDatabaseSensibilidadeRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Use a API de Descanso

Pode utilizar a API REST para gerir programáticamente classificações e recomendações. A API REST publicada apoia as seguintes operações:

- [Criar ou atualizar:](/rest/api/sql/sensitivitylabels/createorupdate)Cria ou atualiza o rótulo de sensibilidade da coluna especificada.
- [Excluir](/rest/api/sql/sensitivitylabels/delete): Elimina a etiqueta de sensibilidade da coluna especificada.
- [Recomendação para desativar](/rest/api/sql/sensitivitylabels/disablerecommendation): Desativa as recomendações de sensibilidade na coluna especificada.
- [Ativar recomendação](/rest/api/sql/sensitivitylabels/enablerecommendation): Ativa recomendações de sensibilidade na coluna especificada. (As recomendações são ativadas por padrão em todas as colunas.)
- [Obter:](/rest/api/sql/sensitivitylabels/get)Obtém a etiqueta de sensibilidade da coluna especificada.
- [Lista Corrente por Base de Dados](/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Obtém as etiquetas de sensibilidade atuais da base de dados especificada.
- [Lista Recomendada por Base de Dados](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Obtém as etiquetas de sensibilidade recomendadas da base de dados especificada.


## <a name="faq---advanced-classification-capabilities"></a>FAQ - Capacidades de classificação avançadas

**Pergunta**: [A Azure Purview](https://docs.microsoft.com/azure/purview/overview) substituirá a Classificação de & de descoberta de dados sql ou será que a SqL Data Discovery & Classification será retirada em breve?
**Resposta**: Continuamos a apoiar a Classificação & classificação de descoberta de dados SQL e encorajamo-lo a adotar [a Azure Purview](https://docs.microsoft.com/azure/purview/overview) que tem capacidades mais ricas para impulsionar capacidades avançadas de classificação e governação de dados. Se decidirmos retirar qualquer serviço, recurso, API ou SKU, receberá aviso prévio, incluindo uma rota de migração ou transição. Saiba mais sobre as políticas do Microsoft Lifecycle aqui.


## <a name="next-steps"></a><a id="next-steps"></a>Passos seguintes

- Considere configurar [a Auditoria Azure SQL](../../azure-sql/database/auditing-overview.md) para monitorizar e auditar o acesso aos seus dados confidenciais confidenciais.
- Para uma apresentação que inclua data Discovery & Classification, consulte [Descobrir, classificar, rotular & proteger dados do SQL | Dados expostos](https://www.youtube.com/watch?v=itVi9bkJUNc).
