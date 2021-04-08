---
title: Monitorizar o Azure FXT Edge Filer
description: Como monitorizar o estado de hardware para a cache de armazenamento híbrido Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 86e1d74d5e4ab9f6e799c73bcf0d807d0d874f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219722"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitor Azure FXT Edge Filer estado de hardware

O sistema de cache híbrido Azure FXT Edge Filer tem múltiplas luzes de estado incorporadas no chassis para ajudar os administradores a entender como o hardware está funcionando.

## <a name="system-health-status"></a>Estado de saúde do sistema

Para monitorizar as operações de cache a um nível mais elevado, utilize a página do Painel de **Controlo** do Software, conforme descrito no Guia do [Painel de Controlo](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>LEDs de estado de hardware

Esta secção explica as várias luzes de estado incorporadas no hardware Azure FXT Edge Filer.

### <a name="hard-drive-status-leds"></a>LEDs de estado de disco rígido

![imagem do disco rígido à frente, horizontal, com etiquetas de chamada 2 (canto superior esquerdo), 1 (canto inferior esquerdo) e 3 (lado direito)](media/fxt-monitor/fxt-drive-callouts.png)

Cada transportador de acionamento tem dois LEDs de estado: um indicador de atividade (1) e um indicador de estado (2).

* O LED de atividade (1) acende-se quando a unidade está a ser utilizada.
* O LED de estado (2) indica o estado da unidade utilizando os códigos na tabela abaixo.

| Estado LED de estado de unidade              | Significado  |
|-------------------------------------|----------------------------------------------------------|
| Pisca verde duas vezes por segundo      | Unidade de identificação *ou* <br> Preparação da unidade para a remoção  |
| Off (não iluminado)                         | O sistema não terminou o arranque *ou* <br>A unidade está pronta para ser removida |
| Flashes verde, âmbar e fora       | Está prevista a falha de unidade   |
| Pisca âmbar quatro vezes por segundo | A unidade falhou   |
| Verde sólido                         | A unidade está online |

O lado direito da unidade (3) está rotulado com a capacidade da unidade e outras informações.

Os números de unidade são impressos no espaço entre as unidades. No Azure FXT Edge Filer, a unidade 0 é a unidade superior esquerda, e a unidade 1 está diretamente por baixo. A numeragem continua nesse padrão.

![foto de um compartimento de disco rígido no chassis FXT, mostrando números de unidade e etiquetas de capacidade](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Painel de controlo esquerdo

O painel de controlo frontal esquerdo tem vários indicadores LED de estado (1) e um grande indicador de saúde do sistema iluminado (2).

![painel de estado esquerdo, com 1 indicadores de estado de rotulagem à esquerda, e 2 rotulando a luz indicadora de saúde do sistema grande à direita](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indicadores de estado do painel de controlo

Os indicadores de estado à esquerda mostram uma luz âmbar sólida se houver um erro nesse sistema. O quadro abaixo descreve possíveis causas e soluções para os erros.

Se ainda tiver o erro depois de experimentar estas soluções, [contacte o suporte](fxt-support-ticket.md) para obter ajuda.

| Ícone | Description | Condição de erro | Possíveis soluções |
|----------------|---------------|--------------------|----------------------|
| ![ícone de unidade](media/fxt-monitor/fxt-hd-icon.jpg) | Estado da unidade | Erro de unidade | Consulte o Registo de Eventos do Sistema para determinar se a unidade tem um erro, ou <br>Executar o teste de diagnóstico online adequado; reiniciar o sistema e executar diagnósticos incorporados (ePSA), ou <br>Se as unidades estiverem configuradas numa matriz RAID, reinicie o sistema e introduza o programa de utilidade de configuração do adaptador de hospedeiro |
|![ícone de temperatura](media/fxt-monitor/fxt-temp-icon.jpg) | Estado da temperatura | Erro térmico - por exemplo, uma ventoinha falhou ou a temperatura ambiente está fora de alcance | Verifique as seguintes condições endereçais: <br>Falta uma ventoinha de arrefecimento ou falhou <br>A tampa do sistema, a mortalha de ar, o módulo de memória em branco ou o suporte de enchimento traseiro são removidos <br>A temperatura ambiente é muito alta <br>O fluxo de ar externo está obstruído |
|![ícone de eletricidade](media/fxt-monitor/fxt-electric-icon.jpg) | Estado elétrico | Erro elétrico - por exemplo, tensão fora de alcance, PSU falhado ou regulador de tensão falhado |  Verifique o registo de eventos do sistema ou as mensagens do sistema para obter o problema específico. Se houver algum problema com a PSU, verifique o LED de estado da PSU e volte a ensetar a PSU, se necessário. |
|![ícone de memória](media/fxt-monitor/fxt-memory-icon.jpg) | Estado da memória | Erro de memória | Verifique o registo de eventos do sistema ou as mensagens do sistema para a localização da memória falhada; resetar o módulo de memória. |
|![Ícone de PCIE](media/fxt-monitor/fxt-pcie-icon.jpg) | Estado do PCIe | Erro do cartão PCIe | Reiniciar o sistema; atualizar os controladores de cartões PCIe; reinstalar o cartão |

### <a name="system-health-status-indicator"></a>Indicador de estado de saúde do sistema

O botão de iluminação grande à direita do painel de controlo esquerdo indica o estado geral do sistema e também é utilizado como uma luz de localização de unidade no modo de ID do sistema.

Pressione a saúde do sistema e o botão de identificação para alternar entre o modo de identificação do sistema e o modo de saúde do sistema.

|Estado do estado de saúde do sistema | Condição |
|-------------------------------------------|-----------------------------------------------|
| Azul sólido | Funcionamento normal: o sistema está ligado, funciona normalmente e o modo de identificação do sistema não está ativo. <br/>Pressione a saúde do sistema e o botão de identificação se quiser mudar para o modo de identificação do sistema. |
| Piscar azul | O modo ID do sistema está ativo. Pressione o botão de identificação para a saúde do sistema e para o sistema se quiser mudar para o modo de saúde do sistema. |
| Âmbar sólido | O sistema está em modo de segurança. Se o problema persistir, contacte o [Serviço e Suporte ao Cliente da Microsoft.](fxt-support-ticket.md) |
| Âmbar piscando | Falha no sistema. Verifique o registo de eventos do sistema para obter mensagens de erro específicas. Para obter informações sobre o evento e mensagens de erro geradas pelo firmware do sistema e agentes que monitorizam os componentes do sistema, consulte a página Error Code Lookup qrl.dell.com. |
