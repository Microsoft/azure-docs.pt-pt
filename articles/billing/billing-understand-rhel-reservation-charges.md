---
title: Compreender o desconto de plano de reserva de Red Hat e a utilização - Azure | Documentos da Microsoft
description: Saiba como os descontos de plano de Red Hat são aplicados no software de Red Hat em máquinas virtuais.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2019
ms.author: cwatson
ms.openlocfilehash: fe0d0f0baa2b3d1c08e871541dce1511e00f7f87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370207"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Compreender a forma como o desconto de plano de reserva do Red Hat Linux Enterprise software é aplicado para o Azure

Depois de comprar um plano de Red Hat Linux, o desconto é aplicado automaticamente às implementado máquinas de virtuais de Red Hat (VMs) que correspondem a reserva. Um plano de Red Hat Linux abrange o custo de executar o software de Red Hat numa VM do Azure.

Para comprar o plano certo do Red Hat Linux, precisa entender que as de VMs Red Hat que executar e o número de vCPUs nessas VMS. Utilize as secções seguintes para ajudar a identificar do seu ficheiro CSV de utilização, o que pretende comprar.

## <a name="discount-applies-to-different-vm-sizes"></a>Desconto aplica-se aos diferentes tamanhos de VM

Como instâncias de VM reservadas, compras de plano de Red Hat oferecem a flexibilidade de tamanho de instância. Isso significa que se aplica o desconto até mesmo quando implementa uma VM com uma contagem de vCPU diferentes. O desconto aplica-se aos diferentes tamanhos de VM dentro do plano de software.

O valor do desconto depende a proporção listada nas tabelas seguintes. O rácio compara os requisitos de espaço relativo para cada medidor nesse grupo. A taxa depende de vCPUs a VM. Utilize o valor rácio para calcular quantas instâncias VM obtém o desconto de plano de Red Hat Linux.

Por exemplo, se comprar um plano para Red Hat Linux Enterprise Server para uma VM com 3 ou 4 vCPUs, proporção para esse reserva é 2. O desconto abrange o custo de software do Red Hat para:

- 2 implementadas VMs com 1 ou 2 vCPUs,
- 1 implementada a VM com 3 ou 4 vCPUs,
- ou a % de 77 0.77 ou cerca de uma VM com vCPUs 5 ou mais.

A proporção de vCPUs 5 ou mais é 2.6. Portanto, uma reserva para Red Hat com uma VM com 5 ou mais vCPUs abrange uma única parte do custo de software, que é cerca de 77%.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Compreender a utilização da VM do Red Hat, antes de comprar

As tabelas seguintes mostram os planos de software, que pode comprar uma reserva para os medidores de utilização associado e as taxas de cada um.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Nomes de mercado de portal do Azure:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (lvm mais recente)

|Red Hat VM | MeterId| Proporção| Tamanho da VM de exemplo|
| -------| ------------------------| --- |--- |
|1 a 4 vCPU licença da VM|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1 a 4 vCPU licença da VM|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1 a 4 vCPU licença da VM|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|5 + vCPU licença da VM|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2.166666667|D8s_v3|
|5 + vCPU licença da VM|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2.166666667|D8s_v3|
|5 + vCPU licença da VM|037eddc0-fedd-4d73-b5d8-92fba9edb831|2.166666667|D8s_v3|
|5 + vCPU licença da VM|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2.166666667|D8s_v3|
|5 + vCPU licença da VM|794dcb90-0793-43e6-9909-70d29974e56d|2.166666667|D8s_v3|
|5 + vCPU licença da VM|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2.166666667|D8s_v3|
|5 + vCPU licença da VM|86c35ec3-0a48-426a-9625-22d80e6ea55b|2.166666667|D8s_v3|
|5 + vCPU licença da VM|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2.166666667|D8s_v3|
|5 + vCPU licença da VM|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2.166666667|D8s_v3|
|5 + vCPU licença da VM|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2.166666667|D8s_v3|
|5 + vCPU licença da VM|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2.166666667|D8s_v3|
|5 + vCPU licença da VM|d09f877e-03b4-48b2-b11a-782b965cff19|2.166666667|D8s_v3|
|Licença de VM de vCPU 44|6f44ae85-a70e-44be-83ec-153a0bc23979|2.166666667||
|60 vCPU licença da VM|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2.166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux para SAP com HA

Nome do marketplace de portal do Azure:

|Red Hat VM | MeterId | Proporção|Tamanho da VM de exemplo|
| ------- | --- | ------------------------| --- | --- |
|1 a 4 vCPU licença da VM |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|5 + vCPU licença da VM|6dfb482b-23ea-487f-810c-e66360f025de|2.333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux com HA

