---
title: Começar com o Windows Virtual Desktop Agent
description: Uma visão geral do Windows Virtual Desktop Agent e processos de atualização.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 6450b44e5c7281f0a24fd5000c9feec6e61fa29c
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540757"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Começar com o Windows Virtual Desktop Agent

No quadro do Windows Virtual Desktop Service, existem três componentes principais: o cliente de Ambiente de Trabalho Remoto, o serviço e as máquinas virtuais. Estas máquinas virtuais vivem na subscrição do cliente onde o agente virtual do Windows e o bootloader do agente estão instalados. O agente atua como o comunicador intermédio entre o serviço e as máquinas virtuais, permitindo a conectividade. Portanto, se estiver a ter algum problema com a instalação, atualização ou configuração do agente, as suas máquinas virtuais não poderão ligar-se ao serviço. O bootloader do agente é o executável que carrega o agente. 

Este artigo irá dar-lhe uma breve visão geral dos processos de instalação e atualização do agente.

>[!NOTE]
>Esta documentação não é para o agente FSLogix ou para o agente cliente de ambiente de trabalho remoto.


## <a name="initial-installation-process"></a>Processo inicial de instalação

O agente virtual do Windows está inicialmente instalado de duas formas. Se forre máquinas virtuais (VMs) no portal Azure e no Azure Marketplace, o bootloader do agente e do agente é instalado automaticamente. Se forre VMs utilizando o PowerShell, tem de descarregar manualmente o bootloader do agente e do agente .msi ficheiros ao [criar um pool de anfitriões virtual do Windows desktop com o PowerShell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). Quando o agente é instalado, a pilha virtual do Windows Desktop lado a lado e o agente de monitorização de Genebra também são instalados simultaneamente. O componente de pilha lado a lado é necessário para que os utilizadores estabeleçam ligações inversas de servidor a cliente. O agente de monitorização de Genebra monitoriza a saúde do agente. Todos estes três componentes são essenciais para que a conectividade do utilizador de ponta a ponta funcione corretamente.

>[!IMPORTANT]
>Para instalar com sucesso o agente virtual windows desktop, a pilha lado a lado e o agente de monitorização de Genebra, tem de desbloquear todos os URLs listados na [lista de URL requerida.](safe-url-list.md#virtual-machines) Desbloqueando estes URLs é necessário para utilizar o serviço de ambiente de trabalho virtual do Windows.

## <a name="agent-update-process"></a>Processo de atualização de agentes

O serviço de ambiente de trabalho virtual do Windows atualiza automaticamente o agente sempre que uma atualização estiver disponível. As atualizações do agente podem incluir novas funcionalidades ou corrigir problemas anteriores. Uma vez instalada a versão inicial do agente virtual do Windows Desktop, o agente consulta regularmente o serviço de desktop virtual do Windows para determinar se existe uma versão mais recente do agente e dos seus componentes disponíveis. Se houver uma nova versão, o bootloader do agente descarrega automaticamente a versão mais recente do agente, a pilha lado a lado e o agente de monitorização de Genebra.

>[!NOTE]
>- Quando o agente de monitorização de Genebra atualiza para a versão mais recente, a antiga tarefa genevaTask é localizada e desativada antes de criar uma nova tarefa para o novo agente de monitorização. A versão anterior do agente de monitorização não é eliminada caso a versão mais recente do agente de monitorização tenha um problema que requer reverter para a versão anterior para corrigir. Se a versão mais recente tiver um problema, o antigo agente de monitorização será re-habilitado para continuar a fornecer dados de monitorização. Todas as versões do monitor que sejam mais cedo do que a última que instalou antes da atualização serão eliminadas do seu VM.
>- O seu VM mantém três versões da pilha lado a lado de cada vez. Isto permite uma rápida recuperação se algo correr mal com a atualização. A versão mais antiga da pilha é removida do VM sempre que a stack atualiza.

Esta instalação de atualização dura normalmente 2-3 minutos num novo VM e não deve fazer com que o seu VM perca a ligação ou desligue. Este processo de atualização aplica-se tanto ao Windows Virtual Desktop (clássico) como à versão mais recente do Windows Virtual Desktop com o Azure Resource Manager.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma melhor compreensão do agente virtual do Windows Desktop, aqui estão alguns recursos que podem ajudá-lo:

- Consulte a secção [de atualizações](whats-new.md) do Windows Virtual Desktop Agent para ver informações sobre o que a nova atualização do agente implica todos os meses.
- Se estiver a ter problemas relacionados com o agente ou com a conectividade, consulte o [guia de resolução de problemas](troubleshoot-agent.md)do Windows Virtual Desktop Agent .