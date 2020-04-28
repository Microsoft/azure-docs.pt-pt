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
ms.openlocfilehash: e9cb691ef60f612672078a9ef84db904c79cbc87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189450"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Perguntas frequentes para a Computação Confidencial Azure

Este artigo fornece respostas a algumas das questões mais comuns sobre a execução de cargas de [trabalho confidenciais de computação no Azure.](overview.md)

Se o seu problema azure não for abordado neste artigo, visite os fóruns do Azure sobre [mSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nestes fóruns, ou publicar [ @AzureSupport no Twitter.](https://twitter.com/AzureSupport) Também pode apresentar um pedido de apoio azure. Para submeter um pedido de apoio, na página de [suporte do Azure,](https://azure.microsoft.com/support/options/)selecione Suporte.

## <a name="confidential-computing-virtual-machines"></a>Máquinas virtuais de computação confidencial<a id="vm-faq"></a>

1. **Como pode começar a implementar VMs da série DCsv2?**

   Aqui estão algumas formas de implementar um DCsv2 VM:
   - Usando um modelo de gestor de [recursos azure](../virtual-machines/windows/template-description.md)
   - Do [portal Azure](https://portal.azure.com/#create/hub)
   - No modelo de solução de mercado [de computação confidencial Azure (Máquina Virtual).](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) O modelo de solução de mercado ajudará a restringir um cliente aos cenários suportados (regiões, imagens, disponibilidade, encriptação de disco). 

1. **Todas as imagens do SO funcionarão com computação confidencial azure?**

   Não. As máquinas virtuais só podem ser implantadas em máquinas virtuais da Geração 2. Oferecemos suporte à Geração 2 para Ubuntu Server 18.04, Ubuntu Server 16.04 e Windows Server 2016 Datacenter. Leia mais sobre Os VMs da Gen 2 no [Linux](../virtual-machines/linux/generation-2.md) e [Windows](../virtual-machines/windows/generation-2.md)

1. **As máquinas virtuais DCsv2 estão cinzentas no portal e não consigo selecionar uma.**

   Com base na bolha de informação ao lado do VM, existem diferentes ações a tomar:
    -   **UnsupportedGeneration**: Mude a geração da imagem da máquina virtual para "Gen2".
    -   **NotAvailableForSubscription** : A região ainda não está disponível para a sua subscrição. Selecione uma região disponível.
    -   **InsuficienteQuota**: [Crie um pedido](../azure-portal/supportability/per-vm-quota-requests.md)de apoio para aumentar a sua quota . As assinaturas de testes gratuitos não têm quota para VMs de computação confidencial. 

1. **As máquinas virtuais DCsv2 não aparecem quando tento procurá-las no seletor de tamanho do portal**

   Certifique-se de que selecionou uma região disponível. Certifique-se também de que seleciona "limpar todos os filtros" no seletor de tamanhos. 

1. **Qual é a diferença entre DCsv2-Series e VMs da série DC?**

   Os VMs da série DC funcionam em processadores Intel de 6 núcleos mais antigos com Intel SGX. Estes têm memória menos total, menos memória EPC (Enclave Page Cage) e estão disponíveis em menos regiões. Estes VMs só estão disponíveis nos EUA Leste e Europa Ocidental estão disponíveis em dois tamanhos: Standard_DC2s e Standard_DC4s. Eles não irão GA e só podem ser implantados na instância de Mercado [De Computação Confidencial DC-Series [Pré-visualização]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)

1. **As máquinas virtuais DCSV2 estão disponíveis globalmente?**

   Não, estas máquinas virtuais só estão disponíveis em regiões selecionadas. Consulte a página dos [produtos por regiões](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter as regiões mais recentes disponíveis. 

1. **Como posso instalar o Open Enclave SDK?**
   
   Para obter instruções sobre como instalar o OE SDK numa máquina, seja em Azure ou no local, siga as instruções do [Open Enclave SDK GitHub](https://github.com/openenclave/openenclave).
     
   Também pode dirigir-se ao Open Enclave SDK GitHub para instruções de instalação específicas do OS:
     - [Instale o OE SDK no Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Instale o OE SDK em Ubuntu 18.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Instale o OE SDK em Ubuntu 16.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
