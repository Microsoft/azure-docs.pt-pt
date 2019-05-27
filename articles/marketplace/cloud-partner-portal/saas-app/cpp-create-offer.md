---
title: Criar oferta de aplicação SaaS do Azure | O Azure Marketplace
description: Como criar uma oferta de aplicação SaaS no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 646e9bf844477b3d8e1c4c42fb5956e015805433
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833506"
---
# <a name="create-a-new-saas-application-offer"></a>Criar uma nova oferta de aplicação SaaS

Este artigo descreve como criar e publicar uma entrada de oferta SaaS application (aplicativo) para o Azure Marketplace.

> [!IMPORTANT] 
> SaaS oferecem a funcionalidade está a ser migrada para o [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Todos os novos editores tem de utilizar Centro de parceiros para novas ofertas de SaaS de criação e gestão de ofertas existentes.  Os publicadores atuais com ofertas de SaaS estão a ser batchwise migrados do Portal de parceiros da Cloud para o Centro de parceiros.  O Portal de parceiros da Cloud irá apresentar mensagens de estado para indicar quando ofertas existentes específicas foram migradas.


## <a name="offer-process"></a>Processo de oferta

O diagrama seguinte mostra o processo para criar uma oferta de aplicação SaaS.

![Processo de criação de uma oferta de SaaS](./media/saas-offer-process-overview.png)

## <a name="offer-components"></a>Componentes de oferta

A oferta da aplicação SaaS consiste em cinco seções, descritas na tabela a seguir:

|  **Grupo de recursos**   |  **Descrição**  |
|  ---------------   |  ---------------  |
|    Definições da oferta  |  Utilize para configurar uma identidade exclusiva para a aplicação SaaS.                 |
|  Informações técnicas    |  Utilizar para configurar o tipo de solução de SaaS e forneça os detalhes de ligação para a sua aplicação.                |
|  Informações de canal      |   Fornece informações de canal como materiais GTM e contactos.                |
|  Test Drive        |   Seção opcional para a definição de um serviço que irá permitir aos clientes testar sua oferta antes de eles adquirem-la.                |
|  Vitrine detalhes       | Contém o marketing, legais e provocar ativos de gestão e as especificações.  <ul><li> Recursos de marketing incluem o nome da oferta, descrição e logotipos</li> <li> Recursos legais incluem uma política de privacidade, termos de utilização e outra documentação legal</li>  <li> Política de gestão levar permite que especifique como lidar com leva a partir do portal de utilizador final do Azure Marketplace.</li> </ul> |
| Contactos            | Contém informações de contacto e política de suporte |

## <a name="new-offer-form"></a>Novo formulário de oferta

Inicie sessão para o [Cloud Partner Portal](https://cloudpartner.azure.com/)e, em seguida, selecione **+ nova oferta** na barra de menu à esquerda. No novo oferecem menu, selecione **aplicações SaaS** para exibir o formulário de nova oferta e iniciar o processo de definição de ativos para uma nova oferta de aplicação SaaS.

![Menu novo de oferta para aplicações SaaS](./media/azure-new-saas-offer.png)

## <a name="next-steps"></a>Passos Seguintes

A página nova oferta para o tipo de oferta SaaS fornece um conjunto de guias e os campos de formulário que irá utilizar para criar uma nova oferta. Cada um dos seguintes artigos explica como utilizar o separador para definir os grupos de recursos e serviços de suporte para a sua oferta de novo.

- [Separador Definições da oferta](./cpp-offer-settings-tab.md)
- [Separador Informações Técnicas](./cpp-technical-info-tab.md)
- [Separador Informações de Canal](./cpp-channel-info-tab.md)
- [Separador Versão de Teste](./cpp-testdrive-tab.md)
- [Separador Detalhes da Loja](./cpp-storefront-tab.md)
- [Separador Contactos](./cpp-contacts-tab.md)
