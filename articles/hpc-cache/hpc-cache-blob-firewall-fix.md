---
title: Trabalhe em torno das definições de firewall de armazenamento
description: Uma definição de firewall de rede de armazenamento pode causar falhas ao criar um alvo de armazenamento Azure Blob em Cache Azure HPC. Este artigo dá uma solução alternativa para a limitação até que uma correção de software esteja em vigor.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: v-erkel
ms.openlocfilehash: 45a7169330b11e98a8618b08205217212414ca5d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258933"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Contornar as definições de firewall da conta de armazenamento de blobs

Uma definição específica utilizada nas firewalls de conta de armazenamento pode fazer com que a criação do alvo de armazenamento Blob falhe. A equipa da Azure HPC Cache está a trabalhar numa correção de software para este problema, mas pode trabalhar em torno dele seguindo as instruções deste artigo.

A definição de firewall que permite o acesso apenas a partir de "redes selecionadas" pode impedir que a cache crie ou modifique um alvo de armazenamento Blob. Esta configuração está na página de **firewalls e configurações de redes virtuais** da conta de armazenamento. (Esta questão não se aplica aos objetivos de armazenamento ADLS-NFS.)

O problema é que o serviço cache usa uma rede virtual de serviço oculto que é separada dos ambientes do cliente. Não é possível autorizar explicitamente esta rede a aceder à sua conta de armazenamento.

Quando cria um alvo de armazenamento Blob, o serviço cache utiliza esta rede para verificar se o recipiente está ou não vazio. Se a firewall não permitir o acesso a partir da rede oculta, a verificação falha e a criação do alvo de armazenamento falha.

A firewall também pode bloquear alterações nos caminhos do espaço-alvo de armazenamento Blob.

Para contornar o problema, altere temporariamente as definições de firewall enquanto cria o alvo de armazenamento:

1. Aceda à conta de armazenamento **Firewalls e página de redes virtuais** e altere a definição "Permitir o acesso a todas as **redes**"
1. Crie o alvo de armazenamento Blob na sua Cache Azure HPC.
1. Crie o caminho do espaço de nome do alvo de armazenamento.
1. Depois de o alvo e o caminho de armazenamento terem sido criados com sucesso, altere a definição de firewall da conta para **redes selecionadas**.

A Azure HPC Cache não utiliza a rede virtual de serviço para aceder ao alvo de armazenamento acabado.

Para obter ajuda nesta solução, [contacte o Microsoft Service and Support](hpc-cache-support-ticket.md).
