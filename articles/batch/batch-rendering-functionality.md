---
title: Composição de capacidades - Azure Batch
description: Recursos de renderização específico no Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: be6c0f9a8874507433606903bcbd58c7723d6a8a
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791805"
---
# <a name="azure-batch-rendering-capabilities"></a>Capacidades de composição do Azure Batch

Capacidades da edição Standard do Azure Batch são utilizadas para executar aplicações e cargas de trabalho de composição. Batch também inclui funcionalidades específicas para suportar cargas de trabalho de composição.

Para uma visão geral dos conceitos do Batch, incluindo conjuntos, trabalhos e tarefas, consulte [este artigo](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Conjuntos do batch

### <a name="rendering-application-installation"></a>Instalação de aplicativos de composição

Uma imagem VM do Azure Marketplace processamento pode ser especificada na configuração do agrupamento se apenas as aplicações pré-instaladas tem de ser utilizada.

Existe uma imagem do 2016 do Windows e uma imagem do CentOS.  Na [do Azure Marketplace](https://azuremarketplace.microsoft.com), as imagens de VM podem ser encontradas ao procurar por "composição do batch".

Para um exemplo de configuração de agrupamento, consulte a [tutorial de composição de CLI do Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  O portal do Azure e o Explorador do Batch fornecem ferramentas de GUI para selecionar uma imagem de VM de composição, quando cria um conjunto.  Se utilizar uma API do Batch, em seguida, especifique os seguintes valores de propriedade para [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) ao criar um agrupamento:

| Publicador | Oferta | Sku | Versão |
|---------|---------|---------|--------|
| lote | rendering-centos73 | composição | mais recente |
| lote | rendering-windows2016 | composição | mais recente |

Outras opções estão disponíveis se aplicativos adicionais são necessários no pool de VMs:

* Uma imagem personalizada com base numa imagem do Marketplace padrão:
  * Com esta opção, pode configurar a VM com as aplicações e versões específicas exatas de que precisa. Para obter mais informações, consulte [utilizar uma imagem personalizada para criar um conjunto de máquinas virtuais](https://docs.microsoft.com/azure/batch/batch-custom-images). Autodesk e a Chaos Group modificou Arnold e V-Ray, respectivamente, para validar em relação a um serviço de licenciamento do Azure Batch. Certifique-se de que tem as versões destas aplicações com esse suporte, caso contrário, que o licenciamento de pagamento por utilização não funcionará. As versões atuais do Maya ou de 3ds Max não requerem um servidor de licenças quando em execução sem periféricos (no modo de batch/linha de comandos). Se não tiver a certeza como proceder com esta opção, contacte o suporte do Azure.
* [Pacotes de aplicações](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Empacotar os ficheiros de aplicação com um ou mais arquivos ZIP, carregar através do portal do Azure e especifique o pacote de configuração de agrupamento. Quando as VMs do conjunto são criados, os ficheiros ZIP são transferidos e extraídos os ficheiros.
* Arquivos de recursos:
  * Arquivos do aplicativo são carregados para o armazenamento de Blobs do Azure e o utilizador Especifica referências do arquivo no [tarefa de início do conjunto](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). Quando são criadas VMs de conjunto, os ficheiros de recursos são transferidos para cada VM.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licenciamento de pagamento para utilização para aplicativos previamente instalados

Os aplicativos que serão utilizados e têm uma tarifa de licenciamento tem de ser especificado na configuração do conjunto.

* Especifique a `applicationLicenses` propriedade quando [criar um agrupamento](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Os seguintes valores podem ser especificados na matriz de cadeias de caracteres, "vray", "arnold", "3dsmax", "maya".
* Quando especificar uma ou mais aplicações, o custo desses aplicativos é adicionado ao custo das VMs.  Os preços de aplicativo são listados os [página de preços do Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Se em vez disso, se liga a um servidor de licença para usar os aplicativos de composição, não especifique o `applicationLicenses` propriedade.

Pode utilizar o portal do Azure ou o Explorador do Batch selecione aplicações e mostrar os preços de aplicação.

Se for feita uma tentativa para utilizar uma aplicação, mas o aplicativo ainda não foram especificado a `applicationLicenses` propriedade da configuração do conjunto ou faz não alcance um servidor de licenças, em seguida, a execução do aplicativo falhar com um erro de licenciamento e o código de saída diferente de zero.

### <a name="environment-variables-for-pre-installed-applications"></a>Variáveis de ambiente para aplicações pré-instaladas

Para poder criar a linha de comandos para tarefas de composição, tem de especificar a localização de instalação de executáveis de aplicativos de composição.  Variáveis de ambiente de sistema tem sido criadas nas imagens de VM do Azure Marketplace, que podem ser utilizadas em vez de precisar especificar caminhos concretos.  Estas variáveis de ambiente são para além da [variáveis de ambiente padrão do Batch](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) criado para cada tarefa.

|Aplicação|Aplicação executável|Variável de ambiente|
|---------|---------|---------|
|Autodesk 3ds Max de 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max de 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Chaos Group V-Ray autónomo|vray.exe|VRAY_3.60.4_EXEC|
Linha de comandos Arnold 2017|kick.exe|ARNOLD_2017_EXEC|
|Linha de comandos Arnold 2018|kick.exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Famílias de VM do Azure

Tal como acontece com outras cargas de trabalho, variam de requisitos de sistema de aplicativos de composição e requisitos de desempenho variam para projetos e tarefas.  Uma grande variedade de famílias de VM estão disponíveis no Azure, dependendo dos requisitos – mais baixos custo, melhor relação preço/desempenho, melhor desempenho e assim por diante.
Alguns aplicativos de composição, como o Arnold, são baseados em CPU; outros como V-Ray e ciclos de Blender podem utilizar CPUs e/ou GPUs.
Para obter uma descrição de famílias de VM disponíveis e tamanhos de VM [Consulte tipos VM e os tamanhos](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>VMs de baixa prioridade

Tal como acontece com outras cargas de trabalho, as VMs de baixa prioridade podem ser utilizadas em conjuntos do Batch para composição.  VMs de baixa prioridade executam as mesmas VMs dedicadas regulares, mas utilizam da capacidade excedente do Azure e estão disponíveis para um desconto significativo.  O compromisso para a utilização de VMs de baixa prioridade é que essas VMs podem não estar disponíveis para atribuir ou podem ser suplantadas em qualquer altura, dependendo da capacidade disponível. Por esse motivo, VMs de baixa prioridade não serão adequado para todos os trabalhos de composição. Por exemplo, se imagens demorar várias horas para processar, em seguida, é provável que a composição dessas imagens interrompido e reiniciado devido a VMs que está a ser substituídas a ter seria aceitável.

Para obter mais informações sobre as características de VMs de baixa prioridade e as várias formas de configurá-las com o Batch, veja [utilizar VMs de baixa prioridade com o Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas

Não existe suporte de renderização específico é necessário para trabalhos e tarefas.  O item de configuração principal é a linha de comandos de tarefas, o que precisa fazer referência a aplicação necessária.
Quando são utilizadas as imagens de VM do Azure Marketplace, a melhor prática é utilizar as variáveis de ambiente para especificar o caminho e o executável do aplicativo.

## <a name="next-steps"></a>Passos Seguintes

Para exemplos de composição do Batch, experimente os dois tutoriais:

* [Composição com a CLI do Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Composição com o Explorador do Batch](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
