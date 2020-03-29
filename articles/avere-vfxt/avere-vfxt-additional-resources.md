---
title: Links adicionais sobre Avere vFXT para Azure
description: Links para informações adicionais sobre Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78e0500057baf407f1d26afe29d9df1e525cbf3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153773"
---
# <a name="additional-documentation"></a>Documentação adicional

Este artigo tem ligações a documentação adicional sobre a interface de gestão do Painel de Controlo de Avere e tópicos relacionados.

## <a name="avere-cluster-documentation"></a>Documentação do cluster de Avere

Documentação adicional do cluster Avere <https://azure.github.io/Avere/>pode ser encontrada no site em . Estes documentos podem ajudá-lo a compreender as capacidades do cluster e como configurar as suas definições.

* O [FxT Cluster Creation Guide](<https://azure.github.io/Avere/#fxt_cluster>) foi concebido para clusters compostos por nós de hardware físico, mas algumas informações no documento também são relevantes para clusters vFXT. Em particular, os novos administradores de cluster vFXT podem beneficiar da leitura destas secções:
  * [Personalizar as definições](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) de suporte e monitorização explica como personalizar as definições de upload de suporte e ativar a monitorização remota.
  * [Configurar VServers e Global Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) tem informações sobre a criação de um espaço de nome virado para o cliente.
  * [Configurar dNS para o cluster Avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) explica como configurar DNS de robin redondo.
  * Adicionar documentos [de armazenamento de back-end](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) como adicionar ficheiros core.

* O Guia de [Configuração](<https://azure.github.io/Avere/#operations>) do Cluster é uma referência completa de configurações e opções para um cluster Avere. Um cluster vFXT usa um subconjunto destas opções, mas a maioria das mesmas páginas de configuração se aplicam.

* O [Guia do Painel](<https://azure.github.io/Avere/#operations>) explica como utilizar as funcionalidades de monitorização do cluster do Painel de Controlo Avere.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT criação e documentação de gestão

Um guia completo para a utilização de vfxt.py, um utilitário de criação e gestão de cluster de nuvem baseado em scripts, é fornecido no GitHub: Gestão de [clusters em nuvem com vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
