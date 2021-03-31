---
title: Ativar o suporte para Avere vFXT - Azure
description: Saiba como ativar o carregamento automático de dados de suporte sobre o seu cluster a partir de Avere vFXT para a Azure para ajudar o Suporte a fornecer o serviço ao cliente.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 93b99aa624a21d9312297e4279b1dcf053c79ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272733"
---
# <a name="enable-support-uploads"></a>Ativar carregamentos de suporte

Avere vFXT para Azure pode carregar automaticamente dados de suporte sobre o seu cluster. Estes uploads permitem que o pessoal de suporte forneça o melhor serviço ao cliente possível.

## <a name="steps-to-enable-uploads"></a>Passos para permitir uploads

Siga estes passos do Painel de Controlo de Avere para ativar o suporte. (Ler [Aceda ao cluster vFXT](avere-vfxt-cluster-gui.md) para aprender a abrir o painel de controlo.)

1. Navegue para o **separador Definições** na parte superior.
1. Clique no link **De apoio** à esquerda e aceite a política de privacidade.

   ![Screenshot mostrando Avere Control Panel e janela pop-up com botão Confirmar para aceitar a política de privacidade](media/avere-vfxt-privacy-policy.png)

1. Na página de configuração de suporte, abra a secção **Informação** do Cliente clicando no triângulo à esquerda.
1. Clique no botão **de informação de upload de Revalidate.**
1. Desa parte para o nome de suporte do cluster no **Nome do Cluster Único.** Certifique-se de que este nome identifica exclusivamente o seu cluster para apoiar o pessoal.
1. Consulte as caixas para **monitorização de estatísticas,** **upload de informações gerais** e **upload de informações de colisão**.
1. Clique **em Submeter.**

   ![Screenshot contendo secção de informações completas do cliente da página de definições de suporte](media/avere-vfxt-support-info.png)

1. Clique no triângulo à esquerda do **Secure Proactive Support (SPS)** para expandir a secção.
1. Verifique a caixa para **ativar a ligação SPS**.
1. Clique **em Submeter.**

   ![Screenshot contendo a secção de suporte proactivo seguro concluída na página de definições de suporte](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Passos seguintes

Se necessitar de adicionar um sistema de armazenamento em nuvem no local ou existente ao cluster, siga as instruções no [armazenamento Configure](avere-vfxt-add-storage.md).

Se estiver pronto para começar a ligar os clientes ao cluster, leia [o conjunto Avere vFXT](avere-vfxt-mount-clients.md).
