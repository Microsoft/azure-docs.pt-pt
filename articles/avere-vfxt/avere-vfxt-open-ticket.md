---
title: Como obter suporte para avere vFXT para Azure
description: Explicação da abertura de tíquetes de suporte sobre o avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153333"
---
# <a name="get-help-with-your-system"></a>Obtenha ajuda com seu sistema

Para obter ajuda com seu avere vFXT para o sistema do Azure, aqui estão as maneiras de obter suporte:

* **Avere vFXT Issue** – Use o portal do Azure para abrir um tíquete de suporte para seu avere vFXT, conforme descrito [abaixo](#open-a-support-ticket-for-your-avere-vfxt).
* **Cota** -se você tiver um problema relacionado à cota, [solicite um aumento de cota](#request-a-quota-increase)
* **Documentação e exemplos** -se você encontrar problemas com esta documentação ou exemplos, role até a parte inferior da página com o problema e use a seção de **comentários** para procurar problemas existentes e arquivar um novo, se necessário.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Abrir um tíquete de suporte para seu avere vFXT

Se você encontrar problemas ao implantar ou usar o avere vFXT, solicite ajuda por meio do portal do Azure.

Siga estas etapas para verificar se o tíquete de suporte está marcado com um recurso do cluster. Marcar o tíquete nos ajuda a circulá-lo para o recurso de suporte correto.

1. Em [https://portal.azure.com](https://portal.azure.com), selecione **grupos de recursos**. Navegue até o grupo de recursos que contém o cluster vFXT em que o problema ocorreu e clique em uma das máquinas virtuais de cluster avere.

    ![captura de tela de portal do Azure painel "visão geral" do grupo de recursos com uma VM específica em um círculo](media/avere-vfxt-ticket-vm.png)

1. Na página VM, role para baixo até a parte inferior do painel esquerdo e clique em **nova solicitação de suporte**.

    ![Captura de tela da página de VM portal do Azure para a VM da captura de tela anterior. O menu à esquerda é rolado para a parte inferior e ' nova solicitação de suporte ' é circulado.](media/avere-vfxt-ticket-request.png)

1. Na primeira página da solicitação de suporte, escolha o tipo de problema e verifique se a assinatura correta está selecionada.

   Em **serviço**, clique em **todos os serviços** e procure em **armazenamento** para escolher **avere vFXT**.

   Adicione um breve resumo e selecione o tipo de problema.

    ![captura de tela de uma nova exibição de solicitação de suporte no portal do Azure. A guia noções básicas está selecionada. Os itens da tela incluem tipo de problema, assinatura, serviço, resumo e tipo de problema.](media/ticket-basics.png)

   Clique em **Next** (Seguinte) para continuar.

1. A segunda página do formulário de suporte contém sugestões para corrigir o problema com base na descrição resumida. Clique no botão **Avançar** na parte inferior se você ainda precisar criar um tíquete de suporte.

   ![instantâneo da tela nova solicitação de suporte com a guia soluções selecionada. Um campo de texto no meio tem o título "solução recomendada" e explica as possíveis soluções.](media/ticket-solutions.png)

1. Na terceira página, forneça detalhes-isso inclui informações sobre o cluster, a hora em que o problema ocorreu, a gravidade e como contatá-lo. Preencha as informações e clique no botão **Avançar** na parte inferior.

   ![instantâneo da tela nova solicitação de suporte com a guia detalhes selecionada. Os campos de informações são organizados nas categorias ' detalhes do problema ', ' método de suporte ' e "informações de contato '.](media/ticket-details.png)

1. Examine as informações na página final e clique em **criar**. Um número de confirmação e de tíquete será enviado para seu endereço de email e um membro da equipe de suporte entrará em contato com você.

## <a name="request-a-quota-increase"></a>Solicitar um aumento de cota

Leia [cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para saber quais componentes são necessários para implantar o avere VFXT para Azure. Você pode [solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) do portal do Azure.
