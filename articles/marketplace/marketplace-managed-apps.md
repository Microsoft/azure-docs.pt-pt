---
title: Guia de publicação oferta de aplicação gerida de aplicações do Azure
description: Este artigo descreve os requisitos para publicar uma aplicação gerida no Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: 15c559bb8b357b7776c101f88db8316b05edb677
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764242"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplicações do Azure: Guia de publicação de oferta de aplicação gerida

Uma aplicação gerida é uma das principais formas que publicar uma solução no mercado. Utilize este guia para compreender os requisitos para esta oferta. 

Estes são ofertas de transação que são implementadas e faturadas através do Marketplace. A chamada a ação que um utilizador vê é a "Obter agora."

Utilizar a aplicação do Azure: geridos o tipo de oferta de aplicação quando as condições seguintes são necessárias:
- Implementar a uma solução baseada na subscrição para o seu cliente através de uma VM ou uma solução baseada em IaaS inteira.
- Ou seu cliente requerem que a solução é gerida por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser uma SI ou o fornecedor de serviços geridos (MSP).  

## <a name="managed-application-offer"></a>Gerido oferta de aplicação

|Requisitos |Detalhes  |
|---------|---------|
|Implementado a subscrição de um cliente do Azure | Aplicações geridas, tem de ser implementadas numa subscrição do cliente e podem ser geridas por uma entidade de 3 | 
|Faturação e medição    |  Os recursos serão aprovisionados na subscrição do Azure do cliente. Máquinas de virtuais de pay as you go (em PAYGO) será transacionadas com o cliente através da Microsoft, cobrado por meio da subscrição do cliente do Azure (em PAYGO) 
No caso de bring-your-own-license, embora a Microsoft irá cobrar os custos de infraestrutura da subscrição de cliente, será transact seu software diretamente de taxas para o cliente de licenciamento        |
|Compatível com o Azure de disco rígido virtual (VHD)    |   As VMs devem ser criadas no Windows ou Linux.<ul> <ul> <li>Para obter mais informações sobre a criação de um VHD do Linux, consulte [distribuições do Linux apoiadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre a criação de um VHD do Windows, consulte [criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Aplicações geridas tem de ser implementáveis através do Marketplace. Se a comunicação de cliente é uma preocupação, em seguida, deve entrar em contacto para clientes interessados depois de ter ativado a partilha de oportunidades potenciais.  

>[!Note]
>Fornecedores de soluções (CSP) parceiro canal optar ativamente por participar na cloud agora está disponível.  Veja [fornecedores de soluções Cloud](./cloud-solution-providers.md) para obter mais informações sobre a sua oferta através do CSP da Microsoft de marketing de parceiros canais.

## <a name="next-steps"></a>Passos Seguintes
Se ainda não fez isso, 

- [Registar](https://azuremarketplace.microsoft.com/sell) no marketplace.

Se é registrado e estiver a criar uma nova oferta ou trabalhando num já existente

- [Inicie sessão no Portal de parceiro de Cloud](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
