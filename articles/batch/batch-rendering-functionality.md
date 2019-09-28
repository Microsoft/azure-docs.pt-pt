---
title: Recursos de renderização – lote do Azure
description: Recursos de renderização específicos no lote do Azure
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: b5eaaa6d41b9dae97a2d6219ffa44fb75ed67e61
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350039"
---
# <a name="azure-batch-rendering-capabilities"></a>Recursos de renderização do lote do Azure

Os recursos padrão do lote do Azure são usados para executar processamento de cargas de trabalho e aplicativos. O lote também inclui recursos específicos para dar suporte à renderização de cargas de trabalho.

Para obter uma visão geral dos conceitos do lote, incluindo pools, trabalhos e tarefas, consulte [Este artigo](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Pools do lote

### <a name="rendering-application-installation"></a>Processando a instalação do aplicativo

Uma imagem de VM de renderização do Azure Marketplace pode ser especificada na configuração do pool se apenas os aplicativos pré-instalados precisarem ser usados.

Há uma imagem do Windows 2016 e uma imagem CentOS.  No [Azure Marketplace](https://azuremarketplace.microsoft.com), as imagens de VM podem ser encontradas pesquisando ' renderização em lote '.

Para obter um exemplo de configuração de pool, consulte o [tutorial de renderização de CLI do Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  O portal do Azure e Batch Explorer fornecem ferramentas de GUI para selecionar uma imagem de renderização de VM quando você cria um pool.  Se estiver usando uma API do lote, especifique os seguintes valores de propriedade para [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) ao criar um pool:

| Fabricante | Oferta | Sku | Version |
|---------|---------|---------|--------|
| lote | rendering-centos73 | tratamento | latest |
| lote | rendering-windows2016 | tratamento | latest |

Outras opções estarão disponíveis se forem necessários aplicativos adicionais nas VMs do pool:

* Uma imagem personalizada da Galeria de imagens compartilhadas:
  * Com esta opção, pode configurar a VM com as aplicações e versões específicas exatas de que precisa. Para obter mais informações, consulte [criar um pool com a Galeria de imagens compartilhadas](batch-sig-images.md). O Autodesk e o grupo de caos modificaram o Arnold e o V-Ray, respectivamente, para validar em relação a um serviço de licenciamento do lote do Azure. Verifique se você tem as versões desses aplicativos com esse suporte, caso contrário, o licenciamento de pagamento por uso não funcionará. As versões atuais do Maya ou 3ds Max não exigem um servidor de licença ao serem executadas sem periféricos (no modo de linha de comando/lote). Contate o suporte do Azure se você não tiver certeza de como continuar com essa opção.
* [Pacotes de aplicativos](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Empacote os arquivos de aplicativo usando um ou mais arquivos ZIP, carregue por meio do portal do Azure e especifique o pacote na configuração de pool. Quando as VMs do pool são criadas, os arquivos ZIP são baixados e os arquivos são extraídos.
* Arquivos de recurso:
  * Os arquivos de aplicativo são carregados no armazenamento de BLOBs do Azure e você especifica as referências de arquivo na [tarefa de início do pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). Quando as VMs do pool são criadas, os arquivos de recursos são baixados em cada VM.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licenciamento de pagamento por uso para aplicativos pré-instalados

Os aplicativos que serão usados e terão uma taxa de licenciamento precisam ser especificados na configuração do pool.

* Especifique a propriedade `applicationLicenses` ao [criar um pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Os valores a seguir podem ser especificados na matriz de cadeias de caracteres-"Vray", "Arnold", "3dsmax", "Maya".
* Quando você especifica um ou mais aplicativos, o custo desses aplicativos é adicionado ao custo das VMs.  Os preços de aplicativo são listados na [página de preços do lote do Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Se, em vez disso, você se conectar a um servidor de licença para usar os aplicativos de renderização, não especifique a propriedade `applicationLicenses`.

Você pode usar o portal do Azure ou Batch Explorer para selecionar aplicativos e mostrar os preços do aplicativo.

Se for feita uma tentativa de usar um aplicativo, mas o aplicativo não tiver sido especificado na propriedade `applicationLicenses` da configuração do pool ou não alcançar um servidor de licença, a execução do aplicativo falhará com um erro de licenciamento e um código de saída diferente de zero.

### <a name="environment-variables-for-pre-installed-applications"></a>Variáveis de ambiente para aplicativos pré-instalados

Para poder criar a linha de comando para as tarefas de renderização, o local de instalação dos executáveis do aplicativo de renderização deve ser especificado.  Variáveis de ambiente do sistema foram criadas nas imagens de VM do Azure Marketplace, que podem ser usadas em vez de ter que especificar caminhos reais.  Essas variáveis de ambiente são além das [variáveis de ambiente do lote padrão](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) criadas para cada tarefa.

|Aplicação|Executável do aplicativo|Variável de Ambiente|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Grupo de caoss V-Ray autônomo|vray.exe|VRAY_3.60.4_EXEC|
Linha de comando do Arnold 2017|Iniciar. exe|ARNOLD_2017_EXEC|
|Linha de comando do Arnold 2018|Iniciar. exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Famílias de VMs do Azure

Assim como acontece com outras cargas de trabalho, a renderização dos requisitos do sistema de aplicativos varia e os requisitos de desempenho variam para trabalhos e projetos.  Uma grande variedade de famílias de VMs está disponível no Azure, dependendo de seus requisitos – custo mais baixo, melhor preço/desempenho, melhor desempenho e assim por diante.
Alguns aplicativos de renderização, como Arnold, são baseados em CPU; outros, como ciclos de V-Ray e do Blender, podem usar CPUs e/ou GPUs.
Para obter uma descrição das famílias de VMs disponíveis e tamanhos de VM, [Consulte tipos e tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>VMs de baixa prioridade

Assim como acontece com outras cargas de trabalho, as VMs de baixa prioridade podem ser utilizadas em pools do lote para renderização.  As VMs de baixa prioridade executam as mesmas como VMs dedicadas regulares, mas utilizam capacidade excedente do Azure e estão disponíveis para um grande desconto.  A desvantagem de usar VMs de baixa prioridade é que essas VMs podem não estar disponíveis para serem alocadas ou podem sofrer preempção a qualquer momento, dependendo da capacidade disponível. Por esse motivo, as VMs de baixa prioridade não serão adequadas para todos os trabalhos de renderização. Por exemplo, se as imagens demorarem muitas horas para serem renderizadas, é provável que a renderização dessas imagens seja interrompida e reiniciada porque as VMs que estão sendo admitidas não seriam aceitáveis.

Para obter mais informações sobre as características de VMs de baixa prioridade e as várias maneiras de configurá-las usando o lote, consulte [usar VMs de baixa prioridade com o lote](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas

Nenhum suporte específico de renderização é necessário para trabalhos e tarefas.  O principal item de configuração é a linha de comando da tarefa, que precisa fazer referência ao aplicativo necessário.
Quando as imagens de VM do Azure Marketplace são usadas, a prática recomendada é usar as variáveis de ambiente para especificar o caminho e o executável do aplicativo.

## <a name="next-steps"></a>Passos seguintes

Para obter exemplos de renderização em lote, experimente os dois tutoriais:

* [Renderização usando o CLI do Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderizando usando Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
