---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/31/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e1cf3905a34fdced878526cfcc55e6dd0a1a369f
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82595342"
---
Este artigo responde a algumas perguntas frequentes sobre discos Azure Managed Disks e Discos SSD Azure Premium.

## <a name="managed-disks"></a>Managed Disks

**O que é Azure Managed Disks?**

A Managed Disks é uma funcionalidade que simplifica a gestão do disco para Os VMs Do Azure IaaS, manuseando a gestão da conta de armazenamento para si. Para mais informações, consulte a visão geral dos [Discos Geridos](../articles/virtual-machines/windows/managed-disks-overview.md).

**Se eu criar um disco gerido padrão a partir de um VHD existente que é de 80 GB, quanto é que isso vai me custar?**

Um disco gerido padrão criado a partir de um VHD de 80 GB é tratado como o próximo tamanho padrão de disco disponível, que é um disco S10. É cobrado de acordo com o preço do disco S10. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Existem custos de transação para discos geridos padrão?**

Sim. É cobrado por cada transação. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Para um disco gerido padrão, serei cobrado pelo tamanho real dos dados no disco ou pela capacidade provisionada do disco?**

É cobrado com base na capacidade do disco. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Como é que a fixação de preços dos discos geridos premium é diferente dos discos não geridos?**

O preço dos discos geridos premium é o mesmo que os discos premium não geridos.

**Posso alterar o tipo de conta de armazenamento (Standard ou Premium) dos meus discos geridos?**

Sim. Pode alterar o tipo de conta de armazenamento dos seus discos geridos utilizando o portal Azure, PowerShell ou o Azure CLI.

**Posso usar um ficheiro VHD numa conta de armazenamento Azure para criar um disco gerido com uma subscrição diferente?**

Sim.

**Posso usar um ficheiro VHD numa conta de armazenamento Azure para criar um disco gerido numa região diferente?**

Não.

**Existem limitações de escala para os clientes que utilizam discos geridos?**

Os Discos Geridos eliminam os limites associados às contas de armazenamento. No entanto, o limite máximo é de 50.000 discos geridos por região e por tipo de disco para uma subscrição.

**Os VMs num conjunto de disponibilidade podem consistir numa combinação de discos geridos e não geridos?**

Não. Os VMs num conjunto de disponibilidade devem utilizar todos os discos geridos ou todos os discos não geridos. Quando cria um conjunto de disponibilidade, pode escolher que tipo de discos pretende utilizar.

**Os Discos Geridos são a opção predefinida no portal Azure?**

Sim.

**Posso criar um disco vazio?**

Sim. Pode criar um disco vazio. Um disco gerido pode ser criado independentemente de um VM, por exemplo, sem o ligar a um VM.

**Qual é a contagem de domínio de falha suportada para um conjunto de disponibilidade que utiliza Discos Geridos?**

Dependendo da região onde está localizado o conjunto de disponibilidade que utiliza discos geridos, a contagem de domínio de falha suportada é de 2 ou 3.

**Como é criada a conta de armazenamento padrão para diagnósticos?**

Criou uma conta de armazenamento privada para diagnósticos vm.

**Que tipo de suporte de Controlo de Acesso baseado em Funções está disponível para discos geridos?**

Os Discos Geridos suportam três funções padrão fundamentais:

* Proprietário: Pode gerir tudo, incluindo acesso
* Contribuinte: Pode gerir tudo menos acesso
* Leitor: Pode ver tudo, mas não pode fazer alterações

**Há alguma maneira de copiar ou exportar um disco gerido para uma conta de armazenamento privado?**

