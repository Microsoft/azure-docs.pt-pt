---
title: Habilitar o suporte para avere vFXT-Azure
description: Como habilitar carregamentos de suporte do avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415373"
---
# <a name="enable-support-uploads"></a>Ativar carregamentos de suporte

O avere vFXT para Azure pode carregar automaticamente os dados de suporte sobre o cluster. Esses uploads permitem que a equipe de suporte forneça o melhor serviço possível ao cliente.

## <a name="steps-to-enable-uploads"></a>Etapas para habilitar carregamentos

Siga estas etapas no painel de controle do avere para ativar o suporte. (Leia [acessar o cluster vFXT](avere-vfxt-cluster-gui.md) para saber como abrir o painel de controle.)

1. Navegue até a guia **configurações** na parte superior.
1. Clique no link de **suporte** à esquerda e aceite a política de privacidade.

   ![Captura de tela mostrando o painel de controle do avere e a janela pop-up com o botão confirmar para aceitar a política de privacidade](media/avere-vfxt-privacy-policy.png)

1. Na página configuração de suporte, abra a seção **informações do cliente** clicando no triângulo à esquerda.
1. Clique no botão **revalidar informações de carregamento** .
1. Defina o nome de suporte do cluster em **nome de cluster exclusivo**. Verifique se esse nome identifica exclusivamente o cluster para dar suporte à equipe.
1. Marque as caixas de **monitoramento de estatísticas**, **carregamento de informações gerais**e **carregamento de informações de falha**.
1. Clique em **Submit** (Submeter).

   ![Captura de tela contendo a seção de informações do cliente concluída da página de configurações de suporte](media/avere-vfxt-support-info.png)

1. Clique no triângulo à esquerda da **SPS (suporte proativo seguro)** para expandir a seção.
1. Marque a caixa **habilitar o link do SPS**.
1. Clique em **Submit** (Submeter).

   ![Captura de tela que contém a seção de suporte proativo seguro completa na página Configurações de suporte](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Passos seguintes

Se você precisar adicionar um sistema de armazenamento em nuvem local ou existente ao cluster, siga as instruções em [Configurar armazenamento](avere-vfxt-add-storage.md).

Se você estiver pronto para começar a anexar clientes ao cluster, leia [montar o cluster avere vFXT](avere-vfxt-mount-clients.md).
