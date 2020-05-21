---
title: Visão geral de renderização
description: Introdução da utilização do Azure para renderização e uma visão geral das capacidades de renderização do Lote Azure
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: fc779d6caba2b896b869440ad523291bc3c9a9b2
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726439"
---
# <a name="rendering-using-azure"></a>Composição com o Azure

Renderização é o processo de pegar em modelos 3D e convertê-los em imagens 2D. Os ficheiros de cenas 3D são da autoria de aplicações como Autodesk 3ds Max, Autodesk Maya e Blender.  Aplicações de renderização como Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray e Blender Cycles produzem imagens 2D.  Às vezes, imagens individuais são criadas a partir dos ficheiros da cena. No entanto, é comum modelar e renderizar múltiplas imagens, e depois combiná-las numa animação.

A carga de trabalho de renderização é fortemente utilizada para efeitos especiais (VFX) na indústria dos Media e Entretenimento. A composição também é utilizada em muitos outros setores, como publicidade, retalho, petróleo e gás e fabrico.

O processo de renderização é computacionalmente intensivo; pode haver muitos quadros/imagens para produzir e cada imagem pode levar muitas horas para renderizar.  A renderização é, portanto, uma carga de trabalho de processamento de lote perfeito que pode alavancar O Lote Azure e Azure para executar muitos renders em paralelo.

## <a name="why-use-azure-for-rendering"></a>Porquê usar o Azure para renderizar?

Por muitas razões, renderização é uma carga de trabalho perfeitamente adequada para O Lote Azure e Azure:

* Os trabalhos de renderização podem ser divididos em muitas peças que podem ser executadas em paralelo usando vários VMs:
  * As animações consistem em muitos quadros e cada quadro pode ser renderizado em paralelo.  Quanto mais VMs estiverem disponíveis para processar cada quadro, mais rápido todos os quadros e a animação podem ser produzidos.
  * Alguns softwares de renderização permitem que quadros individuais sejam divididos em várias peças, tais como azulejos ou fatias.  Cada peça pode ser renderizada separadamente, depois combinada na imagem final quando todas as peças estiverem terminadas.  Quanto mais VMs estiverem disponíveis, mais rápido uma moldura pode ser renderizada.
* Projetos de renderização podem exigir uma enorme escala:
  * Os quadros individuais podem ser complexos e requerem muitas horas para renderizar, mesmo em hardware topo de gama; animações podem consistir em centenas de milhares de quadros.  Uma enorme quantidade de computação é necessária para renderizar animações de alta qualidade em um tempo razoável.  Em alguns casos, mais de 100.000 núcleos têm sido usados para tornar milhares de quadros em paralelo.
* Os projetos de renderização são baseados em projetos e requerem quantidades variadas de cálculo:
  * Aloque a capacidade de computação e armazenamento quando necessário, escale-a para cima ou para baixo de acordo com a carga durante um projeto e remova-a quando um projeto estiver concluído.
  * Pague pela capacidade quando atribuído, mas não pague quando não há carga, como entre projetos.
  * Atender a rajadas devido a alterações inesperadas; escala maior se houver mudanças inesperadas no final de um projeto e essas mudanças precisam de ser processadas em um calendário apertado.
* Escolha entre uma ampla seleção de hardware de acordo com a aplicação, carga de trabalho e prazo:
  * Há uma grande seleção de hardware disponível no Azure que pode ser alocado e gerido com Batch.
  * Dependendo do projeto, a exigência pode ser para o melhor preço/desempenho ou o melhor desempenho geral.  Diferentes cenas e/ou aplicações de renderização terão diferentes requisitos de memória.  Algumas aplicações de renderização podem alavancar gpus para o melhor desempenho ou certas funcionalidades. 
* Os VMs de baixa prioridade reduzem os custos:
  * Os VMs de baixa prioridade estão disponíveis para um grande desconto em comparação com os VMs regulares a pedido e são adequados para alguns tipos de trabalho.
  * Os VMs de baixa prioridade podem ser atribuídos pelo Lote Azure, com o Batch a oferecer flexibilidade na forma como são utilizados para atender a um vasto conjunto de requisitos.  As piscinas de lotes podem consistir tanto em VMs dedicados como em baixa prioridade, sendo possível alterar a mistura de tipos vM a qualquer momento.

## <a name="options-for-rendering-on-azure"></a>Opções para renderização no Azure

Existem uma gama de capacidades Azure que podem ser usadas para renderizar cargas de trabalho.  Quais as capacidades a utilizar depende de qualquer ambiente e requisitos existentes.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Ambiente de renderização existente no local utilizando uma aplicação de gestão renderizada

O caso mais comum é que exista uma exploração existente no local, sendo gerida por uma aplicação de gestão de renderização como PipelineFX Qube, Royal Render ou Thinkbox Deadline.  A obrigação é alargar a capacidade agrícola no local utilizando VMs Azure.

O software de gestão renderização ou tem suporte Azure incorporado ou disponibilizamos plug-ins que adicionam suporte Azure. Para obter mais informações sobre os gestores de renderização suportados e funcionalidades habilitadas, consulte o artigo sobre [a utilização](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers)de gestores de renderização .

### <a name="custom-rendering-workflow"></a>Fluxo de trabalho de renderização personalizada