Pode gerar uma assinatura de acesso partilhado de leitura (SAS) URI para o disco gerido e usá-lo para copiar o conteúdo para uma conta de armazenamento privada ou armazenamento no local. Pode utilizar o SAS URI utilizando o portal Azure, Azure PowerShell, o Azure CLI ou [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Posso criar uma cópia do meu disco gerido?**

Os clientes podem tirar uma foto dos seus discos geridos e depois usar o instantâneo para criar outro disco gerido.

**Os discos não geridos ainda são suportados?**

Sim, tanto os discos não geridos como geridos são suportados. Recomendamos que utilize discos geridos para novas cargas de trabalho e emigra as suas cargas de trabalho atuais para discos geridos.

**Posso co-localizar discos não geridos e geridos no mesmo VM?**

Não.

**Se eu criar um disco de 128 GB e depois aumentar o tamanho para 130 gibibytes (GiB), serei cobrado pelo próximo tamanho do disco (256 GiB)?**

Sim.

**Posso criar armazenamento localmente redundante, armazenamento geo-redundante e discos geridos por zonas redundantes?**

Atualmente, os Discos Geridos Azure suportam apenas discos geridos localmente redundantes.

**Posso encolher ou reduzir os meus discos geridos?**

Não. Esta funcionalidade não é suportada atualmente.

**Posso quebrar um contrato de arrendamento no meu disco?**

Não. Isto não é suportado atualmente, uma vez que está presente um contrato de arrendamento para evitar a supressão acidental quando o disco está a ser utilizado.

**Posso alterar a propriedade de nome do computador quando um disco de sistema de preparação especializado (não criado utilizando a ferramenta de preparação do sistema ou generalizado) é utilizado para fornecer um VM?**

Não. Não pode atualizar a propriedade do nome do computador. O novo VM herda-o do VM-progenitor, que foi usado para criar o disco do sistema operativo. 

**Onde posso encontrar modelos de Gestor de Recursos Azure para criar VMs com discos geridos?**
* [Lista de modelos usando discos geridos](https://github.com/Azure/azure-quickstart-templates/)
* https://github.com/chagarw/MDPP

**Ao criar um disco a partir de uma bolha, existe alguma relação continuamente existente com essa bolha de origem?**

Não, quando o novo disco é criado é uma cópia autónoma completa dessa bolha nessa altura e não há nenhuma ligação entre os dois. Se quiser, uma vez criado o disco, a bolha de origem pode ser eliminada sem afetar de qualquer forma o disco recém-criado.

**Posso mudar o nome de um disco gerido ou não gerido depois de ter sido criado?**

Para discos geridos não é possível rebatizar o nome. No entanto, pode mudar o nome de um disco não gerido desde que não esteja atualmente ligado a um VHD ou VM.

**Posso usar a partição de GPT num Disco Azure?**

As imagens da geração 1 só podem usar a divisão de GPT em discos de dados, e não em discos DES. Os discos OS devem utilizar o estilo de partição MBR.

[As imagens](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) da Geração 2 podem usar a divisão de GPT no disco OS, bem como os discos de dados.

**Que tipos de disco suportam instantâneos?**

SSD premium, SSD padrão e instantâneos de suporte HDD padrão. Para estes três tipos de disco, as imagens são suportadas para todos os tamanhos do disco (incluindo discos até 32 TiB em tamanho). Os discos ultra não suportam instantâneos.

**O que são reservas de disco Azure?**
A reserva do disco é a opção de comprar com antecedência um ano de armazenamento de disco, reduzindo o seu custo total. Para mais detalhes sobre reservas de discos Azure, consulte o nosso artigo sobre o assunto: Entenda como o seu desconto de [reserva é aplicado ao Disco Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md).

**Que opções oferece a reserva de disco Azure?**    
A reserva de disco Azure oferece a opção de comprar SSDs Premium nas SKUs especificadas de P30 (1 TiB) até P80 (32 TiB) por um período de um ano. Não existe qualquer limitação na quantidade mínima de discos necessários para comprar uma reserva de disco. Além disso, pode optar por pagar com um pagamento único, adiantado ou pagamentos mensais. Não existe um custo transacional adicional aplicado aos Discos Geridos Premium SSD.    

As reservas são feitas sob a forma de discos, não de capacidade. Por outras palavras, quando reserva um disco P80 (32 TiB), obtém-se um único disco P80, não pode então dividir essa reserva específica em dois discos P70 (16 TiB) mais pequenos. Pode, naturalmente, reservar quantos ou menos discos quiser, incluindo dois discos P70 (16 TiB) separados.

**Como é aplicada a reserva do disco Azure?**    
A reserva de discos segue um modelo semelhante aos casos de máquina virtual reservada (VM). A diferença é que uma reserva de disco não pode ser aplicada a diferentes SKUs, enquanto uma instância vm pode. Consulte [custos de poupança com as Instâncias VM reservadas](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) do Azure para obter mais informações sobre casos vm.     

**Posso usar o meu armazenamento de dados comprado através da reserva de discos Azure em várias regiões?**    
A reserva de discos Azure é comprada para uma região específica e SKU (como P30 no Leste dos EUA 2), e, portanto, não pode ser usada fora destas construções. Pode sempre adquirir uma Reserva adicional de Discos Azure para as suas necessidades de armazenamento em discos noutras regiões ou SKUs.    

**O que acontece quando a minha reserva de discos Azure expirar?**    
Receberá notificações por e-mail 30 dias antes da expiração e novamente na data de validade. Uma vez expirada a reserva, os discos implantados continuarão a ser executados e serão cobrados com as mais recentes [tarifas de pagamento.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="azure-shared-disks"></a>Discos compartilhados azure

**A funcionalidade de discos partilhados é suportada para discos não geridos ou bolhas de página?**

Não, só é suportado para discos geridos premium SSD.

**Que regiões suportam discos partilhados?**

Atualmente apenas no Centro-Oeste dos EUA.

**Os discos partilhados podem ser usados como um disco de SO?**

Não, os discos partilhados são suportados apenas para discos de dados.

**Que tamanhos de disco suportam discos partilhados?**

Apenas SSDs premium que sejam P15 ou discos partilhados de maior suporte.

**Se eu tiver um SSD premium existente, posso ativar discos partilhados nele?**

Todos os discos geridos criados com a versão API 2019-07-01 ou superior podem permitir discos partilhados. Para isso, é necessário desmontar o disco de todos os VMs a que está ligado. Em seguida, `maxShares` editar a propriedade no disco.

**Se já não quero usar um disco em modo partilhado, como o desativo?**

Desmonte o disco de todos os VMs a que está ligado. Em seguida, editar a propriedade maxShare no disco para 1.

**Pode redimensionar um disco partilhado?**

Sim.

**Posso ativar o acelerador de escrita num disco que também tem discos partilhados ativados?**

Não.

**Posso ativar o cache do hospedeiro para um disco que tenha partilhado o disco ativado?**

A única opção de cache de hospedeiro suportado é "Nenhuma".

## <a name="ultra-disks"></a>Discos Ultra

**Para que devo definir a minha entrada de disco ultra?**
Se não tiver a certeza do que definir a sua entrada de disco, recomendamos que comece por assumir um tamanho IO de 16 KiB e ajuste o desempenho a partir daí à medida que monitoriza a sua aplicação. A fórmula é: Entrada em MBps = # de IOPS * 16 / 1000.

**Configurei o meu disco para 40000 IOPS mas só estou a ver 12800 IOPS, porque não estou a ver o desempenho do disco?**
Além do acelerador do disco, há um acelerador io que é imposto ao nível de VM. Certifique-se de que o tamanho VM que está a utilizar pode suportar os níveis configurados nos seus discos. Para mais detalhes sobre os limites de IO impostos pelo seu VM, consulte [Tamanhos para máquinas virtuais Windows em Azure](../articles/virtual-machines/windows/sizes.md).

**Posso usar os níveis de cache com um disco ultra?**
Não, os discos ultra não suportam os diferentes métodos de cache que são suportados em outros tipos de disco. Desloque o cache do disco para Nenhum.

**Posso anexar um disco ultra ao meu VM existente?**
Talvez o seu VM tenha de estar numa região e num par de zonas de disponibilidade que suporta discos Ultra. Veja [começar com discos ultra](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) para mais detalhes.

**Posso usar um disco ultra como disco de SO para o meu VM?**
Não, os discos ultra são suportados apenas como discos de dados e são suportados apenas como discos nativos 4K.

**Posso converter um disco existente num disco ultra?**
Não, mas podemigrar os dados de um disco existente para um disco ultra. Para migrar um disco existente para um disco ultra, fixe ambos os discos ao mesmo VM e copie os dados do disco de um disco para o outro ou aproveite uma solução de terceiros para a migração de dados.

**Posso criar instantâneos para discos ultra?**
Não, as fotos ainda não estão disponíveis.

**O Azure Backup está disponível para discos ultra?**
Não, o suporte de backup Azure ainda não está disponível.

**Posso anexar um disco ultra a um VM em execução num conjunto de disponibilidade?**
Não, isto ainda não é apoiado.

**Posso permitir a recuperação do site Azure para VMs usando discos ultra?**
Não, a Recuperação do Site Azure ainda não é suportada para discos ultra.

## <a name="uploading-to-a-managed-disk"></a>Upload para um disco gerido

**Posso enviar dados para um disco gerido existente?**

Não, o upload só pode ser usado durante a criação de um novo disco vazio com o estado **ReadyToUpload.**

**Como faço o upload para um disco gerido?**

Crie um disco gerido com a propriedade [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) da [criaçãoData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) definido para "Upload", para depois pode fazer o upload de dados para o mesmo.

**Posso prender um disco a um VM enquanto está num estado de upload?**

Não.

**Posso tirar uma foto de um disco de manhoso num estado de upload?**

Não.

## <a name="standard-ssd-disks"></a>Discos SSD padrão

**O que são discos SSD Standard Azure?**
Os discos SSD padrão são discos standard apoiados por meios de estado sólido, otimizados como armazenamento rentável para cargas de trabalho que precisam de um desempenho consistente em níveis de IOPS mais baixos.

<a id="standard-ssds-azure-regions"></a>**Quais são as regiões atualmente suportadas para discos Standard SSD?**
Todas as regiões azure suportam agora discos Standard SSD.

**O Azure Backup está disponível quando utilizar SSDs Standard?**
Sim, o Azure Backup já está disponível.

**Como crio discos Standard SSD?**
Pode criar discos Standard SSD utilizando modelos de Gestor de Recursos Azure, SDK, PowerShell ou CLI. Abaixo estão os parâmetros necessários no modelo de Gestor de Recursos para criar discos SSD standard:

* *apiVersão* para Microsoft.Compute deve `2018-04-01` ser definido como (ou mais tarde)
* Especificar *managedDisk.storageAccountType* como`StandardSSD_LRS`

O exemplo seguinte mostra as *propriedades.storageProfile.osDisk* para um VM que utiliza discos SSD padrão:

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

Para obter um exemplo completo de como criar um disco SSD padrão com um modelo, consulte [Criar um VM a partir de uma imagem do Windows com discos de dados SSD padrão](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Posso converter os meus discos existentes em SSD padrão?**
Sim, pode. Consulte o armazenamento de discos geridos pela [Convert Azure de série para premium, e vice-versa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) para as diretrizes gerais para a conversão de Discos Geridos. E, use o seguinte valor para atualizar o tipo de disco para O SSD Padrão.
-StandardSSD_LRS de tipo de conta

**Qual é o benefício de usar discos Standard SSD em vez de HDD?**
Os discos SSD padrão proporcionam melhor latência, consistência, disponibilidade e fiabilidade em comparação com os discos HDD. As cargas de trabalho de aplicação funcionam muito mais suavemente no SSD Padrão por causa disso. Nota: Os discos Premium SSD são a solução recomendada para a maioria das cargas de trabalho de produção intensivas da OI.

**Posso usar SSDs standard como Discos Não Geridos?**
Não, os discos Standard SSDs só estão disponíveis como Discos Geridos.

**Os Discos Standard SSD suportam "VM SLA de instância única"?**
Não, os SSDs Standard não têm uma única instância VM SLA. Utilize discos SSD Premium para uma única instância VM SLA.

## <a name="migrate-to-managed-disks"></a>Migrar para o Managed Disks

**Existe algum impacto da migração no desempenho dos Discos Geridos?**

A migração envolve o movimento do Disco de um local de armazenamento para outro. Isto é orquestrado através de cópia de fundo de dados, que podem levar várias horas para completar, tipicamente menos de 24 Hrs dependendo da quantidade de dados nos discos. Durante esse tempo, a sua aplicação pode experimentar uma latência de leitura superior ao habitual, uma vez que algumas leituras podem ser redirecionadas para a localização original, e podem demorar mais tempo a ser concluídas. Não há impacto na latência escrita durante este período.  

**Que alterações são necessárias numa configuração de serviço de backup azure pré-existente antes/após a migração para Discos Geridos?**

Não são necessárias alterações.

**Os meus backups VM criados através do serviço de backup Azure antes da migração continuar a funcionar?**

Sim, os reforços funcionam perfeitamente.

**Que alterações são necessárias numa configuração de encriptação de Discos Azure pré-existente antes/após a migração para Discos Geridos?**

Não são necessárias alterações.

**A migração automatizada de uma escala virtual existente é definida de discos não geridos para discos geridos suportados?**

Não. Pode criar um novo conjunto de escala com Discos Geridos utilizando a imagem a partir do seu conjunto de escala antiga com discos não geridos.

**Posso criar um Disco Gerido a partir de uma imagem de uma imagem tirada antes de migrar para Discos Geridos?**

Não. Você pode exportar um instantâneo de página blob como uma bolha de página e, em seguida, criar um Disco Gerido a partir da bolha de página exportada.

**Posso falhar nas minhas máquinas no local protegidas pela Recuperação do Site Azure para um VM com Discos Geridos?**

Sim, pode optar por falhar num VM com Discos Geridos.

**Existe algum impacto da migração em VMs Azure protegidos pela recuperação do site Azure através da replicação azure para Azure?**

Não. Está disponível a proteção Azure para Azure para VMs com Discos Geridos.

**Posso migrar VMs com discos não geridos que estão localizados em contas de armazenamento que são ou foram previamente encriptados para discos geridos?**

Sim

## <a name="managed-disks-and-storage-service-encryption"></a>Encriptação de serviço de discos geridos e armazenamento

**A encriptação do serviço de armazenamento Azure está ativada por padrão quando crio um disco gerido?**

Sim.

**O volume de arranque é encriptado por padrão num disco gerido?**

Sim. Por predefinição, todos os discos geridos são encriptados, incluindo o disco OS.

**Quem gere as chaves de encriptação?**

A Microsoft gere as chaves de encriptação.

**Posso desativar a encriptação do serviço de armazenamento para os meus discos geridos?**

Não.

**A encriptação do serviço de armazenamento só está disponível em regiões específicas?**

Não. Está disponível em todas as regiões onde os Discos Geridos estão disponíveis. Os Discos Geridos estão disponíveis em todas as regiões públicas e na Alemanha. No entanto, também está disponível na China, apenas para chaves geridas pela Microsoft, e não para as chaves geridas pelo cliente.

**Como posso descobrir se o meu disco gerido está encriptado?**

Pode descobrir a hora em que um disco gerido foi criado a partir do portal Azure, do Azure CLI e do PowerShell. Se a hora for depois de 9 de junho de 2017, então o seu disco está encriptado.

**Como posso encriptar os meus discos existentes que foram criados antes de 10 de junho de 2017?**

A partir de 10 de junho de 2017, novos dados escritos aos discos geridos existentes são automaticamente encriptados. Também estamos a planear encriptar os dados existentes, e a encriptação vai acontecer de forma assíncrona em segundo plano. Se tiver de encriptar os dados existentes agora, crie uma cópia do seu disco. Novos discos serão encriptados.

* [Copiar discos geridos utilizando o Azure CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Copiar discos geridos utilizando powerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**As imagens e imagens geridas são encriptadas?**

Sim. Todos os instantâneos e imagens geridos criados após 9 de junho de 2017, são automaticamente encriptados. 

**Posso converter VMs com discos não geridos que estão localizados em contas de armazenamento que são ou foram previamente encriptados para discos geridos?**

Sim

**Será que um VHD exportado de um disco gerido ou um instantâneo também será encriptado?**

Não. Mas se exportar um VHD para uma conta de armazenamento encriptada a partir de um disco ou instantâneo encriptado, então está encriptado. 

## <a name="premium-disks-managed-and-unmanaged"></a>Discos premium: Geridos e não geridos

**Se um VM utilizar uma série de tamanhos que suporta discos Premium SSD, como um DSv2, posso anexar discos de dados premium e standard?** 

Sim.

**Posso anexar discos de dados premium e standard a uma série de tamanhos que não suporta discos Premium SSD, tais como séries D, Dv2, G ou F?**

Não. Só é possível anexar discos de dados padrão a VMs que não utilizem uma série de tamanhos que suporta discos SSD Premium.

**Se eu criar um disco de dados premium de um VHD existente que era de 80 GB, quanto é que isso vai custar?**

Um disco de dados premium criado a partir de um VHD de 80 GB é tratado como o próximo tamanho de disco premium disponível, que é um disco P10. É cobrado de acordo com o preço do disco P10.

**Existem custos de transação para utilizar discos Premium SSD?**

Existe um custo fixo para cada tamanho do disco, que vem provisionado com limites específicos no IOPS e na entrada. Os outros custos são a largura de banda de saída e a capacidade instantânea, se aplicável. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Quais são os limites para o IOPS e a entrada que posso obter da cache do disco?**

Os limites combinados para cache e SSD local para uma série DS são 4.000 IOPS por núcleo e 33 MiB por segundo por núcleo. A série GS oferece 5.000 IOPS por núcleo e 50 MiB por segundo por núcleo.

**O SSD local é suportado para um VM de Discos Geridos?**

O SSD local é armazenamento temporário que está incluído com um VM de Discos Geridos. Não há custo extra para este armazenamento temporário. Recomendamos que não utilize este SSD local para armazenar os seus dados de aplicação porque não é persistido no armazenamento da Blob Azure.

**Existem repercussões para a utilização de TRIM em discos premium?**

Não há nenhuma desvantagem na utilização do TRIM nos discos Azure em discos premium ou standard.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Novos tamanhos de disco: Geridoe não gerido

**Que regiões suportam a capacidade de rutura para o tamanho do disco SSD premium aplicável?**

A capacidade de rebentamento é atualmente suportada em todas as regiões da Nuvem Pública de Azure, com o apoio às nuvens soberanas em breve. 

**Em que regiões estão apoiadas 4/8/16 Tamanhos de disco geridos gib (P1/P2/P3, E1/E2/E3) suportados?**

Estes novos tamanhos de disco são atualmente suportados em todas as regiões da Nuvem Pública de Azure, com apoio às nuvens soberanas em breve. 

**Os tamanhos do disco P1/P2/P3 são suportados para discos não geridos ou bolhas de página?**

Não, só é suportado em discos geridos premium SSD. 

**Os tamanhos do disco E1/E2/E3 suportam discos não geridos ou bolhas de página?**

Não, os discos geridos padrão sSD de qualquer tamanho não podem ser usados com discos não geridos ou bolhas de página.

**Qual é o maior tamanho de disco gerido suportado para sistema operativo e discos de dados?**

O tipo de partição que o Azure suporta para um disco do sistema operativo é o registo de arranque principal (MBR). O formato MBR suporta um tamanho de disco até 2 TiB. O maior tamanho que o Azure suporta para um disco de sistema operativo é 2 TiB. O Azure suporta até 32 TiB para discos de dados geridos.

**Qual é o maior tamanho de disco não gerido suportado para sistema operativo e discos de dados?**

O tipo de partição que o Azure suporta para um disco do sistema operativo é o registo de arranque principal (MBR). O formato MBR suporta um tamanho de disco até 2 TiB. O maior tamanho que o Azure suporta para um sistema operativo Não gerido disco é 2 TiB. O Azure suporta até 4 TiB para dados de discos não geridos.

**Qual é o maior tamanho de página blob que é suportado?**

O maior tamanho de página blob que o Azure suporta é 8 TiB (8.191 GiB). O tamanho máximo da página quando ligado a um VM como discos de dados ou sistemaoperativo é 4 TiB (4.095 GiB).

**Preciso de usar uma nova versão das ferramentas Azure para criar, anexar, redimensionar e carregar discos maiores do que 1 TiB?**

Não precisa de atualizar as ferramentas Azure existentes para criar, anexar ou redimensionar discos maiores do que 1 TiB. Para fazer o upload do ficheiro VHD diretamente para o Azure como uma página blob ou um disco não gerido, precisa de utilizar os mais recentes conjuntos de ferramentas listados abaixo. Só apoiamos carregamentos VHD de até 8 TiB.

|Ferramentas Azure      | Versões suportadas                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versão número 4.1.0: Lançamento de junho de 2017 ou posterior|
|Azure CLI v1     | Versão número 0.10.13: Lançamento de maio de 2017 ou posterior|
|Azure CLI v2     | Versão número 2.0.12: Lançamento de julho de 2017 ou posterior|
|AzCopy              | Versão número 6.1.0: Lançamento de junho de 2017 ou posterior|

**Os tamanhos do disco P4 e P6 são suportados para discos não geridos ou bolhas de página?**

Os tamanhos do disco P4 (32 GiB) e P6 (64 GiB) não são suportados como os níveis de disco padrão para discos não geridos e bolhas de página. Você precisa definir explicitamente [o Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) para P4 e P6 para ter o seu disco mapeado para estes níveis. Se implantar um disco ou uma bolha de página não gerida com o tamanho ou o comprimento do conteúdo do disco inferior a 32 GiB ou entre 32 GiB a 64 GiB sem definir o Nível Blob, continuará a aterrar no P10 com 500 IOPS e 100 MiB/s e o nível de preços mapeado.

**Se o meu disco gerido pelo prémio existente foi criado com menos de 64 GiB antes do pequeno disco ter sido ativado (por volta de 15 de junho de 2017), como é cobrado?**

Os discos premium existentes inferiores a 64 GiB continuam a ser faturados de acordo com o nível de preços P10.

**Como posso mudar o nível de disco de discos premium pequenos menos de 64 GiB de P10 para P4 ou P6?**

Pode tirar uma fotografia dos seus discos pequenos e, em seguida, criar um disco para mudar automaticamente o nível de preços para P4 ou P6 com base no tamanho provisionado.

**Pode redimensionar os discos geridos existentes de tamanhos inferiores a 4 tebibytes (TiB) até aos novos tamanhos de disco recém-introduzidos até 32 TiB?**

Sim.

**Quais são os maiores tamanhos de disco suportados pelo serviço azure backup e serviço de recuperação de sites Azure?**

O maior tamanho do disco suportado pela Azure Backup é 32 TiB (4 TiB para discos encriptados). O maior tamanho do disco suportado pela Azure Site Recovery é 8 TiB. O suporte para os discos maiores até 32 TiB ainda não está disponível na Recuperação do Site Azure.

**Quais são os tamanhos de VM recomendados para tamanhos de disco maiores (>4 TiB) para discos Standard SSD e Standard HDD para alcançar iOPS e largura de banda otimizadas?**

Para obter a entrada de disco de tamanhos grandes de disco Standard SSD e Standard HDD (>4 TiB) para além de 500 IOPS e 60 MiB/s, recomendamos que implemente um novo VM a partir de um dos seguintes tamanhos VM para otimizar o seu desempenho: série B, série DSv2, Série DSV3, Série SV3, Série SF, série Fs, série SF2, série M, série GS, série NCv2, série NCv3 ou VMs da série Ls. A fixação de discos grandes a VMs ou VMs existentes que não estejam a utilizar os tamanhos recomendados acima pode experimentar um desempenho mais baixo.

**Como posso atualizar os meus discos (>4 TiB) que foram implantados durante a pré-visualização do tamanho do disco maior, a fim de obter o iOPS mais elevado & largura de banda na GA?**

Pode parar e ligar o VM a que o disco está ligado ou, desmontar e voltar a ligar o disco. Os objetivos de desempenho de tamanhos de disco maiores foram aumentados tanto para SSDs premium como SSDs padrão na GA.

**Em que regiões são suportadas as dimensões do disco gerido de 8 TiB, 16 TiB e 32 TiB?**

Os 8 TiB, 16 TiB e 32 TiB disk SKUs são suportados em todas as regiões sob o governo global de Azure, Microsoft Azure e Azure China 21Vianet.

**Apoiamos a habilitação do Hospedeiro Caching em todos os tamanhos do disco?**

Apoiamos o Recolhimento de Caching de ReadOnly e Read/Write em tamanhos de disco inferiores a 4 TiB. Para tamanhos de disco superiores a 4 TiB, não suportamos a definição de opção de cache para além de Nenhuma. Recomendamos que se reforce para tamanhos de disco mais pequenos, onde pode esperar observar um melhor aumento de desempenho com dados em cache para o VM.

## <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não for respondida aqui?

Se a sua pergunta não estiver listada aqui, avise-nos e ajudaremos a encontrar uma resposta. Pode publicar uma pergunta no final deste artigo nos comentários. Para se envolver com a equipa de Armazenamento Azure e outros membros da comunidade sobre este artigo, use o fórum de armazenamento MSDN [Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Para solicitar funcionalidades, envie os seus pedidos e ideias para o fórum de feedback do [Armazenamento Azure](https://feedback.azure.com/forums/217298-storage).
