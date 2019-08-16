---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a334b19fe4dd819a6e4c391e49d934bf5955a567
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516055"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Perguntas frequentes sobre discos de VM IaaS do Azure e discos Premium gerenciados e não gerenciados

Este artigo responde a algumas perguntas frequentes sobre o Azure Managed Disks e os discos de SSD Premium do Azure.

## <a name="managed-disks"></a>Managed Disks

**O que é o Azure Managed Disks?**

Managed Disks é um recurso que simplifica o gerenciamento de disco para VMs IaaS do Azure manipulando o gerenciamento de conta de armazenamento para você. Para obter mais informações, consulte a [visão geral de Managed disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Se eu criar um disco gerenciado padrão de um VHD existente que tenha 80 GB, quanto isso custará?**

Um disco gerenciado padrão criado a partir de um VHD de 80 GB é tratado como o próximo tamanho de disco padrão disponível, que é um disco S10. Você é cobrado de acordo com o preço do disco S10. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Há algum custo de transação para o Managed disks Standard?**

Sim. Você é cobrado por cada transação. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Para um disco gerenciado padrão, serei cobrado pelo tamanho real dos dados no disco ou pela capacidade provisionada do disco?**

Você é cobrado com base na capacidade provisionada do disco. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Como o preço dos discos gerenciados Premium é diferente dos discos não gerenciados?**

O preço dos discos gerenciados Premium é o mesmo que os discos Premium não gerenciados.

**Posso alterar o tipo de conta de armazenamento (Standard ou Premium) dos meus discos gerenciados?**

Sim. Você pode alterar o tipo de conta de armazenamento de seus discos gerenciados usando o portal do Azure, o PowerShell ou o CLI do Azure.

**Posso usar um arquivo VHD em uma conta de armazenamento do Azure para criar um disco gerenciado com uma assinatura diferente?**

Sim.

**Posso usar um arquivo VHD em uma conta de armazenamento do Azure para criar um disco gerenciado em uma região diferente?**

Não.

**Há limitações de escala para clientes que usam Managed disks?**

Managed Disks elimina os limites associados às contas de armazenamento. No entanto, o limite máximo é 50.000 discos gerenciados por região e por tipo de disco para uma assinatura.

**Posso pegar um instantâneo incremental de um disco gerenciado?**

Não. O recurso de instantâneo atual faz uma cópia completa de um disco gerenciado.

**As VMs em um conjunto de disponibilidade podem consistir em uma combinação de discos gerenciados e não gerenciados?**

Não. As VMs em um conjunto de disponibilidade devem usar todos os discos gerenciados ou todos os discos não gerenciados. Ao criar um conjunto de disponibilidade, você pode escolher o tipo de discos que deseja usar.

**Managed Disks é a opção padrão no portal do Azure?**

Sim.

**Posso criar um disco gerenciado vazio?**

Sim. Você pode criar um disco vazio. Um disco gerenciado pode ser criado independentemente de uma VM, por exemplo, sem anexá-lo a uma VM.

**Qual é a contagem de domínios de falha com suporte para um conjunto de disponibilidade que usa Managed Disks?**

Dependendo da região em que o conjunto de disponibilidade que usa Managed Disks está localizado, a contagem de domínios de falha com suporte é 2 ou 3.

**Como a conta de armazenamento padrão para o diagnóstico é configurada?**

Você configura uma conta de armazenamento particular para diagnóstico de VM.

**Que tipo de suporte ao controle de acesso baseado em função está disponível para Managed Disks?**

Managed Disks dá suporte a três funções padrão principais:

* Proprietário Pode gerenciar tudo, incluindo o acesso
* Simplifica Pode gerenciar tudo, exceto o acesso
* Leitora Pode exibir tudo, mas não pode fazer alterações

**Há uma maneira de copiar ou exportar um disco gerenciado para uma conta de armazenamento particular?**

Você pode gerar um URI de SAS (assinatura de acesso compartilhado) somente leitura para o disco gerenciado e usá-lo para copiar o conteúdo para uma conta de armazenamento particular ou armazenamento local. Você pode usar o URI de SAS usando o portal do Azure, Azure PowerShell, CLI do Azure ou [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Posso criar uma cópia do meu disco gerenciado?**

Os clientes podem tirar um instantâneo de seus discos gerenciados e, em seguida, usar o instantâneo para criar outro disco gerenciado.

**Ainda há suporte para discos não gerenciados?**

Sim, há suporte para discos gerenciados e que não estão no gerenciamento. Recomendamos que você use discos gerenciados para novas cargas de trabalho e migre suas cargas de trabalho atuais para o Managed disks.

**Posso colocalizar o Managed disks e os discos não gerenciados na mesma VM?**

Não.

**Se eu criar um disco de 128 GB e, em seguida, aumentar o tamanho para 130 Gibibytes (GiB), serei cobrado pelo próximo tamanho de disco (256 GiB)?**

Sim.

**Posso criar armazenamento com redundância local, armazenamento com redundância geográfica e discos gerenciados de armazenamento com redundância de zona?**

Atualmente, o Azure Managed Disks dá suporte apenas a discos gerenciados de armazenamento com redundância local.

**Posso reduzir ou diminuir meus discos gerenciados?**

Não. Este recurso não tem suporte no momento.

**Posso interromper uma concessão em meu disco?**

Não. Isso não tem suporte no momento, pois uma concessão está presente para evitar a exclusão acidental quando o disco está sendo usado.

**Posso alterar a propriedade nome do computador quando um disco do sistema operacional especializado (não criado usando a ferramenta de preparação do sistema ou generalizado) é usado para provisionar uma VM?**

Não. Você não pode atualizar a propriedade nome do computador. A nova VM a herda da VM pai, que foi usada para criar o disco do sistema operacional. 

**Onde posso encontrar modelos de Azure Resource Manager de exemplo para criar VMs com discos gerenciados?**
* [Lista de modelos usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Ao criar um disco de um blob, há qualquer relacionamento continuamente existente com esse blob de origem?**

Não, quando o novo disco é criado, ele é uma cópia autônoma completa desse blob naquele momento e não há nenhuma conexão entre os dois. Se desejar, depois de criar o disco, o blob de origem poderá ser excluído sem afetar o disco recém-criado de forma alguma.

**Posso renomear um disco gerenciado ou não gerenciado depois que ele tiver sido criado?**

Para discos gerenciados, você não pode renomeá-los. No entanto, você pode renomear um disco não gerenciado contanto que ele não esteja anexado a um VHD ou VM no momento.

**Posso usar o particionamento GPT em um disco do Azure?**

O particionamento GPT pode ser usado somente em discos de dados, não em discos do sistema operacional. Os discos do sistema operacional devem usar o estilo de partição MBR.

**Quais tipos de disco dão suporte a instantâneos?**

Os instantâneos de suporte a SSD Premium, SSD padrão e HDD padrão. Para esses três tipos de disco, os instantâneos têm suporte para todos os tamanhos de disco (incluindo discos de até 32 TiB de tamanho). Ultra discos não oferecem suporte a instantâneos.

## <a name="ultra-disks"></a>Ultra discos

**Quais regiões suportam ultra disks no momento?**
- EUA Leste 2
- Sudeste da Ásia
- Europa do Norte

**Qual série de VMs oferece suporte a ultra discos atualmente?**
- ESv3
- DSv3

**Para que devo definir a taxa de transferência de ultra Disk?**
Se você não tiver certeza sobre o que definir a taxa de transferência do disco como, recomendamos que comece supondo um tamanho de e/s de 16 KiB e ajuste o desempenho a partir daí, enquanto monitora seu aplicativo. A fórmula é: Taxa de transferência em MBps = n º de IOPS * 16/1000.

**Configurei meu disco para 40000 IOPS, mas estou vendo apenas 12800 IOPS, por que não estou vendo o desempenho do disco?**
Além do acelerador de disco, há um acelerador de e/s que é imposto no nível da VM. Verifique se o tamanho da VM que você está usando pode dar suporte aos níveis que estão configurados em seus discos. Para obter detalhes sobre os limites de e/s impostos pela sua VM, consulte [tamanhos de máquinas virtuais do Windows no Azure](../articles/virtual-machines/windows/sizes.md).

**Posso usar níveis de cache com um ultra Disk?**
Não, ultra discos não dão suporte aos diferentes métodos de cache que têm suporte em outros tipos de disco. Defina o cache de disco como nenhum.

**Posso anexar um ultra Disk à minha VM existente?**
Talvez sua VM tenha que estar em um par de regiões e de zona de disponibilidade que dá suporte a ultra discos. Consulte [introdução aos ultra discos](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) para obter detalhes.

**Posso usar um ultra Disk como o disco do sistema operacional para minha VM?**
Não, ultra discos só têm suporte como discos de dados e só têm suporte como discos nativos de 4K.

**Posso converter um disco existente em um ultra Disk?**
Não, mas você pode migrar a data de um disco existente para um ultra Disk. Para migrar um disco existente para um ultra Disk, anexe ambos os discos à mesma VM e copie os dados do disco de um disco para outro ou aproveite uma solução de terceiros para a migração de dados.

**Posso criar instantâneos para ultra disks?**
Não, os instantâneos ainda não estão disponíveis.

**O backup do Azure está disponível para ultra discos?**
Não, o suporte do backup do Azure ainda não está disponível.

**Posso anexar um ultra Disk a uma VM em execução em um conjunto de disponibilidade?**
Não, ainda não há suporte para isso.

**Posso habilitar a Azure Site Recovery (ASR) para VMs usando ultra discos?**
Não, o ASR ainda não tem suporte para ultra discos.

## <a name="standard-ssd-disks"></a>Discos SSD Standard

**O que são os discos SSD Standard do Azure?**
Os discos de SSD Standard são discos padrão com suporte de mídia de estado sólido, otimizados como armazenamento econômico para cargas de trabalho que precisam de desempenho consistente em níveis de IOPS inferiores.

<a id="standard-ssds-azure-regions"></a>**Quais são as regiões com suporte no momento para discos SSD Standard?**
Todas as regiões do Azure agora dão suporte a discos SSD Standard.

**O backup do Azure está disponível ao usar o SSDs padrão?**
Sim, o backup do Azure agora está disponível.

**Como fazer criar discos SSD Standard?**
Você pode criar SSD Standard discos usando modelos de Azure Resource Manager, SDK, PowerShell ou CLI. Abaixo estão os parâmetros necessários no modelo do Resource Manager para criar SSD Standard discos:

* *apiVersion* para Microsoft. Compute deve ser definido como `2018-04-01` (ou posterior)
* Especifique *managedDisk. storageAccountType* como`StandardSSD_LRS`

O exemplo a seguir mostra a seção *Properties. storageProfile. osDisk* para uma VM que usa SSD Standard discos:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Para obter um exemplo de modelo completo de como criar um disco de SSD Standard com um modelo, consulte [criar uma VM de uma imagem do Windows com discos de dados SSD Standard](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Posso converter meus discos existentes em SSD Standard?**
Sim, pode. Consulte [converter o armazenamento do Azure Managed disks de Standard para Premium e vice-versa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) para obter as diretrizes gerais para a conversão de Managed disks. E use o seguinte valor para atualizar o tipo de disco para SSD Standard.
-AccountType StandardSSD_LRS

**Qual é o benefício de usar discos SSD Standard em vez de HDD?**
SSD Standard discos oferecem melhor latência, consistência, disponibilidade e confiabilidade em comparação com discos de HDD. As cargas de trabalho do aplicativo funcionam muito mais suavemente em SSD Standard por isso. Observe que os discos SSD Premium são a solução recomendada para a maioria das cargas de trabalho de produção com uso intensivo de e/s.

**Posso usar o SSDs padrão como discos não gerenciados?**
Não, os discos SSDs padrão só estão disponíveis como Managed Disks.

**SSD Standard discos dão suporte a "SLA de VM de instância única"?**
Não, o SSDs padrão não tem SLA de VM de instância única. Use SSD Premium discos para o SLA de VM de instância única.

## <a name="migrate-to-managed-disks"></a>Migrar para Managed Disks

**Há algum impacto na migração no desempenho do Managed Disks?**

A migração envolve a movimentação do disco de um local de armazenamento para outro. Isso é orquestrado por meio da cópia em segundo plano dos dados, o que pode levar várias horas para ser concluído, normalmente menos de 24 horas, dependendo da quantidade de dados nos discos. Durante esse tempo, seu aplicativo pode ficar mais alto do que a latência de leitura usual, já que algumas leituras podem ser redirecionadas para o local original e podem levar mais tempo para serem concluídas. Não há nenhum impacto na latência de gravação durante esse período.  

**Quais alterações são necessárias em uma configuração de serviço de backup do Azure já existente anterior/após a migração para Managed Disks?**

Nenhuma alteração é necessária.

**Meus backups de VM serão criados por meio do serviço de backup do Azure antes que a migração continue a funcionar?**

Sim, os backups funcionam sem problemas.

**Quais alterações são necessárias em uma configuração de criptografia de discos do Azure pré-existente anterior/após a migração para o Managed Disks?**

Nenhuma alteração é necessária.

**A migração automatizada de um conjunto de dimensionamento de máquinas virtuais existente de discos não gerenciados para Managed Disks tem suporte?**

Não. Você pode criar um novo conjunto de dimensionamento com Managed Disks usando a imagem do seu conjunto de dimensionamento antigo com discos não gerenciados.

**Posso criar um disco gerenciado a partir de um instantâneo de blob de páginas obtido antes de migrar para Managed Disks?**

Não. Você pode exportar um instantâneo de blob de páginas como um blob de páginas e, em seguida, criar um disco gerenciado com base no blob de páginas exportadas.

**Posso fazer failover de meus computadores locais protegidos por Azure Site Recovery para uma VM com Managed Disks?**

Sim, você pode optar por fazer failover para uma VM com Managed Disks.

**Há algum impacto na migração em VMs do Azure protegidas por Azure Site Recovery por meio da replicação do Azure para o Azure?**

Não. Azure Site Recovery proteção do Azure para o Azure para VMs com Managed Disks está disponível.

**Posso migrar VMs com discos não gerenciados que estão localizados em contas de armazenamento que foram ou foram criptografadas anteriormente para discos gerenciados?**

Sim

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks e Criptografia do Serviço de Armazenamento

**O Azure Criptografia do Serviço de Armazenamento habilitado por padrão quando crio um disco gerenciado?**

Sim.

**Quem gerencia as chaves de criptografia?**

A Microsoft gerencia as chaves de criptografia.

**Posso desabilitar Criptografia do Serviço de Armazenamento para meus discos gerenciados?**

Não.

**O Criptografia do Serviço de Armazenamento está disponível apenas em regiões específicas?**

Não. Ele está disponível em todas as regiões onde Managed Disks estão disponíveis. Managed Disks está disponível em todas as regiões públicas e na Alemanha. Ele também está disponível na China, no entanto, apenas para chaves gerenciadas pela Microsoft, não para chaves gerenciadas pelo cliente.

**Como posso descobrir se meu disco gerenciado está criptografado?**

Você pode descobrir a hora em que um disco gerenciado foi criado no portal do Azure, no CLI do Azure e no PowerShell. Se o horário for posterior a 9 de junho de 2017, o disco será criptografado.

**Como eu posso criptografar meus discos existentes que foram criados antes de 10 de junho de 2017?**

A partir de 10 de junho de 2017, novos dados gravados em discos gerenciados existentes são criptografados automaticamente. Também estamos planejando criptografar os dados existentes e a criptografia ocorrerá de forma assíncrona em segundo plano. Se você precisar criptografar os dados existentes agora, crie uma cópia do disco. Novos discos serão criptografados.

* [Copiar discos gerenciados usando o CLI do Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Copiar discos gerenciados usando o PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Os instantâneos gerenciados e as imagens são criptografados?**

Sim. Todos os instantâneos e imagens gerenciados criados após 9 de junho de 2017 são criptografados automaticamente. 

**Posso converter VMs com discos não gerenciados que estão localizados em contas de armazenamento que foram ou foram criptografadas anteriormente para discos gerenciados?**

Sim

**Um VHD exportado de um disco gerenciado ou um instantâneo também será criptografado?**

Não. Mas se você exportar um VHD para uma conta de armazenamento criptografada de um disco gerenciado ou instantâneo criptografado, ele será criptografado. 

## <a name="premium-disks-managed-and-unmanaged"></a>Discos Premium: Gerenciado e não gerenciado

**Se uma VM usa uma série de tamanho que dá suporte a discos SSD Premium, como um DSv2, posso anexar discos de dados Premium e Standard?** 

Sim.

**Posso anexar discos de dados Premium e Standard a uma série de tamanho que não dá suporte a discos SSD Premium, como a série D, Dv2, G ou F?**

Não. Você pode anexar somente discos de dados padrão a VMs que não usam uma série de tamanho que dá suporte a discos SSD Premium.

**Se eu criar um disco de dados Premium de um VHD existente que tenha 80 GB, quanto custará?**

Um disco de dados Premium criado a partir de um VHD de 80 GB é tratado como o tamanho do disco Premium disponível próximo, que é um disco P10. Você é cobrado de acordo com o preço do disco P10.

**Há custos de transação para usar SSD Premium discos?**

Há um custo fixo para cada tamanho de disco, que vem provisionado com limites específicos de IOPS e taxa de transferência. Os outros custos são a largura de banda de saída e a capacidade do instantâneo, se aplicável. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Quais são os limites de IOPS e taxa de transferência que posso obter do cache de disco?**

Os limites combinados para cache e SSD local para uma série DS são 4.000 IOPS por núcleo e 33 MiB por segundo por núcleo. A série GS oferece 5.000 IOPS por núcleo e 50 MiB por segundo por núcleo.

**O SSD local é compatível com uma VM Managed Disks?**

O SSD local é um armazenamento temporário que é incluído com uma VM Managed Disks. Não há nenhum custo adicional para esse armazenamento temporário. Recomendamos que você não use esse SSD local para armazenar os dados do aplicativo porque eles não são persistidos no armazenamento de BLOBs do Azure.

**Há alguma repercussão para o uso de TRIM em discos Premium?**

Não há nenhuma desvantagem do uso de TRIM em discos do Azure em discos Premium ou Standard.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Novos tamanhos de disco: Gerenciado e não gerenciado

**Qual é o maior tamanho de disco gerenciado com suporte para discos de sistema operacional e de dados?**

O tipo de partição que o Azure dá suporte para um disco do sistema operacional é o MBR (registro mestre de inicialização). O formato MBR dá suporte a um tamanho de disco de até 2 TiB. O maior tamanho que o Azure dá suporte para um disco do sistema operacional é 2 TiB. O Azure dá suporte a até 32 TiB para discos de dados gerenciados no Azure global, 4 TiB em nuvens soberanas do Azure.

**Qual é o maior tamanho de disco não gerenciado com suporte para discos de sistema operacional e de dados?**

O tipo de partição que o Azure dá suporte para um disco do sistema operacional é o MBR (registro mestre de inicialização). O formato MBR dá suporte a um tamanho de disco de até 2 TiB. O maior tamanho que o Azure dá suporte para um disco não gerenciado do sistema operacional é 2 TiB. O Azure dá suporte a até 4 TiB para discos não gerenciados.

**Qual é o tamanho do blob de páginas maior com suporte?**

O maior tamanho de blob de páginas ao qual o Azure dá suporte é 8 TiB (8.191 GiB). O tamanho máximo de blob de páginas quando anexado a uma VM como dados ou discos do sistema operacional é 4 TiB (4.095 GiB).

**Preciso usar uma nova versão das ferramentas do Azure para criar, anexar, redimensionar e carregar discos com mais de 1 TiB?**

Você não precisa atualizar suas ferramentas existentes do Azure para criar, anexar ou redimensionar discos com mais de 1 TiB. Para carregar o arquivo VHD do local diretamente no Azure como um blob de página ou disco não gerenciado, você precisa usar os conjuntos de ferramentas mais recentes listados abaixo. Há suporte apenas para carregamentos de VHD de até 8 TiB.

|Ferramentas do Azure      | Versões suportadas                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Número de versão 4.1.0: Versão de junho de 2017 ou posterior|
|CLI do Azure v1     | Número de versão 0.10.13: Versão 2017 de maio ou posterior|
|CLI do Azure v2     | Número de versão 2.0.12: Versão de julho de 2017 ou posterior|
|AzCopy           | Número de versão 6.1.0: Versão de junho de 2017 ou posterior|

**Os tamanhos de disco P4 e P6 têm suporte para discos não gerenciados ou BLOBs de página?**

Os tamanhos de disco P4 (32 GiB) e P6 (64 GiB) não têm suporte como as camadas de disco padrão para discos não gerenciados e blobs de páginas. Você precisa definir explicitamente [a camada de blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) como P4 e P6 para que o disco seja mapeado para essas camadas. Se você implantar um disco não gerenciado ou um blob de página com o tamanho do disco ou o comprimento do conteúdo inferior a 32 GiB ou entre 32 GiB para 64 GiB sem definir a camada de BLOB, você continuará a ir para P10 com 500 IOPS e 100 MiB/s e o tipo de preço mapeado.

**Se o meu disco gerenciado Premium existente com menos de 64 GiB foi criado antes de o pequeno disco ter sido habilitado (cerca de 15 de junho de 2017), como ele é cobrado?**

Os discos pequenos Premium existentes com menos de 64 GiB continuam sendo cobrados de acordo com o tipo de preço P10.

**Como posso mudar a camada de disco de pequenos discos Premium com menos de 64 GiB de P10 para P4 ou P6?**

Você pode tirar um instantâneo dos discos pequenos e, em seguida, criar um disco para alternar automaticamente o tipo de preço para P4 ou P6 com base no tamanho provisionado.

**Você pode redimensionar Managed Disks existentes de tamanhos menos de 4 tebibytes (TiB) para novos tamanhos de disco recentemente introduzidos até 32 TiB?**

Sim.

**Quais são os maiores tamanhos de disco com suporte pelo serviço de backup e Azure Site Recovery do Azure?**

O maior tamanho de disco com suporte do backup do Azure e do serviço de Azure Site Recovery é 4 TiB. O suporte para discos maiores até 32 TiB ainda não está disponível.

**Quais são os tamanhos de VM recomendados para tamanhos de disco maiores (> 4 TiB) para SSD Standard e HDD Standard discos para alcançar IOPS e largura de banda de disco otimizados?**

Para obter a taxa de transferência de disco de SSD Standard e HDD Standard tamanhos de disco grandes (> 4 TiB) Além de 500 IOPS e 60 MiB/s, recomendamos que você implante uma nova VM de um dos seguintes tamanhos de VM para otimizar o desempenho: Série B, DSv2, série Dsv3, série ESv3, série FS, série Fsv2, série M, série a, NCv2, série NCv3, ou VMs da série ls, ou 2 a 3. Anexar discos grandes a VMs ou VMS existentes que não usam os tamanhos recomendados acima pode apresentar um desempenho inferior.

**Como posso atualizar meus discos (> 4 TiB) que foram implantados durante a visualização de tamanhos de disco maiores para obter o IOPS mais alto & largura de banda em GA?**

Você pode parar e iniciar a VM na qual o disco está anexado ou desanexar e anexar novamente o disco. Os destinos de desempenho de tamanhos de disco maiores foram aumentados para SSDs Premium e SSDs padrão na GA.

**Quais regiões são os tamanhos de disco gerenciado de 8 TiB, 16 TiB e 32 TiB com suporte no?**

As SKUs de disco 8 TiB, 16 TiB e 32 TiB têm suporte em todas as regiões em global Azure, Microsoft Azure Governamental e Azure China 21Vianet.

**Há suporte para habilitar o cache de host em todos os tamanhos de disco?**

Damos suporte ao cache de host de ReadOnly e leitura/gravação em tamanhos de disco inferiores a 4 TiB. Para tamanhos de disco mais de 4 TiB, não há suporte para a definição da opção de cache diferente de nenhum. É recomendável aproveitar o cache para tamanhos de disco menores, em que você pode esperar observar melhor desempenho com os dados armazenados em cache para a VM.

## <a name="what-if-my-question-isnt-answered-here"></a>E se minha pergunta não for respondida aqui?

Se sua pergunta não estiver listada aqui, informe-nos e ajudaremos você a encontrar uma resposta. Você pode postar uma pergunta no final deste artigo nos comentários. Para se envolver com a equipe de armazenamento do Azure e outros membros da Comunidade sobre este artigo, use o [Fórum do armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)no msdn.

Para solicitar recursos, envie suas solicitações e ideias para o [Fórum de comentários do armazenamento do Azure](https://feedback.azure.com/forums/217298-storage).
