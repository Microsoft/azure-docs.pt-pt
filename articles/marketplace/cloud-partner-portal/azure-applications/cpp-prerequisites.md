---
title: Aplicativo Azure oferecer pré-requisitos | Azure Marketplace
description: Os pré-requisitos para a publicação de uma oferta de aplicativo do Azure no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 261af94e233bdb6189d7819f8f28c7e23b5dd112
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826147"
---
# <a name="azure-application-prerequisites"></a>Pré-requisitos do aplicativo do Azure

Este artigo descreve os pré-requisitos técnicos e comerciais para a publicação de uma oferta de aplicativo gerenciado no Azure Marketplace.  Se você ainda não tiver feito isso, examine as seguintes fontes de informação:
- Dependendo do tipo de SKU, [os aplicativos do Azure: guia de publicação da oferta de modelo de solução](../../marketplace-solution-templates.md) ou [aplicativos do Azure: guia de publicação da oferta de aplicativo gerenciado](../../marketplace-managed-apps.md)
- [Criando modelos de solução e aplicativos gerenciados para o vídeo do Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)


## <a name="technical-requirements"></a>Requisitos técnicos

Os requisitos técnicos incluem os seguintes itens:

*   Azure Resource Manager modelos para obter mais informações, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Este artigo descreve a estrutura de um modelo de Azure Resource Manager. Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis nessas seções. O modelo consiste em JSON e expressões que você pode usar para construir valores para sua implantação. 
* Modelos de início rápido do Azure.<br> Para obter mais informações, consulte:

  * [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/). Implemente recursos do Azure através do Gestor de Recursos do Azure com modelos fornecidos pela comunidade para produzir mais. O Gestor de Recursos do Azure permite utilizar modelos declarativos para aprovisionar as suas aplicações. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Pode utilizar o mesmo modelo para implementar repetidamente a sua aplicação durante cada fase do ciclo de vida da aplicação.
  * [Github: Azure Resource Manager modelos de início rápido](https://github.com/azure/azure-quickstart-templates). Este repositório contém todos os modelos de Azure Resource Manager disponíveis no momento que contribuíram pela Comunidade. Um índice de modelo pesquisável é mantido em https://azure.microsoft.com/documentation/templates/.
* Criar definição de interface do usuário<br>
Para obter mais informações, consulte [criar portal do Azure interface do usuário para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Este artigo apresenta os principais conceitos do arquivo createUiDefinition. JSON. O portal do Azure usa esse arquivo para gerar a interface do usuário para criar um aplicativo gerenciado.


## <a name="business-requirements"></a>Requisitos comerciais

Os requisitos de negócios incluem o seguinte procedimento, contratuais e obrigações legais:

* Você deve ser um Publicador do Cloud Marketplace registrado. Se você não estiver registrado, siga as etapas no artigo [tornar-se um editor de Marketplace de nuvem](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>Você deve usar a mesma conta de registro do Microsoft Developer Center para entrar no Portal do Cloud Partner. Você deve ter apenas um conta Microsoft para suas ofertas do Azure Marketplace. Essa conta não deve ser específica a serviços individuais ou ofertas.

* Sua empresa (ou subsidiária) deve estar em uma região de venda/país com suporte do Azure Marketplace. Para obter uma lista atual desses países/regiões, consulte [Microsoft Azure Marketplace políticas de participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Seu produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Azure Marketplace. Para obter mais informações, consulte [Opções de cobrança](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) no Azure Marketplace.
* Você é responsável por disponibilizar o suporte técnico aos clientes de maneira comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens da Comunidade.
* Você é responsável por licenciar seu software e quaisquer dependências de software de terceiros.
* Você deve fornecer conteúdo que atenda aos critérios de sua oferta a ser listado no Azure Marketplace e no portal do Azure.
* Você deve concordar com os termos das políticas de participação Microsoft Azure Marketplace e o contrato do editor.
* Você deve obedecer à Microsoft Azure termos de uso do site, política de privacidade da Microsoft e contrato de programa certificado Microsoft Azure.


## <a name="publishing-requirements"></a>Requisitos de publicação

Para publicar uma nova oferta de aplicativo do Azure, você deve atender aos seguintes pré-requisitos:

* Seus metadados estão prontos para uso. A lista a seguir (não exaustiva) mostra um exemplo desses metadados:
  * Um título
  * Uma descrição (no formato HTML)
  * Uma imagem de logotipo (no formato PNG) e nesses tamanhos fixos de imagem: 40 x 40 pixels, 90 x 90 pixels, 115 x 115 pixels e 255 x 115 pixels.
* Os *termos de uso* e os documentos de *política de privacidade*
* Documentação do aplicativo
* Contatos de suporte


## <a name="next-steps"></a>Passos seguintes

Depois de atender a todos os requisitos, você estará pronto para [criar uma oferta de aplicativo do Azure](./cpp-create-offer.md). 
 
