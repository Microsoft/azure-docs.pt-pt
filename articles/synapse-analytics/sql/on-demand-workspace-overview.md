---
title: Conjunto de SQL sem servidor
description: Saiba mais sobre a piscina SQL sem servidor em Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 87f592f0cff8504fbafba392d20f405640e2578a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682975"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Piscina SQL sem servidor em Azure Synapse Analytics 

Cada espaço de trabalho Azure Synapse Analytics vem com pontos finais sem servidor SQL que você pode usar para consultar dados no lago.

Serverless SQL pool é um serviço de consulta sobre os dados no seu lago de dados. Permite-lhe aceder aos seus dados através das seguintes funcionalidades:
 
- Uma sintaxe T-SQL familiar para consultar dados em vigor sem a necessidade de copiar ou carregar dados numa loja especializada. 
- Conectividade integrada através da interface T-SQL que oferece uma ampla gama de ferramentas de inteligência empresarial e consulta ad-hoc, incluindo os condutores mais populares. 

Serverless SQL pool é um sistema de processamento de dados distribuído, construído para dados em larga escala e funções computacionais. O pool SQL sem servidor permite-lhe analisar o Big Data em segundos a minutos, dependendo da carga de trabalho. Graças à tolerância à execução de consultas incorporadas, o sistema fornece altas taxas de fiabilidade e sucesso, mesmo para consultas de longa duração envolvendo grandes conjuntos de dados.

A piscina SQL sem servidor é sem servidor, portanto não há infraestruturas para configurar ou clusters para manter. Um ponto final predefinido para este serviço é fornecido em cada espaço de trabalho Azure Synapse, para que possa começar a consultar dados assim que o espaço de trabalho for criado. 

Não há qualquer custo para os recursos reservados, está apenas a ser cobrado pelos dados processados por consultas que executou, daí que este modelo seja um verdadeiro modelo pay-per-use.  

Se utilizar o Apache Spark para Azure Synapse no seu pipeline de dados, para preparação de dados, limpeza ou enriquecimento, pode [consultar as tabelas externas de Faíscas](develop-storage-files-spark-tables.md) que criou no processo, diretamente a partir de uma piscina SQL sem servidor. Utilize [o Link Privado](../security/how-to-connect-to-workspace-with-private-links.md) para trazer o seu ponto final de piscina SQL sem servidor para o seu [VNet de espaço de trabalho gerido](../security/synapse-workspace-managed-vnet.md).  

## <a name="serverless-sql-pool-benefits"></a>Benefícios da piscina SQL sem servidor

Se precisar de explorar dados no lago de dados, obter informações sobre ele ou otimizar o seu pipeline de transformação de dados existente, poderá beneficiar da utilização de um pool SQL sem servidor. É adequado para os seguintes cenários:

- Descoberta e exploração básicas - Raciocinar rapidamente sobre os dados em vários formatos (Parquet, CSV, JSON) no seu lago de dados, para que possa planear como extrair insights dos mesmos.
- Armazém de dados lógico – Forneça uma abstração relacional em cima de dados brutos ou díspares sem relocalização e transformação de dados, permitindo uma visão sempre atualizada dos seus dados.
- Transformação de dados - Forma simples, escalável e performante de transformar dados no lago usando T-SQL, para que possa ser alimentado a BI e outras ferramentas, ou carregado numa loja de dados relacional (bases de dados Sinaapse SQL, Base de Dados Azure SQL, etc.).

Diferentes funções profissionais podem beneficiar da piscina SQL sem servidor:

- Os Engenheiros de Dados podem explorar o lago, transformar e preparar dados usando este serviço, e simplificar os seus oleodutos de transformação de dados. Para mais informações, consulte este [tutorial.](tutorial-data-analyst.md)
- Os Cientistas de Dados podem raciocinar rapidamente sobre o conteúdo e estrutura dos dados no lago, graças a funcionalidades como OPENROWSET e inferência automática de esquemas.
- Os Analistas de Dados podem [explorar dados e spark tabelas externas](develop-storage-files-spark-tables.md) criadas por Data Scientists ou Data Engineers usando linguagem T-SQL familiar ou suas ferramentas favoritas, que podem ligar-se ao pool SQL sem servidor.
- Os profissionais do BI podem criar rapidamente [relatórios power bi em cima de dados nas](tutorial-connect-power-bi-desktop.md) tabelas do lago e da Spark.

## <a name="how-to-start-using-serverless-sql-pool"></a>Como começar a usar a piscina SQL sem servidor

O ponto final da piscina SEM servidor SQL é fornecido em cada espaço de trabalho da Azure Synapse. Pode criar um espaço de trabalho e começar a consultar dados instantaneamente utilizando ferramentas que lhe são familiares.

## <a name="client-tools"></a>Ferramentas do cliente

O pool SQL sem servidor permite que as ferramentas de consulta ad-hoc sql existentes e ferramentas de inteligência empresarial acamem no lago de dados. Como fornece uma sintaxe T-SQL familiar, qualquer ferramenta capaz de estabelecer ofertas DE SQL de ligação TDS pode [ligar-se e consultar a Sinaapse SQL a](connect-overview.md) pedido. Pode conectar-se com o Azure Data Studio e executar consultas ad-hoc ou conectar-se com o Power BI para obter informações numa questão de minutos.

## <a name="t-sql-support"></a>Suporte T-SQL

O pool sem servidor SQL oferece área de superfície de consulta T-SQL, que é ligeiramente melhorada/estendida em alguns aspetos para acomodar para experiências em torno de consulta de dados semi-estruturados e não estruturados. Além disso, alguns aspetos da língua T-SQL não são suportados devido ao design de piscina SQL sem servidor, como exemplo, a funcionalidade DML não é atualmente suportada.

