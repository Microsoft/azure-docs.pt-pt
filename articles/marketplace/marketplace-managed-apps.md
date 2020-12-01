---
title: Aplicações Azure geridos guia de publicação de oferta de aplicações - Azure Marketplace
description: Este artigo descreve os requisitos para a publicação de uma aplicação gerida no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: d4fb3354b7035149b80191528b2f5335b593b764
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433553"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Guia de publicação de aplicações geridas pela Azure

Uma oferta *de aplicação gerida pela* Azure é uma forma de publicar uma aplicação Azure no Azure Marketplace. As aplicações geridas são ofertas de transação que são implementadas e faturadas através do Azure Marketplace. A opção de listagem que um utilizador vê é *Get It Now*.

Este artigo explica os requisitos para o tipo de oferta de aplicação gerida.

Utilize o tipo de oferta de aplicação gerida nas seguintes condições:

- Está a implementar uma solução baseada em subscrição para o seu cliente utilizando uma máquina virtual (VM) ou uma infraestrutura inteira como solução baseada em serviço (IaaS).
- Você ou o seu cliente requer que a solução seja gerida por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um integrador de sistemas ou um prestador de serviços gerido (MSP).  

## <a name="managed-application-offer-requirements"></a>Requisitos de oferta de aplicação geridos

|Requisitos |Detalhes  |
|---------|---------|
|Uma subscrição do Azure | As aplicações geridas devem ser implementadas na subscrição de um cliente, mas podem ser geridas por terceiros. |
|Faturação e medição    |  Os recursos são fornecidos na subscrição Azure de um cliente. Os VMs que utilizam o modelo de pagamento pay-as-you-go são transacionados com o cliente através da Microsoft e faturados através da subscrição Azure do cliente. <br><br> Para os VMs de sua própria licença, a Microsoft fatura quaisquer custos de infraestrutura que sejam incorridos na subscrição do cliente, mas você transaciona diretamente as taxas de licenciamento de software com o cliente.        |
|Um disco rígido virtual compatível com Azure (VHD)    |   Os VMs devem ser construídos em Windows ou Linux.<br><br>Para obter mais informações sobre a criação de um Linux VHD, consulte [as distribuições linux endossadas no Azure](../virtual-machines/linux/endorsed-distros.md).<br><br>Para obter mais informações sobre a criação de um VHD do Windows, consulte [criar uma oferta de aplicação Azure.](./create-new-azure-apps-offer.md) |

---

> [!NOTE]
> As aplicações geridas devem ser implementadas através do Azure Marketplace. Se a comunicação do cliente for uma preocupação, contacte os clientes interessados depois de ter ativado a partilha de chumbo.  

> [!Note]
> Um fornecedor de solução de nuvem (CSP) de canal de parceiro opt-in já está disponível. Para obter mais informações sobre o marketing da sua oferta através dos canais parceiros da Microsoft CSP, consulte [os Fornecedores de Soluções Cloud.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, aprenda a [fazer crescer o seu negócio na nuvem com o Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)

Para se inscrever e começar a trabalhar no Partner Center:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte [Criar uma oferta de aplicação Azure](./create-new-azure-apps-offer.md) para mais informações.
