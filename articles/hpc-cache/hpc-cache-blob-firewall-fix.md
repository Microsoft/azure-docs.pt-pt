---
title: Trabalhar em torno das definições de firewall de armazenamento
description: Uma definição de firewall de rede de conta de armazenamento pode causar falha ao criar um alvo de armazenamento Azure Blob em Cache Azure HPC. Este artigo dá uma solução para a limitação até que uma correção de software esteja em vigor.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74174411"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Contornar as definições de firewall da conta de armazenamento de blobs

Uma definição específica usada nas firewalls da conta de armazenamento pode fazer com que a criação do alvo de armazenamento Blob falhe. A equipa azure HPC Cache está a trabalhar numa correção de software para este problema, mas pode contornar o mesmo seguindo as instruções deste artigo.

A definição de firewall que permite o acesso apenas a partir de "redes selecionadas" pode impedir que a cache crie um alvo de armazenamento Blob. Esta configuração está na página de definições de **Firewalls e redes virtuais** da conta de armazenamento.

O problema é que o serviço de cache utiliza uma rede virtual de serviço oculto que é separada dos ambientes dos clientes. Não é possível autorizar explicitamente esta rede a aceder à sua conta de armazenamento.

Quando cria um alvo de armazenamento Blob, o serviço de cache utiliza esta rede para verificar se o recipiente está ou não vazio. Se a firewall não permitir o acesso da rede oculta, o controlo falha e a criação do alvo de armazenamento falha.

Para contornar o problema, altere temporariamente as definições da firewall enquanto cria o alvo de armazenamento:

1. Vá à página da conta de armazenamento **Firewalls e redes virtuais** e altere a definição "Permitir acesso a partir" para **todas as redes**.
1. Crie o alvo de armazenamento Blob no seu Cache Azure HPC.
1. Depois de o alvo de armazenamento ter sido criado com sucesso, altere a definição de firewall da conta para **redes Selecionadas**.

A Azure HPC Cache não utiliza a rede virtual de serviço para aceder ao alvo de armazenamento acabado.

Para obter ajuda nesta seleção, [contacte](hpc-cache-support-ticket.md)o Microsoft Service and Support .
