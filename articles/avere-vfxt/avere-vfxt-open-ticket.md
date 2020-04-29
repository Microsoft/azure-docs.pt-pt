---
title: Como obter apoio para Avere vFXT para Azure
description: Explicação da abertura de bilhetes de apoio sobre Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79252561"
---
# <a name="get-help-with-your-system"></a>Obtenha ajuda com o seu sistema

Para ajuda com o seu Avere vFXT para o sistema Azure, aqui estão as formas de obter apoio:

* **Avere vFXT -** Use o portal Azure para abrir um bilhete de apoio para o seu Avere vFXT como descrito [abaixo](#open-a-support-ticket-for-your-avere-vfxt).
* **Quota** - Se tiver uma questão relacionada com quotas, [solicite um aumento](#request-a-quota-increase) de quota
* **Documentação e exemplos** - Se encontrar problemas com esta documentação ou exemplos, percorra para a parte inferior da página com o problema e utilize a secção **Feedback** para procurar os problemas existentes e arquivar um novo, se necessário.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Abra um bilhete de apoio para o seu Avere vFXT

Se encontrar problemas durante a implementação ou utilização do Avere vFXT, solicite ajuda através do portal Azure.

Siga estes passos para se certificar de que o seu bilhete de apoio está marcado com um recurso do seu cluster. A marcação do bilhete ajuda-nos a encaminhá-lo para o recurso de suporte correto.

1. A [https://portal.azure.com](https://portal.azure.com)partir de , selecione **Grupos de Recursos**. Navegue no grupo de recursos que contém o cluster vFXT onde ocorreu o problema, e clique numa das máquinas virtuais do cluster Avere.

    ![screenshot do painel de recursos do portal Azure "visão geral" com um VM particular circulado](media/avere-vfxt-ticket-vm.png)

1. Na página VM, desloque-se para a parte inferior do painel esquerdo e clique em **Novo pedido**de suporte .

    ![Screenshot da página VM do portal Azure para o VM a partir da imagem anterior. O menu esquerdo é percorrido para baixo e o "Novo pedido de apoio" está circulado.](media/avere-vfxt-ticket-request.png)

1. Na primeira página do pedido de suporte, escolha o tipo de emissão e certifique-se de que a subscrição correta é selecionada.

   No **Serviço,** clique em **Todos os Serviços** e procure em **Armazenamento** para escolher **Avere vFXT**.

   Adicione um resumo curto e selecione o tipo de problema.

    ![screenshot de um novo ecrã de pedido de suporte no portal Azure. O separador Basics é selecionado. Os itens de ecrã incluem tipo de emissão, subscrição, serviço, resumo e tipo de problema.](media/ticket-basics.png)

   Clique **em Seguir** para continuar.

1. A segunda página do formulário de suporte contém sugestões para corrigir o problema com base na sua descrição sumária. Clique no botão **Seguinte** na parte inferior se ainda precisar de criar um bilhete de apoio.

   ![screenshot do novo ecrã de pedido de suporte com o separador Soluções selecionado. Um campo de texto no meio tem o título de "Solução Recomendada" e explica possíveis remédios.](media/ticket-solutions.png)

1. Na terceira página, forneça detalhes - isto inclui informações sobre o seu cluster, o momento em que ocorreu o problema, a gravidade e como contactá-lo. Preencha a informação e clique no botão **Seguinte** na parte inferior.

   ![screenshot do novo ecrã de pedido de suporte com o separador Detalhes selecionado. Os campos de informação estão organizados nas categorias 'Dados problemáticos', 'Método de suporte' e "Informação de contacto".](media/ticket-details.png)

1. Reveja a informação na página final e clique em **Criar**. Uma confirmação e o número do bilhete serão enviados para o seu endereço de e-mail, e um membro do staff de apoio irá contactá-lo.

## <a name="request-a-quota-increase"></a>Solicitar um aumento de quota

Leia [quota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para saber que componentes são necessários para implantar o Avere vFXT para Azure. Pode [solicitar um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) de quota do portal Azure.
