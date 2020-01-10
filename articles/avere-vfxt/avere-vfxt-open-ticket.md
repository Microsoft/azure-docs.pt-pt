---
title: Como obter suporte para avere vFXT para Azure
description: Explicação da abertura de tíquetes de suporte sobre o avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 8a371f902703287ed3105ed53a57d6341b9528d4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415112"
---
# <a name="get-help-with-your-system"></a>Obtenha ajuda com seu sistema

Se você precisar de ajuda com seu avere vFXT para o Azure, aqui estão as várias maneiras de obter suporte:

* **Avere vFXT Issue** – Use o portal do Azure para abrir um tíquete de suporte para seu avere vFXT, conforme descrito [abaixo](#open-a-support-ticket-for-your-avere-vfxt).
* **Cota** -se você tiver um problema relacionado à cota, [solicite um aumento de cota](#request-a-quota-increase)
* **Documentação e exemplos** -se você encontrar problemas com esta documentação ou exemplos, role até a parte inferior da página com o problema e use a seção de **comentários** para procurar problemas existentes e arquivar um novo, se necessário.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Abrir um tíquete de suporte para seu avere vFXT

Se você encontrar problemas ao implantar ou usar o avere vFXT, solicite ajuda por meio do portal do Azure.

Siga estas etapas para verificar se o tíquete de suporte está marcado com um recurso do cluster. Marcar o tíquete nos ajuda a circulá-lo para o recurso de suporte correto.

1. Em [https://portal.azure.com](https://portal.azure.com), selecione **grupos de recursos**.

   ![captura de tela de portal do Azure menu à esquerda com "grupos de recursos" circulados](media/avere-vfxt-ticket-rg.png)

1. Navegue até o grupo de recursos que contém o cluster vFXT em que o problema ocorreu e clique em uma das máquinas virtuais avere.

    ![captura de tela de portal do Azure painel "visão geral" do grupo de recursos com uma VM específica em um círculo](media/avere-vfxt-ticket-vm.png)

1. Na página VM, role para baixo até a parte inferior do painel esquerdo e clique em **nova solicitação de suporte**.

    ![Captura de tela da página de VM portal do Azure para a VM da captura de tela anterior. O menu à esquerda é rolado para a parte inferior e a "nova solicitação de suporte" é circulada.](media/avere-vfxt-ticket-request.png)

1. Na página um da solicitação de suporte, clique em **todos os serviços** e procure em **armazenamento** para escolher **avere vFXT**.

    ![captura da tela da nova solicitação de suporte na portal do Azure com o cabeçalho "Noções básicas" e um círculo em volta do item "serviço". O botão "todos os serviços" é selecionado e o campo de menu suspenso tem o valor "avere vFXT"](media/avere-vfxt-ticket-service.png)

1. Na página dois, escolha o tipo e a categoria do problema que mais se correspondem ao seu problema. Adicione um título curto e uma descrição que inclua a hora em que o problema ocorreu.

   ![instantâneo da tela nova solicitação de suporte com o cabeçalho "problema", que contém muitos campos que precisam ser concluídos](media/avere-vfxt-ticket-problem.png)

1. Na página três, preencha suas informações de contato e clique em **criar**. Um número de confirmação e de tíquete será enviado para seu endereço de email e um membro da equipe de suporte entrará em contato com você.

## <a name="request-a-quota-increase"></a>Solicitar um aumento de cota

Leia [cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para saber quais componentes são necessários para implantar o avere VFXT para Azure. Você pode [solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) do portal do Azure.
