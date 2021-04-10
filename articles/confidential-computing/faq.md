---
title: Azure Confidential Computing FAQ
description: Respostas a perguntas frequentes sobre computação confidencial Azure.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: a5ecd3827bbdc12b098684f1feda2df652f11940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551917"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Perguntas frequentes para Azure Confidential Computing

Este artigo fornece respostas a algumas das questões mais comuns sobre a execução de [cargas de trabalho confidenciais de computação em máquinas virtuais Azure](overview.md).

Se a sua questão Azure não for abordada neste artigo, visite os fóruns Azure sobre [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nestes fóruns ou publicar [ @AzureSupport no Twitter](https://twitter.com/AzureSupport). Também pode submeter um pedido de apoio ao Azure. Para submeter um pedido de apoio, na página de suporte do [Azure,](https://azure.microsoft.com/support/options/)selecione Obter suporte.

## <a name="confidential-computing-virtual-machines"></a>Máquinas virtuais de computação confidencial <a id="vm-faq"></a>

**Como posso implantar VMs da série DCsv2 no Azure?**

Aqui estão algumas formas de implementar um DCsv2 VM:
   - Usando um modelo de [gestor de recursos Azure](../virtual-machines/windows/template-description.md)
   - Do [portal Azure](https://portal.azure.com/#create/hub)
   - No modelo de solução de mercado [Azure Confidential Computing (Virtual Machine).](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) O modelo de solução de mercado ajudará a conter um cliente aos cenários suportados (regiões, imagens, disponibilidade, encriptação do disco). 

**Todas as imagens de SO funcionarão com a computação confidencial do Azure?**

N.º As máquinas virtuais só podem ser implantadas em máquinas operativas da Geração 2 com o Ubuntu Server 18.04, o Ubuntu Server 16.04, o Windows Server 2019 Datacenter e o Windows Server 2016 Datacenter. Ler mais sobre Gen 2 VMs em [Linux](../virtual-machines/generation-2.md) e [Windows](../virtual-machines/generation-2.md)

**As máquinas virtuais DCsv2 estão acinzentadas no portal e não posso selecionar uma**

Com base na bolha de informação ao lado do VM, existem diferentes ações a tomar:
   -    **Não suportadoGereração**: Mude a geração da imagem da máquina virtual para "Gen2".
   -    **Não Disponível Para Aubstrução**: A região ainda não está disponível para a sua subscrição. Selecione uma região disponível.
   -    **InsuficienteSQuota:** [Crie um pedido de apoio para aumentar a sua quota.](../azure-portal/supportability/per-vm-quota-requests.md) As assinaturas de teste gratuitos não têm quota para VMs de computação confidencial. 

**As máquinas virtuais DCsv2 não aparecem quando tento procurá-las no seletor de tamanho do portal**

Certifique-se de ter selecionado uma [região disponível.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) Certifique-se também de que seleciona "limpar todos os filtros" no seletor de tamanhos. 

**Posso permitir networking acelerado com computação confidencial Azure?**

 N.º O Networking Acelerado não é suportado em máquinas virtuais DC-Series ou DCsv2-Series. O Networking Acelerado não pode ser ativado para qualquer implementação de máquinas virtuais de computação confidencial ou para a implementação do cluster do Serviço Azure Kubernetes em execução em computação confidencial.

**Posso usar o Azure Dedicado Host com estas máquinas?**

Sim. Azure Dedicado Host suporta máquinas virtuais da série DCsv2. O Azure Dedicated Host fornece um servidor físico de um único inquilino para executar as suas máquinas virtuais. Os utilizadores geralmente usam o Azure Dedicated Host para abordar os requisitos de conformidade em torno da segurança física, integridade dos dados e monitorização. 

**Recebo um erro de falha de implementação do modelo do Azure Resource Manager: "A operação não pôde ser concluída porque resulta em exceder a quota de núcleos familiares do DcsV2 aprovado"**

[Crie um pedido de apoio para aumentar a sua quota.](../azure-portal/supportability/per-vm-quota-requests.md) As assinaturas de teste gratuitos não têm quota para VMs de computação confidencial. 

**Qual é a diferença entre DCsv2-Series e DC-Series VMs?**

DC-Series VMs funcionam em processadores Intel de 6 núcleos mais antigos com Intel SGX e têm menos memória total, menos memória de Enclave Page Cache (EPC) e estão disponíveis em apenas duas regiões (EUA Leste e Europa Oeste em tamanhos Standard_DC2s e Standard_DC4s). Não existem planos para disponibilizar estes VM geralmente e não são recomendados para uso de produção. Para implementar estes VMs, utilize o  [Relatório Confidencial DC-Series VM [Preview]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace.

**As máquinas virtuais DCsv2 estão disponíveis globalmente?**

N.º Neste momento, estas máquinas virtuais só estão disponíveis em regiões selecionadas. Consulte a página de [produtos por regiões](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para as últimas regiões disponíveis. 

**A hiper-rosca está desligada nestas máquinas?**

O hiper-roscado é desativado para todos os clusters de computação confidencial Azure.

**Como posso instalar o Open Enclave SDK nas máquinas virtuais DCsv2?**
   
Para obter instruções sobre como instalar o OE SDK numa Máquina Azure ou no local, siga as instruções no [Open Enclave SDK GitHub](https://github.com/openenclave/openenclave).
     
Também pode olhar para o Open Enclave SDK GitHub para obter instruções de instalação específicas do OS:
   - [Instale o OE SDK no Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Instale o OE SDK no Ubuntu 18.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [Instale o OE SDK no Ubuntu 16.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)