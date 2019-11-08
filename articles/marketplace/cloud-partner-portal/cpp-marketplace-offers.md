---
title: Ofertas do Azure e AppSource Marketplace
description: Criando e gerenciando ofertas dos Marketplaces do Azure e do AppSource
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pabutler
ms.openlocfilehash: f1e561e84daed6c24ca0d9b7832af3d975082e25
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818764"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Ofertas do Azure e AppSource Marketplace

Esta primeira parte desta seção apresenta as operações gerais usadas para criar e gerenciar ofertas para os Marketplaces do Azure e do AppSource.  Essa parte fornece o plano de fundo que você precisa entender para gerenciar tipos de oferta específicos, bem como informações técnicas que são comuns a todos os tipos de oferta.  A maior parte desta seção contém instruções detalhadas sobre como criar e gerenciar tipos de oferta específicos.  

O vídeo a seguir apresenta os vários recursos e tipos de oferta diferentes disponíveis no Azure Marketplace ou no AppSource.  Ele também aborda aspectos técnicos e comerciais importantes da publicação de um aplicativo ou serviço nesses Marketplaces.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Criando aplicativos e serviços para o Azure Marketplace e o AppSource-Build 2018**

Para obter mais informações sobre esses Marketplaces, consulte o [Guia de publicação do Azure Marketplace e do AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Operações de oferta comum

O processo de criação de uma nova oferta difere muito nos tipos de oferta, por exemplo, entre uma [oferta de aplicativo do Azure](./azure-applications/cpp-azure-app-offer.md) e uma [oferta de serviço de consultoria](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Por outro lado, muitas das outras operações que você executa em uma oferta no [portal do Cloud Partner](https://cloudpartner.azure.com) são bastante padronizadas entre os tipos de oferta.  Essas operações comuns — incluindo publicar, Exibir status, atualizar e excluir — são abordadas na seção [gerenciar ofertas](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Versão de Teste

O *Test Drive* é um recurso do Marketplace que fornece aos clientes uma opção de demonstração "Experimente antes de comprar" para cada oferta, portanto habilitada.  A capacidade do Test Drive é limitada ao seguinte subconjunto de tipos de oferta: [aplicativos do Azure](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 para compromisso com o cliente](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 para finanças e operações](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [SaaS aplicativos](./saas-app/cpp-saas-offer.md)e [máquinas virtuais](./virtual-machine/cpp-virtual-machine-offer.md).  Esse recurso requer que o Publicador crie um modelo de Test Drive, personalizado para sua oferta.  Para obter mais informações, consulte a seção [Test Drive](./test-drive/what-is-test-drive.md).

Você pode procurar as ofertas existentes do Marketplace que têm demonstrações de Test Drive aplicando o [filtro de Test Drive](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Tipos de oferta do Azure Marketplace e AppSource

A tabela a seguir lista os tipos de oferta atuais com suporte no [portal do Cloud Partner](https://cloudpartner.azure.com).  Para cada tipo de oferta, ele lista os Marketplaces nos quais a oferta pode ser listada, bem como uma descrição geral da tecnologia da solução da oferta.

|                Tipo de Oferta                |  Marketplace  |   Descrição                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplicativo do Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | A solução é composta de uma ou mais VMs (máquinas virtuais), código do Azure personalizado opcional, implantado por meio de um modelo do Azure Resource Manager.  A implantação pode ser pelo cliente por meio de um modelo de solução ou gerenciada pelo Publicador. Esse tipo é usado para fornecer mais flexibilidade do que o tipo de oferta de máquina virtual fornecido.  |
| [Serviço de consultoria](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | mesmo | Os consultores qualificados pela Microsoft podem listar seus serviços específicos de domínio no Azure Marketplace ou no AppSource.  Sua experiência de conhecimento ajuda os clientes a avaliar seus problemas e criar e implantar as soluções certas para atender aos seus objetivos de negócios.  |
| [Container](./containers/cpp-containers-offer.md)  | Azure | A solução é uma imagem de contêiner do Docker provisionado como um serviço baseado em kubernetes ou instâncias de contêiner do Azure. |
| [Dynamics 365 Business central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Um pacote que estende o ERP (planejamento de recursos empresariais) e o sistema de gerenciamento de negócios. |
| [Dynamics 365 para compromisso com o cliente](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Um pacote que estende esse sistema de CRM (gerenciamento de recursos do cliente) por meio de seus módulos de vendas, serviço, serviço de projeto e serviço de campo  |
| [Dynamics 365 para finanças e operações](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Um pacote que estende esse serviço de ERP (planejamento de recursos empresariais) que dá suporte ao gerenciamento avançado de finanças, operações, manufatura e gestão de fornecedores |
| [Módulo IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Um contêiner compatível com o Docker que é executado em um dispositivo IoT Edge.  Ele consiste em pequenos módulos computacionais que usam uma combinação de código personalizado, outros serviços do Azure e serviços de terceiros. |
| [Power BI aplicativo](./power-bi/cpp-power-bi-offer.md) | AppSource | Um aplicativo Power BI que empacota conteúdo Power BI personalizável, incluindo conjuntos de gráficos, relatórios e painéis |
| [Aplicativo SaaS](./saas-app/cpp-saas-offer.md) | Azure | A solução é uma assinatura de software como serviço, gerenciada pelo Publicador, que os usuários entram por meio de uma interface personalizada que usa Azure Active Directory. |
| [Máquina virtual](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | A solução está contida em uma única máquina virtual implantada na assinatura do cliente.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Para obter mais informações, consulte [Guia de publicação por tipo de oferta](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Passos seguintes

Você aprenderá sobre as operações gerais que pode executar em ofertas do Marketplace e seus próprios atributos técnicos e ativos no artigo [gerenciar ofertas](./manage-offers/cpp-manage-offers.md).
