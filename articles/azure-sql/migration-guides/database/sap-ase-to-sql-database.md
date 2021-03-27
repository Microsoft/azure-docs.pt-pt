---
title: 'BASE de Dados DE Azure SQL: Guia de migração'
description: Neste guia aprende a migrar as suas bases de dados SAP ASE para uma base de dados Azure SQL utilizando o SqL Server Migration Assistant for SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 138a23b610ab96194424bb0f88cf94f516c2d223
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626457"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Guia de migração: SAP ASE para Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Neste guia, aprende a migrar as bases de dados DO Servidor adaptador SAP Enterprise (ASE) para uma base de dados Azure SQL utilizando o SqL Server Migration Assistant for SAP Adapter Server Enterprise.

Para outros guias de migração, consulte [o Guia de Migração da Base de Dados Azure.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Pré-requisitos 

Antes de começar a migrar a sua base de dados SAP SE para a sua base de dados SQL, faça o seguinte:

- Verifique se o ambiente de origem está suportado. 
- Descarregue e instale [o SqL Server Migration Assistant para a SAP Adaptive Server Enterprise (anteriormente SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256).
- Certifique-se de que tem conectividade e permissões suficientes para aceder tanto à fonte como ao alvo.

## <a name="pre-migration"></a>Pré-migração

Depois de conhecer os pré-requisitos, está pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração.

### <a name="assess"></a>Avaliação

Ao utilizar [o SQL Server Migration Assistant (SSMA) para a SAP Adaptive Server Enterprise (formalmente SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256), pode rever objetos e dados de base de dados, avaliar bases de dados para migração, migrar objetos de base de dados Sybase para a sua base de dados SQL e, em seguida, migrar dados para a base de dados SQL. Para saber mais, consulte o [SqL Server Migration Assistant for Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Para criar uma avaliação, faça o seguinte: 

1. Abrir SSMA para Sybase. 
1. Selecione **'Ficheiro'** e, em seguida, selecione **Novo Projeto**. 
1. No painel new **project,** insira um nome e localização para o seu projeto e, em seguida, na lista **migrar para** baixo, selecione **Azure SQL Database**. 
1. Selecione **OK**.
1. No painel **'Ligar à base'** de ligação, insira os detalhes da ligação SAP. 
1. Clique com o botão direito na base de dados SAP que pretende migrar e, em seguida, **selecione Criar relatório**. Isto gera um relatório HTML. Em alternativa, pode selecionar o **separador 'Criar'** no canto superior direito.
1. Reveja o relatório HTML para compreender as estatísticas de conversão e quaisquer erros ou avisos. Também pode abrir o relatório no Excel para obter um inventário de objetos SAP ASE e o esforço necessário para realizar conversões de esquemas. A localização predefinição do relatório está na pasta do relatório dentro dos Projetos SSMA. Por exemplo:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Validar os mapeamentos de tipo

Antes de efetuar a conversão de esquemas, valide os mapeamentos predefinidos do tipo de dados ou altere-os com base nos requisitos. Pode fazê-lo selecionando  >  **Definições de Projeto de Ferramentas,** ou pode alterar o mapeamento de tipo para cada tabela selecionando a tabela no **Sap ASE Metadata Explorer**.

### <a name="convert-the-schema"></a>Converter o esquema

Para converter o esquema, faça o seguinte:

1. (Opcional) Para converter consultas dinâmicas ou especializadas, clique com o nó à direita e, em seguida, **selecione Adicionar a declaração**. 
1. Selecione o **separador 'Ligar ao Azure SQL Database'** e, em seguida, introduza os detalhes para a sua base de dados SQL. Pode optar por ligar-se a uma base de dados existente ou fornecer um novo nome, caso em que será criada uma base de dados no servidor alvo.
1. No painel **Sybase Metadata Explorer,** clique à direita no esquema SAP ASE com o qual está a trabalhar e, em seguida, selecione **Convert Schema**. 
1. Após a conversão do esquema, compare e reveja a estrutura convertida para a estrutura original, identifique potenciais problemas. 

   Após a conversão do esquema, pode guardar este projeto localmente para um exercício de remediação de esquemas offline. Para tal, selecione Projeto de Poupança **de**  >  **Ficheiros**. Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de publicar o esquema na sua base de dados SQL.

1. No painel **de saída,** selecione **os resultados da revisão** e reveja quaisquer erros no painel **da lista De erros.** 
1. Guarde o projeto localmente para um exercício de remediação de esquemas offline. Para tal, selecione Projeto de Poupança **de**  >  **Ficheiros**. Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de publicar o esquema na sua base de dados SQL.

## <a name="migrate-the-databases"></a>Migrar as bases de dados 

Depois de ter os pré-requisitos necessários e ter concluído as tarefas associadas à fase *de pré-migração,* está pronto para executar o esquema e a migração de dados.

Para publicar o esquema e migrar os dados, faça o seguinte: 

1. Publique o esquema. No painel **Azure SQL Database Metadata Explorer,** clique com o botão direito na base de dados e, em seguida, selecione **Synchronize com Base de Dados**. Esta ação publica o esquema SAP ASE na sua base de dados SQL.

1. Migrar os dados. No painel **SAP ASE Metadata Explorer,** clique com o botão direito na base de dados SAP ASE ou no objeto que pretende migrar e, em seguida, selecione **Dados migratórios**. Em alternativa, pode selecionar o **separador Dados Migratórios** no canto superior direito. 

   Para migrar dados para uma base de dados inteira, selecione a caixa de verificação ao lado do nome da base de dados. Para migrar dados de tabelas individuais, expandir a base de **dados,** expandir tabelas e, em seguida, selecionar a caixa de verificação ao lado da tabela. Para omitir dados de tabelas individuais, limpe a caixa de verificação. 
1. Após a conclusão da migração, consulte o **Relatório de Migração de Dados**. 
1. Validar a migração através da revisão dos dados e do esquema. Para tal, ligue-se à sua base de dados SQL utilizando [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

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


## <a name="next-steps"></a>Passos seguintes

- Para uma matriz de serviços e ferramentas de terceiros da Microsoft que estão disponíveis para o ajudar com vários cenários de migração de dados e dados e tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Base de Dados Azure SQL, consulte:
   - [Uma visão geral da Base de Dados SQL](../../database/sql-database-paas-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/)  

- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, consulte:
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Melhores práticas para custos e dimensionamento de cargas de trabalho para a migração para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso à aplicação, consulte o [Data Access Migration Toolkit (pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).
