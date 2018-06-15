---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: rogara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 812f11a1ced3bac765441bf66f402abb4da4bc3f
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2018
ms.locfileid: "35414574"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Perguntas mais frequentes sobre os discos de VM do IaaS do Azure e os discos premium geridas e não geridas

Este artigo responde a algumas perguntas mais frequentes sobre discos discos gerida do Azure e do Azure Premium SSD.

## <a name="managed-disks"></a>Managed Disks

**O que é discos gerida do Azure?**

Discos geridos é uma funcionalidade que simplifica a gestão de discos para VMs IaaS do Azure, processando gestão de contas de armazenamento para si. Para obter mais informações, consulte o [descrição geral de discos geridos](../articles/virtual-machines/windows/managed-disks-overview.md).

**Se criar um disco gerido standard de um VHD existente que é 80 GB, quanto será que custo-me?**

Um disco gerido standard criado a partir de um VHD de 80 GB é tratado como o tamanho de disco padrão disponível seguinte, o que é um disco de S10. Está a cobrado, de acordo com os preços de disco de S10. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Existem quaisquer custos de transação para discos geridos padrão?**

Sim. Está a cobrado para cada transação. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Para um disco gerido standard, será posso cobrado para o tamanho real dos dados no disco ou para a capacidade do disco aprovisionada?**

Está a cobrados com base na capacidade do disco aprovisionada. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Como é preços dos discos premium gerido diferente dos discos não geridos?**

Os preços dos discos premium gerido é o mesmo que os discos premium não gerido.

**Pode alterar o tipo de conta de armazenamento (Standard ou Premium) do meu discos geridos?**

Sim. Pode alterar o tipo de conta de armazenamento dos seus discos geridos utilizando o portal do Azure, PowerShell ou a CLI do Azure.

**Pode utilizar um ficheiro VHD de uma conta de armazenamento do Azure para criar um disco gerido com uma subscrição diferente?**

Sim.

**Pode utilizar um ficheiro VHD de uma conta de armazenamento do Azure para criar um disco gerido numa região diferente?**

Não.

**Existem algumas limitações de dimensionamento para os clientes que utilizam discos geridos?**

Discos geridos elimina os limites associados a contas de armazenamento. No entanto, o limite máximo é de 50 000 discos geridos por região e por tipo de disco para uma subscrição.

**Pode tirar um instantâneo incremental de um disco gerido?**

Não. A capacidade de instantâneos atual faz uma cópia completa de um disco gerido.

**VMs num conjunto de disponibilidade podem consistir de uma combinação de discos geridos e?**

Não. As VMs num conjunto de disponibilidade tem de utilizar discos de todos os geridos ou todos os discos não geridos. Quando cria um conjunto de disponibilidade, pode escolher o tipo de discos que pretende utilizar.

**É discos geridos a opção predefinida no portal do Azure?**

Sim. 

**Pode criar um disco vazio gerido?**

Sim. Pode criar um disco vazio. Um disco gerido pode ser criado independentemente de uma VM, por exemplo, sem a ligá-la a uma VM.

**O que é o número de domínios de falhas suportado para um disponibilidade definir que utiliza discos geridos?**

Consoante a região onde está localizado o conjunto de disponibilidade que utiliza discos geridos, o número de domínios de falhas suportado é de 2 ou 3.

**Como é a conta de armazenamento standard para configurar o diagnóstico?**

Configurar uma conta de armazenamento privada para diagnósticos da VM.

**Que tipo de suporte de controlo de acesso baseado em funções está disponível para discos geridos?**

Gerido funções do discos suporta três predefinido de chaves:

* O proprietário: Podem gerir tudo, incluindo o acesso
* Contribuidor: Podem gerir tudo, exceto acesso
* Leitor: Podem ver tudo, mas não é possível efetuar alterações

**Existe alguma forma posso pode copiar ou exportar um disco gerido para uma conta de armazenamento privada?**

