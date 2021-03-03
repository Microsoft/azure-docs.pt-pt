---
title: 'Quickstart: Criar uma Instância Gerida Azure SQL (portal)'
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
ms.date: 1/29/2021
ms.openlocfilehash: d356cad1b4754875574e19be732fdf6481c61e22
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691217"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Quickstart: Criar uma instância gerida Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este quickstart ensina-o a criar um [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) no portal Azure.

> [!IMPORTANT]
> Para limitações, consulte [regiões apoiadas](resource-limits.md#supported-regions) e [tipos de assinaturas suportados.](resource-limits.md#supported-subscription-types)

## <a name="create-an-azure-sql-managed-instance"></a>Criar uma Instância Gerida do SQL do Azure

Para criar uma SQL Managed Instance, siga estes passos: 

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Azure SQL** no menu esquerdo do portal Azure. Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços** e introduza o **Azure SQL** na caixa de pesquisa.
1. **Selecione +Adicione** para abrir a página **de opção de implementação Select SQL.** Pode ver informações adicionais sobre a Azure SQL Managed Instance selecionando detalhes do **Show** no azulejo **gerido sql.**
1. Selecione **Criar**.

   ![Criar uma instância gerida](./media/instance-create-quickstart/create-azure-sql-managed-instance.png)

4. Utilize os separadores no formulário de provisionamento **de instância gerida Create Azure SQL** para adicionar informações necessárias e opcionais. As seguintes secções descrevem estes separadores.

### <a name="basics-tab"></a>Separador básico

- Preencha as informações **obrigatórias requeridas no separador Básicos.** Este é um conjunto mínimo de informações necessárias para a disponibilização de uma SQL Managed Instance.

   ![Separador "Básicos" para criar uma SQL Managed Instance](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-basics.png)

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

   ![Formulário de instância gerido](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-configure-performance.png)

| Definição| Valor sugerido | Descrição |
| ------ | --------------- | ----------- |
| **Nível de serviço** | Selecione uma das opções. | Com base no seu cenário, selecione uma das seguintes opções: </br> <ul><li>**Finalidade Geral**: para a maioria das cargas de trabalho de produção e a opção por defeito.</li><li>**Business Critical**: projetado para cargas de trabalho de baixa latência com alta resiliência a falhas e falhas rápidas.</li></ul><BR>Para obter mais informações, consulte [a Base de Dados Azure SQL e os níveis de serviço de instância gerida Azure SQL](../../azure-sql/database/service-tiers-general-purpose-business-critical.md) e reveja [os limites de recursos de Azure SQL Managed Instance](../../azure-sql/managed-instance/resource-limits.md).|
| **Geração de Hardware** | Selecione uma das opções. | A geração de hardware define geralmente os limites de cálculo e memória e outras características que impactam o desempenho da carga de trabalho. **Gen5** é o padrão.|
| **modelo de computação vCore** | Selecione uma opção. | vCores representam uma quantidade exata de recursos computacional que são sempre a provisionados para a sua carga de trabalho. **Oito vCores** é o padrão.|
| **Armazenamento em GB** | Selecione uma opção. | Tamanho de armazenamento em GB, selecione com base no tamanho esperado dos dados. Se migrar os dados existentes a partir das instalações ou em várias plataformas de nuvem, consulte [a visão geral da migração: SQL Server para SQL Managed Instance](../../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).|
| **Benefício Híbrido do Azure** | Verifique a opção se aplicável. | Para alavancar uma licença existente para a Azure. Para mais informações, consulte [Azure Hybrid Benefit - Azure SQL Database & SQL Managed Instance](../../azure-sql/azure-hybrid-benefit.md). |
| **Redundância de armazenamento de backup** | Selecione **armazenamento de backup geo-redundante**. | Redundância de armazenamento dentro de Azure para armazenamento de backup. Note que este valor não pode ser alterado mais tarde. O armazenamento de backup geo-redundante é padrão e recomendado, embora a zona e a redundância local permitam uma maior flexibilidade de custos e residência de dados de uma região única. Para obter mais informações, consulte [a redundância de armazenamento de backup](../database/automated-backups-overview.md?tabs=managed-instance#backup-storage-redundancy).|


- Para rever as suas escolhas antes de criar uma SqL Managed Instance, pode selecionar **Review + create**. Ou, configurar opções de networking selecionando **Seguinte: Networking**.

### <a name="networking-tab"></a>Separador de rede

- Preencha informações opcionais no **separador Networking.** Se omitir esta informação, o portal aplicará definições predefinidas.

   ![Separador "Networking" para criar uma instância gerida](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-networking.png)

   Utilize a tabela abaixo como referência para as informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Rede virtual** | Selecione criar **uma nova rede virtual** ou uma rede virtual válida e uma sub-rede.| Se uma rede ou sub-rede não estiver disponível, deve ser [modificada para satisfazer os requisitos de rede](vnet-existing-add-subnet.md) antes de a selecionar como alvo para a nova instância gerida. Para obter informações sobre os requisitos para configurar o ambiente de rede para a SQL Managed Instance, consulte [configurar uma rede virtual para sql Managed Instance](connectivity-architecture-overview.md). |
   | **Tipo de ligação** | Escolha entre um proxy e um tipo de ligação de redirecionamento.|Para obter mais informações sobre tipos de ligação, consulte [o tipo de ligação Azure SQL Managed Instance](../database/connectivity-architecture.md#connection-policy).|
   | **Ponto final público**  | **Selecione Desativar**. | Para que um caso gerido seja acessível através do ponto final de dados públicos, é necessário ativar esta opção. | 
   | **Permitir o acesso a partir (se** **o ponto final público** estiver ativado) | Selecione **Sem Acesso**  |A experiência do portal permite configurar um grupo de segurança com um ponto final público. </br> </br> Com base no seu cenário, selecione uma das seguintes opções: </br> <ul> <li>**Serviços Azure**: Recomendamos esta opção quando estiver a ligar-se a partir do Power BI ou de outro serviço multitenant. </li> <li> **Internet**: Utilize para fins de teste quando pretender rodar rapidamente uma instância gerida. Não recomendamos para ambientes de produção. </li> <li> **Sem acesso**: Esta opção cria uma regra de segurança **Deny.** Modifique esta regra para tornar uma instância gerida acessível através de um ponto final público. </li> </ul> </br> Para obter mais informações sobre a segurança do ponto final público, consulte [a Utilização de Azure SQL Managed Instance de forma segura com um ponto final público](public-endpoint-overview.md).|

- Selecione **Review + crie** para rever as suas escolhas antes de criar uma instância gerida. Ou, configurar mais definições personalizadas selecionando **Seguinte: Definições adicionais**.


### <a name="additional-settings"></a>Definições adicionais

- Preencha informações opcionais no **separador Definições Adicionais.** Se omitir esta informação, o portal aplicará definições predefinidas.

   ![Separador "Definições adicionais" para criar um caso gerido](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-additional-settings.png)

   Utilize a tabela abaixo como referência para as informações necessárias neste separador.

   | Definição| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   | **Agrupamento** | Escolha a colagem que pretende utilizar para a sua instância gerida. Se migrar bases de dados do SQL Server, verifique a colagem de origem utilizando `SELECT SERVERPROPERTY(N'Collation')` e utilize esse valor.| Para obter informações sobre colagens, consulte [set ou altere a colagem do servidor](/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Fuso horário** | Selecione o fuso horário que a instância gerida observará.|Para mais informações, consulte [fusos horários.](timezones-overview.md)|
   | **Uso como failover secundário** | Selecione **Yes** (Sim). | Ative esta opção para utilizar a instância gerida como um grupo de failover secundário.|
   | **Primeira SQL Caso gerido** (se **a utilização como secundário de failover** está definida para **Sim**) | Escolha um caso gerido primário existente que será acompanhado na mesma zona de DNS com a instância gerida que está a criar. | Este passo permitirá a configuração pós-criação do grupo de failover. Para obter mais informações, consulte [Tutorial: Adicione uma instância gerida a um grupo de failover](failover-group-add-instance-tutorial.md).|

- Selecione **Review + crie** para rever as suas escolhas antes de criar uma instância gerida. Ou, configurar tags Azure selecionando **Seguinte: Tags (recomendadas).**

### <a name="tags"></a>Etiquetas

- Adicione etiquetas aos recursos no seu modelo de Gestor de Recursos Azure (modelo ARM). [As etiquetas](../../azure-resource-manager/management/tag-resources.md) ajudam-no a organizar logicamente os seus recursos. Os valores da etiqueta aparecem nos relatórios de custos e permitem outras atividades de gestão por etiqueta. 

- Considere pelo menos marcar o seu novo SQL Managed Instance com a etiqueta Proprietário para identificar quem criou, e a etiqueta Ambiente para identificar se este sistema é Produção, Desenvolvimento, etc. Para mais informações, consulte [Desenvolver a sua estratégia de nomeação e marcação para recursos Azure.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)
 
- Selecione **Review + criar** para prosseguir.

## <a name="review--create"></a>Rever + criar

1. Selecione **Rever + criar** separador para rever as suas escolhas antes de criar uma instância gerida.

   ![Separador para revisão e criação de um caso gerido](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-review-create.png)

1. Selecione **Criar** para começar a avisão da instância gerida.

> [!IMPORTANT]
> Implementar um caso gerido é uma operação de longa duração. A implantação da primeira instância na sub-rede normalmente demora muito mais tempo do que a implantação numa sub-rede com instâncias geridas existentes. Para os tempos médios de provisionamento, consulte [a visão geral das operações de gestão de instância gerida do Azure SQL](management-operations-overview.md#duration).

## <a name="monitor-deployment-progress"></a>Monitorizar o progresso da implantação

1. Selecione o ícone **'Notificações'** para visualizar o estado da implementação.

   ![Progresso da implantação de uma implantação de instância gerida sql](./media/instance-create-quickstart/azure-sql-managed-instance-create-deployment-in-progress.png)

1. Selecione **a implementação em curso** na notificação para abrir a janela SQL Managed Instance e monitorizar ainda mais o progresso da implementação. 

> [!TIP]
> - Se fechou o seu navegador web ou se afastou do ecrã de progresso da implementação, pode monitorizar a operação de provisionamento através da página **geral** da instância gerida, ou através do PowerShell ou do Azure CLI. Para obter mais informações, consulte [as operações do Monitor.](management-operations-monitor.md#monitor-operations) 
> - Pode cancelar o processo de provisionamento através do portal Azure, ou através do PowerShell ou do Azure CLI ou de outra ferramenta utilizando a API REST. Ver [cancelar operações de gestão de instância gerida a Azure SQL](management-operations-cancel.md).

> [!IMPORTANT]
> - O início da criação de exemplos geridos pela SQL pode ser adiado nos casos em que existam outras operações de impacto, tais como operações de restauro ou dimensionamento de longa duração noutras instâncias geridas na mesma sub-rede. Para saber mais, consulte [as operações de Gestão de impacto transversal.](management-operations-overview.md#management-operations-cross-impact)
> - Para conseguir obter o estado da criação de instâncias **geridas,** é necessário ter permissões de leitura sobre o grupo de recursos. Se não tiver esta permissão ou a revogar enquanto a instância gerida estiver em processo de criação, isto pode fazer com que a SQL Managed Instance não esteja visível na lista de implementações de grupos de recursos.
>

## <a name="view-resources-created"></a>Ver recursos criados

Após a implementação bem sucedida de um caso gerido, para visualizar os recursos criados:

1. Abra o grupo de recursos para a sua instância gerida. 

   ![Recursos de instância gerida sql](./media/instance-create-quickstart/azure-sql-managed-instance-resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Ver e afinar definições de rede

Para afinar opcionalmente as definições de rede, inspecione o seguinte:

1. Na lista de recursos, selecione a tabela de rotas para rever o objeto da tabela rota (UDR) definida pelo utilizador que foi criado.

2. Na tabela de rotas, reveja as entradas para encaminhar o tráfego de e para dentro da rede virtual SQL Managed Instance. Se criar ou configurar manualmente a sua tabela de rotas, crie estas entradas na tabela de rotas SQL Managed Instance.

   ![Entrada para uma sub-rede SQL Managed Instance para local](./media/instance-create-quickstart/azure-sql-managed-instance-route-table-user-defined-route.png)

    Para alterar ou adicionar rotas, abra as **definições** do quadro Rotas na Rota.

3. Volte ao grupo de recursos e selecione o objeto do grupo de segurança de rede (NSG) que foi criado.

4. Reveja as regras de segurança de entrada e saída. 

   ![Regras de segurança](./media/instance-create-quickstart/azure-sql-managed-instance-security-rules.png)

    Para alterar ou adicionar regras, abra as **regras de segurança de entrada** e **saída** nas definições do grupo de segurança da Rede.

> [!IMPORTANT]
> Se configurar um ponto final público para a SQL Managed Instance, precisa de abrir portas para permitir o tráfego de rede permitindo ligações à SQL Managed Instance a partir da internet pública. Para obter mais informações, consulte [configurar um ponto final público para a SQL Managed Instance](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Obter os detalhes de ligação ao SQL Managed Instance

Para ligar à SQL Managed Instance, siga estes passos para recuperar o nome de anfitrião e o nome de domínio totalmente qualificado (FQDN):

1. Volte ao grupo de recursos e selecione o objeto de instância gerido SQL que foi criado.

2. No **separador Vista Geral,** localize a propriedade **Host.** Copie o nome do anfitrião para a sua área de transferência para a instância gerida para utilização no próximo arranque rápido clicando no botão **Copy to Clipboard.**

   ![Nome do anfitrião](./media/instance-create-quickstart/azure-sql-managed-instance-host-name.png)

   O valor copiado representa um nome de domínio totalmente qualificado (FQDN) que pode ser usado para ligar a SQL Managed Instance. É semelhante ao seguinte exemplo de endereço: *your_host_name.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Passos seguintes

Para saber como ligar-se à SQL Managed Instance:
- Para obter uma visão geral das opções de ligação para aplicações, consulte [Conecte as suas aplicações à SQL Managed Instance](connect-application-instance.md).
- Para um arranque rápido que mostre como ligar-se a SQL Managed Instance a partir de uma máquina virtual Azure, consulte [configurar uma ligação virtual Azure](connect-vm-instance-configure.md).
- Para obter um arranque rápido que mostre como ligar-se à SQL Managed Instance a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md).

Para restaurar uma base de dados do SQL Server existente desde as instalações até à SQL Managed Instance: 
- Utilize o [Serviço de Migração da Base de Dados Azure para a migração](../../dms/tutorial-sql-server-to-managed-instance.md) para restaurar a partir de um ficheiro de backup de bases de dados. 
- Utilize o [comando T-SQL RESTORE](restore-sample-database-quickstart.md) para restaurar a partir de um ficheiro de backup de base de dados.

Para uma monitorização avançada do desempenho da base de dados SQL Managed Instance com inteligência incorporada para resolução de problemas, consulte [monitor Azure SQL Managed Instance utilizando Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).