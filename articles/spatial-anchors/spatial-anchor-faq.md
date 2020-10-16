---
title: Perguntas mais frequentes
description: Perguntas frequentes sobre o serviço Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 05/18/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: eb02de25cfcb0dde01699c1169d03f0d822c4ae2
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097410"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Perguntas frequentes sobre âncoras espaciais Azure

Azure Spatial Anchors é uma plataforma gerida de serviços em nuvem e desenvolvedores que permite experiências de realidade mista multiutilizador, espacialmente conscientes em holoLens, iOS e dispositivos Android.

Para mais informações, consulte [a visão geral das Âncoras Espaciais Azure](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Azure Spatial Anchors FaQs de produto

**P: Que dispositivos suporta a Azure Spatial Anchors?**

**A:** O Azure Spatial Anchors permite que os desenvolvedores construam aplicações em HoloLens, em dispositivos iOS com suporte ARKit e em dispositivos Android com suporte ARCore; para iOS e Android isto inclui tanto telefones como tablets.

**P: Tenho de estar ligado à nuvem para usar âncoras espaciais Azure?**

**A:** A Azure Spatial Anchors requer atualmente uma ligação de rede à internet. Congratulamo-nos com os seus comentários no nosso [site de comentários.](https://feedback.azure.com/forums/919252-azure-spatial-anchors)

**P: Quais são os requisitos de conectividade para âncoras espaciais Azure?**

**A:** A Azure Spatial Anchors trabalha com ligações de banda larga Wi-Fi e móveis.

**P: Com que precisão as âncoras espaciais de Azure podem localizar âncoras?**

**A:** Muitos fatores afetam a precisão da localização das âncoras-- condições de iluminação, os objetos no ambiente e até mesmo a superfície em que a âncora é colocada. Para determinar se a precisão irá satisfazer as suas necessidades, experimente as âncoras em ambientes representativos de onde planeia usá-las. Se encontrar ambientes onde a precisão não satisfaz as suas [necessidades, consulte o Registo e os diagnósticos em Âncoras Espaciais Azure](./concepts/logging-diagnostics.md).

**P: Quanto tempo demora a criar e localizar âncoras?**

**A:** O tempo necessário para criar e localizar âncoras depende de muitos factores-- ligação de rede, processamento e carga do dispositivo, e ambiente específico. Temos clientes a construir aplicações em muitas indústrias, incluindo fabricação, retalho e jogos, indicando que o serviço permite uma grande experiência de utilizador para os seus cenários.

## <a name="privacy-faq"></a>FAQ de privacidade

**P: Quando a minha aplicação coloca uma Âncora Espacial em algum lugar todas as aplicações têm acesso a ela?**

**A:** As âncoras são isoladas pela conta Azure. Apenas as aplicações às quais concede acesso à sua conta poderão aceder às âncoras dentro da conta.

**P: Como é que a Azure Spatial Anchors armazena dados?**

**A:** Todos os dados são armazenados encriptados com uma chave de encriptação de dados gerida pela Microsoft e todos os dados são armazenados regionalmente para cada um dos recursos.

**P: Que informação sobre um ambiente é transmitida e armazenada no serviço quando se utiliza âncoras espaciais Azure? As imagens do ambiente são transmitidas e armazenadas?**

**R**: Ao criar ou localizar âncoras, as imagens do ambiente são processadas no dispositivo num formato derivado. Este formato derivado é transmitido e armazenado no serviço.

Para proporcionar transparência, abaixo está uma imagem de um ambiente e a nuvem de ponto escasso derivada. A nuvem de ponto mostra a representação geométrica do ambiente que é transmitido e armazenado no serviço. Para cada ponto na nuvem de ponto escasso, transmitimos e armazenamos um haxixe das características visuais desse ponto. O haxixe é derivado, mas não contém, quaisquer dados de pixel.

A Azure Spatial Anchors adere aos Termos do [Acordo de Serviço Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)e à [Declaração de Privacidade](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409)da Microsoft .

![Um ambiente e a sua nuvem de ponto escasso derivada ](./media/sparse-point-cloud.png)
 *Figura 1: Um ambiente e sua nuvem de ponto escasso derivada*

**P: Há alguma forma de enviar informações de diagnóstico para a Microsoft?**

**A:** Sim. A Azure Spatial Anchors tem um modo de diagnóstico que os desenvolvedores podem optar por optar através da Azure Spatial Anchors API. Isto é útil, por exemplo, se encontrar um ambiente onde não é capaz de criar e localizar âncoras previsivelmente. Podemos perguntar se pode apresentar um relatório de diagnóstico contendo informações que nos ajudem a depurar. Para obter mais informações consulte [Registo e diagnósticos em Âncoras Espaciais Azure](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Disponibilidade e Preços FAQs

**P: Fornece um SLA?**

**A:** Como é padrão para os serviços Azure, temos como alvo uma disponibilidade superior a 99,9%. 

**P: Posso publicar as minhas apps usando âncoras espaciais Azure para lojas de aplicações? Posso usar âncoras espaciais Azure para cenários críticos de produção da missão?**

**A:** Sim, a Azure Spatial Anchors está geralmente disponível e tem um SLA de serviços Azure padrão. Convidamo-lo a desenvolver aplicativos para as suas implementações de produção e [a partilhar connosco](https://feedback.azure.com/forums/919252-azure-spatial-anchors) o seu feedback sobre o produto.

**P: Tem algum limite de estrangulamento no lugar?**

**R:** Sim, temos limites de estrangulamento.  Não esperamos que os atinja para o desenvolvimento e testes típicos de aplicações. Para implantações de produção, estamos prontos para apoiar os requisitos de alta escala dos nossos clientes. [Contacte-nos](mailto:azuremrs@microsoft.com) para discutir. 

**P: Em que regiões está disponível a Azure Spatial Anchors?**

**A:** Azure Spatial Anchors está atualmente disponível em West US 2, East US, East US 2, South Central US, West Europe, North Europe, UK South e Australia East. No futuro, estarão disponíveis regiões adicionais.

O que isto significa é que tanto o computação como o armazenamento que alimentam este serviço estão nestas regiões. Dito isto, não existem restrições sobre onde os seus clientes estão localizados. 

**P: Cobra por Âncoras Espaciais Azure?**

**A:** Pode encontrar detalhes sobre preços na nossa [página de preços.](https://azure.microsoft.com/pricing/details/spatial-anchors/)

## <a name="technical-faqs"></a>Perguntas frequentes técnicas

**P: Como funciona a Azure Spatial Anchors?**

**A:** As Âncoras Espaciais Azure dependem da realidade mista / rastreadores de realidade aumentada. Estes localizadores percebem o ambiente com câmaras e rastreiam o dispositivo em 6 graus de liberdade (6DoF) à medida que se movem pelo espaço.

Dado um rastreador de 6DoF como um bloco de construção, as Âncoras Espaciais Azure permitem-lhe designar certos pontos de interesse no seu ambiente real como pontos de "âncora". Pode, por exemplo, usar uma âncora para renderizar conteúdo num lugar específico no mundo real.

Quando cria uma âncora, o cliente SDK capta informações ambientais em torno desse ponto e transmite-as para o serviço. Se outro dispositivo procurar a âncora nesse mesmo espaço, dados semelhantes transmitem para o serviço. Estes dados são compatíveis com os dados ambientais anteriormente armazenados. A posição da âncora relativa ao dispositivo é então enviada para utilização na aplicação.

**P: Como é que o Azure Spatial Anchors se integra com ARKit e ARCore no iOS e Android?**

**A:** A Azure Spatial Anchors aproveita as capacidades de rastreamento nativas de ARKit e ARCore. Além disso, os nossos SDKs para iOS e Android oferecem capacidades como âncoras persistentes num serviço de cloud gerido, e permite que as suas aplicações voltem a localizar essas âncoras, limitando-se a ligar-se ao serviço.

**P: Como é que as Âncoras Espaciais Azure se integram com hololens?**

**A:** Azure Spatial Anchors aproveita as capacidades nativas de rastreamento dos HoloLens. Fornecemos um Azure Spatial Anchors SDK para a construção de aplicativos em HoloLens. O SDK integra-se com as capacidades nativas do HoloLens e fornece capacidades adicionais. Estas capacidades incluem permitir que os desenvolvedores de aplicações persistam âncoras num serviço de nuvem gerido e permitir que as suas apps voltem a localizar essas âncoras ligando-se ao serviço.

**P: Quais plataformas e idiomas suportam âncoras espaciais Azure?**

**A:** Os desenvolvedores podem construir aplicativos com âncoras espaciais Azure usando ferramentas e estruturas familiares para o seu dispositivo:

- Unidade em HoloLens, iOS e Android
- Xamarin no iOS e Android
- Swift ou Objective-C no iOS
- Java ou o Android NDK no Android
- C++/WinRT em HoloLens

Começa com [o desenvolvimento aqui.](index.yml)

**P: Funciona com o Irreal?**

**A:** O apoio à Irreal será considerado no futuro.

**P: Que portas e protocolos utiliza a Azure Spatial Anchors?**

**A:** A azure Spatial Anchors comunica através da porta TCP 443 usando um protocolo encriptado. Para autenticação, utiliza [o Azure Ative Directory,](../active-directory/index.yml)que comunica utilizando https sobre a porta 443.