---
title: 'Db2 para Azure SQL Caso Gerido: Guia de migração'
description: Este guia ensina-o a migrar as suas bases de dados Db2 para Azure SQL Managed Instance usando o SqL Server Migration Assistant para db2.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: cb6a20d91458d2e4a6a60ced519531bd3c4356c5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642402"
---
# <a name="migration-guide-db2-to-azure-sql-managed-instance"></a>Guia de migração: Db2 para Azure SQL Caso Gerido
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Este guia ensina-o a migrar as suas bases de dados Db2 para Azure SQL Managed Instance usando o SqL Server Migration Assistant para Db2. 

Para outros guias de migração, consulte [a Migração da Base de Dados.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar a sua base de dados Db2 para a SQL Managed Instance, precisa de:

- para verificar se o ambiente [de origem é suportado](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites).
- para baixar [o SqL Server Migration Assistant (SSMA) para Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- um alvo [Azure SQL Managed Instance](../../managed-instance/instance-create-quickstart.md).
- Conectividade e permissões suficientes para aceder tanto à fonte como ao alvo. 



## <a name="pre-migration"></a>Pré-migração

Depois de conhecer os pré-requisitos, está pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração. 

### <a name="assess-and-convert"></a>Avaliar e converter



Crie uma avaliação utilizando o Sql Server Migration Assistant (SSMA). 

Para criar uma avaliação, siga estes passos:

1. Abrir o Sql Server Migration Assistant (SSMA) para Db2. 
1. Selecione **File** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar o seu projeto e, em seguida, selecione Azure SQL Managed Instance como o alvo de migração a partir do drop-down. Selecione **OK**:

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Forneça detalhes do projeto e selecione OK para guardar.":::


1. Introduza os valores para os detalhes da ligação Db2 na caixa de diálogo **'Ligar a Db2':**

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Ligue-se à sua instância Db2":::


1. Clique com o botão direito no esquema Db2 que pretende migrar e, em seguida, escolha **Criar relatório**. Isto gerará um relatório HTML. Em alternativa, pode escolher **Criar relatório** a partir da barra de navegação depois de selecionar o esquema:

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Clique com o botão direito no esquema e escolha criar relatório":::

1. Reveja o relatório HTML para compreender as estatísticas de conversão e quaisquer erros ou avisos. Também pode abrir o relatório no Excel para obter um inventário de objetos Db2 e o esforço necessário para realizar conversões de esquemas. A localização predefinição do relatório está na pasta do relatório dentro dos Projetos SSMA.

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Reveja o relatório para identificar quaisquer erros ou avisos":::


### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos do tipo de dados predefinidos e altere-os com base nos requisitos, se necessário. Para tal, siga estes passos: 

1. Selecione **Ferramentas** do menu. 
1. Selecione **Definições de projetos**. 
1. Selecione o **separador de mapeamentos Tipo:**

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Selecione o esquema e, em seguida, o mapeamento do tipo":::

1. Pode alterar o mapeamento do tipo para cada tabela selecionando a tabela no **explorador de metadados Db2**. 

### <a name="schema-conversion"></a>Conversão de schema 

Para converter o esquema, siga estes passos:

1. (Opcional) Adicione consultas dinâmicas ou ad-hoc a declarações. Clique com o botão direito no nó e, em seguida, escolha **Adicionar declarações**. 
1. Selecione **Connect to Azure SQL Managed Instance**. 
    1. Introduza detalhes de ligação para ligar à sua Instância Gerida Azure SQL. 
    1. Escolha a base de dados-alvo a partir do drop-down ou forneça um novo nome, caso em que uma base de dados será criada no servidor alvo. 
    1. Forneça detalhes de autenticação. 
    1. Selecione **Connect**:

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Preencha detalhes para ligar ao SQL Server":::


1. Clique com o botão direito no esquema e, em seguida, escolha **Converter o Esquema**. Em alternativa, pode escolher **converter o Schema** na barra de navegação superior depois de selecionar o seu esquema:

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Clique com o botão direito no esquema e escolha converter o esquema":::

1. Após a conversão concluída, compare e reveja a estrutura do esquema para identificar potenciais problemas e resolvê-los com base nas recomendações:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Compare e reveja a estrutura do esquema para identificar potenciais problemas e resolvê-los com base em recomendações.":::

1. Selecione **Os resultados** da Revisão no painel de saída e os erros de revisão no painel **da lista de erros.** 
1. Guarde o projeto localmente para um exercício de remediação de esquemas offline. Selecione **Save Project** a partir do menu **'Ficheiro'.** Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline e executar a remediação antes de poder publicar o esquema para SQL Managed Instance.


## <a name="migrate"></a>Migrate

Depois de ter concluído a avaliação das suas bases de dados e de abordar eventuais discrepâncias, o próximo passo é executar o processo de migração.

Para publicar o seu esquema e migrar os seus dados, siga estes passos:

1. Publique o esquema: Clique à direita na base de dados a partir do nó **de bases de dados** no **Azure SQL Managed Instance Metadata Explorer** e escolha **Sincronizar com Base de Dados**:

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Clique com o botão direito na base de dados e escolha sincronizar com base de dados":::

1. Migrar os dados: Clique na base de dados ou no objeto que pretende migrar no **Db2 Metadata Explorer** e escolha **dados migratórios**. Em alternativa, pode **selecionar Dados Migratórios** a partir da barra de navegação de primeira linha. Para migrar dados para uma base de dados inteira, selecione a caixa de verificação ao lado do nome da base de dados. Para migrar dados de tabelas individuais, expandir a base de dados, expandir tabelas e, em seguida, selecionar a caixa de verificação ao lado da tabela. Para omitir dados de tabelas individuais, limpe a caixa de verificação:

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Clique com o botão direito no esquema e escolha dados migratórios":::

1. Forneça detalhes de ligação tanto para db2 como para a SQL. 
1. Após a conclusão da migração, consulte o **Relatório de Migração de Dados:**  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Reveja o relatório de migração de dados":::

1. Conecte-se ao seu Azure SQL Managed Instance utilizando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração através da revisão dos dados e esquemas:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Compare o esquema em SSMS":::

## <a name="post-migration"></a>Pós-migração 

Depois de ter concluído com sucesso a fase de Migração, precisa de passar por uma série de tarefas pós-migração para garantir que tudo está a funcionar da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas 

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização deste facto exigirá, em alguns casos, alterações nas aplicações.


### <a name="perform-tests"></a>Realizar testes

A abordagem de ensaio para a migração de bases de dados consiste nas seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração da base de dados, é necessário utilizar consultas SQL. Tem de criar as consultas de validação para correr contra a fonte e as bases de dados-alvo. As suas consultas de validação devem abranger o âmbito que definiu.
1. **Configurar ambiente de teste**: O ambiente de ensaio deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.
1. **Executar testes de validação**: Escada os testes de validação contra a fonte e o alvo e, em seguida, analise os resultados.
1. **Executar testes de desempenho**: Executar teste de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.


## <a name="leverage-advanced-features"></a>Alavancar recursos avançados 

Certifique-se de aproveitar as funcionalidades avançadas baseadas na nuvem oferecidas pela Azure SQL Managed Instance, tais como [alta disponibilidade incorporada,](../../database/high-availability-sla.md) [deteção de ameaças,](../../database/azure-defender-for-sql.md) [monitorização e afinação da sua carga de trabalho.](../../database/monitor-tune-overview.md) 


Algumas funcionalidades do SQL Server só estão disponíveis quando o [nível de compatibilidade](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) da base de dados for alterado para o nível de compatibilidade mais recente (150). 

## <a name="migration-assets"></a>Ativos de migração 

Para assistência adicional, consulte os seguintes recursos, que foram desenvolvidos em apoio a um envolvimento de um projeto de migração no mundo real:

|Recurso  |Description  |
|---------|---------|
|[Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo e automatizada e uniforme processo de decisão da plataforma-alvo.|
|[Pacote de descoberta e avaliação de ativos de dados DB2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Depois de executar o script SQL numa base de dados, pode exportar os resultados para um ficheiro no sistema de ficheiros. Vários formatos de ficheiros são suportados, incluindo *.csv, para que possa capturar os resultados em ferramentas externas, tais como folhas de cálculo. Este método pode ser útil se quiser partilhar facilmente resultados com equipas que não tenham a bancada de trabalho instalada.|
|[Scripts e artefactos de inventário IBM Db2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Este ativo inclui uma consulta SQL que atinge as tabelas do sistema IBM Db2 LUW versão 11.1 e fornece uma contagem de objetos por esquema e tipo de objeto, uma estimativa aproximada de 'Dados Brutos' em cada esquema, e o dimensionamento de tabelas em cada esquema, com resultados armazenados num formato CSV.|
|[Db2 LUW escala pura em Azure - guia de configuração](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Db2%20PureScale%20on%20Azure.pdf)|Este guia serve de ponto de partida para um plano de implementação Db2. Embora os requisitos empresariais diferam, o mesmo padrão básico se aplica. Este padrão arquitetónico também pode ser usado para aplicações OLAP em Azure.|

A equipa de Engenharia Data SQL desenvolveu estes recursos. A principal carta desta equipa é desbloquear e acelerar a modernização complexa dos projetos de migração da plataforma de dados para a plataforma de dados Azure da Microsoft.

## <a name="next-steps"></a>Passos seguintes

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de base de dados e migração de dados, bem como tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Azure SQL Managed Instance consulte:
   - [Uma visão geral da SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).