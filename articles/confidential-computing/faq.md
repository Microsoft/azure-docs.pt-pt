---
title: FaQ de Computação Confidencial Azure
description: Respostas a perguntas frequentes sobre computação confidencial do Azure.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: bb821d00a168e3b8f0636b93696376dc8b5d492e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772903"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Perguntas frequentes para a Computação Confidencial Azure

Este artigo fornece respostas a algumas das questões mais comuns sobre a execução de cargas de [trabalho confidenciais de computação no Azure.](overview.md)

Se o seu problema azure não for abordado neste artigo, visite os fóruns do Azure sobre [mSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nestes fóruns, ou publicar [ @AzureSupport no Twitter.](https://twitter.com/AzureSupport) Também pode apresentar um pedido de apoio azure. Para submeter um pedido de apoio, na página de [suporte do Azure,](https://azure.microsoft.com/support/options/)selecione Suporte.

## <a name="confidential-computing-virtual-machines"></a>Máquinas virtuais de computação confidencial<a id="vm-faq"></a>

**Como posso implantar VMs da série DCsv2 no Azure?**

Aqui estão algumas formas de implementar um DCsv2 VM:
   - Usando um modelo de gestor de [recursos azure](../virtual-machines/windows/template-description.md)
   - Do [portal Azure](https://portal.azure.com/#create/hub)
   - No modelo de solução de mercado [azure Confidential Computing (Máquina Virtual).](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) O modelo de solução de mercado ajudará a restringir um cliente aos cenários suportados (regiões, imagens, disponibilidade, encriptação de disco). 

**Todas as imagens do SO funcionarão com computação confidencial azure?**

Não. As máquinas virtuais só podem ser implantadas em máquinas de operação da Geração 2 com o Ubuntu Server 18.04, Ubuntu Server 16.04, Windows Server 2019 Datacenter e Windows Server 2016 Datacenter. Leia mais sobre Os VMs da Gen 2 no [Linux](../virtual-machines/linux/generation-2.md) e [Windows](../virtual-machines/windows/generation-2.md)

**As máquinas virtuais DCsv2 estão cinzentas no portal e não consigo selecionar uma.**

Com base na bolha de informação ao lado do VM, existem diferentes ações a tomar:
   -    **UnsupportedGeneration**: Mude a geração da imagem da máquina virtual para "Gen2".
   -    **NotAvailableForSubscription**: A região ainda não está disponível para a sua subscrição. Selecione uma região disponível.
   -    **InsuficienteQuota**: [Crie um pedido](../azure-portal/supportability/per-vm-quota-requests.md)de apoio para aumentar a sua quota . As assinaturas de testes gratuitos não têm quota para VMs de computação confidencial. 

**As máquinas virtuais DCsv2 não aparecem quando tento procurá-las no seletor de tamanho do portal**

Certifique-se de que selecionou uma [região disponível.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) Certifique-se também de que seleciona "limpar todos os filtros" no seletor de tamanhos. 

**Obtenho um erro de falha de falha do modelo do Gestor de Recursos Azure: "A operação não pôde ser concluída, uma vez que resulta em exceder a quota de núcleos familiares dcsV2 aprovado"**

[Crie um pedido](../azure-portal/supportability/per-vm-quota-requests.md)de apoio para aumentar a sua quota. As assinaturas de testes gratuitos não têm quota para VMs de computação confidencial. 

**Qual é a diferença entre DCsv2-Series e VMs da série DC?**

Os VMs da série DC funcionam em processadores Intel de 6 núcleos mais antigos com Intel SGX e têm memória menos total, menos memória enclave Page Cache (EPC), e estão disponíveis em apenas duas regiões (EUA Leste e Europa Oeste em tamanhos Standard_DC2s e Standard_DC4s). Não existem planos para disponibilizar estes VMs em geral e não são recomendados para utilização da produção. Para implementar estes VMs, utilize a instância de Mercado De Mercado da [Série DD [Preview] Confidential Compute DC-Series.](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)

**As máquinas virtuais DCSV2 estão disponíveis globalmente?**

Não. Neste momento, estas máquinas virtuais só estão disponíveis em regiões selecionadas. Consulte a página dos [produtos por regiões](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter as regiões mais recentes disponíveis. 

**Como posso instalar o Open Enclave SDK nas máquinas virtuais DCsv2?**
   
Para obter instruções sobre como instalar o OE SDK numa máquina Azure ou no local, siga as instruções do [Open Enclave SDK GitHub](https://github.com/openenclave/openenclave).
     
Também pode ver o Open Enclave SDK GitHub para instruções de instalação específicas do OS:
   - [Instale o OE SDK no Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Instale o OE SDK em Ubuntu 18.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [Instale o OE SDK em Ubuntu 16.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
