---
title: Pré-requisitos da oferta do aplicativo do Azure | Documentos da Microsoft
description: Os pré-requisitos para a publicação de uma aplicação do Azure oferecem no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: df127eec662f8598246f276ef9b1608ae3021512
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007152"
---
# <a name="azure-application-prerequisites"></a>Pré-requisitos de aplicação do Azure

Este artigo descreve os pré-requisitos técnicos e empresariais para publicar uma oferta de aplicação gerida no Azure Marketplace.  Se ainda não o fez, reveja as seguintes fontes de informações:
- Consoante o tipo de SKU ou [aplicativos do Azure: Guia de publicação da oferta do modelo de solução](../../marketplace-solution-templates.md) ou [aplicações do Azure: Guia de publicação de oferta de aplicação gerida](../../marketplace-managed-apps.md)
- [Criação de modelos de soluções e aplicações geridas para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) vídeo


## <a name="technical-requirements"></a>Requisitos técnicos

Os requisitos técnicos incluem os seguintes itens:

*   Modelos do Azure Resource Manager para obter mais informações, consulte [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Este artigo descreve a estrutura de um modelo Azure Resource Manager. Ela apresenta as diferentes secções de um modelo e as propriedades que estão disponíveis dessas secções. O modelo é constituído por JSON e expressões que pode utilizar para construir valores para a sua implementação. 
* Modelos de início rápido do Azure.<br> Para obter mais informações, consulte:

  * [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/). Implemente recursos do Azure através do Gestor de Recursos do Azure com modelos fornecidos pela comunidade para produzir mais. O Gestor de Recursos do Azure permite utilizar modelos declarativos para aprovisionar as suas aplicações. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Pode utilizar o mesmo modelo para implementar repetidamente a sua aplicação durante cada fase do ciclo de vida da aplicação.
  * [GitHub: Modelos de início rápido do Azure Resource Manager](https://github.com/azure/azure-quickstart-templates). Este repositório contém todos os os atualmente disponíveis do Azure Resource Manager modelos contribuídos pela Comunidade. Um índice pesquisável modelo seja mantido no https://azure.microsoft.com/en-us/documentation/templates/.
* Criar definição de interface do Usuário<br>
Para obter mais informações, consulte [interface de utilizador do portal do Azure de criar para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Este artigo apresenta os principais conceitos do ficheiro Createuidefinition. O portal do Azure utiliza este ficheiro para gerar a interface do usuário para a criação de um aplicativo gerenciado.


## <a name="business-requirements"></a>Requisitos comerciais

Os requisitos comerciais incluem as seguinte obrigações de procedimentos, contratuais e legais:

* Tem de ser um publicador de Marketplace na nuvem registados. Se não estiver registado, siga os passos no artigo [se tornar um publicador de Marketplace da Cloud](../../become-publisher.md).

>[!NOTE]
>Deve utilizar a mesma conta de registo do Microsoft Developer Center para iniciar sessão no Portal de parceiros de nuvem. Deve ter apenas uma conta Microsoft para as ofertas do Azure Marketplace. Esta conta não deve ser específica de ofertas ou serviços individuais.

* A sua empresa (ou respetiva subsidiária) tem de ser num destino de venda--país suportado pelo Azure Marketplace. Para obter uma lista atual nestes países, consulte [políticas de participação do Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Seu produto têm de estar licenciado de forma que seja compatível com modelos de faturação suportados pelo Azure Marketplace. Para obter mais informações, consulte [opções de faturação](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) no Azure Marketplace.
* É responsável por disponibilizar suporte técnico aos clientes de forma que sejam comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens de Comunidade.
* É responsável pelo licenciamento do software e as dependências de software de terceiros.
* Tem de fornecer conteúdo que cumpre os critérios para a sua oferta seja incluída no Azure Marketplace e no portal do Azure.
* Deve concordar com os termos do contrato de publicador e políticas de participação do Microsoft Azure Marketplace.
* Deve estar em conformidade com o Microsoft Azure site termos de utilização, declaração de privacidade da Microsoft e contrato de programa de certificação do Microsoft Azure.


## <a name="publishing-requirements"></a>Requisitos de publicação

Para publicar uma nova oferta de aplicação do Azure, tem de cumprir os seguintes pré-requisitos:

* Ter seus metadados pronto a utilizar. A lista seguinte (parcial) mostra um exemplo dos metadados:
  * Um título
  * Uma descrição (no formato HTML)
  * Uma imagem de logótipo (no formato PNG) e nestes corrigido tamanhos de imagem: 40 x 40 pixels, 90 x 90 pixels, 115 x 115 pixels e 255 x 115 pixels.
* R *termos de utilização* e uma *política de privacidade* documentos
* Documentação da aplicação
* Contactos de suporte


## <a name="next-steps"></a>Passos Seguintes

Após cumprir todos os requisitos, estará pronto para [criar uma oferta de aplicação do Azure](./cpp-create-offer.md). 
 
