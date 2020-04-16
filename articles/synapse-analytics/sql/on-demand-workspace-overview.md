---
title: SQL on-demand (pré-visualização)
description: Saiba mais sobre a Synapse SQL a pedido no Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8d4de424d5d4d6da1ee80e04b35e63ae29df57c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424910"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL on-demand (pré-visualização) em Azure Synapse Analytics 

Todos os espaços de trabalho do Azure Synapse Analytics (pré-visualização) vêm com pontos finais sql on-demand (pré-visualização) que você pode usar para consultar dados no lago.

SQL on-demand é um serviço de consulta sobre os dados no seu lago de dados. Permite-lhe aceder aos seus dados através das seguintes funcionalidades:
 
- Uma sintaxe T-SQL familiar para consulta de dados no lugar sem a necessidade de copiar ou carregar dados numa loja especializada. 
- Conectividade integrada através da interface T-SQL que oferece uma vasta gama de inteligência empresarial e ferramentas de consulta ad-hoc, incluindo os condutores mais populares. 

O SQL on-demand é um sistema de processamento de dados distribuído, construído para grande escala de dados e cálculos. A SQL on-demand permite-lhe analisar o seu Big Data em segundos a minutos, dependendo da carga de trabalho. Graças à tolerância à execução de consultas incorporadas, o sistema fornece elevadas taxas de fiabilidade e sucesso mesmo para consultas de longa duração envolvendo grandes conjuntos de dados.

A procura on-demand da SQL é inservidora, pelo que não existe nenhuma infraestrutura para configurar ou clusters para manter. Um ponto final padrão para este serviço é fornecido em todos os espaços de trabalho Azure Synapse, para que você possa começar a consultar dados assim que o espaço de trabalho é criado. Não há qualquer custo para os recursos reservados, você está apenas sendo cobrado pelos dados digitalizados por consultas que executa, por isso este modelo é um verdadeiro modelo pay-per-use.  

Se utilizar a Spark no seu pipeline de dados, para preparação de dados, limpeza ou enriquecimento, pode [consultar quaisquer tabelas Spark](develop-storage-files-spark-tables.md) que tenha criado no processo, diretamente a partir da SQL on-demand. Utilize o [Private Link](../security/how-to-connect-to-workspace-with-private-links.md) para trazer o seu ponto final a pedido sQL para o seu espaço de trabalho [gerido VNet](../security/synapse-workspace-managed-vnet.md).  

## <a name="who-is-sql-on-demand-for"></a>Quem é SQL on-demand para

Se precisar de explorar dados no lago de dados, obter informações ou otimizar o seu pipeline de transformação de dados existente, pode beneficiar da utilização da SQL on-demand. É adequado para os seguintes cenários:

- Descoberta e exploração básicas - Rapidamente razão sobre os dados em vários formatos (Parquet, CSV, JSON) no seu lago de dados, para que possa planear como extrair insights dos mesmos.
- Armazém de dados lógicos – Forneça uma abstração relacional para além de dados crus ou díspares sem realojar e transformar dados, permitindo uma visão sempre atualizada dos seus dados.
- Transformação de dados - Forma simples, escalável e executante de transformar dados no lago utilizando T-SQL, para que possa ser alimentado a BI e outras ferramentas, ou carregado numa loja de dados relacional (bases de dados Synapse SQL, Base de Dados Azure SQL, etc.).

Diferentes funções profissionais podem beneficiar da SQL a pedido:

- Os Engenheiros de Dados podem explorar o lago, transformar e preparar dados usando este serviço, e simplificar os seus oleodutos de transformação de dados. Para mais informações, consulte este [tutorial.](tutorial-data-analyst.md)
- Dados Os cientistas podem rapidamente raciocinar sobre o conteúdo e estrutura dos dados no lago, graças a funcionalidades como OPENROWSET e inferência automática de esquemas.
- Os analistas de dados podem [explorar dados e tabelas spark](develop-storage-files-spark-tables.md) criadas por Cientistas de Dados ou Engenheiros de Dados usando linguagem T-SQL familiar ou as suas ferramentas favoritas, que podem ligar-se à SQL on-demand.
- Os profissionais do BI podem rapidamente [criar relatórios power BI em cima de dados no lago](tutorial-connect-power-bi-desktop.md) e mesas Spark.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>O que preciso fazer para começar a usá-lo?

O ponto final a pedido da SQL é fornecido em todos os espaços de trabalho azure synapse. Pode criar um espaço de trabalho e começar a consultar dados instantaneamente utilizando ferramentas que lhe são familiares.

## <a name="client-tools"></a>Ferramentas de cliente

A SQL on-demand permite que as ferramentas de consulta ad-hoc ad-hoc existentes e de inteligência empresarial entrem no lago de dados. Como fornece uma sintaxe T-SQL familiar, qualquer ferramenta capaz de estabelecer ofertas De Ligação TDS [SQL pode ligar-se e consultar a Synapse SQL](connect-overview.md) a pedido. Pode ligar-se ao Azure Data Studio e executar consultas ad-hoc ou ligar-se ao Power BI para obter informações em questão de minutos.

## <a name="is-full-t-sql-supported"></a>O T-SQL completo é suportado?

A SQL on-demand oferece a área de superfície de consulta T-SQL, que é ligeiramente melhorada/estendida em alguns aspetos para acomodar para experiências em torno da consulta de dados semi-estruturados e não estruturados. Além disso, alguns aspetos da língua T-SQL não são suportados devido ao design do SQL a pedido, como exemplo, a funcionalidade DML não é atualmente suportada.

