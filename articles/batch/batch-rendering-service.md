---
title: Visão geral da prestação
description: Introdução da utilização do Azure para renderização e uma visão geral das capacidades de renderização do Azure Batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 9fac5d3efabc5d9f796c91d688f35e01aeefdca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092767"
---
# <a name="rendering-using-azure"></a>Composição com o Azure

Renderização é o processo de pegar em modelos 3D e convertê-los em imagens 2D. Os ficheiros de cena 3D são da autoria de aplicações como Autodesk 3ds Max, Autodesk Maya e Blender.  Aplicações de renderização como Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray e Blender Cycles produzem imagens 2D.  Às vezes, imagens individuais são criadas a partir dos ficheiros de cena. No entanto, é comum modelar e renderizar múltiplas imagens, e depois combiná-las numa animação.

A carga de trabalho de renderização é fortemente utilizada para efeitos especiais (VFX) na indústria de media e entretenimento. A composição também é utilizada em muitos outros setores, como publicidade, retalho, petróleo e gás e fabrico.

O processo de renderização é computacionalmente intensivo; pode haver muitos quadros/imagens para produzir e cada imagem pode demorar muitas horas a renderizar.  A renderização é, portanto, uma carga de trabalho perfeita de processamento de lote que pode alavancar Azure e Azure Batch para executar muitos renders em paralelo.

## <a name="why-use-azure-for-rendering"></a>Por que usar Azure para renderização?

Por muitas razões, a renderização é uma carga de trabalho perfeitamente adequada para Azure e Azure Batch:

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
* Os VM de baixa prioridade reduzem os custos:
  * Os VM de baixa prioridade estão disponíveis para um grande desconto em comparação com os VM regulares a pedido e são adequados para alguns tipos de emprego.
  * Os VM de baixa prioridade podem ser atribuídos pelo Azure Batch, com o Batch a fornecer flexibilidade na forma como são utilizados para atender a um vasto conjunto de requisitos.  Os lotes podem consistir em VMs dedicados e de baixa prioridade, sendo possível alterar a mistura de tipos de VM a qualquer momento.

## <a name="options-for-rendering-on-azure"></a>Opções para renderização em Azure

Existem uma gama de capacidades Azure que podem ser usadas para renderizar cargas de trabalho.  Quais as capacidades de utilização dependem de qualquer ambiente e requisitos existentes.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Ambiente de renderização existente no local utilizando uma aplicação de gestão de renderização

O caso mais comum é que exista uma exploração de renderização existente no local sendo gerida por uma aplicação de gestão de renderização, como PipelineFX Qube, Royal Render, ou Thinkbox Deadline.  A exigência consiste em alargar a capacidade agrícola utilizando VMs Azure.

O software de gestão de renderização ou tem suporte Azure incorporado ou disponibilizamos plug-ins que adicionam suporte Azure. Para obter mais informações sobre os gestores de renderização suportados e funcionalidades ativadas, consulte o artigo sobre [a utilização de gestores de renderização](./batch-rendering-render-managers.md).

### <a name="custom-rendering-workflow"></a>Fluxo de trabalho de renderização personalizada

O requisito é que os VM aumentem uma exploração de renderização existente.  As piscinas Azure Batch podem alocar um grande número de VMs, permitir a utilização de VMs de baixa prioridade e dimensionar dinamicamente automaticamente com VMs a preço inteiro, e fornecer licenças pay-for-use para aplicações de renderização populares.

### <a name="no-existing-render-farm"></a>Nenhuma fazenda de renderização existente

As estações de trabalho dos clientes podem estar a realizar renderização, mas a carga de trabalho de renderização está a aumentar e está a demorar demasiado tempo a utilizar exclusivamente a capacidade de estação de trabalho.  O Azure Batch pode ser utilizado tanto para alocar o cálculo da exploração agrícola a pedido, como para agendar os postos de trabalho de renderização para a exploração de renderização de Azure.

## <a name="azure-batch-rendering-capabilities"></a>Capacidades de renderização do Lote Azure

O Azure Batch permite que cargas de trabalho paralelas sejam executadas em Azure.  Permite a criação e gestão de um grande número de VMs em que as aplicações são instaladas e executadas.  Também fornece capacidades abrangentes de agendamento de emprego para executar instâncias dessas aplicações, fornecendo a atribuição de tarefas a VMs, filas, monitorização de aplicações, e assim por diante.

