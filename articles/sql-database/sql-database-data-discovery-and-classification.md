---
title: Deteção de dados de base de dados SQL do Azure e classificação | Documentos da Microsoft
description: Deteção de dados de base de dados SQL do Azure e classificação
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 8417b849ad7589ce6a770eb805c38a13e3c672dd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448769"
---
# <a name="azure-sql-database-data-discovery--classification"></a>Deteção de dados de base de dados SQL do Azure e classificação

Deteção de dados e classificação (atualmente em pré-visualização) fornece capacidades avançadas incorporadas na base de dados do Azure SQL para **detetar**, **classificar**, **etiquetagem**  &  **proteger** os dados confidenciais em seus bancos de dados.
A detetar e classificar os dados mais confidenciais (comerciais, financeiros, cuidados de saúde, dados de identificação pessoal (PII) e assim por diante.) podem desempenham um papel essencial no seu escritor de proteção de informações da organização. Que possa servir como a infraestrutura para:

- Ajudar a atender a requisitos de conformidade a normas e de normas de privacidade de dados.
- Vários cenários de segurança, como monitorização (auditoria) e os alertas no anómalo acesso a dados confidenciais.
- Controlar o acesso ao e o sistema de proteção de segurança de bases de dados que contém dados altamente confidenciais.

Dados de deteção e classificação faz parte do [segurança de dados avançada](sql-database-advanced-data-security.md) (ADS) oferta, que é um pacote unificado para funções de segurança avançadas do SQL. dados de deteção e classificação podem ser acedidas e geridos através do portal de anúncios de SQL central.

