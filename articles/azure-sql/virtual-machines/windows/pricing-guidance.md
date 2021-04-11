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
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d980b92bc4effc58ef84ef6ec70c3908f575e484
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102615606"
---
# <a name="pricing-guidance-for-sql-server-on-azure-vms"></a>Orientação de preços para O SQL Server em VMs Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece orientação de preços para [o SQL Server em Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md). Existem várias opções que afetam o custo, e é importante escolher a imagem certa que equilibra os custos com os requisitos do negócio.

> [!TIP]
> Se necessitar apenas de uma estimativa de custos para uma combinação específica de edição SQL Server e tamanho da máquina virtual (VM), consulte a página de preços do [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Selecione a sua plataforma e edição SQL Server da lista **DE OS/Software.**
>
> ![UI na página de preços VM](./media/pricing-guidance/virtual-machines-pricing-ui.png)
>
> Ou use [a calculadora de preços](https://azure.microsoft.com/pricing/#explore-cost) para adicionar e configurar uma máquina virtual. 

## <a name="free-licensed-sql-server-editions"></a>Edições sql server licenciadas gratuitamente

Se quiser desenvolver, testar ou construir uma prova de conceito, use a edição de desenvolvedores de **servidores SQL** livremente licenciada . Esta edição tem todas as funcionalidades da edição SQL Server Enterprise, permitindo-lhe construir e testar qualquer tipo de aplicação. No entanto, não é possível executar a edição do Developer em produção. Uma edição VM do SqL Server Developer apenas incorre em encargos pelo custo do VM, porque não existem custos de licenciamento associados do SQL Server.

Se pretender executar uma carga de trabalho leve na produção (<4 núcleos, <memória de 1 GB, <10 GB/base de dados), utilize a **edição SQL Server Express** livremente licenciada . Um SQL Server Express edição VM também só incorre em encargos pelo custo do VM.

Para estas cargas de trabalho de produção de desenvolvimento/teste e leve, também pode economizar dinheiro escolhendo um tamanho VM menor que corresponda a estas cargas de trabalho. O DS1v2 pode ser uma boa escolha em alguns cenários.

Para criar um Azure VM em execução SQL Server 2017 com uma destas imagens, consulte os seguintes links:

| Plataforma | Imagens livremente licenciadas |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Desenvolvedor Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Desenvolvedor Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Desenvolvedor Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Desenvolvedor Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Edições pagas do SQL Server

Se tiver uma carga de trabalho de produção não leve, utilize uma das seguintes edições SQL Server:

| Edição do SQL Server | Carga de trabalho |
|-----|-----|
| Web | Pequenos sites |
| Standard | Cargas de trabalho pequenas a médias |
| Grandes Empresas | Cargas de trabalho grandes ou críticas de missão|

Você tem duas opções para pagar o licenciamento SQL Server para estas edições: *pagar por utilização* ou *trazer a sua própria licença (BYOL)*.

## <a name="pay-per-usage"></a>Pagamento por utilização

**Pagar a licença SQL Server por utilização** (também conhecida como pagamento à **medida que vai)** significa que o custo por segundo de execução do VM Azure inclui o custo da licença SQL Server. Pode ver os preços das diferentes edições SQL Server (Web, Standard, Enterprise) na página de preços das Máquinas Virtuais Azure para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

O custo é o mesmo para todas as versões do SQL Server (2012 SP3 a 2019). O custo de licenciamento por segundo depende do número de VM vCPUs.

Recomenda-se o pagamento do licenciamento do SQL Server por utilização para:

- **Cargas temporárias ou periódicas.** Por exemplo, uma aplicação que precisa de suportar um evento durante alguns meses todos os anos, ou análise de negócios às segundas-feiras.

- **Cargas de trabalho com vida ou escala desconhecidas.** Por exemplo, uma aplicação que pode não ser necessária em poucos meses, ou que pode exigir mais, ou menos poder de computação, dependendo da procura.

Para criar um Azure VM em execução SQL Server 2017 com uma destas imagens pay-as-you-go, consulte os seguintes links:

| Plataforma | Imagens licenciadas |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Quando cria uma máquina virtual SQL Server no portal Azure, a janela **Escolha um tamanho** mostra um custo estimado. É importante notar que esta estimativa são apenas os custos de cálculo para a execução do VM, juntamente com quaisquer custos de licenciamento de SO (sistemas operativos Windows ou linux de terceiros).
>
> ![Escolha a lâmina do tamanho VM](./media/pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Não inclui custos adicionais de licenciamento do SQL Server para as edições Web, Standard e Enterprise. Para obter a estimativa de preços mais precisa, selecione o seu sistema operativo e a edição do SQL Server na página de preços do [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Agora é possível alterar o modelo de licenciamento de pay-per-use para trazer a sua própria licença (BYOL) e de volta. Para obter mais informações, consulte [como alterar o modelo de licenciamento para um SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a> Traga a sua própria licença (BYOL)

**Trazer a sua própria licença SQL Server através da Mobility License**, também designada por **BYOL,** significa utilizar uma licença de volume de servidor SQL existente com garantia de software num VM Azure. Um SQL Server VM que utiliza apenas o BYOL cobra apenas pelo custo de execução do VM, e não pelo licenciamento do SQL Server, uma vez que já adquiriu licenças e Garantia de Software através de um programa de licenciamento de volume ou através de um Parceiro de Solução Cloud (CSP).

> [!NOTE]
> Atualmente, as imagens BYOL só estão disponíveis para máquinas virtuais do Windows. No entanto, pode instalar manualmente o SQL Server num VM apenas para Linux. Consulte as diretrizes do [SQL Server numa FAQ Linux VM](../linux/frequently-asked-questions-faq.md).

É aconselhável levar o seu próprio licenciamento do SQL Server através da Mobilidade de Licenças para:

- **Cargas de trabalho contínuas.** Por exemplo, uma app que precisa de suportar operações empresariais 24x7.

- **Cargas de trabalho com vida e escala conhecidas.** Por exemplo, uma aplicação que é necessária para todo o ano e que a procura foi prevista.

Para utilizar o BYOL com um SQL Server VM, tem de ter uma licença para SQL Server Standard ou Enterprise and [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), que é uma opção necessária através de alguns programas de licenciamento de volume e uma compra opcional com outros. O nível de preços fornecido através dos programas de licenciamento de volume varia, com base no tipo de acordo e na quantidade e ou compromisso com o SQL Server. Mas, como regra geral, trazer a sua própria licença para cargas de trabalho contínuas de produção tem os seguintes benefícios:

| Benefício BYOL | Description |
|-----|-----|
| **Poupança de custos** | O [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) oferece até 55% de poupança. Para mais informações, consulte [o modelo de licenciamento da Switch](licensing-model-azure-hybrid-benefit-ahb-change.md) |
| **Réplica secundária passiva livre** | Outro benefício de trazer a sua própria licença é o [licenciamento gratuito para uma réplica secundária passiva](https://azure.microsoft.com/pricing/licensing-faq/) por SQL Server para fins de alta disponibilidade. Isto reduz para metade o custo de licenciamento de uma implementação altamente disponível do SQL Server (por exemplo, usando sempre grupos de disponibilidade). Os direitos de execução do secundário passivo são fornecidos através do benefício Fail-Over Servers Software Assurance. |

Para criar um Azure VM em execução SQL Server 2017 com uma destas imagens de trazer a sua própria licença, consulte os VMs prefixados com "{BYOL}":

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Informe-nos dentro de 10 dias quantas licenças sql Server está a usar em Azure. Os links para as imagens anteriores têm instruções sobre como fazê-lo.

> [!NOTE]
> Agora é possível alterar o modelo de licenciamento de pay-per-use para trazer a sua própria licença (BYOL) e de volta. Para obter mais informações, consulte [como alterar o modelo de licenciamento para um SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 



## <a name="reduce-costs"></a>Reduzir custos

Para evitar custos desnecessários, escolha um tamanho de máquina virtual ideal e considere paragens intermitentes para cargas de trabalho não contínuas.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a> Dimensione corretamente o seu VM

O custo de licenciamento do SQL Server está diretamente relacionado com o número de vCPUs. Escolha um tamanho VM que corresponda às necessidades esperadas para CPU, memória, armazenamento e largura de banda de I/O. Para obter uma lista completa das opções de tamanho da máquina, consulte [os tamanhos do Windows VM](../../../virtual-machines/sizes.md) e [os tamanhos Linux VM](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Existem novos tamanhos de máquina que funcionam bem com certos tipos de cargas de trabalho do SQL Server. Estes tamanhos de máquinas mantêm altos níveis de memória, armazenamento e largura de banda de I/S, mas têm uma contagem de núcleo virtualizada mais baixa. Por exemplo, considere o seguinte exemplo:

| Tamanho da VM | vCPUs | Memória | Máximo de discos | Débito máximo de E/S | Custos de licenciamento do SQL Server | Custos totais (cálculo + licenciamento) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 IOPS ou 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51 200 IOPS ou 768 MB/s | Menos 75% | Menos 57% |

> [!IMPORTANT]
> Este é um exemplo pontual. Para as especificações mais recentes, consulte os artigos de tamanho da máquina e a página de preços do Azure para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

No exemplo anterior, pode ver-se que as especificações para **Standard_DS14v2** e **Standard_DS14-4v2** são idênticas, com exceção dos vCPUs. O sufixo **-4v2** no final do tamanho da máquina **Standard_DS14-4v2** indica o número de vCPUs ativos. Dado que os custos de licenciamento do SQL Server estão ligados ao número de vCPUs, isso reduz significativamente o custo do VM em cenários em que os vCPUs extra não são necessários. Este é um exemplo, e há muitos tamanhos de máquina com vCPUs limitados que são identificados com este padrão de sufixo. Para obter mais informações, consulte o post de blog [Anunciando novos tamanhos Azure VM para um trabalho de base de dados mais rentável.](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)

### <a name="shut-down-your-vm-when-possible"></a>Desligue o seu VM quando possível

Se estiver a utilizar cargas de trabalho que não corram continuamente, considere desligar a máquina virtual durante os períodos inativos. Só paga o que utilizar.

Por exemplo, se estiver simplesmente a experimentar o SQL Server num Azure VM, não pretende incorrer em cargas deixando-o acidentalmente a funcionar durante semanas. Uma solução é utilizar a [função de paragem automática](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Autoshutdown do servidor SQL VM](./media/pricing-guidance/sql-vm-auto-shutdown.png)

A paragem automática faz parte de um conjunto maior de funcionalidades semelhantes fornecidas pela [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Para outros fluxos de trabalho, considere desligar automaticamente e reiniciar os VMs Azure com uma solução de script, como [a Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Fechar e negociar o seu VM é a única forma de evitar acusações. Basta parar ou utilizar opções de potência para desligar o VM ainda incorre em cargas de utilização.

## <a name="next-steps"></a>Passos seguintes

Para orientação geral sobre preços da Azure, consulte [Prevenir custos inesperados com faturação da Azure e gestão de custos](../../../cost-management-billing/cost-management-billing-overview.md). Para obter os preços mais recentes das Máquinas Virtuais Azure, incluindo o SQL Server, consulte a página de preços das máquinas virtuais Azure para [VMs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) do Windows e [VMs Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Para uma visão geral do SQL Server em Azure Virtual Machines, consulte os seguintes artigos:

- [Visão geral do SQL Server em VMs windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Visão geral do SQL Server em Linux VMs](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
