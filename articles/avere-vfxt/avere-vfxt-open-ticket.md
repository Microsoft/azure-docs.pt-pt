---
title: Como obter apoio para Avere vFXT para Azure
description: Saiba como resolver problemas que possam surgir durante a implementação ou utilização do Avere vFXT para Azure, criando um bilhete de apoio através do portal Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 8159d83624cdd474d91030d5376a3db447beffe1
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342353"
---
# <a name="get-help-with-your-system"></a>Obtenha ajuda com o seu sistema

Para ajuda com o seu sistema Avere vFXT para Azure, aqui estão as formas de obter suporte:

* **Avere vFXT emissão** - Utilize o portal Azure para abrir um bilhete de apoio para o seu Avere vFXT, conforme descrito [abaixo](#open-a-support-ticket-for-your-avere-vfxt).
* **Quota** - Se tiver uma questão relacionada com quotas, [solicite um aumento de quota](#request-a-quota-increase)
* **Documentação e exemplos** - Se encontrar problemas com esta documentação ou exemplos, percorra a parte inferior da página com o problema e utilize a secção **de Feedback** para procurar os problemas existentes e arquivar um novo, se necessário.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Abra um bilhete de apoio para o seu Avere vFXT

Se encontrar problemas durante a implementação ou utilização do Avere vFXT, solicite ajuda através do portal Azure.

Siga estes passos para se certificar de que o seu bilhete de apoio está marcado com um recurso do seu cluster. A marcação do bilhete ajuda-nos a encaminhá-lo para o recurso de suporte correto.

1. A partir de [https://portal.azure.com](https://portal.azure.com) , selecione **Grupos de Recursos**. Navegue pelo grupo de recursos que contém o cluster vFXT onde ocorreu o problema, e clique numa das máquinas virtuais do cluster Avere.

    ![screenshot do painel "overview" do grupo de recursos do portal Azure com um VM em círculo](media/avere-vfxt-ticket-vm.png)

1. Na página VM, desloque-se até ao fundo do painel esquerdo e clique em **Novo pedido de suporte**.

    ![Screenshot da página VM do portal Azure para o VM a partir da imagem anterior. O menu esquerdo é deslocalizado para o fundo e 'Novo pedido de apoio' é circulado.](media/avere-vfxt-ticket-request.png)

1. Na primeira página do pedido de suporte, escolha o tipo de problema e certifique-se de que a subscrição correta é selecionada.

   Em **Serviço,** clique em **Todos os Serviços** e procure no **Armazenamento** para escolher **Avere vFXT**.

   Adicione um resumo curto e selecione o tipo de problema.

    ![screenshot de um novo ecrã de pedido de suporte no portal Azure. O separador Básico é selecionado. Os itens de ecrã incluem tipo de emissão, subscrição, serviço, resumo e tipo de problema.](media/ticket-basics.png)

   Clique em **Next** (Seguinte) para continuar.

1. A segunda página do formulário de suporte contém sugestões para corrigir o problema com base na descrição do seu resumo. Clique no botão **Seguinte** na parte inferior se ainda precisar de criar um bilhete de apoio.

   ![screenshot do novo ecrã de pedido de suporte com o separador Soluções selecionado. Um campo de texto no meio tem o título de "Solução Recomendada" e explica possíveis remédios.](media/ticket-solutions.png)

1. Na terceira página, forneça detalhes - isto inclui informações sobre o seu cluster, o momento em que ocorreu o problema, a gravidade e como contactá-lo. Preencha a informação e clique no botão **Seguinte** na parte inferior.

   ![screenshot do novo ecrã de pedido de suporte com o separador Detalhes selecionado. Os campos de informação são organizados nas categorias "Detalhes do problema", "Método de suporte" e "Informação de contacto".](media/ticket-details.png)

1. Reveja as informações na página final e clique em **Criar**. Uma confirmação e número de bilhete será enviado para o seu endereço de e-mail, e um membro do pessoal de apoio entrará em contacto consigo.

## <a name="request-a-quota-increase"></a>Solicitar um aumento de quota

Leia [quota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para saber que componentes são necessários para implementar o Avere vFXT para o Azure. Pode [solicitar um aumento de quota](../azure-portal/supportability/resource-manager-core-quotas-request.md) a partir do portal Azure.