A exigência é que os VMs aumentem uma exploração de renderização existente.  As piscinas do Lote Azure podem alocar um grande número de VMs, permitir que vMs de baixa prioridade sejam usados e dinamicamente auto-dimensionados com VMs a preços inteiros, e fornecer licenças pay-for-use para aplicações de renderização populares.

### <a name="no-existing-render-farm"></a>Nenhuma fazenda de renderização existente

As estações de trabalho dos clientes podem estar a realizar renderização, mas a carga de trabalho de renderização está a aumentar e está a demorar muito tempo a utilizar exclusivamente a capacidade da estação de trabalho.  O Lote Azure pode ser usado para atribuir a putação agrícola renderizada a pedido, bem como agendar os trabalhos de renderização para a fazenda de renderização Azure.

## <a name="azure-batch-rendering-capabilities"></a>Capacidades de renderização do Lote Azure

O Lote Azure permite que as cargas de trabalho paralelas sejam executadas em Azure.  Permite a criação e gestão de um grande número de VMs em que as aplicações são instaladas e executadas.  Também fornece capacidades abrangentes de agendamento de trabalho para executar instâncias dessas aplicações, fornecendo a atribuição de tarefas a VMs, fila, monitorização de aplicações, e assim por diante.

O Lote Azure é utilizado para muitas cargas de trabalho, mas as seguintes capacidades estão disponíveis para tornar mais fácil e rápido executar cargas de trabalho de renderização.

* Imagens VM com gráficos pré-instalados e aplicações de renderização:
  * Estão disponíveis imagens VM do Azure Marketplace que contêm gráficos populares e aplicações de renderização, evitando a necessidade de instalar as aplicações por si mesmo ou criar as suas próprias imagens personalizadas com as aplicações instaladas. 
* Licenciamento por pagamento para aplicações de prestação:
  * Pode optar por pagar as aplicações ao minuto, além de pagar os VMs computacionais, o que evita ter de comprar licenças e potencialmente configurar um servidor de licença para as aplicações.  Pagar pela utilização também significa que é possível atender a cargavariada e inesperada, uma vez que não existe um número fixo de licenças.
  * Também é possível utilizar as aplicações pré-instaladas com as suas próprias licenças e não utilizar o licenciamento pay-per-use. Para isso, normalmente instala-se um servidor de licença baseado no Local ou azure e utiliza uma rede virtual Azure para ligar a piscina de renderização ao servidor de licenças.
* Plug-ins para design de clientes e aplicações de modelação:
  * Os plug-ins permitem que os utilizadores finais utilizem o Lote Azure diretamente a partir da aplicação do cliente, como a Autodesk Maya, permitindo-lhes criar piscinas, submeter empregos e utilizar mais capacidade de computação para realizar renderizações mais rápidas.
* Integração de gestor de renderização:
  * O Azure Batch está integrado em aplicações de gestão renderizadas ou plug-ins estão disponíveis para fornecer a integração do Lote Azure.

Existem várias formas de utilizar o Lote Azure, que também se aplicam à renderização do Lote Azure.

* APIs:
  * Escreva código utilizando o [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET,](https://docs.microsoft.com/dotnet/api/overview/azure/batch) [Python,](https://docs.microsoft.com/python/api/overview/azure/batch) [Java,](https://docs.microsoft.com/java/api/overview/azure/batch)ou outras APIs suportadas.  Os desenvolvedores podem integrar as capacidades do Azure Batch nas suas aplicações ou fluxos de trabalho existentes, seja em nuvem ou com base no local.  Por exemplo, o [plug-in Autodesk Maya](https://github.com/Azure/azure-batch-maya) utiliza a API do Batch Python para invocar o Batch, criar e gerir piscinas, submeter empregos e tarefas e monitorizar o estado.
* Ferramentas de linha de comando:
  * A linha de [comando Azure](https://docs.microsoft.com/cli/azure/) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) podem ser utilizadas para a utilização do lote.
  * Em particular, o suporte do modelo DE Lote CLI torna muito mais fácil criar piscinas e apresentar empregos.
* UIs:
  * [O Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta de cliente transversal que também permite que as contas do Batch sejam geridas e monitorizadas, mas fornece algumas capacidades mais ricas em comparação com o portal Azure UI.  Um conjunto de modelos de bilhar e de trabalho são fornecidos que são personalizados para cada aplicação apoiada e podem ser usados para criar facilmente piscinas e para apresentar empregos.
  * O portal Azure pode ser utilizado para gerir e monitorizar o Lote Azure.
* Plug-in de aplicação de cliente:
  * Estão disponíveis plug-ins que permitem que a renderização do Lote seja utilizada diretamente dentro das aplicações de design e modelação do cliente. Os plug-ins invocam principalmente a aplicação Batch Explorer com informações contextuais sobre o atual modelo 3D.
  * Estão disponíveis os seguintes plug-ins:
    * [Lote Azure para Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Liquidificador](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Começando com a renderização do Lote Azure

Consulte os seguintes tutoriais introdutórios para experimentar a renderização do Lote Azure:

* [Use o Batch Explorer para renderizar uma cena de liquidificador](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Utilize o Batch CLI para renderizar uma cena Autodesk 3ds Max](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Próximos passos

Determine a lista de aplicações e versões de renderização incluídas nas imagens VM do Azure Marketplace [neste artigo](https://docs.microsoft.com/azure/batch/batch-rendering-applications).