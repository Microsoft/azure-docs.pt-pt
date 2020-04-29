---
title: As aplicações do Azure gerem o guia de publicação de candidaturas Mercado Azure
description: Este artigo descreve os requisitos para publicar uma aplicação gerida no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084877"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Aplicações Azure: Oferta de candidatura gerida oferece requisitos de publicação

Este artigo explica os requisitos para o tipo de oferta de aplicação gerida, que é uma forma de publicar uma oferta de aplicação Azure no Azure Marketplace. As aplicações geridas são ofertas transacionais que são implantadas e faturadas através do Mercado Azure. A chamada à ação que um utilizador vê é "Get It Now".

Utilize o tipo de oferta de aplicação gerida quando forem necessárias as seguintes condições:

- Implementa uma solução baseada em subscrição para o seu cliente utilizando um VM ou uma solução inteira baseada em IaaS.
- Você ou o seu cliente exigem que a solução seja gerida por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um SI ou um prestador de serviços gerido (MSP).  

## <a name="managed-application-offer"></a>Oferta de Candidatura Gerida

|Requisitos |Detalhes  |
|---------|---------|
|Implantado para a subscrição do Azure de um cliente | As Aplicações Geridas devem ser implementadas na subscrição do cliente e podem ser geridas por terceiros. |
|Faturação e medição    |  Os recursos serão aprovisionados na subscrição do Azure do cliente. As máquinas virtuais pay-as-you-go (PAYGO) serão transacionadas com o cliente através da Microsoft, faturadas através da subscrição azure do cliente (PAYGO). <br> No caso de trazer a sua própria licença, enquanto a Microsoft irá cobrar os custos de infraestrutura incorridos na subscrição do cliente, irá transacionar as suas taxas de licenciamento de software diretamente para o cliente.        |
|Disco rígido virtual compatível com azure (VHD)    |   Os VMs devem ser construídos em Windows ou Linux.<ul> <ul> <li>Para obter mais informações sobre a criação de um VHD Linux, consulte [as distribuições linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre a criação de um VHD windows, consulte criar uma oferta de [aplicação Azure.](./partner-center-portal/create-new-azure-apps-offer.md)</li> </ul> |

>[!NOTE]
> As aplicações geridas devem ser implantáveis através do Marketplace. Se a comunicação do cliente é uma preocupação, então deve contactar os clientes interessados depois de ter ativado a partilha de chumbo.  

>[!Note]
>O canal parceiro Cloud Solution Providers (CSP) já está disponível. Para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP, consulte [cloud solution Providers](./cloud-solution-providers.md).

## <a name="next-steps"></a>Passos seguintes

- Se ainda não o fez, [saiba](https://azuremarketplace.microsoft.com/sell) mais sobre o Mercado Azure.
- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- [Crie uma oferta de aplicação Azure](./partner-center-portal/create-new-azure-apps-offer.md) para mais informações.