> [!NOTE]
> Este documento está relacionado com a base de dados do Azure SQL apenas. Para o SQL Server (no local), consulte [deteção de dados SQL e classificação](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>O que é a deteção de dados e classificação

Dados de deteção e classificação apresenta um conjunto de serviços avançados e novas capacidades SQL, formando um novo paradigma de proteção de informações de SQL com o objetivo de proteger os dados, não apenas a base de dados:

- **Deteção & recomendações**

  O motor de classificação analisa a sua base de dados e identifica as colunas que contêm dados potencialmente confidenciais. Ele, em seguida, fornece uma forma fácil para rever e aplicar as recomendações de classificação apropriado através do portal do Azure.

- **Etiquetagem**

  Etiquetas de classificação de sensibilidade podem ser etiquetadas de forma permanente em colunas com novos atributos de metadados de classificação introduzidos no motor do SQL. Estes metadados, em seguida, podem ser utilizado para com base na sensibilidade a auditoria e proteção cenários avançados.

- **Sensibilidade de conjunto de resultados de consulta**

  A sensibilidade de conjunto de resultados de consulta é calculada em tempo real para fins de auditoria.

- **Visibility** (Visibilidade)

  O estado de classificação de base de dados pode ser visualizado num dashboard detalhado no portal. Além disso, pode baixar um relatório (no formato Excel) a ser utilizado para conformidade e fins de auditoria, bem como outras necessidades.

## <a id="subheading-2"></a>Detetar, classificar e etiquetar colunas confidenciais

A secção seguinte descreve os passos para deteção, classificação e etiquetagem colunas que contêm dados confidenciais na sua base de dados, bem como visualizar o estado de classificação atual da base de dados e exportar relatórios.

A classificação inclui dois atributos de metadados:

- Etiquetas – os atributos de classificação principal, utilizadas para definir o nível de sensibilidade dos dados armazenados na coluna.  
- Tipos de informações – proporcionam granularidade adicional para o tipo de dados armazenados na coluna.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definir e personalizar a taxonomia de classificação

Deteção de dados SQL e classificação vem com um conjunto interno de etiquetas de sensibilidade e um conjunto interno de tipos de informações e lógica de deteção. Agora tem a capacidade de personalizar este taxonomia e definir um conjunto e classificação das construções de classificação especificamente para seu ambiente.

Definição e personalização da sua taxonomia de classificação é feito num único local central para todo o seu inquilino do Azure. Que a localização está no [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), como parte da sua política de segurança. Somente alguém com direitos administrativos no grupo de gestão de raiz de inquilino pode executar esta tarefa.

Como parte da gestão de política de Information Protection, pode definir etiquetas personalizadas, classifique-os e associá-las com um conjunto selecionado de tipos de informações. Também pode adicionar seus próprios tipos de informações personalizadas e configurá-las com padrões de cadeia de caracteres, que são adicionados para a lógica de deteção para identificar este tipo de dados em seus bancos de dados.
Saiba mais sobre como personalizar e gerir a sua política no [guia de procedimentos de política do Information Protection](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Depois da política ao nível do inquilino tiver sido definida, pode continuar com a classificação de bases de dados individuais com a política personalizada.

## <a name="classify-your-sql-database"></a>Classificar a base de dados SQL

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Navegue para **segurança de dados avançada** sob o cabeçalho de segurança no seu painel de SQL Database do Azure. Clique para ativar a segurança avançada de dados e, em seguida, clique nas **deteção de dados e classificação (pré-visualização)** cartão.

   ![Uma base de dados de análise](./media/sql-data-discovery-and-classification/data_classification.png)

3. O **descrição geral** guia inclui um resumo do Estado de classificação atual da base de dados, incluindo uma lista detalhada de todas as colunas classificadas, também pode filtrar para ver apenas partes do esquema específico, tipos de informações e as etiquetas. Se ainda não classificado quaisquer colunas [avance para o passo 5](#step-5).

   ![Resumo do Estado de classificação atual](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Para transferir um relatório no formato do Excel, clique nas **exportar** opção no menu superior da janela.

   ![Exportar para Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Para começar a classificar seus dados, clique nas **separador de classificação** na parte superior da janela.

    ![Classificar dados](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. O motor de classificação analisa a sua base de dados para colunas que contêm dados potencialmente confidenciais e fornece uma lista de **recomendado classificações de coluna**. Para visualizar e aplicar recomendações de classificação:

   - Para ver a lista de classificações de coluna recomendada, clique no painel de recomendações na parte inferior da janela:

      ![Classifique os seus dados](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Reveja a lista de recomendações – para aceitar uma recomendação para uma coluna de específica, marque a caixa de verificação na coluna esquerda da linha relevante. Também é possível marcar *todas as recomendações* como aceites marcando a caixa de verificação no cabeçalho da tabela de recomendações.

       ![Lista de recomendações de revisão](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Para aplicar as recomendações selecionadas, clique na azul **aceitar as recomendações selecionadas** botão.

      ![Aplicar recomendações](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Também pode **classificar manualmente** colunas como uma alternativa ou além disso, para a classificação baseada na Recomendação:

   - Clique em **Adicionar classificação** no menu superior da janela.

      ![Adicionar manualmente a classificação](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - Na janela de contexto que se abre, selecione o esquema > tabela > coluna que pretende classificar e a etiqueta de tipo e a confidencialidade de informações. Em seguida, clique na azul **Adicionar classificação** na parte inferior da janela do contexto.

      ![Selecione a coluna para classificar](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Para concluir a sua classificação e colunas (etiqueta) a base de dados com os novos metadados de classificação de etiqueta persistentemente, clique em **guardar** no menu superior da janela.

   ![Guardar](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Auditoria de acesso a dados confidenciais

Um aspecto importante do paradigma de proteção de informações é a capacidade de monitorizar o acesso a dados confidenciais. [Auditoria de base de dados SQL do Azure](sql-database-auditing.md) foi aprimorado para incluir um novo campo no registo de auditoria chamado *data_sensitivity_information*, que regista as classificações de sensibilidade (etiquetas) dos dados reais que foi devolvidas pelo a consulta.

![Registo de auditoria](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Classificação automatizada/Programmatic

Pode usar o T-SQL para adicionar ou remover as classificações de coluna, bem como obter todas as classificações para a base de dados.

> [!NOTE]
> Quando utilizar o T-SQL para gerir as etiquetas, não há nenhuma validação adicionadas a uma coluna de etiquetas existentes na política de proteção de informações da empresa (o conjunto de etiquetas que são apresentados no portais recomendações). É, portanto, deve validar isto.

- Adicionar/atualizar a classificação de uma ou mais colunas: [ADICIONAR A CLASSIFICAÇÃO DE SENSIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Remova a classificação de uma ou mais colunas: [REMOVER A CLASSIFICAÇÃO DE SENSIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Ver todas as classificações na base de dados: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

Também pode utilizar as APIs REST para gerir programaticamente classificações. As APIs REST publicados suportam as seguintes operações:

- [Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – cria ou atualiza a etiqueta de sensibilidade de uma determinada coluna
- [Eliminar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) -elimina a etiqueta de sensibilidade de uma determinada coluna
- [Obter](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) -obtém a etiqueta de sensibilidade de uma determinada coluna
- [Lista por base de dados atual](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) -obtém as etiquetas de sensibilidade atual de um determinado banco de dados
- [Recomendado pela base de dados de lista](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) -obtém as etiquetas de sensibilidade recomendada de um determinado banco de dados

## <a id="subheading-5"></a>Passos seguintes

- Saiba mais sobre [segurança de dados avançada](sql-database-advanced-data-security.md).
- Considere configurar [auditoria de base de dados SQL do Azure](sql-database-auditing.md) para monitorização e auditoria de acesso aos seus dados confidenciais classificados.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
