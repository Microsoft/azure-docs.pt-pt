---
title: Visão geral dos conjuntos de disponibilidade
description: Conheça os conjuntos de disponibilidade em Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: b4e9f106354915fe40a4bcf9b60bc35443345202
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530560"
---
# <a name="availability-sets-overview"></a>Visão geral dos conjuntos de disponibilidade

Este artigo fornece-lhe uma visão geral das funcionalidades de disponibilidade das máquinas virtuais Azure (VMs).

## <a name="what-is-an-availability-set"></a>O que é um conjunto de disponibilidade? 

Um conjunto de disponibilidade é um agrupamento lógico de VMs que permite ao Azure compreender a forma como a aplicação é criada, para fornecer redundância e disponibilidade. Recomendamos que sejam criados dois ou mais VMs dentro de um conjunto de disponibilidade para fornecer uma aplicação altamente disponível e para atender o [Azure SLA de 99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Não há qualquer custo para o próprio Conjunto de Disponibilidade, só paga por cada instância VM que cria.

## <a name="how-do-availability-sets-work"></a>Como funcionam os conjuntos de disponibilidade?
Cada máquina virtual no seu conjunto de disponibilidade é atribuída um **domínio de atualização** e um **domínio de avaria** pela plataforma Azure subjacente. Cada conjunto de disponibilidade pode ser configurado com até três domínios de falha e vinte domínios de atualização. Os domínios de atualização indicam grupos de máquinas virtuais e hardware físico subjacente que podem ser reiniciados ao mesmo tempo. Quando são configuradas mais de cinco máquinas virtuais num conjunto de disponibilidade único, a sexta máquina virtual é colocada no mesmo domínio de atualização da primeira máquina virtual, a sétima no mesmo domínio de atualização da segunda máquina virtual, e assim sucessivamente. A ordem dos domínios de atualização que estão a ser reiniciados não pode continuar sequencialmente durante a manutenção planeada, sendo que apenas um domínio de atualização é reiniciado de cada vez. Um domínio de atualização reiniciado dispõe de 30 minutos para realizar a recuperação antes de a manutenção ser iniciada num domínio de atualização diferente.

Os domínios de falha definem o grupo de máquinas virtuais que partilham a mesma origem de energia e o mesmo comutador de rede física. Por predefinição, as máquinas virtuais configuradas dentro do seu conjunto de disponibilidade são separadas em até três domínios de avaria. Embora a colocação de máquinas virtuais num conjunto de disponibilidade não proteja a sua aplicação de falhas específicas do sistema operativo ou da aplicação, limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="Diagrama mostrando vários clusters de computação divididos em domínios de falhas e dentro desses domínios de falha, temos vários domínios de atualização":::

Os VMs também estão alinhados com os domínios de falha do disco. Este alinhamento garante que todos os discos geridos ligados a um VM estão dentro dos mesmos domínios de avaria. 

Apenas as VMs com discos geridos podem ser criadas num conjunto de disponibilidade gerido. O número de domínios de falha do disco gerido varia por região - dois ou três domínios de falha do disco gerido por região. 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diagrama mostrando como os domínios de avaria dos discos e VMs estão alinhados.":::

## <a name="next-steps"></a>Passos seguintes
Pode agora começar a utilizar estas funcionalidades de redundância e disponibilidade para criar o seu ambiente do Azure. Para informações relativas a melhores práticas, veja [Melhores Práticas de Disponibilidade do Azure](/azure/architecture/checklist/resiliency-per-service).

