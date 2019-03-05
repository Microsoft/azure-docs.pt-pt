---
title: Gerida de base de dados SQL do Azure de início rápido - instância | Documentos da Microsoft
description: Saiba como começar a utilizar rapidamente com o Azure SQL Database - instância gerida
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 44ea6db1c31f0ebfbe2abe2f9f6eea165a3ff4e0
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306770"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Introdução ao Azure SQL Database instância gerida

O [instância gerida](sql-database-managed-instance-index.yml) opção de implementação cria uma base de dados com quase 100% de compatibilidade com o mais recente do SQL Server no local (Enterprise Edition) database engine, fornecendo um nativo [rede virtual (VNet)](../virtual-network/virtual-networks-overview.md) implementação que resolva problemas de segurança comuns, e um [modelo de negócio](https://azure.microsoft.com/pricing/details/sql-database/) favoráveis para clientes de SQL Server no local. Neste artigo, aprenderá como configurar rapidamente e criar uma instância gerida e migrar as suas bases de dados.

## <a name="quickstart-overview"></a>Descrição geral do guia de introdução

Os inícios Rápidos seguintes permitem-lhe rapidamente criar uma instância gerida, configurar uma máquina virtual ou do ponto de ligação de VPN de site para a aplicação de cliente e restaurar uma base de dados para a nova instância gerida com um `.bak` ficheiro.

### <a name="configure-environment"></a>Configurar o ambiente

Como primeiro passo, terá de criar a sua primeira instância gerida com o ambiente de rede onde será colocado e ativar a ligação do computador ou máquina virtual em que está a executar consultas para a instância gerida. Pode utilizar os seguintes guias:

- [Criar uma instância gerida no portal do Azure](sql-database-managed-instance-get-started.md). No portal do Azure, configure os parâmetros necessários (nome de utilizador/palavra-passe, número de núcleos e a quantidade de armazenamento máximo) e criar automaticamente o ambiente de rede do Azure sem a necessidade de saber sobre o funcionamento em rede detalhes e requisitos de infraestrutura. Apenas Certifique-se de que tem um [tipo de subscrição](sql-database-managed-instance-resource-limits.md#supported-subscription-types) que atualmente tem permissão para criar uma instância gerida. Se tem sua própria rede que pretende utilizar ou se pretender personalizar a rede, consulte [configurar uma rede virtual existente para a instância gerida da base de dados do Azure SQL](sql-database-managed-instance-configure-vnet-subnet.md) ou [criar uma rede virtual para a base de dados do Azure SQL instância gerida](sql-database-managed-instance-create-vnet-subnet.md).
- Uma instância gerida é criada na própria VNet com nenhum ponto final público. Para obter acesso de aplicação de cliente, pode **criar uma VM na mesma VNet (sub-rede diferente)** ou **criar uma ligação de VPN ponto a site para a VNet a partir do computador cliente** utilizando um dos seguintes Inícios rápidos:

  - Crie [Máquina Virtual do Azure na instância gerida VNet](sql-database-managed-instance-configure-vm.md) para conectividade de aplicativo de cliente, incluindo o SQL Server Management Studio.
  - Configurar [ligação de VPN ponto a site para a sua instância gerida](sql-database-managed-instance-configure-p2s.md) partir do computador cliente em que tiver o SQL Server Management Studio e outros aplicativos de conectividade do cliente. Este é outro das duas opções para a conectividade à sua instância gerida e a sua VNet.

  > [!NOTE]
  > Também pode utilizar o expressroute ou de ligação site a site da rede local, mas essas abordagens estão fora do âmbito dos inícios Rápidos seguintes.

### <a name="migrate-your-databases"></a>Migre bases de dados

Depois de criar uma instância gerida e configurar o acesso, pode começar a migrar as bases de dados do SQL Server no local ou VMs do Azure. Migração falha se tiver algumas funcionalidades não suportadas na base de dados origem que pretende migrar. Para evitar falhas e verificar a compatibilidade, pode instalar [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) que analisa as bases de dados no SQL Server e encontra qualquer problema que poderia bloquear a migração para uma instância gerida, por exemplo, a existência de [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) ou vários ficheiros de registo. Se resolver estes problemas, as bases de dados estão prontos para migrar para a instância gerida. [Base de dados de Assistente de experimentação](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) é outra ferramenta útil que pode gravar sua carga de trabalho no SQL Server e de repetição-lo numa instância gerida para que possa determinar estarão disponíveis para ser problemas de desempenho se migrar para uma instância gerida.

Assim que tiver a certeza de que pode migrar a base de dados para uma instância gerida, pode utilizar os recursos nativos de restauro do SQL Server para restaurar uma base de dados para uma instância gerida de uma `.bak` ficheiro. Pode utilizar este método para migrar bases de dados do SQL Server da base de dados motor instalado no local ou VM do Azure. Para um guia de introdução, consulte [restaurar a partir de cópia de segurança para uma instância gerida](sql-database-managed-instance-get-started-restore.md). Neste início rápido, restaurar a partir de um `.bak` ficheiros armazenados no armazenamento de Blobs do Azure com o `RESTORE` comando do Transact-SQL.

> [!TIP]
> Para utilizar o `BACKUP` comando do Transact-SQL para criar uma cópia de segurança da base de dados no armazenamento de Blobs do Azure, consulte [cópia de segurança do SQL Server para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Estes inícios rápidos permitem-lhe rapidamente criar, configurar e restaurar a cópia de segurança da base de dados para uma instância gerida. Em alguns cenários, precisaria personalizar ou automatizar a implementação de instâncias geridas e o ambiente de rede necessário. Estes cenários serão descritos abaixo.

## <a name="customize-network-environment"></a>Personalizar o ambiente de rede

Embora a VNet/sub-rede podem ser configurada automaticamente quando a instância é criada usando [do portal do Azure](sql-database-managed-instance-get-started.md), talvez seja bom para criá-lo antes de começar a criar instâncias geridas porque pode configurar os parâmetros da VNet e a sub-rede. A maneira mais fácil para criar e configurar o ambiente de rede é usar [implementação de recursos do Azure](sql-database-managed-instance-create-vnet-subnet.md) modelo que irá criar e configurar a rede e a sub-rede onde a instância será colocada. Basta pressionar o Azure Resource Manager botão implementar e preencher o formulário com parâmetros.

Como alternativa, pode utilizar [script do PowerShell](https://www.powershellmagazine.com/20../../configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Como alternativa, pode também utilizar isso [script do PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Se já tiver uma VNet e sub-rede onde pretende implementar a sua instância gerida, terá de certificar-se de que a sua VNet e sub-rede satisfazem os [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Utilize esta opção [script do PowerShell para verificar se a sub-rede está configurada corretamente](sql-database-managed-instance-configure-vnet-subnet.md). Este script valida a sua rede e relatórios quaisquer problemas e ele diz a o que deve ser alterado e, em seguida, ofertas para fazer as alterações necessárias na sua VNet/subrede. Se não quiser configurar a sua VNet/sub-rede manualmente, execute este script. Também pode executá-lo Depois de qualquer grandes Reconfigurações de sua infraestrutura de rede. Se quiser criar e configurar a sua própria rede, leia [arquitetura de conectividade](sql-database-managed-instance-connectivity-architecture.md) e isso[derradeiro guia para criar e configurar um ambiente de instância gerida](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-a-managed-instance"></a>Automatizar a criação de uma instância gerida

 Se não tiver criado o ambiente de rede conforme descrito no passo anterior, o portal do Azure pode fazê-lo para – o único inconveniente é o fato de que ele configura com alguns parâmetros de predefinição que não é possível alterar mais tarde. Como alternativa, pode utilizar:

- [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
- [PowerShell com o modelo do Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [CLI do Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>Migrar para uma instância gerida com o tempo de inatividade mínimo

Artigos nestes inícios rápidos permitem-lhe configurar uma instância gerida e mover as bases de dados nativa a utilizar rapidamente `RESTORE` capacidade. No entanto, com nativa `RESTORE`, terá de aguardar que as bases de dados ser restaurado (e copiados para o armazenamento de Blobs do Azure se não for já armazenadas nele). Isso faz com que algum período de inatividade do seu aplicativo especialmente para bases de dados maiores. Para mover a base de dados de produção, utilize o [serviço de migração de dados (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) para migrar a sua base de dados com o período de indisponibilidade mínimo. O DMS faz isso ao incrementalmente enviar as alterações efetuadas na base de dados de origem para a base de dados de instância gerida a ser restaurada. Dessa forma, pode alternar rapidamente seu aplicativo de origem para a base de dados de destino com o período de indisponibilidade mínimo.

## <a name="next-steps"></a>Passos Seguintes

- Encontrar um [lista de alto nível das funcionalidades suportadas na instância gerida aqui](sql-database-features.md) e [detalhes e problemas conhecidos aqui](sql-database-managed-instance-transact-sql-information.md).
- Saiba mais sobre [características técnicas de instância gerida](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).
- Encontre mais avançadas como-para [como utilizar uma instância gerida na base de dados do Azure SQL](sql-database-howto-managed-instance.md).
