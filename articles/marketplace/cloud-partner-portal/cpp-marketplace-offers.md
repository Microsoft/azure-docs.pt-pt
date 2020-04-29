---
title: Ofertas do Mercado Azure e AppSource
description: Criação e gestão de ofertas dos Mercados Azure e AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278489"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Ofertas do Mercado Azure e AppSource

Esta primeira parte desta secção introduz as operações gerais utilizadas para criar e gerir ofertas para os Mercados Azure e AppSource.  Esta peça fornece o fundo que precisa entender para gerir tipos de oferta específicos, bem como informações técnicas que são comuns a todos os tipos de oferta.  A maioria desta secção contém instruções detalhadas sobre como criar e gerir tipos de oferta específicos.  

O vídeo seguinte introduz as várias capacidades e diferentes tipos de oferta disponíveis no Azure Marketplace ou appSource.  Abrange igualmente aspetos técnicos e empresariais importantes da publicação de uma aplicação ou serviço nestes mercados.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Construir Apps e Serviços para Azure Marketplace e AppSource - Build 2018**

Para obter mais informações sobre estes mercados, consulte o [Azure Marketplace e o guia de publicação AppSource.](../marketplace-publishers-guide.md)


## <a name="common-offer-operations"></a>Operações de oferta comum

O processo de criação de uma nova oferta difere muito entre os tipos de oferta, por exemplo, entre uma oferta de [aplicação Azure](./azure-applications/cpp-azure-app-offer.md) e uma oferta de [serviço de consultoria.](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md)  Em contraste, muitas das outras operações que realiza numa oferta no Portal do [Parceiro cloud](https://cloudpartner.azure.com) são bastante padronizadas entre os tipos de oferta.  Estas operações comuns , incluindo publicar, ver estado, atualizar e eliminar , estão abrangidas pela secção [Gerir ofertas](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Versão de Teste

*Test Drive* é uma funcionalidade de marketplace que fornece aos clientes uma opção de demonstração "tente antes de comprar" para cada oferta tão ativada.  A capacidade test drive está limitada ao seguinte subconjunto de tipos de oferta: [Aplicações Azure,](./azure-applications/cpp-azure-app-offer.md) [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), Dynamics [365 para envolvimento com o cliente,](./dyn365ce/cpp-customer-engagement-offer.md) [Dinâmica 365 para Finanças e Operações,](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) [aplicações SaaS](./saas-app/cpp-saas-offer.md)e [máquinas virtuais.](./virtual-machine/cpp-virtual-machine-offer.md)  Esta capacidade requer que a editora crie um modelo test drive, personalizado para a sua oferta.  Para mais informações, consulte a secção [Test Drive](./test-drive/what-is-test-drive.md).

Pode navegar nas ofertas de mercado existentes que têm demonstrações de Test Drive aplicando o filtro de [test drive](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Tipos de oferta sinuoso e appSource do Azure Marketplace e appSource

A tabela seguinte lista os tipos de oferta atual suportados pelo Portal do [Parceiro cloud](https://cloudpartner.azure.com).  Para cada tipo de oferta, ele lista o(s) mercado(s) onde a oferta pode ser listada, bem como uma descrição geral da tecnologia de solução de oferta.

|                Tipo de Oferta                |  Marketplace  |   Descrição                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplicação Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | A solução é composta por uma ou mais máquinas virtuais (VMs), código opcional personalizado Azure, implantado através de um modelo de Manjedoura de Recursos Azure.  A implementação pode ser pelo cliente através de um modelo de solução ou gerida pela editora. Este tipo é utilizado para proporcionar mais flexibilidade do que o tipo de oferta de máquina virtual fornecido.  |
| [Serviço de consultoria](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | ambos | Os consultores qualificados pela Microsoft podem listar os seus serviços específicos de domínio no Azure Marketplace ou no AppSource.  A sua experiência ajuda os clientes a avaliar os seus problemas e a criar e implementar as soluções adequadas para cumprir os seus objetivos de negócio.  |
| [Contentor](./containers/cpp-containers-offer.md)  | Azure | A solução é uma imagem de contentor Docker aprovisionada como um serviço baseado em Kubernetes ou instâncias de Contentores Azure. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Um pacote que alarga este sistema de planeamento de recursos empresariais (ERP) e sistema de gestão de negócios. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Um pacote que alarga este sistema de gestão de recursos de clientes (CRM), através dos seus módulos de vendas, serviços, serviços de projeto e serviço de campo  |
| [Dynamics 365 para Finanças e Operações](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Um pacote que alarga este serviço de planeamento de recursos empresariais (ERP) que suporta finanças avançadas, operações, fabrico e gestão da cadeia de abastecimento |
| [Módulo IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Um recipiente compatível com o Docker que funciona num dispositivo IoT Edge.  É composto por pequenos módulos computacionais que utilizam uma combinação de código personalizado, outros serviços Azure e serviços de terceiros. |
| [Aplicação Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Uma aplicação Power BI que embala conteúdo supreensível power BI, incluindo conjuntos de dados, relatórios e dashboards |
| [App SaaS](./saas-app/cpp-saas-offer.md) | Azure | A Solução é uma subscrição de software como serviço, gerida pela editora, que os utilizadores assinam através de uma interface personalizada que utiliza o Azure Ative Directory. |
| [Máquina virtual](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | A solução está contida numa única máquina virtual implantada para a subscrição do cliente.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Para mais informações, consulte o [guia de publicação por tipo de oferta](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Passos seguintes

Você vai aprender sobre as operações gerais que você pode realizar em ofertas de mercado e seus atributos técnicos comuns e ativos no artigo [Gerir ofertas](./manage-offers/cpp-manage-offers.md).
