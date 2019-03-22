---
title: 'Portal do Azure: Criar uma instância gerida de SQL | Documentos da Microsoft'
description: Crie uma instância gerida de SQL, o ambiente de rede e a VM de cliente para o acesso.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 6aa8f362f067a4e3e391f435ee849f96abdf752f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997516"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Início rápido: Criar uma instância gerida da base de dados do Azure SQL

Este início rápido explica como criar uma base de dados do SQL do Azure [instância gerida](sql-database-managed-instance.md) no portal do Azure.

> [!IMPORTANT]
> Para limitações, consulte [regiões suportadas](sql-database-managed-instance-resource-limits.md#supported-regions) e [suportados tipos de subscrição](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Criar uma instância gerida

Os passos seguintes mostram como criar uma instância gerida.

1. Selecione **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Localize **instância gerida** e, em seguida, selecione **instância gerida do SQL do Azure**.
3. Selecione **Criar**.

   ![Criar instância gerida](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Preencha os **instância gerida SQL** formulário com as informações pedidas, utilizando as informações na tabela a seguir:

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Subscrição** | A sua subscrição | Uma subscrição em que tenha permissão para criar novos recursos |
   |**Nome da instância gerida**|Qualquer nome válido|Para nomes válidos, consulte [regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Início de sessão de administração da instância gerida**|Qualquer nome de utilizador válido|Para nomes válidos, consulte [regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Não utilize "serveradmin", que é uma função ao nível do servidor reservada.|
   |**Palavra-passe**|Qualquer palavra-passe válida|A palavra-passe tem de ter, pelo menos, 16 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Agrupamento**|O agrupamento que pretende utilizar para a sua instância gerida|Se estiver migrando bancos de dados do SQL Server, verifique o agrupamento de origem com `SELECT SERVERPROPERTY(N'Collation')` e use esse valor. Para obter informações sobre agrupamentos, veja [agrupamentos no nível de servidor](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Localização**|A localização na qual pretende criar a instância gerida|Para obter informações sobre regiões, consulte [regiões do Azure](https://azure.microsoft.com/regions/).|
   |**Rede virtual**|Selecione **criar nova rede virtual** ou uma rede virtual válido e uma sub-rede.| Se não estiver disponível uma rede/sub-rede é deve ser [modificado para satisfazer os requisitos de rede](sql-database-managed-instance-configure-vnet-subnet.md) antes de selecioná-lo como um destino para a nova instância gerida. Para obter informações sobre os requisitos para configurar o ambiente de rede para uma instância gerida, veja [configurar uma VNet para uma instância gerida](sql-database-managed-instance-connectivity-architecture.md). |
   |**Grupo de recursos**|Um grupo de recursos novo ou existente|Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições).|

   ![formulário da instância gerida](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Para utilizar a instância gerida como um grupo de ativação pós-falha de instância secundário, selecione o Check-out e especifique a instância gerida DnsAzurePartner. Esta funcionalidade está em pré-visualização e não é mostrado na captura de ecrã que acompanha este artigo.
6. Selecione **escalão de preço** para dimensionar recursos de computação e armazenamento, bem como para rever as opções de escalão de preço. O escalão de preço Fins Gerais com 32 GB de memória e 16 núcleos virtuais é o valor predefinido.
7. Utilize os controlos de deslize ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais.
8. Quando terminar, escolha **aplicar** para guardar a sua seleção.  
9. Selecione **criar** para implementar a instância gerida.
10. Selecione o **notificações** ícone para ver o estado da implementação.

    ![progresso da implementação da instância gerida](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Selecione **implementação em curso** para abrir a janela instância gerida para monitorizar o progresso da implementação ainda mais.

> [!IMPORTANT]
> Para a primeira instância numa sub-rede, o tempo de implementação é normalmente muito mais do que nas instâncias subsequentes. Não cancele a operação de implementação porque dura mais do que o esperado. Criar a segunda instância gerida na sub-rede demora apenas alguns minutos.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Analisar os recursos e obter o nome do servidor completamente qualificado

Depois da implementação ser concluída com êxito, reveja os recursos criados e obtenha o nome de servidor completamente qualificado para utilização nos guias de início rápido posteriores.

1. Abra o grupo de recursos para a sua instância gerida e ver os respetivos recursos que foram criados por si a [criar uma instância gerida](#create-a-managed-instance) início rápido.

   ![Recursos de instância geridos](./media/sql-database-managed-instance-get-started/resources.png)

2. Selecione a tabela de rotas para rever a rota definida pelo utilizador UDR) tabela que foi criada para.

   ![Tabela de rotas](./media/sql-database-managed-instance-get-started/route-table.png)

3. Na tabela de rotas, reveja as entradas para encaminhar o tráfego de e para dentro da rede virtual de instância gerida. Se estiver a criar ou configurar manualmente a sua tabela de rotas, tem de ser crie estas entradas na tabela de rotas.

   ![Entrada para a sub-rede de MI para local](./media/sql-database-managed-instance-get-started/udr.png)

4. Regresse ao grupo de recursos e selecione o grupo de segurança de rede para rever as regras de segurança.

   ![Network-security-group](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Reveja as regras de segurança de entrada e saída.

   ![Regras de segurança](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Regresse ao grupo de recursos e selecione a sua instância gerida.

   ![Instância gerida](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Sobre o **descrição geral** separador, localize a **anfitrião** propriedade e copie o anfitrião totalmente qualificado de endereços para a instância gerida para utilização no próximo início rápido.

   ![Nome do anfitrião](./media/sql-database-managed-instance-get-started/host-name.png)

   O nome é semelhante à **your_machine_name.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como ligar a uma instância gerida, veja:
  - Para uma descrição geral das opções de ligação para aplicações, consulte [conectar seus aplicativos a uma instância gerida](sql-database-managed-instance-connect-app.md).
  - Para um início rápido que mostra como ligar a uma instância gerida a partir de uma máquina virtual do Azure, consulte [configurar uma ligação de máquina virtual do Azure](sql-database-managed-instance-configure-vm.md).
  - Para um início rápido que mostra como ligar a uma instância gerida a partir de um computador de cliente no local através de uma ligação ponto a site, consulte [configurar uma ligação ponto a site](sql-database-managed-instance-configure-p2s.md).
- Para restaurar uma base de dados do SQL Server existente do local para uma Instância Gerida, pode utilizar o [Azure Database Migration Service (DMS) para a migração](../dms/tutorial-sql-server-to-managed-instance.md) para restaurar a partir de um ficheiro de cópia de segurança da base de dados ou o [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) para restaurar a partir de um ficheiro de cópia de segurança da base de dados.
- Para avançadas de monitorização de desempenho da base de dados de instância gerida com inteligência incorporada de resolução de problemas, consulte [SQL Database do Azure Monitor, através da análise de SQL do Azure](../azure-monitor/insights/azure-sql.md)
