---
title: Funis de insights de aplicação Azure
description: Saiba como pode usar funis para descobrir como os clientes estão interagindo com a sua aplicação.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 89440a6385bab5b917a866b686e8d2ba828c92e5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671057"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Descubra como os clientes estão a usar a sua aplicação com funils de insights de aplicação

Compreender a experiência do cliente é da maior importância para o seu negócio. Se a sua aplicação envolve várias fases, precisa de saber se a maioria dos clientes está a progredir durante todo o processo, ou se estão a terminar o processo em algum momento. A progressão através de uma série de passos numa aplicação web é conhecida como *funil.* Pode utilizar os Funis de Insights de Aplicação Azure para obter informações sobre os seus utilizadores e monitorizar as taxas de conversão passo a passo. 

## <a name="create-your-funnel"></a>Crie o seu funil
Antes de criar o seu funil, decida sobre a pergunta que quer responder. Por exemplo, você pode querer saber quantos utilizadores estão a ver a página inicial, visualizando um perfil de cliente e criando um bilhete. Neste exemplo, os proprietários da empresa Fabrikam Fiber querem saber a percentagem de clientes que criam com sucesso um bilhete de cliente.

Aqui estão os passos que tomam para criar o seu funil.

1. Na ferramenta Funis De Insights de Aplicação, selecione **New**.
1. A partir do menu de entrega do Intervalo de **Tempo,** selecione **Last 90 days**. Selecione **os meus funis** ou **funis partilhados.**
1. A partir da lista de descidas do **Passo 1,** selecione **Index**. 
1. A partir da lista **Passo 2,** selecione **Cliente**.
1. A partir da lista **passo 3,** selecione **Criar**.
1. Adicione um nome ao funil e selecione **Guardar**.

A imagem que se segue mostra um exemplo do tipo de dados que a ferramenta Funnels gera. Os proprietários da Fabrikam podem ver que nos últimos 90 dias, 54,3 por cento dos seus clientes que visitaram a página inicial criaram um bilhete de cliente. Podem também ver que 2.700 dos seus clientes chegaram ao índice a partir da página inicial. Isto pode indicar um problema de atualização.


![Screenshot da ferramenta Funilcom dados](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Funis funis
A imagem anterior inclui cinco áreas destacadas. Estas são características de Funis. A seguinte lista explica mais sobre cada área correspondente na imagem:
1. Se a sua aplicação for amostrada, verá um banner de amostragem. A seleção do banner abre um painel de contexto, explicando como desligar a amostragem. 
2. Pode exportar o seu funil para [Power BI.](../../azure-monitor/app/export-power-bi.md )
3. Selecione um passo para ver mais detalhes à direita. 
4. O gráfico histórico de conversão mostra as taxas de conversão dos últimos 90 dias. 
5. Compreenda melhor os seus utilizadores acedendo à ferramenta dos utilizadores. Pode utilizar filtros em cada passo. 

## <a name="next-steps"></a>Passos seguintes
  * [Visão geral do uso](usage-overview.md)
  * [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
  * [Retenção](usage-retention.md)
  * [Livros](../../azure-monitor/app/usage-workbooks.md)
  * [Adicionar contexto de utilizador](usage-send-user-context.md)
  * [Exportar para o Power BI](../../azure-monitor/app/export-power-bi.md )

