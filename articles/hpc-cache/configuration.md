---
title: Configurar configurações de cache Azure HPC
description: Explica como configurar configurações adicionais para a cache como MTU e no-root-squash, e como aceder às imagens expressas dos alvos de armazenamento Azure Blob.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 05/06/2020
ms.author: v-erkel
ms.openlocfilehash: b01c4d896d5ec600e0fe22e3ca7b7816141776a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86497204"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Configurar configurações adicionais de cache Azure HPC

A página **de Configuração** no portal Azure tem opções para personalizar várias configurações. A maioria dos utilizadores não precisa de alterar estas definições a partir dos seus valores predefinidos.

Este artigo também descreve como usar a funcionalidade de instantâneo para alvos de armazenamento Azure Blob. A função snapshot não tem configurações configuráveis.

Para ver as definições, abra a página **de Configuração** do cache no portal Azure.

![screenshot da página de configuração no portal Azure](media/configuration.png)

> [!TIP]
> O [vídeo Managing Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) mostra a página de configuração e as suas definições.

## <a name="adjust-mtu-value"></a>Ajuste o valor mtu
<!-- linked from troubleshoot-nas article -->

Pode selecionar o tamanho máximo da unidade de transmissão para a cache utilizando o tamanho **mtu**do menu suspenso.

O valor predefinido é de 1500 bytes, mas pode alterá-lo para 1400.

> [!NOTE]
> Se baixar o tamanho MTU da cache, certifique-se de que os clientes e os sistemas de armazenamento que comunicam com a cache têm a mesma definição DE MTU ou um valor mais baixo.

A redução do valor de cache MTU pode ajudá-lo a contornar as restrições de tamanho do pacote no resto da rede do cache. Por exemplo, algumas VPNs não podem transmitir pacotes de 1500 bytes com sucesso. Reduzir o tamanho dos pacotes enviados pela VPN pode eliminar esse problema. No entanto, note que uma configuração MTU de cache inferior significa que qualquer outro componente que comunique com a cache - incluindo clientes e sistemas de armazenamento - também deve ter uma definição de MTU mais baixa para evitar problemas de comunicação.

Se não pretender alterar as definições de MTU noutros componentes do sistema, não deverá baixar a definição de MTU da cache. Existem outras soluções para trabalhar em torno das restrições de tamanho de pacote VPN. Leia Ajustar as restrições de [tamanho do pacote VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) no artigo de resolução de problemas da NAS para saber mais sobre o diagnóstico e a resolução deste problema.

Saiba mais sobre as definições de MTU em redes virtuais Azure lendo [afinação de desempenho TCP/IP para VMs Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Configure abóbora de raiz
<!-- linked from troubleshoot -->

A definição **de abóbora de raiz Enable** controla a forma como o Azure HPC Cache trata os pedidos do utilizador raiz nas máquinas do cliente.

Quando a abóbora raiz está ativada, os utilizadores de raiz de um cliente são automaticamente mapeados para o utilizador "ninguém" quando enviam pedidos através da Cache Azure HPC. Também impede que os pedidos do cliente utilizem bits de permissão set-UID.

Se a abóbora raiz for desativada, um pedido do utilizador raiz do cliente (UID 0) é transmitido para um sistema de armazenamento NFS de back-end como raiz. Esta configuração pode permitir o acesso inadequado ao ficheiro.

A definição de abóboras de raiz na cache pode ajudar a compensar a regulação necessária ``no_root_squash`` nos sistemas NAS que são utilizados como alvos de armazenamento. (Ler mais sobre [os pré-requisitos do alvo de armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements).) Também pode melhorar a segurança quando usado com alvos de armazenamento Azure Blob.

A definição **predefinida**é Sim . (Caches criados antes de abril de 2020 podem ter a definição padrão **Nº**.)

## <a name="view-snapshots-for-blob-storage-targets"></a>Ver instantâneos para alvos de armazenamento de bolhas

A Azure HPC Cache guarda automaticamente imagens de armazenamento para alvos de armazenamento Azure Blob. As imagens fornecem um ponto de referência rápido para o conteúdo do recipiente de armazenamento de fundo.

As imagens não substituem as cópias de segurança de dados e não incluem qualquer informação sobre o estado dos dados em cache.

> [!NOTE]
> Esta funcionalidade de instantâneo é diferente da funcionalidade snapshot incluída no netApp ou no software de armazenamento Isilon. Essas implementações instantâneas limpam as alterações da cache para o sistema de armazenamento de back-end antes de tirar a fotografia.
>
> Para eficiência, o instantâneo Azure HPC Cache não lava as alterações primeiro, e apenas regista dados que foram escritos no recipiente Blob. Este instantâneo não representa o estado dos dados em cache, pelo que pode não incluir alterações recentes.

Esta funcionalidade está disponível apenas para alvos de armazenamento Azure Blob, e a sua configuração não pode ser alterada.

As fotos são tiradas a cada oito horas, às 0:00, 08:00 e às 16:00.

Azure HPC Cache armazena fotos diárias, semanais e mensais até serem substituídas por novas. Os limites são:

* até 20 instantâneos diários
* até 8 instantâneos semanais
* até 3 instantâneos mensais

Aceda às fotos do `.snapshot` diretório no espaço de nome do seu alvo de armazenamento de bolhas.
