---
title: Composição de descrição geral - Azure Batch
description: Introdução ao utilizar o Azure para o processamento e uma descrição geral das capacidades de composição do Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: dbe3fe59fd2674cf3475fc5b589a2c938e706ab2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441343"
---
# <a name="rendering-using-azure"></a>Composição com o Azure

O processamento é o processo de colocar modelos 3D e convertê-los em imagens 2D. Ficheiros de cenas 3D são criados nos aplicativos como o Autodesk 3ds Max e Blender Autodesk Maya.  Aplicações de composição, como o Autodesk Maya, Autodesk Arnold, V-Ray da Chaos Group e ciclos de Blender produzem imagens 2D.  Imagens única, às vezes, são criadas a partir os ficheiros de cenas. No entanto, é comum para modelar e compor várias imagens e, em seguida, combiná-los numa animação.

A carga de trabalho de composição é muito usada para efeitos especiais (VFX) no setor de mídia e entretenimento. Composição também é utilizada em vários outros setores, tais como a publicidade, de varejo, petróleo e gás e produção.

O processo de renderização é computacionalmente intensivo; Pode haver vários quadros/imagens para produzir e cada imagem pode demorar várias horas para processar.  O processamento, portanto, é uma carga de trabalho de processamento de batch perfeito que pode tirar partido do Azure e o Azure Batch para executar várias composições em paralelo.

## <a name="why-use-azure-for-rendering"></a>Porquê utilizar o Azure para processamento?

Por diversos motivos, o processamento é uma carga de trabalho perfeitamente adequada para o Azure e o Azure Batch:

* Composição de tarefas pode ser dividida em várias partes que podem ser executadas em paralelo com várias VMs:
  * Animações consistem em vários quadros e cada quadro pode ser processado em paralelo.  As VMs mais disponíveis para cada quadro, mais rapidamente todos os quadros e a animação podem ser produzidos de processo.
  * Algum software de composição permite que os quadros únicos dividir em várias partes, como mosaicos ou setores.  Cada parte pode ser processada separadamente, em seguida, combinada com a imagem final quando tem concluído a todas as partes.  Quanto mais VMs que estão disponíveis, mais rapidamente um quadro pode ser processado.
* Composição de projetos pode exigir escalas enormes:
  * Quadros individuais podem ser complexos e exigem muitas horas a ser processado, mesmo em hardware de alta gama; animações podem consistir em centenas de milhares de quadros.  Uma grande quantidade de computação é necessária para processar animações de alta qualidade num período de tempo razoável.  Em alguns casos, mais de 100.000 núcleos foram utilizados para processar milhares de quadros em paralelo.
* Projetos de composição são baseados no projeto e requerem quantidades diferentes de computação:
  * Atribuir capacidade de computação e armazenamento quando necessário, aumentar ou reduzir verticalmente, de acordo com carga durante um projeto e removê-lo quando um projeto é concluído.
  * Pague por capacidade quando alocados, mas não paga por isso, quando não existe nenhuma carga, tal como entre projetos.
  * Se adaptar para absorver picos devido a alterações inesperadas; escala superior se existem alterações inesperadas tardia num projeto e essas alterações precisam ser processadas com base numa agenda forte.
* Escolha entre uma seleção ampla de hardware de acordo com a aplicação, a carga de trabalho e o período de tempo:
  * Há uma seleção ampla de hardware disponíveis no Azure que pode ser alocado e gerido com o Batch.
  * Dependendo do projeto, o requisito pode ser o melhor relação preço/desempenho ou o melhor desempenho geral.  Plano diferentes e/ou aplicações de composição tem requisitos diferentes de memória.  Algum aplicativo de processamento pode tirar partido das GPUs para o melhor desempenho ou determinadas funcionalidades. 
* VMs de baixa prioridade reduzam os custos:
  * VMs de baixa prioridade estão disponíveis para um desconto significativo em comparação comparado VMs regulares de sob demanda e são adequadas para alguns tipos de tarefa.
  * VMs de baixa prioridade podem ser alocadas pelo Azure Batch, com o Batch fornece flexibilidade em como elas são usadas para se adaptar para um vasto leque de requisitos.  Os conjuntos do batch podem consistir em VMs dedicadas e baixa prioridade, com o mesmo que está a ser possível alterar a combinação de tipos de VM em qualquer altura.

## <a name="options-for-rendering-on-azure"></a>Opções de composição no Azure

Há um recursos de intervalo do Azure que podem ser utilizado para cargas de trabalho de composição.  Que capacidades a utilizar depende de qualquer ambiente existente e requisitos.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Existente no local através de uma aplicação de gestão de composição de ambiente de composição

O caso mais comum é para aqui para ser um existente no local compor farm a ser gerido por um aplicativo de gerenciamento de composição como PipelineFX Qube, renderizar Royal ou Thinkbox dentro do prazo.  É o requisito expandir no local a capacidade de farm de composição com VMs do Azure.

