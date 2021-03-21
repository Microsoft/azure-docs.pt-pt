---
title: Visão geral da prestação
description: Introdução da utilização do Azure para renderização e uma visão geral das capacidades de renderização do Azure Batch
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98232119"
---
# <a name="rendering-using-azure"></a>Composição com o Azure

Renderização é o processo de pegar em modelos 3D e convertê-los em imagens 2D. Os ficheiros de cena 3D são da autoria de aplicações como Autodesk 3ds Max, Autodesk Maya e Blender.  Aplicações de renderização como Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray e Blender Cycles produzem imagens 2D.  Às vezes, imagens individuais são criadas a partir dos ficheiros de cena. No entanto, é comum modelar e renderizar múltiplas imagens, e depois combiná-las numa animação.

A carga de trabalho de renderização é fortemente utilizada para efeitos especiais (VFX) na indústria de media e entretenimento. A composição também é utilizada em muitos outros setores, como publicidade, retalho, petróleo e gás e fabrico.

O processo de renderização é computacionalmente intensivo; pode haver muitos quadros/imagens para produzir e cada imagem pode demorar muitas horas a renderizar.  A renderização é, portanto, uma carga de trabalho perfeita de processamento de lote que pode aproveitar a Azure para executar muitos renders em paralelo e utilizar uma ampla gama de hardware, incluindo GPUs.

## <a name="why-use-azure-for-rendering"></a>Por que usar Azure para renderização?

Por muitas razões, a renderização é uma carga de trabalho perfeitamente adequada para a Azure:

* Os trabalhos de renderização podem ser divididos em muitas peças que podem ser executadas em paralelo usando vários VMs:
  * As animações consistem em muitos quadros e cada quadro pode ser renderizado em paralelo.  Quanto mais VMs disponíveis para processar cada quadro, mais rápido todos os quadros e a animação podem ser produzidos.
  * Alguns softwares de renderização permitem que quadros individuais sejam divididos em várias peças, tais como azulejos ou fatias.  Cada peça pode ser renderizada separadamente e, em seguida, combinada na imagem final quando todas as peças estiverem terminadas.  Quanto mais VMs estiverem disponíveis, mais rápido uma moldura pode ser renderizada.
* Os projetos de renderização podem exigir uma escala enorme:
  * Os quadros individuais podem ser complexos e requerem muitas horas para renderizar, mesmo em hardware de alta qualidade; animações podem consistir em centenas de milhares de quadros.  Uma enorme quantidade de cálculo é necessária para renderizar animações de alta qualidade em um período de tempo razoável.  Em alguns casos, mais de 100.000 núcleos foram usados para tornar milhares de quadros em paralelo.
* Os projetos de renderização são baseados em projetos e exigem quantidades variadas de cálculo:
  * Aloque a capacidade de cálculo e armazenamento quando necessário, dimensione-a para cima ou para baixo de acordo com a carga durante um projeto, e remova-a quando um projeto estiver concluído.
  * Pague a capacidade quando é atribuída, mas não pague quando não há carga, como entre projetos.
  * Atender a rajadas devido a alterações inesperadas; escala mais alta se houver mudanças inesperadas no final de um projeto e essas alterações precisam ser processadas em um horário apertado.
* Escolha entre uma ampla seleção de hardware de acordo com a aplicação, carga de trabalho e calendário:
  * Há uma grande seleção de hardware disponível no Azure que pode ser alocado e gerido com Batch.
  * Dependendo do projeto, o requisito pode ser para o melhor preço/desempenho ou o melhor desempenho geral.  Diferentes cenas e/ou aplicações de renderização terão diferentes requisitos de memória.  Algumas aplicações de renderização podem alavancar as GPUs para o melhor desempenho ou certas funcionalidades. 
* Os VM de baixa prioridade ou spot reduzem os [custos:](https://azure.microsoft.com/pricing/spot/)
  * Os VM de baixa prioridade e spot estão disponíveis para um grande desconto em comparação com os VM padrão e são adequados para alguns tipos de trabalho.
  
## <a name="existing-on-premises-rendering-environment"></a>Ambiente de transformação existente no local

O caso mais comum é que exista uma exploração de renderização existente no local sendo gerida por uma aplicação de gestão de renderização, como PipelineFX Qube, Royal Render, Thinkbox Deadline ou uma aplicação personalizada.  A exigência consiste em alargar a capacidade agrícola utilizando VMs Azure.

As infraestruturas e serviços azure são utilizados para criar um ambiente híbrido onde o Azure é utilizado para complementar a capacidade no local. Por exemplo:

* Utilize uma [Rede Virtual](../virtual-network/virtual-networks-overview.md) para colocar os recursos Azure na mesma rede que a exploração de renderização no local.
* Utilize [Avere vFXT para Azure](../avere-vfxt/avere-vfxt-overview.md) ou [Azure HPC Cache](../hpc-cache/hpc-cache-overview.md) para cache ficheiros de origem em Azure para reduzir o uso e latência da largura de banda, maximizando o desempenho.
* Certifique-se de que o servidor de licenças existente está na rede virtual e compre as licenças adicionais necessárias para atender à capacidade extra baseada em Azure.

## <a name="no-existing-render-farm"></a>Nenhuma fazenda de renderização existente

As estações de trabalho dos clientes podem estar a realizar renderização, mas a carga de renderização está a aumentar e está a demorar demasiado tempo a utilizar exclusivamente a capacidade de estação de trabalho.

Existem duas opções principais disponíveis:

* Implementar um gestor de renderização no local, como o Royal Render, e configurar um ambiente híbrido para usar o Azure quando for necessária mais capacidade ou desempenho. Um gestor de renderização é especificamente adaptado para renderizar cargas de trabalho e incluirá plug-ins para as aplicações populares do cliente, permitindo a fácil submissão de empregos de renderização.

* Uma solução personalizada usando o Azure Batch para alocar e gerir a capacidade de computação, bem como fornecer o agendamento de trabalho para executar os trabalhos de renderização.

## <a name="next-steps"></a>Passos seguintes

 Saiba como [utilizar infraestruturas e serviços da Azure para alargar uma exploração agrícola](https://azure.microsoft.com/solutions/high-performance-computing/rendering/)existente no local.

Saiba mais sobre [as capacidades de renderização do Azure Batch](batch-rendering-functionality.md).
