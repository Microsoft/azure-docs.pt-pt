---
title: Criar uma oferta de máquina virtual no Azure Marketplace
description: Lista as etapas necessárias para criar uma nova oferta de VM (máquina virtual) para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 1e10bd22b91b51fb811601e49fad06d8f8b30127
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817103"
---
# <a name="create-virtual-machine-offer"></a>Criar oferta de máquina virtual

Esta seção lista as etapas necessárias para criar uma nova solicitação de oferta de máquina virtual (VM) para o Azure Marketplace.  Cada oferta aparece como sua própria entidade no Azure Marketplace e está associada a uma ou mais SKUs.  Uma oferta de VM é composta pelos seguintes agrupamentos de ativos e serviços de suporte: 

![Ativos para uma oferta de VM](./media/publishvm_002.png)

Posição

|  **Grupo de ativos**   |  **Descrição**  |
|  ---------------   |  ---------------  |
|    SKUs            |  A menor unidade compráveis de uma oferta. Uma única oferta (classe Product) pode ter várias SKUs associadas a ela, para diferenciar os recursos com suporte, os tipos de imagem de VM e os modelos de cobrança. |
|  Marketplace       | Contém ativos de marketing, gerenciamento jurídico e de Lead e especificações.  <ul><li> Os ativos de marketing incluem o nome, a descrição e os logotipos da oferta</li> <li> Os ativos legais incluem uma política de privacidade, os termos de uso e outras documentações legais</li>  <li> A política de gerenciamento de leads permite que você especifique como lidar com leads do portal do usuário final do Azure Marketplace.</li> </ul> |
| Suporte            | Contém informações de contato e política de suporte |
| Versão de Teste         | Define os ativos que permitem que os usuários finais testem sua oferta antes de comprá-la |
|  |  |


## <a name="new-offer-form"></a>Formulário de nova oferta

Depois de entrar no [portal do Cloud Partner](https://cloudpartner.azure.com/), clique no item **+ nova oferta** na barra de menus à esquerda. No menu resultante, clique em **máquinas virtuais** para exibir o novo formulário de **oferta** e iniciar o processo de definição de ativos para uma nova oferta de VM. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Nova seleção de interface de usuário da oferta de máquina virtual](./media/publishvm_003.png)

> [!WARNING]
> Se a opção de **máquinas virtuais** não for mostrada ou não estiver habilitada, sua conta não terá permissão para criar esse tipo de oferta.  Verifique se você atendeu a todos os [pré-requisitos](./cpp-prerequisites.md) para esse tipo de oferta, incluindo o registro de uma conta de desenvolvedor.


## <a name="next-steps"></a>Passos seguintes

Os tópicos subsequentes nesta seção espelham as guias na **nova** página de oferta (para um tipo de oferta de VM).  Cada artigo explica como usar a guia associada para definir os grupos de ativos e serviços de suporte para sua nova oferta de VM.

- [Separador Definições da oferta](./cpp-offer-settings-tab.md)
- [Separador de SKUs](./cpp-skus-tab.md)
- [Separador Versão de Teste](./cpp-test-drive-tab.md)
- [Separador Marketplace](./cpp-marketplace-tab.md)
- [Separador Suporte](./cpp-support-tab.md)
