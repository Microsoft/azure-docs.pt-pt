---
title: Desconto de plano de software – Azure | Microsoft Docs
description: Saiba como os descontos de plano de software são aplicados ao software em máquinas virtuais.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: 76fc2d782571818d14a3008c10a0c3fae07a2b7d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225727"
---
# <a name="azure-software-plan-discount"></a>Desconto de plano de software do Azure

Os planos de software do Azure para SUSE e RedHat são reservas que se aplicam a VMs implementadas. O desconto de plano de software é aplicado à utilização do software de VMs implementadas que correspondem à reserva.

Quando encerra uma VM, o desconto é aplicado automaticamente a outra VM correspondente, se disponível. Um plano de software abrange o custo da execução do software numa VM. Outros custos, como computação, armazenamento e rede, são cobrados em separado.

Para comprar o plano certo, tem de compreender a utilização da VM e o número de vCPUs nessas VMs. Utilize as secções a seguir para ajudar a identificar o plano a ser comprado, com base nos dados de utilização.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva é “*use-it-or-lose-it*” (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Analisar a utilização da VM do RedHat antes de comprar

Obtenha o nome do produto dos dados de utilização e compre o plano do RedHat com o mesmo tipo e tamanho.

Por exemplo, se a sua utilização tiver o produto **Red Hat Enterprise Linux - 1-4 vCPU VM License**, deve comprar o **Red Hat Enterprise Linux** para **1-4 vCPU VM**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Analisar a utilização da VM do SUSE antes de comprar

Obtenha o nome do produto dos dados de utilização e compre o plano do SUSE com o mesmo tipo e tamanho.

Por exemplo, se a sua utilização for para o produto **SUSE Linux Enterprise Server Priority – 2-4 vCPU VM Support**, deve comprar o **SUSE Linux Enterprise Server Priority** para **2-4 vCPU**.

![Exemplo de seleção do produto a ser comprado](./media/billing-understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>O desconto aplica-se a diferentes tamanhos de VM para planos SUSE

Como as Instâncias Reservadas da VM, as compras do plano SUSE oferecem flexibilidade de tamanho de instância. Isto significa que o seu desconto se aplica mesmo quando implementa uma VM com uma contagem de vCPU diferente. O desconto aplica-se a diferentes tamanhos de VM no plano de software.

O valor do desconto depende da proporção apresentada nas seguintes tabelas. A proporção compara a quantidade relativa para cada medidor nesse grupo. A proporção depende das vCPUs da VM. Utilize o valor da proporção para calcular quantas instâncias de VM obtêm o desconto do plano SUSE Linux.

Por exemplo, se comprar um plano para SUSE Linux Enterprise Server for HPC Priority para uma VM com 3 ou 4 vCPUs, a proporção dessa reserva será 2. O desconto abrange o custo do software SUSE para:

- 2 VMs implementadas com 1 ou 2 vCPUs,
- 1 VM implementada com 3 ou 4 vCPUs,
- ou 0,77 ou cerca de 77% de uma VM com 5 ou mais vCPUs.

A proporção para 5 ou mais vCPUs é de 2,6. Portanto, uma reserva para o SUSE com uma VM com 5 ou mais vCPUs abrange apenas uma parte do custo do software, que é de cerca de 77%.

As tabelas seguintes mostram os planos de software para os quais pode comprar uma reserva, os medidores de utilização associados e as proporções para cada um.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Nome do marketplace do portal do Azure:

- SLES 12 SP3 for HPC (Priority)

|VM do SUSE | MeterId| Proporção| Exemplo de tamanho da VM|
| -------| ------------------------| --- |--- |
|SLES for HPC com 1-2 vCPUs|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES for HPC com 3-4 vCPUs|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES for HPC com mais de 5 vCPUs|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Nome do marketplace do portal do Azure:

- SLES 12 SP3 for HPC

|VM do SUSE | MeterId | Proporção|Exemplo de tamanho da VM|
| ------- | --- | ------------------------| --- |
|SLES for HPC com 1-2 vCPUs |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES for HPC com 3-4 vCPUs|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SLES for HPC com mais de 5 vCPUs |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Nomes do marketplace do portal do Azure:

- SLES for SAP 15 (Priority)
- SLES for SAP 12 SP3 (Priority)
- SLES for SAP 12 SP2 (Priority)

|VM do SUSE | MeterId | Proporção|Exemplo de tamanho da VM|
| ------- |------------------------| --- | --- |
|SLES for SAP Priority com 1-2 vCPUs|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES for SAP Priority com 3-4 vCPUs |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES for SAP Priority com mais de 5 vCPUs |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server Priority

Nomes do marketplace do portal do Azure:

- SLES 15 (PRIORITY)
- SLES 12 SP3 (Priority)
- SLES 11 SP4 (Priority)

|VM do SUSE | MeterId | Proporção|Exemplo de tamanho da VM|
| ------- |------------------------| --- |--- |
|SLES com 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES com 2-4 vCPUs |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES com 2-4 vCPUs |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES com 6 vCPUs |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES com 8 vCPUs |e11331a8-fd32-4e71-b60e-4de2a818c67a|3,2|D8s_v3|
|SLES com vCPUs com 12 núcleos |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3,2||
|SLES com 16 vCPUs |bb21066f-fe46-46d3-8006-b326b1663e52|3,2| D16s_v3|
|SLES com 20 vCPUs |c5228804-1de6-4bd4-a61c-501d9003acc8|3,2| |
|SLES com vCPUs com 24 núcleos |-005d-4075-ac11-822ccde9e8f6|3,2| ND24s|
|SLES com 32 vCPUs |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3,2| D32s_v3|
|SLES com vCPUs com 40 núcleos |a161d3d3-0592-4956-9b64-6829678b6506|3,2||
|SLES com 64 vCPUs |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3,2| D64s_v3|
|SLES com vCPUs com 72 núcleos |93329a72-24d7-4faa-93d9-203f367ed334|3,2|F72s_v2|
|SLES com vCPUs com 96 núcleos |2018c3a8-ff13-41f8-b64d-9558c5206547|3,2||
|SLES com vCPUs com 128 núcleos |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3,2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Nomes do marketplace do portal do Azure:

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|VM do SUSE | MeterId | Proporção|Exemplo de tamanho da VM|
| ------- |------------------------| --- |--- |
|SLES com vCPUS com 1-2 núcleos |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES com vCPUs com 3-4 núcleos |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SLES com mais de 5 vCPUs |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as reservas, veja os seguintes artigos:

- [O que são as Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de planos de software SUSE com Reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](billing-understand-reserved-instance-usage-ea.md)
