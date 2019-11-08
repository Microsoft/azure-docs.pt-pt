---
title: 'Portal do Azure: criar uma instância gerenciada'
description: Crie uma instância gerenciada do banco de dados SQL, um ambiente de rede e uma VM cliente para acesso.
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
ms.openlocfilehash: 578ab250d0759aa7216327124417019f2e5f1fc7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825470"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Início rápido: criar uma instância gerenciada do banco de dados SQL do Azure

Este guia de início rápido explica como criar uma [instância gerenciada](sql-database-managed-instance.md) do banco de dados SQL do Azure no portal do Azure.

> [!IMPORTANT]
> Para obter limitações, consulte [regiões com suporte](sql-database-managed-instance-resource-limits.md#supported-regions) e [tipos de assinatura com suporte](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Criar uma instância gerida

As etapas a seguir mostram como criar uma instância gerenciada:

1. Selecione **SQL do Azure** no menu à esquerda de portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, insira **SQL do Azure** na caixa de pesquisa.
2. Selecione **+ Adicionar** para abrir a página **selecionar opção de implantação do SQL** . Você pode exibir informações adicionais sobre uma instância gerenciada do banco de dados SQL do Azure selecionando **Mostrar detalhes** no bloco **instâncias gerenciadas** .
3. Selecione **Criar**.

   ![Criar uma instância gerida](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Use as guias no formulário **criar instância gerenciada do banco de dados SQL do Azure** provisionamento para adicionar informações necessárias e opcionais. As seções a seguir descrevem essas guias.

### <a name="basics"></a>Noções básicas

- Preencha as informações obrigatórias necessárias na guia **noções básicas** . Este é um conjunto mínimo de informações necessárias para provisionar uma instância gerenciada.

   ![Guia "Noções básicas" para criar uma instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Use a tabela abaixo como referência para as informações necessárias nesta guia.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Subscrição** | Sua assinatura. | Uma assinatura que lhe dá permissão para criar novos recursos. |
   | **Grupo de recursos** | Um grupo de recursos novo ou existente.|Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) (Atribuição de nomes de regras e restrições).|
   | **Nome da instância gerida** | Qualquer nome válido.|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).|
   | **Região** |A região na qual você deseja criar a instância gerenciada.|Para obter informações sobre regiões, consulte [regiões do Azure](https://azure.microsoft.com/regions/).|
   | **Início de sessão de administração da instância gerida** | Qualquer nome de usuário válido. | Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging). Não use "ServerAdmin" porque essa é uma função de nível de servidor reservada.|
   | **Palavra-passe** | Qualquer senha válida.| A palavra-passe tem de ter, pelo menos, 16 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Selecione **configurar instância gerenciada** para dimensionar os recursos de computação e armazenamento e para examinar os tipos de preço. Utilize os controlos de deslize ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais. Quando tiver terminado, selecione **aplicar** para salvar sua seleção. 

   ![Formulário de instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Para revisar suas escolhas antes de criar uma instância gerenciada, você pode selecionar **revisar + criar**. Ou configure as opções de rede selecionando **Avançar: rede**.

### <a name="networking"></a>Redes

- Preencha as informações opcionais na guia **rede** . Se você omitir essas informações, o portal aplicará as configurações padrão.

   ![Guia "rede" para criar uma instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Use a tabela abaixo como referência para as informações necessárias nesta guia.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Rede virtual** | Selecione **criar nova rede virtual** ou uma rede virtual e uma sub-rede válidas.| Se uma rede ou sub-rede não estiver disponível, ela deverá ser [modificada para atender aos requisitos de rede](sql-database-managed-instance-configure-vnet-subnet.md) antes de você selecioná-la como um destino para a nova instância gerenciada. Para obter informações sobre os requisitos para configurar o ambiente de rede para uma instância gerenciada, consulte [Configurar uma rede virtual para uma instância gerenciada](sql-database-managed-instance-connectivity-architecture.md). |
   | **Tipo de conexão** | Escolha entre um proxy e um tipo de conexão de redirecionamento.|Para obter mais informações sobre tipos de conexão, consulte [política de conexão do banco de dados SQL do Azure](sql-database-connectivity-architecture.md#connection-policy).|
   | **Ponto de extremidade público**  | Selecione **Ativar**. | Para que uma instância gerenciada possa ser acessada por meio do ponto de extremidade de dados públicos, você precisa habilitar essa opção. | 
   | **Permitir acesso de** (se o **ponto de extremidade público** estiver habilitado) | Selecione uma das opções.   |A experiência do portal permite configurar um grupo de segurança com um ponto de extremidade público. </br> </br> Com base em seu cenário, selecione uma das seguintes opções: </br> <ul> <li>**Serviços do Azure**: recomendamos essa opção quando você estiver se conectando de Power bi ou outro serviço multilocatário. </li> <li> **Internet**: Use para fins de teste quando desejar acelerar rapidamente uma instância gerenciada. Não é recomendável para ambientes de produção. </li> <li> **Sem acesso**: essa opção cria uma regra de segurança de **negação** . Modifique esta regra para tornar uma instância gerenciada acessível por meio de um ponto de extremidade público. </li> </ul> </br> Para obter mais informações sobre segurança de ponto de extremidade público, consulte [usando uma instância gerenciada do banco de dados SQL do Azure com segurança com um ponto de extremidade público](sql-database-managed-instance-public-endpoint-securely.md).|

- Selecione **revisão + criar** para revisar suas escolhas antes de criar uma instância gerenciada. Ou defina mais configurações personalizadas selecionando **Avançar: configurações adicionais**.

### <a name="additional-settings"></a>Configurações adicionais

- Preencha as informações opcionais na guia **configurações adicionais** . Se você omitir essas informações, o portal aplicará as configurações padrão.

   ![Guia "configurações adicionais" para criar uma instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Use a tabela abaixo como referência para as informações necessárias nesta guia.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Agrupamento** | Escolha o agrupamento que você deseja usar para a instância gerenciada. Se você migrar bancos de dados do SQL Server, verifique o agrupamento de origem usando `SELECT SERVERPROPERTY(N'Collation')` e use esse valor.| Para obter informações sobre agrupamentos, consulte [definir ou alterar o agrupamento do servidor](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Time zone** (Fuso horário) | Selecione o fuso horário que sua instância gerenciada observará.|Para obter mais informações, consulte [fusos horários](sql-database-managed-instance-timezone.md).|
   | **Usar como failover secundário** | Selecione **Sim**. | Habilite esta opção para usar a instância gerenciada como um grupo de failover secundário.|
   | **Instância gerenciada primária** (se **usar as secundárias de failover** estiver definido como **Sim**) | Escolha uma instância gerenciada primária existente que será unida na mesma zona DNS com a instância gerenciada que você está criando. | Esta etapa habilitará a configuração após a criação do grupo de failover. Para obter mais informações, consulte [tutorial: adicionar uma instância gerenciada do banco de dados SQL a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Examinar + criar

5. Selecione a guia **revisar + criar** para revisar suas escolhas antes de criar a instância gerenciada.

   ![Guia para revisar e criar uma instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Selecione **criar** para iniciar o provisionamento da instância gerenciada.

> [!IMPORTANT]
> A implantação de uma instância gerenciada é uma operação de execução longa. A implantação da primeira instância na sub-rede normalmente demora muito mais do que a implantação em uma sub-rede com instâncias gerenciadas existentes. Para tempos de provisionamento médios, consulte [operações de gerenciamento de instância gerenciada](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Monitorar o progresso da implantação

7. Selecione o ícone **notificações** para exibir o status da implantação.

   ![Progresso da implantação de uma implantação de instância gerenciada](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Selecione **implantação em andamento** na notificação para abrir a janela de instância gerenciada e monitore ainda mais o progresso da implantação. 

> [!TIP]
> Se você fechou o navegador da Web ou saiu da tela de progresso da implantação, siga estas etapas para localizar novamente a tela de progresso da implantação:
> 1. No portal do Azure, abra o grupo de recursos (na guia **noções básicas** ) para o qual você está implantando uma instância gerenciada.
> 2. Selecione **implantações**.
> 3. Selecione a operação de implantação de instância gerenciada em andamento.

## <a name="post-deployment-operations"></a>Operações pós-implantação

Para examinar os recursos criados, ajuste as configurações de rede e recupere os detalhes da conexão do host (FQDN) siga as etapas descritas nesta seção.

### <a name="view-resources-created"></a>Exibir recursos criados

Após a implantação bem-sucedida da instância gerenciada, para exibir os recursos criados:

1. Abra o grupo de recursos para sua instância gerenciada. Exiba seus recursos que foram criados para você no guia de início rápido [criar uma instância gerenciada](#create-a-managed-instance) .

   ![Recursos de instância gerenciada](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Exibir e ajustar as configurações de rede

Para, opcionalmente, ajustar as configurações de rede, inspecione o seguinte:

1. Selecione a tabela de rotas para examinar a UDR (rota definida pelo usuário) que foi criada para você.

   ![Tabela de rota](./media/sql-database-managed-instance-get-started/route-table.png)

2. Na tabela de rotas, examine as entradas para rotear o tráfego de e dentro da rede virtual da instância gerenciada. Se você criar ou configurar sua tabela de rotas manualmente, certifique-se de criar essas entradas na tabela de rotas da instância gerenciada.

   ![Entrada para uma sub-rede de instância gerenciada para local](./media/sql-database-managed-instance-get-started/udr.png)

3. Retorne ao grupo de recursos e selecione o grupo de segurança de rede.

   ![Grupo de segurança de rede](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Examine as regras de segurança de entrada e saída. 

   ![Regras de segurança](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Se você tiver configurado o ponto de extremidade público para sua instância gerenciada, precisará abrir portas para permitir o tráfego de rede permitindo conexões com a instância gerenciada da Internet pública, consulte [configurar um ponto de extremidade público para instância gerenciada](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) para obter mais informações .
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Recuperar detalhes da conexão para a instância gerenciada

Para se conectar à instância gerenciada, siga estas etapas para recuperar o nome do host e o FQDN (nome de domínio totalmente qualificado):

1. Retorne ao grupo de recursos e selecione sua instância gerenciada.

   ![Instância gerenciada no grupo de recursos](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Na guia **visão geral** , localize a propriedade **host** . Copie o nome do host para a instância gerenciada para uso no próximo início rápido.

   ![Nome do host](./media/sql-database-managed-instance-get-started/host-name.png)

   O valor copiado representa um FQDN (nome de domínio totalmente qualificado) que pode ser usado para se conectar à instância gerenciada. Ele é semelhante ao seguinte exemplo de endereço: *your_host_name. a1b2c3d4e5f6. Database. Windows. net*.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como se conectar a uma instância gerenciada:
- Para obter uma visão geral das opções de conexão para aplicativos, consulte [conectar seus aplicativos a uma instância gerenciada](sql-database-managed-instance-connect-app.md).
- Para obter um início rápido que mostra como se conectar a uma instância gerenciada de uma máquina virtual do Azure, consulte [Configurar uma conexão de máquina virtual do Azure](sql-database-managed-instance-configure-vm.md).
- Para obter um guia de início rápido que mostra como se conectar a uma instância gerenciada de um computador cliente local usando uma conexão ponto a site, consulte [Configurar uma conexão ponto a site](sql-database-managed-instance-configure-p2s.md).

Para restaurar um banco de dados de SQL Server existente do local para uma instância gerenciada: 
- Use o [serviço de migração de banco de dados do Azure para migração](../dms/tutorial-sql-server-to-managed-instance.md) para restaurar de um arquivo de backup de banco de dados. 
- Use o [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) para restaurar de um arquivo de backup de banco de dados.

Para monitoramento avançado do desempenho do banco de dados de instância gerenciada com inteligência de solução de problemas interna, consulte [monitorar o banco de dados SQL do Azure usando análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).
