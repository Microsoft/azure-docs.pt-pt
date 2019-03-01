---
title: Ativar o suporte para Avere vFXT - Azure
description: Carrega de como ativar o suporte de Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: fe096b2e2a75cc89e3ce5ef905d8e4c347cc153a
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992469"
---
# <a name="enable-support-uploads"></a>Ativar carregamentos de suporte

O vFXT Avere para o Azure automaticamente pode carregar dados de suporte sobre o cluster. Esses carregamentos permitem à equipe de suporte fornecem o melhor serviço de cliente possíveis.

## <a name="steps-to-enable-uploads"></a>Passos para ativar o carregamentos

Siga estes passos do painel de controle Avere para ativar o suporte. (Leia [aceder ao cluster vFXT](avere-vfxt-cluster-gui.md) para aprender a abrir o painel de controlo de Avere.)

1. Navegue para o **definições** separador na parte superior.
1. Clique nas **suporte** associar à esquerda e aceite a política de privacidade.

   ![Captura de ecrã que mostra o painel de controlo de Avere e uma janela pop-up com o botão confirmar para aceitar a política de privacidade](media/avere-vfxt-privacy-policy.png)

1. Clique no triângulo à esquerda da vírgula **Customer Info** para expandir a secção.
1. Clique nas **Revalide informações de carregamento** botão.
1. Definir o nome do suporte do cluster no **nome de Cluster exclusivo** -Certifique-se de que identifica exclusivamente o seu cluster para o suporte técnico.
1. As caixas de verificação **estatísticas de monitorização**, **carregar de informações gerais**, e **carregar de informações de falhas**.
1. Clique em **Submit** (Submeter).

   ![Captura de ecrã que contém concluído a secção de informações de cliente da página de definições de suporte](media/avere-vfxt-support-info.png)

1. Clique no triângulo à esquerda da vírgula **Secure Proativa suporte (SPS)** para expandir a secção.
1. Marque a caixa **ativar a ligação do SPS**.
1. Clique em **Submit** (Submeter).

   ![Captura de ecrã que contém concluído a secção de proteger o suporte Proativo de mensagens em fila na página de definições de suporte](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Passos Seguintes

Se precisar de adicionar uma local ou existente de sistema de armazenamento para o cluster na cloud, siga as instruções em [configurar o armazenamento](avere-vfxt-add-storage.md). 

Se estiver pronto para começar a anexar os clientes para o cluster, leio [montar o cluster de vFXT Avere](avere-vfxt-mount-clients.md).