Nomes de mercado de portal do Azure:

|Red Hat VM | MeterId | Proporção|Tamanho da VM de exemplo|
| ------- |------------------------| --- | --- |
|1 a 4 vCPU licença da VM|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|5 + vCPU licença da VM|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Nomes de mercado de portal do Azure:

- Red Hat Enterprise Linux 6.8 para aplicações empresariais do SAP
- Red Hat Enterprise Linux 7.3 para aplicações empresariais do SAP
- Red Hat Enterprise Linux 7.4 para SAP
- Red Hat Enterprise Linux 7.5 para SAP

|Red Hat VM | MeterId | Proporção|Tamanho da VM de exemplo|
| ------- |------------------------| --- |--- |
|1 vCPU licença da VM|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 vCPU licença da VM|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 vCPU licença da VM|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 de vCPU licença da VM|69a140fa-e08e-415c-85f2-48158e4c73a0|2.166666667||
|vCPU de 8 licença da VM|777a5a74-22d6-48c9-9705-ac38fe05a278|2.166666667|D8s_v3|
|12 vCPU licença da VM|d6b8917a-5127-497a-9f48-1e959df98812|2.166666667||
|16 vCPU licença da VM|03667e82-e009-425a-83f7-8ebddbca5af4|2.166666667|D16s_v3|
|20 vCPU licença da VM|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2.166666667||
|24 de vCPU licença da VM|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2.166666667|ND24s|
|32 Vcpus licença da VM|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2.166666667|D32s_v3|
|40 vCPU licença da VM|737142c3-8e4f-4fc1-aa41-05b1661edff8|2.166666667||
|Licença de VM de vCPU 44|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2.166666667||
|60 vCPU licença da VM|a22bb342-ba9a-4529-a178-39a92ce770b6|2.166666667||
|64 Vcpus licença da VM|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2.166666667|64s_v3|
|72 vCPU licença da VM|14341b96-e92c-4dca-ba66-322c88a79aa6|2.166666667|F72s_v2|
|96 vCPU licença da VM|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2.166666667||
|128 Vcpus licença da VM|9b198a68-974a-47a7-9013-49169ac0f2e9|2.166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Nomes de mercado de portal do Azure:

- Red Hat Enterprise Linux 6.7 para o SAP HANA
- Red Hat Enterprise Linux 7.2 para o SAP HANA
- Red Hat Enterprise Linux 7.3 para o SAP HANA

|Red Hat VM | MeterId | Proporção|Tamanho da VM de exemplo|
| ------- |------------------------| --- |--- |
|1 vCPU licença da VM|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 vCPU licença da VM|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 vCPU licença da VM|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 de vCPU licença da VM|a5963812-0f5a-4053-8ace-2b5babd15ed8|2.166666667||
|vCPU de 8 licença da VM|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2.166666667|D8s_v3|
|12 vCPU licença da VM|0e3bc72d-a888-4bcf-8437-119f763a3215|2.166666667||
|16 vCPU licença da VM|b40e95d8-3176-42f0-967c-497785c031b2|2.166666667|D16s_v3|
|20 vCPU licença da VM|81f34277-499d-40a3-a634-99adc08e2d45|2.166666667||
|24 de vCPU licença da VM|e03f1906-d35d-4084-b2cd-63281869c8ee|2.166666667|ND24s|
|32 Vcpus licença da VM|0a58c082-ceb8-4327-9b64-887c30dddb23|2.166666667|D32s_v3|
|40 vCPU licença da VM|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2.166666667||
|Licença de VM de vCPU 44|378b8125-d8a5-4e09-99bc-c1462534ffb0|2.166666667||
|60 vCPU licença da VM|5d7db11a-54e9-404e-aaa8-509fac7c0638|2.166666667||
|64 Vcpus licença da VM|3c8157b2-a57d-45ce-ba02-bd86e9209795|2.166666667|64s_v3|
|72 vCPU licença da VM|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2.166666667|F72s_v2|
|96 vCPU licença da VM|b13895fc-0d06-4de9-b860-627c471cd247|2.166666667||
|128 Vcpus licença da VM|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2.166666667| M128ms|

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas, veja os artigos seguintes:

- [Quais são as reservas para o Azure](billing-save-compute-costs-reservations.md)
- [Efetue o pré-pagamento do Red Hat planos de software com as reservas do Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerir reservas para o Azure](billing-manage-reserved-vm-instance.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
