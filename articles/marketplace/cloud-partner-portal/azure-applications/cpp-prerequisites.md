---
title: Oferta de candidatura Azure Mercado Azure
description: Os pré-requisitos para a publicação de uma oferta de aplicação Azure no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b927404758dc59419fb3c5e638ac32758d534681
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281753"
---
# <a name="azure-application-prerequisites"></a>Pré-requisitos de aplicação Azure

Este artigo descreve os pré-requisitos técnicos e empresariais para a publicação de uma oferta de candidatura gerida no Mercado Azure.  Se ainda não o fez, reveja as seguintes fontes de informação:
- Dependendo do seu tipo SKU, ou [aplicações Azure: Guia](../../marketplace-solution-templates.md) de publicação de modelo de solução ou [aplicações azure: Guia](../../marketplace-managed-apps.md) de publicação de oferta de aplicação gerida
- [Modelos de solução de construção e aplicações geridas para o](https://channel9.msdn.com/Events/Build/2018/BRK3603) vídeo do Azure Marketplace


## <a name="technical-requirements"></a>Requisitos técnicos

Os requisitos técnicos incluem os seguintes itens:

*   Modelos de Gestor de Recursos Azure Para mais informações, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Este artigo descreve a estrutura de um modelo de Gestor de Recursos Azure. Apresenta as diferentes secções de um modelo e as propriedades que estão disponíveis nessas secções. O modelo consiste em JSON e expressões que pode usar para construir valores para a sua implementação. 
* Modelos Azure Quickstart.<br> Para obter mais informações, consulte:

  * [Modelos azure quickstart](https://azure.microsoft.com/documentation/templates/). Desloque os recursos azure através do Gestor de Recursos Azure com modelos comunitários para obter mais. O Azure Resource Manager permite-lhe utilizar as suas aplicações com um modelo declarativo. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Pode utilizar o mesmo modelo para implementar repetidamente a sua aplicação durante cada fase do ciclo de vida da aplicação.
  * [GitHub: Modelos quickstart do Gestor de Recursos Azure](https://github.com/azure/azure-quickstart-templates). Este repo contém todos os modelos de Gestor de Recursos Azure atualmente disponíveis contribuídos pela comunidade. Um índice de modelo pesquisável é mantido em https://azure.microsoft.com/documentation/templates/.
* Criar definição ui<br>
Para mais informações, consulte a interface de utilizador do [portal Create Azure para a sua aplicação gerida.](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview) Este artigo introduz os conceitos fundamentais do ficheiro createUiDefinition.json. O portal Azure utiliza este ficheiro para gerar a interface de utilizador para criar uma aplicação gerida.


## <a name="business-requirements"></a>Requisitos empresariais

Os requisitos comerciais incluem as seguintes obrigações processuais, contratuais e legais:

* Deve ser um Editor de Cloud Marketplace registado. Se não estiver registado, siga os passos do artigo [Torne-se um Editor do Mercado de Nuvem](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>Deve utilizar a mesma conta de registo do Microsoft Developer Center para iniciar sessão no Portal do Parceiro cloud. Você deve ter apenas uma conta Microsoft para as suas ofertas do Azure Marketplace. Esta conta não deve ser específica para serviços ou ofertas individuais.

* A sua empresa (ou sua subsidiária) deve estar numa região de venda/região apoiada pelo Azure Marketplace. Para obter uma lista atual destes países/regiões, consulte as Políticas de [Participação no Mercado do Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* O seu produto deve ser licenciado de forma compatível com modelos de faturação suportados pelo Azure Marketplace. Para mais informações, consulte [as opções de faturação](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) no Azure Marketplace.
* É responsável por disponibilizar apoio técnico aos clientes de forma comercialmente razoável. Este apoio pode ser gratuito, pago ou através de abordagens comunitárias.
* É responsável por licenciar o seu software e quaisquer dependências de software de terceiros.
* Deve fornecer conteúdo que satisfaça os critérios para a sua oferta ser listada no Azure Marketplace e no portal Azure.
* Deve concordar com os termos das Políticas de Participação no Mercado do Microsoft Azure e do Acordo de Editor.
* Deve cumprir os Termos de Utilização do Website microsoft Azure, a Declaração de Privacidade da Microsoft e o Acordo de Programa Certificado Microsoft Azure.


## <a name="publishing-requirements"></a>Requisitos de publicação

Para publicar uma nova oferta de aplicação Azure, deve cumprir os seguintes pré-requisitos:

* Tenha os seus metadados prontos a usar. A seguinte lista (não exaustiva) mostra um exemplo destes metadados:
  * Um título
  * Uma descrição (em formato HTML)
  * Uma imagem de logotipo (em formato PNG) e nestes tamanhos de imagem fixas: 40 x 40 pixels, 90 x 90 pixels, 115 x 115 pixels e 255 x 115 pixels.
* Um *Termos de Uso* e documentos de Política de *Privacidade*
* Documentação de aplicação
* Contactos de suporte


## <a name="next-steps"></a>Passos seguintes

Uma vez cumprido todos os requisitos, estará pronto para criar uma oferta de [aplicação Azure.](./cpp-create-offer.md) 
 
