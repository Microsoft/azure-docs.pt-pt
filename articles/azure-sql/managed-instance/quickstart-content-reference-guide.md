---
title: Começar a referência de conteúdo
titleSuffix: Azure SQL Managed Instance
description: 'Uma referência para conteúdos que o ajudam a começar com o Azure SQL Managed Instance. '
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: 2ef242b95bdafd1781e4db6e72a6374ecf8298c5
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054348"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Começar com a Instância Gerida Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[A Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) cria uma base de dados com quase 100% de compatibilidade com o mais recente motor de base de dados SQL Server on-premis (Enterprise Edition), fornecendo uma implementação de rede virtual nativa [(VNet)](../../virtual-network/virtual-networks-overview.md) que aborda preocupações comuns de segurança, e um modelo de [negócio](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes sQL Server no local.

Neste artigo, encontrará referências a conteúdos que o ensinam a configurar e criar rapidamente uma Instância Gerida SQL e migrar as suas bases de dados.

## <a name="quickstart-overview"></a>Descrição geral do Início Rápido

Os seguintes quickstarts permitem-lhe criar rapidamente uma Instância Gerida SQL, configurar uma máquina virtual ou apontar para a ligação VPN do site para aplicação do cliente, e restaurar uma base de dados para o seu novo SQL Managed Instance usando um `.bak` ficheiro.

### <a name="configure-environment"></a>Configurar o ambiente

Como primeiro passo, você precisaria criar o seu primeiro SQL Managed Instance com o ambiente de rede onde será colocado, e ativar a ligação do computador ou da máquina virtual onde está a executar consultas para SQL Managed Instance. Pode utilizar os seguintes guias:

