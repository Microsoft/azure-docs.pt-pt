---
title: 'Quickstart: Criar uma instância gerida de SQL Gestd Instance (portal)'
description: Crie um exemplo gerido, ambiente de rede e VM cliente para acesso usando o portal Azure neste arranque rápido.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 09/26/2019
ms.openlocfilehash: bf606af0425c04873dadcf47932c806a1099942a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788711"
---
# <a name="quickstart-create-a-managed-instance-of-sql-managed-instance"></a>Quickstart: Criar uma instância gerida de SQL Gestão De Instância
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este quickstart ensina-o a criar uma instância gerida de [Azure SQL No](sql-managed-instance-paas-overview.md) portal Azure.

> [!IMPORTANT]
> Para limitações, consulte [regiões apoiadas](resource-limits.md#supported-regions) e [tipos de assinaturas suportados.](resource-limits.md#supported-subscription-types)

## <a name="create-a-managed-instance"></a>Criar uma instância gerida

Para criar uma instância gerida, siga estes passos: 

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Selecione **Azure SQL** no menu esquerdo do portal Azure. Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços** e introduza o **Azure SQL** na caixa de pesquisa.
1. **Selecione +Adicione** para abrir a página **de opção de implementação Select SQL.** Pode ver informações adicionais sobre a Azure SQL Managed Instance selecionando detalhes do **Show** no azulejo **gerido sql.**
1. Selecione **Criar** .

   ![Criar uma instância gerida](./media/instance-create-quickstart/create-managed-instance.png)

4. Utilize os separadores no formulário de provisionamento **de instância gerida Create Azure SQL** para adicionar informações necessárias e opcionais. As seguintes secções descrevem estes separadores.

### <a name="basics-tab"></a>Separador básico

- Preencha as informações **obrigatórias requeridas no separador Básicos.** Trata-se de um conjunto mínimo de informações necessárias para a prestação de uma instância gerida.

   ![Separador "Básicos" para criar um caso gerido](./media/instance-create-quickstart/mi-create-tab-basics.png)

   Utilize a tabela abaixo como referência para as informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Subscrição** | A sua assinatura. | Uma subscrição que lhe dá permissão para criar novos recursos. |
   | **Grupo de recursos** | Um grupo de recursos novo ou existente.|Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições).|
   | **Nome da instância gerida** | Qualquer nome válido.|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   | **Região** |A região em que quer criar o caso gerido.|Para obter informações sobre as regiões, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/)|
   | **Início de sessão de administração da instância gerida** | Qualquer nome de utilizador válido. | Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). Não use "serveradmin" porque é um papel reservado ao nível do servidor.|
   | **Palavra-passe** | Qualquer senha válida.| A palavra-passe tem de ter, pelo menos, 16 carateres e cumprir os [requisitos de complexidade definidos](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Selecione **Configurar Instância Gerida** para dimensionar os recursos de computação e armazenamento e rever os níveis de preços. Utilize os controlos de deslize ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais. Quando terminar, **selecione Aplicar** para salvar a sua seleção. 

   ![Formulário de instância gerido](./media/instance-create-quickstart/mi-create-tab-configure-performance.png)

- Para rever as suas escolhas antes de criar uma SqL Managed Instance, pode selecionar **Review + create** . Ou, configurar opções de networking selecionando **Seguinte: Networking** .

### <a name="networking-tab"></a>Separador de rede

- Preencha informações opcionais no **separador Networking.** Se omitir esta informação, o portal aplicará definições predefinidas.

   ![Separador "Networking" para criar uma instância gerida](./media/instance-create-quickstart/mi-create-tab-networking.png)

   Utilize a tabela abaixo como referência para as informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Rede virtual** | Selecione criar **uma nova rede virtual** ou uma rede virtual válida e uma sub-rede.| Se uma rede ou sub-rede não estiver disponível, deve ser [modificada para satisfazer os requisitos de rede](vnet-existing-add-subnet.md) antes de a selecionar como alvo para a nova instância gerida. Para obter informações sobre os requisitos para configurar o ambiente de rede para a SQL Managed Instance, consulte [configurar uma rede virtual para sql Managed Instance](connectivity-architecture-overview.md). |
   | **Tipo de ligação** | Escolha entre um proxy e um tipo de ligação de redirecionamento.|Para obter mais informações sobre tipos de ligação, consulte [o tipo de ligação Azure SQL Managed Instance](../database/connectivity-architecture.md#connection-policy).|
   | **Ponto final público**  | Selecione **Ativar** . | Para que um caso gerido seja acessível através do ponto final de dados públicos, é necessário ativar esta opção. | 
   | **Permitir o acesso a partir (se** **o ponto final público** estiver ativado) | Selecione uma das opções.   |A experiência do portal permite configurar um grupo de segurança com um ponto final público. </br> </br> Com base no seu cenário, selecione uma das seguintes opções: </br> <ul> <li>**Serviços Azure** : Recomendamos esta opção quando estiver a ligar-se a partir do Power BI ou de outro serviço multitenant. </li> <li> **Internet** : Utilize para fins de teste quando pretender rodar rapidamente uma instância gerida. Não recomendamos para ambientes de produção. </li> <li> **Sem acesso** : Esta opção cria uma regra de segurança **Deny.** Modifique esta regra para tornar uma instância gerida acessível através de um ponto final público. </li> </ul> </br> Para obter mais informações sobre a segurança do ponto final público, consulte [a Utilização de Azure SQL Managed Instance de forma segura com um ponto final público](public-endpoint-overview.md).|

- Selecione **Review + crie** para rever as suas escolhas antes de criar uma instância gerida. Ou, configurar mais definições personalizadas selecionando **Seguinte: Definições adicionais** .

### <a name="additional-settings"></a>Definições adicionais

- Preencha informações opcionais no **separador Definições Adicionais.** Se omitir esta informação, o portal aplicará definições predefinidas.

   ![Separador "Definições adicionais" para criar um caso gerido](./media/instance-create-quickstart/mi-create-tab-additional-settings.png)

   Utilize a tabela abaixo como referência para as informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Agrupamento** | Escolha a colagem que pretende utilizar para a sua instância gerida. Se migrar bases de dados do SQL Server, verifique a colagem de origem utilizando `SELECT SERVERPROPERTY(N'Collation')` e utilize esse valor.| Para obter informações sobre colagens, consulte [set ou altere a colagem do servidor](/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Fuso horário** | Selecione o fuso horário que a instância gerida observará.|Para mais informações, consulte [fusos horários.](timezones-overview.md)|
   | **Uso como failover secundário** | Selecione **Sim** . | Ative esta opção para utilizar a instância gerida como um grupo de failover secundário.|
   | **Primeira SQL Caso gerido** (se **a utilização como secundário de failover** está definida para **Sim** ) | Escolha um caso gerido primário existente que será acompanhado na mesma zona de DNS com a instância gerida que está a criar. | Este passo permitirá a configuração pós-criação do grupo de failover. Para obter mais informações, consulte [Tutorial: Adicione uma instância gerida a um grupo de failover](failover-group-add-instance-tutorial.md).|

## <a name="review--create"></a>Rever + criar

1. Selecione **Rever + criar** separador para rever as suas escolhas antes de criar uma instância gerida.

   ![Separador para revisão e criação de um caso gerido](./media/instance-create-quickstart/mi-create-tab-review-create.png)

1. Selecione **Criar** para começar a avisão da instância gerida.

> [!IMPORTANT]
> Implementar um caso gerido é uma operação de longa duração. A implantação da primeira instância na sub-rede normalmente demora muito mais tempo do que a implantação numa sub-rede com instâncias geridas existentes. Para tempos médios de provisionamento, consulte [as operações de gestão de instância gerida sql](sql-managed-instance-paas-overview.md#management-operations).

## <a name="monitor-deployment-progress"></a>Monitorizar o progresso da implantação

1. Selecione o ícone **'Notificações'** para visualizar o estado da implementação.

   ![Progresso da implantação de uma implantação de instância gerida sql](./media/instance-create-quickstart/mi-create-deployment-in-progress.png)

1. Selecione **a implementação em curso** na notificação para abrir a janela SQL Managed Instance e monitorizar ainda mais o progresso da implementação. 

> [!TIP]
> Se fechou o seu navegador web ou se afastou do ecrã de progresso da implementação, siga estes passos para localizar o ecrã de progresso da implementação:
> 1. No portal Azure, abra o grupo de recursos (no **separador Básicos)** ao qual está a implantar a SQL Managed Instance.
> 2. Selecione **Implementações** .
> 3. Selecione a operação de implantação de instância gerida SQL em curso.

> [!IMPORTANT]
> - A criação da SQL Managed Instance é uma operação de longa duração que pode demorar algumas horas de cada vez, dependendo de circunstâncias específicas. Consulte [a duração das operações de gestão](management-operations-overview.md#duration) para os tempos típicos de criação.
> - O início da criação de exemplos geridos pela SQL pode ser adiado nos casos em que existam outras operações de impacto, tais como operações de restauro ou dimensionamento de longa duração noutras instâncias geridas na mesma sub-rede. Para saber mais, consulte [as operações de Gestão de impacto transversal.](management-operations-overview.md#management-operations-cross-impact)
> - Para conseguir obter o estado da criação de instâncias **geridas,** é necessário ter permissões de leitura sobre o grupo de recursos. Se não tiver esta permissão ou a revogar enquanto a instância gerida estiver em processo de criação, isto pode fazer com que a SQL Managed Instance não esteja visível na lista de implementações de grupos de recursos.
>

## <a name="view-resources-created"></a>Ver recursos criados

Após a implementação bem sucedida de um caso gerido, para visualizar os recursos criados:

1. Abra o grupo de recursos para a sua instância gerida. 

   ![Recursos de instância gerida sql](./media/instance-create-quickstart/resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Ver e afinar definições de rede

Para afinar opcionalmente as definições de rede, inspecione o seguinte:

1. Selecione a tabela de rotas para rever a rota definida pelo utilizador (UDR) que foi criada para si.

   ![Tabela de rota](./media/instance-create-quickstart/route-table.png)

2. Na tabela de rotas, reveja as entradas para encaminhar o tráfego de e para dentro da rede virtual SQL Managed Instance. Se criar ou configurar manualmente a sua tabela de rotas, certifique-se de criar estas entradas na tabela de rotas SQL Managed Instance.

   ![Entrada para uma sub-rede SQL Managed Instance para local](./media/instance-create-quickstart/udr.png)

3. Volte ao grupo de recursos e selecione o grupo de segurança da rede.

   ![Grupo de segurança de rede](./media/instance-create-quickstart/network-security-group.png)

4. Reveja as regras de segurança de entrada e saída. 

   ![Regras de segurança](./media/instance-create-quickstart/security-rules.png)

> [!IMPORTANT]
> Se configurar um ponto final público para a SQL Managed Instance, precisa de abrir portas para permitir o tráfego de rede permitindo ligações à SQL Managed Instance a partir da internet pública. Consulte [configurar um ponto final público para SQL Managed Instance](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) para obter mais informações.
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Obter os detalhes de ligação ao SQL Managed Instance

Para ligar à SQL Managed Instance, siga estes passos para recuperar o nome de anfitrião e o nome de domínio totalmente qualificado (FQDN):

1. Volte ao grupo de recursos e selecione a sua instância gerida.

   ![Caso gerido no grupo de recursos](./media/instance-create-quickstart/managed-instance.png)

2. No **separador Vista Geral,** localize a propriedade **Host.** Copie o nome do anfitrião para a instância gerida para utilização no arranque rápido seguinte.

   ![Nome do anfitrião](./media/instance-create-quickstart/host-name.png)

   O valor copiado representa um nome de domínio totalmente qualificado (FQDN) que pode ser usado para ligar a SQL Managed Instance. É semelhante ao seguinte exemplo de endereço: *your_host_name.a1b2c3d4e5f6.database.windows.net* .

## <a name="next-steps"></a>Passos seguintes

Para saber como ligar-se à SQL Managed Instance:
- Para obter uma visão geral das opções de ligação para aplicações, consulte [Conecte as suas aplicações à SQL Managed Instance](connect-application-instance.md).
- Para um arranque rápido que mostre como ligar-se a SQL Managed Instance a partir de uma máquina virtual Azure, consulte [configurar uma ligação virtual Azure](connect-vm-instance-configure.md).
- Para obter um arranque rápido que mostre como ligar-se à SQL Managed Instance a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md).

Para restaurar uma base de dados do SQL Server existente desde as instalações até à SQL Managed Instance: 
- Utilize o [Serviço de Migração da Base de Dados Azure para a migração](../../dms/tutorial-sql-server-to-managed-instance.md) para restaurar a partir de um ficheiro de backup de bases de dados. 
- Utilize o [comando T-SQL RESTORE](restore-sample-database-quickstart.md) para restaurar a partir de um ficheiro de backup de base de dados.

Para uma monitorização avançada do desempenho da base de dados SQL Managed Instance com inteligência incorporada para resolução de problemas, consulte [monitor Azure SQL Managed Instance utilizando Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).