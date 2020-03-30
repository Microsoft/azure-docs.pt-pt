---
title: Monitorizar o Azure FXT Edge Filer
description: Como monitorizar o estado do hardware para o cache de armazenamento híbrido Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254884"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitor Azure FXT Edge Filer hardware

O sistema de cache híbrido Azure FXT Edge Filer tem várias luzes de estado incorporadas no chassis para ajudar os administradores a entender como o hardware está a funcionar.

## <a name="system-health-status"></a>Estado de saúde do sistema

Para monitorizar as operações de cache a um nível mais elevado, utilize a página do Painel de **Controlo** do Software, conforme descrito no Guia do Painel de [Controlo](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>LEDs de estado de hardware

Esta secção explica as várias luzes de estado incorporadas no hardware Azure FXT Edge Filer.

### <a name="hard-drive-status-leds"></a>LEDs de estado de disco rígido

![imagem de disco rígido frontal, horizontal, com etiquetas de chamada 2 (canto superior esquerdo), 1 (canto inferior esquerdo) e 3 (lado direito)](media/fxt-monitor/fxt-drive-callouts.png)

Cada transportador de acionamento tem dois LED de estado: um indicador de atividade (1) e um indicador de estado (2). 

* O LED de atividade (1) acende-se quando a unidade está a ser utilizada.  
* O LED de estado (2) indica o estado da unidade utilizando os códigos na tabela abaixo.

| Estado LED estado de unidade              | Significado  |
|-------------------------------------|----------------------------------------------------------|
| Pisca verde duas vezes por segundo      | Identificação da unidade *ou* <br> Preparação da unidade para a remoção  |
| Desligado (não iluminado)                         | Sistema não terminou arranque *ou* <br>A unidade está pronta para ser removida |
| Flashes verde, âmbar, e fora       | A falha de acionamento está prevista   |
| Pisca âmbar quatro vezes por segundo | Unidade falhada   |
| Verde sólido                         | A unidade está online |

O lado direito da unidade (3) está rotulado com a capacidade da unidade e outras informações.

Os números de unidade são impressos no espaço entre unidades. No Azure FXT Edge Filer, a unidade 0 é a unidade superior esquerda e a unidade 1 está diretamente por baixo. A numeração continua nesse padrão. 

![foto de um compartimento de disco rígido no chassis FXT, mostrando números de unidade e etiquetas de capacidade](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Painel de controlo esquerdo

O painel de controlo dianteiro esquerdo tem vários indicadores LED de estado (1) e um grande indicador de saúde do sistema iluminado (2). 

![painel de estado esquerdo, com 1 indicadores de estado de rotulagem à esquerda, e 2 rotulando a luz indicadora de saúde do sistema à direita](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indicadores de estado do painel de controlo 

Os indicadores de estado à esquerda mostram uma luz âmbar sólida se houver um erro nesse sistema. A tabela abaixo descreve possíveis causas e soluções para os erros. 

Se ainda tiver o erro depois de experimentar estas soluções, [contacte](fxt-support-ticket.md) o suporte para obter ajuda. 

| Ícone | Descrição | Condição de erro | Possíveis soluções |
|----------------|---------------|--------------------|----------------------|
| ![ícone de unidade](media/fxt-monitor/fxt-hd-icon.jpg) | Estado de condução | Erro de acionamento | Verifique o Registo de Eventos do Sistema para determinar se a unidade tem um erro, ou <br>Executar o teste de diagnóstico online apropriado; reiniciar o sistema e executar diagnósticos incorporados (ePSA), ou <br>Se as unidades estiverem configuradas numa matriz RAID, reinicie o sistema e introduza o programa de configuração do adaptador de hospedeiro |
|![ícone de temperatura](media/fxt-monitor/fxt-temp-icon.jpg) | Estado da temperatura | Erro térmico - por exemplo, uma ventoinha falhou ou a temperatura ambiente está fora de alcance | Verifique as seguintes condições endereçáveis: <br>Falta uma ventoinha de arrefecimento ou falhou <br>A cobertura do sistema, a mortalha do ar, o módulo de memória em branco ou o suporte de enchimento traseiro são removidos <br>A temperatura ambiente é muito alta. <br>O fluxo de ar externo está obstruído |
|![ícone de eletricidade](media/fxt-monitor/fxt-electric-icon.jpg) | Estado elétrico | Erro elétrico - por exemplo, tensão fora de alcance, PSU falhado ou um regulador de tensão falhado |  Verifique o registo de eventos do sistema ou as mensagens do sistema para obter o problema específico. Se houver um problema de PSU, verifique o LED de estado da PSU e readesa a PSU se necessário. | 
|![ícone de memória](media/fxt-monitor/fxt-memory-icon.jpg) | Estado da memória | Erro de memória | Verifique o registo de eventos do sistema ou as mensagens do sistema para obter a localização da memória falhada; reseat o módulo de memória. |
|![Ícone pcie](media/fxt-monitor/fxt-pcie-icon.jpg) | Estatuto do PCIE | Erro do cartão PCIe | Reiniciar o sistema; atualizar os controladores de cartões PCIe; reinstalar o cartão |


### <a name="system-health-status-indicator"></a>Indicador de estado de saúde do sistema

O botão de iluminação grande à direita do painel de controlo esquerdo indica o estado geral do sistema e também é utilizado como uma luz localizadorde unidade no modo id do sistema.

Pressione o botão de saúde do sistema e id para alternar entre o modo de id do sistema e o modo de saúde do sistema.

|Estado de saúde do sistema | Condição |
|-------------------------------------------|-----------------------------------------------|
| Azul sólido | Funcionamento normal: o sistema está ligado, funcionando normalmente e o modo ID do sistema não está ativo. <br/>Pressione o botão de saúde e id do sistema se quiser mudar para o modo ID do sistema. |
| Azul piscando | O modo ID do sistema está ativo. Pressione o botão de identificação de saúde e sistema do sistema se quiser mudar para o modo de saúde do sistema. |
| Âmbar sólido | O sistema está em modo de segurança. Se o problema persistir, [contacte](fxt-support-ticket.md)o Serviço e Suporte ao Cliente da Microsoft . |
| Âmbar piscando | Falha no sistema. Verifique o registo do evento do sistema para obter mensagens de erro específicas. Para obter informações sobre o evento e mensagens de erro geradas pelo firmware do sistema e agentes que monitorizam os componentes do sistema, consulte a página de Procura de Código de Erro no qrl.dell.com. |


