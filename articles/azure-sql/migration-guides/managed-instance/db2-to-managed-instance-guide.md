---
title: 'Db2 para Azure SQL Caso Gerido: Guia de migração'
description: Este guia ensina-o a migrar as suas bases de dados IBM Db2 para Azure SQL Managed Instance, utilizando o SqL Server Migration Assistant para db2.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: bb031e4f0233b9f26571c3ae9fc2a26fe5fe957f
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553962"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-managed-instance"></a>Guia de migração: IBM Db2 para Azure SQL Caso Gerido
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Este guia ensina-o a migrar as suas bases de dados IBM Db2 para Azure SQL Managed Instance, utilizando o SqL Server Migration Assistant para Db2. 

Para outros guias de migração, consulte os [Guias de Migração da Base de Dados Azure.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar a sua base de dados Db2 para a SQL Managed Instance, precisa de:

- Para verificar se o seu [ambiente de origem é suportado](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites).
- Para baixar [o SqL Server Migration Assistant (SSMA) para Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- Um exemplo-alvo de [Azure SQL Managed Instance](../../managed-instance/instance-create-quickstart.md).
- Conectividade e permissões suficientes para aceder tanto à fonte como ao alvo. 

## <a name="pre-migration"></a>Pré-migração

Depois de conhecer os pré-requisitos, está pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração. 

### <a name="assess-and-convert"></a>Avaliar e converter

Crie uma avaliação utilizando o SqL Server Migration Assistant. 

Para criar uma avaliação, siga estes passos:

1. [Abrir SSMA para Db2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Selecione **o**  >  **novo projeto de arquivo**. 
1. Forneça um nome de projeto e uma localização para salvar o seu projeto. Em seguida, selecione Azure SQL Managed Instance como o alvo de migração da lista de drop-down, e selecione **OK**.

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Screenshot que mostra detalhes do projeto para especificar.":::


1. Em **Ligar ao Db2,** introduza valores para os detalhes da ligação Db2. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Screenshot que mostra opções para ligar à sua instância Db2.":::


1. Clique com o botão direito no esquema Db2 que pretende migrar e, em seguida, escolha **Criar relatório**. Isto gerará um relatório HTML. Em alternativa, pode escolher **Criar relatório** a partir da barra de navegação depois de selecionar o esquema.

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Screenshot que mostra como criar um relatório.":::

1. Reveja o relatório HTML para compreender as estatísticas de conversão e quaisquer erros ou avisos. Também pode abrir o relatório no Excel para obter um inventário de objetos Db2 e o esforço necessário para realizar conversões de esquemas. A localização predefinição do relatório está na pasta do relatório dentro *dos Projetos SSMA*.

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Screenshot do relatório que revê para identificar quaisquer erros ou avisos":::


### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos do tipo de dados predefinidos e altere-os com base nos requisitos, se necessário. Para tal, siga estes passos: 

1. Selecione **Ferramentas** do menu. 
1. Selecione **Definições de projetos**. 
1. Selecione o **separador de mapeamentos Tipo.**

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Screenshot que mostra a seleção do esquema e do mapeamento do tipo.":::

1. Pode alterar o mapeamento do tipo para cada tabela selecionando a tabela no **Db2 Metadata Explorer**. 

### <a name="convert-schema"></a>Converter esquema 

Para converter o esquema, siga estes passos:

1. (Opcional) Adicione consultas dinâmicas ou ad-hoc a declarações. Clique com o botão direito no nó e, em seguida, escolha **Adicionar declarações**. 
1. Selecione **Connect to Azure SQL Managed Instance**. 
    1. Introduza detalhes de ligação para ligar ao Azure SQL Managed Instance. 
    1. Escolha a base de dados-alvo da lista de drop-down ou forneça um novo nome, caso em que uma base de dados será criada no servidor alvo. 
    1. Forneça detalhes de autenticação. 
    1. Selecione **Ligar**.

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Screenshot que mostra os detalhes necessários para ligar ao SQL Server.":::


1. Clique com o botão direito no esquema e, em seguida, escolha **Converter o Esquema**. Em alternativa, pode escolher **converter o Schema** na barra de navegação superior depois de selecionar o seu esquema.

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Screenshot que mostra selecionar o esquema e convertê-lo.":::

1. Após a conversão concluída, compare e reveja a estrutura do esquema para identificar potenciais problemas. Resolva os problemas com base nas recomendações.

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Screenshot que mostra comparar e rever a estrutura do esquema para identificar potenciais problemas.":::

1. No painel **de saída,** selecione **resultados de revisão**. No painel da **lista De Erro,** reveja os erros. 
1. Guarde o projeto localmente para um exercício de remediação de esquemas offline. A partir do menu **'Ficheiro',** **selecione Save Project**. Isto dá-lhe a oportunidade de avaliar os esquemas de origem e alvo offline, e executar a remediação antes de poder publicar o esquema para SQL Managed Instance.

## <a name="migrate"></a>Migrate

Depois de ter concluído a avaliação das suas bases de dados e de abordar eventuais discrepâncias, o próximo passo é executar o processo de migração.

Para publicar o seu esquema e migrar os seus dados, siga estes passos:

1. Publique o esquema. No **Azure SQL Managed Instance Metadata Explorer**, a partir do nó **de bases de dados,** clique com o botão direito na base de dados. Em seguida, **selecione Synchronize com Base de Dados**.

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Screenshot que mostra a opção de sincronizar com a base de dados.":::

1. Migrar os dados. Clique com o botão direito na base de dados ou objeto que pretende migrar no **Db2 Metadata Explorer** e escolha **dados migratórios**. Em alternativa, pode **selecionar Dados migratórios** a partir da barra de navegação. Para migrar dados para uma base de dados inteira, selecione a caixa de verificação ao lado do nome da base de dados. Para migrar dados de tabelas individuais, expandir a base de **dados,** expandir tabelas e, em seguida, selecionar a caixa de verificação ao lado da tabela. Para omitir dados de tabelas individuais, limpe a caixa de verificação.

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Screenshot que mostra selecionar o esquema e optar por migrar dados.":::

1. Forneça detalhes de ligação tanto para db2 como para a SQL. 
1. Após a conclusão da migração, consulte o **Relatório de Migração de Dados.**  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Screenshot que mostra onde rever o relatório de migração de dados.":::

1. Ligue-se ao seu exemplo de Azure SQL Managed Instance utilizando [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Validar a migração através da revisão dos dados e esquemas:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Screenshot que mostra comparar o esquema no SQL Server Management Studio.":::

## <a name="post-migration"></a>Pós-migração 

Depois de concluída a migração, é necessário passar por uma série de tarefas pós-migração para garantir que tudo esteja a funcionar da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas 

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização deste facto exigirá, em alguns casos, alterações nas aplicações.


### <a name="perform-tests"></a>Realizar testes

Os ensaios consistem nas seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração da base de dados, é necessário utilizar consultas SQL. Tem de criar as consultas de validação para correr contra a fonte e as bases de dados-alvo. As suas consultas de validação devem abranger o âmbito que definiu.
1. **Configurar o ambiente de ensaio**: O ambiente de ensaio deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.
1. **Executar testes de validação**: Escada os testes de validação contra a fonte e o alvo e, em seguida, analise os resultados.
1. **Executar testes de desempenho**: Executar testes de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.

## <a name="advanced-features"></a>Funcionalidades avançadas 

Certifique-se de aproveitar as funcionalidades avançadas baseadas na nuvem oferecidas pela Azure SQL Managed Instance, tais como [alta disponibilidade incorporada,](../../database/high-availability-sla.md) [deteção de ameaças,](../../database/azure-defender-for-sql.md) [monitorização e afinação da sua carga de trabalho.](../../database/monitor-tune-overview.md) 

Algumas funcionalidades do SQL Server só estão disponíveis quando o [nível de compatibilidade](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) da base de dados é alterado para o nível de compatibilidade mais recente. 

## <a name="migration-assets"></a>Ativos de migração 

Para assistência adicional, consulte os seguintes recursos, que foram desenvolvidos em apoio a um envolvimento de um projeto de migração no mundo real:

|Recurso  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo e automatizada e uniforme processo de decisão da plataforma-alvo.|
|[Pacote de descoberta e avaliação de ativos de dados DB2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Depois de executar o script SQL numa base de dados, pode exportar os resultados para um ficheiro no sistema de ficheiros. Vários formatos de ficheiros são suportados, incluindo *.csv, para que possa capturar os resultados em ferramentas externas, tais como folhas de cálculo. Este método pode ser útil se quiser partilhar facilmente resultados com equipas que não tenham a bancada de trabalho instalada.|
|[Scripts e artefactos de inventário IBM Db2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Este ativo inclui uma consulta SQL que atinge as tabelas do sistema IBM Db2 LUW versão 11.1 e fornece uma contagem de objetos por esquema e tipo de objeto, uma estimativa aproximada de "dados brutos" em cada esquema, e o dimensionamento de tabelas em cada esquema, com resultados armazenados num formato CSV.|
|[Db2 LUW escala pura em Azure - guia de configuração](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Db2%20PureScale%20on%20Azure.pdf)|Este guia serve de ponto de partida para um plano de implementação Db2. Embora os requisitos empresariais diferam, o mesmo padrão básico se aplica. Este padrão arquitetónico também pode ser usado para aplicações OLAP em Azure.|

A equipa de Engenharia Data SQL desenvolveu estes recursos. A principal carta desta equipa é desbloquear e acelerar a modernização complexa dos projetos de migração da plataforma de dados para a plataforma de dados Azure da Microsoft.

## <a name="next-steps"></a>Passos seguintes

- Para a Microsoft e serviços e ferramentas de terceiros para ajudá-lo com vários cenários de base de dados e migração de dados, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Azure SQL Managed Instance, consulte:
   - [Uma visão geral da SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, consulte:
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices for costing and sizing workloads migrated to Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) (Melhores práticas para avaliar custos e dimensionar as cargas de trabalho migradas para o Azure) 

- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados.](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter mais informações sobre como realizar testes da camada A/B de acesso a dados, consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).
