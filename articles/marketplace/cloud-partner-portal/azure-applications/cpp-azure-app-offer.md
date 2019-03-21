---
title: Oferta de aplicação do Azure | Documentos da Microsoft
description: Descrição geral do processo de publicação de uma aplicação do Azure oferecem no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pbutlerm
ms.openlocfilehash: 9faa38a23b2039902366e5b885ab73c68a2a3d80
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076025"
---
# <a name="azure-application-offer"></a>Oferta de aplicação do Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Esta secção explica como publicar uma nova oferta de aplicação do Azure para o [do Azure Marketplace](https://azuremarketplace.microsoft.com).  Cada aplicação do Azure contém um modelo do Azure Resource Manager que define todos os recursos técnicos usados pelo aplicativo, que normalmente inclui uma ou mais máquinas virtuais e outros serviços de suporte do Azure - ou baseado na Web. Todas as ofertas de aplicações do Azure tem de ativar a segurança de acesso através de [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ícone de aplicações do Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Descrição geral de publicação

O vídeo seguinte, [criando modelos de soluções e aplicações geridas para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), é uma introdução: o que oferece tipos estão disponíveis, o que são ativos técnicos necessários, como criar um Azure Resource Manager modelo, desenvolver e testar a aplicação de interface do Usuário, como publicar a oferta da aplicação e o processo de revisão da aplicação.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Tipos de aplicações do Azure

Existem dois tipos de aplicações do Azure: geridos aplicativos e modelos de soluções. 

- Modelos de soluções são uma das principais formas que publicar uma solução no mercado. Este tipo de oferta é utilizado quando a sua solução requer implementação adicional e automatização de configuração para além de uma única máquina virtual (VM). Pode automatizar o fornecimento de mais do que uma VM com um modelo de solução. Esta automatização inclui o aprovisionamento de recursos de rede e armazenamento para fornecer soluções de IaaS complexas. Para uma descrição geral dos requisitos de modelo de solução e o modelo de faturação, consulte [aplicações do Azure: modelos de soluções](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Aplicações geridas são semelhantes aos modelos de soluções, com uma diferença importante. Numa aplicação gerida, os recursos são implementados num grupo de recursos que é gerido pelo editor da aplicação. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos. Como editor, tem de especificar o custo do suporte contínuo da solução. Utilize as aplicações geridas do Azure para facilmente criar e fornecer aplicações chave na mão totalmente geridas para os seus clientes.

Para além do Azure Marketplace, também pode oferecer aplicativos gerenciados num catálogo de serviços. O catálogo de serviços é um catálogo interno de soluções aprovadas para os utilizadores de uma organização. Utilizar o catálogo para atender aos padrões organizacionais, oferecendo soluções para grupos de uma organização. Os funcionários utilizam o catálogo para localizar facilmente aplicações que são recomendadas e aprovadas pelos departamentos de TI.

>[!Note]
>Fornecedores de soluções (CSP) parceiro canal optar ativamente por participar na cloud agora está disponível.  Veja [fornecedores de soluções Cloud](../../cloud-solution-providers.md) para obter mais informações sobre a sua oferta através do CSP da Microsoft de marketing de parceiros canais.

Para obter mais informações sobre as vantagens e os tipos de aplicativos gerenciados, consulte a [descrição geral de aplicativos de geridas do Azure](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Fluxo de trabalho de processo publicação

O diagrama seguinte mostra o processo de alto nível para publicar uma oferta de aplicação do Azure.

![Fluxo de trabalho para a publicação da oferta](./media/new-offer-process.png)

Os passos de alto nível para publicar uma oferta de aplicação do Azure são:

1. Cumprir os [pré-requisitos](./cpp-prerequisites.md) - (não mostrado) certifique-se de que cumpriu os requisitos comerciais e técnicos para a publicação de uma aplicação do Azure no Azure Marketplace. 

1. [Criar a oferta](./cpp-create-offer.md) -fornecem informações detalhadas sobre a oferta. Estas informações incluem: a descrição da oferta, as especificações dos ativos, informações de suporte e materiais de marketing.

1. [Criar ou recolher o negócio existente e ativos técnicos](./cpp-create-technical-assets.md) -criar os ativos empresariais (documentos legais e materiais de marketing) e ativos técnicos para a solução associada.

1. [Criar o SKU](./cpp-skus-tab.md) -criar o SKU (s) associado com a oferta. Um SKU exclusivo é necessário para cada imagem que pretende publicar.

1. Certificar e [publicar a oferta](./cpp-publish-offer.md) -depois da oferta e os ativos técnicos são concluídos, pode enviar a oferta. Esta submissão inicia o processo de publicação. Durante este processo, a solução é testada, validado, certified, em seguida, "entrar no ar" no Azure Marketplace.

## <a name="next-steps"></a>Passos Seguintes

Antes de considerar estes passos, tem de cumprir os [requisitos técnicos e empresariais](./cpp-prerequisites.md) para publicar uma aplicação gerida no Microsoft Azure Marketplace.