- A carga de trabalho pode ser organizada usando conceitos familiares:
- Bases de dados - O ponto final da SQL a pedido pode ter várias bases de dados.
- Schemas - Dentro de uma base de dados, pode haver um ou muitos grupos de propriedade de objetos chamados schemas.
- Vistas
- Recursos externos – fontes de dados, formatos de ficheiros e tabelas

A segurança pode ser aplicada usando:

- Inícios de sessão e utilizadores
- Credenciais para controlar o acesso às contas de armazenamento
- Conceder, negar e revogar permissões por nível de objeto
- Integração do Azure Active Directory

T-SQL suportado:

- A área de superfície [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) completa é suportada, incluindo a maioria das funções SQL
- CETAS - CRIE TABELA EXTERNA COMO SELECIONADO
- Declarações dDL relacionadas apenas com pontos de vista e segurança

A SQL on-demand não tem armazenamento local, apenas os objetos de metadados são armazenados em bases de dados. Por conseguinte, a T-SQL relacionada com os seguintes conceitos não é suportada:

- Tabelas
- Acionadores
- Vistas materializadas
- Declarações dDL que não as relacionadas com pontos de vista e segurança
- Declarações DML

### <a name="extensions"></a>Extensões

A fim de permitir uma experiência suave para a consulta de dados residentes em ficheiros em data lake, a SQL on-demand alarga a função [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) existente adicionando as seguintes capacidades:

[Consulta de vários ficheiros ou pastas](develop-storage-files-overview.md#query-multiple-files-or-folders)

[Formato de ficheiro PARQUET](develop-storage-files-overview.md#parquet-file-format)

[Opções adicionais para trabalhar com texto delimitado (exterminador de campo, exterminador de linha, escape char)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Leia um subconjunto escolhido de colunas](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Inferência de esquema](develop-storage-files-overview.md#schema-inference)

[função de nome de arquivo](develop-storage-files-overview.md#filename-function)

[função de arquivo](develop-storage-files-overview.md#filepath-function)

[Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Segurança

A SQL on-demand oferece mecanismos para garantir o acesso aos seus dados.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A SQL on-demand permite-lhe gerir centralmente identidades de utilizadores de bases de dados e outros serviços da Microsoft com [integração do Diretório Ativo do Azure](../../sql-database/sql-database-Azure AD-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Ative Directory (Azure AD) suporta a [autenticação de vários fatores](../../sql-database/sql-database-ssms-mfa-authentication-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) para aumentar a segurança dos dados e da aplicação, ao mesmo tempo que suporta um único processo de inscrição.

#### <a name="authentication"></a>Autenticação

A autenticação a pedido do SQL refere-se à forma como os utilizadores comprovam a sua identidade ao ligarem-se ao ponto final. São suportados dois tipos de autenticação:

- **Autenticação SQL**

  Este método de autenticação utiliza um nome de utilizador e uma senha.

- **Autenticação do Diretório Ativo Azure:**

  Este método de autenticação utiliza identidades geridas pelo Azure Ative Directory. Para os utilizadores de Anúncios Azure, a autenticação de vários fatores pode ser ativada. Utilize a autenticação do Active Directory (segurança integrada) [sempre que possível](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="authorization"></a>Autorização

A autorização refere-se ao que um utilizador pode fazer dentro de uma base de dados on-demand SQL, e é controlado pelas subscrições de papel da sua conta de utilizador e permissões ao nível do objeto.

Se for utilizada a autenticação SQL, o utilizador SQL existe apenas na SQL a pedido e as permissões são orientadas para os objetos no SQL a pedido. O acesso a objetos securáveis noutros serviços (como o Armazenamento Azure) não pode ser concedido diretamente ao utilizador SQL, uma vez que só existe no âmbito da SQL a pedido. O utilizador SQL precisa de utilizar um dos tipos de [autorização suportados](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) para aceder aos ficheiros.

Se a autenticação AD Azure for utilizada, um utilizador pode iniciar sessão na SQL a pedido e outros serviços, como o Azure Storage, e pode conceder permissões ao utilizador da AD Azure.

### <a name="access-to-storage-accounts"></a>Acesso a contas de armazenamento

Um utilizador que esteja registado no serviço sql on-demand deve ser autorizado a aceder e consultar os ficheiros no Armazenamento Azure. A SQL a pedido suporta os seguintes tipos de autorização:

- A assinatura de **acesso partilhado (SAS)** proporciona acesso delegado aos recursos na conta de armazenamento. Com um SAS, pode conceder aos clientes acesso aos recursos na conta de armazenamento, sem partilhar chaves de conta. Um SAS dá-lhe controlo granular sobre o tipo de acesso que concede aos clientes que têm o SAS: intervalo de validade, permissões concedidas, intervalo de endereçoIP aceitável, protocolo aceitável (https/http).

- **Identidade de Utilizador** (também conhecido como "pass-through") é um tipo de autorização em que a identidade do utilizador da AD Azure que acedeu à SQL a pedido é utilizada para autorizar o acesso aos dados. Antes de aceder aos dados, o administrador de Armazenamento Azure deve conceder permissões ao utilizador da Azure AD para aceder aos dados. Este tipo de autorização utiliza o utilizador Azure AD que acedeu à SQL a pedido, pelo que não é suportado para tipos de utilizadores SQL.

## <a name="next-steps"></a>Passos seguintes
Informações adicionais sobre a ligação de pontofinal e os ficheiros de consulta podem ser encontrados nos seguintes artigos: 
- [Ligue-se ao seu ponto final](connect-overview.md)
- [Consulta dos seus ficheiros](develop-storage-files-overview.md)
