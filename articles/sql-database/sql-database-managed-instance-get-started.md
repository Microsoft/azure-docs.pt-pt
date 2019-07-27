---
title: 'Portal do Azure: Criar uma instância gerenciada do banco de dados SQL | Microsoft Docs'
description: Crie uma instância gerenciada do banco de dados SQL, um ambiente de rede e uma VM cliente para acesso.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 646a602d7e6c47454f039a5f4f1981a60b56fa56
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567526"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Início rápido: Criar uma instância gerenciada do banco de dados SQL do Azure

Este guia de início rápido explica como criar uma [instância gerenciada](sql-database-managed-instance.md) do banco de dados SQL do Azure no portal do Azure.

> [!IMPORTANT]
> Para obter limitações, consulte [regiões com suporte](sql-database-managed-instance-resource-limits.md#supported-regions) e [tipos de assinatura com suporte](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Criar uma instância gerida

As etapas a seguir mostram como criar uma instância gerenciada.

1. Selecione **criar um recurso** no canto superior esquerdo do portal do Azure.
2. Localize a **instância gerenciada**e, em seguida, selecione **Azure SQL instância gerenciada**.
3. Selecione **Criar**.

   ![Criar uma instância gerida](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Preencha o formulário **instância gerenciada do SQL** com as informações solicitadas usando as informações na tabela a seguir.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Subscrição** | Sua assinatura. | Uma assinatura que lhe dá permissão para criar novos recursos. |
   |**Nome da instância gerida**|Qualquer nome válido.|Para nomes válidos, veja [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Início de sessão de administração da instância gerida**|Qualquer nome de usuário válido.|Para nomes válidos, veja [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Não use "ServerAdmin" porque essa é uma função de nível de servidor reservada.|
   |**Palavra-passe**|Qualquer senha válida.|A palavra-passe tem de ter, pelo menos, 16 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Fuso horário**|O fuso horário a ser observado por sua instância gerenciada.|Para obter mais informações, consulte [fusos horários](sql-database-managed-instance-timezone.md).|
   |**Agrupamento**|O agrupamento que você deseja usar para a instância gerenciada.|Se você migrar bancos de dados do SQL Server, verifique o agrupamento de origem `SELECT SERVERPROPERTY(N'Collation')` usando e use esse valor. Para obter informações sobre agrupamentos, consulte [definir ou alterar o agrupamento do servidor](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Location**|O local no qual você deseja criar a instância gerenciada.|Para obter informações sobre regiões, consulte [regiões do Azure](https://azure.microsoft.com/regions/).|
   |**Rede virtual**|Selecione **criar nova rede virtual** ou uma rede virtual e uma sub-rede válidas.| Se uma rede ou sub-rede não estiver disponível, ela deverá ser [modificada para atender aos requisitos de rede](sql-database-managed-instance-configure-vnet-subnet.md) antes de você selecioná-la como um destino para a nova instância gerenciada. Para obter informações sobre os requisitos para configurar o ambiente de rede para uma instância gerenciada, consulte [Configurar uma rede virtual para uma instância gerenciada](sql-database-managed-instance-connectivity-architecture.md). |
   |**Habilitar ponto de extremidade público**   |Marque esta opção para habilitar o ponto de extremidade público   |Para que a instância gerenciada seja acessível por meio do ponto de extremidade de dados públicos, habilite o **ponto de extremidade público** deve ser verificado.| 
   |**Permitir acesso de**   |Selecione uma das opções: <ul> <li>**Serviços do Azure**</li> <li>**Internet**</li> <li>**Sem acesso**</li></ul>   |A experiência do portal permite configurar o grupo de segurança com o ponto de extremidade público. </br> </br> Com base em seu cenário, selecione uma das seguintes opções: </br> <ul> <li>Serviços do Azure – recomendado ao se conectar de Power BI ou de outro serviço de vários locatários. </li> <li> Internet-use para fins de teste quando desejar criar rapidamente uma instância gerenciada. Não é recomendável para uso em ambientes de produção. </li> <li> Sem acesso-essa opção cria uma regra de segurança de negação. Você precisará modificar essa regra para tornar a instância gerenciada acessível por meio do ponto de extremidade público. </li> </ul> </br> Para obter mais informações sobre segurança de ponto de extremidade público, consulte [usando a instância gerenciada do banco de dados SQL do Azure com segurança com o ponto de extremidade](sql-database-managed-instance-public-endpoint-securely.md)|
   |**Tipo de conexão**|Escolha entre um proxy e um tipo de conexão de redirecionamento.|Para obter mais informações sobre tipos de conexão, consulte [política de conexão do banco de dados SQL do Azure](sql-database-connectivity-architecture.md#connection-policy).|
   |**Grupo de recursos**|Um grupo de recursos novo ou existente.|Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições).|

   ![Formulário de instância gerenciada](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Para usar a instância gerenciada como um grupo de failover de instância secundário, selecione o check-out e especifique a instância gerenciada do DnsAzurePartner. Esse recurso está em visualização e não é mostrado na captura de tela a seguir.
6. Selecione o **tipo de preço** para dimensionar os recursos de computação e armazenamento e revisar as opções de tipo de preço. O escalão de preço Fins Gerais com 32 GB de memória e 16 núcleos virtuais é o valor predefinido.
7. Utilize os controlos de deslize ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais.
8. Quando tiver terminado, selecione **aplicar** para salvar sua seleção. 
9. Selecione **criar** para implantar a instância gerenciada.
10. Selecione o ícone **notificações** para exibir o status da implantação.

    ![Progresso da implantação da instância gerenciada](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Selecione **implantação em andamento** para abrir a janela de instância gerenciada para monitorar ainda mais o progresso da implantação. 

> [!IMPORTANT]
> Para a primeira instância em uma sub-rede, o tempo de implantação normalmente é muito maior do que nas instâncias subsequentes. Não cancele a operação de implantação porque ela dura mais do que o esperado.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Examine os recursos e recupere seu nome de servidor totalmente qualificado

Depois que a implantação for bem-sucedida, examine os recursos que foram criados e recupere o nome do servidor totalmente qualificado para uso em guias de início rápido posteriores.

1. Abra o grupo de recursos para sua instância gerenciada. Exiba seus recursos que foram criados para você no guia de início rápido [criar uma instância gerenciada](#create-a-managed-instance) .

   ![Recursos de instância geridos](./media/sql-database-managed-instance-get-started/resources.png)

2. Selecione a tabela de rotas para examinar a tabela UDR (rota definida pelo usuário) que foi criada para você.

   ![Tabela de rotas](./media/sql-database-managed-instance-get-started/route-table.png)

3. Na tabela de rotas, examine as entradas para rotear o tráfego de e dentro da rede virtual da instância gerenciada. Se você criar ou configurar sua tabela de rotas manualmente, deverá ter certeza de criar essas entradas na tabela de rotas.

   ![Entrada para uma sub-rede de instância gerenciada para local](./media/sql-database-managed-instance-get-started/udr.png)

4. Retorne ao grupo de recursos e selecione o grupo de segurança de rede para examinar as regras de segurança.

   ![Grupo de segurança de rede](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Examine as regras de segurança de entrada e saída. Se você tiver configurado pontos de extremidade públicos para sua instância gerenciada, consulte o artigo configurar ponto de [extremidades público](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) para obter mais informações.

   ![Regras de segurança](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Retorne ao grupo de recursos e selecione sua instância gerenciada.

   ![Instância gerida](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Na guia **visão geral** , localize a propriedade **host** . Copie o endereço do host totalmente qualificado para a instância gerenciada para uso no próximo início rápido.

   ![Nome do anfitrião](./media/sql-database-managed-instance-get-started/host-name.png)

   O nome é semelhante a **your_machine_name. a1b2c3d4e5f6. Database. Windows. net**.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como se conectar a uma instância gerenciada:
  - Para obter uma visão geral das opções de conexão para aplicativos, consulte [conectar seus aplicativos a uma instância gerenciada](sql-database-managed-instance-connect-app.md).
  - Para obter um início rápido que mostra como se conectar a uma instância gerenciada de uma máquina virtual do Azure, consulte [Configurar uma conexão de máquina virtual do Azure](sql-database-managed-instance-configure-vm.md).
  - Para obter um guia de início rápido que mostra como se conectar a uma instância gerenciada de um computador cliente local usando uma conexão ponto a site, consulte [Configurar uma conexão ponto a site](sql-database-managed-instance-configure-p2s.md).
- Para restaurar um banco de dados de SQL Server existente do local para uma instância gerenciada: 
    - Use o [serviço de migração de banco de dados do Azure (DMS) para migração](../dms/tutorial-sql-server-to-managed-instance.md) para restaurar de um arquivo de backup de banco de dados. 
    - Use o [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) para restaurar de um arquivo de backup de banco de dados.
- Para monitoramento avançado do desempenho do banco de dados de instância gerenciada com inteligência de solução de problemas interna, consulte [monitorar o banco de dados SQL do Azure usando análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).
