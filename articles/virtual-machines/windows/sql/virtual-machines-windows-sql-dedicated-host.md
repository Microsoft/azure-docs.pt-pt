---
title: VM SQL Server em um host dedicado do Azure
description: Saiba mais sobre os detalhes para executar uma VM SQL Server em um host dedicado do Azure.
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
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834357"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>VM SQL Server em um host dedicado do Azure 

Este artigo detalha as especificações do uso de uma VM SQL Server com um [host dedicado do Azure](/azure/virtual-machines/windows/dedicated-hosts). Informações adicionais sobre o host dedicado do Azure podem ser encontradas na postagem do blog [apresentando o host dedicado do Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Visão geral
O [host dedicado do Azure](/azure/virtual-machines/windows/dedicated-hosts) é um serviço que fornece servidores físicos capazes de hospedar uma ou mais máquinas virtuais-dedicadas a uma assinatura do Azure. Os hosts dedicados são os mesmos servidores físicos usados nos data centers da Microsoft, fornecidos como um recurso. Você pode provisionar hosts dedicados em uma região, zona de disponibilidade e domínio de falha. Em seguida, você pode posicionar VMs diretamente em seus hosts provisionados, em qualquer configuração que melhor atenda às suas necessidades.

## <a name="limitations"></a>Limitações

- Atualmente, não há suporte para conjuntos de dimensionamento de máquinas virtuais em hosts dedicados.
- Há suporte para a série de VMs a seguir: DSv3 e ESv3. 

## <a name="licensing"></a>Licenças

Você pode escolher entre duas opções de licenciamento diferentes ao adicionar sua VM de SQL Server a um host do Azure dedicado. 

  - **Licenciamento da VM do SQL**: essa é a opção de licenciamento existente, em que você paga por cada licença de VM SQL Server individualmente. 
  - **Licenciamento de host dedicado**: o novo modelo de licenciamento disponível para o host do Azure dedicado, no qual SQL Server licenças são agrupadas e pagas no nível do host. 


Opções de nível de host para usar licenças de SQL Server existentes: 
  - Benefício Híbrido do Azure do SQL Server Enterprise Edition
    - Disponível para clientes com SA ou assinatura.
    - Licenciar todos os núcleos físicos disponíveis e desfrutar de virtualização ilimitada (até o máximo de vCPUs com suporte pelo host).
        - Para obter mais informações sobre como aplicar o Benefício Híbrido do Azure ao host dedicado do Azure, consulte as [perguntas frequentes benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - SQL Server licenças adquiridas antes de 1º de outubro
      - O SQL Server Enterprise Edition tem opções de licença de nível de host e de VM. 
      - O SQL Server Standard Edition só tem a opção de licença de VM disponível. 
          - Para obter detalhes, consulte os [termos do produto da Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Se nenhuma opção de nível de host SQL Server dedicada for selecionada, SQL Server AHB poderá ser selecionado no nível de VMs individuais, assim como nas VMs de multilocatário.



## <a name="provisioning"></a>Aprovisionamento  
O provisionamento de uma VM SQL Server para o host dedicado não é diferente de qualquer outra máquina virtual do Azure. Você pode fazer isso usando [Azure PowerShell](../dedicated-hosts-powershell.md), o [portal do Azure](../dedicated-hosts-portal.md)e o [CLI do Azure](../../linux/dedicated-hosts-cli.md).

O processo de adicionar uma VM de SQL Server existente ao host dedicado requer tempo de inatividade, mas não afetará os dados e não terá perda de dados. No entanto, é necessário fazer backup de todos os bancos de dados, incluindo bancos de dados do sistema, antes da movimentação.

## <a name="virtualization"></a>Virtualização 

Um dos benefícios de um host dedicado é a virtualização ilimitada. Por exemplo, você pode ter licenças para 64 vCores, mas pode configurar o host para ter 128 vCores, portanto, você obtém o vCores duplo, mas apenas pagando pela metade das licenças SQL Server. 

Desde que seja seu host, você está qualificado para definir a virtualização com uma proporção de 1:2. 

## <a name="faq"></a>FAQ

**P: como o Benefício Híbrido do Azure funciona para licenças do Windows Server/SQL Server no host dedicado do Azure?**

R: os clientes podem usar o valor de suas licenças existentes do Windows Server e do SQL Server com Software Assurance, ou licenças de assinatura qualificadas, para pagar uma taxa reduzida no host dedicado do Azure usando Benefício Híbrido do Azure. Os clientes do Windows Server Datacenter e do SQL Server Enterprise Edition obtêm virtualização ilimitada (implante quantas máquinas virtuais do Windows Server forem possíveis no host, sujeito à capacidade física do servidor subjacente) quando licenciarem todo o host e use Benefício Híbrido do Azure.  Todas as cargas de trabalho do Windows Server e do SQL Server no host dedicado do Azure também estão qualificadas para atualizações de segurança estendidas para o Windows Server e o SQL Server 2008/R2 sem custo adicional. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


