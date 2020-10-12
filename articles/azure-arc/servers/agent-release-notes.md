---
title: Novidades com o agente de servidores Azure Arc
description: Este artigo tem notas de lançamento para o agente de servidores ativado pelo Azure Arc. Para muitas das questões resumidas, existem ligações a detalhes adicionais.
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: 1918d03b5bbfaaa64b7d74c18fad4eb9a86800a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908160"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Novidades com o agente de servidores Azure Arc

O agente Azure Arc ativado por máquina conectada recebe melhorias numa base contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros

## <a name="september-2020"></a>Setembro de 2020

Versão: 1.0 (Disponibilidade Geral)

### <a name="plan-for-change"></a>Plano de alteração

- O suporte para agentes de pré-visualização (todas as versões com mais de 1.0) será removido numa futura atualização de serviço.
- Suporte removido para o ponto final do recuo `.azure-automation.net` . Se tiver um representante, tem de permitir o ponto `*.his.arc.azure.com` final.
- Se o agente 'Máquina Conectada' for instalado numa máquina virtual hospedada em Azure, as extensões VM não podem ser instaladas ou modificadas a partir do recurso 'Servidores ativados pelo Arco'. Isto é para evitar que as operações de extensão conflituosas sejam executadas a partir do recurso **Microsoft.Compute** e **Microsoft.HybridCompute** da máquina virtual. Utilize o recurso **Microsoft.Compute** para a máquina para todas as operações de extensão.
- O nome do processo de Configuração de Hóspedes mudou, de *gcd* para *gcad* no Linux, e *gcservice* para *gcarcservice* no Windows.

### <a name="new-feature"></a>Nova funcionalidade

- Opção adicional `azcmagent logs` de recolher informações para suporte.
- Opção `azcmagent license` adicional para exibir EULA.
- Opção `azcmagent show --json` adicionada ao estado do agente de saída em formato facilmente parseable.
- A bandeira adicionada na `azcmagent show` saída para indicar se o servidor está numa máquina virtual hospedada em Azure.
- Opção `azcmagent disconnect --force-local-only` adicional para permitir o reset do estado do agente local quando o serviço Azure não pode ser alcançado.
- Opção adicional `azcmagent connect --cloud` de suporte a nuvens adicionais. Nesta versão, apenas a Azure é suportada pelo serviço no momento da libertação do agente.
- O agente foi localizado em línguas apoiadas pelo Azure.

### <a name="fixed"></a>Fixo

- Melhorias na verificação de conectividade.
- Problema corrigido com as definições do servidor proxy a perder-se ao atualizar o agente no Linux.
- Problemas resolvidos ao tentar instalar o agente no servidor que executa o Windows Server 2012 R2.
- Melhorias na fiabilidade da instalação de extensão

## <a name="august-2020"></a>Agosto de 2020

Versão: 0.11

- Esta versão previamente anunciada apoiou ubuntu 20.04. Como algumas extensões Azure VM não suportam Ubuntu 20.04, o suporte para esta versão do Ubuntu está a ser removido.

- Melhorias de fiabilidade para as implementações de extensões.

### <a name="known-issues"></a>Problemas conhecidos

Se estiver a utilizar uma versão mais antiga do agente Linux e o configurar para utilizar um servidor proxy, tem de reconfigurar a definição do servidor proxy após a atualização. Para fazer isto, `sudo azcmagent_proxy add http://proxyserver.local:83` corra.

## <a name="next-steps"></a>Passos seguintes

Antes de avaliar ou ativar os servidores ativados pelo Arc em várias máquinas híbridas, [reveja a visão geral do agente da Máquina Conectada](agent-overview.md) para compreender os requisitos, detalhes técnicos sobre o agente e métodos de implementação.