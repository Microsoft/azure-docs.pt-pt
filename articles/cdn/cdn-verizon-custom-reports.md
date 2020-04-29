---
title: Relatórios Personalizados de Verizon [ Microsoft Docs
description: 'Pode visualizar padrões de utilização para o seu CDN utilizando os seguintes relatórios: Largura de Banda, Dados Transferidos, Acessos, Estados de Cache, Relação de Impacto de Cache, IPV4/IPV6 Dados transferidos.'
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
ms.topic: article
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: 737803f5b7fcddb07787afbb48354b4ca922e9ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259753"
---
# <a name="custom-reports-from-verizon"></a>Relatórios Personalizados de Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ao utilizar relatórios personalizados verizon através do portal Manage para perfis Verizon, pode definir o tipo de dados a recolher para relatórios DE SCNAMEs de borda.


## <a name="accessing-verizon-custom-reports"></a>Acesso a Relatórios Personalizados verizon
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Perfil CDN Gerir botão](./media/cdn-reports/cdn-manage-btn.png)
   
    Abre o portal de gestão da CDN.
2. Paire sobre o separador **Analytics** e, em seguida, paire sobre o voo custom **Reports.** Clique em **Edge CNAMEs**.
   
    ![Portal de gestão cdN - Menu de relatórios personalizados](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Relatório personalizado Edge CNAMES
O relatório personalizado Edge CNAMES fornece acessos e estatísticas transferidas de dados para CNAMEs de borda sobre as quais foi ativado o registo de relatórios personalizados. Os CNAMEs edge consistem em nomes de anfitriões de ponto final Azure CDN e quaisquer nomes de anfitriões de domínio personalizado associados. 

O registo personalizado de dados do relatório começa uma hora depois de ativar a capacidade de reporte personalizada de um CNAME de borda. Pode ver os dados do relatório gerando um relatório Edge CNAMEs para uma plataforma específica ou para todas as plataformas. A cobertura deste relatório limita-se à borda cnames para as quais foram recolhidos dados de relatórios personalizados durante o período de tempo especificado. O relatório CNAMEs de borda consiste num gráfico e numa tabela de dados para os 10 CNAMEs de borda superior, de acordo com a métrica definida na opção Métricas. 

Gerar um relatório personalizado definindo as seguintes opções de relatório:

- Métricas: As seguintes opções são suportadas:

   - Acessos: Indica o número total de pedidos que são direcionados para um CNAME de borda no qual a capacidade de reporte personalizada está ativada. Esta métrica não inclui o código de estado devolvido ao cliente.

   - Dados Transferidos: Indica a quantidade total de dados transferidos dos servidores de borda para os clientes HTTP (por exemplo, navegadores web) para pedidos direcionados para um CNAME de borda no qual a capacidade de reporte personalizada está ativada. A quantidade de dados transferidos é calculada adicionando cabeçalhos de resposta HTTP ao corpo de resposta. Como resultado, a quantidade de dados transferidos para cada ativo é maior do que o tamanho real do ficheiro.

- Agrupamentos: Determina o tipo de estatísticas que são mostradas abaixo do gráfico de barras. As seguintes opções são suportadas:

   - Códigos de Resposta HTTP: Organiza estatísticas por código de resposta HTTP (por exemplo, 200, 403, etc.) devolvidas ao cliente. 

   - Estado cache: Organiza estatísticas por estado de cache.


Para definir o intervalo de data para o relatório, pode selecionar uma gama de datas pré-definida, como **hoje** ou **esta semana,** a partir da lista de lançamentos ou pode selecionar **Custom** e introduzir manualmente um intervalo de datas clicando nos ícones do calendário. 

Depois de ter selecionado o intervalo de datas, clique em **Ir** para gerar o relatório.

Pode exportar os dados em formato Excel clicando no símbolo Excel à direita do botão **Go.**

![Relatório cnames](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Campos de relatório personalizados Edge CNAMES

| Campo                     | Descrição   |
|---------------------------|---------------|
| 2xx                       | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME de borda que resulta num código de estado 2xx HTTP (por exemplo, 200 OK). |
| 3xx                       | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME de borda que resulta num código de estado 3xx HTTP (por exemplo, 302 Encontradoou 304 Não Modificado. |
| 4xx                       | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME de borda que resulta num código de estado 4xx HTTP (por exemplo, 400 Bad Request, 403 Proibido ou 404 Não Encontrados). |
| 5xx                       | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME de borda que resulta num código de estado 5xx HTTP (por exemplo, 500 Erro do Servidor Interno ou 502 Bad Gateway). |
| Cache Hit %               | Indica a percentagem de pedidos cacheable que foram servidos diretamente da cache para o solicitador. |
| Cache Hits                | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME de borda que resulta num impacto de cache (por exemplo, TCP_EXPIRED_HIT, TCP_HIT ou TCP_PARTIAL_HIT). Um impacto em cache ocorre quando uma versão em cache do conteúdo solicitado é encontrada. |
| Dados Transferidos (MB)     | Indica a quantidade total de dados transferidos (MB) dos servidores de borda para clientes HTTP (navegadores web) para o Edge CNAME. A quantidade de dados transferidos é calculada adicionando os cabeçalhos de resposta HTTP ao corpo de resposta. Como resultado, a quantidade de dados transferidos para cada ativo é maior do que o tamanho real do ficheiro. |
| Descrição               | Identifica um CNAME de borda pelo seu nome de anfitrião |
| Acertos                      | Indica o número total de pedidos à borda CNAME |
| Misses                    | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME de borda que resulta numa falha de cache (por exemplo, TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS ou TCP_MISS). Uma falha de cache ocorre quando o conteúdo solicitado não foi colocado em cache no servidor de borda que honrou o pedido. | 
| Sem Cache                  | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME de borda que resulta num código de estado de cache CONFIG_NOCACHE.  |
| Outros                     | Indica o número total de pedidos ou dados transferidos (MB) para a borda CNAME indicando que resulta num código de estado HTTP que fica fora da gama 2xx - 5xx. |
| Plataforma                  | Indica a plataforma que lida com o tráfego do CNAME. |
| Não atribuído               | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME de borda para o qual o código de estado de cache ou as informações do código de estado HTTP não foram registadas.  |
| Incacheable               | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME borda que resulta num código de estado de cache UNCACHEABLE.  |


## <a name="considerations"></a>Considerações
Os relatórios só podem ser gerados nos últimos 18 meses.

