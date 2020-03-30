---
title: Orientação de preços & custos de gestão
description: Fornece as melhores práticas para escolher o modelo de preços de máquina virtual SQL Server certo.
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
ms.openlocfilehash: b7a8f7136d0899ab3afe04bce2d25bc2e7f8d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981720"
---
# <a name="pricing-guidance-for-azure-sql-server-vms"></a>Orientação de preços das VMs do SQL Server do Azure

Este artigo fornece orientação de preços para [máquinas virtuais SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) em Azure. Existem várias opções que afetam o custo, e é importante escolher a imagem certa que equilibra os custos com os requisitos do negócio.

> [!TIP]
> Se apenas precisar de descobrir uma estimativa de custos para uma combinação específica da edição do SQL Server e do tamanho da máquina virtual, consulte a página de preços para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Selecione a sua plataforma e edição do SQL Server na lista **OS/Software.**
>
> ![UI na página de preços vm](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Ou use a [calculadora](https://azure.microsoft.com/pricing/#explore-cost) de preços para adicionar e configurar uma máquina virtual. 

## <a name="free-licensed-sql-server-editions"></a>Edições SQL Server licenciadas gratuitamente

Se quiser desenvolver, testar ou construir uma prova de conceito, use a edição de Desenvolvedor de **Servidores SQL**livremente licenciada. Esta edição conta com todas as funcionalidades da edição Da SQL Server Enterprise, permitindo-lhe construir e testar qualquer tipo de aplicação. No entanto, não é possível executar a edição developer em produção. Uma edição De desenvolvedor de servidorS SQL VM só incorre em encargos pelo custo do VM, porque não existem custos associados de licenciamento do Servidor SQL.

Se pretender executar uma carga de trabalho leve em produção (<4 núcleos, <memória de 1-GB, <10 GB/base de dados), utilize a **edição Livremente licenciada do SQL Server Express**. Uma VM edição SQL Server Express também só incorre em encargos pelo custo do VM.

Para estas cargas de trabalho de desenvolvimento/teste e de produção leve, também pode economizar dinheiro escolhendo um tamanho VM menor que corresponda a estas cargas de trabalho. O DS1v2 pode ser uma boa escolha em alguns cenários.

Para criar um SQL Server 2017 Azure VM com uma destas imagens, consulte os seguintes links:

| Plataforma | Imagens livremente licenciadas |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Edições pagas do SQL Server

Se tiver uma carga de trabalho de produção não leve, utilize uma das seguintes edições do SQL Server:

| Edição do Servidor SQL | Carga de trabalho |
|-----|-----|
| Web | Pequenos sites |
| Standard | Cargas de trabalho pequenas a médias |
| Enterprise | Cargas de trabalho grandes ou críticas à missão|

Tem duas opções para pagar o licenciamento do SQL Server para estas edições: *pagar por utilização* ou *trazer a sua própria licença (BYOL)*.

## <a name="pay-per-usage"></a>Pagar por utilização

**Pagar a licença SQL Server por utilização** (também conhecido como **pagamento à medida que vai)** significa que o custo por segundo de funcionamento do Azure VM inclui o custo da licença SQL Server. Pode ver os preços das diferentes edições do SQL Server (Web, Standard, Enterprise) na página de preços Azure VM para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

O custo é o mesmo para todas as versões do SQL Server (2012 SP3 a 2017). O custo de licenciamento por segundo depende do número de VM vCPUs.

O pagamento do licenciamento do Servidor SQL por utilização é recomendado para:

- **Cargas de trabalho temporárias ou periódicas.** Por exemplo, uma app que precisa de suportar um evento durante alguns meses todos os anos, ou análise de negócios às segundas-feiras.

- **Cargas de trabalho com vida ou escala desconhecidas.** Por exemplo, uma aplicação que pode não ser necessária em poucos meses, ou que pode exigir mais, ou menos poder de computação, dependendo da procura.

Para criar um SQL Server 2017 Azure VM com uma destas imagens pay-as-you-go, consulte os seguintes links:

| Plataforma | Imagens licenciadas |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Quando cria uma máquina virtual SQL Server no portal, a janela **Choose a size** mostra um custo estimado. É importante notar que esta estimativa é apenas os custos do cálculo para executar o VM juntamente com quaisquer custos de licenciamento de OS (Windows ou sistemas operativos Linux de terceiros).
>
> ![Escolha a lâmina de tamanho VM](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Não inclui custos adicionais de licenciamento do SQL Server para edições Web, Standard e Enterprise. Para obter a estimativa de preços mais precisa, selecione o seu sistema operativo e edição do SQL Server na página de preços para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Agora é possível alterar o modelo de licenciamento do pay-per-usage para trazer a sua própria licença (BYOL) e de volta. Para mais informações, consulte [Como alterar o modelo de licenciamento para um SQL VM](virtual-machines-windows-sql-ahb.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a>Traga a sua própria licença (BYOL)

**Trazer a sua própria licença De Servidor SQL através**da Mobilidade de Licença , também referida como **BYOL**, significa usar uma licença de volume de servidor SQL existente com garantia de software num VM Azure. Um VM de servidor SQL que utiliza apenas byoL pelo custo de execução do VM, não para licenciamento do SQL Server, dado que já adquiriu licenças e Garantia de Software através de um programa de licenciamento de volume.

> [!IMPORTANT]
> As imagens BYOL requerem um Acordo de Empresa com garantia de software. Não estão disponíveis como parte do Azure Cloud Solution Partner (CSP) neste momento. Os clientes da CSP podem trazer a sua própria licença implantando uma imagem pay-as-you-go e, em seguida, permitindo o [Benefício Híbrido Azure](virtual-machines-windows-sql-ahb.md).

> [!NOTE]
> Atualmente, as imagens BYOL só estão disponíveis para máquinas virtuais do Windows. No entanto, pode instalar manualmente o SQL Server num VM exclusivamente linux. Consulte as diretrizes no [Linux SQL VM FAQ](../../linux/sql/sql-server-linux-faq.md).

É recomendado para:

- **Cargas de trabalho contínuas.** Por exemplo, uma app que precisa de apoiar operações comerciais 24x7.

- **Cargas de trabalho com vida e escala conhecidas.** Por exemplo, uma app que é necessária para todo o ano e que a procura foi prevista.

Para utilizar o BYOL com um VM de servidor SQL, deve ter uma licença para sQL Server Standard ou Enterprise and [Software Assurance,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1)que é uma opção necessária através de alguns programas de licenciamento de volume e uma compra opcional com outros. O nível de preços fornecido através de programas de licenciamento de volume varia, com base no tipo de acordo e na quantidade e ou compromisso com o Servidor SQL. Mas, como regra geral, trazer a sua própria licença para cargas de trabalho contínuas de produção tem os seguintes benefícios:

| Benefício BYOL | Descrição |
|-----|-----|
| **Poupança de custos** | O [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) oferece até 55% de poupança. Para mais informações, consulte o [modelo de licenciamento da Switch](virtual-machines-windows-sql-ahb.md) |
| **Réplica secundária passiva livre** | Outro benefício de trazer a sua própria licença é o [licenciamento gratuito para uma réplica secundária passiva](https://azure.microsoft.com/pricing/licensing-faq/) por SQL Server para fins de alta disponibilidade. Isto reduz para metade o custo de licenciamento de uma implementação altamente disponível do Servidor SQL (por exemplo, utilizando sempre grupos de disponibilidade). Os direitos de execução do secundário passivo são fornecidos através do benefício fail-over servers software assurance. |

Para criar um SQL Server 2017 Azure VM com uma destas imagens de trazer a sua própria licença, consulte os VMs pré-fixados com "{BYOL}":

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Informe-nos dentro de 10 dias quantas licenças do SQL Server está a usar no Azure. Os links para as imagens anteriores têm instruções sobre como fazê-lo.

> [!NOTE]
> Agora é possível alterar o modelo de licenciamento do pay-per-usage para trazer a sua própria licença (BYOL) e de volta. Para mais informações, consulte [Como alterar o modelo de licenciamento para um SQL VM](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Reduzir custos

Para evitar custos desnecessários, escolha um tamanho ideal da máquina virtual e considere as paragens intermitentes para cargas de trabalho não contínuas.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a>Tamanho correto do seu VM

O custo de licenciamento do SQL Server está diretamente relacionado com o número de vCPUs. Escolha um tamanho VM que corresponda às suas necessidades esperadas para CPU, memória, armazenamento e largura de banda De/S. Para obter uma lista completa de opções de tamanho da máquina, consulte os [tamanhos vM do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) e os [tamanhos de VM Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Existem novos tamanhos de máquina que funcionam bem com certos tipos de cargas de trabalho do SQL Server. Estes tamanhos de máquinas mantêm altos níveis de memória, armazenamento e largura de banda em I/S, mas têm uma contagem de núcleos virtualizada mais baixa. Por exemplo, considere o seguinte exemplo:

| Tamanho da VM | vCPUs | Memória | Discos Max | Max I/O Reput | Custos de licenciamento SQL | Custos totais (Compute + licenciamento) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 IOPS ou 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51 200 IOPS ou 768 MB/s | Menos 75% | Menos 57% |

> [!IMPORTANT]
> Este é um exemplo pontual. Para as especificações mais recentes, consulte os artigos de tamanho da máquina e a página de preços do Azure para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

No exemplo anterior, pode ver que as especificações para **Standard_DS14v2** e **Standard_DS14-4v2** são idênticas, exceto para vCPUs. O sufixo **-4v2** no final do tamanho da máquina **Standard_DS14-4v2** indica o número de vCPUs ativos. Dado que os custos de licenciamento do SQL Server estão ligados ao número de vCPUs, isto reduz significativamente o custo do VM em cenários em que os vCPUs extra não são necessários. Este é um exemplo, e há muitos tamanhos de máquina com vCPUs limitados que são identificados com este padrão de sufixo. Para mais informações, consulte a publicação de blogue [anunciando novos tamanhos de VM Azure para um trabalho de base de dados mais rentável.](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)

### <a name="shut-down-your-vm-when-possible"></a>Desligue o seu VM quando possível

Se estiver a utilizar cargas de trabalho que não funcionam continuamente, considere desligar a máquina virtual durante os períodos inativos. Só paga o que utilizar.

Por exemplo, se estiver simplesmente a experimentar o SQL Server num VM Azure, não quereria incorrer em cargas deixando-o acidentalmente a funcionar durante semanas. Uma solução é utilizar a [função](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)de paragem automática .

![Encerramento automático sQL VM](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

O encerramento automático faz parte de um conjunto maior de funcionalidades semelhantes fornecidas pela [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Para outros fluxos de trabalho, considere desligar e reiniciar automaticamente os VMs Azure com uma solução de script, como [a Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Fechar e negociar o seu VM é a única maneira de evitar acusações. Simplesmente parar ou usar opções de energia para desligar o VM ainda incorre em cargas de utilização.

## <a name="next-steps"></a>Passos seguintes

Para orientação geral de preços do Azure, consulte [Evitar custos inesperados com faturação Azure e gestão de custos.](../../../cost-management-billing/manage/getting-started.md) Para obter os preços mais recentes das Máquinas Virtuais, incluindo o SQL Server, consulte a página de preços Do Azure VM Azure para [VMs windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [VMs Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Para obter uma visão geral do Servidor SQL em execução em Máquinas Virtuais Azure, consulte os seguintes artigos:

- [Visão geral do Servidor SQL nos VMs do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Visão geral do Servidor SQL em VMs Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
