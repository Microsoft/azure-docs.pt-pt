---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/30/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 233834bd040362e27ffadc3d0003a87827012352
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407881"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Perguntas mais frequentes acerca dos discos de VM de IaaS do Azure e discos geridos e não geridos premium

Este artigo responde a algumas perguntas mais frequentes acerca dos discos de Managed Disks do Azure e do Azure Premium SSD.

## <a name="managed-disks"></a>Managed Disks

**O que é o Managed Disks do Azure?**

Discos geridos é uma funcionalidade que simplifica a gestão de discos para VMs IaaS do Azure ao lidar com gestão de conta de armazenamento para. Para obter mais informações, consulte a [descrição geral do Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Se eu criar um disco gerido standard a partir de um VHD existente que é 80 GB, como será que o custo-me?**

Um disco gerido standard criado a partir de um VHD de 80 GB é tratado como o próximo tamanho de disco standard disponíveis, que é um disco S10. É-lhe cobrado de acordo com os preços de disco S10. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Existem custos de transação para discos geridos standard?**

Sim. É-lhe cobrado para cada transação. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Para um disco gerido standard, vou ser cobrado para o tamanho real dos dados no disco ou para a capacidade aprovisionada do disco?**

É cobrado com base na capacidade do disco aprovisionada. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Como é o preço dos discos geridos premium diferentes de discos não geridos?**

Os preços dos discos geridos premium são o mesmo que os discos não geridos premium.

**Pode alterar o tipo de conta de armazenamento (Standard ou Premium) de meus discos geridos?**

Sim. Pode alterar o tipo de conta de armazenamento dos discos geridos utilizando o portal do Azure, PowerShell ou a CLI do Azure.

**Pode utilizar um ficheiro VHD numa conta de armazenamento do Azure para criar um disco gerido com uma subscrição diferente?**

Sim.

**Pode utilizar um ficheiro VHD numa conta de armazenamento do Azure para criar um disco gerido numa região diferente?**

Não.

**Existem limitações de escala para os clientes que utilizam discos geridos?**

Discos geridos elimina os limites associados a contas de armazenamento. No entanto, o limite máximo é de 50 000 discos geridos por região e por tipo de disco para uma subscrição.

**Pode tirar um instantâneo incremental de um disco gerido?**

Não. A capacidade de instantâneo atual faz uma cópia completa de um disco gerido.

**As VMs num conjunto de disponibilidade podem incluir uma combinação de discos geridos e não geridos?**

Não. As VMs num conjunto de disponibilidade tem de utilizar todos os discos geridos ou todos os discos não geridos. Quando cria um conjunto de disponibilidade, pode escolher o tipo de discos que pretende utilizar.

**É a opção predefinida no portal do Azure de Managed Disks?**

Sim.

**Pode criar um disco gerido vazio?**

Sim. Pode criar um disco vazio. Um disco gerido pode ser criado independentemente de uma VM, por exemplo, sem ligá-la a uma VM.

**O que é a contagem de domínios de falhas suportados para uma disponibilidade definida que utiliza Managed Disks?**

Consoante a região onde está localizado o conjunto de disponibilidade que utiliza discos geridos, a contagem de domínios de falhas suportados é de 2 ou 3.

**Como é a conta de armazenamento standard para obter um diagnóstico configurar?**

Configurar uma conta de armazenamento privado para diagnósticos de VM.

**Que tipo de suporte de controlo de acesso baseado em funções está disponível para os Managed Disks?**

Gerida discos suporta três principais padrão funções:

* Proprietário: Pode gerir tudo, incluindo o acesso
* Contribuinte de: Pode gerir tudo, exceto o acesso
* Leitor de: Pode ver tudo, mas não pode efetuar alterações

**Existe uma forma que pode copiar ou exportar um disco gerido para uma conta de armazenamento privado?**

Pode gerar uma assinatura de só de leitura de acesso partilhado (SAS) URI para o disco gerido e utilizá-lo para copiar o conteúdo para um armazenamento de conta ou no local de armazenamento privado. Pode utilizar o URI de SAS com o portal do Azure, Azure PowerShell, CLI do Azure, ou [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Pode criar uma cópia do meu disco gerido?**

Os clientes podem tirar um instantâneo de seus discos geridos e, em seguida, utilize o instantâneo para criar outro disco gerido.

**Discos não geridos ainda são suportados?**

Sim, os discos geridos e não são suportados. Recomendamos que utilize discos geridos para novas cargas de trabalho e migrar cargas de trabalho atuais para discos geridos.

**Conjuntamente posso localizar discos geridos e não na mesma VM?**

Não.

**Se eu criar um disco de 128 GB e, em seguida, aumente o tamanho para 130 gibibytes (GiB), vou ser cobrado para o próximo tamanho de disco (256gib)?**

Sim.

**Posso criar armazenamento localmente redundante, armazenamento georredundante, e managed disks de armazenamento com redundância de zona?**

Atualmente, o Managed Disks do Azure suporta discos de armazenamento apenas localmente redundante gerido.

**Pode reduzir ou atividade meus discos geridos?**

Não. Esta funcionalidade não é suportada atualmente.

**Pode quebrar uma concessão no meu disco?**

Não. Isto não é suportado atualmente como uma concessão está presente para impedir a eliminação acidental, quando o disco está a ser utilizado.

**Posso alterar a propriedade de nome do computador quando um especializado (não criada utilizando a ferramenta de preparação do sistema ou generalizado) o disco do sistema operativo é utilizado para Aprovisionar uma VM?**

Não. Não é possível atualizar a propriedade de nome de computador. A nova VM herda do principal VM, o que foi utilizado para criar o disco do sistema operativo. 

**Onde posso encontrar modelos do Azure Resource Manager de exemplo para criar VMs com discos geridos?**
* [Lista de modelos a utilizar discos geridos](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Ao criar um disco de um blob, existe qualquer relação continuamente existente com esse blob de origem?**

Não, quando é criado o novo disco é uma cópia autônoma completa desse blob nesse momento, e não existe nenhuma ligação entre os dois. Se desejar, depois de criar o disco, o blob de origem pode ser eliminado sem afetar o disco criado recentemente de qualquer forma.

**Posso renomear um disco gerido ou depois de este ter sido criado?**

Para discos geridos não é possível mudar o nome-los. No entanto, pode mudar o nome de um disco não gerido, desde que não está atualmente ligado a um VHD ou VM.

**Posso utilizar o GPT criação de partições num disco do Azure?**

A criação de partições de GPT pode ser usada apenas em discos de dados, não os discos de SO. Discos de SO tem de utilizar o estilo de partição MBR.

## <a name="standard-ssd-disks"></a>Discos SSD Standard

**Quais são os discos de SSD Standard do Azure?**
Os discos SSD Standard são apoiados por suporte de dados de estado sólido, com a otimização de armazenamento económico para cargas de trabalho que necessitam de um desempenho consistente em níveis inferiores de IOPS de discos standard.

<a id="standard-ssds-azure-regions"></a>**Quais são as regiões atualmente suportadas para discos de Standard SSD?**
Todas as regiões do Azure suportam agora os discos Standard SSD.

**Cópia de segurança do Azure está disponível quando utiliza SSDs padrão?**
Sim, cópia de segurança do Azure está agora disponível.

**Como posso criar discos Standard SSD?**
Pode criar discos Standard SSD utilizando modelos do Azure Resource Manager, SDK, o PowerShell ou CLI. Seguem-se os parâmetros necessários no modelo do Resource Manager para criar os discos de SSD Standard:

* *apiVersion* para a Microsoft. Compute tem de ser definido como `2018-04-01` (ou posterior)
* Especifique *managedDisk.storageAccountType* como `StandardSSD_LRS`

A exemplo a seguir mostra a *properties.storageProfile.osDisk* secção para uma VM que utilize discos de SSD Standard:

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

Para obter um exemplo de modelo completo de como criar um disco Standard SSD com um modelo, veja [criar uma VM a partir de uma imagem do Windows com discos de dados padrão do SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Posso converter a minha discos existentes para Standard SSD?**
Sim, pode. Consulte a [converter geridas do Azure o armazenamento de discos de standard para premium e vice-versa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) para as diretrizes gerais para a conversão de Managed Disks. E, utilize o seguinte valor para atualizar o tipo de disco para Standard SSD.
-AccountType StandardSSD_LRS

**O que é a vantagem de utilizar discos de SSD padrão em vez de HDD?**
Os discos Standard SSD entregar melhor latência, consistência, disponibilidade e fiabilidade em comparação comparada discos HDD. Cargas de trabalho de aplicação executam muito mais facilidade em Standard SSD por causa disso. Tenha em atenção de que os discos de Premium SSD são a solução recomendada para a maioria das cargas de trabalho de produção de e/s intensiva.

**Pode utilizar SSDs padrão como discos não geridos?**
Não, os discos Standard SSDs só estão disponíveis como Managed Disks.

**SSD os discos Standard suportam "SLA de VM de instância única"?**
Não, SSDs padrão não tem SLA de VM de instância única. Utilize discos de Premium SSD para VM SLA de instância única.

## <a name="migrate-to-managed-disks"></a>Migrar para o Managed Disks

**Existe qualquer impacto da migração sobre o desempenho de Managed Disks?**

A migração envolve a movimentação do disco de uma localização de armazenamento para outro. Isso é orquestrado por meio de cópia em segundo plano de dados, que podem demorar várias horas a concluir, normalmente, menos de 24 horas, dependendo da quantidade de dados nos discos. Durante esse tempo seu aplicativo pode assistir superior do que a latência de leitura normal como algumas leituras podem obter redirecionadas para a localização original e podem demorar mais tempo a concluir. Não é afetado na latência de escrita durante este período.  

**As alterações que são necessárias numa já existente do Azure Backup serviço antes/depois de migração de configuração para os Managed Disks?**

Não são necessárias alterações.

**Minhas cópias de segurança VM criadas através do serviço de cópia de segurança do Azure antes da migração continuarão a funcionar?**

Sim, as cópias de segurança funcionam perfeitamente.

**As alterações que são necessárias numa já existente encriptação de discos do Azure antes/depois de migração de configuração para os Managed Disks?**

Não são necessárias alterações.

**É migração automatizada de um dimensionamento de máquinas virtuais existentes do conjunto de discos não geridos para Managed Disks suportada?**

Não. Pode criar um novo conjunto de dimensionamento com discos geridos com a imagem a partir do antigo conjunto de dimensionamento com discos não geridos.

**Pode criar um disco gerido a partir de um instantâneo de blob de página antes de migrar para Managed Disks?**

Não. Pode exportar um instantâneo de blob de página como um blob de página e, em seguida, crie um disco gerido a partir do blob de página exportado.

**Pode falhar ao longo de minhas máquinas no local protegidas pelo Azure Site Recovery para uma VM com Managed Disks?**

Sim, pode escolher a ativação pós-falha para uma VM com discos geridos.

**Existe qualquer impacto da migração em VMs do Azure protegidos pelo Azure Site Recovery por meio da replicação do Azure para o Azure?**

Sim. Atualmente, a proteção do Azure para o Azure Site Recovery para VMs com discos geridos só está disponível como um serviço de pré-visualização pública.

**Pode migrar as VMs com discos não geridos que estão localizados em contas de armazenamento que estão ou que anteriormente eram criptografadas para os managed disks?**

Sim

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks e a encriptação do serviço de armazenamento

**É Azure Storage Service Encryption ativado por predefinição quando crio um disco gerido?**

Sim.

**Quem gere as chaves de encriptação?**

Microsoft gerencia as chaves de encriptação.

**Posso desativar a encriptação do serviço de armazenamento para meus discos geridos?**

Não.

**Encriptação do serviço de armazenamento só está disponível em regiões específicas?**

Não. Está disponível em todas as regiões onde o Managed Disks estão disponíveis. Discos geridos está disponível em todas as regiões públicas e Alemanha. Também está disponível na China, no entanto, apenas para Microsoft geridos chaves, não as chaves geridas pelo cliente.

**Como posso saber se meu disco gerido é encriptado?**

Pode encontrar a hora de criação de um disco gerido no portal do Azure, CLI do Azure e PowerShell. Se o período após o dia 9 de Junho de 2017, o disco está encriptado.

**Como pode criptografar os meus discos existentes que foram criados antes de 10 de Junho de 2017?**

A partir de 10 de Junho de 2017, os novos dados escritos nos discos geridos existentes são encriptados automaticamente. Estamos também a planear encriptar os dados existentes e a encriptação irá ocorrer de forma assíncrona em segundo plano. Se agora tem de encriptar os dados existentes, crie uma cópia do seu disco. Novos discos serão encriptados.

* [Copiar discos geridos com a CLI do Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Copiar discos geridos com o PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**São encriptadas de imagens e os instantâneos geridos?**

Sim. Instantâneos e imagens criadas após a 9 de Junho de 2017, todos os geridos são encriptadas automaticamente. 

**Pode converter VMs com discos não geridos que estão localizados em contas de armazenamento que estão ou que anteriormente eram criptografadas para os managed disks?**

Sim

**Irá também ser encriptado um VHD exportado a partir de um disco gerido ou um instantâneo?**

Não. Mas se exportar um VHD para uma conta de armazenamento encriptado de um encriptados gerido disco ou instantâneo e, em seguida, é encriptada. 

## <a name="premium-disks-managed-and-unmanaged"></a>Discos Premium: Geridos e não geridos

**Se uma VM utiliza uma série de tamanho que suporte discos Premium SSD, por exemplo, um DSv2, posso anexar premium e os discos de dados padrão?** 

Sim.

**Posso anexar premium e os discos de dados standard para uma série de tamanho que não suporta discos Premium SSD, por exemplo, a série D, Dv2, G ou F?**

Não. Pode anexar discos de dados padrão apenas para VMs que não utilizem uma série de tamanho que suporte discos Premium SSD.

**Se eu criar um disco de dados premium a partir de um VHD existente que foi 80 GB, como será que o custo?**

Um disco de dados de premium criado a partir de um VHD de 80 GB é tratado como o tamanho do disco premium disponíveis para a próxima, que é um disco P10. É-lhe cobrado de acordo com os preços de disco P10.

**Existem custos de transação para utilizar os discos de Premium SSD?**

Existe um custo fixo para cada tamanho de disco, o que vem aprovisionado limites específicos, em IOPS e débito. Os outros custos estão largura de banda de saída e a capacidade de instantâneo, se aplicável. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Quais são os limites de IOPS e débito que eu consegue a partir da cache de disco?**

Os limites combinados para a cache e SSD local para uma série de DS são 4000 IOPS por núcleo e MiB 33 por segundo por núcleo. A série GS oferece 5000 IOPS por núcleo e 50 MiB por segundo por núcleo.

**Local SSD é suportado para uma VM dos Managed Disks?**

Local SSD é um armazenamento temporário que acompanha uma VM de discos geridos. Não há custo adicional para este armazenamento temporário. Recomendamos que não utilize este SSD local para armazenar os dados da aplicação, porque elas não são mantidas no armazenamento de Blobs do Azure.

**Existem qualquer repercussões para a utilização de cortar no discos premium?**

Não há nenhuma desvantagem para a utilização de cortar nos discos do Azure no premium o ou os discos standard.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Novos tamanhos de disco: Geridos e não geridos

**O que é o maior tamanho de disco gerido suportado para o sistema operativo e discos de dados?**

O tipo de partição que o Azure suporta para o disco de sistema operativo é o registo de arranque principal (MBR). O formato do MBR suporta um tamanho de disco até 2 TiB. O maior tamanho que suporte o Azure para um disco do sistema operativo é 2 TiB. O Azure suporta até 32 TiB para discos de dados geridos no Azure global, 4 TiB em clouds independentes do Azure.

**O que é o maior tamanho de disco não gerido suportado para o sistema operativo e discos de dados?**

O tipo de partição que o Azure suporta para o disco de sistema operativo é o registo de arranque principal (MBR). O formato do MBR suporta um tamanho de disco até 2 TiB. O maior tamanho que suporte o Azure para um disco não gerido do sistema operativo é 2 TiB. O Azure suporta até 4 TiB para discos não geridos de dados.

**O que é o maior tamanho de blob de página que é suportado?**

O maior tamanho de blob de página suportados pelo Azure é 8 TiB (8,191 GiB). O tamanho do blob de páginas máxima quando anexado a uma VM, como dados ou discos do sistema operativo é 4 TiB (4095 GiB).

**É necessário utilizar uma nova versão das ferramentas do Azure para criar, anexar, redimensionar e carregar discos maiores do que 1 TiB?**

Não precisa de ferramentas do Azure existentes para criar, anexar ou redimensionar discos maiores do que 1 TiB de atualização. Para carregar o ficheiro VHD no local diretamente para o Azure como um blob de página ou o disco não gerido, tem de utilizar os conjuntos de ferramentas mais recentes listados abaixo. Suportamos apenas carregamentos VHD de até 8 TiB.

|Ferramentas do Azure      | Versões suportadas                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Número de versão 4.1.0: Versão de Junho de 2017 ou posterior|
|Azure CLI v1     | Número de versão 0.10.13: Versão de Maio de 2017 ou posterior|
|Azure CLI v2     | Número da versão 2.0.12: Versão de Julho de 2017 ou posterior|
|AzCopy           | Número de versão 6.1.0: Versão de Junho de 2017 ou posterior|

**Tamanhos de disco do P4 e P6 há suporte para discos não geridos ou blobs de páginas?**

P4 (32 GiB) e P6 (64 GiB) tamanhos de disco não são suportados como os escalões de disco predefinido para discos não geridos e blobs de páginas. Precisa explicitamente [definir a camada de Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) P4 e P6 ter seu disco mapeado para estas camadas. Se implementar um blob de página ou de disco não gerido com menos do que 32 GiB ou entre 32 GiB para 64 GiB sem definir a camada de Blob o tamanho do disco ou o comprimento do conteúdo, irá continuar firmar com base no P10 com 500 IOPS e 100 MiB/s e o escalão de preço mapeado.

**Se meu premium existente gerido disco menor do que 64 GiB foi criado antes do pequeno disco foi ativado (cerca de 15 de Junho de 2017), como é cobrada?**

Premium pequenas existente discos menor do que 64 GiB continua a ser faturado de acordo com o escalão de preço P10.

**Como posso alterar a camada de disco dos discos premium pequeno inferior a 64 GiB de P10 P4 ou P6?**

Pode tirar um instantâneo dos seus discos pequenos e, em seguida, criar um disco mude automaticamente o escalão de preço para P4 ou P6 com base no tamanho aprovisionado.

**Pode redimensionar discos geridos existentes de tamanhos de menos de 4 tebibytes (TiB) para novos tamanhos de disco recém-lançado até 32 TiB?**

Sim.

**Quais são os maiores tamanhos de disco suportados pelo serviço de cópia de segurança do Azure e o Azure Site Recovery?**

O maior tamanho de disco suportado pelo Azure Backup e o serviço Azure Site Recovery é 4 TiB.

**O que são VM recomendado tamanhos para tamanhos de disco maiores (> 4 TiB) para discos Standard HDD e Standard SSD para alcançar otimizado de disco IOPS e largura de banda?**

Para alcançar o débito de disco de tamanhos de disco grande padrão SSD e HDD padrão (> 4 TiB) para além dos 500 IOPS e 60 MiB/s, recomendamos que implantar uma nova VM a partir de um dos seguintes tamanhos de VM para otimizar o desempenho: Série B, série DSv2, série Dsv3, série ESv3, Fs-, Fsv2, série M, série GS, NCv2-séries, série NCv3 ou VMs da série Ls. Anexar discos grandes para VMs que não estiver a utilizar os tamanhos recomendados acima ou VMs existentes, pode ter um desempenho inferior.

**Como posso atualizar meus discos (> 4 TiB) que foram implementadas durante a pré-visualização de tamanhos de disco maior para obter o IOPS e largura de banda superior em disponibilidade geral?**

Pode parar e iniciar a VM que o disco está ligado à ou, desligar e voltar a anexar o disco. As metas de desempenho de tamanhos de disco maiores foram aumentadas para premium SSDs e standard SSDs em GA.

**O que regiões são os tamanhos de disco gerido de 8 TiB, TiB de 16 e 32 TiB, heterogenních doménách aplikace?**

O TiB de 8, TiB de 16 e 32 SKUs de disco de TiB são suportadas em todas as regiões em global Azure. Suporte para o Microsoft Azure Government e Azure China 21Vianet ainda não está disponível.

**Suportamos ativar a colocação em cache do anfitrião de mensagens em fila em todos os tamanhos de disco?**

Suportamos o anfitrião de colocação em cache de só de leitura e de leitura/gravação tamanhos de disco TiB inferior a 4. Para tamanhos de disco mais do que 4 TiB, não é suportado definir a opção que não seja None a colocação em cache. Recomendamos a tirar partido da colocação em cache para tamanhos de disco mais pequenos, onde pode esperar para observar o aumento do desempenho melhor com dados armazenados em cache para a VM.

## <a name="what-if-my-question-isnt-answered-here"></a>E se minha pergunta não respondida aqui?

Se a sua pergunta não está listada aqui, informe-nos e vamos ajudar a encontrar uma resposta. Pode postar uma pergunta no final deste artigo nos comentários. Para interagir com a equipa de armazenamento do Azure e outros membros da Comunidade sobre este artigo, utilize o MSDN [fórum de armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Para pedir funcionalidades, submeter os pedidos e idéias para o [fórum de comentários do armazenamento do Azure](https://feedback.azure.com/forums/217298-storage).