- A carga de trabalho pode ser organizada usando conceitos familiares:
- Bases de dados - o ponto final da piscina SQL sem servidor pode ter várias bases de dados.
- Schemas - Dentro de uma base de dados, pode haver um ou muitos grupos de propriedade de objetos chamados esquemas.
- Vistas
- Recursos externos – fontes de dados, formatos de ficheiros e tabelas

A segurança pode ser aplicada utilizando:

- Inícios de sessão e utilizadores
- Credenciais para controlar o acesso a contas de armazenamento
- Conceder, negar e revogar permissões por nível de objeto
- Integração do Azure Active Directory

T-SQL suportado:

- A área de superfície [select](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) full select é suportada, incluindo a maioria das funções SQL
- CETAS - CRIE UMA TABELA EXTERNA COMO SELEÇÃO
- Declarações DDL relacionadas apenas com pontos de vista e segurança

O pool SQL sem servidor não tem armazenamento local, apenas os objetos de metadados são armazenados em bases de dados. Portanto, a T-SQL relacionada com os seguintes conceitos não é suportada:

- Tabelas
- Acionadores
- Vistas materializadas
- Declarações DDL que não as relacionadas com vistas e segurança
- Declarações DML

### <a name="extensions"></a>Extensões

De forma a permitir uma experiência suave para a consulta em vigor de dados que residem em ficheiros em data lake, o pool SQL sem servidor alarga a função [openrowset](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) existente, adicionando as seguintes capacidades:

[Consultar vários ficheiros ou pastas](query-data-storage.md#query-multiple-files-or-folders)

[Formato de ficheiro PARQUET](query-data-storage.md#query-parquet-files)

[Opções adicionais para trabalhar com textolimitado (exterminador de campo, exterminador de linha, char de fuga)](query-data-storage.md#query-csv-files)

[Leia um subconjunto de colunas escolhido](query-data-storage.md#read-a-chosen-subset-of-columns)

[Inferência do esquema](query-data-storage.md#schema-inference)

[função de nome de arquivo](query-data-storage.md#filename-function)

[função de filepa](query-data-storage.md#filepath-function)

[Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Segurança

O pool SQL sem servidor oferece mecanismos para garantir o acesso aos seus dados.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

O pool SQL sem servidor permite-lhe gerir centralmente identidades de utilizador de base de dados e outros serviços da Microsoft com [integração do Azure Ative Directory](../../azure-sql/database/authentication-aad-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Ative Directory (Azure AD) suporta a [autenticação de vários fatores](../../azure-sql/database/authentication-mfa-ssms-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) para aumentar a segurança de dados e aplicações, suportando um único processo de inscrição.

#### <a name="authentication"></a>Autenticação

A autenticação da piscina SQL sem servidor refere-se à forma como os utilizadores provam a sua identidade ao ligarem-se ao ponto final. São suportados dois tipos de autenticação:

- **Autenticação SQL**

  Este método de autenticação utiliza um nome de utilizador e uma palavra-passe.

- **Autenticação do Diretório Ativo Azure:**

  Este método de autenticação utiliza identidades geridas pelo Azure Ative Directory. Para os utilizadores AD Azure, a autenticação multi-factor pode ser ativada. Utilize a autenticação do Active Directory (segurança integrada) [sempre que possível](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

#### <a name="authorization"></a>Autorização

A autorização refere-se ao que um utilizador pode fazer dentro de uma base de dados de piscinas SQL sem servidor, e é controlada pelos membros da função de base de dados da sua conta de utilizador e permissões de nível de objeto.

Se a autenticação SQL for utilizada, o utilizador SQL existe apenas na piscina SQL sem servidor e as permissões são miradas para os objetos na piscina SQL sem servidor. O acesso a objetos securáveis noutros serviços (como o Azure Storage) não pode ser concedido diretamente ao utilizador SQL, uma vez que só existe no âmbito da piscina SQL sem servidor. O utilizador SQL precisa de utilizar um dos tipos de [autorização suportados](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) para aceder aos ficheiros.

Se a autenticação AZure AD for utilizada, um utilizador pode iniciar sação na piscina SQL sem servidor e outros serviços, como o Azure Storage, e pode conceder permissões ao utilizador Azure AD.

### <a name="access-to-storage-accounts"></a>Acesso a contas de armazenamento

Um utilizador que esteja registado no serviço de piscina SQL sem servidor deve ser autorizado a aceder e consultar os ficheiros no Azure Storage. a piscina SQL sem servidor suporta os seguintes tipos de autorização:

- **A assinatura de acesso partilhado (SAS)** proporciona acesso delegado aos recursos na conta de armazenamento. Com um SAS, pode conceder aos clientes acesso a recursos na conta de armazenamento, sem partilhar chaves de conta. Um SAS dá-lhe controlo granular sobre o tipo de acesso que concede aos clientes que têm o SAS: intervalo de validade, permissões concedidas, intervalo de endereço IP aceitável, protocolo aceitável (https/http).

- **Identidade do Utilizador** (também conhecido como "pass-through") é um tipo de autorização onde a identidade do utilizador Azure AD que iniciou sessão no pool SQL sem servidor é usada para autorizar o acesso aos dados. Antes de aceder aos dados, o administrador do Azure Storage deve conceder permissões ao utilizador Azure AD para aceder aos dados. Este tipo de autorização utiliza o utilizador Azure AD que iniciou sessão na piscina SQL sem servidor, pelo que não é suportado para tipos de utilizadores SQL.

## <a name="next-steps"></a>Passos seguintes
Informações adicionais sobre a ligação de pontos finais e ficheiros de consulta podem ser encontradas nos seguintes artigos: 
- [Ligue-se ao seu ponto final](connect-overview.md)
- [Consultar os seus ficheiros](develop-storage-files-overview.md)
