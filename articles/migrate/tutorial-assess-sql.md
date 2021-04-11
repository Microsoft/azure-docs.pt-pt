---
title: Tutorial para avaliar casos SQL para migração para Azure SQL Gestd Instance e Azure SQL Database
description: Saiba como criar avaliação para O SQL Azure em Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: d4078d1403df01475c6055dded2bd012e97af98e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557994"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>Tutorial: Avaliar os casos sql para migração para Azure SQL

Como parte da sua viagem de migração para Azure, você avalia as suas cargas de trabalho no local para medir a prontidão na nuvem, identificar riscos e estimar custos e complexidade.
Este artigo mostra-lhe como avaliar as bases de dados de casos do SQL Server descobertos em preparação para a migração para Azure SQL, utilizando a ferramenta Azure Migrate: Discovery e assessment.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Executar uma avaliação com base na configuração do servidor e nos dados de desempenho.
> * Reveja uma avaliação do Azure SQL 

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão sempre que possível. 


## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

- Antes de seguir este tutorial para avaliar os seus casos de SERVIDOR SQL para migração para Azure SQL, certifique-se de ter descoberto as instâncias SQL que deseja avaliar usando o aparelho Azure Migrate, [siga este tutorial](tutorial-discover-vmware.md)
- Se pretender experimentar esta funcionalidade num projeto existente, certifique-se de que completou os [pré-requisitos](how-to-discover-sql-existing-project.md) deste artigo.


## <a name="run-an-assessment"></a>Executar uma avaliação
E executar uma avaliação da seguinte forma:
1. Na página **'Visão Geral'** > **Windows, Linux e SQL Server,** clique em **Avaliar e migrar servidores**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Página geral para Azure Migrate":::
2. On **Azure Migrate: Discovery and assessment,** clique em **Avaliar** e escolha o tipo de avaliação como **Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Dropdown para escolher o tipo de avaliação como Azure SQL":::
3. Nos **servidores de Avaliação** > poderá ver o tipo de avaliação pré-selecionado como **Azure SQL** e a fonte de descoberta padrão para **servidores descobertos a partir do aparelho Azure Migrate**.

4. Clique **em Editar** para rever as propriedades de avaliação.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Editar botão de onde as propriedades de avaliação podem ser personalizadas":::
5. Em propriedades de avaliação > **propriedades-alvo:**
    - Na **localização do alvo,** especifique a região de Azure para a qual pretende migrar. 
        - A configuração e recomendações de custos do Azure SQL baseiam-se na localização que especifica. 
    - No **tipo de implementação de alvo,**
        - Selecione **Recomendado**, se quiser que a Azure Migrate avalie a prontidão dos seus casos SQL para migrar para Azure SQL MI e Azure SQL DB, e recomendar a opção de implementação do alvo mais adequada, o nível alvo, configuração Azure SQL e estimativas mensais. [Saiba mais](concepts-azure-sql-assessment-calculation.md)
        - Selecione **Azure SQL DB,** se quiser avaliar a prontidão dos seus casos SQL para migrar apenas para Azure SQL Databases e rever o nível alvo, configuração Azure SQL e estimativas mensais.
        - Selecione **Azure SQL MI,** se quiser avaliar a prontidão dos seus casos SQL para migrar apenas para Azure SQL Gestd Instance e rever o nível alvo, configuração Azure SQL e estimativas mensais.
    - Na **Capacidade Reservada**, especifique se pretende utilizar a capacidade reservada para o servidor SQL após a migração.
        - Se selecionar uma opção de capacidade reservada, não pode especificar "Desconto (%)".

6. Nos critérios de **avaliação** de propriedades >:
    - Os critérios de dimensionamento estão em incumprimento em **termos de desempenho,** o que significa que a migração do Azure recolherá métricas de desempenho relativas a instâncias SQL e as bases de dados geridas por ele para recomendar uma base de dados Azure SQL de tamanho ideal e/ou configuração de instância gerida Azure SQL. Pode especificar:
        - **Histórico de** desempenho para indicar a duração dos dados em que pretende basear a avaliação. (O padrão é um dia)
        - **Utilização percentil**, para indicar o valor percentil que pretende utilizar para a amostra de desempenho. (O padrão é o percentil 95)
    - No **fator Comfort,** indique o tampão que pretende utilizar durante a avaliação. Isto explica questões como o uso sazonal, o histórico de desempenho curto e, provavelmente, o aumento do uso futuro. Por exemplo, se utilizar um fator de conforto de dois: 
        
        **Componente** | **Utilização eficaz** | **Adicionar fator de conforto (2.0)**
        --- | --- | ---
        Núcleos | 2  | 4
        Memória | 8 GB | 16 GB
   
