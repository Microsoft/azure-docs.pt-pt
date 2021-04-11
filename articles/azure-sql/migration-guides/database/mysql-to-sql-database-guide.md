---
title: 'MySQL a Azure SQL Base de Dados: Guia de migração'
description: Neste guia, aprende a migrar as suas bases de dados MySQL para uma base de dados Azure SQL utilizando o SqL Server Migration Assistant para o MySQL (SSMA for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 32c56df5bafa9439fc559edf137c1080920cfb32
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284379"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Guia de migração: MySQL para Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Neste guia, aprende [a migrar a](https://azure.microsoft.com/migration/migration-journey) sua base de dados MySQL para uma base de dados Azure SQL utilizando o [SqL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant para o MySQL (SSMA for MySQL). 

Para outros guias de migração, consulte [o Guia de Migração da Base de Dados Azure.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a migrar a sua base de dados MySQL para uma base de dados SQL, faça o seguinte:

- Verifique se o ambiente de origem está suportado. Atualmente, o MySQL 5.6 e 5.7 são suportados. 
- Descarregue e instale [o SqL Server Migration Assistant para o MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Certifique-se de que tem conectividade e permissões suficientes para aceder tanto à fonte como ao alvo.

## <a name="pre-migration"></a>Pré-migração 

Depois de conhecer os pré-requisitos, está pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua [migração em nuvem Azure.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Avaliação 

Utilize o SqL Server Migration Assistant (SSMA) para o MySQL para analisar objetos e dados de base de dados e avaliar bases de dados para migração. 

Para criar uma avaliação, faça o seguinte:

1. [SSMA aberto para o MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Selecione **'Ficheiro'** e, em seguida, selecione **Novo Projeto**. 
1. No painel new **project,** insira um nome e localização para o seu projeto e, em seguida, na lista **migrar para** baixo, selecione **Azure SQL Database**. 
1. Selecione **OK**.

   ![Screenshot do painel "New Project" para introduzir o nome, localização e alvo do seu projeto de migração.](./media/mysql-to-sql-database-guide/new-project.png)

1. Selecione o **separador 'Ligar para o MySQL'** e, em seguida, fornecer detalhes para ligar o servidor MySQL. 

   ![Screenshot do painel "Connect to MySQL" para especificar ligações à fonte.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. No painel **MySQL Metadata Explorer,** clique com o botão direito no esquema MySQL e, em seguida, selecione **Criar Relatório**. Em alternativa, pode selecionar o separador **'Criar Relatório'** no canto superior direito.

   ![Screenshot das ligações "Create Report" em SSMA para o MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Reveja o relatório HTML para compreender as estatísticas de conversão, erros e avisos. Analise-o para compreender as questões e resoluções de conversão. 
   Também pode abrir o relatório no Excel para obter um inventário de objetos MySQL e entender o esforço necessário para realizar conversões de esquemas. A localização predefinição do relatório está na pasta do relatório dentro dos Projetos SSMA. Por exemplo: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Screenshot de um relatório de conversão de exemplo em SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Validar os tipos de dados

Valide os mapeamentos do tipo de dados predefinidos e altere-os com base em requisitos, se necessário. Para tal: 

1. Selecione **Ferramentas** e, em seguida, selecione **Definições de Projeto**.  
1. Selecione o **separador 'Mapeamentos'** tipo. 

   ![Screenshot do painel "Type Mapping" em SSMA para o MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Pode alterar o mapeamento de tipo para cada tabela selecionando o nome da tabela no painel **MySQL Metadata Explorer.** 

### <a name="convert-the-schema"></a>Converter o esquema 

Para converter o esquema, faça o seguinte: 

1. (Opcional) Para converter consultas dinâmicas ou especializadas, clique com o nó à direita e, em seguida, **selecione Adicionar a declaração**. 

1. Selecione o **separador 'Ligar ao Azure SQL Database'** e, em seguida, faça o seguinte:

   a. Introduza os detalhes para a ligação à sua base de dados SQL.  
   b. Na lista de drop-down, selecione a base de dados SQL alvo. Ou pode fornecer um novo nome, caso em que uma base de dados será criada no servidor alvo.  
   c. Forneça detalhes de autenticação.  
   d. Selecione **Ligar**.

   ![Screenshot do painel "Connect to Azure SQL Database" em SSMA para o MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Clique com o botão direito no esquema com o qual está a trabalhar e, em seguida, selecione **Convert Schema**. Em alternativa, pode selecionar o **separador de esquemas Converte-se** no canto superior direito.

   ![Screenshot do comando "Convert Schema" no painel "MySQL Metadata Explorer".](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Após a conversão estar concluída, reveja e compare os objetos convertidos com os objetos originais para identificar potenciais problemas e resolvê-los com base nas recomendações. 

   ![Screenshot mostrando uma comparação dos objetos convertidos com os objetos originais.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Compare o texto Transact-SQL convertido com o código original e reveja as recomendações.

   ![Screenshot mostrando uma comparação de consultas convertidas com o código fonte.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. No painel **de saída,** selecione **os resultados da revisão** e, em seguida, reveja quaisquer erros no painel **da lista de erros.** 
1. Guarde o projeto localmente para um exercício de remediação de esquemas offline. Para tal, selecione Projeto de Poupança **de**  >  **Ficheiros**. Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de publicar o esquema na sua base de dados SQL.

   Compare os procedimentos convertidos com os procedimentos originais, como mostrado aqui: 

   ![Screenshot mostrando uma comparação dos procedimentos convertidos com os procedimentos originais.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Migrar as bases de dados 

Depois de ter avaliado as suas bases de dados e abordado quaisquer discrepâncias, pode executar o processo de migração. A migração envolve dois passos: a publicação do esquema e a migração dos dados. 

Para publicar o esquema e migrar os dados, faça o seguinte: 

1. Publique o esquema. No painel **Azure SQL Database Metadata Explorer,** clique com o botão direito na base de dados e, em seguida, selecione **Synchronize com Base de Dados**. Esta ação publica o esquema MySQL na sua base de dados SQL.

   ![Screenshot do painel "Sincronizar com a Base de Dados" para rever o mapeamento da base de dados.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrar os dados. No painel **MySQL Metadata Explorer,** clique com o botão direito no esquema MySQL que pretende migrar e, em seguida, selecione **Dados migratórios**. Em alternativa, pode selecionar o **separador Dados Migratórios** no canto superior direito.

   Para migrar dados para uma base de dados inteira, selecione a caixa de verificação ao lado do nome da base de dados. Para migrar dados de tabelas individuais, expandir a base de **dados,** expandir tabelas e, em seguida, selecionar a caixa de verificação ao lado da tabela. Para omitir dados de tabelas individuais, limpe a caixa de verificação.

   ![Screenshot do comando "Dados Migratórios" no painel "MySQL Metadata Explorer".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Após a conclusão da migração, consulte o **Relatório de Migração de Dados**.
   
   ![Screenshot do Relatório de Migração de Dados.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Conecte-se à sua base de dados SQL utilizando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração através da revisão dos dados e esquemas.

   ![Screenshot do SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Pós-migração 

Depois de ter concluído com sucesso a fase *de migração,* precisa de completar uma série de tarefas pós-migração para garantir que tudo está a funcionar da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização deste facto exigirá, em alguns casos, alterações nas aplicações.

### <a name="perform-tests"></a>Realizar testes

A abordagem de ensaio da migração da base de dados consiste nas seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração da base de dados, é necessário utilizar consultas SQL. Tem de criar as consultas de validação para correr contra as bases de dados de origem e alvo. As suas consultas de validação devem cobrir o âmbito que definiu.

1. **Configurar um ambiente de ensaio**: O ambiente de ensaio deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.

1. **Executar testes de validação**: Escorram testes de validação com a fonte e o alvo e analisem os resultados.

1. **Executar testes de desempenho**: Executar testes de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.

### <a name="optimize"></a>Otimização

A fase pós-migração é crucial para conciliar quaisquer problemas de precisão de dados, verificar a completude e abordar problemas de desempenho com a carga de trabalho.

Para obter mais informações sobre estas questões e os passos para os mitigar, consulte o [guia de validação e otimização pós-migração.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Ativos de migração

Para obter mais assistência para completar este cenário de migração, consulte o seguinte recurso. Foi desenvolvido em apoio a um projeto de migração no mundo real.

| Título | Descrição |
| --- | --- |
| [Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Fornece plataformas-alvo "mais adequadas", prontidão na nuvem e níveis de remediação de aplicações/bases de dados para cargas de trabalho especificadas. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo um processo automatizado e uniforme de decisão da plataforma-alvo. |

A equipa de Engenharia Data SQL desenvolveu estes recursos. A principal carta desta equipa é desbloquear e acelerar a modernização complexa dos projetos de migração da plataforma de dados para a plataforma de dados Azure da Microsoft.

## <a name="next-steps"></a>Passos seguintes 

- Para ajudar a estimar as economias de custos que pode realizar ao migrar as suas cargas de trabalho para Azure, consulte o [custo total do Azure da calculadora de propriedade.](https://aka.ms/azure-tco)

- Para uma matriz de serviços e ferramentas de terceiros da Microsoft que estão disponíveis para o ajudar com vários cenários de migração de dados e dados e tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para outros guias de migração, consulte [o Guia de Migração da Base de Dados Azure.](https://datamigration.microsoft.com/) 

- Para vídeos de migração, consulte [a visão geral da viagem de migração e recomendados ferramentas e serviços de migração e avaliação.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)

- Para obter mais [recursos de migração em nuvem,](https://azure.microsoft.com/migration/resources/)consulte [soluções de migração em nuvem.](https://azure.microsoft.com/migration)

