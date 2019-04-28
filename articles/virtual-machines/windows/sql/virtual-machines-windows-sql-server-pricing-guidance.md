---
title: Gerir os custos com eficiência para o SQL Server em máquinas virtuais do Azure | Documentos da Microsoft
description: Disponibiliza as melhores práticas para a escolha certa máquina do SQL Server virtual modelo de preços.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 19ba6896d9186cae32249fc8eb51319f9b37a6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477244"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Orientações de preços para as VMs do SQL Server do Azure

Este artigo fornece orientações de preços para [máquinas virtuais do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) no Azure. Existem várias opções que afetam o custo e é importante escolher a imagem correta que equilibra os custos com os requisitos empresariais.

> [!TIP]
> Se só precisa de obter uma estimativa de custo para uma combinação de edição do SQL Server e o tamanho da máquina virtual, consulte a página de preços para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Selecione a plataforma e a edição do SQL Server do **OS/Software** lista.
>
> ![Interface do Usuário na página de preços de VM](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Ou utilize o [Calculadora de preços](https://azure.microsoft.com/pricing/#explore-cost) para adicionar e configurar uma máquina virtual. 

## <a name="free-licensed-sql-server-editions"></a>Licença gratuita das edições do SQL Server

Se quiser desenvolver, testar ou criar uma prova de conceito, em seguida, utilizar livremente licenciada **edição do SQL Server Developer**. Esta edição tem todas as funcionalidades da edição do SQL Server Enterprise, permitindo-lhe criar e testar qualquer tipo de aplicação. No entanto, não é possível executar a edição de programador na produção. Uma edição do SQL Server Developer VM só leva a custos para o custo da VM, porque não há nenhum servidor SQL associado, os custos de licenciamento.

Se pretender executar uma carga de trabalho leve em produção (< 4 núcleos, < 1 GB de memória, < 10 GB/base de dados), usar livremente licenciada **SQL Server Express edition**. Uma edição Express do SQL Server VM também só incorre em custos para o custo da VM.

Para estes desenvolvimento/teste e cargas de trabalho de produção simples, também pode economizar dinheiro ao escolher um tamanho VM mais pequeno que corresponde a estas cargas de trabalho. O DS1v2 pode ser uma boa opção em alguns cenários.

Para criar uma VM do SQL Server 2017 do Azure com uma destas imagens, consulte as seguintes ligações:

| Plataforma | Imagens livremente licenciadas |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[VM de Express Azure do SQL Server 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[VM de Express Azure do SQL Server 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[VM de Express Azure do SQL Server 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[VM de Express Azure do SQL Server 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Nas edições pagas do SQL Server

Se tiver uma carga de trabalho de produção não lightweight, utilize uma das seguintes edições do SQL Server:

| SQL Server Edition | Carga de trabalho |
|-----|-----|
| Web | Sites da web pequeno |
| Standard | Pequenas e médias cargas de trabalho |
| Enterprise | Cargas de trabalho grandes ou de missão crítica|

Tem duas opções para pagar o licenciamento do SQL Server para estas edições: *pagamento por uso* ou *traga a sua própria licença (BYOL)*.

## <a name="pay-per-usage"></a>Pagar por utilização

**Pagar a licença do SQL Server por utilização** significa que o custo por segundo de execução da VM do Azure inclui o custo da licença do SQL Server. Pode ver os preços para as diferentes edições do SQL Server (Web, Standard, Enterprise) na VM do Azure, página dos preços [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

O custo é o mesmo para todas as versões do SQL Server (2012 SP3 para 2017). O custo de licenciamento por segundo depende o número de vCPUs VM.

Pagar o SQL Server de licenciamento por utilização é recomendado para:

- **Cargas de trabalho temporárias ou periódicas**. Por exemplo, uma aplicação que precisa para suportar um evento para alguns meses cada ano ou análise de negócio às segundas-feiras.

- **Cargas de trabalho com o tempo de vida desconhecido ou escala**. Por exemplo, uma aplicação que não podem ser necessárias em alguns meses, ou que pode exigir mais ou menos poder de computação, consoante a pedido.

Para criar uma VM do SQL Server 2017 do Azure com uma destas imagens de pagamento por utilização, consulte as seguintes ligações:

| Plataforma | Imagens licenciadas |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[VM do Azure do SQL Server 2017 padrão](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[VM do Azure do SQL Server 2017 padrão](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[VM do Azure do SQL Server 2017 padrão](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[VM do Azure do SQL Server 2017 padrão](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Quando cria uma máquina virtual do SQL Server no portal, o **escolher um tamanho** janela mostra um custo estimado. É importante observar que esta estimativa é apenas os custos de computação para executar a VM, juntamente com qualquer sistema operacional (Windows ou sistemas de operativos Linux de terceiros) de custos de licenciamento.
>
> ![Escolha o painel de tamanho VM](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Não inclui adicionais do SQL Server, os custos para as edições Web, Standard e Enterprise de licenciamento. Para obter a estimativa de preço mais precisa, selecione o sistema operativo e edição do SQL Server, na página de preços para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Agora, é possível alterar o modelo de licenciamento de pagamento por utilização para trazer a sua própria licença (BYOL) e de volta. Para obter mais informações, consulte [como alterar o modelo de licenciamento de uma VM do SQL](virtual-machines-windows-sql-ahb.md). 

## <a id="byol"></a> Traga a sua própria licença (BYOL)

**Trazer a sua própria licença do SQL Server por meio de mobilidade de licenças**, também referido como **BYOL**, significa que, com uma licença de Volume existente do SQL Server com Software Assurance numa VM do Azure. Uma VM do SQL Server com custos BYOL apenas para o custo de execução da VM, não para o licenciamento do SQL Server, dado que já tenham adquirido licenças e Software Assurance através de um programa de licenciamento por Volume.

> [!IMPORTANT]
> Imagens BYOL requerem um contrato Enterprise com Software Assurance. Não estão disponíveis como parte do parceiro de soluções do Azure Cloud (CSP) neste momento.

> [!NOTE]
> As imagens BYOL só estão atualmente disponíveis para as máquinas virtuais do Windows. No entanto, pode instalar manualmente do SQL Server numa VM apenas de Linux. Consulte as diretrizes a [FAQ de VM do Linux SQL](../../linux/sql/sql-server-linux-faq.md).

Trazer o seu próprio SQL licenciamento graças à mobilidade de licenças é recomendado para:

- **Cargas de trabalho contínuas**. Por exemplo, uma aplicação que tem de suportar as operações comerciais 24x7.

- **Cargas de trabalho com o tempo de vida conhecido e o dimensionamento**. Por exemplo, uma aplicação que é necessária para o ano inteiro e qual a pedido tem sido prevista.

Para utilizar BYOL com uma VM do SQL Server, tem de ter uma licença para o SQL Server Standard ou Enterprise e [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), que é uma opção necessária por meio de alguns programas de licenciamento de volume e uma compra opcional com outras pessoas. O nível de preço fornecido por meio de programas de licenciamento por Volume varia com base no tipo de contrato e a quantidade e ou compromisso para com SQL Server. Mas como uma regra prática, a trazer a sua própria licença para cargas de trabalho de produção contínua tem as seguintes vantagens:

| Benefício BYOL | Descrição |
|-----|-----|
| **Economia de custos** | Trazer a sua própria licença do SQL Server é mais económico do que pagar por utilização, se uma carga de trabalho seja executada continuamente SQL Server Standard ou Enterprise para *mais de 10 meses*. |
| **Economia de longo prazo** | Em média, é *30% mais barato por ano* para comprar ou renovar uma licença do SQL Server para os primeiros 3 anos. Além disso, depois de 3 anos, não precisa de renovar a licença deixa de poder, pague apenas o Software Assurance. Nesse ponto, é *200% mais barato*. |
| **Réplica secundária de passiva gratuita** | Outro benefício de colocar a sua própria licença é o [livre de licenciamento para uma réplica secundária passiva](https://azure.microsoft.com/pricing/licensing-faq/) por SQL Server para fins de elevada disponibilidade. Isso reduz em metade o custo de licenciamento de uma implementação de SQL Server de elevada disponibilidade (por exemplo, através de grupos de Disponibilidade AlwaysOn). Os direitos para executar o passivo secundário são fornecidos através do benefício de garantia de Software de servidores de ativação pós-falha. |

Para criar uma VM do SQL Server 2017 do Azure com uma destas imagens de bring-your-own-license, consulte as VMs prefixadas com "{BYOL}":

- [SQL Server 2017 Enterprise do Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [VM do Azure do SQL Server 2017 padrão](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Queremos sabe dentro de 10 dias quantas licenças do SQL Server estiver a utilizar no Azure. As ligações para as imagens anteriores têm instruções sobre como fazer isso.

> [!NOTE]
> Agora, é possível alterar o modelo de licenciamento de pagamento por utilização para trazer a sua própria licença (BYOL) e de volta. Para obter mais informações, consulte [como alterar o modelo de licenciamento de uma VM do SQL](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Reduzir os custos

Para evitar custos desnecessários, escolha um tamanho de máquina de virtual ideal e considere encerramentos intermitentes, para cargas de trabalho não contínuos.

### <a id="machinesize"></a> Dimensionar corretamente sua VM

O custo de licenciamento do SQL Server está diretamente relacionado com o número de vCPUs. Escolha um tamanho de VM que atende às suas necessidades esperadas de CPU, memória, armazenamento e largura de banda de e/s. Para obter uma lista completa das opções de tamanho de máquina, consulte [tamanhos de VM do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) e [tamanhos de VM do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Existem novos tamanhos de máquinas que funcionam bem com determinados tipos de cargas de trabalho do SQL Server. Estes tamanhos de máquinas manter altos níveis de memória, armazenamento e largura de banda de e/s, mas têm um menor número de núcleos de virtualizado. Por exemplo, considere o exemplo seguinte:

| Tamanho da VM | vCPUs | Memória | Máx. discos | Débito máximo do e/s | Custos de licenciamento do SQL | Custos totais (computação + licenciamento) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 IOPS ou 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51,200 IOPS ou 768 MB/s | 75% inferior | 57% inferior |

> [!IMPORTANT]
> Este é um exemplo de ponto no tempo. Para especificações de mais recentes, veja os artigos de tamanhos de máquina e o Azure, página dos preços [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

No exemplo anterior, pode ver que as especificações para **Standard_DS14v2** e **Standard_DS14 4v2** idênticas, à exceção vCPUs. O sufixo **-4v2** no final do **Standard_DS14 4v2** tamanho da máquina indica o número de vCPUs Active Directory. Uma vez que os custos de licenciamento do SQL Server estão associados ao número de vCPUs, esta opção reduz significativamente o custo da VM em cenários em que não são necessários os vCPUs extras. Este é um exemplo, e existem muitos tamanhos de máquinas com vCPUs restritos, que são identificados com esse padrão de sufixo. Para obter mais informações, consulte a mensagem de blogue [anunciando novos tamanhos de VM do Azure para mais de trabalho de base de dados económico](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Encerre a VM sempre que possível

Se estiver a utilizar quaisquer cargas de trabalho que não execute continuamente, considere a encerrar a máquina virtual durante os períodos inativos. Só paga o que utilizar.

Por exemplo, se estiver a experimentar simplesmente do SQL Server numa VM do Azure, seria não quer incorrer em custos ao acidentalmente deixá-lo a executar para semanas. Uma solução consiste em utilizar o [funcionalidade de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Autoshutdown de VM do SQL Server](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Encerramento automático faz parte de um conjunto maior de recursos semelhantes fornecida pela [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Para outros fluxos de trabalho, considere automaticamente encerrar e reiniciar as VMs do Azure com uma solução de criação de scripts, como [automatização do Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Encerrar e a desalocar a VM são a única forma de evitar encargos. Simplesmente parar ou utilizar as opções de energia para encerrar a VM ainda incorre em custos de utilização.

## <a name="next-steps"></a>Passos Seguintes

Para o Azure geral preços orientações, consulte [evitar custos inesperados com a faturação do Azure e a gestão de custos](../../../billing/billing-getting-started.md). Para as máquinas virtuais mais recente, preços, incluindo o SQL Server, consulte a página dos preços do Azure VM Azure [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [VMs do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Para obter uma descrição geral do SQL Server em execução em máquinas de virtuais do Azure, veja os artigos seguintes:

- [Descrição geral do SQL Server em VMs do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Descrição geral do SQL Server em VMs do Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)