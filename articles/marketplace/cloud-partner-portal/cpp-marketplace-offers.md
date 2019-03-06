---
title: O Azure e ofertas do Marketplace do AppSource | Documentos da Microsoft
description: Criação e gestão de ofertas do Azure e mercados de AppSource
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pbutlerm
ms.openlocfilehash: fd51a9f70d60219a9429ff66d6f1d404391f6fb0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446714"
---
# <a name="azure-and-appsource-marketplace-offers"></a>O Azure e ofertas do Marketplace do AppSource

Nesta primeira parte desta secção apresenta as operações gerais utilizadas para criar e gerir ofertas para o Azure e mercados de AppSource.  Esta parte fornece em segundo plano, que precisa entender para gerir tipos de ofertas específicos, bem como as informações técnicas, que é comuns a todos os tipos de oferta.  A maioria desta secção contém instruções detalhadas sobre como criar e gerir tipos de ofertas específicos.  

O vídeo seguinte apresenta os vários recursos e tipos de ofertas diferentes disponíveis no Azure Marketplace ou no AppSource.  Também aborda técnicas importantes e os aspectos comerciais de publicar uma aplicação ou serviço nesses mercados.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Criar aplicações e serviços para o Azure Marketplace e AppSource - Build 2018**

Para obter mais informações sobre esses mercados, consulte [guia de publicação do Azure Marketplace e AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Operações de oferta comuns

O processo de criação de uma nova oferta difere bastante em tipos de oferta, por exemplo entre uma [oferta de aplicação do Azure](./azure-applications/cpp-azure-app-offer.md) e uma [consultoria oferta de serviço](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Por outro lado, muitas das outras operações de executar a numa oferta no [Cloud Partner Portal](https://cloudpartner.azure.com) bastante são padronizadas em todos os tipos de oferta.  Estas operações comuns — incluindo publicar, ver o estado, update e delete — são abordados na secção [gerir ofertas](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Versão de Teste

*O Test Drive* é uma funcionalidade do marketplace que fornece aos clientes uma opção de demonstração "experimentar antes de o comprar" para cada oferta então ativada.  A capacidade de teste de unidade é limitada ao subconjunto seguinte de tipos de oferta: [Aplicações do Azure](./azure-applications/cpp-azure-app-offer.md), [do Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [do Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), [do Dynamics 365 para operações e finanças](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [ Aplicações SaaS](./saas-app/cpp-saas-offer.md), e [máquinas virtuais](./virtual-machine/cpp-virtual-machine-offer.md).  Esse recurso requer o publicador para criar um modelo de versão de teste personalizado para a sua oferta.  Para obter mais informações, consulte a secção [Test-Drive](./test-drive/what-is-test-drive.md).

Pode procurar as ofertas do marketplace existentes que tenham demonstrações de versão de teste ao aplicar a [filtro de unidade de teste](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Tipos de oferta do Azure Marketplace e AppSource

A tabela seguinte lista os tipos de oferta atual suportados pelos [Cloud Partner Portal](https://cloudpartner.azure.com).  Para cada tipo de oferta, lista marketplace(s) em que a oferta pode ser apresentada, bem como uma descrição geral da tecnologia da solução de oferta.

|                Tipo de Oferta                |  Marketplace  |   Descrição                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplicação do Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | Solução é composta por um ou mais máquinas virtuais (VMs), código do Azure personalizado opcional, implementadas através de um modelo Azure Resource Manager.  Implementação pode ser uma pelo cliente através de um modelo de solução ou gerido pelo publicador. Este tipo é utilizado para fornecer mais flexibilidade do que o tipo de oferta de máquina virtual fornecido.  |
| [Serviço de consultoria](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Ambos | Consultores da Microsoft qualificado podem listar os serviços de domínio específico no Azure Marketplace ou no AppSource.  Seus conhecimentos auxiliam os clientes a avaliar seus problemas e a criação e a implementar as soluções certas para cumprir os seus objetivos empresariais.  |
| [Contentor](./containers/cpp-containers-offer.md)  | Azure | Solução é uma imagem de contentor do Docker aprovisionada como um serviço baseado em Kubernetes ou instâncias de contentor do Azure. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Um pacote que expande esse planejamento de recursos empresariais (ERP) e o sistema de gerenciamento de negócios. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Um pacote que estende esse cliente sistema (CRM gestão) de recursos, por meio de suas vendas, serviço, o serviço de projeto e módulos do serviço de campo.  |
| [Dynamics 365 para operações e finanças](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Um pacote que expande este serviço (ERP) que suporta a avançada de planeamento de recursos do enterprise Finanças, operações, fabrico e fornecer gestão de cadeias. |
| [Módulo do IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Um contentor de Docker-compatíveis com o que é executado num dispositivo IoT Edge.  Ela contém de módulos de computacionais pequeno que usar uma combinação de código personalizado, outros serviços do Azure e serviços de terceiros 3rd. |
| [Aplicação do Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Um pacote que utiliza fluxos de dados para ligar relatórios e dashboards a dados no armazenamento de dados comuns. |
| [Aplicação SaaS](./saas-app/cpp-saas-offer.md) | Azure | Solução é uma subscrição de software-como-serviço, gerenciada pelo publicador, os utilizadores que iniciem sessão através de uma interface personalizada que tira partido do Azure Active Directory. |
| [Máquina virtual](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Solução está contida dentro de uma única máquina virtual implementada para a subscrição do cliente.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Para obter mais informações, consulte [publicação guia por tipo de oferta](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Passos Seguintes

Ficará a par das operações gerais que pode efetuar em ofertas do marketplace e seus atributos técnicos comuns e os ativos no tópico [gerir ofertas](./manage-offers/cpp-manage-offers.md).
