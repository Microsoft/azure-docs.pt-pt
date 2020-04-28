---
title: Ativar suporte para Avere vFXT - Azure
description: Como permitir uploads de suporte de Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75415373"
---
# <a name="enable-support-uploads"></a>Ativar carregamentos de suporte

O Avere vFXT para o Azure pode enviar automaticamente dados de suporte sobre o seu cluster. Estes uploads permitem que o pessoal de suporte forneça o melhor atendimento ao cliente possível.

## <a name="steps-to-enable-uploads"></a>Passos para ativar uploads

Siga estes passos do Painel de Controlo de Avere para ativar o suporte. (Leia [Aceda ao cluster vFXT](avere-vfxt-cluster-gui.md) para aprender a abrir o painel de controlo.)

1. Navegue para o separador **Definições** na parte superior.
1. Clique no link **Suporte** à esquerda e aceite a política de privacidade.

   ![Screenshot mostrando Painel de Controlo Avere e janela pop-up com botão Confirmar para aceitar a política de privacidade](media/avere-vfxt-privacy-policy.png)

1. Na página de configuração de suporte, abra a secção **Informação** do Cliente clicando no triângulo à esquerda.
1. Clique no botão de informação de **upload Revalida.**
1. Desloque o nome de suporte do cluster em nome único do **cluster**. Certifique-se de que este nome identifica exclusivamente o seu cluster para apoiar a equipa.
1. Verifique as caixas para **monitorização de estatísticas,** upload de **informações gerais**e upload de informações sobre **acidentes**.
1. Clique em **Submeter**.

   ![Screenshot contendo secção de informação completa do cliente da página de definições de suporte](media/avere-vfxt-support-info.png)

1. Clique no triângulo à esquerda do **Suporte Proactivo Seguro (SPS)** para expandir a secção.
1. Verifique a caixa para **ativar a ligação SPS**.
1. Clique em **Submeter**.

   ![Screenshot contendo secção de suporte proactivo seguro completado na página de definições de suporte](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Passos seguintes

Se precisar de adicionar um sistema de armazenamento de nuvens no local ou existente ao cluster, siga as instruções no [armazenamento de Configuração](avere-vfxt-add-storage.md).

Se estiver pronto para começar a ligar clientes ao cluster, leia [o cluster Avere vFXT](avere-vfxt-mount-clients.md).
