---
title: Aplicações azure geridas oferecem guia de publicação - Azure Marketplace
description: Este artigo descreve os requisitos para a publicação de uma aplicação gerida no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 5bd89e406c3067585595479dc1d8351e9ea7eea8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856109"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Guia de publicação de aplicações geridas pelo Azure

Uma oferta de *candidatura gerida pela* Azure é uma forma de publicar uma aplicação Azure no Azure Marketplace. As aplicações geridas são ofertas transacionais que são implantadas e faturadas através do Azure Marketplace. A chamada à ação que um utilizador vê é *Get It Now*.

Este artigo explica os requisitos para o tipo de oferta de candidatura gerida.

Utilize o tipo de oferta de aplicação gerida nas seguintes condições:

- Está a implementar uma solução baseada em subscrição para o seu cliente utilizando uma máquina virtual (VM) ou uma infraestrutura inteira como uma solução baseada em serviço (IaaS).
- Você ou o seu cliente exige que a solução seja gerida por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um integrador de sistemas ou um prestador de serviços gerido (MSP).  

## <a name="managed-application-offer-requirements"></a>Requisitos de oferta de candidatura geridos

|Requisitos |Detalhes  |
|---------|---------|
|Uma subscrição do Azure. | As aplicações geridas devem ser implementadas para a subscrição de um cliente, mas podem ser geridas por terceiros. |
|Faturação e medição    |  Os recursos são fornecidos na subscrição do Azure de um cliente. Os VMs que utilizam o modelo de pagamento pay-as-you-go são transacionados com o cliente através da Microsoft e faturados através da subscrição azure do cliente. <br><br> Para vMs de trazer a sua própria licença, a Microsoft cobra quaisquer custos de infraestrutura incorridos na subscrição do cliente, mas você transaciona taxas de licenciamento de software com o cliente diretamente.        |
|Um disco rígido virtual compatível com Azure (VHD)    |   Os VMs devem ser construídos em Windows ou Linux.<br><br>Para obter mais informações sobre a criação de um VHD Linux, consulte [as distribuições linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).<br><br>Para obter mais informações sobre a criação de um VHD windows, consulte criar uma oferta de [aplicação Azure.](./partner-center-portal/create-new-azure-apps-offer.md) |

---

> [!NOTE]
> As aplicações geridas devem ser implantáveis através do Azure Marketplace. Se a comunicação do cliente for uma preocupação, contacte os clientes interessados depois de ter ativado a partilha de chumbo.  

> [!Note]
> Um canal parceiro do Cloud Solution Provider (CSP) já está disponível. Para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP, consulte [cloud solution Providers](./cloud-solution-providers.md).

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, aprenda a cultivar o seu negócio de cloud com o [Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)

Para se inscrever e começar a trabalhar no Partner Center:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte Criar uma oferta de [aplicação Azure](./partner-center-portal/create-new-azure-apps-offer.md) para mais informações.
