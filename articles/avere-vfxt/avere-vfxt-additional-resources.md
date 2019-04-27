---
title: Links adicionais sobre Avere vFXT para o Azure
description: Ligações para informações adicionais sobre Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: v-erkell
ms.openlocfilehash: 2efbe7ddc39b8bde76ee4a135f3f44af0864a374
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515601"
---
# <a name="additional-documentation"></a>Documentação adicional

Este artigo tem ligações para documentação adicional sobre a interface de gestão do painel de controlo de Avere e tópicos relacionados. 

## <a name="avere-cluster-documentation"></a>Documentação do cluster Avere

Documentação do cluster de Avere adicional pode ser encontrada no site em <https://azure.github.io/Avere/>. Estes documentos podem ajudar a compreender os recursos do cluster e como configurar as respetivas definições. 

* O [guia de criação do Cluster FXT](<https://azure.github.io/Avere/#fxt_cluster>) foi concebido para clusters constituído por nós de hardware físico, mas algumas informações no documento são relevantes para os clusters de vFXT também. Em particular, os administradores de cluster do novo vFXT podem beneficiar de ler estas secções:
  * [Personalizar o suporte e as definições da monitorização](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) explica como personalizar as definições de carregamento de suporte e ativar a monitorização remota. 
  * [Configurar VServers e espaço de nomes Global](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) tem informações sobre como criar um espaço de nomes com clientes.
  * [Configurar o DNS para o cluster Avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) explica como configurar o DNS round robin.
  * [Adicionar armazenamento de back-end](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) documenta como adicionar se filtram de núcleo.

* O [guia de configuração de Cluster](<https://azure.github.io/Avere/#operations>) é uma referência completa de definições e as opções para um cluster de Avere. Um cluster de vFXT utiliza um subconjunto destas opções, mas se aplicam a maior parte as mesmas páginas de configuração.

* O [guia de Dashboard](<https://azure.github.io/Avere/#operations>) explica como utilizar o cluster de funcionalidades do painel de controle Avere de monitorização.

## <a name="vfxt-creation-and-management-documentation"></a>documentação de criação e gestão de vFXT

Um guia completo para utilizar vfxt.py, a criação de cluster de nuvem e o utilitário de gerenciamento, é fornecido no GitHub: [Cloud de gestão de clusters com vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
