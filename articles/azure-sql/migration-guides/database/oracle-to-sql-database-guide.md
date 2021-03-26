---
title: 'Oracle para Azure SQL Base de Dados: Guia de migração'
description: Este guia ensina-o a migrar o seu esquema Oráculo para a Base de Dados Azure SQL utilizando o SqL Server Migration Assistant for Oracle (SSMA for Oracle).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 62b1924a1aeb1c1e5bc06ec0783d83d6918d162c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557926"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Guia de migração: Oracle para Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina-o a migrar os seus esquemas Oráculos para a Base de Dados Azure SQL utilizando o SqL Server Migration Assistant para o Oráculo.

Para outros guias de migração, consulte [a Migração da Base de Dados.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar o seu esquema Oráculo para a Base de Dados SQL, precisa: 

- Para verificar se o ambiente de origem é suportado. 
- Para baixar [o SqL Server Migration Assistant (SSMA) para o Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
- Uma [base de dados Azure SQL .](../../database/single-database-create-quickstart.md) 
- As [permissões necessárias para a SSMA para a Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) e para o [fornecedor.](/sql/ssma/oracle/connect-to-oracle-oracletosql)
 

## <a name="pre-migration"></a>Pré-migração

Depois de conhecer os pré-requisitos, está pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração. Esta parte do processo envolve a realização de um inventário das bases de dados que precisa de migrar, avaliando essas bases de dados para potenciais problemas de migração ou bloqueadores, e, em seguida, resolver quaisquer itens que possa ter descoberto.



### <a name="assess"></a>Avaliação 


Utilize o SqL Server Migration Assistant (SSMA) para a Oracle para analisar objetos e dados de base de dados, avaliar bases de dados para migração, migrar objetos de base de dados para a Base de Dados Azure SQL e, finalmente, migrar dados para a base de dados. 

Para criar uma avaliação, siga estes passos: 

1. Abrir [o assistente de migração do servidor SQL para o oráculo](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selecione **File** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar o seu projeto e, em seguida, selecione Azure SQL Database como o alvo de migração a partir do drop-down. Selecione **OK**:

   ![Novo Projeto](./media/oracle-to-sql-database-guide/new-project.png)

1. Selecione **Connect to Oracle**. Introduza os valores para os detalhes da ligação Oracle na caixa de diálogo **Connect to Oracle:**

   ![Ligar ao Oráculo](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

   Selecione os esquemas do Oráculo que pretende migrar: 

   ![Selecione esquema Oráculo](./media/oracle-to-sql-database-guide/select-schema.png)

1. Clique com o botão direito no esquema do Oráculo que pretende migrar no **Oracle Metadata Explorer** e, em seguida, escolha **Criar relatório**. Isto gerará um relatório HTML. Em alternativa, pode escolher **Criar relatório** a partir da barra de navegação depois de selecionar a base de dados:

   ![Criar Relatório](./media/oracle-to-sql-database-guide/create-report.png)

1. Reveja o relatório HTML para compreender as estatísticas de conversão e quaisquer erros ou avisos. Também pode abrir o relatório no Excel para obter um inventário de objetos Oráculos e o esforço necessário para realizar conversões de esquemas. A localização predefinição do relatório está na pasta do relatório dentro dos Projetos SSMA.

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Relatório de Avaliação](./media/oracle-to-sql-database-guide/assessment-report.png) 



### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos do tipo de dados predefinidos e altere-os com base nos requisitos, se necessário. Para tal, siga estes passos:

1. Selecione **Ferramentas** do menu. 
1. Selecione **Definições de projetos**. 
1. Selecione o **separador de mapeamentos Tipo:** 

   ![Mapeamentos de tipo](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Pode alterar o mapeamento do tipo para cada tabela selecionando a tabela no **Oracle Metadata Explorer**.

### <a name="convert-schema"></a>Converter esquema

Para converter o esquema, siga estes passos: 

1. (Opcional) Adicione consultas dinâmicas ou ad-hoc a declarações. Clique com o botão direito no nó e, em seguida, escolha **Adicionar declarações**.
1. Selecione **Connect to Azure SQL Database**. 
    1. Introduza os dados de ligação para ligar a sua base de dados na Base de Dados Azure SQL.
    1. Escolha a base de dados SQL alvo a partir do drop-down ou forneça um novo nome, caso em que uma base de dados será criada no servidor alvo. 
    1. Forneça detalhes de autenticação. 
    1. Selecione **Connect**:

    ![Ligar à Base de dados SQL](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)


1. Clique com o botão direito no esquema do **Oráculo** no Oracle Metadata Explorer e, em seguida, escolha **Converter o Schema**. Em alternativa, pode escolher **converter o Schema** na barra de navegação superior depois de selecionar o seu esquema:

   ![Converter Schema](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Após a conversão concluída, compare e reveja os objetos convertidos aos objetos originais para identificar potenciais problemas e abordá-los com base nas recomendações:

   ![Rever esquema de recomendações](./media/oracle-to-sql-database-guide/table-mapping.png)

   Compare o texto Transact-SQL convertido com os procedimentos armazenados originais e reveja as recomendações:

   ![Recomendações de revisão](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. Selecione **Os resultados** da Revisão no painel de saída e os erros de revisão no painel **da lista de erros.** 
1. Guarde o projeto localmente para um exercício de remediação de esquemas offline. Selecione **Save Project** a partir do menu **'Ficheiro'.** Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de poder publicar o esquema para a Base de Dados SQL.

## <a name="migrate"></a>Migrate

Depois de ter concluído a avaliação das suas bases de dados e de abordar eventuais discrepâncias, o próximo passo é executar o processo de migração. A migração envolve dois passos – publicar o esquema e migrar os dados. 

Para publicar o seu esquema e migrar os seus dados, siga estes passos:

1. Publique o esquema: Clique à direita na base de dados a partir do nó de bases de dados no **Azure SQL Database Medata Explorer** e escolha **Sincronizar com Base de Dados:** 

   ![Sincronizar com base de dados](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

   Reveja o mapeamento entre o seu projeto de origem e o seu alvo:

   ![Sincronizar com a revisão da base de dados](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)


1. Migrar os dados: Clique na base de dados ou no objeto que pretende migrar no **Oracle Metadata Explorer** e escolha **dados migratórios**. Em alternativa, pode **selecionar Dados Migratórios** a partir da barra de navegação de primeira linha. Para migrar dados para uma base de dados inteira, selecione a caixa de verificação ao lado do nome da base de dados. Para migrar dados de tabelas individuais, expandir a base de dados, expandir tabelas e, em seguida, selecionar a caixa de verificação ao lado da tabela. Para omitir dados de tabelas individuais, limpe a caixa de verificação:

   ![Dados migratórios](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Forneça detalhes de conexão tanto para a Oracle como para a Azure SQL Database.
1. Após a conclusão da migração, consulte o **Relatório de Migração de Dados:**  

   ![Relatório de Migração de Dados](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Conecte-se à sua Base de Dados Azure SQL utilizando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração através da revisão dos dados e esquemas:

   ![Validar em SSMA](./media/oracle-to-sql-database-guide/validate-data.png)

Em alternativa, também pode utilizar os Serviços de Integração de Servidores SQL (SSIS) para realizar a migração. Para saber mais, veja: 

- [Começar com serviços de integração de servidores SQL](/sql/integration-services/sql-server-integration-services)
- [Serviços de Integração de Servidores SQL: SSIS para movimento de dados Azure e Híbrido](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)


## <a name="post-migration"></a>Pós-migração 

Depois de ter concluído com sucesso a fase **de Migração,** precisa de passar por uma série de tarefas pós-migração para garantir que tudo está a funcionar da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização deste facto exigirá, em alguns casos, alterações nas aplicações.

O [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) é uma extensão para o Código do Estúdio Visual que permite analisar o seu código fonte Java e detetar chamadas e consultas de acesso a dados, proporcionando-lhe uma visão única do que precisa de ser endereçado para suportar a nova base de dados. Para saber mais, consulte a [aplicação Da Migração java do](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) blog Da Oracle. 



### <a name="perform-tests"></a>Realizar testes

A abordagem de ensaio para a migração da base de dados consiste na realização das seguintes atividades:

1.  **Desenvolver testes de validação.** Para testar a migração da base de dados, precisa de utilizar consultas SQL. Tem de criar as consultas de validação para correr contra a fonte e as bases de dados-alvo. As suas consultas de validação devem abranger o âmbito que definiu.

2.  **Configurar ambiente de teste.** O ambiente de teste deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.

3.  **Executar testes de validação**. Executar os testes de validação contra a fonte e o alvo e, em seguida, analisar os resultados.

4.  **Executar testes de desempenho**. Executar o teste de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.


### <a name="optimize"></a>Otimização

A fase pós-migração é crucial para conciliar quaisquer problemas de precisão de dados e verificar a completude, bem como abordar problemas de desempenho com a carga de trabalho.

> [!NOTE]
> Para mais detalhes sobre estas questões e medidas específicas para os mitigar, consulte o [Guia de Validação e Otimização pós-migração.](/sql/relational-databases/post-migration-validation-and-optimization-guide)


## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional para completar este cenário de migração, consulte os seguintes recursos, que foram desenvolvidos em apoio de um envolvimento de projetos de migração no mundo real.

| **Título/ligação**                                                                                                                                          | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda muito a acelerar as grandes avaliações imobiliárias, fornecendo e automatizada e uniforme processo de decisão da plataforma-alvo.                                                          |
| [Artefactos de script de inventário de oráculo](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Este ativo inclui uma consulta PL/SQL que atinge as tabelas do sistema Oracle e fornece uma contagem de objetos por tipo de esquema, tipo de objeto e estado. Também fornece uma estimativa aproximada de 'Dados Brutos' em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados num formato CSV.                                                                                                               |
| [Automatizar a coleção de avaliação da SSMA Oracle & Consolidação](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Este conjunto de recursos utiliza um ficheiro .csv como entrada (sources.csv nas pastas do projeto) para produzir os ficheiros xml necessários para executar a avaliação SSMA no modo consola. O source.csv é fornecido pelo cliente com base num inventário dos casos existentes da Oracle. Os ficheiros de saída são AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [SSMA para os erros comuns da Oracle e como corrigi-los](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Com a Oracle, pode atribuir uma condição não-escalar na cláusula WHERE. No entanto, o SQL Server não suporta este tipo de condição. Como resultado, o SQL Server Migration Assistant (SSMA) para a Oracle não converte consultas com uma condição não-escalar na cláusula WHERE, em vez de gerar um erro O2SS001. Este livro branco fornece mais detalhes sobre a questão e formas de o resolver.          |
| [Manual de migração do Oráculo para o SqL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Este documento centra-se nas tarefas associadas à migração de um esquema Oráculo para a versão mais recente da base do SQL Server. Se a migração exigir alterações nas funcionalidades/funcionalidades, então o possível impacto de cada alteração nas aplicações que utilizam a base de dados deve ser cuidadosamente considerado.                                                     |

Estes recursos foram desenvolvidos como parte do Programa Ninja Data SQL, que é patrocinado pela equipa de engenharia do Azure Data Group. A carta principal do programa Data SQL Ninja é desbloquear e acelerar a modernização complexa e competir oportunidades de migração da plataforma de dados para a plataforma de dados Azure Data da Microsoft. Se acha que a sua organização estaria interessada em participar no programa Data SQL Ninja, contacte a sua equipa de conta e peça-lhes que apresentem uma nomeação.

## <a name="next-steps"></a>Passos seguintes

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de migração de dados e dados, bem como tarefas especiais, consulte o serviço de [artigos e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Base de Dados Azure SQL, consulte: 
  - [Uma visão geral da Base de Dados Azure SQL](../../database/sql-database-paas-overview.md)
  - [Calculadora Azure Total Cost of Ownership (TCO)](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para conteúdos de vídeo, consulte: 
    - [Panorâmica da viagem de migração e dos instrumentos/serviços recomendados para a avaliação e migração](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)