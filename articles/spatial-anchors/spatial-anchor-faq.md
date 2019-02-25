---
title: Perguntas mais frequentes sobre as âncoras espaciais do Azure | Documentos da Microsoft
description: Âncoras espaciais do Azure é uma plataforma de serviço e programadores de cloud gerido que permite entre dispositivos, experiências de realidade mista, de vários utilizador em dispositivos Android, iOS e HoloLens. Essas perguntas de endereço de perguntas frequentes sobre o serviço do ponto de vista técnico.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: d38f7a22d90c62b245e49e6bdb0a3041c4f1023f
ms.sourcegitcommit: 68ba7916a6ff8dd40f5037e57059733d74374954
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753598"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Perguntas mais frequentes sobre as âncoras espaciais do Azure

Âncoras espaciais do Azure é um serviço cloud gerido e experiências de plataforma de desenvolvedor que permite a realidade mista spatially com para o reconhecimento de vários utilizador em dispositivos Android, iOS e HoloLens.

Para obter mais informações, consulte [descrição geral de âncoras de geográficos Azure](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>FAQs de produto do Azure âncoras espaciais

**P: Os dispositivos que suportam as âncoras espaciais do Azure?**

**R:** Âncoras espaciais do Azure permite aos programadores criar aplicações no HoloLens, em dispositivos iOS com o suporte de ARKit, e no Android suportam dispositivos com ARCore; para iOS e Android inclui os telemóveis e tablets.

**P: É necessário estar conectado a cloud para utilizar as âncoras espaciais do Azure?**

**R:** Âncoras espaciais do Azure atualmente requer uma ligação de rede para a internet. Agradecemos seus comentários no nosso [site de comentários](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**P: Quais são os requisitos de conectividade para as âncoras espaciais do Azure?**

**R:** Âncoras espaciais do Azure funciona com Wi-Fi e conexões de banda larga móveis.

**P: Como com precisão o âncoras espaciais do Azure pode localizar as âncoras?**

**R:** Muitos fatores afetam a precisão de localizar as âncoras – condições de iluminação, os objetos no ambiente e até mesmo a superfície em que a âncora é colocada. Para determinar se a precisão satisfazem as suas necessidades, experimente as âncoras de representante de ambientes de onde pretende usá-las. Se tiver ambientes em que a precisão não é satisfazendo as necessidades, consulte [registo e diagnóstico no Azure âncoras de geográficos](./concepts/logging-diagnostics.md).

**P: Quanto tempo demora a criar e localizar as âncoras?**

**R:** O tempo necessário para criar e localizar as âncoras é dependente de muitos fatores - ligação de rede, processamento do dispositivo e de carga e o ambiente específico. Temos clientes que criam aplicativos em várias indústrias, incluindo fabrico, varejo e jogos que indica que o serviço permite uma experiência de utilizador fantástica para seus cenários.

## <a name="privacy-faq"></a>FAQ de privacidade

**P: Quando meu aplicativo coloca uma âncora de geográficos em algum lugar todas as aplicações têm acesso a ele?**

**R:** As âncoras são isoladas por conta do Azure. Apenas aplicações aos quais pode conceder acesso à sua conta será capazes de acessar as âncoras dentro da conta.

**P: Quais informações sobre um ambiente são transmitidas e armazenadas no serviço ao utilizar as âncoras espaciais do Azure? Imagens do ambiente são transmitidas e armazenadas?**

**A**: Ao criar ou localizar âncoras, imagens de ambiente são processadas no dispositivo num formato derivado. Este formato derivado é transmitido para e armazenado no serviço.

Para fornecer transparência, segue-se uma imagem de um ambiente e a cloud de ponto dispersas derivada. A cloud de ponto mostra a representação geométrica do ambiente que tem transmitidos e armazenados no serviço. Para cada ponto na cloud ponto dispersa, transmitir e armazenar um hash das características visual desse ponto. O hash é derivado de, mas não contém, quaisquer dados de pixel.

Azure âncoras geográficos respeita os [termos de contrato de serviço do Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)e o [declaração de privacidade do Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Um ambiente e sua cloud ponto dispersas derivada](./media/sparce-point-cloud.png)
*figura 1: Um ambiente e sua cloud ponto dispersas derivada*


**P: Existe uma forma que posso enviar informações de diagnóstico para a Microsoft?**

**A**: Sim. Âncoras espaciais do Azure tem um modo de diagnóstico que os desenvolvedores podem optar por optam por meio da API de âncoras espaciais do Azure. Isso é útil, por exemplo, se tiver um ambiente onde está não é possível criar e localizar as âncoras de forma previsível. Poderemos solicitar se pode submeter um relatório de diagnóstico que contenham informações que ajudam-na depurar. Para obter mais informações, consulte [registo e diagnóstico no Azure âncoras de geográficos](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Disponibilidade e FAQs de preços

**P: É fornecido um SLA?**

**R:** Como é o padrão para serviços do Azure, podemos mais de 99,9% de disponibilidade de destino. Tenha em atenção que as âncoras espaciais do Azure está atualmente em pré-visualização e assim a [termos complementares da pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) aplicam-se.

**P: Pode publicar as minhas aplicações utilizando as âncoras espaciais do Azure para lojas de aplicações? Pode utilizar as âncoras espaciais do Azure para cenários de produção de missão crítica?**

**R:** Âncoras espaciais do Azure está atualmente em pré-visualização e durante este tempo, convidamos a desenvolver aplicações, [fornecer comentários](https://feedback.azure.com/forums/919252-azure-spatial-anchors) sobre o produto e o plano para as implementações de produção.

Datas de disponibilidade (GA) geral serão anunciadas em breve.

**P: Tem limites de limitação no local?**
 
**A**: Sim, temos a limitação de limites.  Não esperamos, irá atingir o-los para aplicativo típico de desenvolvimento e teste. Para implementações de produção, estamos prontos para suportar os requisitos de grande escala dos nossos clientes. [Contacte-nos](mailto:azuremrs@microsoft.com) para discutir. Durante esta fase de pré-visualização, não ainda publicamos nosso disposição em camadas e a estrutura de preços, mas Esperamos fazê-lo em breve.

**P: Em que regiões estão âncoras espaciais do Azure disponíveis?**

**R:** Pode criar uma conta de âncoras espaciais do Azure hoje mesmo na região do Azure Leste E.u.a. 2. Isso significa é que ambos de computação e armazenamento que alimenta este serviço está nesta região. Dito isso, não há restrições sobre onde se encontram os seus clientes. No futuro, posteriormente, iremos abordar disponibilidade regional do serviço para todas as regiões do Azure principal.

**P: É cobrado para âncoras espaciais do Azure? Nunca cobrar?**

**R:** Pode encontrar detalhes sobre os preços durante a pré-visualização em nosso [página de preços](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Perguntas técnicas frequentes

**P: Como funciona a âncoras espaciais do Azure?**

**R:** Azure âncoras geográficos depende de realidade mista / aumentados monitores de realidade. Estes controladores poderá ver o ambiente com câmeras e controlam o dispositivo em 6 graus-de liberdade (6DoF) pois ela se move através do espaço.

Devido um controlador de 6DoF como um bloco de construção, as âncoras espaciais do Azure permite-lhe designar certos pontos de interesse no seu ambiente real, como pontos de "âncora". Por exemplo, pode, usar uma âncora para processar conteúdo num local específico do mundo real.

Quando cria uma âncora, o SDK do cliente recolhe informações de ambiente em torno desse ponto e transmite-a para o serviço. Se outro dispositivo procura a âncora nesse mesmo espaço e transmite dados semelhantes ao serviço. Esses dados são comparados com os dados de ambiente anteriormente armazenados. A posição da âncora em relação ao dispositivo, em seguida, é enviada novamente para uso no aplicativo.

**P: Como âncoras geográficos de Azure integrar ARKit e ARCore em iOS e Android?**

**R:** Âncoras espaciais do Azure tira partido das capacidades controlo nativo de ARKit e ARCore. Além disso, nossos SDKs para iOS e Android oferecem capacidades, como persistência âncoras num serviço gerido na cloud e permitindo que as suas aplicações para localizar esses âncoras novamente ao simplesmente ligar ao serviço.

**P: Como o âncoras espaciais do Azure integrar com o HoloLens?**

**R:** Azure âncoras geográficos aproveita os recursos de controle nativo do HoloLens. Fornecemos um SDK de âncoras espaciais do Azure para criar aplicações no HoloLens. O SDK integra-se com os recursos nativos do HoloLens e fornece capacidades adicionais. Esses recursos incluem o que permite aos programadores de aplicações manter as âncoras de um serviço gerido na cloud e permitindo que as suas aplicações para localizar esses âncoras novamente ao ligar ao serviço.

**P: Que plataformas e idiomas oferece suporte a âncoras espaciais do Azure?**

**R:** Os programadores podem criar aplicações com as âncoras espaciais do Azure com ferramentas familiares e estruturas para o respetivo dispositivo:

- Unity em HoloLens, iOS e Android
- SWIFT ou Objective-C no iOS
- Java ou o NDK Android em Android
- C++ /CLI WinRT no HoloLens

Introdução ao [desenvolvimento aqui](index.yml).

**P: Funciona com Unreal?**

**R:** Esperamos ter suporte para Unreal em breve.

**P: Funciona com o Xamarin?**

**R:** Sim. Enquanto não fornecemos um SDK Xamarin, esperamos que os desenvolvedores podem usar âncoras espaciais do Azure nas suas aplicações Xamarin com a integração com a API de âncoras espaciais do Azure.
