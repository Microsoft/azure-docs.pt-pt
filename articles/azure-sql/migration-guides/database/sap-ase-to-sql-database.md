---
title: 'BASE de Dados DE Azure SQL: Guia de migração'
description: Este guia ensina-o a migrar as suas bases de dados SAP ASE para Azure SQL Database utilizando o SqL Server Migration Assistant for SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 81956a16142f314f54afd9d5a1b9055a559e906c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565338"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Guia de migração: SAP ASE para Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina-o a migrar as suas bases de dados SAP ASE para Azure SQL Database utilizando o SqL Server Migration Assistant for SAP Adapter Server Enterprise.

Para outros guias de migração, consulte [a Migração da Base de Dados.](https://datamigration.microsoft.com/) 

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar a sua base de dados SAP SE para a Base de Dados Azure SQL, precisa de:

- para verificar se o seu ambiente de origem é suportado. 
- [Sql Server Migration Assistant for SAP Adaptive Server Enterprise (anteriormente SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256). 

## <a name="pre-migration"></a>Pré-migração

Depois de conhecer os pré-requisitos, está pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração.

### <a name="assess"></a>Avaliação

Utilize [o SqL Server Migration Assistant (SSMA) para a SAP Adaptive Server Enterprise (formalmente SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) para analisar objetos e dados de base de dados, avaliar bases de dados para migração, migrar objetos de base de dados Sybase para Azure SQL Database e, em seguida, migrar dados para a Base de Dados Azure SQL. Para saber mais, consulte o [SqL Server Migration Assistant for Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Para criar uma avaliação, siga estes passos: 

1. **Abrir SSMA para Sybase**. 
1. Selecione **File** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar o seu projeto e, em seguida, selecione Azure SQL Database como o alvo de migração a partir do drop-down. Selecione **OK**.
1. Introduza os valores para os detalhes da ligação SAP na caixa de diálogo **'Ligar à Sybase'.** 
1. Clique com o botão direito na base de dados SAP que pretende migrar e, em seguida, escolha **Criar relatório**. Isto gera um relatório HTML.
1. Reveja o relatório HTML para compreender as estatísticas de conversão e quaisquer erros ou avisos. Também pode abrir o relatório no Excel para obter um inventário de objetos DB2 e o esforço necessário para realizar conversões de esquemas. A localização predefinição do relatório está na pasta do relatório dentro dos Projetos SSMA.

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 


### <a name="validate-type-mappings"></a>Validar mapeamentos de tipo

Antes de efetuar a conversão de esquemas valide os mapeamentos de tipo de dados predefinidos ou altere-os com base nos requisitos. Pode fazê-lo navegando no menu **Ferramentas** e escolhendo **Definições** de Projeto ou pode alterar o mapeamento do tipo para cada mesa selecionando a tabela no **Sap ASE Metadata Explorer**.


### <a name="convert-schema"></a>Converter esquema

Para converter o esquema, siga estes passos:

1. (Opcional) Para converter consultas dinâmicas ou ad-hoc, clique no nó à direita e escolha **Adicionar Statement**. 
1. Selecione **Connect to Azure SQL Database** na barra de navegação de primeira linha e forneça detalhes da Base de Dados Azure SQL. Pode optar por ligar-se a uma base de dados existente ou fornecer um novo nome, caso em que será criada uma base de dados no servidor alvo.
1. Clique com o botão direito no esquema SAP ASE no **Sybase Metadata Explorer** e escolha **Converter o esquema**. Em alternativa, pode **selecionar Converter o esquema** da barra de navegação de primeira linha. 
1. Compare e reveja a estrutura do esquema para identificar potenciais problemas. 

   Após a conversão do esquema, pode guardar este projeto localmente para um exercício de remediação de esquemas offline. Selecione **Save Project** a partir do menu **'Ficheiro'.** Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de poder publicar o esquema para a Base de Dados Azure SQL.

Para saber mais, consulte [converter o esquema](/sql/ssma/sybase/converting-sybase-ase-database-objects-sybasetosql)


## <a name="migrate"></a>Migrate 

Depois de ter os pré-requisitos necessários e ter concluído as tarefas associadas à fase **de pré-migração,** está pronto para executar o esquema e a migração de dados.

Para publicar o esquema e migrar os dados, siga estes passos: 

1. Clique com o botão direito na base de dados **do Azure SQL Database Metadata Explorer** e escolha **Sincronizar com Base de Dados**.  Esta ação publica o esquema SAP ASE para a instância da Base de Dados Azure SQL.
1. Clique com o botão direito no esquema SAP ASE no **SAP ASE Metadata Explorer** e escolha **Dados Migratórios**.  Em alternativa, pode **selecionar Dados Migratórios** a partir da barra de navegação de primeira linha.  
1. Após a conclusão da migração, consulte o **Relatório de Migração de Dados:** 
1. Validar a migração através da revisão dos dados e esquemas na instância Azure SQL Database, utilizando o Azure SQL Database Management Studio (SSMS).


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

> [!NOTE]
> Para mais detalhes sobre estas questões e medidas específicas para os mitigar, consulte o [Guia de Validação e Otimização pós-migração.](/sql/relational-databases/post-migration-validation-and-optimization-guide)


## <a name="next-steps"></a>Passos seguintes

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de base de dados e migração de dados, bem como tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Base de Dados Azure SQL consulte:
   - [Uma visão geral da Base de Dados SQL](../../database/sql-database-paas-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).
