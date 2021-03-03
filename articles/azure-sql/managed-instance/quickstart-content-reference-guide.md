---
title: Começar a referência de conteúdo
titleSuffix: Azure SQL Managed Instance
description: 'Uma referência para conteúdos que o ajudam a começar com a Azure SQL Managed Instance. '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: c84c81fa81cf4ba9be454eea5782927a09c3252a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690969"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Começando com Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[A Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) cria uma base de dados com quase 100% de compatibilidade com o mais recente motor de base de dados SQL Server (Enterprise Edition), fornecendo uma implementação de rede virtual nativa [(VNet)](../../virtual-network/virtual-networks-overview.md) que aborda preocupações de segurança comuns, e um [modelo de negócio](https://azure.microsoft.com/pricing/details/sql-database/) favorável aos clientes existentes do SQL Server.

Neste artigo, encontrará referências a conteúdos que o ensinam a configurar rapidamente e criar uma SQL Managed Instance e migrar as suas bases de dados.

## <a name="quickstart-overview"></a>Descrição geral do Início Rápido

Os seguintes quickstarts permitem-lhe criar rapidamente uma SQL Managed Instance, configurar uma máquina virtual ou apontar para a ligação VPN do site para aplicação do cliente, e restaurar uma base de dados para a sua nova SQL Managed Instance usando um `.bak` ficheiro.

### <a name="configure-environment"></a>Configurar o ambiente

Como primeiro passo, você precisaria criar a sua primeira SQL Managed Instance com o ambiente de rede onde será colocada, e ativar a ligação a partir do computador ou máquina virtual onde você está executando consultas para SQL Managed Instance. Pode utilizar os seguintes guias:

