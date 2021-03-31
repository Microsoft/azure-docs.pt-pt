---
title: Relatórios Personalizados da Verizon | Microsoft Docs
description: 'Pode ver os padrões de utilização do seu CDN utilizando os seguintes relatórios: Largura de banda, dados transferidos, acessos, estados de cache, relação de impacto de cache, ipv4/IPV6 Dados transferidos.'
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: cff355aeb74efc9dec2780840ae57fea4c5764cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84888713"
---
# <a name="custom-reports-from-verizon"></a>Relatórios Personalizados da Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ao utilizar relatórios personalizados da Verizon através do portal Manage para perfis Verizon, pode definir o tipo de dados a recolher para relatórios CNAMEs de borda.


## <a name="accessing-verizon-custom-reports"></a>Aceder a Relatórios Personalizados da Verizon
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão de perfil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    O portal de gestão cdn abre.
2. Passe por cima do **separador Analytics** e, em seguida, paire sobre o flyout **de Relatórios Personalizados.** Clique **em CNAMEs de borda**.
   
    ![Portal de gestão CDN - Menu de relatórios personalizados](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Relatório personalizado edge CNAMES
O relatório personalizado Edge CNAMES fornece acessos e estatísticas transferidas de dados para cnames de borda em que a registo de relatórios personalizados foi ativada. Edge CNAMEs consistem em hostnames de ponto final Azure CDN e quaisquer nomes de anfitriões de domínio personalizado associados. 

O registo de dados de relatórios personalizados começa uma hora depois de ativar a capacidade de reporte personalizado da CNAME. Pode ver os dados do relatório gerando um relatório Edge CNAMEs para uma plataforma específica ou para todas as plataformas. A cobertura deste relatório limita-se à borda cnames para a qual foram recolhidos dados de relatórios personalizados durante o período de tempo especificado. O relatório CNAMEs de borda consiste num gráfico e numa tabela de dados para as 10 CNAMEs de ponta superior, de acordo com a métrica definida na opção Métricas. 

Gere um relatório personalizado definindo as seguintes opções de relatório:

- Métricas: As seguintes opções são suportadas:

   - Hits: Indica o número total de pedidos que são direcionados para uma cname de borda em que a capacidade de reporte personalizado está ativada. Esta métrica não inclui o código de estado devolvido ao cliente.

   - Dados transferidos: Indica a quantidade total de dados transferidos dos servidores de borda para os clientes HTTP (por exemplo, navegadores web) para pedidos que são direcionados para uma cname de borda em que a capacidade de reporte personalizado está ativada. A quantidade de dados transferidos é calculada adicionando cabeçalhos de resposta HTTP ao organismo de resposta. Como resultado, a quantidade de dados transferidos para cada ativo é maior do que o seu tamanho real de ficheiro.

- Agrupamentos: Determina o tipo de estatísticas que são mostradas abaixo da tabela de barras. As seguintes opções são suportadas:

   - CÓDIGOS DE RESPOSTA HTTP: Organiza estatísticas por código de resposta HTTP (por exemplo, 200, 403, etc.) devolvidos ao cliente. 

   - Estado da Cache: Organiza estatísticas por cache status.


Para definir o intervalo de datas para o relatório, pode selecionar uma gama de datas pré-definida, como **Hoje** ou **Esta Semana,** a partir da lista de drop-down ou pode selecionar **Personalizado** e introduzir manualmente um intervalo de datas clicando nos ícones do calendário. 

Depois de ter selecionado o intervalo de datas, clique em **Go** para gerar o relatório.

Pode exportar os dados em formato Excel clicando no símbolo Excel à direita do botão **Go.**

![Relatório CNAMEs](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Edge CNAMES campos de relatório personalizado

| Campo                     | Descrição   |
|---------------------------|---------------|
| 2xx                       | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME que resulta num código de estado HTTP 2xx (por exemplo, 200 OK). |
| 3xx                       | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME que resulta num código de estado HTTP 3xx (por exemplo, 302 Encontrados ou 304 Não Modificados. |
| 4xx                       | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME que resulta num código de estado HTTP 4xx (por exemplo, 400 Mau Pedido, 403 Proibidos ou 404 Não Encontrados). |
| 5xx                       | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME que resulta num código de estado HTTP 5xx (por exemplo, 500 Erro de Servidor Interno ou 502 Bad Gateway). |
| Cache Hit %               | Indica a percentagem de pedidos cacheable que foram servidos diretamente da cache para o solicitador. |
| Acertos na Cache                | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME que resulta num sucesso de cache (por exemplo, TCP_EXPIRED_HIT, TCP_HIT ou TCP_PARTIAL_HIT). Um golpe de cache ocorre quando uma versão em cache do conteúdo solicitado é encontrada. |
| Dados transferidos (MB)     | Indica a quantidade total de dados transferidos (MB) dos servidores de borda para clientes HTTP (navegadores web) para a borda CNAME. A quantidade de dados transferidos é calculada adicionando os cabeçalhos de resposta HTTP ao organismo de resposta. Como resultado, a quantidade de dados transferidos para cada ativo é maior do que o seu tamanho real de ficheiro. |
| Description               | Identifica uma borda CNAME pelo seu nome de anfitrião |
| Acertos                      | Indica o número total de pedidos à borda CNAME |
| Misses                    | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME que resulta numa falha de cache (por exemplo, TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS ou TCP_MISS). Uma falha de cache ocorre quando o conteúdo solicitado não foi colocado no servidor de borda que honrou o pedido. | 
| Sem Cache                  | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME que resulta num código de estado de cache CONFIG_NOCACHE.  |
| Outro                     | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME indicado que resulta num código de estado HTTP que se encontra fora da gama 2xx - 5xx. |
| Plataforma                  | Indica a plataforma que trata do tráfego da CNAME. |
| Não atribuído               | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME para a qual o código de estado da cache ou informações do código de estado HTTP não foram registados.  |
| Incacheable               | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME que resulta num código de estado de cache UNCACHEABLE.  |


## <a name="considerations"></a>Considerações
Os relatórios só podem ser gerados nos últimos 18 meses.

