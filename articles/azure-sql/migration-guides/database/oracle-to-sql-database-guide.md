---
title: 'Oracle para Azure SQL Base de Dados: Guia de migração'
description: Neste guia, aprende a migrar o seu esquema Oráculo para a Base de Dados Azure SQL utilizando o SqL Server Migration Assistant para a Oracle.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 9ae7e8c4544d2e8bd9dc4ff846aabb0c7f7f9358
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284345"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Guia de migração: Oracle para Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Neste guia, aprende [a migrar](https://azure.microsoft.com/migration/migration-journey) os seus esquemas Oráculos para Azure SQL Database utilizando o [SqL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for Oracle (SSMA for Oracle).

Para outros guias de migração, consulte os [Guias de Migração da Base de Dados Azure.](https://docs.microsoft.com/data-migration)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a migrar o seu esquema Oráculo para a Base de Dados SQL:

- Verifique se o ambiente de origem está suportado.
- Baixar [SSMA para o Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
- Tenha uma instância de [base de dados SQL](../../database/single-database-create-quickstart.md) alvo.
- Obtenha as [permissões necessárias para sSMA para a Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) e [fornecedor.](/sql/ssma/oracle/connect-to-oracle-oracletosql)
 
## <a name="pre-migration"></a>Pré-migração

Depois de conhecer os pré-requisitos, está pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua [migração em nuvem Azure.](https://azure.microsoft.com/migration) Esta parte do processo envolve a realização de um inventário das bases de dados que precisa de migrar, avaliando essas bases de dados para potenciais problemas de migração ou bloqueadores, e, em seguida, resolver quaisquer itens que possa ter descoberto.

### <a name="assess"></a>Avaliação

Ao utilizar o SSMA para a Oracle, pode rever objetos e dados de base de dados, avaliar bases de dados para migração, migrar objetos de base de dados para a Base de Dados SQL e, finalmente, migrar dados para a base de dados.

Para criar uma avaliação:

1. [SSMA aberto para o oráculo.](https://www.microsoft.com/download/details.aspx?id=54258)
1. Selecione **'Ficheiro'** e, em seguida, selecione **Novo Projeto**.
1. Insira um nome de projeto e um local para salvar o seu projeto. Em seguida, selecione **Azure SQL Database** como o alvo de migração da lista de drop-down e selecione **OK**.

   ![Screenshot que mostra Connect to Oracle.](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. Selecione **Connect to Oracle**. Introduza os valores para os detalhes da ligação Oracle na caixa de diálogo **Connect to Oracle.**

1. Selecione os esquemas do Oráculo que pretende migrar.

   ![Screenshot que mostra a seleção do esquema do Oráculo.](./media/oracle-to-sql-database-guide/select-schema.png)

1. No **Oracle Metadata Explorer,** clique com o botão direito no esquema do Oráculo que pretende migrar e, em seguida, selecione **Criar Relatório** para gerar um relatório HTML. Em alternativa, pode selecionar uma base de dados e, em seguida, selecionar o separador **'Criar Relatório'.**

   ![Screenshot que mostra Criar Relatório.](./media/oracle-to-sql-database-guide/create-report.png)

1. Reveja o relatório HTML para compreender as estatísticas de conversão e quaisquer erros ou avisos. Também pode abrir o relatório no Excel para obter um inventário de objetos Oráculos e o esforço necessário para realizar conversões de esquemas. A localização predefinição do relatório está na pasta do relatório dentro dos Projetos SSMA.

   Por exemplo, veja `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Screenshot que mostra um relatório de avaliação.](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Validar os tipos de dados

Valide os mapeamentos do tipo de dados predefinidos e altere-os com base nos requisitos, se necessário. Para tal, siga estes passos:

1. Em SSMA para Oráculo, selecione **Ferramentas** e, em seguida, selecione **Definições de Projeto**.
1. Selecione o **separador 'Tipo de Mapeamento'.**

   ![Screenshot que mostra mapeamento do tipo.](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Pode alterar o mapeamento do tipo para cada tabela selecionando a tabela no **Oracle Metadata Explorer**.

### <a name="convert-the-schema"></a>Converter o esquema

Para converter o esquema:

1. (Opcional) Adicione consultas dinâmicas ou ad-hoc a declarações. Clique com o botão direito no nó e, em seguida, **selecione Adicionar declarações**.
1. Selecione o **separador 'Connect to Azure SQL Database'.**
    1. Na **Base de Dados SQL,** introduza os dados de ligação para ligar a sua base de dados.
    1. Selecione a sua instância de base de dados SQL alvo a partir da lista de drop-down, ou introduza um novo nome, caso em que uma base de dados será criada no servidor alvo.
    1. Introduza detalhes de autenticação e selecione **Connect**.

    ![Screenshot que mostra Connect to Azure SQL Database.](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. No **Oracle Metadata Explorer,** clique com o botão direito do esquema do Oráculo e, em seguida, selecione **Convert Schema**. Em alternativa, pode selecionar o seu esquema e, em seguida, selecionar o **separador Converte-o.S.**

   ![Screenshot que mostra Converte Schema.](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Após os acabamentos da conversão, compare e reveja os objetos convertidos aos objetos originais para identificar potenciais problemas e abordá-los com base nas recomendações.

   ![Screenshot que mostra o esquema de recomendações de Revisão.](./media/oracle-to-sql-database-guide/table-mapping.png)

1. Compare o texto Transact-SQL convertido com os procedimentos armazenados originais e reveja as recomendações.

   ![Screenshot que mostra as recomendações de Revisão.](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. No painel de saída, selecione **os resultados da revisão** e reveja os erros no painel **de listas** de erros.
1. Guarde o projeto localmente para um exercício de remediação de esquemas offline. No menu **Ficheiro,** **selecione Save Project**. Este passo dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de publicar o esquema para a Base de Dados SQL.

## <a name="migrate"></a>Migrate

Depois de ter avaliado as suas bases de dados e abordado quaisquer discrepâncias, o próximo passo é executar o processo de migração. A migração envolve dois passos: a publicação do esquema e a migração dos dados.

Para publicar o seu esquema e migrar os seus dados:

1. Publique o esquema clicando à direita na base de dados a partir do nó **de bases de dados** no **Azure SQL Database Medata Explorer** e selecione **Synchronize com Base de Dados**.

   ![Screenshot que mostra Sincronização com Base de Dados.](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. Reveja o mapeamento entre o seu projeto de origem e o seu alvo.

   ![Screenshot que mostra Sincronização com a revisão da Base de Dados.](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. Migrar os dados clicando corretamente na base de dados ou no objeto que pretende migrar no **Oracle Metadata Explorer** e selecionar **Dados Migratórios**. Em alternativa, pode selecionar o **separador Dados Migratórios.** Para migrar dados para uma base de dados inteira, selecione a caixa de verificação ao lado do nome da base de dados. Para migrar dados de tabelas individuais, expandir a base de **dados,** expandir tabelas e, em seguida, selecionar as caixas de verificação ao lado das tabelas. Para omitir dados de tabelas individuais, limpe as caixas de verificação.

   ![Screenshot que mostra Dados Migratórios.](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Introduza os detalhes da ligação tanto para a Oracle como para a Base de Dados SQL.
1. Após a conclusão da migração, consulte o **Relatório de Migração de Dados**.

   ![Screenshot que mostra o Relatório de Migração de Dados.](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Conecte-se à sua 22-base de dados utilizando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)e valide a migração através da revisão dos dados e esquemas.

   ![Screenshot que mostra validação no SQL Server Management Studio.](./media/oracle-to-sql-database-guide/validate-data.png)

Em alternativa, também pode utilizar os Serviços de Integração de Servidor SQL para realizar a migração. Para saber mais, veja:

- [Começar com serviços de integração de servidores SQL](/sql/integration-services/sql-server-integration-services)
- [Serviços de integração de servidores SQL para movimento de dados Azure e Híbrido](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Pós-migração

Depois de ter concluído com sucesso a fase *de migração,* precisa de completar uma série de tarefas pós-migração para garantir que tudo está a funcionar da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização desta tarefa exigirá alterações nas aplicações em alguns casos.

O [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) é uma extensão para o Código do Estúdio Visual que permite analisar o seu código fonte Java e detetar chamadas e consultas de acesso a dados. O conjunto de ferramentas fornece-lhe uma visão única do que precisa de ser endereçado para suportar a nova base de dados. Para saber mais, consulte as [aplicações De Migração de Java a partir do](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) post de blog da Oracle.

### <a name="perform-tests"></a>Realizar testes

A abordagem de ensaio da migração da base de dados consiste nas seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração da base de dados, é necessário utilizar consultas SQL. Tem de criar as consultas de validação para correr contra a fonte e as bases de dados-alvo. As suas consultas de validação devem cobrir o âmbito que definiu.
1. **Configurar um ambiente de ensaio**: O ambiente de ensaio deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.
1. **Executar testes de validação**: Escorram testes de validação com a fonte e o alvo e analisem os resultados.
1. **Executar testes de desempenho**: Executar testes de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.

### <a name="optimize"></a>Otimização

A fase pós-migração é crucial para conciliar quaisquer problemas de precisão de dados, verificar a completude e abordar problemas de desempenho com a carga de trabalho.

> [!NOTE]
> Para obter mais informações sobre estas questões e os passos para os mitigar, consulte o [guia de validação e otimização pós-migração.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Ativos de migração

Para obter mais assistência na conclusão deste cenário de migração, consulte os seguintes recursos. Foram desenvolvidas em apoio a um projeto de migração no mundo real.

| **Título/ligação**                                                                                                                                          | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações ou bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo um processo de decisão de plataforma-alvo automatizado e uniforme.                                                          |
| [Artefactos de script de inventário de oráculo](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Este ativo inclui uma consulta PL/SQL que atinge as tabelas do sistema Oracle e fornece uma contagem de objetos por tipo de esquema, tipo de objeto e estado. Também fornece uma estimativa aproximada de dados brutos em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados em formato CSV.                                                                                                               |
| [Automatizar a coleção de avaliação da SSMA Oracle & Consolidação](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Este conjunto de recursos utiliza um ficheiro .csv como entrada (sources.csv nas pastas do projeto) para produzir os ficheiros xml necessários para executar uma avaliação SSMA no modo consola. O source.csv é fornecido pelo cliente com base num inventário dos casos existentes da Oracle. Os ficheiros de saída são AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [SSMA para erros comuns da Oracle e como corrigi-los](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Com a Oracle, pode atribuir uma condição nãocalar na cláusula WHERE. No entanto, o SQL Server não suporta este tipo de condição. Como resultado, o SSMA para a Oracle não converte consultas com uma condição nãocalar na cláusula WHERE. Em vez disso, gera o erro O2SS0001. Este livro branco fornece mais detalhes sobre a questão e formas de o resolver.          |
| [Manual de migração do Oráculo para o SqL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Este documento centra-se nas tarefas associadas à migração de um esquema Oráculo para a versão mais recente da Base de Dados do Servidor SQL. Se a migração necessitar de alterações nas funcionalidades ou funcionalidades, o possível impacto de cada alteração nas aplicações que utilizam a base de dados deve ser cuidadosamente considerado.                                                     |

A equipa de Engenharia Data SQL desenvolveu estes recursos. A principal carta desta equipa é desbloquear e acelerar a modernização complexa dos projetos de migração da plataforma de dados para a plataforma de dados Azure da Microsoft.

## <a name="next-steps"></a>Passos seguintes

- Para uma matriz de serviços e ferramentas de terceiros da Microsoft que estão disponíveis para o ajudar com vários cenários de migração de dados e dados e tarefas especiais, consulte [Serviços e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Base de Dados SQL, consulte:
  - [Uma visão geral da Base de Dados Azure SQL](../../database/sql-database-paas-overview.md)
  - [Calculadora Azure Total Cost of Ownership (TCO)](https://azure.microsoft.com/pricing/tco/calculator/)

- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, consulte:
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Melhores práticas para custos e dimensionamento de cargas de trabalho para a migração para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Recursos migratórios em nuvem](https://azure.microsoft.com/migration/resources)

- Para conteúdos de vídeo, consulte:
    - [Panorâmica da viagem de migração e dos instrumentos e serviços recomendados para a avaliação e migração](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
