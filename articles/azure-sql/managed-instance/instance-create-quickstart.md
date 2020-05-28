---
title: 'Quickstart: Criar uma instância gerida Azure SQL (Portal)'
description: Crie um Azure SQL Managed Instance, ambiente de rede e VM cliente para acesso usando o portal Azure neste arranque rápido.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: cc3a25992297dd8deb02deb2c561cad4b53e318b
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84113750"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Quickstart: Criar uma instância gerida azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este quickstart ensina-o a criar um [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) no portal Azure.

> [!IMPORTANT]
> Para limitações, consulte [regiões apoiadas](resource-limits.md#supported-regions) e [tipos de subscrição suportados.](resource-limits.md#supported-subscription-types)

## <a name="create-sql-managed-instance"></a>Criar Instância Gerida do SQL

Para criar um Azure SQL Manged Instance, siga estes passos: 

### <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Se não tiver uma subscrição Azure, [crie uma conta gratuita.](https://azure.microsoft.com/free/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Azure SQL** no menu esquerdo do portal Azure. Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços**e, em seguida, introduza o **Azure SQL** na caixa de pesquisa.
1. Selecione **+Adicionar** para abrir a página de opção de **implementação Select SQL.** Pode ver informações adicionais sobre um Caso Gerido Azure SQL selecionando detalhes do **Show** no azulejo **SQL Managed Instance.**
1. Selecione **Criar**.

   ![Criar uma instância gerida por SQL](./media/instance-create-quickstart/create-managed-instance.png)

4. Utilize os separadores no formulário de provisionamento **create Azure SQL Managed Instance** para adicionar informações necessárias e opcionais. As seguintes secções descrevem estes separadores.

### <a name="basics-tab"></a>Separador básico

- Preencha as informações **obrigatórias exigidas** no separador Basics. Este é um conjunto mínimo de informações necessárias para fornecer uma Instância Gerida SQL.

   ![Separador "Basics" para a criação de uma instância gerida por SQL](./media/instance-create-quickstart/mi-create-tab-basics.png)

   Utilize a tabela abaixo como referência para obter informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Subscrição** | A sua assinatura. | Uma subscrição que lhe dá permissão para criar novos recursos. |
   | **Grupo de recursos** | Um novo ou existente grupo de recursos.|Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições).|
   | **Nome da instância gerida** | Qualquer nome válido.|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   | **Região** |A região em que pretende criar o SQL Managed Instance.|Para obter informações sobre [regiões, consulte as regiões de Azure.](https://azure.microsoft.com/regions/)|
   | **Início de sessão de administração da instância gerida** | Qualquer nome de utilizador válido. | Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). Não use "serveradmin" porque é uma função reservada ao nível do servidor.|
   | **Palavra-passe** | Qualquer senha válida.| A palavra-passe tem de ter, pelo menos, 16 carateres e cumprir os [requisitos de complexidade definidos](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- **Selecione Configure Managed Instance** para dimensionar os recursos de computação e armazenamento e rever os níveis de preços. Utilize os controlos de deslize ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais. Quando terminar, selecione **Aplicar** para salvar a sua seleção. 

   ![Formulário de instância gerido](./media/instance-create-quickstart/mi-create-tab-configure-performance.png)

- Para rever as suas escolhas antes de criar uma Instância Gerida SQL, pode selecionar **Rever + criar**. Ou, configure as opções de networking selecionando **Next: Networking**.

### <a name="networking-tab"></a>Separador de rede

- Preencha informações opcionais no separador **Networking.** Se omite esta informação, o portal aplicará definições predefinidas.

   ![Separador "Networking" para a criação de uma instância gerida sQL](./media/instance-create-quickstart/mi-create-tab-networking.png)

   Utilize a tabela abaixo como referência para obter informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Rede virtual** | Selecione **criar uma nova rede virtual** ou uma rede virtual válida e sub-rede.| Se uma rede ou sub-rede não estiver disponível, deve ser [modificada para satisfazer os requisitos](vnet-existing-add-subnet.md) de rede antes de selecioná-la como um alvo para a nova Instância Gerida SQL. Para obter informações sobre os requisitos para configurar o ambiente de rede para uma instância gerida pela SQL, consulte configurar uma rede virtual para uma instância gerida pela [SQL](connectivity-architecture-overview.md). |
   | **Tipo de ligação** | Escolha entre um proxy e um tipo de ligação redireccional.|Para obter mais informações sobre tipos de ligação, consulte o tipo de [ligação Azure SQL Managed Instance](../database/connectivity-architecture.md#connection-policy).|
   | **Ponto final público**  | Selecione **Ativar**. | Para que um SQL Managed Instance seja acessível através do ponto final de dados públicos, você precisa ativar esta opção. | 
   | **Permitir o acesso** a partir (se o **ponto final público** estiver ativado) | Selecione uma das opções.   |A experiência do portal permite configurar um grupo de segurança com um ponto final público. </br> </br> Com base no seu cenário, selecione uma das seguintes opções: </br> <ul> <li>**Serviços Azure**: Recomendamos esta opção quando estiver a ligar-se do Power BI ou de outro serviço multiarrendatário. </li> <li> **Internet**: Utilize para efeitos de teste quando pretender girar rapidamente uma instância gerida pela SQL. Não o recomendamos para ambientes de produção. </li> <li> **Sem acesso**: Esta opção cria uma regra de segurança **Negar.** Modifique esta regra para tornar um SQL Managed Instance acessível através de um ponto final público. </li> </ul> </br> Para obter mais informações sobre segurança de pontos finais públicos, consulte A utilização de Um Ponto De Utilização [Gerido Azure SQL de forma segura com um ponto final público](public-endpoint-overview.md).|

- Selecione **Review + crie** para rever as suas escolhas antes de criar uma Instância Gerida SQL. Ou, configure configurar definições mais personalizadas selecionando **Next: Definições adicionais**.

### <a name="additional-settings"></a>Definições adicionais

- Preencha informações opcionais no separador **definições adicionais.** Se omite esta informação, o portal aplicará definições predefinidas.

   ![Separador "Definições adicionais" para a criação de uma instância gerida pelo SQL](./media/instance-create-quickstart/mi-create-tab-additional-settings.png)

   Utilize a tabela abaixo como referência para obter informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Agrupamento** | Escolha a colagem que pretende utilizar para a sua Instância Gerida SQL. Se migrar bases de dados do SQL Server, verifique a colagem de origem utilizando `SELECT SERVERPROPERTY(N'Collation')` e use esse valor.| Para obter informações sobre colagens, consulte [Definir ou alterar a colagem do servidor](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Time zone** (Fuso horário) | Selecione o fuso horário que a sua Instância Gerida SQL observará.|Para mais informações, consulte [fusos horários.](timezones-overview.md)|
   | **Usar como failover secundário** | Selecione **Sim**. | Ative esta opção de utilizar o SQL Managed Instance como um grupo secundário de failover.|
   | **Instância Gerida Primária SQL** (se **a utilização como secundária failover** for definida para **Sim)** | Escolha uma instância sql gerida primária existente que será unida na mesma zona DNS com a Instância Gerida SQL que está a criar. | Este passo permitirá a configuração pós-criação do grupo failover. Para mais informações, consulte [Tutorial: Adicione uma Instância Gerida SQL de Base de Dados SQL a um grupo](failover-group-add-instance-tutorial.md)de failover .|

## <a name="review--create"></a>Rever + criar

1. Selecione **Review + crie** o separador para rever as suas escolhas antes de criar a Instância Gerida SQL.

   ![Separador para revisão e criação de uma Instância Gerida SQL](./media/instance-create-quickstart/mi-create-tab-review-create.png)

1. Selecione **Criar** para começar a fornecer a Instância Gerida SQL.

> [!IMPORTANT]
> A implantação de uma Instância Gerida SQL é uma operação de longa duração. A implantação da primeira instância na sub-rede normalmente demora muito mais tempo do que a implantação numa sub-rede com instâncias geridas sQL existentes. Para tempos médios de provisionamento, consulte as operações de gestão de [instâncias geridas pela SQL](sql-managed-instance-paas-overview.md#management-operations).

## <a name="monitor-deployment-progress"></a>Monitorizar o progresso da implantação

1. Selecione o ícone **Notificações** para ver o estado da implementação.

   ![Progresso de implantação de uma implantação de instância gerida sQL](./media/instance-create-quickstart/mi-create-deployment-in-progress.png)

1. Selecione **a implementação em curso** na notificação para abrir a janela SQL Managed Instance e monitorizar melhor o progresso da implementação. 

> [!TIP]
> Se fechou o seu navegador web ou se afastou do ecrã de progresso de implementação, siga estes passos para localizar o ecrã de progresso de implementação:
> 1. No portal Azure, abra o grupo de recursos (no separador **Basics)** para o qual está a implementar uma Instância Gerida SQL.
> 2. Selecione **Implementações**.
> 3. Selecione a operação de implantação de instâncias geridas sQL em andamento.

> [!IMPORTANT]
> Para conseguir o estatuto de criação de Instância Gerida sQL, precisa de ter **lido permissões** sobre o grupo de recursos. Se não tiver esta permissão ou a revogar enquanto a Instância Gerida sQL estiver em processo de criação, isso pode fazer com que a Instância Gerida sQL não seja visível na lista de implementações do grupo de recursos.
>

## <a name="view-resources-created"></a>Ver recursos criados

Após a implantação bem sucedida da SQL Managed Instance, para visualizar os recursos criados:

1. Abra o grupo de recursos para a sua Instância Gerida SQL. 

   ![Recursos de instância gerida sQL](./media/instance-create-quickstart/resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Ver e afinar definições de rede

Para afinar opcionalmente as definições de rede, inspecione o seguinte:

1. Selecione a tabela de rotas para rever a rota definida pelo utilizador (UDR) que foi criada para si.

   ![Tabela de rota](./media/instance-create-quickstart/route-table.png)

2. Na tabela de rotas, reveja as entradas para encaminhar o tráfego de e para dentro da rede virtual da SQL Managed Instance. Se criar ou configurar manualmente a tabela de rotas, certifique-se de criar estas entradas na tabela de rotas SQL Managed Instance.

   ![Entrada para uma subnet SQL Managed Instance para local](./media/instance-create-quickstart/udr.png)

3. Volte ao grupo de recursos e selecione o grupo de segurança da rede.

   ![Grupo de segurança de rede](./media/instance-create-quickstart/network-security-group.png)

4. Reveja as regras de segurança de entrada e saída. 

   ![Regras de segurança](./media/instance-create-quickstart/security-rules.png)

> [!IMPORTANT]
> Se configurar um ponto final público para a sua Instância Gerida SQL, precisa de abrir portas para permitir o tráfego de rede que permite ligações à SQL Managed Instance a partir da Internet pública, consulte [configurar um ponto final público para a SQL Managed Instance](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) para obter mais informações.
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Recuperar detalhes de ligação à Instância Gerida sQL

Para ligar à Instância Gerida sQL, siga estes passos para recuperar o nome do anfitrião e o nome de domínio totalmente qualificado (FQDN):

1. Volte ao grupo de recursos e selecione a sua Instância Gerida SQL.

   ![Instância gerida sQL no grupo de recursos](./media/instance-create-quickstart/managed-instance.png)

2. No separador **Overview,** localize a propriedade **do Anfitrião.** Copie o nome do anfitrião para o SQL Managed Instance para ser utilizado no próximo arranque rápido.

   ![Nome do anfitrião](./media/instance-create-quickstart/host-name.png)

   O valor copiado representa um nome de domínio totalmente qualificado (FQDN) que pode ser usado para ligar à Instância Gerida sQL. É semelhante ao seguinte exemplo de endereço: *your_host_name.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Próximos passos

Para aprender como se conectar a uma Instância Gerida SQL:
- Para uma visão geral das opções de ligação para aplicações, consulte Connect as suas aplicações a uma instância gerida por [SQL](connect-application-instance.md).
- Para um arranque rápido que mostre como se conectar a uma Instância Gerida SQL a partir de uma máquina virtual Azure, consulte [Configure uma ligação virtual azure](connect-vm-instance-configure.md).
- Para um arranque rápido que mostre como ligar a um SQL Managed Instance a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [Configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md).

Para restaurar uma base de dados existente do SQL Server a partir do local para uma instância gerida pela SQL: 
- Utilize o Serviço de Migração de Bases de [Dados Azure para migração](../../dms/tutorial-sql-server-to-managed-instance.md) para restaurar a partir de um ficheiro de backup da base de dados. 
- Utilize o [comando RESTABELECIMENTO T-SQL](restore-sample-database-quickstart.md) para restaurar a partir de um ficheiro de backup da base de dados.

Para uma monitorização avançada do desempenho da base de dados SQL Managed Instance com inteligência incorporada de resolução de problemas, consulte [monitor Azure SQL Managed Instance utilizando o Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
