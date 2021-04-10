---
title: Guia de publicação por tipo de oferta - Mercado comercial da Microsoft
description: Este artigo descreve os tipos de oferta que estão disponíveis no mercado comercial da Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 04/06/2021
ms.openlocfilehash: 381ee45bd678ef22222483675ddf001e16970628
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552058"
---
# <a name="publishing-guide-by-offer-type"></a>Guia de publicação por tipo de oferta

Este artigo descreve os tipos de oferta que estão disponíveis no mercado comercial. O *tipo de oferta* define a estrutura da oferta, que inclui os metadados, artefactos e outros conteúdos apresentados no mercado comercial.

Depois de [decidir uma opção de publicação,](determine-your-listing-type.md)tem de escolher um tipo de oferta antes de começar a criar a sua oferta no Partner Center. O tipo de oferta corresponderá ao tipo de solução, app ou oferta de serviço que deseja publicar, bem como ao seu alinhamento com os produtos e serviços da Microsoft.

> [!NOTE]
> Depois de selecionar um tipo de oferta, não pode alterar a oferta para outro tipo. Para criar um tipo de oferta diferente, você precisa criar uma nova oferta.

Pode configurar um único tipo de oferta de diferentes formas para permitir diferentes opções de publicação, opção de listagem, provisionamento ou preços. A opção de publicação e configuração do tipo de oferta também alinham-se com a elegibilidade da oferta e os requisitos técnicos.

Certifique-se de rever a loja online e oferecer requisitos de elegibilidade do tipo e os requisitos técnicos de publicação antes de criar a sua oferta.

## <a name="list-of-offer-types"></a>Lista de tipos de ofertas

A tabela seguinte mostra os tipos de oferta de mercado comercial no Partner Center.

| **Tipo de oferta**    | **Descrição**  |
| :------------------- | :-------------------|
| [**Aplicação do Azure**](plan-azure-application-offer.md) | Existem dois tipos de planos de aplicação Azure: _modelo de solução_ e _aplicação gerida._ Ambos os tipos de planos suportam automatizar a implementação e configuração de uma solução para além de uma única máquina virtual (VM). Pode automatizar o processo de disponibilização de múltiplos recursos, incluindo VMs, networking e recursos de armazenamento para fornecer soluções complexas, como soluções IaAS. Ambos os tipos de planos podem empregar muitos tipos diferentes de recursos Azure, incluindo, mas não se limitando a VMs.<ul><li>**Os** planos de modelo de solução são uma das principais formas de publicar uma solução no mercado comercial. Os planos de modelo de solução não são transacionáveis no mercado comercial, mas podem ser usados para implementar ofertas de VM pagas que são faturadas através do mercado comercial. Utilize o tipo de plano de modelo de solução quando o cliente irá gerir a solução e as transações são faturadas através de outro plano.</li><br><li>Os planos **de aplicação geridos** permitem-lhe construir e entregar aplicações chave na mão e geridas para os seus clientes. Têm as mesmas capacidades que os planos de modelo de solução, com algumas diferenças fundamentais:</li><ul><li> Os recursos são implantados para um grupo de recursos e são geridos pelo editor da app. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos.</li><li>Como editor, especifica o custo para o apoio contínuo da solução e as transações são suportadas através do mercado comercial.</li></ul>Utilize o tipo de plano de aplicação gerido quando você ou o seu cliente requer que a solução seja gerida por um parceiro ou que implemente uma solução baseada em subscrição.</ul> |
| [**Recipiente Azure**](marketplace-containers.md) | Utilize o tipo de oferta de recipiente Azure quando a sua solução for uma imagem de contentor Docker antejada como um serviço de contentores Azure baseado em Kubernetes. |
| [**Máquina virtual Azure**](marketplace-virtual-machines.md) | Utilize o tipo de oferta de máquina virtual quando colocar um aparelho virtual na subscrição associada ao seu cliente. |
| [**Serviço de consultoria**](./plan-consulting-service-offer.md) | Os serviços de consultoria ajudam a conectar os clientes com serviços de apoio e ampliação da sua utilização dos serviços Azure, Dynamics 365 ou Power Suite.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Pode publicar ofertas appSource que se baseiam ou prolongam as aplicações Dynamics 365 Business Central, Dynamics 365 Customer Engagement, Power Apps e Finance and Operations.|
| [**Módulo IoT Edge**](iot-edge-module.md) | Os módulos Azure IoT Edge são as unidades de computação mais pequenas geridas pelo IoT Edge, e podem conter serviços microsoft (como a Azure Stream Analytics), serviços de terceiros ou o seu próprio código específico para solução. |
| [**Serviço gerido**](./plan-managed-service-offer.md) | Pode criar ofertas de serviços geridos e gerir subscrições ou grupos de recursos delegados ao cliente através [do Farol de Azure.](../lighthouse/overview.md)|
| [**Aplicativo Power BI** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Pode publicar ofertas appSource que se baseiam ou prolongam o Power BI e o Microsoft 365.|
| [**Software como serviço**](plan-saas-offer.md) | Utilize o software como tipo de oferta de serviço (SaaS) para permitir ao seu cliente comprar a sua solução técnica baseada no SaaS como subscrição. Para obter informações sobre os requisitos de inscrição única para as ofertas da SaaS, consulte [a Azure AD e as ofertas transacionáveis saaS no mercado comercial.](azure-ad-saas.md) |


## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos de elegibilidade no artigo correspondente para o seu tipo de oferta para finalizar a seleção e configuração da sua oferta.
- Reveja os padrões de publicação de cada loja online, por exemplo, sobre como a sua solução mapeia para um tipo de oferta e configuração.