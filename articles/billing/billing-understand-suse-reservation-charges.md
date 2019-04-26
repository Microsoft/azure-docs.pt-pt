---
title: Desconto de plano de software - Azure | Documentos da Microsoft
description: Saiba como os descontos de plano de software são aplicados no software em máquinas virtuais.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: bcbf5ab48f3476a911fc4ade1eb0c395fb335d43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370244"
---
# <a name="azure-software-plan-discount"></a>Desconto de plano de software do Azure

Planos de software do Azure para o SUSE e Red Hat estão as reservas que se aplicam a VMs implantadas. O desconto de plano de software é aplicado à utilização de software de VMs implementadas que correspondem a reserva.

Quando encerrar uma VM, o desconto é aplicado automaticamente a outra VM correspondente, se disponível. Um plano de software abrange o custo de executar o software numa VM. Outros custos, como computação, armazenamento e rede são cobrados em separado.

Para comprar o plano certo, terá de compreender a utilização da máquina virtual e o número de vCPUs nessas VMS. Utilize as secções seguintes para ajudar a identificar o que pretende comprar, com base nos seus dados de utilização.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é "*utilização-it-ou-perder-it*". Por isso, se não tiver recursos correspondentes para qualquer hora, em seguida, perderá uma quantidade de reserva para essa hora. Não pode transportar reencaminhar horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente para outro recurso correspondente no âmbito especificado. Se não existem recursos correspondentes encontram-se no âmbito especificado, são as horas reservadas *perdido*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Reveja a utilização da VM de RedHat antes de comprar

Obter o nome de produto a partir dos seus dados de utilização e comprar o plano de Red Hat com o mesmo tipo e tamanho.

Por exemplo, se tiver de sua utilização de produto **Red Hat Enterprise Linux - 1 a 4 vCPU licença de VM**, deverá comprar **Red Hat Enterprise Linux** para **1 a 4 vCPU VM**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Reveja a utilização de VM do SUSE antes de comprar

Obter o nome de produto a partir dos seus dados de utilização e comprar o plano SUSE com o mesmo tipo e tamanho.

Por exemplo, se a utilização de produto **SUSE Linux Enterprise Server prioritário - 2 a 4 vCPU suporte de VM**, deverá comprar **SUSE Linux Enterprise Server prioridade** para **2 a 4 vCPU**.

![Exemplo de seleção de produto para comprar](./media/billing-understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Desconto aplica-se aos diferentes tamanhos de VM para os planos de SUSE

Como instâncias de VM reservadas, SUSE planejar compras oferecem flexibilidade de tamanho de instância. Isso significa que se aplica o desconto até mesmo quando implementa uma VM com uma contagem de vCPU diferentes. O desconto aplica-se aos diferentes tamanhos de VM dentro do plano de software.

O valor do desconto depende a proporção listada nas tabelas seguintes. O rácio compara os requisitos de espaço relativo para cada medidor nesse grupo. A taxa depende de vCPUs a VM. Utilize o valor rácio para calcular quantas instâncias VM obtém o desconto de plano de SUSE Linux.

Por exemplo, se comprar um plano para o servidor do SUSE Linux Enterprise para HPC prioritário para uma VM com 3 ou 4 vCPUs, proporção para esse reserva é 2. O desconto abrange o custo do software SUSE para:

- 2 implementadas VMs com 1 ou 2 vCPUs,
- 1 implementada a VM com 3 ou 4 vCPUs,
- ou a % de 77 0.77 ou cerca de uma VM com vCPUs 5 ou mais.

A proporção de vCPUs 5 ou mais é 2.6. Portanto, uma reserva para SUSE com uma VM com 5 ou mais vCPUs abrange uma única parte do custo de software, o que é cerca de 77%.

As tabelas seguintes mostram os planos de software, que pode comprar uma reserva para os medidores de utilização associado e as taxas de cada um.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server para HPC prioritário

Nome do marketplace de portal do Azure:

- SLES 12 SP3 para HPC (prioridade)

|VM DE SUSE | Id do Medidor| Proporção| Tamanho da VM de exemplo|
| -------| ------------------------| --- |--- |
|SLES para HPC 1-2 vCPUs|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES para HPC 3 a 4 vCPUs|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES para HPC 5 + vCPUs|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server para HPC Standard

Nome do marketplace de portal do Azure:

- SP3 de 12 do SLES para HPC

|VM DE SUSE | Id do Medidor | Proporção|Tamanho da VM de exemplo|
| ------- | --- | ------------------------| --- |
|SLES para HPC 1-2 vCPUs |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES para HPC 3 a 4 vCPUs|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SLES para HPC 5 + vCPUs |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Nomes de mercado de portal do Azure:

- SLES para SAP 15 (prioridade)
- SLES para SAP 12 SP3 (prioridade)
- SLES para SAP 12 SP2 (prioridade)

|VM DE SUSE | Id do Medidor | Proporção|Tamanho da VM de exemplo|
| ------- |------------------------| --- | --- |
|SLES para SAP prioridade 1 ou 2 vCPUs|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES para SAP prioridade 3 a 4 vCPUs |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES para SAP Priority 5 + vCPUs |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>Prioridade do SUSE Linux Enterprise Server

Nomes de mercado de portal do Azure:

- SLES 15 (PRIORIDADE)
- SLES 12 SP3 (prioridade)
- SLES 11 SP4 (prioridade)

|VM DE SUSE | Id do Medidor | Proporção|Tamanho da VM de exemplo|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2 a 4 vCPUs |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2 a 4 vCPUs |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|6 de SLES vCPUs |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 vCPUs |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|SLES 12 núcleos vCPUs |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 vCPUs |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 vCPUs |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|24 de SLES núcleos vCPUs |-005d-4075-ac11-822ccde9e8f6|3.2| ND24s|
|SLES 32 vCPUs |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|VCPUs de núcleos de SLES 40 |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 vCPUs |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES 72 núcleos vCPUs |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES 96 núcleos vCPUs |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 núcleos vCPUss |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Nomes de mercado de portal do Azure:

- SLES 15
- SLES 15 (padrão)
- SLES 12 SP3 (Standard)

|VM DE SUSE | Id do Medidor | Proporção|Tamanho da VM de exemplo|
| ------- |------------------------| --- |--- |
|SLES 1-2 núcleos vCPUs |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3 a 4 núcleos vCPUs |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5+ vCPUs |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas, veja os artigos seguintes:

- [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetue o pré-pagamento do SUSE planos de software com as reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
