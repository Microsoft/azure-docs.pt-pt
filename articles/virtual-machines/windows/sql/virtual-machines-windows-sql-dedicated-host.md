---
title: SQL Server VM em um anfitrião dedicado Azure
description: Saiba mais sobre os detalhes para executar um VM De Servidor SQL num Anfitrião Dedicado Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75834357"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server VM em um anfitrião dedicado Azure 

Este artigo detalha as especificidades da utilização de um VM de servidor SQL com um [Anfitrião Dedicado Azure](/azure/virtual-machines/windows/dedicated-hosts). Informações adicionais sobre o anfitrião dedicado azure podem ser encontradas no post de blog [Introdução do Anfitrião Dedicado Azure.](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/) 

## <a name="overview"></a>Descrição geral
[O Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) é um serviço que fornece servidores físicos - capazes de acolher uma ou mais máquinas virtuais - dedicados a uma subscrição azure. Os anfitriões dedicados são os mesmos servidores físicos utilizados nos centros de dados da Microsoft, fornecidos como recurso. Você pode fornecer anfitriões dedicados dentro de uma região, zona de disponibilidade e domínio de falha. Em seguida, pode colocar VMs diretamente nos seus anfitriões, em qualquer configuração que melhor atenda às suas necessidades.

## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são atualmente suportados em anfitriões dedicados.
- As seguintes séries VM são suportadas: DSv3 e ESv3. 

## <a name="licensing"></a>Licenciamento

Pode escolher entre duas opções de licenciamento diferentes ao adicionar o seu VM De Servidor SQL a um Anfitrião Dedicado Azure. 

  - **Licenciamento SQL VM**: Esta é a opção de licenciamento existente, onde paga individualmente cada licença VM do SQL Server. 
  - **Licenciamento dedicado ao anfitrião**: O novo modelo de licenciamento disponível para o Anfitrião Dedicado Azure, onde as licenças do SQL Server são agregadas e pagas ao nível do anfitrião. 


Opções de nível de anfitrião para utilizar licenças sql server existentes: 
  - SQL Server Enterprise Edition Azure Hybrid Benefit
    - Disponível para clientes com SA ou subscrição.
    - Licencie todos os núcleos físicos disponíveis e desfrute de virtualização ilimitada (até ao vCPUs max suportado pelo anfitrião).
        - Para obter mais informações sobre a aplicação do Azure Hybrid Benefit ao anfitrião Azure Dedicado, consulte o [Azure Hybrid Benefit FAQ](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licenças de servidor SQL adquiridas antes de 1 de outubro
      - A edição Da SQL Server Enterprise tem opções de licença de nível de anfitrião e by-VM. 
      - A edição SQL Server Standard tem apenas a opção de licença by-VM disponível. 
          - Para mais detalhes, consulte os [Termos do Produto da Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Se não for selecionada nenhuma opção de nível de anfitrião dedicada ao SQL Server, então o SQL Server AHB poderá ser selecionado ao nível de VMs individuais, tal como com VMs multi-inquilinos.



## <a name="provisioning"></a>Aprovisionamento  
Fornecer um VM de servidor SQL ao anfitrião dedicado não é diferente de qualquer outra Máquina Virtual Azure. Pode fazê-lo utilizando [o Azure PowerShell,](../dedicated-hosts-powershell.md)o [portal Azure](../dedicated-hosts-portal.md)e o [Azure CLI](../../linux/dedicated-hosts-cli.md).

O processo de adição de um VM de Servidor SQL existente ao anfitrião dedicado requer tempo de inatividade, mas não terá dados de impacto, e não terá perda de dados. No entanto, todas as bases de dados, incluindo as bases de dados do sistema, devem ser apoiadas antes da mudança.

## <a name="virtualization"></a>Virtualização 

Um dos benefícios de um anfitrião dedicado é a virtualização ilimitada. Por exemplo, pode ter licenças para 64 vCores, mas pode configurar o anfitrião para ter 128 vCores, para que obtenha o dobro dos vCores mas apenas pagando metade das licenças do SQL Server. 

Uma vez que é o seu anfitrião, você é elegível para definir a virtualização com uma proporção de 1:2. 

## <a name="faq"></a>FAQ

**P: Como funciona o Azure Hybrid Benefit para licenças do Windows Server/SQL Server no Anfitrião Dedicado do Azure?**

R: Os clientes podem utilizar o valor das licenças existentes do Windows Server e Do SQL Server com garantia de software, ou licenças de subscrição qualificadas, para pagar uma taxa reduzida no Azure Dedicado Host utilizando o Azure Hybrid Benefit. Os clientes do Windows Server Datacenter e SQL Server Enterprise Edition obtêm virtualização ilimitada (implementem o maior número possível de máquinas virtuais do Windows Server no anfitrião sujeito à capacidade física do servidor subjacente) quando licenciam todo o anfitrião e utilizam o Azure Hybrid Benefit.  Todas as cargas de trabalho do Windows Server e do SQL Server no Anfitrião Dedicado do Azure também são elegíveis para Atualizações de Segurança Estendidas para Windows Server e SQL Server 2008/R2 sem custos adicionais. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral do Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ para Servidor SQL em um VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para O Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


