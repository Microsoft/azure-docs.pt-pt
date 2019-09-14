---
title: Análise da Web
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a6b2a7981452b47a3992b6c0efee286878e143fb
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962887"
---
<a name="web-analytics"></a>Análise da Web
=============

Este artigo fornece instruções sobre como aprender e usar o Web Analytics para aumentar melhor seus negócios. Atualmente, essa guia insights está disponível para qualquer oferta do AppSource.

Agora que você criou e publicou sua oferta, a próxima parte da sua jornada é acompanhar e medir seu\' sucesso. Com o **Web Analytics**, adicionamos a capacidade de ver exatamente como cada uma de suas ofertas está fazendo no Marketplace. Para iniciar sua jornada, navegue até a página de informações no lado esquerdo da Portal do Cloud Partner para ver a nova guia análise.

![Página do WebAnalytics](./media/si-getting-started/WebAnalytics1.png)

Você verá um painel avançado para sua ID de editor criado com o Microsoft Power bi e permitirá que você veja cada um dos seus dados\' de ofertas, que são atualizados diariamente.

<a name="microsoft-campaigns"></a>**Campanhas da Microsoft**
-----------------------

Para aumentar suas ofertas e acompanhar o crescimento de suas ofertas, habilitamos a capacidade de usar as campanhas da **Microsoft** no portal do Cloud Partner. As campanhas são um recurso com suporte recente para o Marketplace que permitirá que você acompanhe os diferentes canais que estão enviando clientes para a página de detalhes do aplicativo.

### <a name="how-to-make-a-campaign"></a>**Como criar uma campanha**

A maneira mais simples de descrever campanhas é que você está adicionando uma palavra/termo personalizado à sua URL que chega à sua página de detalhes do aplicativo no Marketplace. O Google, Bing, LinkedIn e muitos outros sites incentivam você a criar um anúncio, vincular de seu site ao site desejado.

Em geral, esses esforços são ajudar a impulsionar novos clientes em seu produto e é essencial medir o sucesso de como cada um dos seus canais está se saindo. É aí que entram as campanhas.

Há duas maneiras de gerar sua própria campanha.

1. Adicione à sua URL o parâmetro de consulta **mktcmpid** que descreve o que é a campanha e em qual página/evento os clientes estão vindo.

Por exemplo, você pode usar:<https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Avançado): Use uma das nossas IDs de campanha genéricas com suporte na URL. Queremos ser acomodados com marcas ref adicionais que você precisa usar e, portanto, damos suporte à Convenção para reconhecer automaticamente essas marcas adicionais:
    
    1. **utm\_campaign**
    2. **origem\_do UTM**
    3. **ref**
    4. **src**

Por exemplo, você pode usar:<https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Você pode optar por ter uma combinação de várias dessas IDs de campanha para identificar ainda mais várias fontes direcionando o tráfego para a campanha, como onde o cliente veio (email, blog, fonte de mídia social etc.).

Por exemplo:

1. Referenciador de boletim informativo:<https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. Referenciador do LinkedIn:<https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Garantindo que as campanhas passem por todas as suas páginas**

Pode haver um cenário em que suas campanhas têm uma página intermediária para a qual você está direcionando o tráfego e, em seguida, prossegue para enviar os clientes para o Marketplace. É importante passar por suas IDs de campanha inicial para a URL final que você envia para o Marketplace.

Segue-se um exemplo:

1. O funcionário de marketing compra anúncios do Google para direcionar o\'tráfego para a <https://contoso.com>página de aterrissagem da empresa. Esta página de aterrissagem \"tem um link\" Experimente o meu produto <https://appsource.com>que vai para o.
2. Um usuário clica no anúncio e chega na página inicial\'de sua empresa.
    1.  URL de referência = google.com
    2.  URL da página de aterrissagem =<https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. O usuário clica no \"link Experimente meu\" produto e entra em AppSource.
    1. URL de referência =<https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. URL da página de aterrissagem (**Verifique se essa\_URL tem UTM\_Campaign e UTM Source adicionados a esta URL**) = [ https://appsource.microsoft.com/product/dynamics-365/contoso.offername? **UTM\_ Campaign =\_ MyCampaignAdName & UTM Source = MySourceAdName** ](https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Como avaliar o sucesso de uma campanha
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Visitas à página por campanha**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Esta é a divisão de cada uma das suas visitas de página diárias pela campanha da qual vieram.

### <a name="conversion-rate-by-campaign"></a>**Taxa de conversão por campanha**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Assim como mostramos a taxa de conversão de toda a sua oferta, neste gráfico, você pode ver a análise de como as diferentes campanhas estão fazendo. Este gráfico deve ajudá-lo a identificar onde são provenientes suas campanhas de taxa de conversão mais altas.

### <a name="distribution-by-campaign"></a>**Distribuição por campanha**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Assim como examinamos os domínios de seus clientes, esse gráfico permite que você veja a distribuição de seus dados por campanha que os usuários estão chegando ao Marketplace em. \_Nocampaign significa que o cliente não tinha uma ID de campanha na URL quando navegou para o Marketplace.

<a name="next-steps"></a>**Passos Seguintes**
--------------

Agora que você tem a capacidade de acompanhar o sucesso de suas ofertas, queremos incentivar você a criar suas próprias campanhas.

Se você tiver perguntas/solicitações de recursos, compartilhe-as por meio de comentários, localizadas no canto superior direito.

![Comentários em Portal do Cloud Partner](./media/si-getting-started/WebAnalytics5.png)
