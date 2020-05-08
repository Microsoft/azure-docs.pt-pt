---
title: Configurar as definições de cache do HPC Azure
description: Explica como configurar configurações adicionais para a cache como MTU e sem raiz-squash, e como aceder às imagens expressas dos alvos de armazenamento do Azure Blob.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: v-erkel
ms.openlocfilehash: a3bab06166110a3627bb3a99d51ceb09b0c7ed80
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871409"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Configure configurar configurações adicionais de cache Azure HPC

A página **de Configuração** do portal Azure tem opções para personalizar várias configurações. A maioria dos utilizadores não precisa de alterar estas definições dos seus valores predefinidos.

Este artigo também descreve como usar a funcionalidade de instantâneo para alvos de armazenamento De Blob Azure. A função snapshot não tem definições configuráveis.

Para ver as definições, abra a página de **Configuração** da cache no portal Azure.

![screenshot da página de configuração no portal Azure](media/configuration.png)

## <a name="adjust-mtu-value"></a>Ajustar o valor MTU
<!-- linked from troubleshoot-nas article -->

Pode selecionar o tamanho máximo da unidade de transmissão para a cache utilizando o menu suspenso com o **tamanho MTU**.

O valor padrão é de 1500 bytes, mas pode mudá-lo para 1400.

> [!NOTE]
> Se baixar o tamanho do MTU da cache, certifique-se de que os clientes e sistemas de armazenamento que comunicam com a cache têm a mesma definição De MTU ou um valor mais baixo.

Baixar o valor do Cache MTU pode ajudá-lo a contornar as restrições de tamanho do pacote no resto da rede do cache. Por exemplo, algumas VPNs não podem transmitir pacotes de 1500 bytes em tamanho real com sucesso. A redução do tamanho dos pacotes enviados sobre a VPN pode eliminar esse problema. No entanto, note que uma definição mTU de cache mais baixa significa que qualquer outro componente que comunique com a cache - incluindo clientes e sistemas de armazenamento - também deve ter uma definição MTU mais baixa para evitar problemas de comunicação.

Se não quiser alterar as definições de MTU noutros componentes do sistema, não deve baixar a definição de MTU da cache. Existem outras soluções para contornar as restrições de tamanho de pacote VPN. Leia [Ajustar as restrições](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) de tamanho do pacote VPN no artigo de resolução de problemas da NAS para saber mais sobre o diagnóstico e a resolução deste problema.

Saiba mais sobre as definições de MTU nas redes virtuais Azure, lendo [a sintonização de desempenho TCP/IP para VMs Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Configurar abóbora-raiz
<!-- linked from troubleshoot -->

A definição de **abóbora de raiz Ativa** controla a forma como o Cache Azure HPC trata os pedidos do utilizador raiz nas máquinas do cliente.

Quando a base de raízes está ativada, os utilizadores de raiz de um cliente são automaticamente mapeados para o utilizador "ninguém" quando enviam pedidos através do Cache Azure HPC. Também impede os pedidos dos clientes de utilizarem brocas de permissão set-UID.

Se a abóbora de raiz for desativada, um pedido do utilizador raiz do cliente (UID 0) é transmitido através de um sistema de armazenamento NFS de back-end como raiz. Esta configuração pode permitir um acesso inadequado ao ficheiro.

A colocação de abóboras de raiz ``no_root_squash`` na cache pode ajudar a compensar a definição necessária nos sistemas NAS que são usados como alvos de armazenamento. (Ler mais sobre os [pré-requisitos](hpc-cache-prereqs.md#nfs-storage-requirements)do objetivo de armazenamento nFS .) Também pode melhorar a segurança quando usado com alvos de armazenamento Azure Blob.

A definição predefinida é **Sim**. (Caches criados antes de abril de 2020 podem ter a definição padrão **Nº**.)

## <a name="view-snapshots-for-blob-storage-targets"></a>Ver instantâneos para alvos de armazenamento de bolhas

A Azure HPC Cache guarda automaticamente instantâneos de armazenamento para alvos de armazenamento Da Blob Azure. As imagens fornecem um ponto de referência rápido para o conteúdo do recipiente de armazenamento traseiro.

Os instantâneos não substituem as cópias de segurança dos dados, e não incluem qualquer informação sobre o estado dos dados em cache.

> [!NOTE]
> Esta funcionalidade de instantâneo é diferente da funcionalidade de instantâneo incluída no software de armazenamento NetApp ou Isilon. Essas implementações instantâneas descarregam as alterações da cache para o sistema de armazenamento traseiro antes de tirar o instantâneo.
>
> Para a eficiência, o instantâneo azure HPC Cache não elimina as alterações em primeiro lugar, e apenas regista dados que foram escritos no recipiente Blob. Este instantâneo não representa o estado dos dados em cache, pelo que pode não incluir alterações recentes.

Esta funcionalidade está disponível apenas para alvos de armazenamento Azure Blob, e a sua configuração não pode ser alterada.

As fotos são tiradas a cada oito horas, na UTC 0:00, 08:00 e 16:00.

Azure HPC Cache armazena instantâneos diários, semanais e mensais até serem substituídos por novos. Os limites são:

* até 20 instantâneos diários
* até 8 instantâneos semanais
* até 3 instantâneos mensais

Aceda às fotos `.snapshot` do diretório no espaço de nome do seu alvo de armazenamento blob.
