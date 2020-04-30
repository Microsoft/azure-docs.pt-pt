---
title: Quickstart - Instância gerida pela SQL
description: Saiba como começar rapidamente com a Base de Dados Azure SQL - instância gerida
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 07/11/2019
ms.openlocfilehash: 602de3e23eb5419958f84b071e2220550d1d04d0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73821725"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Getting started with Azure SQL Database managed instance (Introdução à instância gerida da Base de Dados SQL do Azure)

A opção de implementação de [instânciagerida](sql-database-managed-instance-index.yml) cria uma base de dados com quase 100% de compatibilidade com o mais recente motor de base de dados SQL Server on-premises (Enterprise Edition), fornecendo uma implementação de rede virtual nativa [(VNet)](../virtual-network/virtual-networks-overview.md) que aborda preocupações comuns de segurança, e um modelo de [negócio](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes sQL Server no local. Neste artigo, você aprenderá a configurar rapidamente e criar uma instância gerida e migrar as suas bases de dados.

## <a name="quickstart-overview"></a>Descrição geral do Início Rápido

Os seguintes quickstarts permitem-lhe criar rapidamente uma instância gerida, configurar uma máquina virtual ou apontar para a ligação VPN do site para aplicação do cliente, e restaurar uma base de dados para a sua nova instância gerida usando um `.bak` ficheiro.

### <a name="configure-environment"></a>Configurar o ambiente

Como primeiro passo, você precisaria criar a sua primeira Instância Gerida com o ambiente de rede onde será colocada, e ativar a ligação do computador ou da máquina virtual onde está a executar consultas à Instância Gerida. Pode utilizar os seguintes guias:

- [Crie uma instância gerida utilizando o portal Azure.](sql-database-managed-instance-get-started.md) No portal Azure, configura os parâmetros necessários (nome de utilizador/palavra-passe, número de núcleos e valor máximo de armazenamento), e cria automaticamente o ambiente da rede Azure sem a necessidade de saber sobre detalhes de rede e requisitos de infraestrutura. Apenas certifique-se de que tem um tipo de [subscrição](sql-database-managed-instance-resource-limits.md#supported-subscription-types) que está atualmente autorizado a criar uma instância gerida. Se tiver a sua própria rede que pretende utilizar ou pretender personalizar a rede, consulte uma rede virtual existente para a instância gerida pela [Azure SQL Database](sql-database-managed-instance-configure-vnet-subnet.md) ou crie uma rede virtual para a instância gerida pela [Azure SQL Database](sql-database-managed-instance-create-vnet-subnet.md).
- Um caso gerido é criado em VNet próprio sem ponto final público. Para acesso a aplicações ao cliente, pode **criar um VM na mesma VNet (subnet diferente)** ou **criar uma ligação VPN ponto-a-site à VNet a partir do seu computador cliente** utilizando um destes quickstarts:
  - Ative [o ponto final público](sql-database-managed-instance-public-endpoint-configure.md) na sua Instância Gerida de forma a aceder aos seus dados diretamente do seu ambiente.
  - Crie [a Máquina Virtual Azure na instância gerida VNet](sql-database-managed-instance-configure-vm.md) para conectividade de aplicação de clientes, incluindo o SQL Server Management Studio.
  - Configurar [a ligação VPN ponto-a-site à sua instância gerida](sql-database-managed-instance-configure-p2s.md) a partir do seu computador cliente no qual tem o SQL Server Management Studio e outras aplicações de conectividade do cliente. Esta é outra de duas opções de conectividade com a sua instância gerida e com o seu VNet.

  > [!NOTE]
  > Também pode utilizar a rota expressa ou a ligação site-to-site da sua rede local, mas estas abordagens estão fora do âmbito destes quickstarts.

Como alternativa à criação manual de Instância Gerida, pode utilizar [powerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), [PowerShell com modelo de Gestor](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)de Recursos , ou [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) para script e automatizar este processo.

### <a name="migrate-your-databases"></a>Migrar as suas bases de dados

Depois de criar uma instância gerida e configurar o acesso, pode começar a migrar as suas bases de dados a partir de SQL Server no local ou VMs Azure. A migração falha se tiver algumas funcionalidades não suportadas na base de dados de origem que pretende migrar. Para evitar falhas e verificar a compatibilidade, pode instalar o [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) que analisa as suas bases de dados no SQL Server e encontra qualquer problema que possa bloquear a migração para uma instância gerida, como a existência de [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) ou vários ficheiros de registo. Se resolver estes problemas, as suas bases de dados estão prontas para migrar para instânciagerida. O Assistente de [Experimentação](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) de Bases de Dados é outra ferramenta útil que pode gravar a sua carga de trabalho no SQL Server e reproduzi-la numa instância gerida para que possa determinar que haverá problemas de desempenho se migrar para uma instância gerida.

Uma vez que tenha a certeza de que pode migrar a sua base de dados para uma instância gerida, pode `.bak` utilizar as capacidades de restauro do SQL Server nativo para restaurar uma base de dados numa instância gerida a partir de um ficheiro. Pode utilizar este método para migrar bases de dados do motor de base de dados SQL Server instalado no local ou no Azure VM. Para um arranque rápido, consulte [Restaurar de backup para um caso gerido](sql-database-managed-instance-get-started-restore.md). Neste arranque rápido, restaure a partir de um `.bak` ficheiro `RESTORE` armazenado no armazenamento Azure Blob utilizando o comando Transact-SQL.

> [!TIP]
> Para utilizar `BACKUP` o comando Transact-SQL para criar uma cópia de segurança da sua base de dados no armazenamento Do Blob Azure, consulte a cópia de [segurança do SQL Server para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Estes quickstarts permitem-lhe criar, configurar e restaurar rapidamente a cópia de segurança da base de dados para uma instância gerida. Em alguns cenários, você precisaria personalizar ou automatizar a implementação de instâncias geridas e o ambiente de networking necessário. Estes cenários serão descritos abaixo.

## <a name="customize-network-environment"></a>Personalizar o ambiente de rede

Embora a VNet/subnet possa ser configurada automaticamente quando a instância é criada utilizando [o portal Azure,](sql-database-managed-instance-get-started.md)pode ser bom criá-la antes de começar a criar Instâncias Geridas porque pode configurar os parâmetros de VNet e subnet. A forma mais fácil de criar e configurar o ambiente de rede é usar o modelo de implementação do [Recurso Azure](sql-database-managed-instance-create-vnet-subnet.md) que irá criar e configurar a sua rede e sub-rede onde a instância será colocada. Basta pressionar o Gestor de Recursos Azure a acionar o botão e preencher o formulário com parâmetros.

Como alternativa, também pode utilizar este [script PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Se já tem uma VNet e uma subnet onde deseja implementar a sua instância gerida, tem de se certificar de que o seu VNet e a sua subnet satisfazem os [requisitos](sql-database-managed-instance-connectivity-architecture.md#network-requirements)de rede . Utilize este [script PowerShell para verificar se a sua sub-rede está corretamente configurada](sql-database-managed-instance-configure-vnet-subnet.md). Este script valida a sua rede e reporta quaisquer problemas, e diz-lhe o que deve ser alterado e, em seguida, oferece-se para fazer as alterações necessárias na sua VNet/subnet. Execute este script se não quiser configurar manualmente o seu VNet/subnet. Também pode executá-lo após qualquer reconfiguração importante da sua infraestrutura de rede. Se quiser criar e configurar a sua própria rede, leia a [arquitetura de conectividade](sql-database-managed-instance-connectivity-architecture.md) e este guia final para criar e [configurar um ambiente de instância gerido.](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)

## <a name="migrate-to-a-managed-instance"></a>Migrar para um caso gerido

Os artigos nestes quickstarts permitem-lhe configurar rapidamente uma instância `RESTORE` gerida e mover as suas bases de dados utilizando a capacidade nativa. Este é um bom ponto de partida se quiser completar conceitos rápidos e verificar se a sua solução pode funcionar em Managed Instance. 

No entanto, para migrar a sua base de dados de produção ou mesmo bases de dados de dev/teste que pretende utilizar para algum teste de desempenho, teria de considerar a utilização de algumas técnicas adicionais, tais como:
- Teste de desempenho - Deve medir o desempenho da linha de base na sua fonte SQL Server e compará-los com o desempenho no destino Managed Instance onde emigrou a base de dados. Saiba mais sobre as [melhores práticas para a comparação de desempenho.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)
- Migração online - `RESTORE` Com o nativo descrito neste artigo, é preciso esperar que as bases de dados sejam restauradas (e copiadas para o armazenamento da Blob Azure se ainda não estiverem armazenadas lá). Isto causa algum tempo de inatividade da sua aplicação especialmente para bases de dados maiores. Para mover a sua base de dados de produção, utilize o serviço de Migração de [Dados (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) para migrar a sua base de dados com o tempo mínimo de inatividade. O DMS consegue isso empurrando incrementalmente as alterações feitas na sua base de dados de origem para a base de dados de instâncias gerida seleções a ser restaurada. Desta forma, pode mudar rapidamente a sua aplicação de origem para base de dados de destino com o tempo mínimo de inatividade.

Saiba mais sobre o [processo de migração recomendado.](sql-database-managed-instance-migrate.md)

## <a name="next-steps"></a>Passos seguintes

- Encontre uma [lista de funcionalidades suportadas de alto nível em instância gerida aqui](sql-database-features.md) e detalhes e [questões conhecidas aqui](sql-database-managed-instance-transact-sql-information.md).
- Conheça [as características técnicas da instância gerida.](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)
- Encontre como usar uma instância gerida na Base de [Dados Azure SQL](sql-database-howto-managed-instance.md).
- Identifique a base de [dados Azure SQL/SKU](/sql/dma/dma-sku-recommend-sql-db/)de instância gerida para a sua base de dados no local .
