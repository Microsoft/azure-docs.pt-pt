---
title: Criar oferta de Máquina Virtual no Mercado Azure
description: Lista os passos necessários para criar uma nova oferta de máquina virtual (VM) para o Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: bc1cf7a839307e65bd91eb29531663141e521472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278046"
---
# <a name="create-virtual-machine-offer"></a>Criar oferta de Máquina Virtual

Esta secção enumera os passos necessários para criar uma nova máquina virtual (VM) oferecer pedido para o Mercado Azure.  Cada oferta aparece como sua própria entidade no Azure Marketplace e está associada a uma ou mais SKUs.  Uma oferta VM é composta pelos seguintes agrupamentos de ativos e serviços de apoio: 

![Ativos para uma oferta VM](./media/publishvm_002.png)

em que:

|  **Grupo de ativos**   |  **Descrição**  |
|  ---------------   |  ---------------  |
|    SKU            |  A menor unidade de compra de uma oferta. Uma única oferta (classe de produto) pode ter múltiplas SKUs associadas a ela, para diferenciar entre funcionalidades suportadas, tipos de imagem VM e modelos de faturação. |
|  Marketplace       | Contém ativos e especificações de marketing, gestão legal e de chumbo.  <ul><li> Os ativos de marketing incluem nome, descrição e logotipos de oferta</li> <li> Os ativos legais incluem uma política de privacidade, termos de uso e outra documentação legal</li>  <li> A política de gestão de chumbo permite especificar como lidar com os leads do portal de utilizador final do Mercado Azure.</li> </ul> |
| Suporte            | Contém informações de contacto de suporte e políticas |
| Versão de Teste         | Define ativos que permitem aos utilizadores finais testar a sua oferta antes de a adquirirem |
|  |  |


## <a name="new-offer-form"></a>Formulário New Offer

Assim que iniciar sessão no Portal do [Parceiro cloud,](https://cloudpartner.azure.com/)clique no item **+ New Offer** na barra de menus esquerda. No menu resultante, clique em **Máquinas Virtuais** para exibir o formulário **New Offer** e inicie o processo de definição de ativos para uma nova oferta VM. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Nova máquina virtual oferece seleção de interface de utilizador](./media/publishvm_003.png)

> [!WARNING]
> Se a opção **Máquinas Virtuais** não for mostrada ou não estiver ativada, então a sua conta não tem permissão para criar este tipo de oferta.  Verifique se cumpriu todos os [pré-requisitos](./cpp-prerequisites.md) para este tipo de oferta, incluindo o registo de uma conta de desenvolvedor.


## <a name="next-steps"></a>Passos seguintes

Os tópicos subsequentes nesta secção espelham os separadores na página **New Offer** (para um tipo de oferta VM).  Cada artigo explica como usar o separador associado para definir os grupos de ativos e serviços de apoio para a sua nova oferta vm.

- [Separador Definições da oferta](./cpp-offer-settings-tab.md)
- [Separador de SKUs](./cpp-skus-tab.md)
- [Separador Versão de Teste](./cpp-test-drive-tab.md)
- [Separador Marketplace](./cpp-marketplace-tab.md)
- [Separador Suporte](./cpp-support-tab.md)