7. Na **fixação de preços:**
    - No **programa Oferta/Licenciamento,** especifique a oferta Azure se estiver inscrito. Atualmente, só pode escolher entre Pay-as-you-go e Pay-as-you-go Dev/Test. 
        - Você pode obter desconto adicional aplicando capacidade reservada e Azure Hybrid Benefit em cima da oferta Pay-as-you-go. 
        - Pode aplicar o Azure Hybrid Benefit em cima do Pay-as-you-go Dev/Test. A avaliação atualmente não suporta a aplicação da Capacidade Reservada para além da oferta Pay-as-you-go Dev/Test.
    - No **Service Tier**, escolha a opção de nível de serviço mais adequada para acomodar as suas necessidades de negócio para migração para Azure SQL Database e/ou Azure SQL Managed Instance: 
        - Selecione **Recomendado** se quiser que a Azure Migrate recomende o nível de serviço mais adequado para os seus servidores. Isto pode ser um propósito geral ou um negócio crítico. Saiba Mais
        - Selecione **Finalidade Geral** se quiser uma configuração Azure SQL projetada para cargas de trabalho orientadas para o orçamento.
        - Selecione **Business Critical** se quiser uma configuração Azure SQL projetada para cargas de trabalho de baixa latência com alta resiliência a falhas e falhas rápidas.
    - Em **Desconto (%)**, adicione quaisquer descontos específicos de subscrição que receba em cima da oferta Azure. A predefinição é 0%.
    - Em **Moeda,** selecione a moeda de faturação para a sua conta.
    - No **Azure Hybrid Benefit**, especifique se já tem uma licença SQL Server. Se o fizer e estiver coberto com uma garantia de software ativa das subscrições do servidor SQL, pode candidatar-se ao Benefício Híbrido Azure quando levar licenças ao Azure.
    - Clique em Guardar se escoda alterações.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Guardar botão em propriedades de avaliação":::
8. Em **Avaliar servidores** > clique em Seguinte.
9.  Em **Servidores Selecionados para avaliar** o nome de  >  **avaliação** > especificar um nome para a avaliação.
10. Em **Select ou criar um grupo** > selecione Create **New** e especifique um nome de grupo.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Localização do novo botão de grupo":::
11. Selecione o aparelho e selecione os servidores que pretende adicionar ao grupo. Em seguida, clique em Seguinte.
12. Em **Review + criar avaliação,** rever os detalhes da avaliação e clicar em Criar Avaliação para criar o grupo e executar a avaliação.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Localização do Revisão e criação de botão de avaliação.":::
13. Após a avaliação ser criada, vá ao **Windows, Linux e SQL Server**  >  **Azure Migrate: Discovery and assessment** tile > Clique no número ao lado da avaliação do Azure SQL.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Navegação para a avaliação criada":::
15. Clique no nome de avaliação que deseja ver.

> [!NOTE]
> Como as avaliações do Azure SQL são avaliações baseadas no desempenho, recomendamos que espere pelo menos um dia após iniciar a descoberta antes de criar uma avaliação. Isto dá tempo para recolher dados de desempenho com maior confiança. Se a sua descoberta ainda estiver em curso, a prontidão dos seus casos SQL será marcada como **Desconhecido.** Idealmente, depois de começar a ser descoberto, **aguarde a duração de desempenho que especifica (dia/semana/mês)** para criar ou recalcular a avaliação para uma classificação de alta confiança. 

## <a name="review-an-assessment"></a>Rever uma avaliação

**Para visualizar uma avaliação:**

1. Servidor Windows, **Linux e SQL**  >  **Azure Migrate: Descoberta e avaliação** > Clique no número ao lado da avaliação do Azure SQL.
2. Clique no nome de avaliação que deseja ver. Como exemplo (estimativas e custos, por exemplo apenas): :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="visão geral da avaliação do SQL":::
3. Reveja o resumo da avaliação. Também pode editar as propriedades de avaliação ou recalcular a avaliação.

#### <a name="discovered-items"></a>Itens descobertos

Isto indica o número de servidores, casos e bases de dados SQL que foram avaliados nesta avaliação.
    
#### <a name="azure-readiness"></a>Preparação para o Azure

Isto indica a distribuição de instâncias SQL avaliadas: 
    
**Tipo de implantação de destino (em propriedades de avaliação)** | **Prontidão**   
--- | --- |
**Recomendado** |  Pronto para a Base de Dados Azure SQL, Pronto para Exemplo Gerido Azure SQL, potencialmente pronto para Azure VM, Prontidão desconhecida (Caso a descoberta esteja em andamento ou existam alguns problemas de descoberta a serem corrigidos)
**Azure SQL DB** ou **Azure SQL MI** | Pronto para Azure SQL Database ou Azure SQL Managed Instance, Não pronto para Azure SQL Database ou Azure SQL Managed Instance, Readiness unknown (Caso a descoberta esteja em andamento ou existam alguns problemas de descoberta a corrigir)
     
Você pode perfurar para entender detalhes sobre questões de migração/avisos que você pode remediar antes da migração para Azure SQL. [Saiba mais](concepts-azure-sql-assessment-calculation.md) Também pode rever as configurações Azure SQL recomendadas para migrar para bases de dados Azure SQL e/ou Instâncias Geridas.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Azure SQL Base de Dados e Detalhes de custos de Instância Gerida

