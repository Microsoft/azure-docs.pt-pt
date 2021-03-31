---
title: Introdução aos diagnósticos de configuração de rede no Azure Network Watcher | Microsoft Docs
description: Esta página fornece uma visão geral do Observador de Rede - Diagnóstico de Configuração de Rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 5feef79a08789ad381b0c93cb938abd9effdfcc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502013"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Introdução aos Diagnósticos de Configuração de Rede no Observador de Redes Azure

A ferramenta de Diagnóstico de Configuração de Rede ajuda os clientes a entender quais os fluxos de tráfego que serão permitidos ou negados na sua Rede Virtual Azure, juntamente com informações detalhadas para depuração. Pode ajudar a compreender se as suas regras NSG estiverem configuradas corretamente. 

## <a name="pre-requisites"></a>Pré-requisitos
Para utilizar diagnósticos de configuração de rede, o Observador de Rede deve ser ativado na sua subscrição. Consulte [Criar uma instância do Observador de Rede Azure](./network-watcher-create.md) para ativar.

## <a name="background"></a>Fundo

- Os seus recursos em Azure estão ligados através de Redes Virtuais (VNETs) e sub-redes. A segurança destes VNets e sub-redes pode ser gerida através de um Grupo de Segurança de Rede (NSG).
- Um NSG contém uma lista de regras de segurança que permitem ou negam o tráfego de rede aos recursos a que está ligado. Os NSGs podem ser associados com sub-redes, VMs individuais ou interfaces de rede individuais (NICs) anexas aos VMs. 
- Todos os fluxos de tráfego da sua rede são avaliados usando as regras do NSG aplicável.
- As regras são avaliadas com base no número de prioridades do mais baixo ao mais alto 

## <a name="how-does-network-configuration-diagnostic-work"></a>Como funciona o diagnóstico de configuração de rede? 

Para um determinado fluxo, a ferramenta NCD executa uma simulação do fluxo e devolve se o fluxo seria permitido (ou negado) e informações detalhadas sobre regras que permitem/negam o fluxo.  Os clientes devem fornecer detalhes de um fluxo como fonte, destino, protocolo, etc. A ferramenta devolve se o tráfego foi permitido ou negado, as regras do NSG que foram avaliadas para o fluxo especificado e os resultados de avaliação para cada regra.

## <a name="next-steps"></a>Passos seguintes

Utilizar diagnóstico de configuração de rede através de outras interfaces
 - [API REST](/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic)
 - [CLI do Azure](/cli/azure/network/watcher#az_network_watcher_run_configuration_diagnostic)