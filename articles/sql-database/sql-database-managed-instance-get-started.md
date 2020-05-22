---
title: 'Portal Azure: Criar uma instância gerida'
description: Criar uma base de dados SQL gerida por exemplo, ambiente de rede e VM do cliente para acesso.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: e8a0b27f055f39186371e23e46c8b41679e05dea
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770113"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Quickstart: Criar uma base de dados Azure SQL gerida

Este quickstart acompanha-o através de como criar uma base de dados Azure SQL [gerida](sql-database-managed-instance.md) no portal Azure.

> [!IMPORTANT]
> Para limitações, consulte [regiões apoiadas](sql-database-managed-instance-resource-limits.md#supported-regions) e [tipos de subscrição suportados.](sql-database-managed-instance-resource-limits.md#supported-subscription-types)

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Se não tiver uma subscrição Azure, [crie uma conta gratuita.](https://azure.microsoft.com/free/)

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Criar uma instância gerida

Os seguintes passos mostram-lhe como criar um caso gerido:

1. Selecione **Azure SQL** no menu esquerdo do portal Azure. Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços**e, em seguida, introduza o **Azure SQL** na caixa de pesquisa.
2. Selecione **+Adicionar** para abrir a página de opção de **implementação Select SQL.** Pode visualizar informações adicionais sobre uma base de dados Azure SQL gerida através da seleção de detalhes do **Show** sobre o azulejo **de instâncias geridas.**
3. Selecione **Criar**.

   ![Criar uma instância gerida](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Utilize os separadores no formulário de fornecimento de instância de **instância gerida** para adicionar informações necessárias e opcionais. As seguintes secções descrevem estes separadores.

### <a name="basics"></a>Noções básicas

- Preencha as informações **obrigatórias exigidas** no separador Basics. Trata-se de um conjunto mínimo de informações necessárias para fornecer uma instância gerida.

   ![Separador "Básico" para criar um exemplo gerido](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Utilize a tabela abaixo como referência para obter informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Subscrição** | A sua assinatura. | Uma subscrição que lhe dá permissão para criar novos recursos. |
   | **Grupo de recursos** | Um novo ou existente grupo de recursos.|Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições).|
   | **Nome da instância gerida** | Qualquer nome válido.|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   | **Região** |A região em que se quer criar o caso gerido.|Para obter informações sobre [regiões, consulte as regiões de Azure.](https://azure.microsoft.com/regions/)|
   | **Início de sessão de administração da instância gerida** | Qualquer nome de utilizador válido. | Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). Não use "serveradmin" porque é uma função reservada ao nível do servidor.|
   | **Palavra-passe** | Qualquer senha válida.| A palavra-passe tem de ter, pelo menos, 16 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- **Selecione Configure Managed Instance** para dimensionar os recursos de computação e armazenamento e rever os níveis de preços. Utilize os controlos de deslize ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais. Quando terminar, selecione **Aplicar** para salvar a sua seleção. 

   ![Formulário de instância gerido](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Para rever as suas escolhas antes de criar uma instância gerida, pode selecionar **Review + criar**. Ou, configure as opções de networking selecionando **Next: Networking**.

### <a name="networking"></a>Redes

- Preencha informações opcionais no separador **Networking.** Se omite esta informação, o portal aplicará definições predefinidas.

   ![Separador "Networking" para criar uma instância gerida](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Utilize a tabela abaixo como referência para obter informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Rede virtual** | Selecione **criar uma nova rede virtual** ou uma rede virtual válida e sub-rede.| Se uma rede ou sub-rede não estiver disponível, deve ser [modificada para satisfazer os requisitos](sql-database-managed-instance-configure-vnet-subnet.md) de rede antes de selecioná-la como um alvo para a nova instância gerida. Para obter informações sobre os requisitos para configurar o ambiente de rede para uma instância gerida, consulte [Configurar uma rede virtual para uma instância gerida](sql-database-managed-instance-connectivity-architecture.md). |
   | **Tipo de ligação** | Escolha entre um proxy e um tipo de ligação redireccional.|Para obter mais informações sobre tipos de ligação, consulte a política de ligação da [Base de Dados Azure SQL](sql-database-connectivity-architecture.md#connection-policy).|
   | **Ponto final público**  | Selecione **Ativar**. | Para que uma instância gerida seja acessível através do ponto final dos dados públicos, é necessário ativar esta opção. | 
   | **Permitir o acesso** a partir (se o **ponto final público** estiver ativado) | Selecione uma das opções.   |A experiência do portal permite configurar um grupo de segurança com um ponto final público. </br> </br> Com base no seu cenário, selecione uma das seguintes opções: </br> <ul> <li>**Serviços Azure**: Recomendamos esta opção quando estiver a ligar-se do Power BI ou de outro serviço multiarrendatário. </li> <li> **Internet**: Utilize para efeitos de teste quando pretender girar rapidamente uma instância gerida. Não o recomendamos para ambientes de produção. </li> <li> **Sem acesso**: Esta opção cria uma regra de segurança **Negar.** Modifique esta regra para tornar uma instância gerida acessível através de um ponto final público. </li> </ul> </br> Para obter mais informações sobre segurança de pontos finais públicos, consulte A utilização de uma base de [dados Azure SQL gerida de forma segura com um ponto final público](sql-database-managed-instance-public-endpoint-securely.md).|

- Selecione **Review + crie** para rever as suas escolhas antes de criar uma instância gerida. Ou, configure configurar definições mais personalizadas selecionando **Next: Definições adicionais**.

### <a name="additional-settings"></a>Definições adicionais

- Preencha informações opcionais no separador **definições adicionais.** Se omite esta informação, o portal aplicará definições predefinidas.

   ![Separador "definições adicionais" para criar uma instância gerida](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Utilize a tabela abaixo como referência para obter informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Agrupamento** | Escolha a colagem que pretende utilizar para a sua instância gerida. Se migrar bases de dados do SQL Server, verifique a colagem de origem utilizando `SELECT SERVERPROPERTY(N'Collation')` e use esse valor.| Para obter informações sobre colagens, consulte [Definir ou alterar a colagem do servidor](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Time zone** (Fuso horário) | Selecione o fuso horário que a sua instância gerida observará.|Para mais informações, consulte [fusos horários.](sql-database-managed-instance-timezone.md)|
   | **Usar como failover secundário** | Selecione **Sim**. | Ative esta opção de utilizar a instância gerida como um grupo secundário de failover.|
   | **Instância gerida primária** (se **a utilização como secundária failover** está definida para **Sim)** | Escolha uma instância primária gerida existente que será unida na mesma zona DNS com a instância gerida que está a criar. | Este passo permitirá a configuração pós-criação do grupo failover. Para mais informações, consulte Tutorial: Adicione uma base de [dados SQL gerida a um grupo](sql-database-managed-instance-failover-group-tutorial.md)de failover .|

### <a name="review--create"></a>Rever + criar

5. Selecione **Review + crie** o separador para rever as suas escolhas antes de criar a instância gerida.

   ![Separador para rever e criar um caso gerido](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Selecione **Criar** para começar a fornecer a instância gerida.

> [!IMPORTANT]
> Implementar uma instância gerida é uma operação de longa duração. A implantação da primeira instância na sub-rede normalmente demora muito mais tempo do que a implantação numa subrede com instâncias geridas existentes. Para tempos médios de provisionamento, consulte operações de [gestão de instâncias geridas.](sql-database-managed-instance.md#managed-instance-management-operations)

### <a name="monitor-deployment-progress"></a>Monitorizar o progresso da implantação

7. Selecione o ícone **Notificações** para ver o estado da implementação.

   ![Progresso de implantação de uma implantação de instânciagerida](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Selecione **a implementação em curso** na notificação para abrir a janela de instância gerida e monitorizar melhor o progresso da implantação. 

> [!TIP]
> Se fechou o seu navegador web ou se afastou do ecrã de progresso de implementação, siga estes passos para localizar o ecrã de progresso de implementação:
> 1. No portal Azure, abra o grupo de recursos (no separador **Basics)** para o qual está a implementar uma instância gerida.
> 2. Selecione **Implementações**.
> 3. Selecione a operação de implantação de instâncias geridas em curso.

> [!IMPORTANT]
> Para conseguir o estatuto de criação de instânciagerida, é necessário ter **lido permissões** sobre o grupo de recursos. Se não tiver esta permissão ou a revogar enquanto a instância gerida estiver em processo de criação, isso pode fazer com que a instância gerida não seja visível na lista de implementações do grupo de recursos.
>

## <a name="post-deployment-operations"></a>Operações de pós-implantação

Para rever os recursos criados, afinar as definições de rede e recuperar os detalhes da ligação do hospedeiro (FQDN) siga os passos descritos nesta secção.

### <a name="view-resources-created"></a>Ver recursos criados

Após a implantação bem sucedida de instância gerida, para visualizar os recursos criados:

1. Abra o grupo de recursos para a sua instância gerida. Veja os seus recursos que foram criados para si na [Create um quickstart de instância gerido.](#create-a-managed-instance)

   ![Recursos de instância geridos](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Ver e afinar definições de rede

Para afinar opcionalmente as definições de rede, inspecione o seguinte:

1. Selecione a tabela de rotas para rever a rota definida pelo utilizador (UDR) que foi criada para si.

   ![Tabela de rota](./media/sql-database-managed-instance-get-started/route-table.png)

2. Na tabela de rotas, reveja as entradas para encaminhar o tráfego de e para dentro da rede virtual da instância gerida. Se criar ou configurar manualmente a tabela de rotas, certifique-se de criar estas entradas na tabela de rotas de instância gerida.

   ![Entrada para uma subnet de instância gerida para local](./media/sql-database-managed-instance-get-started/udr.png)

3. Volte ao grupo de recursos e selecione o grupo de segurança da rede.

   ![Grupo de segurança de rede](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Reveja as regras de segurança de entrada e saída. 

   ![Regras de segurança](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Se configurar o ponto final público para a sua instância gerida, precisa de abrir portas para permitir o tráfego de rede permitindo que as ligações possam ser geridas a partir da Internet pública, consulte [configurar um ponto final público para](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) obter mais informações.
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Recuperar detalhes da ligação para a instância gerida

Para ligar à instância gerida, siga estes passos para recuperar o nome do anfitrião e o nome de domínio totalmente qualificado (FQDN):

1. Volte ao grupo de recursos e selecione a sua instância gerida.

   ![Instância gerida no grupo de recursos](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. No separador **Overview,** localize a propriedade **do Anfitrião.** Copie o nome do anfitrião para a instância gerida para utilização no próximo arranque rápido.

   ![Nome do anfitrião](./media/sql-database-managed-instance-get-started/host-name.png)

   O valor copiado representa um nome de domínio totalmente qualificado (FQDN) que pode ser usado para ligar à instância gerida. É semelhante ao seguinte exemplo de endereço: *your_host_name.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Passos seguintes

Para aprender como se conectar a um caso gerido:
- Para uma visão geral das opções de ligação para aplicações, consulte [Connect as suas aplicações para uma instância gerida](sql-database-managed-instance-connect-app.md).
- Para um arranque rápido que mostre como se conectar a uma instância gerida a partir de uma máquina virtual Azure, consulte [Configure uma ligação virtual azure](sql-database-managed-instance-configure-vm.md).
- Para um arranque rápido que mostre como se conectar a uma instância gerida a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [Configurar uma ligação ponto-a-local](sql-database-managed-instance-configure-p2s.md).

Para restaurar uma base de dados existente do SQL Server a partir do local para uma instância gerida: 
- Utilize o Serviço de Migração de Bases de [Dados Azure para migração](../dms/tutorial-sql-server-to-managed-instance.md) para restaurar a partir de um ficheiro de backup da base de dados. 
- Utilize o [comando RESTABELECIMENTO T-SQL](sql-database-managed-instance-get-started-restore.md) para restaurar a partir de um ficheiro de backup da base de dados.

Para uma monitorização avançada do desempenho da base de dados de instâncias geridas com inteligência incorporada de resolução de problemas, consulte a Base de [Dados SQL do Monitor Azure utilizando o Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
