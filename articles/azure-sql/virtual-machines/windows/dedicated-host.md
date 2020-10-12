---
title: Executar SQL Server VM em um anfitrião dedicado Azure
description: Aprenda a executar um SQL Server VM num Anfitrião Dedicado Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 40c851e5ff5fc83ccf6b6d67e319bb97bd860bd5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84669108"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>Executar SQL Server VM em um anfitrião dedicado Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo detalha as especificidades da utilização de uma máquina virtual SQL Server (VM) com [o Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts). Informações adicionais sobre o Azure Dedicado Host podem ser encontradas no post do blog [Introduceing Azure Dedicated Host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Descrição geral
[O Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) é um serviço que fornece servidores físicos - capazes de hospedar uma ou mais máquinas virtuais - dedicados a uma subscrição do Azure. Os anfitriões dedicados são os mesmos servidores físicos utilizados nos centros de dados da Microsoft, fornecidos como recurso. Você pode providenciar anfitriões dedicados dentro de uma região, zona de disponibilidade e domínio de falhas. Em seguida, pode colocar VMs diretamente nos seus anfitriões a provisionados, em qualquer configuração que satisfaça melhor as suas necessidades.

## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são suportados atualmente em anfitriões dedicados.
- As seguintes séries VM são suportadas: DSv3 e ESv3. 

## <a name="licensing"></a>Licenciamento

Pode escolher entre duas opções de licenciamento diferentes quando colocar o seu SQL Server VM num Anfitrião Dedicado Azure. 

  - **Licenciamento SQL VM**: Esta é a opção de licenciamento existente, onde você paga por cada licença SQL Server VM individualmente. 
  - **Licenciamento dedicado ao anfitrião**: O novo modelo de licenciamento disponível para o Azure Dedicated Host, onde as licenças sql Server são agregadas e pagas ao nível do anfitrião. 


Opções de nível de anfitrião para a utilização das licenças existentes do SQL Server: 
  - SQL Server Enterprise Edition Azure Hybrid Benefit (AHB)
    - Disponível para clientes com SA ou subscrição.
    - Licencie todos os núcleos físicos disponíveis e desfrute de uma virtualização ilimitada (até ao máximo de vCPUs suportados pelo anfitrião).
        - Para obter mais informações sobre a aplicação do AHB ao Azure Dedicado Host, consulte [a Azure Hybrid Benefit FAQ](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licenças SQL Server adquiridas antes de 1 de outubro
      - A edição SQL Server Enterprise tem opções de licença de nível de anfitrião e by-VM. 
      - A edição SQL Server Standard tem apenas uma opção de licença by-VM disponível. 
          - Para mais informações, consulte [os Termos do Produto da Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Se não for selecionada nenhuma opção de nível de anfitrião dedicada ao SQL Server, poderá selecionar o SQL Server AHB ao nível de VMs individuais, tal como faria com VMs multi-inquilinos.



## <a name="provisioning"></a>Aprovisionamento  
O fornecimento de um SQL Server VM ao anfitrião dedicado não é diferente de qualquer outra máquina virtual Azure. Pode fazê-lo utilizando [a Azure PowerShell,](../../../virtual-machines/windows/dedicated-hosts-powershell.md)o [portal Azure,](../../../virtual-machines/windows/dedicated-hosts-portal.md)e o [Azure CLI](../../../virtual-machines/linux/dedicated-hosts-cli.md).

O processo de adição de um VM do Servidor SQL existente ao anfitrião dedicado requer tempo de inatividade, mas não afetará os dados e não terá perda de dados. No entanto, todas as bases de dados, incluindo bases de dados do sistema, devem ser apoiadas antes da mudança.

## <a name="virtualization"></a>Virtualização 

Um dos benefícios de um hospedeiro dedicado é a virtualização ilimitada. Por exemplo, pode ter licenças para 64 vCores, mas pode configurar o anfitrião para ter 128 vCores, para que obtenha o dobro dos vCores mas pague apenas metade do que pagaria pelas licenças do SQL Server. 

Porque como é o seu anfitrião, você é elegível para definir a virtualização com um rácio de 1:2. 

## <a name="faq"></a>FAQ

**P: Como funciona o Benefício Híbrido Azure para as licenças do Servidor Windows Server/SQL no Azure Dedicated Host?**

R: Os clientes podem utilizar o valor das licenças existentes do Windows Server e SQL Server com garantia de software, ou licenças de subscrição elegíveis, para pagar uma taxa reduzida no Azure Dedicated Host usando o Azure Hybrid Benefit. Os clientes do Windows Server Datacenter e SQL Server Enterprise Edition obtêm uma virtualização ilimitada (implemente o maior número possível de máquinas virtuais do Windows Server no hospedeiro sujeito à capacidade física do servidor subjacente) quando licenciam todo o anfitrião e utilizam o Azure Hybrid Benefit.  Todas as cargas de trabalho do Windows Server e do SQL Server no Azure Dedicated Host também são elegíveis para atualizações de segurança estendidas para Windows Server e SQL Server 2008/R2 sem custo adicional. 

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)
* [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](doc-changes-updates-release-notes.md)


