---
title: Criar oferta de Máquina Virtual no Azure Marketplace | Documentos da Microsoft
description: Listas os passos necessários para criar uma nova máquina virtual (VM) oferecem para o Azure Marketplace.
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
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: a3124ca2f163291d266fba77255d2ee98d30fd23
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888813"
---
# <a name="create-virtual-machine-offer"></a>Criar oferta de Máquina Virtual

Esta secção lista os passos necessários para criar um novo pedido de oferta de máquina virtual (VM) para o Azure Marketplace.  Cada oferta é apresentada como uma entidade própria no Azure Marketplace e está associada um ou mais SKUs.  Uma oferta VM é composta pelos seguintes agrupamentos de recursos e serviços de suporte: 

![Recursos para uma oferta VM](./media/publishvm_002.png)

Em que:

|  **Grupo de recursos**   |  **Descrição**  |
|  ---------------   |  ---------------  |
|    SKUs            |  A menor unidade que podem ser comprada de uma oferta. Uma única oferta (a classe product) pode ter várias SKUs associados à mesma, para diferenciar entre funcionalidades suportadas, tipos de imagem VM e modelos de faturação. |
|  Marketplace       | Contém o marketing, legais e provocar ativos de gestão e as especificações.  <ul><li> Recursos de marketing incluem o nome da oferta, descrição e logotipos</li> <li> Recursos legais incluem uma política de privacidade, termos de utilização e outra documentação legal</li>  <li> Política de gestão levar permite que especifique como lidar com leva a partir do portal do utilizador final do Azure Marketplace.</li> </ul> |
| Suporte            | Contém informações de contacto e política de suporte |
| Versão de Teste         | Define os ativos que permitem que os utilizadores finais teste a sua oferta antes de eles adquirem-la |
|  |  |


## <a name="new-offer-form"></a>Novo formulário de oferta

Uma vez seu início de sessão para o [Cloud Partner Portal](https://cloudpartner.azure.com/), clique nas **+ nova oferta** item na barra de menu à esquerda. No menu de resultante, clique em **máquinas virtuais** para apresentar o **nova oferta** formam e iniciar o processo de definição de ativos para uma nova oferta VM. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Nova seleção de interface do usuário de oferta de máquina virtual](./media/publishvm_003.png)

> [!WARNING]
> Se o **máquinas virtuais** opção não está apresentada ou não está ativada, então a sua conta não tem permissão para criar este tipo de oferta.  Certifique-se de que cumpriu todos os [pré-requisitos](./cpp-prerequisites.md) para este tipo de oferta, incluindo se registrar para uma conta de programador.


## <a name="next-steps"></a>Passos Seguintes

Os tópicos subsequentes nesta secção espelham os separadores na **nova oferta** página (para um tipo de oferta VM).  Cada artigo explica como utilizar o separador associado para definir os grupos de recursos e serviços de suporte para a nova oferta VM.

- [Separador Definições da oferta](./cpp-offer-settings-tab.md)
- [Separador de SKUs](./cpp-skus-tab.md)
- [Separador Versão de Teste](./cpp-test-drive-tab.md)
- [Separador Marketplace](./cpp-marketplace-tab.md)
- [Separador Suporte](./cpp-support-tab.md)
