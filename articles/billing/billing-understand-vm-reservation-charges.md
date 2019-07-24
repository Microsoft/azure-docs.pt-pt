---
title: Entender o desconto de instâncias de VM reservadas do Azure | Microsoft Docs
description: Saiba como o desconto da instância de VM reservada do Azure é aplicado à execução de máquinas virtuais.
author: yashesvi
manager: yashar
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2019
ms.author: banders
ms.openlocfilehash: 191160035f516d818d5537c5c47f9604998c46f7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849998"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Como o desconto de reserva do Azure é aplicado às máquinas virtuais

Depois de comprar uma instância de máquina virtual reservada do Azure, o desconto de reserva é aplicado automaticamente às máquinas virtuais que correspondem aos atributos e à quantidade da reserva. Uma reserva abrange os custos de computação de suas máquinas virtuais.

Um desconto de reserva se aplica às VMs de base que você compra do Azure Marketplace.

Para capacidade reservada do banco de dados SQL, consulte [entender o desconto de instâncias reservadas do Azure](billing-understand-reservation-charges.md).

A tabela a seguir ilustra os custos para sua máquina virtual após a compra de uma instância de VM reservada. Em todos os casos, você será cobrado pelo armazenamento e pela rede com as tarifas normais.

| Tipo de máquina virtual  | Encargos com a instância de VM reservada |
|-----------------------|--------------------------------------------|
|VMs do Linux sem software adicional | A reserva abrange os custos de infraestrutura da VM.|
|VMs do Linux com encargos de software (por exemplo, Red Hat) | A reserva abrange os custos de infraestrutura. Você é cobrado por software adicional.|
|VMs do Windows sem software adicional |A reserva abrange os custos de infraestrutura. Você é cobrado pelo software do Windows.|
|VMs do Windows com software adicional (por exemplo, SQL Server) | A reserva abrange os custos de infraestrutura. Você é cobrado pelo software Windows e por software adicional.|
|VMs do Windows com [benefício híbrido do Azure](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | A reserva abrange os custos de infraestrutura. Os custos de software do Windows são cobertos pelo Benefício Híbrido do Azure. Qualquer software adicional é cobrado separadamente.|

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é "*use-it-or-perca-it*". Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você encerra um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

## <a name="reservation-discount-for-non-windows-vms"></a>Desconto de reserva para VMs não Windows

 O desconto de reserva do Azure é aplicado à execução de instâncias de VM por hora. As reservas que você comprou são correspondidas ao uso emitido pelas VMs em execução para aplicar o desconto de reserva. Para VMs que não podem executar a hora completa, a reserva será preenchida de outras VMs que não usam uma reserva, incluindo VMs em execução simultânea. No final da hora, o aplicativo de reserva para VMs na hora é bloqueado. No caso de uma VM não ser executada por uma hora ou VMs simultâneas dentro da hora não preencherem a hora da reserva, a reserva será subutilizada para essa hora. O grafo a seguir ilustra a aplicação de uma reserva para o uso de VM faturável. A ilustração se baseia em uma compra de reserva e duas instâncias de VM correspondentes.

![Captura de tela de uma reserva aplicada e duas instâncias de VM correspondentes](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Qualquer uso acima da linha de reserva é cobrado com as tarifas pagas conforme o uso normais. Você não é cobrado por nenhum uso abaixo da linha de reservas, pois já foi pago como parte da compra de reserva.
2. Na hora 1, a instância 1 é executada por 0,75 horas e a instância 2 é executada por 0,5 horas. O uso total para a hora 1 é de 1,25 horas. Você é cobrado pelas tarifas pagas conforme o uso das 0,25 horas restantes.
3. Para a hora 2 e a hora 3, ambas as instâncias foram executadas por 1 hora. Uma instância é coberta pela reserva e a outra é cobrada de acordo com as tarifas pagas conforme o uso.
4. Para a hora 4, a instância 1 é executada por 0,5 horas e a instância 2 é executada por 1 hora. A instância 1 é totalmente coberta pela reserva e 0,5 horas da instância 2 é coberta. Você é cobrado pela taxa paga conforme o uso pelas 0,5 horas restantes.

Para entender e exibir o aplicativo de suas reservas do Azure em relatórios de uso de cobrança, consulte [entender o uso de reserva](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Desconto de reserva para VMs do Windows

Quando você estiver executando instâncias de VM do Windows, a reserva será aplicada para cobrir os custos de infraestrutura. O aplicativo da reserva para os custos de infraestrutura de VM para VMs do Windows é o mesmo para VMs não Windows. Você é cobrado separadamente para o software Windows por vCPU. Consulte [custos de software do Windows com reservas](billing-reserved-Instance-windows-software-costs.md). Você pode cobrir os custos de licenciamento do Windows com o [benefício híbrido do Azure para Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>O desconto pode se aplicar a diferentes tamanhos

Quando você comprar uma instância de VM reservada, se selecionar **otimizado para**: flexibilidade de **tamanho de instância**, a cobertura de desconto dependerá do tamanho da VM que você selecionar. A reserva pode ser aplicada a tamanhos de VMs (máquinas virtuais) no mesmo grupo de séries de tamanho. Para obter mais informações, consulte [flexibilidade de tamanho de máquina virtual com instâncias de VM reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>O desconto se aplica somente ao ServiceType correspondente

Um desconto de reserva se aplica somente ao uso da VM `ServiceType` em que `AdditionalInfo` o valor em corresponde à reserva comprada. O aplicativo de desconto de reserva ignora o medidor usado para VMs e só `ServiceType`é avaliado. Saiba o tipo de serviço para o qual você comprou a VM. Você pode trocar uma reserva de VM de armazenamento não Premium para uma reserva de armazenamento Premium ou da maneira oposta.

## <a name="services-that-get-vm-reservation-discounts"></a>Serviços que obtêm descontos de reserva de VM

Suas reservas de VM podem se aplicar ao uso de VM emitido de vários serviços – não apenas para implantações de VM. Os recursos que obtêm descontos de reserva mudam dependendo da configuração de flexibilidade do tamanho da instância.

### <a name="instance-size-flexibility-setting"></a>Configuração de flexibilidade do tamanho da instância

A configuração flexibilidade do tamanho da instância determina quais serviços obtêm os descontos da instância reservada.

Se a configuração estiver ativada ou desativada, os descontos de reserva serão aplicados automaticamente a  qualquer uso `Microsoft.Compute`de VM correspondente quando o ConsumedService for. Portanto, verifique os dados de uso para o valor *ConsumedService* . Alguns exemplos incluem:

- Máquinas virtuais
- Conjuntos de dimensionamento de máquinas virtuais
- Serviço de contêiner
- Implantações do lote do Azure (no modo de assinaturas do usuário)
- Serviço de Kubernetes do Azure (AKS)
- Service Fabric

Quando a configuração está ativada, os descontos de reserva se aplicam automaticamente ao uso correspondente da VM quando o *ConsumedService* é qualquer um dos seguintes itens:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Verifique o valor de *ConsumedService* em seus dados de uso para determinar se o uso está qualificado para descontos de reserva.

Para obter mais informações sobre flexibilidade de tamanho de instância, consulte [flexibilidade de tamanho de máquina virtual com instâncias de VM reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

- [O que são reservas para o Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gerenciar reservas para o Azure](billing-manage-reserved-vm-instance.md)
- [Entenda o uso de reserva para sua assinatura pré-paga](billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro corporativo](billing-understand-reserved-instance-usage-ea.md)
- [Entender o uso de reserva para assinaturas do CSP](/partner-center/azure-reservations)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
