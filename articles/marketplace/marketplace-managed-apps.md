---
title: Guia de publicação de oferta de aplicações geridas por aplicações do Azure
description: Este artigo descreve os requisitos para publicar uma aplicação gerida no Mercado
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: dsindona
ms.openlocfilehash: 38323ecfee69460b16542a3e004513f6a7389c41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288415"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplicações Azure: Guia de publicação de oferta de aplicação gerida

Uma aplicação gerida é uma das principais formas de publicar uma solução no Mercado. Utilize este guia para compreender os requisitos desta oferta. 

Estas são ofertas de transações que são implementadas e faturadas através do Marketplace. A chamada à ação que um utilizador vê é "Get It Now".

Utilize a aplicação Azure: tipo de oferta de aplicações gerida quando forem necessárias as seguintes condições:
- Implementa uma solução baseada em subscrição para o seu cliente utilizando um VM ou uma solução inteira baseada em IaaS.
- Você ou o seu cliente exigem que a solução seja gerida por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um SI ou um prestador de serviços gerido (MSP).  

## <a name="managed-application-offer"></a>Oferta de Candidatura Gerida

|Requisitos |Detalhes  |
|---------|---------|
|Implantado para a subscrição do Azure de um cliente | As Aplicações Geridas devem ser implementadas na subscrição do cliente e podem ser geridas por terceiros. | 
|Faturação e medição    |  Os recursos serão aprovisionados na subscrição do Azure do cliente. As máquinas virtuais pay-as-you-go (PAYGO) serão transacionadas com o cliente através da Microsoft, faturadas através da subscrição azure do cliente (PAYGO). <br> No caso de trazer a sua própria licença, enquanto a Microsoft irá cobrar os custos de infraestrutura incorridos na subscrição do cliente, irá transacionar as suas taxas de licenciamento de software diretamente para o cliente.        |
|Disco rígido virtual compatível com azure (VHD)    |   Os VMs devem ser construídos em Windows ou Linux.<ul> <ul> <li>Para obter mais informações sobre a criação de um VHD Linux, consulte [as distribuições linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre a criação de um VHD windows, consulte [Criar um VHD compatível com Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> As aplicações geridas devem ser implantáveis através do Marketplace. Se a comunicação do cliente é uma preocupação, então deve contactar os clientes interessados depois de ter ativado a partilha de chumbo.  

>[!Note]
>O canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte os [Fornecedores de Soluções cloud](./cloud-solution-providers.md) para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP.

## <a name="next-steps"></a>Passos seguintes
Se ainda não o fez, 

- [Registe-se](https://azuremarketplace.microsoft.com/sell) no mercado.

Se está registado e está a criar uma nova oferta ou a trabalhar numa já existente,

- [Inscreva-se no Portal do Parceiro cloud](https://cloudpartner.azure.com) para criar ou completar a sua oferta.
