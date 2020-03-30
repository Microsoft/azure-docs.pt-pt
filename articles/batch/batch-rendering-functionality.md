---
title: Capacidades de renderização - Lote Azure
description: As capacidades standard Azure Batch são usadas para executar cargas de trabalho e aplicações de renderização. O lote inclui funcionalidades específicas para suportar a renderização de cargas de trabalho.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 697e2640b7215e0bbb9202c672f936535831eb99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449711"
---
# <a name="azure-batch-rendering-capabilities"></a>Capacidades de renderização do Lote Azure

As capacidades standard azure batch são usadas para executar cargas de trabalho e aplicações de renderização. O lote também inclui funcionalidades específicas para suportar a renderização de cargas de trabalho.

Para uma visão geral dos conceitos de Lote, incluindo piscinas, empregos e tarefas, consulte [este artigo](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Piscinas de Lote

### <a name="rendering-application-installation"></a>Instalação de aplicação de renderização

Uma imagem VM de renderização do Azure Marketplace pode ser especificada na configuração da piscina se apenas as aplicações pré-instaladas precisarem de ser utilizadas.

Existe uma imagem do Windows 2016 e uma imagem CentOS.  No [Mercado Azure,](https://azuremarketplace.microsoft.com)as imagens VM podem ser encontradas procurando por 'renderização de lote'.

Para um exemplo, configuração da piscina, consulte o tutorial de [renderização Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  O portal Azure e o Batch Explorer fornecem ferramentas GUI para selecionar uma imagem VM de renderização quando criar uma piscina.  Se utilizar uma API de lote, especifique os seguintes valores de propriedade para [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) ao criar um pool:

| Publicador | Oferta | Sku | Versão |
|---------|---------|---------|--------|
| lote | renderização-centos73 | renderização | mais recente |
| lote | renderização-windows2016 | renderização | mais recente |

Outras opções estão disponíveis se forem necessárias aplicações adicionais nos VMs do pool:

* Uma imagem personalizada da Galeria de Imagem Partilhada:
  * Com esta opção, pode configurar a VM com as aplicações e versões específicas exatas de que precisa. Para mais informações, consulte [Criar uma piscina com a Galeria de Imagem Partilhada.](batch-sig-images.md) A Autodesk e o Grupo Caos modificaram arnold e V-Ray, respectivamente, para validar com um serviço de licenciamento azure Batch. Certifique-se de que tem as versões destas aplicações com este suporte, caso contrário o licenciamento pay-per-use não funcionará. As versões atuais de Maya ou 3ds Max não necessitam de um servidor de licença quando estão a correr sem cabeça (no modo de lote/linha de comando). Contacte o suporte azure se não tiver a certeza de como proceder com esta opção.
* [Pacotes de candidatura:](https://docs.microsoft.com/azure/batch/batch-application-packages)
  * Embaas os ficheiros de aplicação utilizando um ou mais ficheiros ZIP, carregues através do portal Azure e especifique o pacote na configuração do pool. Quando os VMs da piscina são criados, os ficheiros ZIP são descarregados e os ficheiros extraídos.
* Ficheiros de recursos:
  * Os ficheiros de aplicação são enviados para o armazenamento de blob Azure e especifica referências de ficheiros na tarefa de início da [piscina](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). Quando os VMs do pool são criados, os ficheiros de recursos são descarregados em cada VM.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licenciamento pay-for-use para aplicações pré-instaladas

As aplicações que serão utilizadas e têm uma taxa de licenciamento precisam de ser especificadas na configuração do pool.

* Especifique a `applicationLicenses` propriedade ao [criar uma piscina.](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body)  Os seguintes valores podem ser especificados na gama de cordas - "vray", "arnold", "3dsmax", "maya".
* Quando especifica uma ou mais aplicações, o custo dessas aplicações é adicionado ao custo dos VMs.  Os preços da aplicação estão listados na página de preços do [Lote Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Se em vez disso se ligar a um servidor de `applicationLicenses` licença para utilizar as aplicações de renderização, não especifique a propriedade.

Pode utilizar o portal Azure ou o Batch Explorer para selecionar aplicações e mostrar os preços de aplicação.

Se for feita uma tentativa de utilização de uma aplicação, mas a aplicação não foi especificada na `applicationLicenses` propriedade da configuração do pool ou não chega a um servidor de licença, então a execução da aplicação falha com um erro de licenciamento e código de saída não zero.

### <a name="environment-variables-for-pre-installed-applications"></a>Variáveis ambientais para aplicações pré-instaladas

Para poder criar a linha de comando para tarefas de renderização, deve especificar-se a localização de instalação dos executíveis da aplicação de renderização.  Foram criadas variáveis do ambiente do sistema nas imagens VM do Azure Marketplace, que podem ser usadas em vez de terem de especificar caminhos reais.  Estas variáveis ambientais são além das [variáveis ambientais padrão](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) do Lote criadas para cada tarefa.

|Aplicação|Execução de aplicação|Variável ambiental|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Grupo Caos V-Ray Autónomo|vray.exe|VRAY_3.60.4_EXEC|
Linha de comando Arnold 2017|kick.exe|ARNOLD_2017_EXEC|
|Linha de comando Arnold 2018|kick.exe|ARNOLD_2018_EXEC|
|Liquidificador|liquidificador.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Famílias Azure VM

Tal como acontece com outras cargas de trabalho, os requisitos do sistema de aplicação variam e os requisitos de desempenho variam para empregos e projetos.  Uma grande variedade de famílias vm estão disponíveis em Azure dependendo das suas necessidades – custo mais baixo, melhor preço/desempenho, melhor desempenho, e assim por diante.
Algumas aplicações de renderização, como arnold, são baseadas em CPU; outros, como os Ciclos V-Ray e Blender, podem utilizar CPUs e/ou GPUs.
Para obter uma descrição das famílias VM disponíveis e tamanhos VM, [consulte tipos e tamanhos VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>VMs de baixa prioridade

Tal como acontece com outras cargas de trabalho, os VMs de baixa prioridade podem ser utilizados em piscinas de lote para renderização.  Os VMs de baixa prioridade executam o mesmo que os VMs dedicados regulares, mas utilizam a capacidade excedentária do Azure e estão disponíveis para um grande desconto.  A compensação pela utilização de VMs de baixa prioridade é que esses VMs podem não estar disponíveis para serem atribuídos ou podem ser antecipados a qualquer momento, dependendo da capacidade disponível. Por esta razão, os VMs de baixa prioridade não serão adequados para todos os trabalhos de representação. Por exemplo, se as imagens demoram muitas horas a renderizar, então é provável que ter a renderização dessas imagens interrompida e reiniciada devido à prevenção de VMs não seria aceitável.

Para obter mais informações sobre as características dos VMs de baixa prioridade e as várias formas de configurá-los usando o Batch, consulte [Use VMs de baixa prioridade com Lote](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Empregos e tarefas

Não é necessário um apoio específico para os postos de trabalho e tarefas.  O item principal de configuração é a linha de comando de tarefa, que precisa de fazer referência à aplicação necessária.
Quando as imagens VM do Azure Marketplace são utilizadas, então a melhor prática é utilizar as variáveis ambientais para especificar o caminho e a aplicação executáveis.

## <a name="next-steps"></a>Passos seguintes

Por exemplo, a renderização do Lote experimente os dois tutoriais:

* [Renderização utilizando o Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Compor com o Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
