---
title: Oferta de candidatura Azure [ Oferta de Candidatura Azul ] Mercado Azure
description: Visão geral do processo de publicação de uma oferta de aplicação Azure no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: dsindona
ms.openlocfilehash: ed086ffdc49e21b819c0ee05b38ad882b4e269d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285321"
---
# <a name="azure-application-offer"></a>Oferta da aplicação do Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Esta secção explica como publicar uma nova oferta de aplicação Azure ao [Azure Marketplace.](https://azuremarketplace.microsoft.com)  Cada aplicação Azure contém um modelo de Gestor de Recursos Azure que define todos os ativos técnicos utilizados pela aplicação, que normalmente inclui uma ou mais máquinas virtuais e outros serviços baseados no Azure ou na Web. Todas as ofertas de aplicações Azure devem permitir a segurança de acesso através do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/)  </div> | ![Ícone de aplicativos Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Visão geral de publicação

O seguinte vídeo, Modelos de [Soluções de Construção e Aplicações Geridas para o Mercado Azure,](https://channel9.msdn.com/Events/Build/2018/BRK3603)é uma introdução: que tipos de oferta estão disponíveis, que ativos técnicos são necessários, como autor de um modelo de Gestor de Recursos Azure, desenvolvendo e testando a app UI, como publicar a oferta da app, e o processo de revisão de aplicações.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Tipos de aplicações Azure

Existem dois tipos de aplicações Azure: aplicações geridas e modelos de solução. 

- Os modelos de solução são uma das principais formas de publicar uma solução no Mercado. Este tipo de oferta é utilizado quando a sua solução requer uma implementação adicional e automatização de configuração para além de uma única máquina virtual (VM). Pode automatizar o fornecimento de mais de um VM utilizando um modelo de solução. Esta automatização inclui o fornecimento de recursos de networking e armazenamento para fornecer soluções complexas de IaaS. Para uma visão geral dos requisitos do modelo de solução e do modelo de faturação, consulte [aplicações Azure: modelos de solução](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- As aplicações geridas são semelhantes aos modelos de solução, com uma diferença fundamental. Numa aplicação gerida, os recursos são implementados num grupo de recursos que é gerido pelo editor da aplicação. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos. Como editor, tem de especificar o custo do suporte contínuo da solução. Utilize aplicações geridas pelo Azure para construir e entregar aplicações chave na mão totalmente geridas aos seus clientes.

Além do Azure Marketplace, também pode oferecer aplicações geridas num catálogo de serviços. O catálogo de serviços é um catálogo interno de soluções aprovadas para os utilizadores de uma organização. Você usa o catálogo para cumprir os padrões organizacionais, ao mesmo tempo que oferece soluções para grupos numa organização. Os funcionários utilizam o catálogo para localizar facilmente aplicações que são recomendadas e aprovadas pelos departamentos de TI.

>[!Note]
>O canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte os [Fornecedores de Soluções cloud](../../cloud-solution-providers.md) para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP.

Para obter mais informações sobre as vantagens e tipos de aplicações geridas, consulte a visão geral das [aplicações geridas pelo Azure.](https://docs.microsoft.com/azure/managed-applications/overview)


## <a name="publishing-process-workflow"></a>Fluxo de trabalho de processo de publicação

O diagrama seguinte mostra o processo de alto nível para a publicação de uma oferta de aplicação Azure.

![Fluxo de trabalho para oferta de publicação](./media/new-offer-process.png)

Os passos de alto nível para a publicação de uma oferta de aplicação Azure são:

1. Conheça os [Pré-requisitos](./cpp-prerequisites.md) - (Não mostrado) Verifique se cumpriu os requisitos comerciais e técnicos para a publicação de uma aplicação Azure no Azure Marketplace. 

1. [Criar a oferta](./cpp-create-offer.md) - Forneça informações detalhadas sobre a oferta. Estas informações incluem: descrição da oferta, materiais de marketing, informações de suporte e especificações de ativos.

1. [Criar ou recolher ativos empresariais e técnicos existentes](./cpp-create-technical-assets.md) - Criar os ativos empresariais (documentos legais e materiais de marketing) e ativos técnicos para a solução associada.

1. [Criar o SKU](./cpp-skus-tab.md) - Criar o SKU(s) associado à oferta. É necessário um SKU único para cada imagem que planeia publicar.

1. Certifique e [publique a oferta](./cpp-publish-offer.md) - Depois de concluída a oferta e os ativos técnicos, pode submeter a oferta. Esta submissão inicia o processo de publicação. Durante este processo, a solução é testada, validada, certificada e depois "vai ao vivo" no Azure Marketplace.

## <a name="next-steps"></a>Passos seguintes

Antes de considerar estas etapas, deve cumprir os [requisitos técnicos e empresariais](./cpp-prerequisites.md) para a publicação de uma aplicação gerida no Microsoft Azure Marketplace.
