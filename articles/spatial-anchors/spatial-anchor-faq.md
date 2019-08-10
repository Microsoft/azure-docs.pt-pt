---
title: Perguntas frequentes sobre âncoras espaciais do Azure | Microsoft Docs
description: As âncoras espaciais do Azure são um serviço de nuvem gerenciado e uma plataforma de desenvolvedor que permite experiências de realidade mista entre dispositivos, multiusuários e de vários usuários em dispositivos de HoloLens, iOS e Android. Essas perguntas frequentes abordam perguntas sobre o serviço a partir de um ponto de vista técnico.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 06936a196813dacfc1fc9d02945bee4119b7eea8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927501"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Perguntas mais frequentes acerca do Azure Spatial Anchors

As âncoras espaciais do Azure são um serviço de nuvem gerenciado e uma plataforma de desenvolvedor que permite experiências de realidade mista de vários usuários, com reconhecimento espacial, em dispositivos de HoloLens, iOS e Android.

Para obter mais informações, consulte [visão geral das âncoras espaciais do Azure](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Perguntas frequentes sobre o produto da âncora espacial do Azure

**P: A quais dispositivos as âncoras espaciais do Azure dão suporte?**

**R:** As âncoras espaciais do Azure permitem que os desenvolvedores criem aplicativos no HoloLens, em dispositivos iOS com suporte a ARKit e em dispositivos Android com suporte a ARCore; para iOS e Android, isso inclui telefones e tablets.

**P: Preciso estar conectado à nuvem para usar as âncoras espaciais do Azure?**

**R:** As âncoras espaciais do Azure atualmente exigem uma conexão de rede com a Internet. Agradecemos seus comentários sobre nosso [site de comentários](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**P: Quais são os requisitos de conectividade para âncoras espaciais do Azure?**

**R:** As âncoras espaciais do Azure funcionam com conexões Wi-Fi e de banda larga móvel.

**P: Com que precisão as âncoras espaciais do Azure podem localizar âncoras?**

**R:** Muitos fatores afetam a precisão da localização de âncoras – condições de iluminação, os objetos no ambiente e até mesmo a superfície na qual a âncora é colocada. Para determinar se a exatidão atenderá às suas necessidades, experimente as âncoras no representante de ambientes de onde você planeja usá-las. Se você encontrar ambientes em que a precisão não atende às suas necessidades, consulte [registro em log e diagnóstico em âncoras espaciais do Azure](./concepts/logging-diagnostics.md).

**P: Quanto tempo leva para criar e localizar âncoras?**

**R:** O tempo necessário para criar e localizar âncoras depende de muitos fatores, da conexão de rede, do processamento e da carga do dispositivo e do ambiente específico. Temos clientes criando aplicativos em muitos setores, incluindo manufatura, varejo e jogos, indicando que o serviço permite uma excelente experiência de usuário para seus cenários.

## <a name="privacy-faq"></a>Perguntas frequentes sobre privacidade

**P: Quando meu aplicativo coloca uma âncora espacial em algum lugar, todos os aplicativos têm acesso a ele?**

**R:** As âncoras são isoladas pela conta do Azure. Somente os aplicativos aos quais você concede acesso à sua conta poderão acessar âncoras dentro da conta.

**P: Quais informações sobre um ambiente são transmitidas e armazenadas no serviço ao usar âncoras espaciais do Azure? As imagens do ambiente são transmitidas e armazenadas?**

**A**: Ao criar ou localizar âncoras, as imagens do ambiente são processadas no dispositivo em um formato derivado. Esse formato derivado é transmitido para e armazenado no serviço.

Para fornecer transparência, abaixo está uma imagem de um ambiente e a nuvem de pontos esparsos derivados. A nuvem do ponto mostra a representação geométrica do ambiente transmitido e armazenado no serviço. Para cada ponto na nuvem de ponto esparso, transmitimos e armazenamos um hash das características visuais desse ponto. O hash é derivado de, mas não contém, nenhum dado de pixel.

As âncoras espaciais do Azure seguem os [termos do contrato de serviço do Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)e a [política de privacidade da Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Um ambiente e sua nuvem](./media/sparse-point-cloud.png)
*de pontos esparsos derivados Figura 1: Um ambiente e sua nuvem de pontos esparsos derivados*


**P: É possível enviar informações de diagnóstico para a Microsoft?**

**A**: Sim. As âncoras espaciais do Azure têm um modo de diagnóstico que os desenvolvedores podem optar por aceitar por meio da API de âncoras espaciais do Azure. Isso é útil, por exemplo, se você encontrar um ambiente no qual não é possível criar e localizar âncoras de forma previsível. Poderemos perguntar se você pode enviar um relatório de diagnóstico contendo informações que nos ajudam a depurar. Para obter mais informações, consulte [registro em log e diagnóstico em âncoras espaciais do Azure](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Perguntas frequentes sobre disponibilidade e preços

**P: Você fornece um SLA?**

**R:** Como é o padrão para os serviços do Azure, visamos uma disponibilidade maior que 99,9%. Observe que as âncoras espaciais do Azure estão atualmente em visualização e, assim, os [termos suplementares de visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se aplicam.

**P: Posso publicar meus aplicativos usando âncoras espaciais do Azure para lojas de aplicativos? Posso usar âncoras espaciais do Azure para cenários de produção de missão crítica?**

**R:** As âncoras espaciais do Azure estão atualmente em visualização e, durante esse tempo, convidamos você a desenvolver aplicativos, [fornecer comentários](https://feedback.azure.com/forums/919252-azure-spatial-anchors) sobre o produto e planejar suas implantações de produção.

As datas de disponibilidade geral (GA) serão anunciadas em breve.

**P: Você tem algum limite de limitação em vigor?**

**A**: Sim, temos limites de limitação.  Não esperamos que você os acessará para desenvolvimento e teste de aplicativos típicos. Para implantações de produção, estamos prontos para dar suporte aos requisitos de grande escala dos nossos clientes. [Entre em contato conosco](mailto:azuremrs@microsoft.com) para discutir. Durante essa fase de visualização, ainda não publicamos nossa estrutura de camadas e preços, mas esperamos fazer isso em breve.

**P: Em quais regiões as âncoras espaciais do Azure estão disponíveis?**

**R:** Você pode criar uma conta de âncoras espaciais do Azure hoje na região leste dos EUA 2 do Azure. Isso significa que a computação e o armazenamento que capacitam esse serviço estão nessa região. Dito isso, não há restrições de onde os clientes estão localizados. No futuro, expandiremos a disponibilidade regional do serviço para todas as regiões primárias do Azure.

**P: Existe algum custo associado ao Azure Spatial Anchors? Você já será cobrado?**

**R:** Você pode encontrar detalhes sobre os preços durante a visualização em nossa [página de preços](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Perguntas técnicas frequentes

**P: Como as âncoras espaciais do Azure funcionam?**

**R:** As âncoras espaciais do Azure dependem de realidade misturada/roteiros de realidade aumentada. Esses rastreadores percebem o ambiente com câmeras e acompanham o dispositivo em 6 graus de liberdade (6DoF) à medida que se movem pelo espaço.

Dado um controlador 6DoF como um bloco de construção, as âncoras espaciais do Azure permitem designar determinados pontos de interesse em seu ambiente real como pontos de "ancoragem". Você pode, por exemplo, usar uma âncora para renderizar o conteúdo em um local específico no mundo real.

Quando você cria uma âncora, o SDK do cliente captura informações de ambiente nesse ponto e transmite-as para o serviço. Se outro dispositivo procurar a âncora no mesmo espaço, os dados semelhantes são transmitidos para o serviço. Esses dados são correspondidos em relação aos dados de ambiente armazenados anteriormente. A posição da âncora relativa ao dispositivo é enviada de volta para uso no aplicativo.

**P: Como as âncoras espaciais do Azure se integram ao ARKit e ao ARCore no iOS e no Android?**

**R:** As âncoras espaciais do Azure aproveitam os recursos de rastreamento nativo de ARKit e ARCore. Além disso, nossos SDKs para iOS e Android oferecem recursos como as âncoras persistentes em um serviço de nuvem gerenciado e permitem que seus aplicativos localizem essas âncoras novamente simplesmente conectando-se ao serviço.

**P: Como as âncoras espaciais do Azure se integram ao HoloLens?**

**R:** As âncoras espaciais do Azure aproveitam os recursos de acompanhamento nativo do HoloLens. Fornecemos um SDK de âncoras espaciais do Azure para criar aplicativos no HoloLens. O SDK integra-se com os recursos nativos do HoloLens e fornece recursos adicionais. Esses recursos incluem permitir que os desenvolvedores de aplicativos persistam âncoras em um serviço de nuvem gerenciado e permitindo que seus aplicativos localizem essas âncoras novamente conectando-se ao serviço.

**P: A quais plataformas e idiomas há suporte para âncoras espaciais do Azure?**

**R:** Os desenvolvedores podem criar aplicativos com âncoras espaciais do Azure usando ferramentas e estruturas familiares para seus dispositivos:

- Unity entre o HoloLens, o iOS e o Android
- Swift ou Objective-C no iOS
- Java ou Android NDK no Android
- C++/WinRT no HoloLens

Comece a usar o [desenvolvimento aqui](index.yml).

**P: Ele funciona com um inreal?**

**R:** O suporte para inreal será considerado no futuro.

**P: Quais portas e protocolos as âncoras espaciais do Azure usam?**

**R:** As âncoras espaciais do Azure se comunicam pela porta TCP 443 usando um protocolo criptografado. Para autenticação, ele usa [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/), que se comunica usando HTTPS pela porta 443.
