---
title: Configurar as definições de cache do HPC Azure
description: Explica como configurar configurações adicionais para a cache como MTU e sem raiz-squash, e como aceder às imagens expressas dos alvos de armazenamento do Azure Blob.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538820"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Configure configurar configurações adicionais de cache Azure HPC

A página **de Configuração** do portal Azure tem opções para personalizar várias configurações. A maioria dos utilizadores não precisa de os alterar dos valores predefinidos.

Este artigo também descreve como usar a funcionalidade de instantâneo para alvos de armazenamento De Blob Azure. A função snapshot não tem definições configuráveis.

Para ver as definições, abra a página de **Configuração** da cache no portal Azure.

![screenshot da página de configuração no portal Azure](media/configuration.png)

## <a name="adjust-mtu-value"></a>Ajustar o valor MTU
<!-- linked from troubleshoot-nas article -->

Pode selecionar o tamanho máximo da unidade de transmissão para a cache utilizando o menu suspenso com o **tamanho MTU**.

O valor padrão é de 1500 bytes, mas pode mudá-lo para 1400.

> [!NOTE]
> Se baixar o tamanho do MTU da cache, certifique-se de que os clientes e sistemas de armazenamento que comunicam com a cache têm a mesma definição De MTU ou um valor mais baixo.

Baixar o valor do Cache MTU pode ajudá-lo a contornar as restrições de tamanho do pacote no resto da rede do cache. Por exemplo, algumas VPNs não podem transmitir pacotes de byte em tamanho real 1500 com sucesso. A redução do tamanho dos pacotes enviados sobre a VPN pode eliminar esse problema. No entanto, note que uma definição mTU de cache inferior significa que qualquer outro componente que comunique com a cache - incluindo clientes e sistemas de armazenamento - também deve ter uma definição mais baixa para evitar problemas de comunicação com a cache.

Se não quiser alterar as definições de MTU noutros componentes do sistema, não deve baixar a definição de MTU da cache. Existem outras soluções para contornar as restrições de tamanho de pacote VPN. Leia [Ajustar as restrições](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) de tamanho do pacote VPN no artigo de resolução de problemas da NAS para saber mais sobre o diagnóstico e a resolução deste problema.

Saiba mais sobre as definições de MTU nas redes virtuais Azure, lendo [a sintonização de desempenho TCP/IP para VMs Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Configurar abóbora-raiz
<!-- linked from troubleshoot -->

A definição de **abóbora de raiz Ativa** controla como o Cache Azure HPC permite o acesso à raiz. A abóbora-raiz ajuda a impedir o acesso ao nível da raiz de clientes não autorizados.

Esta definição permite que os utilizadores controlem o acesso à ``no_root_squash`` raiz ao nível da cache, o que pode ajudar a compensar a definição necessária para os sistemas NAS utilizados como alvos de armazenamento. (Ler mais sobre os [pré-requisitos](hpc-cache-prereqs.md#nfs-storage-requirements)do objetivo de armazenamento nFS .) Também pode melhorar a segurança quando usado com alvos de armazenamento Azure Blob.

A definição predefinida é **Sim**. (Caches criados antes de abril de 2020 podem ter a definição padrão **Nº**.) Quando ativado, esta funcionalidade também impede a utilização de bits de permissão set-UID nos pedidos do cliente para a cache.

## <a name="view-snapshots-for-blob-storage-targets"></a>Ver instantâneos para alvos de armazenamento de bolhas

A Azure HPC Cache guarda automaticamente instantâneos de armazenamento para alvos de armazenamento Da Blob Azure. As imagens fornecem um ponto de referência rápido para o conteúdo do recipiente de armazenamento traseiro. Os instantâneos não substituem as cópias de segurança dos dados, e não incluem qualquer informação sobre o estado dos dados em cache.

> [!NOTE]
> Esta funcionalidade de instantâneo é diferente da funcionalidade de instantâneo incluída no software de armazenamento NetApp, Isilon ou ZFS. Essas implementações instantâneas descarregam as alterações da cache para o sistema de armazenamento traseiro antes de tirar o instantâneo.
>
> Para a eficiência, o instantâneo azure HPC Cache não elimina as alterações em primeiro lugar, e apenas regista dados que foram escritos no recipiente Blob. Este instantâneo não representa o estado dos dados em cache, pelo que as alterações recentes podem ser excluídas.

Esta funcionalidade está disponível apenas para alvos de armazenamento Azure Blob, e a sua configuração não pode ser alterada.

As fotos são tiradas a cada oito horas, na UTC 0:00, 08:00 e 16:00.

Azure HPC Cache armazena instantâneos diários, semanais e mensais até serem substituídos por novos. Os limites são:

* até 20 instantâneos diários
* até 8 instantâneos semanais
* até 3 instantâneos mensais

Aceda às fotos `.snapshot` do diretório no espaço de nome do seu alvo de armazenamento blob.
