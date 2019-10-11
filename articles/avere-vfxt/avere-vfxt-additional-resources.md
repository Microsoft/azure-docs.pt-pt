---
title: Links adicionais sobre o avere vFXT para Azure
description: Links para informações adicionais sobre o avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: rohogue
ms.openlocfilehash: c8ac08e3d03e8eb525cad7d73bece40c515e31a1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256289"
---
# <a name="additional-documentation"></a>Documentação adicional

Este artigo contém links para documentação adicional sobre a interface de gerenciamento do painel de controle do avere e tópicos relacionados. 

## <a name="avere-cluster-documentation"></a>Documentação do cluster avere

A documentação adicional do cluster avere pode ser encontrada no site em <https://azure.github.io/Avere/>. Esses documentos podem ajudá-lo a entender os recursos do cluster e como definir suas configurações. 

* O [Guia de criação de cluster FXT](<https://azure.github.io/Avere/#fxt_cluster>) foi projetado para clusters compostos por nós de hardware físico, mas algumas informações no documento também são relevantes para clusters vFXT. Em particular, os novos administradores de cluster vFXT podem se beneficiar com a leitura dessas seções:
  * A [personalização das configurações de suporte e monitoramento](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) explica como personalizar as configurações de upload de suporte e habilitar o monitoramento remoto. 
  * A [configuração de VServers e namespace global](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) tem informações sobre como criar um namespace voltado para o cliente.
  * [Configurar o DNS para o cluster avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) explica como configurar o DNS Round Robin.
  * [Adicionar armazenamento de back-end](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) documenta como adicionar os principais Filers.

* O [Guia de configuração de cluster](<https://azure.github.io/Avere/#operations>) é uma referência completa de configurações e opções para um cluster avere. Um cluster vFXT usa um subconjunto dessas opções, mas a maioria das mesmas páginas de configuração se aplica.

* O [Guia do painel](<https://azure.github.io/Avere/#operations>) explica como usar os recursos de monitoramento de cluster do painel de controle do avere.

## <a name="vfxt-creation-and-management-documentation"></a>documentação de criação e gerenciamento do vFXT

Um guia completo para usar o vfxt.py, o utilitário de criação e gerenciamento de cluster de nuvem, é fornecido no GitHub: [Gerenciamento de cluster de nuvem com vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