- [Crie uma Instância Gerida SQL utilizando o portal Azure](instance-create-quickstart.md). No portal Azure, configura os parâmetros necessários (nome de utilizador/palavra-passe, número de núcleos e valor máximo de armazenamento), e cria automaticamente o ambiente da rede Azure sem a necessidade de saber sobre detalhes de rede e requisitos de infraestrutura. Apenas certifique-se de que tem um tipo de [subscrição](resource-limits.md#supported-subscription-types) que está atualmente autorizado a criar uma Instância Gerida SQL. Se tiver a sua própria rede que pretende utilizar ou pretender personalizar a rede, consulte a configuração de [uma rede virtual existente para a Instância Gerida azure SQL](vnet-existing-add-subnet.md) ou crie uma rede virtual para a Instância Gerida [Azure SQL](virtual-network-subnet-create-arm-template.md).
- Um SQL Managed Instance é criado no seu próprio VNet sem ponto final público. Para acesso a aplicações ao cliente, pode **criar um VM na mesma VNet (subnet diferente)** ou **criar uma ligação VPN ponto-a-site à VNet a partir do seu computador cliente** utilizando um destes quickstarts:
  - Ative [o ponto final público](public-endpoint-configure.md) na sua Instância Gerida SQL de forma a aceder aos seus dados diretamente do seu ambiente.
  - Crie [a Máquina Virtual Azure no SQL Managed Instance VNet](connect-vm-instance-configure.md) para conectividade de aplicação de clientes, incluindo o Estúdio de Gestão de Servidores SQL.
  - Configurar [a ligação VPN ponto-a-site ao seu SQL Managed Instance](point-to-site-p2s-configure.md) a partir do seu computador cliente no qual tem o SQL Server Management Studio e outras aplicações de conectividade do cliente. Esta é outra de duas opções de conectividade com a sua Instância Gerida SQL e com o seu VNet.

  > [!NOTE]
  > - Também pode utilizar a rota expressa ou a ligação site-to-site da sua rede local, mas estas abordagens estão fora do âmbito destes quickstarts.
  > - Se alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, por favor note que a retenção só se aplicará aos registos escritos após a alteração do valor de retenção (os registos escritos durante o período em que a retenção foi definida para ilimitado são preservados, mesmo após a retenção ser ativada).

Como alternativa à criação manual de Instância Gerida SQL, pode utilizar [powerShell](scripts/create-configure-managed-instance-powershell.md), [PowerShell com modelo de Gestor](scripts/create-powershell-azure-resource-manager-template.md)de Recursos , ou [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) para script e automatizar este processo.

### <a name="migrate-your-databases"></a>Migrar as suas bases de dados

Depois de criar um SQL Managed Instance e configurar o acesso, pode começar a migrar as suas bases de dados a partir de SQL Server no local ou VMs Azure. A migração pode falhar se tiver algumas funcionalidades não suportadas na base de dados de origem que pretende migrar. Para evitar falhas e verificar a compatibilidade, pode utilizar o [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) para analisar as suas bases de dados no SQL Server e encontrar qualquer problema que possa bloquear a migração para uma Instância Gerida sQL, como a existência de [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) ou vários ficheiros de registo. Se resolver estes problemas, as suas bases de dados estão prontas para migrar para a Instância Gerida pela SQL. O Assistente de [Experimentação](/sql/dea/database-experimentation-assistant-overview) de Bases de Dados é outra ferramenta útil que pode gravar a sua carga de trabalho no SQL Server e reproduzi-la numa Instância Gerida SQL para que possa determinar que haverá problemas de desempenho se migrar para uma Instância Gerida SQL.

Assim que tiver a certeza de que pode migrar a sua base de dados para uma instância gerida pela SQL, pode utilizar as capacidades de restauro do SQL Server nativo para restaurar uma base de dados num Caso Gerido SQL a partir de um `.bak` ficheiro. Pode utilizar este método para migrar bases de dados do motor de base de dados SQL Server instalado no local ou no Azure VM. Para um arranque rápido, consulte [Restaurar de reserva para uma instância gerida por SQL](restore-sample-database-quickstart.md). Neste arranque rápido, restaure a partir de um `.bak` ficheiro armazenado no armazenamento Azure Blob utilizando o comando `RESTORE` Transact-SQL.

> [!TIP]
> Para utilizar o `BACKUP` comando Transact-SQL para criar uma cópia de segurança da sua base de dados no armazenamento Do Blob Azure, consulte a cópia de [segurança do SQL Server para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Estes quickstarts permitem-lhe criar, configurar e restaurar rapidamente a cópia de segurança da base de dados numa Instância Gerida SQL. Em alguns cenários, você precisaria personalizar ou automatizar a implementação de Instâncias Geridas SQL e o ambiente de networking necessário. Estes cenários serão descritos abaixo.

## <a name="customize-network-environment"></a>Personalizar o ambiente de rede

Embora a VNet/subnet possa ser configurada automaticamente quando a instância é [criada utilizando o portal Azure,](instance-create-quickstart.md)pode ser bom criá-la antes de começar a criar Instâncias Geridas SQL porque pode configurar os parâmetros de VNet e subnet. A forma mais fácil de criar e configurar o ambiente de rede é usar o modelo de implementação do [Recurso Azure](virtual-network-subnet-create-arm-template.md) que cria e configura a sua rede e sub-rede onde a instância será colocada. Basta pressionar o Gestor de Recursos Azure a acionar o botão e preencher o formulário com parâmetros.

Como alternativa, também pode utilizar este [script PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Se já tem uma VNet e uma subnet onde deseja implementar a sua Instância Gerida SQL, tem de se certificar de que o seu VNet e subnet satisfazem os [requisitos](connectivity-architecture-overview.md#network-requirements)de rede . Utilize este [script PowerShell para verificar se a sua sub-rede está corretamente configurada](vnet-existing-add-subnet.md). Este script valida a sua rede e reporta quaisquer problemas, dizendo-lhe o que deve ser alterado e, em seguida, oferece-se para fazer as alterações necessárias na sua VNet/subnet. Execute este script se não quiser configurar manualmente o seu VNet/subnet. Também pode executá-lo após qualquer reconfiguração importante da sua infraestrutura de rede. Se quiser criar e configurar a sua própria rede, leia a [arquitetura de conectividade](connectivity-architecture-overview.md) e este guia final para criar e [configurar um ambiente de Instância Gerida SQL](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-sql-managed-instance"></a>Migrar para uma instância gerida pela SQL

Os quickstarts anteriormente mencionados permitem-lhe configurar rapidamente uma Instância Gerida SQL e mover as suas bases de dados utilizando a `RESTORE` capacidade nativa. Este é um bom ponto de partida se quiser completar conceitos rápidos e verificar se a sua solução pode funcionar em Managed Instance.

No entanto, para migrar a sua base de dados de produção ou mesmo bases de dados de dev/teste que pretende utilizar para algum teste de desempenho, teria de considerar a utilização de algumas técnicas adicionais, tais como:

- Teste de desempenho - Deve medir as métricas de desempenho de base na sua fonte SQL Server e compará-las com as métricas de desempenho no destino SQL Managed Instance onde emigrou a base de dados. Saiba mais sobre as [melhores práticas para a comparação de desempenho.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)
- Migração online - Com o nativo `RESTORE` descrito neste artigo, é preciso esperar que as bases de dados sejam restauradas (e copiadas para o armazenamento da Blob Azure se ainda não estiverem armazenadas lá). Isto causa algum tempo de inatividade da sua aplicação especialmente para bases de dados maiores. Para mover a sua base de dados de produção, utilize o serviço de Migração de [Dados (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) para migrar a sua base de dados com o tempo mínimo de inatividade. O DMS consegue isso empurrando incrementalmente as alterações feitas na sua base de dados de origem para a base de dados SQL Managed Instance sendo restaurada. Desta forma, pode mudar rapidamente a sua aplicação de origem para base de dados de destino com o mínimo de tempo de inatividade.

Saiba mais sobre o [processo de migração recomendado.](migrate-to-instance-from-sql-server.md)

## <a name="next-steps"></a>Próximos passos

- Encontre aqui uma [lista de funcionalidades suportadas de alto nível no SQL Managed Instance](../database/features-comparison.md) e detalhes e [questões conhecidas aqui](transact-sql-tsql-differences-sql-server.md).
- Conheça [as características técnicas da Instância Gerida SQL](resource-limits.md#service-tier-characteristics).
- Encontre como usar [uma instância gerida sQL](how-to-content-reference-guide.md)mais avançada .
- [Identifique o SKU de instância gerido pelo Azure SQL direito para a sua base de dados no local](/sql/dma/dma-sku-recommend-sql-db/).
