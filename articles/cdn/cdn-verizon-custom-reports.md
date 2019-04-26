---
title: Relatórios personalizados da Verizon | Documentos da Microsoft
description: 'Pode ver padrões de utilização da sua CDN utilizando os seguintes relatórios: Largura de banda, os dados transferidos, visitas, Estados de Cache, a taxa, dados de IPV4/IPV6 transferidos de acerto na Cache.'
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: magattus
ms.openlocfilehash: 75a1fb4beb4025dc3ec63d29944331d07be35d8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60323607"
---
# <a name="custom-reports-from-verizon"></a>Relatórios personalizados da Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ao utilizar relatórios de personalizados da Verizon através do portal de gerenciar para Verizon perfis, pode definir o tipo de dados a serem recolhidos para relatórios de CNAMEs do edge.


## <a name="accessing-verizon-custom-reports"></a>Aceder a relatórios personalizados da Verizon
1. No painel de perfil da CDN, clique a **gerir** botão.
   
    ![Botão de gerir do perfil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    É aberto o portal de gestão da CDN.
2. Paire o rato sobre o **Analytics** separador, em seguida, coloque o cursor sobre o **relatórios personalizados** submenu. Clique em **CNAMEs de borda**.
   
    ![Portal de gestão CDN - menu de relatórios personalizados](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Relatório do Edge CNAMES personalizados
O relatório personalizado do Edge CNAMES fornece acertos e estatísticas de dados transferidos para CNAMEs de borda no qual o registo de relatório personalizado foi ativado. Edge CNAMEs é composto por nomes de anfitrião de ponto final de CDN do Azure e nomes de anfitrião associado de domínio personalizado. 

Registo de dados de relatório personalizado começa uma hora depois de ativar a capacidade de relatórios personalizada de um CNAME de borda. Pode ver os dados de relatório através da geração de um relatório de CNAMEs de borda de uma plataforma específica ou para todas as plataformas. A cobertura para este relatório está limitada aos CNAMEs de borda para o qual os dados de relatório personalizado foi recolhidos durante o período de tempo especificado. O limite de relatório de CNAMEs consiste numa tabela de dados e de gráfico para o limite de 10 superior CNAMEs, de acordo com a métrica definida na opção de métricas. 

Gere um relatório personalizado, definindo as seguintes opções de relatório:

- Métricas: São suportadas as seguintes opções:

   - Resultados: Indica o número total de pedidos que são direcionados para um CNAME de borda em que a capacidade de relatórios personalizada está ativada. Esta métrica não inclui o código de estado devolvido ao cliente.

   - Dados transferidos: Indica a quantidade total de dados transferidos a partir de servidores de borda para os clientes HTTP (por exemplo, os navegadores da web) para pedidos que são direcionados para um CNAME de borda em que a capacidade de relatórios personalizada está ativada. A quantidade de dados transferidos é calculada ao adicionar cabeçalhos de resposta HTTP para o corpo da resposta. Como resultado, a quantidade de dados transferidos para cada recurso é superior ao tamanho de ficheiro real.

- Agrupamentos: Determina o tipo de estatísticas que são apresentados abaixo o gráfico de barras. São suportadas as seguintes opções:

   - Códigos de resposta de HTTP: Organiza as estatísticas por código de resposta HTTP (por exemplo, 200, 403, etc.) devolvida ao cliente. 

   - Estado da cache: Organiza as estatísticas por Estado da cache.


Para definir o intervalo de datas para o relatório, pode selecionar um intervalo de datas predefinidas, como **hoje** ou **esta semana**, desde a lista pendente, ou pode selecionar **Custom** e introduza manualmente um intervalo de datas, clicando nos ícones de calendário. 

Depois de selecionar o intervalo de datas, clique em **ir** para gerar o relatório.

Pode exportar os dados no formato Excel clicando no símbolo de Excel à direita dos **ir** botão.

![Relatório de CNAMEs](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Campos de relatório personalizado de CNAMES do Edge

| Campo                     | Descrição   |
|---------------------------|---------------|
| 2xx                       | Indica o número total de pedidos ou os dados transferidos (MB) para o edge CNAME que resulta num código de estado 2xx HTTP (por exemplo, 200 OK). |
| 3xx                       | Indica o número total de pedidos ou os dados transferidos (MB) para o edge CNAME que resulta num código de estado HTTP 3xx (por exemplo, 302 encontrado ou 304 não modificado. |
| 4xx                       | Indica o número total de pedidos ou os dados transferidos (MB) para o edge CNAME que resulta num código de estado HTTP 4xx (por exemplo, 400 pedido inválido, 403 proibido ou 404 não encontrado). |
| 5xx                       | Indica o número total de pedidos ou os dados transferidos (MB) para o edge CNAME que resulta num código de estado HTTP 5xx (por exemplo, 500 Erro de servidor interno ou 502 Gateway incorreto). |
| % De acertos na cache               | Indica a percentagem de pedidos em cache que foram fornecidos diretamente a partir da cache para o autor do pedido. |
| Acertos na Cache                | Indica o número total de pedidos ou os dados transferidos (MB) para o edge CNAME que resulta num acertos na cache (por exemplo, TCP_EXPIRED_HIT, TCP_HIT ou TCP_PARTIAL_HIT). Um problema de cache ocorre quando é encontrada uma versão em cache do conteúdo pedido. |
| Dados transferidos (MB)     | Indica a quantidade total de dados transferidos (MB) de servidores de borda para clientes HTTP (navegadores da web) para o edge CNAME. A quantidade de dados transferidos é calculada adicionando os cabeçalhos de resposta HTTP para o corpo da resposta. Como resultado, a quantidade de dados transferidos para cada recurso é superior ao tamanho de ficheiro real. |
| Descrição               | Identifica uma borda CNAME pelo respetivo nome de anfitrião |
| Acertos                      | Indica o número total de pedidos na periferia CNAME |
| Falhas                    | Indica o número total de pedidos ou os dados transferidos (MB) para o edge CNAME que resulta numa falha de acerto na cache (por exemplo, TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS ou TCP_MISS). Uma falha de acerto na cache ocorre quando o conteúdo solicitado não foi colocado em cache no servidor edge que honrou o pedido. | 
| Sem Cache                  | Indica o número total de pedidos ou os dados transferidos (MB) para o edge CNAME que resulta num código de estado de cache CONFIG_NOCACHE.  |
| Outros                     | Indica o número total de pedidos ou os dados transferido (MB) para o edge CNAME indicado que resulta num código de estado HTTP que se encontre fora 2xx - intervalo 5xx. |
| Plataforma                  | Indica a plataforma que processa o tráfego do CNAME edge. |
| Não atribuído               | Indica o número total de pedidos ou os dados transferidos (MB) para o CNAME do edge para que o código de estado de cache ou o código de estado HTTP informações não tenha sido registadas.  |
| Atribuir               | Indica o número total de pedidos ou os dados transferidos (MB) para o edge CNAME que resulta num código de estado atribuir cache.  |


## <a name="considerations"></a>Considerações
Podem ser gerados relatórios apenas nos últimos 18 meses.

