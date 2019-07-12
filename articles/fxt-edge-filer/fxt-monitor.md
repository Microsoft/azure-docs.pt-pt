---
title: Monitorizar o filtro de borda FXT do Azure
description: Como monitorizar o estado do hardware para a cache de armazenamento híbrido do Azure FXT Edge filtro
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: e6afd90c4e5a1b0759b3de7789ec37db4c04f2c7
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827450"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorizar o estado de hardware de ficheiros do Azure FXT Edge

O sistema de cache de armazenamento do Azure FXT Edge filtro híbrida tem vários luzes de estado incorporados para os chassis para ajudar os administradores compreender a forma como o hardware esteja funcionando.

## <a name="system-health-status"></a>Estado de funcionamento do sistema

Para monitorizar as operações de cache num nível superior, utilize o painel de controlo do software **Dashboard** página, conforme descrito no [Guia do painel de controle de Dashboard](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Estado do hardware LEDs

Esta secção explica as várias luzes de estado incorporadas para o hardware de ficheiros do Azure FXT Edge.

### <a name="hard-drive-status-leds"></a>Estado da unidade de disco rígido LEDs

![imagem da frente do disco rígido, horizontal, com as etiquetas de nota de aviso 2 (canto superior esquerdo), 1 (canto inferior esquerdo) e 3 (lado direito)](media/fxt-monitor/fxt-drive-callouts.png)

Cada companhia de unidade tem dois LEDs de estado: um indicador de atividade (1) e um indicador de estado (2). 

* A atividade LEVOU luzes (1) quando a unidade está em utilização.  
* O estado LED (2) indica a condição da unidade, utilizando os códigos na tabela abaixo.

| Unidade de estado do Estado do LED              | Significado  |
|-------------------------------------|----------------------------------------------------------|
| Flashes verde duas vezes por segundo      | Identificação de unidade *ou* <br> Preparando a unidade para remoção  |
| Desativado (unlit)                         | Sistema não concluiu a inicialização *ou* <br>Unidade está pronta para ser removido |
| Flashes verde, amber e desativado       | Falha da unidade é prevista   |
| Amber flashes quatro vezes por segundo | Unidade de falha   |
| Verde sólido                         | Unidade está online |

À direita da unidade (3) tem o nome com de capacidade a unidade e outras informações.

Números de unidade são impressos no espaço de entre unidades. O filtro de borda do Azure FXT, unidade 0 é a parte superior esquerda da unidade e unidade 1 está diretamente abaixo dela. Numeração continua esse padrão. 

![fotografia de um disco rígido cais no chassi FXT, que mostra a unidade de números e capacidade de etiquetas](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Painel de controlo do esquerda

O painel de controle de front-esquerdo tem vários indicadores de estado LED (1) e um indicador de estado de funcionamento do sistema lighted grandes (2). 

![Painel de estado à esquerda, com 1 etiquetagem indicadores de estado à esquerda e a etiquetagem o indicador de estado de funcionamento do sistema de grandes dimensões leve no lado direito de 2](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indicadores de estado do painel de controlo 

Os indicadores de estado à esquerda mostram uma luz amber sólida se existe um erro no sistema. A tabela abaixo descreve possíveis causas e soluções para os erros. 

Se ainda terá o erro depois destas soluções, de tentar [contacte o suporte](fxt-support-ticket.md) para obter ajuda. 

| Ícone | Descrição | Condição de erro | Possíveis soluções |
|----------------|---------------|--------------------|----------------------|
| ![ícone de unidade](media/fxt-monitor/fxt-hd-icon.jpg) | Unidade de estado | Erro de unidade | Consulte o registo de eventos do sistema para determinar se a unidade tem um erro, ou <br>Execute o teste de diagnóstico online apropriado; reiniciar o sistema e executar diagnósticos incorporados (ePSA), ou <br>Se as unidades estão configuradas numa matriz RAID, reiniciar o sistema e introduza o programa de utilitário de configuração de adaptador de anfitrião |
|![ícone de temperatura](media/fxt-monitor/fxt-temp-icon.jpg) | Estado de temperatura | Erro térmicos - por exemplo, um fã falhou ou temperatura ambiente está fora do intervalo | Verifique as seguintes condições endereçáveis: <br>Um fã de refrigeração está em falta ou falhou <br>Capa do sistema, shroud ar, módulo de memória em branco ou back-Reto de fecho de preenchimento é removido <br>A temperatura ambiente é demasiado elevada <br>É obstructed ventilação externa |
|![ícone de eletricidade](media/fxt-monitor/fxt-electric-icon.jpg) | Estado elétrico | Erro de elétrico: por exemplo, a tensão fora do intervalo, falha PSU ou regulator uma tensão com falhas |  Verifique o registo de eventos do sistema ou mensagens de sistema para o problema específico. Se houver um problema PSU, verifique o estado PSU LED e reseat o PSU se for necessário. | 
|![ícone de memória](media/fxt-monitor/fxt-memory-icon.jpg) | Estado da memória | Erro de memória | Verifique o registo de eventos do sistema ou mensagens de sistema para a localização da memória com falha; reseat o módulo de memória. |
|![Ícone de PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Estado de PCIe | Erro de cartão de PCIe | Reiniciar o sistema; Atualizar controladores de smart PCIe; reinstalar o cartão |


### <a name="system-health-status-indicator"></a>Indicador de estado de funcionamento do sistema

O botão grande iluminado à direita do painel de controle à esquerda indica o estado geral do sistema e também é utilizado como uma luz de localizador de unidade no modo de ID de sistema.

Prima o botão de ID para alternar entre o modo de ID de sistema e o modo de estado de funcionamento do sistema e o estado de funcionamento do sistema.

|Estado de estado de funcionamento do sistema | Condição |
|-------------------------------------------|-----------------------------------------------|
| Sólido azul | Operação normal: o sistema estiver ativado, operando normalmente, e o modo de ID de sistema não está ativo. <br/>Prima o botão de ID e o estado de funcionamento do sistema se quiser mudar para o modo de ID de sistema. |
| Intermitência azul | Modo de ID de sistema está ativo. Prima o botão de ID de sistema e o estado de funcionamento do sistema, se quiser mudar para o modo de estado de funcionamento do sistema. |
| Amber sólido | O sistema está no modo de isento de falhas. Se o problema persistir, [contacte o suporte ao cliente da Microsoft](fxt-support-ticket.md). |
| Intermitência amber | Falha de sistema. Verifique o registo de eventos do sistema de mensagens de erro específicas. Para obter informações sobre as mensagens de evento e erro gerado pelo firmware do sistema e os agentes que monitorizam os componentes do sistema, consulte a página de pesquisa de código de erro em qrl.dell.com. |


