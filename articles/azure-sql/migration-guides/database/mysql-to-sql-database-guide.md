---
title: 'MySQL a Azure SQL Base de Dados: Guia de migração'
description: Este guia ensina-o a migrar as suas bases de dados MySQL para Azure SQL Database utilizando o SqL Server Migration Assistant for MySQL (SSMA for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: ff7b2115b396bf42cdeffa9c58bffb1802e980d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721890"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Guia de migração: MySQL para Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina-o a migrar a sua base de dados MySQL para Azure SQL Database utilizando o SqL Server Migration Assistant for MySQL (SSMA for MySQL). 

Para outros guias de migração, consulte [a Migração da Base de Dados.](https://datamigration.microsoft.com/) 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar a sua base de dados MySQL para a Base de Dados Azure SQL, precisa de:

- para verificar se o seu ambiente de origem é suportado. Atualmente, o MySQL 5.6 e 5.7 são suportados. 
- [Assistente de migração de servidores SQL para o MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257)


## <a name="pre-migration"></a>Pré-migração 

Depois de conhecer os pré-requisitos, está pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração.

### <a name="assess"></a>Avaliação 

Ao utilizar [o SqL Server Migration Assistant para o MySQL,](https://www.microsoft.com/download/confirmation.aspx?id=54257)pode rever objetos e dados de base de dados e avaliar bases de dados para migração.

Para criar uma avaliação, execute os seguintes passos.

1. Abra o Assistente de Migração do Servidor SQL para o MySQL. 
1. Selecione **File** no menu e, em seguida, escolha **Novo Projeto.** Forneça o nome do projeto, um local para salvar o seu projeto. Escolha **a Base de Dados Azure SQL** como o alvo da migração. 

   ![Novo Projeto](./media/mysql-to-sql-database-guide/new-project.png)

1. Escolha **Ligar ao MySQL** e fornecer detalhes de conexão para ligar o seu servidor MySQL. 

   ![Ligue-se ao MySQL](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Clique com o botão direito no esquema MySQL no **MySQL Metadata Explorer** e escolha **Criar relatório**. Em alternativa, pode selecionar **Criar relatório** a partir da barra de navegação de primeira linha. 

   ![Criar Relatório](./media/mysql-to-sql-database-guide/create-report.png)

1. Reveja o relatório HTML para estatísticas de conversão, bem como erros e avisos. Analise-o para compreender questões e resoluções de conversão. 

   Este relatório também pode ser acedido a partir da pasta de projetos SSMA, tal como selecionado no primeiro ecrã. Do exemplo acima, localize o ficheiro report.xml a partir de:
 
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   e abri-lo no Excel para obter um inventário de objetos MySQL e o esforço necessário para realizar conversões de esquemas.

    ![Relatório de Conversão](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos do tipo de dados predefinidos e altere-os com base nos requisitos, se necessário. Para tal, siga estes passos: 

1. Selecione **Ferramentas** do menu. 
1. Selecione **Definições de projetos**. 
1. Selecione o **separador de mapeamentos Tipo.** 

   ![Mapeamentos de tipo](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Pode alterar o mapeamento do tipo para cada tabela selecionando a tabela no **explorador de metadados MySQL**. 

### <a name="convert-schema"></a>Converter esquema 

Para converter o esquema, siga estes passos: 

1. (Opcional) Para converter consultas dinâmicas ou ad-hoc, clique no nó à direita e escolha **Adicionar a declaração**. 
1. Escolha **Ligar à Base de Dados Azure SQL** a partir da barra de navegação de primeira linha e fornecer detalhes de conexão. Pode optar por ligar-se a uma base de dados existente ou fornecer um novo nome, caso em que será criada uma base de dados no servidor alvo.

   ![Ligue-se ao SQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Clique com o botão direito no esquema e escolha **Converter o esquema**. 

   ![Converter Schema](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Depois de ter terminado a conversão do esquema, compare o código convertido com o código original para identificar potenciais problemas. 

   Compare objetos convertidos com objetos originais: 

   ![ Comparar e rever objeto ](./media/mysql-to-sql-database-guide/table-comparison.png)

   Compare os procedimentos convertidos com os procedimentos originais: 

   ![Compare e reveja o código do objeto](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate"></a>Migrate 

Depois de ter concluído a avaliação das suas bases de dados e de abordar eventuais discrepâncias, o próximo passo é executar o processo de migração. A migração envolve dois passos – publicar o esquema e migrar os dados. 

Para publicar o esquema e migrar os dados, siga estes passos: 

1. Clique com o botão direito na base de dados a partir do **Azure SQL Database Metadata Explorer** e escolha **Sincronizar com Base de Dados**. Esta ação publica o esquema MySQL para a Base de Dados Azure SQL.

   ![Sincronizar com base de dados](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Reveja o mapeamento entre o seu projeto de origem e o seu alvo:

   ![Sincronizar com a Revisão da Base de Dados](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Clique com o botão direito no esquema MySQL do **MySQL Metadata Explorer** e escolha **Dados Migratórios**. Em alternativa, pode **selecionar Dados Migratórios** a partir da navegação de primeira linha. 

   ![Migrar dados](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Após a conclusão da migração, consulte o relatório **sobre migração de dados:** 

   ![Relatório de Migração de Dados](./media/mysql-to-sql-database-guide/data-migration-report.png)

1.  Validar a migração através da revisão dos dados e esquemas na Base de Dados Azure SQL utilizando o SQL Server Management Studio (SSMS).

    ![Validar em SSMA](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>Pós-migração 

Depois de ter concluído com sucesso a fase **de Migração,** precisa de passar por uma série de tarefas pós-migração para garantir que tudo está a funcionar da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização deste facto exigirá, em alguns casos, alterações nas aplicações.

### <a name="perform-tests"></a>Realizar testes

A abordagem de ensaio para a migração da base de dados consiste na realização das seguintes atividades:

1. **Desenvolver testes de validação.** Para testar a migração da base de dados, precisa de utilizar consultas SQL. Tem de criar as consultas de validação para correr contra a fonte e as bases de dados-alvo. As suas consultas de validação devem abranger o âmbito que definiu.

2. **Configurar ambiente de teste.** O ambiente de teste deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.

3. **Executar testes de validação**. Executar os testes de validação contra a fonte e o alvo e, em seguida, analisar os resultados.

4. **Executar testes de desempenho**. Executar o teste de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.

### <a name="optimize"></a>Otimização

A fase pós-migração é crucial para conciliar quaisquer problemas de precisão de dados e verificar a completude, bem como abordar problemas de desempenho com a carga de trabalho.

Para mais detalhes sobre estas questões e medidas específicas para os mitigar, consulte o [Guia de Validação e Otimização pós-migração.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Ativos de migração

Para obter assistência adicional para completar este cenário de migração, consulte os seguintes recursos, que foram desenvolvidos em apoio de um envolvimento de projetos de migração no mundo real.

| Título/ligação     | Description    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda muito a acelerar as grandes avaliações imobiliárias, fornecendo e automatizada e uniforme processo de decisão da plataforma-alvo. |

Estes recursos foram desenvolvidos como parte do Programa Ninja Data SQL, que é patrocinado pela equipa de engenharia do Azure Data Group. A carta principal do programa Data SQL Ninja é desbloquear e acelerar a modernização complexa e competir oportunidades de migração da plataforma de dados para a plataforma de dados Azure Data da Microsoft. Se acha que a sua organização estaria interessada em participar no programa Data SQL Ninja, contacte a sua equipa de conta e peça-lhes que apresentem uma nomeação.

## <a name="next-steps"></a>Passos seguintes 

- Certifique-se de verificar a [Calculadora Azure Total Cost of Ownership (TCO)](https://aka.ms/azure-tco) para ajudar a estimar as economias de custos que pode realizar migrando as suas cargas de trabalho para Azure.

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de migração de dados e dados, bem como tarefas especiais, consulte o serviço de [artigos e ferramentas para a migração de dados.](https://docs.microsoft.com/azure/dms/dms-tools-matrix)

- Para outros guias de migração, consulte [a Migração da Base de Dados.](https://datamigration.microsoft.com/) 

Para vídeos, consulte: 
- [Panorâmica da viagem de migração e dos instrumentos/serviços recomendados para a avaliação e migração](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
