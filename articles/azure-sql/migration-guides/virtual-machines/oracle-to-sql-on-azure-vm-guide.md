---
title: 'Oracle para SQL Server em Azure Virtual Machines: Guia de migração'
description: Este guia ensina-o a migrar os seus esquemas Oráculos para o SQL Server em Azure Virtual Machines utilizando o SqL Server Migration Assistant para o Oráculo.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: d4fb33e8e904d12e242f7eeaf9c2dc50a02eff4d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961256"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Guia de migração: Oracle para SQL Server em Azure Virtual Machines
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina-o a migrar os seus esquemas Oráculos para o SQL Server em Azure Virtual Machines utilizando o SqL Server Migration Assistant para o Oráculo. 

Para outros guias de migração, consulte [a Migração da Base de Dados.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar o seu esquema Oráculo para o SQL Server em Azure Virtual Machines, precisa de:

- Um ambiente de origem apoiado.
- [Sql Server Migration Assistant (SSMA) para o Oráculo](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Um [VM do servidor SQL](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md)alvo.
- As [permissões necessárias para a SSMA para](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) a Oracle e o [fornecedor.](/sql/ssma/oracle/connect-to-oracle-oracletosql)
- Conectividade e permissões suficientes para aceder à fonte e ao alvo. 


## <a name="pre-migration"></a>Pré-migração

Para se preparar para migrar para a nuvem, verifique se o seu ambiente de origem está suportado e que se dirigiu a quaisquer pré-requisitos. Ao fazê-lo, ajudará a assegurar uma migração eficiente e bem sucedida.

Esta parte do processo envolve: 
- A fazer um inventário das bases de dados que precisas de migrar.
- Avaliando essas bases de dados para potenciais problemas de migração ou bloqueadores. 
- A resolver os problemas que descobrires. 

### <a name="discover"></a>Detetar

Utilize [o MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) para identificar as fontes de dados existentes e detalhes sobre as funcionalidades que o seu negócio está a usar. Ao fazê-lo, irá dar-lhe uma melhor compreensão da migração e ajudá-lo-á a planeá-la. Este processo envolve digitalizar a rede para identificar as instâncias Oráculos da sua organização e as versões e funcionalidades que está a usar.

Para utilizar o MAP Toolkit para fazer uma verificação de inventário, siga estes passos: 


1. Abra [o kit de ferramentas DO MAPA](https://go.microsoft.com/fwlink/?LinkID=316883).


1. Selecione **Criar/Selecionar base de dados**:

   ![Screenshot que mostra a opção criar/selecionar base de dados.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Selecione **Criar uma base de dados de inventário.** Insira um nome para a nova base de dados de inventário que está a criar, forneça uma breve descrição e, em seguida, selecione **OK**: 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Screenshot que mostra a interface para criar uma base de dados de inventário.":::

1. Selecione **Recolher dados de inventário** para abrir o Assistente de Inventário e **Avaliação**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Screenshot que mostra a ligação de dados de inventário Recolher.":::


1. No Assistente de **Inventário e Avaliação,** selecione **oráculo** e, em seguida, selecione **Seguinte**:

   ![Screenshot que mostra a página de Cenários de Inventário do Assistente de Inventário e Avaliação.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Selecione a opção de pesquisa de computador que melhor se adapte às necessidades e ambiente do seu negócio e, em seguida, selecione **Seguinte**: 

   ![Screenshot que mostra a página Discovery Methods do Assistente de Inventário e Avaliação.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Introduza credenciais ou crie novas credenciais para os sistemas que pretende explorar e, em seguida, selecione **Seguinte**:

    ![Screenshot que mostra a página de todas as credenciais de todos os computadores do Assistente de Inventário e Avaliação.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Desacione a ordem das credenciais e, em seguida, selecione **Seguinte**: 

   ![Screenshot que mostra a página de Pedido de Credenciais do Assistente de Inventário e Avaliação.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Introduza as credenciais para cada computador que pretende descobrir. Pode utilizar credenciais únicas para cada computador/máquina, ou pode utilizar a lista de credenciais de Todos os Computadores.  

   ![Screenshot que mostra a página de Espec de Computadores e Credenciais do Assistente de Inventário e Avaliação.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Verifique as suas seleções e, em seguida, **selecione Terminar**:

   ![Screenshot que mostra a página resumo do Assistente de Inventário e Avaliação.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. Depois de terminar a verificação, consulte o resumo da **Recolha de Dados.** A tomografia pode demorar alguns minutos, dependendo do número de bases de dados. **Selecione Fechar** quando terminar: 

   ![Screenshot que mostra o resumo da recolha de dados.](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Selecione **Opções** para gerar um relatório sobre a avaliação da Oracle e detalhes da base de dados. Selecione ambas as opções, uma de cada vez, para gerar o relatório.


### <a name="assess"></a>Avaliação

Depois de identificar as fontes de dados, utilize o [SqL Server Migration Assistant para](https://www.microsoft.com/en-us/download/details.aspx?id=54258) a Oracle para avaliar as instâncias Oráculos que migram para o SQL Server VM. O assistente irá ajudá-lo a entender as lacunas entre as bases de dados de origem e destino. Pode rever objetos e dados de base de dados, avaliar bases de dados para migração, migrar objetos de base de dados para o SQL Server e, em seguida, migrar dados para o SQL Server.

Para criar uma avaliação, siga estes passos: 


1. Abrir [o assistente de migração do servidor SQL para o oráculo](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. No menu **'Ficheiro',** selecione **Novo Projeto.** 
1. Forneça um nome de projeto e uma localização para o seu projeto e, em seguida, selecione um alvo de migração sql Server da lista. Selecione **OK**: 

   ![Screenshot que mostra a caixa de diálogo do Novo Projeto.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. Selecione **Connect to Oracle**. Introduza os valores da ligação Oráculo na caixa de diálogo **Connect to Oracle:**

   ![Screenshot que mostra a caixa de diálogo Connect to Oracle.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Selecione os esquemas do Oráculo que pretende migrar: 

   ![Screenshot que mostra a lista de esquemas da Oráculo que podem ser migrados.](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. No **Oracle Metadata Explorer,** clique com o botão direito no esquema do Oráculo que pretende migrar e, em seguida, selecione **Criar Relatório**. Ao fazê-lo, gerará um relatório HTML. Em alternativa, pode selecionar a base de dados e, em seguida, selecionar **Criar relatório** no menu superior.

   ![Screenshot que mostra como criar um relatório.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Reveja o relatório HTML para estatísticas de conversão, erros e avisos. Analise-o para compreender os problemas de conversão e as resoluções.

    Também pode abrir o relatório no Excel para obter um inventário de objetos Oráculos e o esforço necessário para concluir conversões de esquemas. A localização predefinição do relatório é a pasta de relatório nos Projetos SSMA. 

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Screenshot que mostra um relatório de conversão.](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos do tipo de dados predefinidos e altere-os com base em requisitos, se necessário. Para tal, siga estes passos: 


1. No menu **Ferramentas,** selecione **Definições de Projeto**. 
1. Selecione o **separador 'Mapeamentos'** tipo. 

   ![Screenshot que mostra o separador 'Mapeamentos' tipo.](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Pode alterar o mapeamento do tipo para cada tabela selecionando a tabela no **Oracle Metadata Explorer**. 

### <a name="convert-the-schema"></a>Converter o esquema

Para converter o esquema, siga estes passos: 

1. (Opcional) Para converter consultas dinâmicas ou ad-hoc, clique no nó e selecione **Adicionar a declaração**.

1. Selecione **Connect to SQL Server** no menu superior. 
     1. Introduza os detalhes da ligação para o seu SQL Server no Azure VM. 
     1. Selecione a sua base de dados-alvo da lista ou forneça um novo nome. Se fornecer um novo nome, será criada uma base de dados no servidor alvo. 
     1. Forneça detalhes de autenticação. 
     1. Selecione **Ligar**. 


   ![Screenshot que mostra como ligar ao SQL Server.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Clique com o botão direito no esquema do Oráculo no **Oracle Metadata Explorer** e selecione **Convert Schema**. Em alternativa, pode **selecionar converter o esquema** no menu superior:

   ![Screenshot que mostra como converter o esquema.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. Após a conversão do esquema estar concluída, reveja os objetos convertidos e compare-os com os objetos originais para identificar potenciais problemas. Utilize as recomendações para resolver quaisquer problemas:

   ![Screenshot que mostra uma comparação de dois esquemas.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Compare o texto Transact-SQL convertido com os procedimentos armazenados originais e reveja as recomendações: 

   ![Screenshot que mostra Transact-SQL, procedimentos armazenados, e um aviso.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Pode guardar o projeto localmente para um exercício de remediação de esquemas offline. Para tal, **selecione Save Project** no menu **'Ficheiro'.** A poupança do projeto localmente permite-lhe avaliar os esquemas de origem e alvo offline e executar a remediação antes de publicar o esquema para o SQL Server.

1. Selecione **Os resultados** da Revisão no painel **de saída** e, em seguida, reveja erros no painel de lista **de erros.** 
1. Guarde o projeto localmente para um exercício de remediação de esquemas offline. **Selecione Save Project** no menu **'Ficheiro'.** Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de publicar o esquema para SQL Server em Azure Virtual Machines.


## <a name="migrate"></a>Migrate

Depois de ter os pré-requisitos necessários e ter concluído as tarefas associadas à fase de pré-migração, está pronto para iniciar o esquema e a migração de dados. A migração envolve dois passos: a publicação do esquema e a migração dos dados. 


Para publicar o seu esquema e migrar os dados, siga estes passos: 

1. Publique o esquema: clique à direita na base de dados no **SQL Server Metadata Explorer** e selecione **Synchronize com Base de Dados**. Ao fazê-lo, publica o esquema do Oracle para o SQL Server em Azure Virtual Machines. 

   ![Screenshot que mostra o Synchronize com o comando Base de Dados.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Reveja o mapeamento entre o seu projeto de origem e o seu alvo:

   ![Screenshot que mostra o estado de sincronização.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. Migrar os dados: clique à direita na base de dados ou objeto que pretende migrar no **Oracle Metadata Explorer** e selecione **Dados migratórios**. Em alternativa, pode **selecionar Dados Migratórios** no menu superior.

   Para migrar dados para uma base de dados inteira, selecione a caixa de verificação ao lado do nome da base de dados. Para migrar dados de tabelas individuais, expandir a base de **dados,** expandir tabelas e, em seguida, selecionar a caixa de verificação ao lado da tabela. Para omitir dados de tabelas individuais, limpe as caixas de verificação.

   ![Screenshot que mostra o comando Dados Migratórios.](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Forneça detalhes de conexão para o Oracle e SQL Server em Azure Virtual Machines na caixa de diálogo.
1. Após o fim da migração, consulte o **Relatório de Migração de Dados:**

    ![Screenshot que mostra o Relatório de Migração de Dados.](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Conecte-se ao seu servidor SQL na instância Azure Virtual Machines utilizando [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Validar a migração através da revisão dos dados e esquemas:


   ![Screenshot que mostra uma instância do SQL Server em SSMA.](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

Em vez de utilizar o SSMA, poderá utilizar os Serviços de Integração de Servidores SQL (SSIS) para migrar os dados. Para saber mais, veja: 
- O artigo [SqL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- O livro branco [SSIS for Azure and Hybrid Data Movement](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).


## <a name="post-migration"></a>Pós-migração 

Depois de completar a fase de migração, precisa de completar uma série de tarefas pós-migração para garantir que tudo está a correr da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização dessas alterações pode requerer alterações nas aplicações.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) é uma extensão para Código de Estúdio Visual. Permite-lhe analisar o seu código fonte Java e detetar chamadas e consultas de acesso a dados. O conjunto de ferramentas fornece uma visão única do que precisa de ser endereçado para suportar a nova base de dados. Para saber mais, consulte [a aplicação De Java da Oracle.](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) 

### <a name="perform-tests"></a>Realizar testes

Para testar a migração da sua base de dados, complete estas atividades:

1. **Desenvolver testes de validação.** Para testar a migração da base de dados, precisa de utilizar consultas SQL. Crie as consultas de validação para correr contra as bases de dados de origem e alvo. As suas consultas de validação devem cobrir o âmbito que definiu.

2. **Crie um ambiente de teste.** O ambiente de teste deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.

3. **Executar testes de validação**. Executar os testes de validação contra a fonte e o alvo e, em seguida, analisar os resultados.

4. **Executar testes de desempenho**. Executar o teste de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.

### <a name="optimize"></a>Otimização

A fase pós-migração é crucial para conciliar quaisquer problemas de precisão dos dados e verificar a completude. Também é fundamental para abordar problemas de desempenho com a carga de trabalho.

> [!Note]
> Para obter mais informações sobre estes problemas e medidas específicas para os mitigar, consulte o [guia de validação e otimização pós-migração.](/sql/relational-databases/post-migration-validation-and-optimization-guide)


## <a name="migration-resources"></a>Recursos de migração 

Para obter mais ajuda para completar este cenário de migração, consulte os seguintes recursos, que foram desenvolvidos para apoiar um projeto de migração no mundo real.

| **Título/Ligação**                                                                                                                                          | **Descrição**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Esta ferramenta fornece plataformas-alvo mais adequadas, prontidão na nuvem e níveis de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um simples cálculo de um clique e geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo um processo de decisão automatizado e uniforme da plataforma-alvo.                                                          |
| [Artefactos de script de inventário de oráculo](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Este ativo inclui uma consulta PL/SQL que visa as tabelas do sistema Oracle e fornece uma contagem de objetos por tipo de esquema, tipo de objeto e estado. Também fornece uma estimativa aproximada de dados brutos em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados em formato CSV.                                                                                                               |
| [Automatizar a coleção de avaliação da SSMA Oracle & Consolidação](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Este conjunto de recursos utiliza um ficheiro .csv como entrada (sources.csv nas pastas do projeto) para produzir os ficheiros XML que precisa de executar uma avaliação SSMA no modo consola. Você fornece o arquivo source.csv fazendo um inventário das instâncias oráculos existentes. Os ficheiros de saída são AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [Problemas de SSMA e possíveis soluções ao migrar bases de dados da Oracle](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Com a Oracle, pode atribuir uma condição não-escalar numa cláusula WHERE. O SQL Server não suporta este tipo de condição. Assim, o SSMA para a Oracle não converte consultas que tenham uma condição não-escalar na cláusula WHERE. Em vez disso, gera um erro: O2SS0001. Este livro branco fornece pormenores sobre o problema e as formas de o resolver.          |
| [Manual de migração do Oráculo para o SqL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Este documento centra-se nas tarefas associadas à migração de um esquema Oráculo para a versão mais recente do SQL Server. Se a migração necessitar de alterações nas funcionalidades/funcionalidades, é necessário ponderar cuidadosamente o possível efeito de cada alteração nas aplicações que utilizam a base de dados.                                                     |


A equipa de Engenharia Data SQL desenvolveu estes recursos. A principal carta desta equipa é desbloquear e acelerar a modernização complexa para projetos de migração de plataformas de dados para a plataforma de dados Microsoft Azure.


## <a name="next-steps"></a>Passos seguintes

- Para verificar a disponibilidade de serviços aplicáveis ao SQL Server, consulte o [centro de infraestruturas Azure Global.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de migração de dados e dados e tarefas especializadas, consulte [Serviços e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre o Azure SQL, consulte:
   - [Opções de implementação](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server nas Máquinas Virtuais do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/)


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, consulte:
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para o custo e o tamanho das cargas de trabalho migraram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para obter informações sobre o licenciamento, consulte:
   - [Traga a sua própria licença com o Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Obtenha suporte alargado gratuito para SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Para avaliar a camada de acesso à aplicação, utilize o [Kit de Ferramentas de Migração do Acesso a Dados](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Para obter mais informações sobre como fazer testes da camada A/B de acesso a dados, consulte [a visão geral do Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).


