---
title: Mover recursos Azure SQL entre regiões com Azure Resource Mover
description: Saiba como mover os recursos do Azure SQL para outra região com a Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e3e2c9aa42ff3189e90f57d7c6e92b2a71f46639
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061614"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>Tutorial: Mover recursos da Base de Dados Azure SQL para outra região

Neste tutorial, aprenda a mover bases de dados Azure SQL e piscinas elásticas para uma região de Azure diferente, utilizando [o Azure Resource Mover.](overview.md)

> [!NOTE]
> A Azure Resource Mover está atualmente em pré-visualização.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique os pré-requisitos e requisitos.
> * Selecione os recursos que pretende mover.
> * Resolver as dependências de recursos.
> * Prepare e mova o SQL Server para a região alvo.
> * Prepare e mova bases de dados e piscinas elásticas.
> * Decida se quer descartar ou cometer o movimento. 
> * Opcionalmente, remova recursos na região de origem após a mudança. 

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, inscreva-se no [portal Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

-  Verifique se tem acesso *ao Proprietário* na subscrição que contém os recursos que pretende mover.
    - A primeira vez que adiciona um recurso para um par de origem e destino específico numa subscrição do Azure, o Resource Mover cria uma [identidade gerida atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como Managed Service Identify (MSI)) que é fidedigna pela subscrição.
    - Para criar a identidade e atribuir-lhe a função necessária (administrador de Acesso ao Utilizador ou Colaborador na subscrição de origem), a conta que utiliza para adicionar recursos necessita de permissões *do Proprietário* na subscrição. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre os papéis do Azure.
- A subscrição precisa de quota suficiente para criar os recursos que está a mover na região alvo. Se não tiver quota, [solicite limites adicionais.](/azure/azure-resource-manager/management/azure-subscription-service-limits)
- Verifique os preços e os encargos associados à região-alvo para a qual está a movimentar recursos. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudá-lo.
    

## <a name="check-sql-requirements"></a>Verifique os requisitos do SQL

1. [Verifique quais](support-matrix-move-region-sql.md) as características da base de dados/piscina elástica suportadas para se deslocar para outra região.
2. Na região alvo, crie um servidor alvo para cada servidor de origem. [Saiba mais](/azure/azure-sql/database/active-geo-replication-security-configure#how-to-configure-logins-and-users).
4. Se as bases de dados forem encriptadas com encriptação de dados transparentes (TDE) e utilizar a sua própria chave de encriptação no Cofre da Chave Azure, [aprenda a](../key-vault/general/move-region.md) mover cofres-chave para outra região.
5. Se a sincronização de dados SQL estiver ativada, as bases de dados dos membros em movimento são suportadas. Após a mudança, tem de configurar a sincronização de dados SQL para a nova base de dados-alvo.
6. Remova as definições avançadas de segurança de dados antes da mudança. Após o movimento, [configufique as definições](/azure/sql-database/sql-database-advanced-data-security) ao nível do SQL Server na região alvo.
7. Se a auditoria estiver ativada, as políticas reiniciam a padrão após a mudança. [Configurar a auditoria](/azure/sql-database/sql-database-auditing) novamente, depois da mudança.
7. As políticas de retenção de backup para a base de dados de origem são transitadas para a base de dados-alvo. [Saiba mais](/azure/sql-database/sql-database-long-term-backup-retention-configure ) sobre a modificação das definições após a mudança.
8. Remova as regras de firewall ao nível do servidor antes da mudança. As regras de firewall ao nível da base de dados são copiadas do servidor de origem para o servidor alvo, durante o movimento. Após o movimento, [estabeleça regras](/azure/sql-database/sql-database-server-level-firewall-rule) de firewall para o SQL Server na região alvo.
9. Remova as definições de ajuste automático antes de mover. [Volte a configurar automaticamente ](/azure/sql-database/sql-database-automatic-tuning-enable) depois de se mover.
10. Remova as definições de alerta de base de dados antes da mudança. [Reinicie](/azure/sql-database/sql-database-insights-alerts-portal) depois de se mover.
    
## <a name="select-resources"></a>Selecione recursos

Selecione os recursos que pretende mover.

- Pode selecionar qualquer tipo de recurso suportado em quaisquer grupos de recursos na região de origem selecionada.
- Você move recursos para uma região alvo na mesma subscrição que a região de origem. Se quiser alterar a subscrição, pode fazê-lo depois de os recursos serem transferidos.

1. No portal Azure, procure *por recursos.* Em seguida, em **Serviços**, selecione **Azure Resource Mover**.

     ![Pesquisar resultados para o transportador de recursos no portal Azure](./media/tutorial-move-region-sql/search.png)

2. In **Overview**, clique **em Começar**.

    ![Botão para adicionar recursos para se mudar para outra região](./media/tutorial-move-region-sql/get-started.png)

3. Em **Move resources**Fonte +  >  **destino,** selecione a subscrição de origem e região.
4. No **Destino,** selecione a região para a qual pretende mover os recursos. Em seguida, clique em **Seguinte**.

    ![Página para selecionar origem e região de destino](./media/tutorial-move-region-sql/source-target.png)

6. Em **Recursos a mover,** clique em **Selecionar recursos.**
7. Em **Recursos Selecionados,** selecione os recursos. Só é possível adicionar recursos apoiados para a mudança. Em seguida, clique em **Fazer**.

    ![Página para selecionar recursos SQL para mover](./media/tutorial-move-region-sql/select-resources.png)

8. Em **Recursos a mover- se,** clique em **Seguinte**.

9. Em **Comentário + Adicionar,** verifique as definições de origem e destino. Verifique se compreende que os metadados sobre a mudança serão armazenados num grupo de recursos criado para o efeito na região dos metadados.


    ![Página para rever definições e proceder com movimento](./media/tutorial-move-region-sql/review.png)

10. Clique **em Continuar,** para começar a adicionar os recursos.
11. Após o processo de adição terminar com sucesso, clique em **Adicionar recursos para se mover** no ícone de notificação.
12. Depois de clicar na notificação, reveja os recursos na página **das regiões.**


> [!NOTE]
> 
> - O SQL Server encontra-se agora numa *missão manual pendente.*
> - Outros recursos adicionais estão em um estado *de preparação pendente.*
> - Se quiser remover um recurso de uma recolha de movimentos, o método para o fazer depende de onde estiver no processo de movimento. [Saiba mais](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Resolver dependências


1. Em **Regiões Trans regiões**, se os recursos mostrarem uma mensagem *de dependências validadas* na coluna **Questões,** clique no botão **'Validar dependências'.** O processo de validação começa.
2. Se forem encontradas dependências, clique em **Adicionar dependências**.

    ![Botão para adicionar dependências](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. Nas **dependências de adicionar**, selecione os recursos dependentes > Adicionar **dependências**. Monitorize o progresso nas notificações.

4. Adicione dependências adicionais se necessário e valide as dependências novamente. 

5. Na página **do Across regions,** verifique se os recursos estão agora num Estado pendente de *Preparação,* sem problemas.

    ![Página mostrando recursos na preparação do estado pendente](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>Mover o Servidor SQL

Atribua um servidor SQL alvo na região alvo e cometa o movimento.

### <a name="assign-a-target-sql-server"></a>Atribuir um servidor SQL alvo

1. Em **Regiões Trans regiões**, para o recurso SQL Server, na coluna **de configuração destino,** clique em **Recurso não atribuído**.
2. Selecione um recurso SQL Server existente na região alvo. 
    
    ![Entrada mostrando sql servidor estado definido para cometer movimento pendente](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> O estado do sql server de origem altera as alterações para *cometer movimento pendente*. 

### <a name="commit-the-sql-server-move"></a>Comprometa o movimento SQL Server

1. Em **Regiões,** selecione o SQL Server e, em seguida, clique em **Cometer movimento**.
2. Nos **recursos de cometer,** clique **em Cometer**.

    ![Página para comprometer o movimento sql server](./media/tutorial-move-region-sql/commit-sql-server.png)

3. O progresso da pista na barra de notificações.

> [!NOTE]
> Após o compromisso, o SQL Server encontra-se agora num estado *pendente de origem Delete.*


## <a name="prepare-resources-to-move"></a>Preparar recursos para mover

Com a origem SQL Server movida, pode preparar-se para mover os outros recursos.

## <a name="prepare-an-elastic-pool"></a>Preparar uma piscina elástica

1. Em **Regiões,** selecione a piscina elástica de origem (demo-test1-elasticpool na nossa passagem) e, em seguida, clique em **Preparar**.

    ![Botão para preparar recursos](./media/tutorial-move-region-sql/prepare-elastic.png)

2. Na **preparação dos recursos,** clique **em Preparar.**
3. Quando as notificações mostrarem que o processo de preparação foi bem sucedido, clique em **Refresh**.

> [!NOTE]
> A piscina elástica *encontra-se* agora em movimento iniciado pendente.

## <a name="prepare-a-single-database"></a>Preparar uma única base de dados

1. Em **Regiões,** selecione a base de dados única (não numa piscina elástica) e, em seguida, clique em **Preparar**.

    ![Botão para preparar recursos selecionados](./media/tutorial-move-region-sql/prepare-db.png)

2. Na **preparação dos recursos,** clique **em Preparar.**
3. Quando as notificações mostrarem que o processo de preparação foi bem sucedido, clique em **Refresh**.

> [!NOTE]
> A base de dados encontra-se agora num *estado de início pendente,* tendo sido criada na região alvo.


## <a name="move-the-pool-and-prepare-pool-databases"></a>Mova a piscina e prepare bases de dados de piscinas

Para preparar bases de dados numa piscina elástica, a piscina elástica deve estar em *um estado pendente.* Para se mudar para este estado, inicie a mudança para a piscina.

#### <a name="initiate-move---elastic-pool"></a>Movimento iniciado - piscina elástica

1. Em **Regiões,** selecione a piscina elástica de origem (demo-test1-elasticpool na nossa passagem) e, em seguida, clique em **Iniciar movimento**.
2. Em **Recursos Move,** clique em **Iniciar movimento**.

    
    ![Botão para iniciar movimento de piscina elástica](./media/tutorial-move-region-sql/initiate-elastic.png)

1. O progresso da pista na barra de notificações.
1. Quando as notificações mostrarem que o movimento foi bem sucedido, clique em **Refresh**.

> [!NOTE]
> A piscina elástica está agora em um *movimento comprometedor pendente* estado.

#### <a name="prepare-database"></a>Preparar base de dados

1. Em **Regiões,** selecione a base de dados (demo-test2-sqldb na nossa passagem) e, em seguida, clique em **Preparar**.
2. Na **preparação dos recursos,** clique **em Preparar.**

    ![Botão para preparar base de dados em piscina elástica](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

Durante a preparação, a base de dados-alvo é criada na região alvo, e a replicação de dados começa. Depois de preparar, a base de dados encontra-se em *processo de início pendente.* 

![Botão para preparar a base de dados selecionada na piscina elástica](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>Mover bases de dados

Comece a mover as bases de dados.
1. Em **Todas as regiões**, selecione recursos com **movimento de iniciado**do estado pendente . Em seguida, clique em **Iniciar movimento**.
2. Em **Recursos Move,** clique em **Iniciar movimento**.

    ![Página para iniciar movimento](./media/tutorial-move-region-sql/initiate-move.png)

3. O progresso da pista na barra de notificações.

> [!NOTE]
> As bases de dados estão agora num *processo pendente* de estado.


## <a name="discard-or-commit"></a>Descartar ou comprometer-se?

Após o movimento inicial, pode decidir se quer cometer o movimento ou descartá-lo. 

- **Descartar**: É melhor descartar um movimento se estiver a testar e não pretende mover o recurso de origem. Descartar o movimento devolve o recurso a um estado de **movimento iniciado pendente**.
- **Compromete-se**: Compromete-se a concluir a mudança para a região alvo. Após o compromisso, um recurso de origem estará num estado de **Eliminação de fonte pendente**, e pode decidir se pretende eliminá-lo.


## <a name="discard-the-move"></a>Descartar o movimento 

Pode descartar o movimento da seguinte forma:

1. Em **Todas as regiões**, selecione recursos com movimento de compromisso do estado **pendente**, e clique em **Descartar move**.
2. Em **Descartar movimento**, clique em **Descartar**.
3. O progresso da pista na barra de notificações.


> [!NOTE]
> - Depois de descartarem os recursos, estão numa *ação iniciada pendente.*
> - Se houver apenas uma piscina elástica, descarte progride, e a piscina elástica criada na região alvo é eliminada.
> - Se houver uma piscina elástica com bases de dados associadas no estado pendente do *Commit,* não pode descartar a piscina elástica.
> - Se descartar uma base de dados SQL, os recursos da região-alvo não são eliminados. 

Se pretender recomeçar o movimento depois de descartado, selecione a base de dados SQL ou piscina elástica e inicie o movimento novamente.


## <a name="commit-the-move"></a>Cometa o movimento

Terminando bases de dados móveis e piscinas elásticas da seguinte forma:


1. Verifique se o SQL Server é uma *fonte de exclusão pendente.*
2. Atualize as cadeias de ligação da base de dados para a região alvo, antes de se comprometer.
3. Em **Todas as regiões,** selecione os recursos SQL e, em seguida, clique em **Cometer movimento**.
4. Nos **recursos de cometer,** clique **em Cometer**.

    ![Comprometer-se](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. Track cometer progressos na barra de notificações.


> [!NOTE]
> Algum tempo de inatividade ocorre para bases de dados SQL durante o processo de compromisso.
> Bases de dados comprometidas e piscinas elásticas estão agora num estado *pendente de exclusão.*
> Após o compromisso, atualize as definições relacionadas com a base de dados, incluindo regras de firewall, políticas e alertas, na base de dados-alvo.


## <a name="delete-source-resources-after-commit"></a>Eliminar recursos de origem após cometer

Após a mudança, pode opcionalmente apagar recursos na região de origem. 

1. Em **Regiões Diferentes,** clique no nome de cada recurso de origem que pretende eliminar.
2. Na página de propriedades para cada recurso, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Mudou as bases de dados do Azure SQL para outra região do Azure.
> * Mudou as piscinas elásticas Azure SQL para outra região.

Agora, a tentar mover os VMs do Azure para outra região.

> [!div class="nextstepaction"]
> [Mover VMs do Azure](./tutorial-move-region-virtual-machines.md)
