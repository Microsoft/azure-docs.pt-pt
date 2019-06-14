---
title: Como obter suporte para Avere vFXT para o Azure
description: Explicação de abrir pedidos de suporte sobre Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d621511cbb6983f8ad57ea8305823039475f40d0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409690"
---
# <a name="get-help-with-your-system"></a>Obtenha ajuda com o seu sistema

Se precisar de ajuda com sua vFXT Avere do Azure, aqui estão as várias formas de obter suporte:

* **Problema de vFXT Avere** -utilize o portal do Azure para abrir um pedido de suporte para sua vFXT Avere, conforme descrito [abaixo](#open-a-support-ticket-for-your-avere-vfxt).
* **Quota** – se tiver um problema relacionado com a quota, [pedir um aumento de quota](#request-a-quota-increase)
* **Documentação e exemplos** – se encontrar problemas com esta documentação ou exemplos, desloque-se para a parte inferior da página com o problema e a utilização a **comentários** secção para procurar problemas existentes e um novo um se de ficheiros necessário.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Abra um pedido de suporte para sua vFXT Avere

Se tiver problemas ao implementar ou usando Avere vFXT, pedir ajuda através do portal do Azure.  

Siga estes passos para se certificar de que o seu pedido de suporte é marcado com um recurso do seu cluster. O pedido de suporte de marcação ajuda-na encaminhar para o recurso de suporte corretas. 

1. Partir [ https://portal.azure.com ](https://portal.azure.com), selecione **grupos de recursos**.

   ![captura de ecrã do menu esquerdo do portal do Azure com "Grupos de recursos" circuladas](media/avere-vfxt-ticket-rg.png)

1. Navegue para o grupo de recursos que contém o cluster de vFXT onde ocorreu o problema e clique das máquinas virtuais Avere.

    ![captura de ecrã do painel de "Descrição geral do grupo" de recursos de portal do Azure com uma VM específica circulada](media/avere-vfxt-ticket-vm.png)

1. Na página de VM, desloque para baixo para a parte inferior do painel do lado esquerdo e clique em **novo pedido de suporte**.

    ![Captura de ecrã do portal do Azure VM página para a VM da captura de ecrã anterior. Menu da esquerda é rolado para a parte inferior e, em seguida, "Novo pedido de suporte" é com círculos.](media/avere-vfxt-ticket-request.png)

1. Na página de um pedido de suporte, clique em **todos os serviços** e procure **armazenamento** escolher **Avere vFXT**.

    ![captura de ecrã do ecrã de pedido de suporte de novo no portal do Azure com o cabeçalho "Noções básicas" e um círculo à volta do item de "Serviço". O botão "Todos os serviços" está selecionado e o campo de menu pendente tem o valor "Avere vFXT"](media/avere-vfxt-ticket-service.png)

1. Na página dois, escolha o tipo de problema e a categoria que melhor correspondem aos seu problema. Adicione um título curto e a descrição que inclui o tempo que o problema ocorreu. 

   ![captura de ecrã do novo ecrã de pedido de suporte com o cabeçalho "Problema", que contém muitos campos que devem ser concluídas](media/avere-vfxt-ticket-problem.png)

1. Na página três, preencha as informações de contacto e clique em **criar**. Um número de confirmação e pedido de suporte será enviado para o seu endereço de e-mail e será contactado por um membro da equipe de suporte.

## <a name="request-a-quota-increase"></a>Pedir um aumento de quota

Leia [Quota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para saber quais componentes são necessários para implantar o vFXT Avere para o Azure. Pode [pedir um aumento de quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) do portal do Azure.