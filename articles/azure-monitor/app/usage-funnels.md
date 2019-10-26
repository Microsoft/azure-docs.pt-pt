---
title: Funils do Aplicativo Azure insights
description: Saiba como você pode usar os Funils para descobrir como os clientes estão interagindo com seu aplicativo.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: d6a6e34f9688747891ac91401941a96b6e85d76a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899460"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Descubra como os clientes estão usando seu aplicativo com Application Insights Funils

Entender a experiência do cliente é o máximo de importância para seus negócios. Se seu aplicativo envolver vários estágios, você precisará saber se a maioria dos clientes está progredindo por todo o processo ou se eles estão encerrando o processo em algum momento. A progressão por meio de uma série de etapas em um aplicativo Web é conhecida como *funil*. Você pode usar Aplicativo Azure os Funils de informações para obter informações sobre os usuários e monitorar as taxas de conversão passo a passo. 

## <a name="create-your-funnel"></a>Criar seu funil
Antes de criar o funil, decida sobre a pergunta que deseja responder. Por exemplo, talvez você queira saber quantos usuários estão exibindo o home page, exibindo um perfil de cliente e criando um tíquete. Neste exemplo, os proprietários da empresa Fabrikam Fiber querem saber a porcentagem de clientes que criaram com êxito um tíquete de cliente.

Estas são as etapas necessárias para criar o funil.

1. Na ferramenta Application Insights Funils, selecione **novo**.
1. No menu suspenso **intervalo de tempo** , selecione **últimos 90 dias**. Selecione **meus funils** ou **funils compartilhados**.
1. Na lista suspensa **etapa 1** , selecione **índice**. 
1. Na lista **etapa 2** , selecione **cliente**.
1. Na lista **etapa 3** , selecione **criar**.
1. Adicione um nome ao funil e selecione **salvar**.

A captura de tela a seguir mostra um exemplo do tipo de dados que a ferramenta de funil gera. Os proprietários da Fabrikam podem ver que durante os últimos 90 dias, 54,3% de seus clientes que visitaram o home page criou um tíquete de cliente. Eles também podem ver que 2.700 de seus clientes vieram para o índice da home page. Isso pode indicar um problema de atualização.


![Captura de tela da ferramenta de funil com dados](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Recursos de funils
A captura de tela anterior inclui cinco áreas realçadas. Esses são recursos de Funils. A lista a seguir explica mais sobre cada área correspondente na captura de tela:
1. Se seu aplicativo for amostrado, você verá uma faixa de amostragem. A seleção da faixa abre um painel de contexto, explicando como desativar a amostragem. 
2. Você pode exportar o funil para [Power bi](../../azure-monitor/app/export-power-bi.md ).
3. Selecione uma etapa para ver mais detalhes à direita. 
4. O gráfico de conversão histórica mostra as taxas de conversão nos últimos 90 dias. 
5. Entenda melhor seus usuários acessando a ferramenta usuários. Você pode usar filtros em cada etapa. 

## <a name="next-steps"></a>Passos seguintes
  * [Visão geral de uso](usage-overview.md)
  * [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
  * [Retenção](usage-retention.md)
  * [Livros](../../azure-monitor/app/usage-workbooks.md)
  * [Adicionar contexto de usuário](usage-send-user-context.md)
  * [Exportar para o Power BI](../../azure-monitor/app/export-power-bi.md )

