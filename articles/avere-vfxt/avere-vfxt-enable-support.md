---
title: Habilitar o suporte para avere vFXT-Azure
description: Como habilitar carregamentos de suporte do avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: ac7db46a681fcde6bfcbb7695e2d66724f738918
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256228"
---
# <a name="enable-support-uploads"></a>Ativar carregamentos de suporte

O avere vFXT para Azure pode carregar automaticamente os dados de suporte sobre o cluster. Esses uploads permitem que a equipe de suporte forneça o melhor serviço possível ao cliente.

## <a name="steps-to-enable-uploads"></a>Etapas para habilitar carregamentos

Siga estas etapas no painel de controle do avere para ativar o suporte. (Leia [acessar o cluster vFXT](avere-vfxt-cluster-gui.md) para saber como abrir o painel de controle do avere.)

1. Navegue até a guia **configurações** na parte superior.
1. Clique no link de **suporte** à esquerda e aceite a política de privacidade.

   ![Captura de tela mostrando o painel de controle do avere e a janela pop-up com o botão confirmar para aceitar a política de privacidade](media/avere-vfxt-privacy-policy.png)

1. Clique no triângulo à esquerda das **informações do cliente** para expandir a seção.
1. Clique no botão **revalidar informações de carregamento** .
1. Definir o nome de suporte do cluster em **nome de cluster exclusivo** -Verifique se ele identifica exclusivamente o cluster para dar suporte à equipe.
1. Marque as caixas de **monitoramento de estatísticas**, **carregamento de informações gerais**e **carregamento de informações de falha**.
1. Clique em **Enviar**.

   ![Captura de tela contendo a seção de informações do cliente concluída da página de configurações de suporte](media/avere-vfxt-support-info.png)

1. Clique no triângulo à esquerda da **SPS (suporte proativo seguro)** para expandir a seção.
1. Marque a caixa **habilitar o link do SPS**.
1. Clique em **Enviar**.

   ![Captura de tela que contém a seção de suporte proativo seguro completa na página Configurações de suporte](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Passos seguintes

Se você precisar adicionar um sistema de armazenamento em nuvem local ou existente ao cluster, siga as instruções em [Configurar armazenamento](avere-vfxt-add-storage.md). 

Se você estiver pronto para começar a anexar clientes ao cluster, leia [montar o cluster avere vFXT](avere-vfxt-mount-clients.md).