O Azure Batch é usado para muitas cargas de trabalho, mas as seguintes capacidades estão disponíveis para tornar especificamente mais fácil e rápido executar cargas de trabalho de renderização.

* Imagens VM com gráficos pré-instalados e aplicações de renderização:
  * Estão disponíveis imagens Azure Marketplace VM que contêm gráficos populares e aplicações de renderização, evitando a necessidade de instalar as aplicações por si mesmo ou criar as suas próprias imagens personalizadas com as aplicações instaladas. 
* Licenciamento por utilização para aplicações de prestação de medidas:
  * Pode optar por pagar as aplicações ao minuto, além de pagar os VMs computacional, o que evita ter de comprar licenças e potencialmente configurar um servidor de licença para as aplicações.  Pagar para uso também significa que é possível atender a carga variada e inesperada, uma vez que não há um número fixo de licenças.
  * Também é possível utilizar as aplicações pré-instaladas com as suas próprias licenças e não utilizar o licenciamento pay-per-use. Para tal, normalmente instala-se um servidor de licença baseado no Azure ou usa uma rede virtual Azure para ligar o pool de renderização ao servidor de licenças.
* Plug-ins para aplicações de design e modelação de clientes:
  * Os plug-ins permitem que os utilizadores finais utilizem o Azure Batch diretamente da aplicação do cliente, como a Autodesk Maya, permitindo-lhes criar piscinas, apresentar empregos e utilizar mais capacidade de computação para executar renderizações mais rápidas.
* Integração de gestores de renderização:
  * O Azure Batch está integrado em aplicações de gestão de renderização ou estão disponíveis plug-ins para proporcionar a integração do Azure Batch.

Existem várias formas de usar o Azure Batch, que também se aplicam à renderização do Azure Batch.

* APIs:
  * Escreva código utilizando as [APis REST](/rest/api/batchservice), [.NET](/dotnet/api/overview/azure/batch), [Python,](/python/api/overview/azure/batch) [Java,](/java/api/overview/azure/batch)ou outras APIs suportadas.  Os desenvolvedores podem integrar as capacidades do Azure Batch nas suas aplicações ou fluxo de trabalho existentes, seja em nuvem ou com base nas instalações.  Por exemplo, o [plug-in Autodesk Maya](https://github.com/Azure/azure-batch-maya) utiliza a API de Batch Python para invocar o Batch, criar e gerir piscinas, apresentar empregos e tarefas e monitorizar o estado.
* Ferramentas de linha de comando:
  * A [linha de comando Azure](/cli/azure/) ou [Azure PowerShell](/powershell/azure/) pode ser usada para script Batch.
  * Em particular, o suporte ao modelo do Grupo CLI facilita muito a criação de piscinas e a apresentação de empregos.
* UIs:
  * [O Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta de clientes de plataforma cruzada que também permite que as contas do Batch sejam geridas e monitorizadas, mas fornece algumas capacidades mais ricas em comparação com o portal Azure UI.  Um conjunto de modelos de piscina e de trabalho são fornecidos à medida de cada aplicação suportada e podem ser usados para criar facilmente piscinas e para apresentar empregos.
  * O portal Azure pode ser utilizado para gerir e monitorizar o Azure Batch.
* Plug-in de aplicação do cliente:
  * Estão disponíveis plug-ins que permitem a utilização de Batching diretamente dentro das aplicações de design e modelação do cliente. Os plug-ins invocam principalmente a aplicação Batch Explorer com informações contextuais sobre o modelo 3D atual.
  * Estão disponíveis os seguintes plug-ins:
    * [Lote Azure para Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Liquidificador](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Começando com a renderização do Azure Batch

Consulte os seguintes tutoriais introdutórios para experimentar a renderização do Azure Batch:

* [Use o Batch Explorer para tornar uma cena de liquidificador](./tutorial-rendering-batchexplorer-blender.md)
* [Utilize o Lote CLI para tornar uma cena Autodesk 3ds Max](./tutorial-rendering-cli.md)

## <a name="next-steps"></a>Passos seguintes

Determine a lista de aplicações e versões de renderização incluídas nas imagens VM do Azure Marketplace [neste artigo](./batch-rendering-applications.md).