Pode gerar uma assinatura de só de leitura de acesso partilhado (SAS) URI para o disco gerida e utilizá-lo para copiar o conteúdo para um armazenamento de conta ou no local de armazenamento privada. Pode utilizar o URI de SAS através do portal do Azure, Azure PowerShell, a CLI do Azure ou [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Pode criar uma cópia do meu disco gerido?**

Os clientes podem tirar um instantâneo os respetivos discos geridos e, em seguida, utilize o instantâneo para criar outro disco gerido.

**Os discos não geridos ainda são suportados?**

Sim, são suportados discos de não geridos e geridos. Recomendamos que utilize discos geridos para novas cargas de trabalho e migre as cargas de trabalho atuais para discos geridos.


**Se criar um disco de 128 GB e, em seguida, aumentar o tamanho da 130 GB, será posso cobrado para o próximo tamanho de disco (GB de 512)?**

Sim.

**Posso criar armazenamento localmente redundante, armazenamento georredundante, e discos geridos pelo armazenamento com redundância de zona?**

Discos gerida do Azure suporta atualmente os discos de armazenamento apenas localmente redundante gerido.

**Pode reduzir ou downsize meu discos geridos?**

Não. Esta funcionalidade não é suportada atualmente. 

**Pode dividir uma concessão no meu disco?**

Não. Isto não é suportado atualmente como uma concessão está presente para evitar a eliminação acidental quando o disco está a ser utilizado.

**Posso alterar a propriedade de nome do computador quando um especializadas (não criada utilizando a ferramenta de preparação do sistema ou generalizado) o disco do sistema operativo é utilizado para Aprovisionar uma VM?**

Não. Não é possível atualizar a propriedade de nome de computador. A VM nova herda a VM, que foi utilizado para criar o disco de sistema operativo principal. 

**Onde posso encontrar modelos do Azure Resource Manager de exemplo para criar as VMs com discos geridos?**
* [Lista de modelos utilizando discos geridos](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

## <a name="standard-ssd-disks-preview"></a>Discos SSD padrão (pré-visualização)

**Quais são os discos de SSD padrão do Azure?**
Discos SSD padrão são apoiados por suporte de dados de estado sólido, com a otimização de armazenamento económico para cargas de trabalho que necessitam de um desempenho consistente em níveis inferiores de IOPS de discos padrão. Pré-visualização, estão disponíveis num número limitado de regiões, com capacidade de gestão limitada (disponível através de modelos do Resource Manager).

<a id="standard-ssds-azure-regions"></a>**Quais são as regiões suportadas atualmente para discos de Standard SSD (pré-visualização)?**
* Europa do Norte

**Como posso criar discos padrão SSD?**
Atualmente, pode criar discos de SSD padrão com modelos Azure Resource Manager. Seguem-se os parâmetros necessários no modelo do Resource Manager para criar discos padrão de SSD:

* *apiVersion* para Microsoft. Compute tem de ser definido como `2018-04-01` (ou posterior)
* Especifique *managedDisk.storageAccountType* como `StandardSSD_LRS`

O seguinte exemplo mostra o *properties.storageProfile.osDisk* secção para uma VM que utiliza discos de SSD padrão:

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

Para obter um exemplo de modelo completo de como criar um disco Standard SSD com um modelo, consulte [criar uma VM a partir de uma imagem do Windows com discos de dados padrão do SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Pode utilizar SSDs padrão como discos não gerido?**
Não, os discos padrão SSDs só estão disponíveis como discos geridos.

## <a name="migrate-to-managed-disks"></a>Migrar para o Managed Disks 

**As alterações que são necessárias no pré-existente cópia de segurança do Azure configuração anterior/após a migração de um serviço em discos geridos?**

Não são necessárias alterações. 

**A minha cópias de segurança VM criadas através do serviço de cópia de segurança do Azure antes da migração continuarão a funcionar?**

Sim, as cópias de segurança funcionam sem problemas.

**As alterações que são necessárias numa pré-existente encriptação de discos do Azure antes/após a migração de configuração de discos geridos?**

Não são necessárias alterações. 

**É a migração automática de um dimensionamento da máquina virtual existente conjuntos de discos não geridos para gerido discos suportados?**

Não. Pode criar um novo conjunto com discos geridos utilizando a imagem a partir do seu conjunto com os discos não geridos de dimensionamento antigo de dimensionamento. 

**Pode criar um disco geridos de um instantâneo de blob de página efetuado antes de migrar discos geridos?**

Não. Pode exportar um instantâneo de blob de página como um blob de página e, em seguida, criar um disco gerido a partir do blob de página exportado. 

**Pode falhar ao longo do meu máquinas no local protegidas pelo Azure Site Recovery para uma VM com discos geridos?**

Sim, pode escolher a ativação pós-falha para uma VM com discos geridos.

**Existe qualquer impacto da migração em VMs do Azure protegida pelo Azure Site Recovery através de replicação do Azure para o Azure?**

Sim. Atualmente, a proteção do Azure para o Azure do Azure Site Recovery para VMs com discos geridos só está disponível como um serviço de pré-visualização pública.

**Pode migrar VMs com discos não geridos que estão localizados em contas de armazenamento que estão ou que foram anteriormente encriptadas para discos geridos?**

Sim

## <a name="managed-disks-and-storage-service-encryption"></a>Geridos discos e a encriptação do serviço de armazenamento 

**É encriptação do serviço de armazenamento do Azure ativada por predefinição quando criar um disco gerido?**

Sim.

**Quem gere as chaves de encriptação?**

Microsoft gere as chaves de encriptação.

**Pode desativar encriptação do serviço de armazenamento para os meus discos geridos?**

Não.

**Encriptação do serviço de armazenamento só está disponível em regiões específicas?**

Não. Está disponível em todas as regiões onde discos geridos estão disponíveis. Discos geridos está disponível em todas as regiões públicas e na Alemanha.

**Como posso saber se se o meu disco gerido é encriptado?**

Pode encontrar a hora quando um disco gerido foi criado a partir do portal do Azure, a CLI do Azure e o PowerShell. Se a hora de 9 de Junho de 2017, o disco está encriptado. 

**Como encriptar o meu discos existentes que foram criados antes de 10 de Junho de 2017?**

A partir de 10 de Junho de 2017, os novos dados escritos em discos geridos existentes é encriptados automaticamente. Podemos também estiver a planear para encriptar os dados existentes e a encriptação irá acontecer de forma assíncrona em segundo plano. Se tem de encriptar dados existentes agora, crie uma cópia do seu disco. Novos discos serão encriptados.

* [Copiar discos geridos utilizando a CLI do Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Copiar discos geridos utilizando o PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**São gerido instantâneos e imagens encriptadas?**

Sim. Gerido todos os instantâneos e as imagens criadas após 9 de Junho de 2017, são encriptadas automaticamente. 

**Pode converter VMs com discos não geridos que estão localizados em contas de armazenamento que estão ou que foram anteriormente encriptadas para discos geridos?**

Sim

**Será um VHD exportado a partir de um disco gerido ou um instantâneo também será encriptado?**

Não. Mas se exportar um VHD para uma conta de armazenamento encriptada de uma encriptados geridos disco ou um instantâneo, em seguida, é encriptada. 

## <a name="premium-disks-managed-and-unmanaged"></a>Os discos Premium: geridos e não geridas

**Se uma VM utiliza uma série de tamanho que suporta discos Premium SSD, tais como uma série DSv2, posso anexar os discos de dados standard e premium?** 

Sim.

**Posso anexar os discos de dados standard e premium a uma série de tamanho não suporta discos Premium SSD, tais como a série de D, Dv2, G ou F?**

Não. Pode anexar apenas discos de dados standard para VMs que não utilizem uma série de tamanho que suporta discos Premium SSD.

**Se criar um disco de dados premium a partir de um VHD existente que estava 80 GB, quanto será que custo?**

Um disco de dados premium criado a partir de um VHD de 80 GB é tratado como o tamanho do disco premium seguinte disponíveis, que é um disco de P10. Está a cobrado, de acordo com os preços de disco de P10.

**Existem custos de transação para utilizar discos Premium SSD?**

Não há um custo fixo para cada tamanho de disco, o que é aprovisionado com limites específicos no IOPS e débito. Os custos de outros são largura de banda de saída e a capacidade de instantâneos, se aplicável. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Quais são os limites de IOPS e débito que pode receber de cache do disco?**

Os limites combinados para a cache e o SSD local para uma série DS são 4000 IOPS por núcleos e 33 MB por segundo por núcleo. A série GS oferece 5000 IOPS por núcleos e 50 MB por segundo por núcleo.

**O SSD local é suportado para uma VM de discos geridos?**

O SSD local é armazenamento temporário que está incluído com uma VM de discos geridos. Existe um custo extra para este armazenamento temporário. Recomendamos que utilize este SSD local para armazenar os dados de aplicação porque este não é continuada no Blob storage do Azure.

**Existem quaisquer repercussions para a utilização de operações de COMPACTAÇÃO no discos premium?**

Não há nenhum downside a utilização de operações de COMPACTAÇÃO nos discos do Azure premium o ou os discos padrão.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Novos tamanhos de disco: geridos e não geridas

**O que é o maior tamanho de disco suportado pelo sistema operativo e os discos de dados?**

O tipo de partição que suporta o Azure para um disco de sistema operativo é o registo de arranque principal (MBR). O suporta de formato MBR um disco tamanho até 2 TB. O tamanho maior que suporta o Azure para um disco de sistema operativo é 2 TB. Azure suporta até 4 TB para discos de dados. 

**O que é o maior tamanho do blob de página que é suportado?**

O maior tamanho do blob de página que suporte do Azure é de 8 TB (8,191 GB). O tamanho de blogue de página maxmium quando ligado a uma VM, como dados ou discos de sistema operativo é 4 TB (4,095 GB).

**É necessário utilizar uma nova versão das ferramentas do Azure para criar, anexar, redimensionar e carregar discos superiores a 1 TB?**

Não precisa de atualizar as ferramentas do Azure existentes para criar e anexar ou redimensionar discos superiores a 1 TB. Para carregar o ficheiro VHD no local diretamente para o Azure como um blob de página ou disco não gerido, tem de utilizar os conjuntos de ferramenta mais recentes:

|Ferramentas do Azure      | Versões suportadas                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Número de versão 4.1.0: versão de Junho de 2017 ou posterior|
|CLI do Azure v1     | Número de versão 0.10.13: versão de Maio de 2017 ou posterior|
|AzCopy           | Número de versão 6.1.0: versão de Junho de 2017 ou posterior|

O suporte para v2 CLI do Azure e o Explorador de armazenamento do Azure estará disponível brevemente. 

**P4 e P6 tamanhos de disco são suportados para os discos não geridos ou blobs de página?**

Não. P4 (32 GB) e P6 tamanhos de disco (64 GB) são suportados apenas para discos geridos. Suporte para discos não geridos e blobs de páginas está disponível em breve.

**Se a minha premium existente gerido disco inferior a 64 GB foi criado antes do disco pequeno foi ativado (em torno do dia 15 de Junho de 2017), como é é faturada?**

Premium pequeno existente discos inferior a 64 GB continuar a ser faturada, de acordo com o escalão de preço P10. 

**Como posso mudar a camada de disco de discos premium pequeno inferior a 64 GB de P10 P4 ou P6?**

Pode tirar um instantâneo os discos pequenos e, em seguida, criar um disco para mudar automaticamente o escalão de preço para P4 ou P6 com base no tamanho aprovisionado. 


## <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não é atendida aqui?

Se a sua pergunta não está listada aqui, informe-nos e vamos ajudá-lo a encontrar uma resposta. Pode colocar uma pergunta no final deste artigo nos comentários. Para interagir com o agrupamento de armazenamento do Azure e outros membros da Comunidade sobre neste artigo, utilize o MSDN [fórum de armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Para pedir as funcionalidades, submeter os pedidos e ideias para o [fórum de comentários do Storage do Azure](https://feedback.azure.com/forums/217298-storage).