A estimativa mensal de custos inclui custos de cálculo e armazenamento para configurações Azure SQL correspondentes à base de dados Azure SQL recomendada e/ou tipo de implementação de instância gerida Azure SQL. [Saiba mais](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Revisão de prontidão

1. Clique **na prontidão do Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Detalhes de prontidão Azure SQL":::
1. Na prontidão do Azure SQL, reveja a prontidão do **Azure SQL DB** e **a prontidão do Azure SQL MI** para os casos SQL avaliados:
    - **Pronto**: O caso está pronto para ser migrado para Azure SQL DB/MI sem quaisquer problemas de migração ou avisos. 
        - Pronto (ícone de informação hiperligada e azul): O caso está pronto para ser migrado para Azure SQL DB/MI sem quaisquer problemas de migração, mas tem alguns avisos de migração que precisa de rever. Pode clicar na hiperligação para rever os avisos de migração e a orientação de remediação recomendada: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="Avaliação com estado pronto":::       
    - **Não está pronto**: O caso tem um ou mais problemas de migração para a Azure SQL DB/MI. Pode clicar na hiperligação e rever os problemas de migração e a orientação de remediação recomendada.
    - **Desconhecido**: A azure Migrate não pode avaliar a prontidão, porque a descoberta está em andamento ou há problemas durante a descoberta que precisam de ser corrigidos a partir da lâmina de notificações. Se o problema persistir, contacte o Suporte da Microsoft. 
1. Reveja o tipo de implementação recomendado para a instância SQL que é determinada de acordo com a matriz abaixo:

    - **Tipo de implantação de destino** (selecionado em propriedades de avaliação): **Recomendado**

        **Prontidão Azure SQL DB** | **Prontidão Azure SQL MI** | **Tipo de implantação recomendado** | **Configuração Azure SQL e estimativas de custos calculadas?**
         --- | --- | --- | --- |
        Pronto | Pronto | Azure SQL DB ou Azure SQL MI [Saiba mais](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | Yes
        Pronto | Não pronto ou desconhecido | BD SQL do Azure | Yes
        Não pronto ou desconhecido | Pronto | Azure SQL MI | Yes
        Não está pronto. | Não está pronto. | Potencialmente pronto para Azure VM [Saiba mais](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | No
        Não pronto ou desconhecido | Não pronto ou desconhecido | Desconhecido | No
    
    - **Tipo de implantação de destino** (selecionado em propriedades de avaliação): **Azure SQL DB**
    
        **Prontidão Azure SQL DB** | **Configuração Azure SQL e estimativas de custos calculadas?**
        --- | --- |
        Pronto | Yes
        Não está pronto. | Não
        Desconhecido | No
    
    - **Tipo de implantação de destino** (selecionado em propriedades de avaliação): **Azure SQL MI**
    
        **Prontidão Azure SQL MI** | **Configuração Azure SQL e estimativas de custos calculadas?**
         --- | --- |
        Pronto | Yes
        Não está pronto. | Não
        Desconhecido | No

4. Clique na broca de nome de instância para ver o número de bases de dados dos utilizadores, detalhes de casos incluindo propriedades de instância, cálculo (telescópio em instância) e detalhes de armazenamento de bases de dados de origem.
5. Clique no número de bases de dados dos utilizadores para rever a lista de bases de dados e os seus dados. Como exemplo (estimativas e custos, por exemplo apenas): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="detalhes de instâncias SQL":::
5. Clique em detalhes de revisão na coluna questões de migração para rever as questões de migração e avisos para um determinado tipo de implementação de alvo. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="Questões de migração dB e avisos":::

### <a name="review-cost-estimates"></a>Rever as estimativas de custos
O resumo da avaliação mostra os custos estimados mensal de computação e armazenamento para as configurações Azure SQL correspondentes às bases de dados Azure SQL recomendadas e/ou tipo de implementação de instâncias geridas.

1. Reveja os custos totais mensais. Os custos são agregados para todos os casos SQL no grupo avaliado.
    - As estimativas de custos baseiam-se na configuração Azure SQL recomendada, por exemplo. 
    - Os custos mensais estimados para computação e armazenamento são mostrados. Como exemplo (estimativas e custos, por exemplo:
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Detalhes de custos":::

1. Pode perfurar a um nível de instância para ver a configuração do Azure SQL e as estimativas de custos a um nível de instância.  
1. Também pode aprofundar a lista de bases de dados para rever a configuração do Azure SQL e as estimativas de custos por base de dados quando é recomendada uma configuração de Base de Dados Azure SQL.

### <a name="review-confidence-rating"></a>Rever a classificação de confiança
A Azure Migrate atribui uma classificação de confiança a todas as avaliações do Azure SQL com base na disponibilidade dos pontos de dados de desempenho/utilização necessários para calcular a avaliação de todas as instâncias e bases de dados sql avaliadas. A classificação é de uma estrela (mais baixa) para cinco estrelas (mais alta).
A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho na avaliação. As classificações de confiança são as seguintes:

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Saiba mais](concepts-azure-sql-assessment-calculation.md#confidence-ratings) sobre as classificações de confiança.


## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](concepts-azure-sql-assessment-calculation.md) sobre como as avaliações do Azure SQL são calculadas.
- Comece a migrar casos e bases de dados SQL utilizando [o Serviço de Migração da Base de Dados Azure](../dms/dms-overview.md).