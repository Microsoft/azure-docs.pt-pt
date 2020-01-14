---
title: Guia de publicação de oferta de aplicativo gerenciado por aplicativos do Azure
description: Este artigo descreve os requisitos para publicar um aplicativo gerenciado no Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: MaggiePucciEvans
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/14/2018
ms.author: evansma
ms.openlocfilehash: 764212ac148b336b07d29c29a72314c5d889d47c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934658"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplicativos do Azure: guia de publicação de oferta de aplicativo gerenciado

Um aplicativo gerenciado é uma das principais maneiras de publicar uma solução no Marketplace. Use este guia para entender os requisitos para esta oferta. 

Essas são ofertas de transação que são implantadas e cobradas pelo Marketplace. O plano de ação que um usuário vê é "obter agora".

Use o tipo de oferta aplicativo do Azure: aplicativo gerenciado quando as seguintes condições forem necessárias:
- Você implanta uma solução baseada em assinatura para seu cliente usando uma VM ou uma solução inteira baseada em IaaS.
- Você ou seu cliente exigem que a solução seja gerenciada por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um SI ou um provedor de serviços gerenciado (MSP).  

## <a name="managed-application-offer"></a>Oferta de aplicativo gerenciado

|Requisitos |Detalhes  |
|---------|---------|
|Implantado na assinatura do Azure de um cliente | Os aplicativos gerenciados devem ser implantados na assinatura do cliente e podem ser gerenciados por terceiros. | 
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. As máquinas virtuais PAYGO (pré-pagas) serão transacionadas com o cliente pela Microsoft, cobradas por meio da assinatura do Azure do cliente (PAYGO). <br> No caso do traga sua própria licença, enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você receberá suas tarifas de licenciamento de software diretamente para o cliente.        |
|VHD (disco rígido virtual) compatível com o Azure    |   As VMs devem ser criadas no Windows ou no Linux.<ul> <ul> <li>Para obter mais informações sobre como criar um VHD do Linux, consulte [distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre como criar um VHD do Windows, consulte [criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Os aplicativos gerenciados devem ser implantados por meio do Marketplace. Se a comunicação do cliente for uma preocupação, você deverá entrar em contato com clientes interessados depois de habilitar o compartilhamento de leads.  

>[!Note]
>A aceitação do canal de parceiros do CSP (provedores de soluções na nuvem) já está disponível.  Consulte os [provedores de soluções de nuvem](./cloud-solution-providers.md) para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP.

## <a name="next-steps"></a>Passos seguintes
Se você ainda não fez isso, 

- [Registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- [Entre no portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
