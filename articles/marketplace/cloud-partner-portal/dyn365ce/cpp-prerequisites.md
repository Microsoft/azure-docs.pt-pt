---
title: Dynamics 365 for Customer Engagement de oferta de pré-requisitos - Azure Marketplace | Documentos da Microsoft
description: Os pré-requisitos para a publicação de uma aplicação do Azure oferecem no Azure Marketplace.
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
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
ms.openlocfilehash: b08312040f9702b8a9100886c198138431012e3f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007376"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Dynamics 365 para pré-requisitos de envolvimento do cliente

Este artigo descreve os pré-requisitos técnicos e empresariais para a publicação de um do Dynamics 365 para a oferta de aplicação de envolvimento do cliente no Marketplace do AppSource.  Se ainda não o fez, reveja os [Office 365, Dynamics 365, PowerApps e o Power BI oferecem guia de publicação](../../appsource-offer-publishing-guide.md).


## <a name="technical-requirements"></a>Requisitos técnicos

O Dynamics 365 para a aplicação de envolvimento do cliente deve estar em conformidade com o [diretrizes de revisão de aplicação do Microsoft AppSource](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf), que inclui os seguintes requisitos:


|              Requisito             |        Descrição           |
|            ---------------           |      ---------------         |
| Integração do Azure Active Directory   | Tem de permitir a sua aplicação do Azure Active Directory, federada início de sessão único (SSO federado AAD) com consentimento ativado. Para obter mais informações, consulte [como obter o AppSource certificada para Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified). |
| Integração com os serviços Cloud da Microsoft (opcionais) | Em que esta funcionalidade é necessária, a aplicação deve integrar com outros serviços Cloud da Microsoft, como serviços do Microsoft Power BI, o Microsoft Flow ou o Microsoft Azure, tais como a aprendizagem automática ou os serviços cognitivos. |
| Concentra-se de linha de negócio            |  Seu aplicativo deve se concentrar num processo de negócio bem definidos ou emitir, principalmente direcionar os clientes empresariais e permitir que os utilizadores iniciem sessão com as respetivas credenciais de trabalho (nome de utilizador e palavra-passe).  |
| Período de avaliação gratuita e a experiência de avaliação |  Um cliente tem de ser capaz de utilizar a sua aplicação gratuitamente por um tempo limitado: o uma "obter agora" para aplicações gratuitas, uma "avaliação gratuita de" durante um período especificado, demonstrator uma "Unidade de teste", ou um "contactar-me" pedido de opção.  |
| Configuração de não/baixa                 | A aplicação tem de ser fácil e rápida configurar e configurar (sem desenvolvimento ou é necessária personalização).  |
| Suporte ao cliente                     | O suporte para a sua aplicação tem de incluir uma ligação de suporte em que os clientes podem encontrar ajuda.  |
| Disponibilidade/tempo de atividade                  | A aplicação tem de ter um tempo de atividade de, pelo menos, 99,9%. |
|  |  |


## <a name="business-requirements"></a>Requisitos comerciais

Os requisitos comerciais incluem as seguinte obrigações de procedimentos, contratuais e legais:

* Tem de estar registado no [Microsoft Partner Network (MPN)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx) ou ser um publicador de Marketplace na nuvem registados. Se não estiver registrado, siga os passos em [se tornar um publicador de Marketplace da Cloud](../../become-publisher.md).  (Para o terceiro passo, em vez disso, utilize o [formulário de nomeação do parceiro de AppSource](https://appsource.microsoft.com/partners/signup)). 

    >[!NOTE]
    >Deve utilizar a mesma conta de registo do Microsoft Developer Center para iniciar sessão no Portal de parceiros de nuvem. Deve ter apenas uma conta Microsoft para as ofertas do Azure Marketplace. Esta conta não deve ser específica de ofertas ou serviços individuais.

* Como o AppSource não oferece uma opção de publicação habilitados no comércio, tem de utilizar sua infraestrutura de faturação e de ordenação atual sem qualquer investimento adicional ou alterações.
* É responsável por disponibilizar suporte técnico aos clientes de forma que sejam comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens de Comunidade.
* É responsável pelo licenciamento do software e as dependências de software de terceiros.
* Deverá ter criado o colaterais de marketing associados, como um nome de aplicação oficial, descrição (no formato HTML), imagens de logótipo no formato PNG (40 x 40, 90 x 90, 115 x 115 e 255 x 115 pixels) e termos de utilização e uma política de privacidade.  


## <a name="next-steps"></a>Passos Seguintes

Depois de ter cumprido estes requisitos, poderá [criar uma oferta de Dynamics 365 Customer Engagement](./cpp-create-offer.md) 
