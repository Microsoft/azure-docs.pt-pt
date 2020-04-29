---
title: Análise Web
description: Este artigo fornece-lhe instruções sobre como aprender e usar o Web Analytics para melhor fazer crescer o seu negócio.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bd6d6375b19689c582c0f129378c76936e624e04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416288"
---
<a name="web-analytics"></a>Análise Web
=============

Este artigo fornece-lhe instruções sobre como aprender e usar o Web Analytics para melhor fazer crescer o seu negócio. Atualmente este separador Insights está disponível para quaisquer ofertas appSource.

Agora que construiu e publicou a sua oferta, a próxima parte\' da sua viagem é acompanhar e medir o seu sucesso. Com o **Web Analytics,** adicionámos a capacidade de ver exatamente o quão bem cada uma das suas ofertas está a correr no mercado. Para iniciar a sua viagem, navegue para a página Insights no lado esquerdo do Cloud Partner Portal para ver o novo separador Analytics.

![Página WebAnalytics](./media/si-getting-started/WebAnalytics1.png)

Você verá um dashboard rico para o seu ID editor que foi construído com\' o Microsoft Power BI e permite-lhe ver cada uma das suas ofertas de dados, que é atualizado diariamente.

<a name="microsoft-campaigns"></a>**Campanhas da Microsoft**
-----------------------

Para aumentar as suas ofertas e acompanhar o crescimento das suas **ofertas,** permitimos a capacidade de utilizar as campanhas da Microsoft no Portal do Parceiro cloud. As campanhas são uma funcionalidade recentemente suportada para o mercado que lhe permitirá rastrear os diferentes canais que estão a enviar clientes para a sua página de detalhes da sua aplicação.

### <a name="how-to-make-a-campaign"></a>**Como fazer uma Campanha**

A forma mais simples de descrever campanhas é que está a adicionar uma palavra/termo personalizado ao seu URL que aterra na página de detalhes da sua aplicação no mercado. Google, Bing, LinkedIn e muitos outros sites incentivam-no a construir um anúncio, link do seu site para o seu site pretendido.

Em geral, estes esforços são para ajudar a impulsionar novos clientes no seu produto e é essencial medir o sucesso de como cada um dos seus canais está a fazer. É aqui que entram as campanhas.

Há duas maneiras de gerar a sua própria campanha.

1. Adicione ao seu URL o parâmetro de consulta **mktcmpid** que descreve o que é a campanha e de que página/evento estes clientes estão vindo.

Por exemplo, pode utilizar:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign```

1. (Avançado): Use uma das nossas identificações de campanha genéricas apoiadas no URL. Queremos acomodar-nos com etiquetas ref adicionais que você precisa usar, por isso apoiamos a convenção para reconhecer automaticamente estas tags adicionais:
    
    1. **campanha\_utm**
    2. **fonte\_utm**
    3. **ref**
    4. **src**

Por exemplo, pode utilizar:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign```

Pode optar por ter uma combinação de vários destes IDs de campanha para identificar ainda mais várias fontes que conduzem o tráfego para a campanha, como de onde o cliente veio (e-mail, blog, fonte de redes sociais, etc.).

Por exemplo:

1. Newsletter:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter```
2. Referer LinkedIn:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn```

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Garantir que as campanhas passem por todas as suas páginas**

Pode haver um cenário em que as suas campanhas tenham uma página intermédia para que esteja a conduzir o tráfego para que, em seguida, proceda ao envio dos clientes para o mercado. É importante passar as suas iDs de campanha iniciais para o URL final que envia para o mercado.

Segue-se um exemplo:

1. Funcionário de marketing compra anúncios da Google\'para conduzir ```https://contoso.com```o tráfego para a página de aterragem da empresa . Esta página de \"aterragem\" tem uma ```https://appsource.com```tentativa do meu link de produto que vai para .
2. Um utilizador clica no anúncio e\'aterra na página de aterragem da sua empresa.
    1.  URL de referência = google.com
    2.  URL da página de aterragem =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
3. O utilizador clica no link do \"meu produto\" e entra no AppSource.
    1. URL de referência =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
    2. URL da página de aterragem (**Certifique-se\_de que este URL tem a campanha utm e a fonte utm\_adicionada a este URL**) =```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**```

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Como avaliar o sucesso de uma Campanha
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Visitas de página por campanha**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Esta é a desagregação de cada uma das suas visitas diárias pela campanha de onde vieram.

### <a name="conversion-rate-by-campaign"></a>**Taxa de conversão por campanha**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

À semelhança de como mostramos a taxa de conversão de toda a sua oferta, neste gráfico pode ver a desagregação de como estão as suas diferentes campanhas. Este gráfico deve ajudá-lo a identificar de onde vêm as suas campanhas de taxa de conversão mais elevadas.

### <a name="distribution-by-campaign"></a>**Distribuição por campanha**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

À semelhança da forma como olhamos para os domínios dos seus clientes, este gráfico permite-lhe ver a distribuição dos seus dados por campanha em que os utilizadores estão a chegar ao mercado. \_NoCampaign significa que o cliente não tinha um ID de campanha na url quando navegava para o mercado.

<a name="next-steps"></a>**Próximos Passos**
--------------

Agora que tem a capacidade de acompanhar o sucesso das suas ofertas, queremos encorajá-lo a criar as suas próprias campanhas.

Se tiver pedidos de perguntas/funcionalidades, partilhe-os através do Feedback, localizado no canto superior direito.

![Feedback no Portal do Parceiro cloud](./media/si-getting-started/WebAnalytics5.png)