O software de gestão de composição tem suporte do Azure interno ou podemos tornar o plug-ins disponíveis que adicionar suporte do Azure. Para obter mais informações sobre o suporte compor os gerentes e a funcionalidade ativada, consulte o artigo sobre [usando renderizar gerentes](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Fluxo de trabalho de composição personalizada

O requisito é para as VMs expandir um farm de composição existente.  Conjuntos do Azure Batch podem alocar um grande número de VMs, permitir às VMs de baixa prioridade ser utilizado e dinamicamente dimensionamento automático com VMs com preços completo e fornecer o licenciamento de pagamento para utilização para aplicações de composição populares.

### <a name="no-existing-render-farm"></a>Nenhum farm de composição existente

Estações de trabalho cliente poderão estar a executar a composição, mas está a aumentar a carga de trabalho de composição e está a demorar demasiado tempo a utilizar apenas a capacidade de estação de trabalho.  O Azure Batch pode ser utilizado para alocar render farm computação sob demanda os e agendar trabalhos de composição para o farm de composição do Azure.

## <a name="azure-batch-rendering-capabilities"></a>Capacidades de composição do Azure Batch

O Azure Batch permite que cargas de trabalho paralelas ser executado no Azure.  Permite a criação e gestão de grandes quantidades de VMs em que as aplicações são instaladas e executadas.  Ele também oferece capacidades para executar as instâncias desses aplicativos, fornecendo a atribuição de tarefas para VMs, colocação em fila, aplicação de monitorização e assim por diante de agendamento de tarefas abrangente.

O Azure Batch é utilizado para muitas cargas de trabalho, mas as seguintes funcionalidades estão disponíveis para especificamente que seja mais fácil e rápido executar cargas de trabalho de composição.

* Imagens de VM com gráficos pré-instaladas e aplicações de composição:
  * Imagens de VM do Marketplace do Azure estão disponíveis que contêm gráficos populares e aplicações de composição, evitando a necessidade de instalar os aplicativos por conta própria ou criar suas próprias imagens personalizadas com os aplicativos instalados. 
* Licenciamento pay-per-use para aplicações de composição:
  * Pode optar por pagar para os aplicativos ao minuto, além de pagar para a computação de VMs, que evita ter de comprar licenças e potencialmente configurar um servidor de licenças para os aplicativos.  Pagar por utilização também significa que é possível para se adaptar para variados e inesperada de carga porque não é um número fixo de licenças não existe.
  * Também é possível utilizar as aplicações pré-instaladas com suas próprias licenças e não utilizar o licenciamento de pagamento por utilização. Para fazer isso, normalmente instala um no local ou baseado no Azure de licença de servidor e utilizar uma rede virtual do Azure para ligar o conjunto de composição para o servidor de licenças.
* Plug-ins para aplicativos de modelagem e design de cliente:
  * Plug-ins de permitir que os utilizadores finais possam utilizar o Azure Batch diretamente a partir do aplicativo cliente, como o Autodesk Maya, permitindo-lhes criar conjuntos, submeter tarefas e fazer uso de mais a capacidade para executar composições mais rápidas de computação.
* Integração do Gestor de composição:
  * O Azure Batch está integrado a aplicações de gestão de composição ou plug-ins estejam disponíveis para fornecer a integração do Azure Batch.

Existem várias formas de utilizar o Azure Batch, que também se aplicam ao Azure Batch rendering.

* APIs:
  * Escrever código com o [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch), ou outro suporte de APIs.  Os desenvolvedores podem integrar capacidades do Azure Batch em seus aplicativos existentes ou o fluxo de trabalho, quer na cloud ou com base no local.  Por exemplo, o [Autodesk Maya Plug-in](https://github.com/Azure/azure-batch-maya) utiliza a API de Python do Batch para invocar o Batch, criar e gerir conjuntos, submeter trabalhos e tarefas e monitorização do Estado.
* Ferramentas de linha de comandos:
  * O [linha de comandos do Azure](https://docs.microsoft.com/cli/azure/) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) pode ser utilizado para a utilização de Batch do script.
  * Em particular, o suporte de modelo de CLI do Batch torna muito mais fácil criar agrupamentos e submeter tarefas.
* Interfaces do usuário:
  * [Explorador do batch](https://github.com/Azure/BatchExplorer) é uma ferramenta de cliente para várias plataformas, que também permite que as contas do Batch a ser geridos e monitorizados, mas fornece algumas capacidades mais ricas em comparação comparadas a IU do portal do Azure.  Um conjunto de modelos de conjunto e o trabalho são desde que são adaptados para cada aplicativo suportado e pode ser usado para facilmente criar agrupamentos e submeter tarefas.
  * O portal do Azure pode ser utilizado para gerir e monitorizar o Azure Batch.
* Cliente aplicativo do plug-in:
  * Plug-ins estão disponíveis que permitem a composição do Batch para ser utilizada a partir diretamente do design de cliente e aplicações de modelos. Os plug-ins principalmente invocar a aplicação do Explorador do Batch com informações contextuais sobre o atual modelo 3D.
  * Os plug-ins seguintes estão disponíveis:
    * [O Azure Batch para Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Introdução ao Azure Batch rendering

Consulte os seguintes tutoriais de introdução para experimentar o Azure Batch rendering:

* [Utilize o Explorador do Batch para compor uma cena Blender](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Utilizar a CLI do Batch para compor uma cena Autodesk 3ds Max](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Passos Seguintes

Determinar a lista de aplicações de composição e versões incluídas nas imagens de VM do Azure Marketplace no [este artigo](https://docs.microsoft.com/azure/batch/batch-rendering-applications).