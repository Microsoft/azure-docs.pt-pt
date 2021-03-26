---
title: 'Oracle para SQL Server em Azure VM: Guia de migração'
description: Este guia ensina-o a migrar os seus esquemas Oráculos para o SQL Server em VMs Azure utilizando o SqL Server Migration Assistant para o Oráculo.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 4173fe34c6966514ef4f150527cd712c9d95ab7e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561717"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Guia de migração: Oracle para SQL Server em Azure VM
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina-o a migrar os seus esquemas Oráculos para o SQL Server em Azure VM utilizando o SqL Server Migration Assistant para o Oráculo. 

Para outros guias de migração, consulte [a Migração da Base de Dados.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar o seu esquema Oráculo para o SQL Server em Azure VM, precisa:

- Para verificar se o ambiente de origem é suportado.
- Para baixar [o SqL Server Migration Assistant (SSMA) para o Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Um [VM do servidor SQL](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md)alvo.
- As [permissões necessárias para a SSMA para a Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) e para o [fornecedor.](/sql/ssma/oracle/connect-to-oracle-oracletosql)
- Conectividade e permissões suficientes para aceder tanto à fonte como ao alvo. 


## <a name="pre-migration"></a>Pré-migração

Enquanto se prepara para migrar para a nuvem, verifique se o seu ambiente de origem está suportado e que se dirigiu a quaisquer pré-requisitos. Isto ajudará a assegurar uma migração eficiente e bem sucedida.

Esta parte do processo envolve a realização de um inventário das bases de dados que precisa de migrar, avaliando essas bases de dados para potenciais problemas de migração ou bloqueadores, e, em seguida, resolver quaisquer itens que possa ter descoberto. 

### <a name="discover"></a>Detetar

Utilize o [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) para identificar as fontes de dados existentes e detalhes sobre as funcionalidades que estão a ser usadas pelo seu negócio para obter uma melhor compreensão e plano para a migração. Este processo envolve a digitalização da rede para identificar todas as instâncias Oráculos da sua organização, juntamente com a versão e funcionalidades em uso.

Para utilizar o kit de ferramentas MAP para efetuar uma verificação de inventário, siga estes passos: 

1. Abra o [conjunto de ferramentas MAP](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Selecione **Criar/Selecionar base de dados**:

   ![Selecionar base de dados](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Selecione **Criar uma base de dados de inventário,** insira um nome para a nova base de dados de inventário que está a criar, forneça uma breve descrição e, em seguida, selecione **OK**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Criar uma base de dados de inventário":::

1. Selecione **Recolher dados de inventário** para abrir o Assistente de Inventário e **Avaliação**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Recolher dados de inventário":::

1. No Assistente de **Inventário e Avaliação,** escolha **oráculo** e, em seguida, selecione **Seguinte**:

   ![Escolha oráculo](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Escolha a opção de pesquisa de computador que melhor se adapte às necessidades e ambiente do seu negócio e, em seguida, selecione **Seguinte**: 

   ![Escolha a opção de pesquisa de computador que melhor se adapte às necessidades do seu negócio](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Introduza credenciais ou crie novas credenciais para os sistemas que pretende explorar e, em seguida, selecione **Seguinte**:

    ![Introduzir credenciais](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)

1. Desacione a ordem das credenciais e, em seguida, selecione **Seguinte**:

   ![Definir ordem de credencial](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  

1. Especifique as credenciais para cada computador que pretende descobrir. Pode utilizar credenciais únicas para cada computador/máquina, ou pode optar por utilizar a lista **de Todas as Credenciais de Computador:**


   ![Especifique as credenciais para cada computador que pretende descobrir](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Verifique o resumo da sua seleção e, em seguida, **selecione Terminar**:

   ![Resumo da revisão](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)

1. Após a conclusão da varredura, consulte o relatório do resumo da Recolha de **Dados.** A varredura pode demorar alguns minutos, e depende do número de bases de dados. Selecione **Fechar** quando terminar:

   ![Relatório de resumo da coleção](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Selecione **Opções** para gerar um relatório sobre a Avaliação da Oracle e detalhes da base de dados. Selecione ambas as opções (uma por uma) para gerar o relatório.


### <a name="assess"></a>Avaliação

Depois de identificar as fontes de dados, utilize o [SqL Server Migration Assistant (SSMA) para](https://www.microsoft.com/en-us/download/details.aspx?id=54258) a Oracle para avaliar a(s) instância oracle(s) migrando para o SQL Server VM para que você entenda as lacunas entre os dois. Utilizando o assistente de migração, pode rever objetos e dados de base de dados, avaliar bases de dados para migração, migrar objetos de base de dados para o SQL Server e, em seguida, migrar dados para o SQL Server.

Para criar uma avaliação, siga estes passos: 

1. Abra o  [SqL Server Migration Assistant (SSMA) para o Oráculo](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selecione **File** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar o seu projeto e, em seguida, selecione um alvo de migração sql Server a partir do drop-down. Selecione **OK**:

   ![Novo projeto](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)

1. Selecione **Connect to Oracle**. Introduza os valores para os detalhes da ligação Oracle na caixa de diálogo **Connect to Oracle:**

   ![Ligar ao Oráculo](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Selecione os esquemas do Oráculo que pretende migrar: 

   ![Selecione esquema Oráculo](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)

1. Clique com o botão direito no esquema do Oráculo que pretende migrar no **Oracle Metadata Explorer** e, em seguida, escolha **Criar relatório**. Isto gerará um relatório HTML. Em alternativa, pode escolher **Criar relatório** a partir da barra de navegação depois de selecionar a base de dados:

   ![Criar Relatório](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. No **Oracle Metadata Explorer**, selecione o esquema Oráculo e, em seguida, selecione Criar **Relatório** para gerar um relatório HTML com estatísticas de conversão e erros/avisos, se houver.
1. Reveja o relatório HTML para compreender as estatísticas de conversão e quaisquer erros ou avisos. Também pode abrir o relatório no Excel para obter um inventário de objetos Oráculos e o esforço necessário para realizar conversões de esquemas. A localização predefinição do relatório está na pasta do relatório dentro dos Projetos SSMA. 

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`
    
   ![Relatório de Conversão](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)

### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos do tipo de dados predefinidos e altere-os com base nos requisitos, se necessário. Para tal, siga estes passos: 

1. Selecione **Ferramentas** do menu. 
1. Selecione **Definições de projetos**. 
1. Selecione o **separador de mapeamentos Tipo:**

   ![Mapeamentos de tipo](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Pode alterar o mapeamento do tipo para cada tabela selecionando a tabela no explorador de **metadados Oráculo**. 

### <a name="convert-schema"></a>Converter esquema

Para converter o esquema, siga estes passos: 

1. (Opcional) Para converter consultas dinâmicas ou ad-hoc, clique no nó à direita e escolha **Adicionar a declaração**.
1. Selecione **Connect to SQL Server** a partir da barra de navegação de primeira linha. 
     1. Introduza os detalhes da ligação para o seu SQL Server no Azure VM. 
     1. Escolha a base de dados-alvo a partir do drop-down ou forneça um novo nome, caso em que uma base de dados será criada no servidor alvo. 
     1. Forneça detalhes de autenticação. 
     1. Selecione **Ligar**. 

   ![Ligue-se ao SQL](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Clique com o botão direito no esquema do **Oráculo** no Oracle Metadata Explorer e escolha **Converter o Schema.** Em alternativa, pode **selecionar converter o esquema** da barra de navegação de primeira linha:

   ![Converter Schema](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)

1. Após a conversão concluída, compare e reveja os objetos convertidos aos objetos originais para identificar potenciais problemas e abordá-los com base nas recomendações:

   ![Recomendações de revisão](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Compare o texto Transact-SQL convertido com os procedimentos armazenados originais e reveja as recomendações: 

   ![Código de recomendações de revisão](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Pode guardar o projeto localmente para um exercício de remediação de esquemas offline. Pode **fazê-lo** selecionando Save Project a partir do menu **'Ficheiro'.** Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de poder publicar o esquema para o SQL Server.

1. Selecione **Os resultados** da Revisão no painel de saída e os erros de revisão no painel **da lista de erros.** 
1. Guarde o projeto localmente para um exercício de remediação de esquemas offline. Selecione **Save Project** a partir do menu **'Ficheiro'.** Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de poder publicar o esquema para o SQL Server no Azure VM.


## <a name="migrate"></a>Migrate

Depois de ter os pré-requisitos necessários e ter concluído as tarefas associadas à fase **de pré-migração,** está pronto para executar o esquema e a migração de dados. A migração envolve dois passos – publicar o esquema e migrar os dados. 


Para publicar o seu esquema e migrar os dados, siga estes passos: 

1. Publique o esquema: Clique à direita na base de dados do **SQL Server Metadata Explorer**  e escolha **Sincronizar com base de dados**. Esta ação publica o esquema da Oracle para o SQL Server no Azure VM:

   ![Sincronizar com base de dados](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Reveja o mapeamento entre o seu projeto de origem e o seu alvo:

   ![Rever o estado da sincronização](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)


1. Migrar os dados: Clique na base de dados ou no objeto que pretende migrar no **Oracle Metadata Explorer** e escolha **dados migratórios**. Em alternativa, pode **selecionar Dados Migratórios** a partir da barra de navegação de primeira linha. Para migrar dados para uma base de dados inteira, selecione a caixa de verificação ao lado do nome da base de dados. Para migrar dados de tabelas individuais, expandir a base de dados, expandir tabelas e, em seguida, selecionar a caixa de verificação ao lado da tabela. Para omitir dados de tabelas individuais, limpe a caixa de verificação:

   ![Dados migratórios](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Forneça detalhes de ligação para o Oracle e SQL Server no Azure VM na caixa de diálogo.
1. Após a conclusão da migração, consulte o **Relatório de Migração de Dados:**  

    ![Relatório de Migração de Dados](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Ligue ao seu SQL Server no exemplo Azure VM utilizando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração através da revisão dos dados e esquemas:

   ![Validar em SSMA](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)


Além de utilizar o SSMA, também pode utilizar os Serviços de Integração de Servidores SQL (SSIS) para migrar os dados. Para saber mais, veja: 
- O artigo [Começando com serviços de integração de servidores SQL](//sql/integration-services/sql-server-integration-services).
- Os serviços de [integração de servidores SQL de papel branco: SSIS for Azure and Hybrid Data Movement](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).



## <a name="post-migration"></a>Pós-migração 

Depois de ter concluído com sucesso a fase **de Migração,** precisa de passar por uma série de tarefas pós-migração para garantir que tudo está a funcionar da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização deste facto exigirá, em alguns casos, alterações nas aplicações.

O [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) é uma extensão para o Código do Estúdio Visual que permite analisar o seu código fonte Java e detetar chamadas e consultas de acesso a dados, proporcionando-lhe uma visão única do que precisa de ser endereçado para suportar a nova base de dados. Para saber mais, consulte a [aplicação Da Migração java do](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) blog Da Oracle. 

### <a name="perform-tests"></a>Realizar testes

A abordagem de ensaio para a migração da base de dados consiste na realização das seguintes atividades:

1. **Desenvolver testes de validação.** Para testar a migração da base de dados, precisa de utilizar consultas SQL. Tem de criar as consultas de validação para correr contra a fonte e as bases de dados-alvo. As suas consultas de validação devem abranger o âmbito que definiu.

2. **Configurar ambiente de teste.** O ambiente de teste deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.

3. **Executar testes de validação**. Executar os testes de validação contra a fonte e o alvo e, em seguida, analisar os resultados.

4. **Executar testes de desempenho**. Executar o teste de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.

### <a name="optimize"></a>Otimização

A fase pós-migração é crucial para conciliar quaisquer problemas de precisão de dados e verificar a completude, bem como abordar problemas de desempenho com a carga de trabalho.

> [!Note]
> Para mais detalhes sobre estas questões e medidas específicas para os mitigar, consulte o [Guia de Validação e Otimização pós-migração.](/sql/relational-databases/post-migration-validation-and-optimization-guide)


## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional para completar este cenário de migração, consulte os seguintes recursos, que foram desenvolvidos em apoio de um envolvimento de projetos de migração no mundo real.

| **Título/ligação**                                                                                                                                          | **Descrição**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda muito a acelerar as grandes avaliações imobiliárias, fornecendo e automatizada e uniforme processo de decisão da plataforma-alvo.                                                          |
| [Artefactos de script de inventário de oráculo](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Este ativo inclui uma consulta PL/SQL que atinge as tabelas do sistema Oracle e fornece uma contagem de objetos por tipo de esquema, tipo de objeto e estado. Também fornece uma estimativa aproximada de 'Dados Brutos' em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados num formato CSV.                                                                                                               |
| [Automatizar a coleção de avaliação da SSMA Oracle & Consolidação](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Este conjunto de recursos utiliza um ficheiro .csv como entrada (sources.csv nas pastas do projeto) para produzir os ficheiros xml necessários para executar a avaliação SSMA no modo consola. O source.csv é fornecido pelo cliente com base num inventário dos casos existentes da Oracle. Os ficheiros de saída são AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [SSMA para os erros comuns da Oracle e como corrigi-los](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Com a Oracle, pode atribuir uma condição não-escalar na cláusula WHERE. No entanto, o SQL Server não suporta este tipo de condição. Como resultado, o SQL Server Migration Assistant (SSMA) para a Oracle não converte consultas com uma condição não-escalar na cláusula WHERE, em vez de gerar um erro O2SS001. Este livro branco fornece mais detalhes sobre a questão e formas de o resolver.          |
| [Manual de migração do Oráculo para o SqL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Este documento centra-se nas tarefas associadas à migração de um esquema Oráculo para a versão mais recente do SQL Server. Se a migração exigir alterações nas funcionalidades/funcionalidades, então o possível impacto de cada alteração nas aplicações que utilizam a base de dados deve ser cuidadosamente considerado.                                                     |

Estes recursos foram desenvolvidos como parte do Programa Ninja Data SQL, que é patrocinado pela equipa de engenharia do Azure Data Group. A carta principal do programa Data SQL Ninja é desbloquear e acelerar a modernização complexa e competir oportunidades de migração da plataforma de dados para a plataforma de dados Azure Data da Microsoft. Se acha que a sua organização estaria interessada em participar no programa Data SQL Ninja, contacte a sua equipa de conta e peça-lhes que apresentem uma nomeação.

## <a name="next-steps"></a>Passos seguintes

- Para verificar a disponibilidade de serviços aplicáveis ao SQL Server consulte o centro de [infraestruturas Azure Global](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de migração de dados e dados, bem como tarefas especiais, consulte o serviço de [artigos e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre o Azure SQL consulte:
   - [Opções de implementação](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server nas VMs do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para obter informações sobre licenciamento, consulte
   - [Traga a sua própria licença com o Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Obtenha suporte alargado gratuito para SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).