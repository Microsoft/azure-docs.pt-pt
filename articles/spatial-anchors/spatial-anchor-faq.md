---
title: Perguntas mais frequentes
description: Perguntas frequentes sobre o serviço De Âncoras Espaciais Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 563e2da8eba228636b05db2112739fdead4a4aa3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76844892"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Perguntas frequentes sobre âncoras espaciais Azure

O Azure Spatial Anchors é uma plataforma de serviço sinuoso gerido em nuvem e plataforma de desenvolvimento que permite experiências de realidade mista multiutilizador, conscientes espacialmente em dispositivos HoloLens, iOS e Android.

Para mais informações, consulte a visão geral das [Âncoras Espaciais Azure](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>FaQs de produtos de âncora espacial azure

**P: Que dispositivos suportam as Âncoras Espaciais Azure?**

**A:** O Azure Spatial Anchors permite que os desenvolvedores construam aplicações em HoloLens, em dispositivos iOS com suporte ARKit e em dispositivos Android com suporte ARCore; para iOS e Android isto inclui tanto telefones como tablets.

**P: Tenho de estar ligado à nuvem para usar âncoras espaciais Azure?**

**A:** Atualmente, a Azure Spatial Anchors requer uma ligação de rede à internet. Congratulamo-nos com os seus comentários no nosso site de [comentários.](https://feedback.azure.com/forums/919252-azure-spatial-anchors)

**P: Quais são os requisitos de conectividade para âncoras espaciais Azure?**

**A:** As Âncoras Espaciais Azure funcionam com ligações Wi-Fi e banda larga móvel.

**P: Com precisão as âncoras espaciais azure podem localizar âncoras?**

**A:** Muitos fatores afetam a precisão da localização das âncoras- condições de iluminação, os objetos no ambiente e até mesmo a superfície em que a âncora é colocada. Para determinar se a precisão vai atender às suas necessidades, experimente as âncoras em ambientes representativos de onde planeia usá-las. Se encontrar ambientes onde a precisão não satisfaz as suas necessidades, consulte [a exploração madeireira e os diagnósticos em Âncoras Espaciais Azure](./concepts/logging-diagnostics.md).

**P: Quanto tempo demora a criar e localizar âncoras?**

**A:** O tempo necessário para criar e localizar âncoras depende de muitos factores-- ligação de rede, processamento e carga do dispositivo, e o ambiente específico. Temos clientes a construir aplicações em muitas indústrias, incluindo fabricação, retalho e jogos, indicando que o serviço permite uma grande experiência de utilizador para os seus cenários.

## <a name="privacy-faq"></a>FAQ de privacidade

**P: Quando a minha aplicação coloca uma Âncora Espacial em algum lugar todas as aplicações têm acesso a ela?**

**A:** As âncoras são isoladas pela conta Azure. Apenas as aplicações às quais concede acesso à sua conta poderão aceder a âncoras dentro da conta.

**P: Que informações sobre um ambiente são transmitidas e armazenadas no serviço quando se utilizam âncoras espaciais Azure? As imagens do ambiente são transmitidas e armazenadas?**

**R**: Ao criar ou localizar âncoras, as imagens do ambiente são processadas no dispositivo num formato derivado. Este formato derivado é transmitido e armazenado no serviço.

Para proporcionar transparência, abaixo está uma imagem de um ambiente e a nuvem de ponto escasso derivada. A nuvem de ponto mostra a representação geométrica do ambiente que é transmitida e armazenada no serviço. Para cada ponto na nuvem de ponto escasso, transmitimos e armazenamos um hash das características visuais desse ponto. O hash é derivado, mas não contém, quaisquer dados de pixel.

A Azure Spatial Anchors adere aos Termos do Contrato de [Serviço Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)e à Declaração de Privacidade da [Microsoft.](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409)

![Um ambiente e a sua nuvem](./media/sparse-point-cloud.png)
de ponto escasso derivada*Figura 1: Um ambiente e a sua nuvem de ponto escassa derivada*


**P: Existe uma maneira de enviar informações de diagnóstico para a Microsoft?**

**A:** Sim. A Azure Spatial Anchors tem um modo de diagnóstico que os desenvolvedores podem optar por optar através da API de Âncoras Espaciais Azure. Isto é útil, por exemplo, se encontrar um ambiente onde não é capaz de criar e localizar âncoras previsivelmente. Podemos perguntar se pode apresentar um relatório de diagnóstico contendo informações que nos ajudem a depurar. Para mais informações consulte [A exploração madeireira e diagnósticos em Âncoras Espaciais Azure](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>FaQs de disponibilidade e preços

**P: Fornece um SLA?**

**A:** Como é padrão para os serviços Azure, visamos uma disponibilidade superior a 99,9%. Note que as âncoras espaciais Azure estão atualmente em Pré-visualização e, como tal, aplicam-se os Termos Suplementares de [Pré-Visualização.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

**P: Posso publicar as minhas aplicações usando âncoras espaciais Azure para lojas de aplicações? Posso usar âncoras espaciais Azure para cenários de produção críticos da missão?**

**A:** A Azure Spatial Anchors está atualmente em Pré-visualização e durante este tempo convidamo-lo a desenvolver aplicações, [fornecer feedback](https://feedback.azure.com/forums/919252-azure-spatial-anchors) sobre o produto e planear as suas implementações de produção.

As datas de Disponibilidade Geral (GA) serão anunciadas em breve.

**P: Tem algum limite de estrangulamento?**

**R:** Sim, temos limites de estrangulamento.  Não esperamos que os acerte para o desenvolvimento e teste típicos de aplicações. Para implementações de produção, estamos prontos para apoiar os requisitos de alta escala dos nossos clientes. [Contacte-nos](mailto:azuremrs@microsoft.com) para discutir. Durante esta fase de Pré-visualização, ainda não publicámos a nossa estrutura de tiering e preços, mas esperamos fazê-lo em breve.

**P: Em que regiões estão disponíveis âncoras espaciais Azure?**

**A:** Você pode criar uma conta Azure Spatial Anchors hoje na região azure east US 2. O que isto significa é que tanto a computação como o armazenamento deste serviço estão nesta região. Dito isto, não existem restrições sobre onde os seus clientes estão localizados. No futuro, iremos expandir a disponibilidade regional do serviço a todas as regiões primárias do Azure.

**P: Cobra por Âncoras Espaciais Azure? Alguma vez vai cobrar?**

**A:** Pode encontrar detalhes sobre preços durante a Pré-visualização na nossa [página de preços](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Perguntas-Gerais Técnicas

**P: Como funciona a Azure Spatial Anchors?**

**A:** As Âncoras Espaciais Azure dependem da realidade mista /rastreadores de realidade aumentada. Estes rastreadores percebem o ambiente com câmaras e rastreiam o dispositivo em 6 graus de liberdade (6DoF) à medida que se move através do espaço.

Dado um localizador 6DoF como um bloco de construção, as Âncoras Espaciais Azure permitem-lhe designar certos pontos de interesse no seu ambiente real como pontos de "âncora". Pode, por exemplo, usar uma âncora para tornar o conteúdo num lugar específico no mundo real.

Quando cria uma âncora, o cliente SDK captura informações ambientais em torno desse ponto e transmite-a para o serviço. Se outro dispositivo procurar a âncora no mesmo espaço, dados semelhantes transmitem ao serviço. Estes dados são comparados com os dados ambientais anteriormente armazenados. A posição da âncora relativa ao dispositivo é então enviada para utilização na aplicação.

**P: Como é que as Âncoras Espaciais Azure se integram com arKit e ARCore no iOS e Android?**

**A:** As Âncoras Espaciais Azure aproveitam as capacidades de rastreio nativas da ARKit e arCore. Além disso, os nossos SDKs para iOS e Android oferecem capacidades como âncoras persistentes num serviço de nuvem gerido, e permitindo que as suas aplicações voltem a localizar essas âncoras, limitando-se a ligar-se ao serviço.

**P: Como é que as Âncoras Espaciais Azure se integram com hololens?**

**A:** As Âncoras Espaciais Azure aproveitam as capacidades de localização nativas dos HoloLens. Fornecemos um Azure Spatial Anchors SDK para construir aplicações em HoloLens. O SDK integra-se com as capacidades nativas dos HoloLens e fornece capacidades adicionais. Estas capacidades incluem permitir que os desenvolvedores de aplicações persistam âncoras num serviço de cloud gerido e permitir que as suas aplicações voltem a localizar essas âncoras, conectando-se ao serviço.

**P: Quais plataformas e línguas suportam as Âncoras Espaciais Azure?**

**A:** Os desenvolvedores podem construir aplicações com âncoras espaciais Azure usando ferramentas e estruturas familiares para o seu dispositivo:

- Unidade através de HoloLens, iOS e Android
- Xamarin no iOS e Android
- Swift ou Objective-C no iOS
- Java ou o Android NDK no Android
- C++/WinRT em HoloLens

Começar com o [desenvolvimento aqui.](index.yml)

**P: Funciona com irreal?**

**A:** O apoio à Irreal será considerado no futuro.

**P: Que portas e protocolos utilizam as Âncoras Espaciais Azure?**

**A:** As Âncoras Espaciais Azure comunicam através da porta TCP 443 utilizando um protocolo encriptado. Para autenticação, utiliza o [Diretório Ativo Azure,](https://docs.microsoft.com/azure/active-directory/)que comunica utilizando HTTPS sobre a porta 443.
