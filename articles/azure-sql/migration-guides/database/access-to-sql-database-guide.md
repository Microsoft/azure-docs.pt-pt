---
title: 'Acesso à Base de Dados Azure SQL: Guia de migração'
description: Neste guia, aprende a migrar as bases de dados do Microsoft Access para uma base de dados Azure SQL utilizando o SqL Server Migration Assistant for Access (SSMA for Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: f9fa2426e371ab9fd99e88979cbcbbb34adb00d6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643586"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guia de migração: Acesso à Base de Dados Azure SQL

Neste guia, aprende a migrar a sua base de dados do Microsoft Access para uma base de dados Azure SQL utilizando o SqL Server Migration Assistant for Access (SSMA for Access).

Para outros guias de migração, consulte [o Guia de Migração da Base de Dados Azure.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a migrar a sua base de dados de Acesso para uma base de dados SQL, faça o seguinte:

- Verifique se o ambiente de origem está suportado. 
- Descarregue e instale [o SqL Server Migration Assistant para acesso](https://www.microsoft.com/download/details.aspx?id=54255).
- Certifique-se de que tem conectividade e permissões suficientes para aceder tanto à fonte como ao alvo.

## <a name="pre-migration"></a>Pré-migração

Depois de conhecer os pré-requisitos, está pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração.


### <a name="assess"></a>Avaliação 

Utilize o SSMA para aceder a rever objetos e dados de base de dados e avaliar bases de dados para migração. 

Para criar uma avaliação, faça o seguinte: 

1. Abrir [SSMA para acesso](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Selecione **'Ficheiro'** e, em seguida, selecione **Novo Projeto**. 
1. Forneça um nome de projeto e uma localização para o seu projeto e, em seguida, na lista de drop-down, selecione **Azure SQL Database** como o alvo da migração. 
1. Selecione **OK**. 

   ![Screenshot do painel "New Project" para introduzir o nome e localização do seu projeto de migração.](./media/access-to-sql-database-guide/new-project.png)

1. Selecione **Adicionar Bases de Dados** e, em seguida, selecione as bases de dados a adicionar ao seu novo projeto. 

   ![Screenshot do separador "Adicionar bases de dados" em SSMA para acesso.](./media/access-to-sql-database-guide/add-databases.png)

1. No painel **Access Metadata Explorer,** clique com o botão direito numa base de dados e, em seguida, selecione **'Criar Relatório'.** Em alternativa, pode selecionar o separador **'Criar Relatório'** no canto superior direito.

   ![Screenshot do comando "Criar Relatório" no Access Metadata Explorer.](./media/access-to-sql-database-guide/create-report.png)

1. Reveja o relatório HTML para compreender as estatísticas de conversão e quaisquer erros ou avisos. Também pode abrir o relatório no Excel para obter um inventário de objetos access e entender o esforço necessário para realizar conversões de esquemas. A localização predefinição do relatório está na pasta do relatório dentro dos Projetos SSMA. Por exemplo:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Screenshot de uma avaliação do relatório de base de dados de exemplo em SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Validar os tipos de dados

Valide os mapeamentos predefinidos do tipo de dados e altere-os com base nos seus requisitos, se necessário. Para tal:

1. No SSMA para acesso, selecione **Ferramentas** e, em seguida, selecione **Definições de Projeto**. 
1. Selecione o **separador 'Tipo de Mapeamento'.** 

   ![Screenshot do painel "Type Mapping" em SSMA para acesso.](./media/access-to-sql-database-guide/type-mappings.png)

1. Pode alterar o mapeamento de tipo para cada tabela selecionando o nome da tabela no painel **Access Metadata Explorer.**


### <a name="convert-the-schema"></a>Converter o esquema

Para converter objetos de base de dados, faça o seguinte: 

1. Selecione o **separador 'Ligar ao Azure SQL Database'** e, em seguida, faça o seguinte:

   a. Introduza os detalhes para a ligação à sua base de dados SQL.  
   b. Na lista de drop-down, selecione a base de dados SQL alvo. Ou pode introduzir um novo nome, caso em que uma base de dados será criada no servidor alvo.  
   c. Forneça detalhes de autenticação.   
   d. Selecione **Ligar**.

   ![Screenshot do painel "Connect to Azure SQL Database" para introduzir detalhes de ligação.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. No painel **Access Metadata Explorer,** clique à direita na base de dados e, em seguida, selecione **Converte o Esquema**. Em alternativa, pode selecionar a sua base de dados e, em seguida, selecionar o **separador 'Converter Esquema'.**

   ![Screenshot do comando "Convert Schema" no painel "Access Metadata Explorer".](./media/access-to-sql-database-guide/convert-schema.png)

1. Após a conversão concluída, compare os objetos convertidos com os objetos originais para identificar potenciais problemas e resolva os problemas com base nas recomendações.

   ![Screenshot mostrando uma comparação dos objetos convertidos com os objetos de origem.](./media/access-to-sql-database-guide/table-comparison.png)

    Compare o texto Transact-SQL convertido com o código original e reveja as recomendações.

   ![Screenshot mostrando uma comparação de consultas convertidas com o código fonte.](./media/access-to-sql-database-guide/query-comparison.png) 

1. (Opcional) Para converter um objeto individual, clique com o botão direito do objeto e, em seguida, selecione **Convert Schema**. Os objetos convertidos aparecem em texto arrojado no **Access Metadata Explorer:** 

   ![Screenshot mostrando que os objetos no Access Metadata Explorer são convertidos.](./media/access-to-sql-database-guide/converted-items.png)
 
1. No painel **de saída,** selecione o ícone **de resultados de Revisão** e reveja os erros no painel **da lista de erros.** 
1. Guarde o projeto localmente para um exercício de remediação de esquemas offline. Para tal, selecione Projeto de Poupança **de**  >  **Ficheiros**. Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de publicá-los na sua base de dados SQL.

## <a name="migrate-the-databases"></a>Migrar as bases de dados

Depois de ter avaliado as suas bases de dados e abordado quaisquer discrepâncias, pode executar o processo de migração. Os dados migratórios são uma operação de carga em massa que move linhas de dados para uma base de dados Azure SQL em transações. O número de linhas a carregar na sua base de dados SQL em cada transação está configurado nas definições do projeto.

Para publicar o seu esquema e migrar os dados utilizando o SSMA para acesso, faça o seguinte: 

1. Se ainda não o fez, selecione **Connect to Azure SQL Database** e forneça detalhes de ligação. 

1. Publique o esquema. No painel **Azure SQL Database Metadata Explorer,** clique com o botão direito na base de dados com que está a trabalhar e, em seguida, selecione **Synchronize com Base de Dados**. Esta ação publica o esquema MySQL na base de dados SQL.

1. No **Synchronize com o** painel de bases de dados, reveja o mapeamento entre o seu projeto de origem e o seu alvo:

   ![Screenshot do painel "Sincronizar com a Base de Dados" para rever a sincronização com a base de dados.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. No painel **Access Metadata Explorer,** selecione as caixas de verificação ao lado dos itens que pretende migrar. Para migrar toda a base de dados, selecione a caixa de verificação ao lado da base de dados. 

1. Migrar os dados. Clique com o botão direito na base de dados ou no objeto que pretende migrar e, em seguida, **selecione Dados migratórios**. Em alternativa, pode selecionar o **separador Dados Migratórios** no canto superior direito.  

   Para migrar dados para uma base de dados inteira, selecione a caixa de verificação ao lado do nome da base de dados. Para migrar dados de tabelas individuais, expandir a base de **dados,** expandir tabelas e, em seguida, selecionar a caixa de verificação ao lado da tabela. Para omitir dados de tabelas individuais, limpe a caixa de verificação.

    ![Screenshot do comando "Dados migratórios" no painel "Access Metadata Explorer".](./media/access-to-sql-database-guide/migrate-data.png)

1. Após a conclusão da migração, consulte o **Relatório de Migração de Dados**.  

    ![Screenshot do painel "Migrar dados" mostrando um relatório de exemplo para revisão.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Conecte-se à sua base de dados Azure SQL utilizando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)e valide a migração através da revisão dos dados e esquemas.

   ![Screenshot do SQL Server Management Studio Object Explorer para validar a sua migração em SSMA.](./media/access-to-sql-database-guide/validate-data.png)

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

- Para uma matriz de serviços e ferramentas de terceiros da Microsoft que estão disponíveis para o ajudar com vários cenários de migração de dados e dados e tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Base de Dados Azure SQL consulte:
   - [Uma visão geral da Base de Dados SQL](../../database/sql-database-paas-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, consulte:
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Melhores práticas para custos e dimensionamento de cargas de trabalho para a migração para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso à aplicação, consulte o [Data Access Migration Toolkit (pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Para obter informações sobre como realizar testes da camada A/B do acesso a dados, consulte [a visão geral do Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).
