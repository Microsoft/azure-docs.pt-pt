---
title: Início rápido-instância gerenciada do banco de dados SQL do Azure | Microsoft Docs
description: Saiba como começar rapidamente com a instância gerenciada do banco de dados SQL do Azure
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
ms.openlocfilehash: a8dfe0db5277a995b6e6675381ebeb5aeb243c9b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647219"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Introdução à instância gerenciada do banco de dados SQL do Azure

A opção de implantação de [instância gerenciada](sql-database-managed-instance-index.yml) cria um banco de dados com quase 100% de compatibilidade com o mecanismo de banco de dados local mais recente do SQL Server (Enterprise Edition), fornecendo uma implementação de [rede virtual nativa (VNet)](../virtual-network/virtual-networks-overview.md) que aborda questões comuns de segurança e um [modelo de negócios](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes SQL Server locais. Neste artigo, você aprenderá a configurar e criar rapidamente uma instância gerenciada e migrar seus bancos de dados.

## <a name="quickstart-overview"></a>Visão geral do início rápido

Os guias de início rápido a seguir permitem criar rapidamente uma instância gerenciada, configurar uma máquina virtual ou uma conexão VPN ponto a site para o aplicativo cliente e restaurar um banco de dados para sua `.bak` nova instância gerenciada usando um arquivo.

### <a name="configure-environment"></a>Configurar o ambiente

Como uma primeira etapa, você precisaria criar sua primeira Instância Gerenciada com o ambiente de rede em que ele será colocado e habilitar a conexão do computador ou da máquina virtual em que você está executando consultas para Instância Gerenciada. Você pode usar os seguintes guias:

- [Crie uma instância gerenciada usando o portal do Azure](sql-database-managed-instance-get-started.md). No portal do Azure, você configura os parâmetros necessários (nome de usuário/senha, número de núcleos e quantidade máxima de armazenamento) e cria automaticamente o ambiente de rede do Azure sem a necessidade de conhecer os detalhes da rede e os requisitos de infraestrutura. Você só tem certeza de que tem um [tipo de assinatura](sql-database-managed-instance-resource-limits.md#supported-subscription-types) que atualmente tem permissão para criar uma instância gerenciada. Se você tiver sua própria rede que deseja usar ou se quiser personalizar a rede, consulte [Configurar uma rede virtual existente para instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-configure-vnet-subnet.md) ou [criar uma rede virtual para instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-create-vnet-subnet.md).
- Uma instância gerenciada é criada na própria VNet sem nenhum ponto de extremidade público. Para acesso ao aplicativo cliente, você pode **criar uma VM na mesma vnet (sub-rede diferente)** ou **criar uma conexão VPN ponto a site com a VNet do computador cliente** usando um destes guias de início rápido:
  - Habilite o [ponto de extremidade público](sql-database-managed-instance-public-endpoint-configure.md) no seu instância gerenciada para acessar seus dados diretamente do seu ambiente.
  - Crie [uma máquina virtual do Azure na VNet da instância gerenciada](sql-database-managed-instance-configure-vm.md) para conectividade de aplicativo cliente, incluindo SQL Server Management Studio.
  - Configure [a conexão VPN ponto a site para sua instância gerenciada](sql-database-managed-instance-configure-p2s.md) do computador cliente no qual você tem SQL Server Management Studio e outros aplicativos de conectividade de cliente. Essa é outra das duas opções de conectividade com sua instância gerenciada e sua VNet.

  > [!NOTE]
  > Você também pode usar a rota expressa ou a conexão site a site da sua rede local, mas essas abordagens estão fora do escopo desses guias de início rápido.

Como alternativa à criação manual de Instância Gerenciada, você pode usar o [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), o [PowerShell com o modelo do Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)ou [CLI do Azure](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) para gerar script e automatizar esse processo.

### <a name="migrate-your-databases"></a>Migrar seus bancos de dados

Depois de criar uma instância gerenciada e configurar o acesso, você pode começar a migrar seus bancos de dados de SQL Server VMs locais ou do Azure. A migração falhará se você tiver alguns recursos sem suporte no banco de dados de origem que deseja migrar. Para evitar falhas e verificar a compatibilidade, você pode instalar o [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) que analisa seus bancos de dados no SQL Server e encontrar qualquer problema que possa bloquear a migração para uma instância gerenciada, como a existência de [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) ou vários arquivos de log. Se você resolver esses problemas, seus bancos de dados estarão prontos para migrar para a instância gerenciada. [Assistente para experimentos de banco de dados](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) é outra ferramenta útil que pode registrar sua carga de trabalho no SQL Server e reproduzi-la em uma instância gerenciada para que você possa determinar que haverá problemas de desempenho se você migrar para uma instância gerenciada.

Depois de ter certeza de que você pode migrar seu banco de dados para uma instância gerenciada, você pode usar os recursos de restauração de SQL Server nativos para restaurar um `.bak` banco de dados em uma instância gerenciada de um arquivo. Você pode usar esse método para migrar bancos de dados do SQL Server mecanismo de banco de dados instalado no local ou na VM do Azure. Para obter um início rápido, consulte [restaurar do backup para uma instância gerenciada](sql-database-managed-instance-get-started-restore.md). Neste guia de início rápido, você restaura `.bak` de um arquivo armazenado no armazenamento de BLOBs do Azure usando o `RESTORE` comando Transact-SQL.

> [!TIP]
> Para usar o `BACKUP` comando Transact-SQL para criar um backup de seu banco de dados no armazenamento de BLOBs do Azure, consulte [SQL Server Backup to URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Esses guias de início rápido permitem criar, configurar e restaurar rapidamente o backup de banco de dados em uma instância gerenciada. Em alguns cenários, você precisaria personalizar ou automatizar a implantação de instâncias gerenciadas e o ambiente de rede necessário. Esses cenários serão descritos abaixo.

## <a name="customize-network-environment"></a>Personalizar o ambiente de rede

Embora a VNet/sub-rede possa ser configurada automaticamente quando a instância é criada usando [o portal do Azure](sql-database-managed-instance-get-started.md), talvez seja bom criá-la antes de começar a criar instâncias gerenciadas, pois você pode configurar os parâmetros de VNet e de sub-rede. A maneira mais fácil de criar e configurar o ambiente de rede é usar o modelo de [implantação de recursos do Azure](sql-database-managed-instance-create-vnet-subnet.md) , que criará e configurará a rede e a sub-rede em que a instância será colocada. Você só precisa pressionar o botão Azure Resource Manager implantar e preencher o formulário com parâmetros.

Como alternativa, você também pode usar esse [script do PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Se você já tiver uma VNet e uma sub-rede em que deseja implantar sua instância gerenciada, você precisará certificar-se de que sua VNet e sub-rede atendam [aos requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Use este [script do PowerShell para verificar se sua sub-rede está configurada corretamente](sql-database-managed-instance-configure-vnet-subnet.md). Esse script valida sua rede e relata quaisquer problemas e informa o que deve ser alterado e, em seguida, oferece as alterações necessárias em sua VNet/sub-rede. Execute este script se não quiser configurar a VNet/sub-rede manualmente. Você também pode executá-lo após qualquer reconfiguração principal da sua infraestrutura de rede. Se você quiser criar e configurar sua própria rede, leia [arquitetura de conectividade](sql-database-managed-instance-connectivity-architecture.md) e este [guia final para criar e configurar um ambiente de instância gerenciada](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-managed-instance"></a>Migrar para uma instância gerenciada

Os artigos nestes guias de início rápido permitem que você configure rapidamente uma instância gerenciada e mova seus bancos de dados usando `RESTORE` o recurso nativo. Esse é um bom ponto de partida se você quiser concluir os conceitos de provas rápidas e verificar se sua solução pode funcionar em Instância Gerenciada. 

No entanto, a fim de migrar seu banco de dados de produção ou até mesmo os bancos de dados de desenvolvimento/teste que você deseja usar para algum teste de desempenho, você precisará considerar o uso de algumas técnicas adicionais, como:
- Teste de desempenho-você deve medir o desempenho de linha de base em sua instância de SQL Server de origem e compará-las com o desempenho no Instância Gerenciada de destino em que você migrou o banco de dados. Saiba mais sobre as [práticas recomendadas para comparação de desempenho](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Migração online-com o nativo `RESTORE` descrito neste artigo, você precisa aguardar que os bancos de dados sejam restaurados (e copiados para o armazenamento de BLOBs do Azure, se ainda não estiverem armazenados nele). Isso causa algum tempo de inatividade do seu aplicativo, especialmente para bancos de dados maiores. Para mover seu banco de dados de produção, use o DMS para migrar seu banco de [dado](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) com o mínimo de tempo de inatividade. O DMS realiza isso incrementalmente as alterações feitas no banco de dados de origem para o banco de dados de instância gerenciada que está sendo restaurado. Dessa forma, você pode alternar rapidamente seu aplicativo do banco de dados de origem para destino com o tempo de inatividade mínimo.

Saiba mais sobre o [processo de migração recomendado](sql-database-managed-instance-migrate.md).

## <a name="next-steps"></a>Passos Seguintes

- Encontre uma [lista de alto nível dos recursos com suporte na instância gerenciada aqui](sql-database-features.md) e [detalhes e problemas conhecidos aqui](sql-database-managed-instance-transact-sql-information.md).
- Saiba mais sobre as [características técnicas da instância gerenciada](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).
- Encontre instruções mais avançadas [sobre como usar uma instância gerenciada no banco de dados SQL do Azure](sql-database-howto-managed-instance.md).
- [Identifique o banco de dados SQL do Azure/instância gerenciada SKU correto para seu banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).