- [Criar uma sql Gestd Instance utilizando o portal Azure](instance-create-quickstart.md). No portal Azure, configura os parâmetros necessários (nome de utilizador/palavra-passe, número de núcleos e quantidade máxima de armazenamento) e cria automaticamente o ambiente da rede Azure sem a necessidade de saber sobre detalhes de rede e requisitos de infraestrutura. Certifique-se apenas de que tem um [tipo de subscrição](resource-limits.md#supported-subscription-types) que é atualmente permitido criar uma SQL Managed Instance. Se tiver a sua própria rede que pretende utilizar ou quiser personalizar a rede, consulte [a configuração de uma rede virtual existente para Azure SQL Managed Instance](vnet-existing-add-subnet.md) ou [criar uma rede virtual para Azure SQL Managed Instance](virtual-network-subnet-create-arm-template.md).
- Um SQL Managed Instance é criado no seu próprio VNet sem ponto final público. Para o acesso à aplicação do cliente, pode **criar um VM na mesma VNet (sub-rede diferente)** ou **criar uma ligação VPN ponto a local ao VNet a partir do computador do seu cliente** utilizando um destes arranques rápidos:
  - Ative [o ponto final público](public-endpoint-configure.md) na sua SQL Managed Instance para aceder aos seus dados diretamente do seu ambiente.
  - Crie [a máquina virtual Azure no SQL Managed Instance VNet](connect-vm-instance-configure.md) para conectividade de aplicações de clientes, incluindo o SQL Server Management Studio.
  - Configurar [a ligação VPN ponto a local ao seu SQL Managed Instance](point-to-site-p2s-configure.md) a partir do seu computador cliente no qual tem SQL Server Management Studio e outras aplicações de conectividade do cliente. Esta é outra de duas opções de conectividade com o seu SQL Managed Instance e com o seu VNet.

  > [!NOTE]
  > - Também pode utilizar a rota expressa ou a ligação site-to-site a partir da sua rede local, mas estas abordagens estão fora do âmbito destes arranques rápidos.
  > - Se alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, note que a retenção só se aplicará aos registos escritos após a alteração do valor da retenção (os registos escritos durante o período em que a retenção foi definida para ilimitada são preservados, mesmo após a retenção ser ativada).

Como alternativa à criação manual de SQL Managed Instance, pode utilizar [o PowerShell](scripts/create-configure-managed-instance-powershell.md), [PowerShell com o modelo de Gestor de Recursos,](./create-template-quickstart.md)ou [Azure CLI](/cli/azure/sql/mi#az-sql-mi-create) para scriptar e automatizar este processo.

### <a name="migrate-your-databases"></a>Migrar as suas bases de dados

Depois de criar um SQL Managed Instance e configurar o acesso, pode começar a migrar as bases de dados do SQL Server. A migração pode falhar se tiver algumas funcionalidades não apoiadas na base de dados de origem que pretende migrar. Para evitar falhas e verificar a compatibilidade, pode utilizar [o Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) para analisar as suas bases de dados no SQL Server e encontrar qualquer problema que possa bloquear a migração para uma SqL Managed Instance, como a existência de [FicheiroStream](/sql/relational-databases/blob/filestream-sql-server) ou vários ficheiros de registo. Se resolver estes problemas, as suas bases de dados estão prontas para migrar para a SQL Managed Instance. [O Assistant de Experimentação de Base de Dados](/sql/dea/database-experimentation-assistant-overview) é outra ferramenta útil que pode gravar a sua carga de trabalho no SQL Server e reproduzi-la numa SQL Managed Instance para que possa determinar se haverá problemas de desempenho se migrar para uma SqL Managed Instance.

Uma vez que tenha a certeza de que pode migrar a sua base de dados para uma SqL Managed Instance, pode utilizar as capacidades de restauro do SQL Server nativo para restaurar uma base de dados numa SqL Managed Instance a partir de um `.bak` ficheiro. Pode utilizar este método para migrar bases de dados a partir do motor de base de dados SQL Server instalado no local ou máquinas virtuais Azure. Para um arranque rápido, consulte [Restaurar de backup para uma SQL Managed Instance](restore-sample-database-quickstart.md). Neste arranque rápido, restaura-se a partir de um `.bak` ficheiro armazenado no armazenamento Azure Blob utilizando o comando `RESTORE` Transact-SQL.

> [!TIP]
> Para utilizar o `BACKUP` comando Transact-SQL para criar uma cópia de segurança da sua base de dados no armazenamento Azure Blob, consulte [a cópia de segurança do SQL Server para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Estes quickstarts permitem-lhe criar, configurar e restaurar a cópia de segurança da base de dados para uma SqL Managed Instance. Em alguns cenários, você precisaria personalizar ou automatizar a implementação de SQL Managed Instance e o ambiente de networking necessário. Estes cenários serão descritos abaixo.

## <a name="customize-network-environment"></a>Personalizar ambiente de rede

Embora o VNet/sub-rede possa ser configurado automaticamente quando o caso é [criado usando o portal Azure,](instance-create-quickstart.md)pode ser bom criá-lo antes de começar a criar instâncias em SQL Managed Instance porque pode configurar os parâmetros de VNet e sub-rede. A forma mais fácil de criar e configurar o ambiente de rede é utilizar o modelo [de implementação de Recursos Azure](virtual-network-subnet-create-arm-template.md) que cria e configura a sua rede e sub-rede onde o caso será colocado. Basta premir o botão de implantação do Gestor de Recursos Azure e preencher o formulário com parâmetros.

Como alternativa, também pode utilizar este [script PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Se já tem um VNet e uma sub-rede onde gostaria de implementar a sua SQL Managed Instance, tem de se certificar de que o seu VNet e a sub-rede satisfazem os [requisitos de rede](connectivity-architecture-overview.md#network-requirements). Utilize este [script PowerShell para verificar se a sua sub-rede está corretamente configurada](vnet-existing-add-subnet.md). Este script valida a sua rede e relata quaisquer problemas, dizendo-lhe o que deve ser alterado e, em seguida, oferece-se para fazer as alterações necessárias na sua VNet/sub-rede. Execute este script se não quiser configurar manualmente o seu VNet/sub-rede. Também pode executá-lo após qualquer reconfiguração importante da sua infraestrutura de rede. Se quiser criar e configurar a sua própria rede, leia [a arquitetura de conectividade](connectivity-architecture-overview.md) e este guia final para criar e [configurar um ambiente sql Managed Instance](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-sql-managed-instance"></a>Migrar para uma sql caso gerido

Os quickstarts anteriormente mencionados permitem-lhe configurar rapidamente uma SQL Managed Instance e mover as suas bases de dados utilizando a `RESTORE` capacidade nativa. Este é um bom ponto de partida se quiser completar uma prova rápida de conceitos e verificar se a sua solução pode funcionar em Casos Geridos.

No entanto, para migrar a sua base de dados de produção ou mesmo dev/base de dados de teste que pretende utilizar para algum teste de desempenho, você precisa considerar a utilização de algumas técnicas adicionais, tais como:

- Testes de desempenho - Deve medir as métricas de desempenho de base na sua origem SQL Server e compará-las com as métricas de desempenho no destino SQL Managed Instance onde emigrou a base de dados. Saiba mais sobre as [melhores práticas para a comparação de desempenho.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)
- Migração on-line - Com o nativo `RESTORE` descrito neste artigo, você tem que esperar que as bases de dados sejam restauradas (e copiadas para armazenamento Azure Blob se ainda não estiver lá armazenado). Isto causa algum tempo de inatividade da sua aplicação, especialmente para bases de dados maiores. Para mover a sua base de dados de produção, utilize o [serviço de migração de dados (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) para migrar a sua base de dados com o tempo de inatividade mínimo. A DMS consegue-o empurrando gradualmente as alterações efetuadas na sua base de dados de origem para a base de dados SQL Managed Instance que está a ser restaurada. Desta forma, pode mudar rapidamente a sua aplicação de fonte para base de dados alvo com o mínimo de tempo de inatividade.

Saiba mais sobre o [processo de migração recomendado.](migrate-to-instance-from-sql-server.md)

## <a name="next-steps"></a>Passos seguintes

- Encontre aqui uma [lista de funcionalidades suportadas de alto nível em SQL Managed Instance](../database/features-comparison.md) e detalhes e [questões conhecidas aqui.](transact-sql-tsql-differences-sql-server.md)
- Conheça as [características técnicas da SQL Managed Instance](resource-limits.md#service-tier-characteristics).
- Encontre como fazer mais avançado em [como usar um SQL Managed Instance](how-to-content-reference-guide.md).
- [Identifique o SKU de Instância Gerida Azure SQL para a sua base de dados no local.](/sql/dma/dma-sku-recommend-sql-db/)