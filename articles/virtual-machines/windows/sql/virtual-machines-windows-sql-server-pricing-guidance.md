---
title: Diretrizes de preço & gerenciar custos
description: Fornece as práticas recomendadas para escolher o modelo de preços certo SQL Server máquina virtual.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 076d6fc387aaee85a1cd407fa48e7347ff185ef4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038863"
---
# <a name="pricing-guidance-for-azure-sql-server-vms"></a>Diretrizes de preços para VMs de SQL Server do Azure

Este artigo fornece diretrizes de preços para [SQL Server máquinas virtuais](virtual-machines-windows-sql-server-iaas-overview.md) no Azure. Há várias opções que afetam o custo, e é importante escolher a imagem certa que equilibra os custos com os requisitos de negócios.

> [!TIP]
> Se você só precisa descobrir uma estimativa de custo para uma combinação específica de SQL Server edição e tamanho da máquina virtual, consulte a página de preços para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Selecione sua plataforma e a edição do SQL Server na lista de **so/software** .
>
> ![Página de preços da interface do usuário na VM](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Ou use a [calculadora de preços](https://azure.microsoft.com/pricing/#explore-cost) para adicionar e configurar uma máquina virtual. 

## <a name="free-licensed-sql-server-editions"></a>Edições de SQL Server licenciadas gratuitamente

Se você quiser desenvolver, testar ou criar uma prova de conceito, use a **edição de SQL Server Developer**licenciada gratuitamente. Esta edição tem todos os recursos do SQL Server Enterprise Edition, permitindo que você crie e teste qualquer tipo de aplicativo. No entanto, você não pode executar a Developer Edition em produção. Uma VM de edição SQL Server Developer apenas incorre em encargos pelo custo da VM, porque não há nenhum custo de licenciamento associado SQL Server.

Se você quiser executar uma carga de trabalho leve em produção (< 4 núcleos, < memória de 1 GB, < 10 GB/banco de dados), use a **edição de SQL Server Express**licenciada gratuitamente. Uma VM do SQL Server Express Edition também incorre em encargos pelo custo da VM.

Para essas cargas de trabalho de desenvolvimento/teste e de produção leves, você também pode economizar dinheiro escolhendo um tamanho de VM menor que corresponda a essas cargas de trabalho. O DS1v2 pode ser uma boa opção em alguns cenários.

Para criar uma VM do Azure SQL Server 2017 com uma dessas imagens, consulte os links a seguir:

| Plataforma | Imagens licenciadas livremente |
|---|---|
| Windows Server 2016 | [VM do Azure para o SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[VM do Azure para SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [VM do Azure para o SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[VM do Azure para SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [VM do Azure para o SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[VM do Azure para SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [VM do Azure para o SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[VM do Azure para SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Edições de SQL Server pagas

Se você tiver uma carga de trabalho de produção não leve, use uma das seguintes SQL Server edições:

| SQL Server Edition | Carga de trabalho |
|-----|-----|
| Web | Sites pequenos |
| Standard | Cargas de trabalho pequenas a médias |
| Enterprise | Cargas de trabalho grandes ou de missão crítica|

Você tem duas opções para pagar SQL Server licenciamento para essas edições: *pague por uso* ou *traga sua própria licença (BYOL)* .

## <a name="pay-per-usage"></a>Pagar por uso

**Pagar a licença de SQL Server por uso** (também conhecido como **pré-pago**) significa que o custo por segundo da execução da VM do Azure inclui o custo da licença de SQL Server. Você pode ver os preços das diferentes SQL Server edições (Web, Standard, Enterprise) na página de preços de VM do Azure para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

O custo é o mesmo para todas as versões do SQL Server (2012 SP3 a 2017). O custo por segundo de licenciamento depende do número de vCPUs de VM.

O pagamento do SQL Server licenciamento por uso é recomendado para:

- **Cargas de trabalho temporárias ou periódicas**. Por exemplo, um aplicativo que precisa dar suporte a um evento por alguns meses todos os anos ou análise de negócios nas segundas-feiras.

- **Cargas de trabalho com tempo de vida ou escala desconhecidos**. Por exemplo, um aplicativo que pode não ser necessário em alguns meses ou que pode exigir mais ou menos poder de computação, dependendo da demanda.

Para criar uma VM do Azure SQL Server 2017 com uma dessas imagens pagas conforme o uso, consulte os links a seguir:

| Plataforma | Imagens licenciadas |
|---|---|
| Windows Server 2016 | [VM do Azure para a SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[VM do Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [VM do Azure para a SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[VM do Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [VM do Azure para a SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[VM do Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [VM do Azure para a SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[VM do Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Quando você cria um SQL Server máquina virtual no portal, a janela **escolher um tamanho** mostra um custo estimado. É importante observar que essa estimativa é apenas os custos de computação para executar a VM junto com os custos de licenciamento do sistema operacional (Windows ou sistemas operacionais Linux de terceiros).
>
> ![Escolha a folha tamanho da VM](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Ele não inclui custos adicionais de licenciamento SQL Server para as edições Web, Standard e Enterprise. Para obter a estimativa de preços mais precisa, selecione seu sistema operacional e SQL Server Edition na página de preços para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Agora é possível alterar o modelo de licenciamento de pagamento por uso para trazer sua própria licença (BYOL) e de volta. Para obter mais informações, consulte [como alterar o modelo de licenciamento para uma VM do SQL](virtual-machines-windows-sql-ahb.md). 

## <a id="byol"></a>BYOL (traga sua própria licença)

**Trazer sua própria licença de SQL Server por meio de mobilidade de licenças**, também conhecido como **BYOL**, significa usar uma licença de volume SQL Server existente com o Software Assurance em uma VM do Azure. Uma VM SQL Server usando BYOL apenas cobra pelo custo da execução da VM, não pelo licenciamento de SQL Server, Considerando que você já tenha adquirido licenças e Software Assurance por meio de um programa de licenciamento por volume.

> [!IMPORTANT]
> As imagens BYOL exigem um Enterprise Agreement com o Software Assurance. Eles não estão disponíveis como parte do parceiro de solução de nuvem do Azure (CSP) no momento. Os clientes do CSP podem trazer sua própria licença implantando uma imagem paga conforme o uso e, em seguida, habilitando o [benefício híbrido do Azure](virtual-machines-windows-sql-ahb.md).

> [!NOTE]
> As imagens BYOL estão atualmente disponíveis apenas para máquinas virtuais do Windows. No entanto, você pode instalar manualmente SQL Server em uma VM somente Linux. Consulte as diretrizes nas [perguntas frequentes sobre VM do SQL do Linux](../../linux/sql/sql-server-linux-faq.md).

É recomendável trazer seu próprio licenciamento do SQL por meio de Mobilidade de Licenças para:

- **Cargas de trabalho contínuas**. Por exemplo, um aplicativo que precisa dar suporte a operações de negócios 24x7.

- **Cargas de trabalho com tempo de vida e escala conhecidos**. Por exemplo, um aplicativo que é necessário para o ano inteiro e qual demanda foi prevista.

Para usar o BYOL com uma VM SQL Server, você deve ter uma licença para SQL Server Standard ou Enterprise e [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), que é uma opção necessária por meio de alguns programas de licenciamento por volume e uma compra opcional com outras pessoas. O nível de preço fornecido por meio de programas de licenciamento por volume varia, com base no tipo de contrato e na quantidade e no compromisso com a SQL Server. Mas, como regra prática, trazer sua própria licença para cargas de trabalho de produção contínuas tem os seguintes benefícios:

| Benefício do BYOL | Descrição |
|-----|-----|
| **Economia de custos** | A [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) oferece economia de até 55%. Para obter mais informações, consulte [alternar modelo de licenciamento](virtual-machines-windows-sql-ahb.md) |
| **Réplica secundária passiva gratuita** | Outro benefício de trazer sua própria licença é o [Licenciamento gratuito para uma réplica secundária passiva](https://azure.microsoft.com/pricing/licensing-faq/) por SQL Server para fins de alta disponibilidade. Isso reduz a metade do custo de licenciamento de uma implantação de SQL Server altamente disponível (por exemplo, usando grupos de disponibilidade Always On). Os direitos para executar o secundário passivo são fornecidos por meio do benefício do Software Assurance de servidores de failover. |

Para criar uma VM do Azure SQL Server 2017 com uma dessas imagens traga sua própria licença, consulte as VMs com o prefixo "{BYOL}":

- [VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [VM do Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Informe-nos em até 10 dias quantas licenças de SQL Server você está usando no Azure. Os links para as imagens anteriores têm instruções sobre como fazer isso.

> [!NOTE]
> Agora é possível alterar o modelo de licenciamento de pagamento por uso para trazer sua própria licença (BYOL) e de volta. Para obter mais informações, consulte [como alterar o modelo de licenciamento para uma VM do SQL](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Reduzir custos

Para evitar custos desnecessários, escolha um tamanho de máquina virtual ideal e considere desligamentos intermitentes para cargas de trabalho não contínuas.

### <a id="machinesize"></a>Dimensionar corretamente sua VM

O custo de licenciamento de SQL Server está diretamente relacionado ao número de vCPUs. Escolha um tamanho de VM que corresponda às suas necessidades esperadas de CPU, memória, armazenamento e largura de banda de e/s. Para obter uma lista completa das opções de tamanho de máquina, consulte [tamanhos de VM do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) e tamanhos de VM do [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Há novos tamanhos de máquina que funcionam bem com determinados tipos de cargas de trabalho de SQL Server. Esses tamanhos de máquinas mantêm altos níveis de memória, armazenamento e largura de banda de e/s, mas têm uma contagem de núcleos virtualizados mais baixo. Por exemplo, considere o seguinte exemplo:

| Tamanho da VM | vCPUs | Memória | Máximo de discos | Taxa de transferência de e/s máxima | Custos de licenciamento do SQL | Custos totais (computação + licenciamento) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51.200 IOPS ou 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51.200 IOPS ou 768 MB/s | 75% inferior | 57% inferior |

> [!IMPORTANT]
> Este é um exemplo de ponto no tempo. Para obter as especificações mais recentes, consulte os artigos sobre tamanhos de máquina e a página de preços do Azure para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

No exemplo anterior, você pode ver que as especificações para **Standard_DS14v2** e **Standard_DS14-4V2** são idênticas, exceto para vCPUs. O sufixo **-4V2** ao final do tamanho da máquina **Standard_DS14-4V2** indica o número de vCPUs ativos. Como os custos de licenciamento SQL Server estão vinculados ao número de vCPUs, isso reduz significativamente o custo da VM em cenários em que os vCPUs extras não são necessários. Este é um exemplo, e há muitos tamanhos de máquina com vCPUs restritas que são identificados com esse padrão de sufixo. Para obter mais informações, consulte a postagem de blog [anunciando novos tamanhos de VM do Azure para um trabalho de banco de dados mais](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)econômico.

### <a name="shut-down-your-vm-when-possible"></a>Desligar sua VM quando possível

Se você estiver usando qualquer carga de trabalho que não seja executada continuamente, considere desligar a máquina virtual durante os períodos inativos. Só paga o que utilizar.

Por exemplo, se você estiver simplesmente experimentando SQL Server em uma VM do Azure, você não desejará incorrer em encargos deixando-o acidentalmente em execução por semanas. Uma solução é usar o [recurso de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Desligamento automático da VM do SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

O desligamento automático faz parte de um conjunto maior de recursos semelhantes fornecidos pelo [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Para outros fluxos de trabalho, considere desligar e reiniciar automaticamente as VMs do Azure com uma solução de script, como a [automação do Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Desligar e desalocar sua VM é a única maneira de evitar encargos. Simplesmente parar ou usar as opções de energia para desligar a VM ainda incorre em encargos de uso.

## <a name="next-steps"></a>Passos seguintes

Para obter diretrizes gerais de preços do Azure, confira [evitar custos inesperados com cobrança e gerenciamento de custos do Azure](../../../billing/billing-getting-started.md). Para obter os preços das máquinas virtuais mais recentes, incluindo SQL Server, consulte a página de preços do Azure da VM do Azure para [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e VMs do [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Para obter uma visão geral do SQL Server em execução em máquinas virtuais do Azure, consulte os seguintes artigos:

- [Visão geral de SQL Server em VMs do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Visão geral das VMs SQL Server em Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
