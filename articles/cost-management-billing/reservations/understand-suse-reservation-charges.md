---
title: Desconto de plano de software – Azure | Microsoft Docs
description: Saiba como os descontos de plano de software são aplicados ao software em máquinas virtuais.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: banders
ms.openlocfilehash: 8bf53715b7f19c44d9114150e617f903cd05a51e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566320"
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

![Exemplo de seleção do produto a ser comprado](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

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

|VM do SUSE | MeterId| Proporção| Exemplo de tamanho da VM|
| -------| ------------------------| --- |--- |
|SUSE Linux Enterprise Server para Prioridade HPC 1-2 vCPUs|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SUSE Linux Enterprise Server para Prioridade HPC 3-4 vCPUs|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SUSE Linux Enterprise Server para HPC Priority 5+ vCPUs|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

|VM do SUSE | MeterId | Proporção|Exemplo de tamanho da VM|
| ------- | --- | ------------------------| --- |
|SUSE Linux Enterprise Server para HPC Standard 1-2 vCPUs |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SUSE Linux Enterprise Server para HPC Standard 3-4 vCPUs|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SUSE Linux Enterprise Server para HPC Standard 5+ vCPUs |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-standard"></a>SUSE Linux Enterprise Server para SAP Standard

Anteriormente, o SUSE Linux Enterprise Server para SAP Standard foi nomeado SUSE Linux Enterprise Server para a PRIORIDADE SAP.

|VM do SUSE | MeterId | Proporção|Exemplo de tamanho da VM|
| ------- |------------------------| --- | --- |
|SUSE Linux Enterprise Server para SAP Standard 1-2 vCPUs|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SUSE Linux Enterprise Server para SAP Standard 3-4 vCPUs |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SUSE Linux Enterprise Server para SAP Standard 5+ vCPUs |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

|VM do SUSE | MeterId | Proporção|Exemplo de tamanho da VM|
| ------- |------------------------| --- |--- |
|SUSE Linux Enterprise Server Standard 1-2 núcleos vCPUs |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SUSE Linux Enterprise Server Standard 3-4 núcleos vCPUs |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SUSE Linux Enterprise Server Standard 5+ vCPUs |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as reservas, veja os seguintes artigos:

- [O que são as reservas do Azure?](save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de planos de software SUSE com Reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Gerir o Azure Reservations](manage-reserved-vm-instance.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)