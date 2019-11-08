---
title: Oferta de Aplicativo Azure | Azure Marketplace
description: Visão geral do processo de publicação de uma oferta de Aplicativo Azure no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pabutler
ms.openlocfilehash: 9125b5c71b63b27c58ea72b7bfd49f730854b33d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818797"
---
# <a name="azure-application-offer"></a>Oferta da aplicação do Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Esta seção explica como publicar uma nova oferta de aplicativo do Azure no [Azure Marketplace](https://azuremarketplace.microsoft.com).  Cada aplicativo do Azure contém um modelo de Azure Resource Manager que define todos os ativos técnicos usados pelo aplicativo, que normalmente inclui uma ou mais máquinas virtuais e outros serviços de suporte do Azure ou baseados na Web. Todas as ofertas de aplicativo do Azure devem habilitar a segurança de acesso por meio de [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ícone de aplicativos do Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Visão geral da publicação

O vídeo a seguir, [criação de modelos de solução e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), é uma introdução: quais tipos de oferta estão disponíveis, quais ativos técnicos são necessários, como criar um modelo de Azure Resource Manager, desenvolver e testando a interface do usuário do aplicativo, como publicar a oferta de aplicativo e o processo de revisão do aplicativo.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Tipos de aplicativos do Azure

Há dois tipos de aplicativos do Azure: aplicativos gerenciados e modelos de solução. 

- Os modelos de solução são uma das principais maneiras de publicar uma solução no Marketplace. Esse tipo de oferta é usado quando sua solução requer implantação adicional e automação de configuração além de uma única máquina virtual (VM). Você pode automatizar o fornecimento de mais de uma VM usando um modelo de solução. Essa automação inclui o provisionamento de recursos de rede e de armazenamento para fornecer soluções complexas de IaaS. Para obter uma visão geral dos requisitos de modelo de solução e do modelo de cobrança, consulte [aplicativos do Azure: modelos de solução](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Os aplicativos gerenciados são semelhantes aos modelos de solução, com uma diferença importante. Numa aplicação gerida, os recursos são implementados num grupo de recursos que é gerido pelo editor da aplicação. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos. Como editor, tem de especificar o custo do suporte contínuo da solução. Use aplicativos gerenciados do Azure para criar e fornecer facilmente aplicativos completos e totalmente gerenciados para seus clientes.

Além do Azure Marketplace, você também pode oferecer aplicativos gerenciados em um catálogo de serviços. O catálogo de serviços é um catálogo interno de soluções aprovadas para os utilizadores de uma organização. Você usa o catálogo para atender aos padrões organizacionais ao mesmo tempo em que oferece soluções para grupos em uma organização. Os funcionários utilizam o catálogo para localizar facilmente aplicações que são recomendadas e aprovadas pelos departamentos de TI.

>[!Note]
>A aceitação do canal de parceiros do CSP (provedores de soluções na nuvem) já está disponível.  Consulte os [provedores de soluções de nuvem](../../cloud-solution-providers.md) para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP.

Para obter mais informações sobre as vantagens e os tipos de aplicativos gerenciados, consulte [visão geral dos aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Fluxo de trabalho do processo de publicação

O diagrama a seguir mostra o processo de alto nível para a publicação de uma oferta de aplicativo do Azure.

![Fluxo de trabalho para a oferta de publicação](./media/new-offer-process.png)

As etapas de alto nível para a publicação de uma oferta de aplicativo do Azure são:

1. Conheça os [pré-requisitos](./cpp-prerequisites.md) -(não mostrado) Verifique se você atendeu aos requisitos técnicos e de negócios para publicar um aplicativo do Azure no Azure Marketplace. 

1. [Criar a oferta](./cpp-create-offer.md) -forneça informações detalhadas sobre a oferta. Essas informações incluem: a descrição da oferta, materiais de marketing, informações de suporte e especificações de ativos.

1. [Criar ou coletar ativos técnicos e de negócios existentes](./cpp-create-technical-assets.md) -crie os ativos de negócios (documentos legais e materiais de marketing) e ativos técnicos para a solução associada.

1. [Criar a SKU](./cpp-skus-tab.md) -crie as SKU associadas à oferta. Um SKU exclusivo é necessário para cada imagem que você pretende publicar.

1. Certificar e [publicar a oferta](./cpp-publish-offer.md) -após a oferta e os ativos técnicos serem concluídos, você pode enviar a oferta. Esse envio inicia o processo de publicação. Durante esse processo, a solução é testada, validada, certificada e, em seguida, "fica ativa" no Azure Marketplace.

## <a name="next-steps"></a>Passos seguintes

Antes de considerar essas etapas, você deve atender aos [requisitos técnicos e de negócios](./cpp-prerequisites.md) para publicar um aplicativo gerenciado no Microsoft Azure Marketplace.
