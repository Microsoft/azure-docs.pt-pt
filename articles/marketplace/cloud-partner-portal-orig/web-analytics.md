---
title: Análise da Web | Documentos da Microsoft
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 89cc8c4bffe910de0861d7f44925a10df3811fdb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097479"
---
<a name="web-analytics"></a>Análise da Web
=============

Este artigo fornece a com instruções sobre como obter e utilizar a análise da Web para melhor desenvolver o seu negócio. Atualmente este separador de informações está disponível para quaisquer ofertas de AppSource.

Agora que criou e publicou a sua oferta, a próxima parte da sua jornada é controlar e medir seu\' êxito. Com o **análise Web**, adicionámos a capacidade de ver exatamente a eficiência com que cada uma das suas ofertas está fazendo no marketplace. Para começar a sua jornada, navegue para a página de informações no lado esquerdo do Portal de parceiros da Cloud para ver o novo separador de análise.

![Página de WebAnalytics](./media/si-getting-started/WebAnalytics1.png)

Verá um dashboard avançado para o seu ID de publicador, que foi criada com o Microsoft Power BI e permite-lhe ver cada uma das suas ofertas\' dados, o que são atualizados diariamente.

<a name="microsoft-campaigns"></a>**Microsoft campanhas**
-----------------------

Para expandir suas ofertas e controlar o crescimento das suas ofertas, ativámos a capacidade de usar **campanhas de Microsoft** no Portal de parceiros de nuvem. Campanhas são uma funcionalidade recentemente suportada para o mercado que permite que controle os diferentes canais que estão a enviar os clientes na sua página de detalhes da aplicação.

### <a name="how-to-make-a-campaign"></a>**Como tornar uma campanha**

A forma mais simples para descrever as campanhas é que está a adicionar um termo/word personalizado para o URL que está na sua página de detalhes da aplicação no marketplace. Google, Bing, LinkedIn e muitos outros sites, recomendo que crie um anúncio, ligar a partir do respetivo site em seu site pretendido.

Em geral, esses esforços são para ajudar os clientes de novas de unidade em seu produto e é essencial para medir o sucesso de cada um dos seus canais de estado de funcionamento. É onde entram as campanhas.

Existem duas formas de gerar seu próprio campanha.

1. Adicionar ao seu URL, o parâmetro de consulta **mktcmpid** que descreve as novidades da campanha e quais eventos/página esses clientes são provenientes.

Por exemplo, pode utilizar: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Avançado): Utilize um dos nossos campanha suportada, genérica IDs no URL. Gostaríamos de acomodar com etiquetas de ref adicionais que tem de utilizar, portanto, damos suporte a Convenção de reconhece automaticamente essas marcas adicionais:
    
    1. **utm\_campaign**
    2. **utm\_source**
    3. **ref**
    4. **src**

Por exemplo, pode utilizar: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Pode optar por ter uma combinação de várias destas identificações de campanha para identificar ainda mais a originar tráfego para a campanha, como o cliente proveniência (e-mail, blogue, origem de mídia social, etc.) de várias origens.

Por exemplo:

1. Referenciador newsletter:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. Referenciador LinkedIn:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Campanhas de garantir que passam por todas as suas páginas**

Pode haver um cenário em que as suas campanhas têm uma página intermediária que estão a originar tráfego para o que, em seguida, prossegue para enviar os clientes no Marketplace. É importante passar por meio da sua campanha inicial IDs para o URL final que envia para o marketplace.

Segue-se um exemplo:

1. Funcionário de marketing compra anúncios do Google para o tráfego de unidade para a empresa\'página de destino s <https://contoso.com>. Esta página de destino tem um \"Experimente o meu produto\" ligação que direciona-o para <https://appsource.com>.
2. Um usuário clica o ad e pousar em sua empresa\'página de destino de s.
    1.  URL de referência = google.com
    2.  URL da página de destino = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. A utilizador clica no \"Experimente o meu produto\" associar e entra no AppSource.
    1. URL de referência =  <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. URL da página de aterrissagem (**Certifique-se de que este URL tem utm\_campanha e utm\_adicionada a este URL de origem**) = [ https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername? **utm\_campanha = MyCampaignAdName & utm\_origem = MySourceAdName**](https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Como avaliar o sucesso de uma campanha
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Visitas de página por campanha**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Esta é a divisão de cada um dos seus diária visitas de página por campanha que eles vieram.

### <a name="conversion-rate-by-campaign"></a>**Taxa de conversão por campanha**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Semelhante a como a taxa de conversão da sua oferta de toda, vamos mostrar neste gráfico, que pode ver a divisão de como estão a fazer as suas campanhas diferentes. Este gráfico deve ajudar a identificar onde as suas campanhas de taxa de conversão superior são provenientes.

### <a name="distribution-by-campaign"></a>**Distribuição por campanha**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Semelhante a como examinarmos os domínios dos seus clientes, este gráfico permite-lhe ver a distribuição dos seus dados por campanha que os utilizadores estão a chegar ao mercado em. \_NoCampaign significa que o cliente não tinha um ID de campanha no url quando eles navegar no Marketplace.

<a name="next-steps"></a>**Passos Seguintes**
--------------

Agora que tem a capacidade de controlar o sucesso dos suas ofertas do, queremos encorajá-lo a criar seu próprio campanhas.

Se tiver perguntas/funcionalidade pedidos, partilhá-los por meio de comentários, localizado no canto superior direito.

![Comentários no Portal de parceiros da Cloud](./media/si-getting-started/WebAnalytics5.png)
