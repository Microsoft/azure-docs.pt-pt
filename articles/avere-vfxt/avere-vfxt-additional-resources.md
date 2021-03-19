---
title: Links adicionais sobre Avere vFXT para Azure
description: Utilize estes recursos para obter informações adicionais sobre Avere vFXT para a Azure, incluindo documentação do cluster Avere e documentação de gestão vFXT.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 6f9a9f8e6d2a279871ded3dff614be99dd2901df
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88271095"
---
# <a name="additional-documentation"></a>Documentação adicional

Este artigo tem ligações com documentação adicional sobre a interface de gestão do Painel de Controlo avere e tópicos relacionados.

## <a name="avere-cluster-documentation"></a>Documentação do cluster Avere

Documentação adicional do cluster Avere pode ser encontrada no site em <https://azure.github.io/Avere/> . Estes documentos podem ajudá-lo a compreender as capacidades do cluster e como configurar as suas definições.

* O [FxT Cluster Creation Guide](<https://azure.github.io/Avere/#fxt_cluster>) foi concebido para clusters compostos por nós de hardware físicos, mas algumas informações no documento também são relevantes para clusters vFXT. Em particular, os novos administradores de cluster vFXT podem beneficiar da leitura destas secções:
  * [A personalização das definições de suporte e monitorização](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) explica como personalizar as definições de upload de suporte e permitir a monitorização remota.
  * [Configurar vServers e Global Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) tem informações sobre a criação de um espaço de nome virado para o cliente.
  * [Configurar o DNS para o cluster Avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) explica como configurar o DNS de rodapé.
  * Adicionar documentos [de armazenamento de back-end](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) como adicionar filetes principais.

* O [Guia de Configuração do Cluster](<https://azure.github.io/Avere/#operations>) é uma referência completa de configurações e opções para um cluster Avere. Um cluster vFXT usa um subconjunto destas opções, mas a maioria das mesmas páginas de configuração aplicam-se.

* O [Guia do Painel](<https://azure.github.io/Avere/#operations>) explica como utilizar as características de monitorização do cluster do Painel de Controlo de Avere.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT criação e documentação de gestão

Um guia completo para a utilização de vfxt.py, um utilitário de criação e gestão de clusters de nuvem baseado em scripts, é fornecido no GitHub: [Cloud cluster management com vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
