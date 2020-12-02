---
title: Perguntas frequentes sobre discos
description: Perguntas frequentes sobre discos VM Azure IaaS Linux e discos premium (geridos e não geridos)
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: dcfef81f2d7f3413489490d97c143fdec7e11bed
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499327"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Perguntas mais frequentes sobre discos de VM IaaS do Azure e discos premium geridos e não geridos

Este artigo responde a algumas perguntas frequentes sobre discos geridos Azure e discos SSD Azure Premium.

## <a name="managed-disks"></a>Managed Disks

**O que é Azure Managed Disks?**

A Managed Disks é uma funcionalidade que simplifica a gestão de discos para Azure IaaS VMs, manuseando a gestão de conta de armazenamento para si. Para mais informações, consulte a [visão geral dos Discos Geridos.](managed-disks-overview.md)

**Se eu criar um disco gerido padrão a partir de um VHD existente que é 80 GB, quanto é que isso me vai custar?**

Um disco gerido padrão criado a partir de um VHD de 80 GB é tratado como o próximo tamanho padrão disponível do disco, que é um disco S10. É cobrado de acordo com o preço do disco S10. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Existem custos de transação para discos geridos padrão?**

Sim. É cobrado por cada transação. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Para um disco gerido padrão, serei cobrado pelo tamanho real dos dados no disco ou pela capacidade prevista do disco?**

És cobrado com base na capacidade do disco. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Como é que os preços dos discos geridos premium são diferentes dos discos não geridos?**

O preço dos discos geridos premium é o mesmo que discos premium não geridos.

**Posso alterar o tipo de conta de armazenamento (Standard ou Premium) dos meus discos geridos?**

Sim. Pode alterar o tipo de conta de armazenamento dos seus discos geridos utilizando o portal Azure, o PowerShell ou o Azure CLI.

**Posso usar um ficheiro VHD numa conta de armazenamento Azure para criar um disco gerido com uma subscrição diferente?**

Sim.

**Posso usar um ficheiro VHD numa conta de armazenamento Azure para criar um disco gerido numa região diferente?**

Não.

**Existem limitações de escala para clientes que usam discos geridos?**

Os Discos Geridos eliminam os limites associados às contas de armazenamento. No entanto, o limite máximo é de 50.000 discos geridos por região e por tipo de disco para uma subscrição.

**Os VMs num conjunto de disponibilidade podem consistir numa combinação de discos geridos e não geridos?**

Não. Os VMs num conjunto de disponibilidade devem utilizar todos os discos geridos ou todos os discos não geridos. Quando criar um conjunto de disponibilidade, pode escolher que tipo de discos pretende utilizar.

**Os Discos Geridos são a opção padrão no portal Azure?**

Sim.

**Posso criar um disco gerido vazio?**

Sim. Pode criar um disco vazio. Um disco gerido pode ser criado independentemente de um VM, por exemplo, sem o ligar a um VM.

**Qual é a contagem de domínio de avaria suportada para um conjunto de disponibilidade que utiliza Discos Geridos?**

Dependendo da região onde está localizado o conjunto de disponibilidade que utiliza Discos Geridos, a contagem de domínio de avaria suportada é de 2 ou 3.

**Como é criada a conta de armazenamento padrão para diagnósticos?**

Criou uma conta de armazenamento privado para diagnósticos de VM.

**Que tipo de suporte de controlo de acesso baseado em funções Azure está disponível para Discos Geridos?**

Os Discos Geridos suportam três funções-chave predefinidos:

* Proprietário: Pode gerir tudo, incluindo acesso
* Contribuinte: Pode gerir tudo, exceto acesso
* Leitor: Pode ver tudo, mas não pode fazer alterações

**Há alguma maneira de copiar ou exportar um disco gerido para uma conta de armazenamento privado?**

Pode gerar uma assinatura de acesso partilhado (SAS) URI apenas para o disco gerido e usá-lo para copiar o conteúdo para uma conta de armazenamento privado ou armazenamento no local. Pode utilizar o SAS URI utilizando o portal Azure PowerShell, o Azure CLI ou [AzCopy](../storage/common/storage-use-azcopy-v10.md)

**Posso criar uma cópia do meu disco gerido?**

Os clientes podem tirar uma foto dos seus discos geridos e, em seguida, usar o instantâneo para criar outro disco gerido.

**Os discos não geridos ainda são suportados?**

Sim, tanto discos não geridos como geridos são suportados. Recomendamos que utilize discos geridos para novas cargas de trabalho e migrar as cargas de trabalho atuais para discos geridos.

**Posso arranjar discos não geridos e geridos no mesmo VM?**

Não.

**Se eu criar um disco de 128 GB e depois aumentar o tamanho para 130 gibibytes (GiB), serei cobrado para o próximo tamanho do disco (256 GiB)?**

Sim.

**Posso criar armazenamento localmente redundante, armazenamento geo-redundante e discos geridos de armazenamento redundante?**

A Azure Managed Disks suporta atualmente apenas discos geridos de armazenamento redundantes locais.

**Posso encolher ou reduzir os meus discos?**

Não. Esta funcionalidade não é suportada atualmente.

**Posso quebrar um contrato de arrendamento no meu disco?**

Não. Isto não é suportado atualmente como um arrendamento está presente para evitar a eliminação acidental quando o disco está sendo usado.

**Posso alterar a propriedade do nome do computador quando um disco de sistema operativo especializado (não criado por utilização da ferramenta de preparação do sistema ou generalizado) é utilizado para a provisionação de um VM?**

Não. Não pode atualizar a propriedade do nome do computador. O novo VM herda-o do VM-mãe, que foi usado para criar o disco do sistema operativo. 

**Onde posso encontrar modelos de gestor de recursos Azure para criar VMs com discos geridos?**
* [Lista de modelos que usam Discos Geridos](https://github.com/Azure/azure-quickstart-templates/)
* https://github.com/chagarw/MDPP

**Ao criar um disco a partir de uma bolha, existe alguma relação continuamente existente com essa bolha de origem?**

Não, quando o novo disco é criado é uma cópia autónoma completa dessa bolha naquele momento e não há nenhuma ligação entre os dois. Se quiser, uma vez criado o disco, a bolha de origem pode ser eliminada sem afetar de forma alguma o disco recém-criado.

**Posso mudar o nome de um disco gerido ou não gerido depois de ter sido criado?**

Para discos geridos não é possível renomeá-los. No entanto, pode mudar o nome de um disco não gerido, desde que não esteja atualmente ligado a um VHD ou VM.

**Posso usar a partição gpt num disco Azure?**

As imagens da geração 1 só podem usar partição GPT em discos de dados, não em discos DE. Os discos de OS devem utilizar o estilo de partição MBR.

[As imagens da geração 2](./generation-2.md) podem utilizar a partição GPT no disco OS, bem como os discos de dados.

**Que tipos de disco suportam instantâneos?**

Premium SSD, SSD padrão e fotos de suporte HDD padrão. Para estes três tipos de discos, as imagens são suportadas para todos os tamanhos do disco (incluindo discos até 32 TiB em tamanho). Os discos ultra não suportam instantâneos.

**O que são reservas de disco Azure?**
A reserva em disco é a opção de comprar um ano de armazenamento em disco com antecedência, reduzindo o seu custo total. Para mais informações sobre reservas de disco Azure, consulte o nosso artigo sobre o assunto: [Entenda como o seu desconto de reserva é aplicado ao Disco Azure.](../cost-management-billing/reservations/understand-disk-reservations.md)

**Que opções oferece a reserva de disco Azure?**    
A reserva de disco Azure oferece a opção de comprar SSDs Premium nos SKUs especificados de P30 (1 TiB) até P80 (32 TiB) por um período de um ano. Não há limitação na quantidade mínima de discos necessários para comprar uma reserva de disco. Além disso, pode optar por pagar com um pagamento único, antecipado ou mensal. Não existe um custo transacional adicional aplicado aos Discos Geridos Premium SSD.    

As reservas são feitas sob a forma de discos, não de capacidade. Por outras palavras, quando reserva um disco P80 (32 TiB), obtém-se um único disco P80, não pode então dividir essa reserva específica em dois discos P70 (16 TiB) mais pequenos. Pode, naturalmente, reservar quantos ou poucos discos quiser, incluindo dois discos P70 (16 TiB) separados.

**Como é aplicada a reserva de disco Azure?**    
A reserva de discos segue um modelo semelhante aos casos de máquina virtual reservada (VM). A diferença é que uma reserva de disco não pode ser aplicada a diferentes SKUs, enquanto uma instância VM pode. Consulte [os custos de poupança com Azure Reserved VM Instances](./prepay-reserved-vm-instances.md) para obter mais informações sobre as instâncias VM.     

**Posso usar o meu armazenamento de dados adquirido através da reserva de discos Azure em várias regiões?**    
A reserva de discos Azure é comprada para uma região específica e SKU (como P30 no Leste dos EUA 2), e, portanto, não pode ser usado fora destas construções. Pode sempre adquirir uma Reserva adicional de Discos Azure para as suas necessidades de armazenamento de discos noutras regiões ou SKUs.    

**O que acontece quando a minha reserva de discos Azure expirar?**    
Receberá notificações por e-mail 30 dias antes do termo e novamente na data de validade. Uma vez expirada a reserva, os discos implantados continuarão a funcionar e serão cobrados com as mais recentes tarifas de pagamento à [sua utilização](https://azure.microsoft.com/pricing/details/managed-disks/).

**Os Discos SSD Standard suportam "SLA VM SLA de instância única"?**

Sim, todos os tipos de disco suportam VM SLA de instância única.

### <a name="azure-shared-disks"></a>Discos partilhados do Azure

**A funcionalidade de discos partilhados é suportada para discos não geridos ou bolhas de página?**

Não, só é suportado para discos ultra e discos geridos premium SSD.

**Que regiões suportam discos partilhados?**

Para obter informações regionais, consulte o nosso [artigo conceptual.](disks-shared.md)

**Os discos partilhados podem ser usados como disco DE?**

Não, os discos partilhados só são suportados para discos de dados.

**Que tamanhos de disco suportam discos partilhados?**

Para tamanhos suportados, consulte o nosso [artigo conceptual.](disks-shared.md)

**Se eu tiver um disco existente, posso permitir discos partilhados nele?**

Todos os discos geridos criados com a versão API 2019-07-01 ou superior podem permitir discos partilhados. Para isso, é necessário desmontar o disco de todos os VMs aos que está ligado. Em seguida, edite a propriedade **maxShares** no disco.

**Se já não quero usar um disco em modo partilhado, como posso desativá-lo?**

Desmonte o disco de todos os VMs aos que está ligado. Em seguida, edite a propriedade maxShare no disco para 1.

**Pode redimensionar um disco partilhado?**

Sim.

**Posso permitir escrever um acelerador num disco que também tenha discos partilhados ativados?**

Não.

**Posso permitir o cache do anfitrião para um disco que tenha partilhado o disco ativado?**

A única opção de caching de hospedeiro suportado é **Nenhuma**.

## <a name="ultra-disks"></a>Discos Ultra

**Para que devo definir a minha produção ultra disco?**
Se não tiver a certeza para que definir a sua produção de disco, recomendamos que comece assumindo um tamanho IO de 16 KiB e ajuste o desempenho a partir daí à medida que monitoriza a sua aplicação. A fórmula é: Produção em MBps = # de IOPS * 16 / 1000.

**Configurado o meu disco para 40000 IOPS mas só estou a ver 12800 IOPS, porque não estou a ver a performance do disco?**
Além do acelerador do disco, existe um acelerador IO que é imposto ao nível de VM. Certifique-se de que o tamanho VM que está a utilizar pode suportar os níveis configurados nos seus discos. Para obter detalhes sobre os limites de IO impostos pelo seu VM, consulte [tamanhos para máquinas virtuais em Azure](sizes.md).

**Posso usar níveis de cache com um disco ultra?**
Não, os discos ultra não suportam os diferentes métodos de cache que são suportados noutros tipos de disco. Desa estatuçar o disco a **Nenhum**.

**Posso anexar um disco ultra ao meu VM existente?**
Talvez o seu VM tenha de estar numa região e um par de zonas de disponibilidade que suporte discos Ultra. Veja [como começar com discos ultra](disks-enable-ultra-ssd.md) para mais detalhes.

**Posso usar um disco ultra como disco de so para o meu VM?**
Não, os discos ultra são suportados apenas como discos de dados e são suportados apenas como discos nativos 4K.

**Posso converter um disco existente num disco ultra?**
Não, mas pode migrar os dados de um disco existente para um disco ultra. Para migrar um disco existente para um disco ultra, ligue ambos os discos ao mesmo VM e copie os dados do disco de um disco para o outro ou aproveite uma solução de terceiros para a migração de dados.

**Posso criar fotos para discos ultra?**
Não, as fotos ainda não estão disponíveis.

**A Azure Backup está disponível para discos ultra?**
Não, o suporte de backup do Azure ainda não está disponível.

**Posso anexar um disco ultra a um VM a funcionar num conjunto de disponibilidade?**
Não, isto ainda não está apoiado.

**Posso permitir a recuperação do site Azure para VMs usando discos ultra?**
Não, a Recuperação do Local Azure ainda não está suportada para discos ultra.

## <a name="uploading-to-a-managed-disk"></a>Upload para um disco gerido

**Posso enviar dados para um disco gerido existente?**

Não, o upload só pode ser utilizado durante a criação de um novo disco vazio com o estado **ReadyToUpload.**

**Como faço o upload para um disco gerido?**

Crie um disco gerido com a propriedade [createOption](/rest/api/compute/disks/createorupdate#diskcreateoption) da [criaçãoData](/rest/api/compute/disks/createorupdate#creationdata) definido para "Upload", então pode enviar dados para o mesmo.

**Posso anexar um disco a um VM enquanto está em estado de upload?**

Não.

**Posso tirar uma foto de um disco manged em estado de upload?**

Não.

## <a name="standard-ssd-disks"></a>Discos SSD standard

**O que são discos SSD Standard Azure?**
Os discos SSD standard são discos standard apoiados por meios de comunicação de estado sólido, otimizados como armazenamento eficaz em termos de custos para cargas de trabalho que precisam de um desempenho consistente em níveis de IOPS mais baixos.

<a id="standard-ssds-azure-regions"></a>**Quais são as regiões atualmente suportadas para discos SSD standard?**
Todas as regiões Azure agora suportam discos SSD standard.

**O Azure Backup está disponível quando se utiliza SSDs Standard?**
Sim, a Azure Backup já está disponível.

**Qual é o benefício de utilizar discos SSD standard em vez de HDD?**
Os discos SSD standard proporcionam uma melhor latência, consistência, disponibilidade e fiabilidade em comparação com os discos HDD. As cargas de trabalho das aplicações funcionam muito mais suavemente no Standard SSD por causa disso. Nota: Os discos Premium SSD são a solução recomendada para a maioria das cargas de trabalho de produção intensivas de IO.

**Posso usar SSDs padrão como discos não geridos?**
Não, os discos SSDs standard só estão disponíveis como Discos Geridos.

## <a name="migrate-to-managed-disks"></a>Migrar para o Managed Disks

**Existe algum impacto da migração no desempenho dos Discos Geridos?**

A migração envolve a deslocação do Disco de um local de armazenamento para outro. Isto é orquestrado através de cópia de fundo de dados, que pode levar várias horas para ser concluído, tipicamente menos de 24 Hrs dependendo da quantidade de dados nos discos. Durante esse tempo, a sua aplicação pode experimentar uma latência de leitura superior ao habitual, uma vez que algumas leituras podem ser redirecionadas para o local original, podendo demorar mais tempo a ser concluída. Durante este período, não há qualquer impacto na latência da escrita.  

**Que alterações são necessárias numa configuração de serviço de backup Azure pré-existente antes/após a migração para Discos Geridos?**

Não são necessárias alterações.

**Os meus backups VM criados através do serviço Azure Backup antes da migração continuar a funcionar?**

Sim, os reforços funcionam perfeitamente.

**Que alterações são necessárias numa configuração de encriptação de discos Azure pré-existentes antes/após a migração para Discos Geridos?**

Não são necessárias alterações.

**A migração automatizada de uma escala de máquina virtual existente definida de discos não geridos para Discos Geridos é suportada?**

Não. Pode criar um conjunto de escala nova com Discos Geridos utilizando a imagem do seu conjunto de escala antiga com discos não geridos.

**Posso criar um Disco Gerido a partir de uma imagem de bolha de página tirada antes de migrar para Discos Geridos?**

Não. Pode exportar uma imagem de bolha de página como uma bolha de página e, em seguida, criar um Disco Gerido a partir da bolha de página exportada.

**Posso falhar nas minhas máquinas no local protegidas pela Azure Site Recovery a um VM com Discos Geridos?**

Sim, pode optar por falhar num VM com Discos Geridos.

**Existe algum impacto da migração nos VMs Azure protegidos pela Recuperação do Sítio Azure via Azure para a replicação do Azure?**

Não. Azure Site Recovery Azure to Azure protection for VMs with Managed Disks está disponível.

**Posso migrar VMs com discos não geridos que estão localizados em contas de armazenamento que são ou foram previamente encriptadas para discos geridos?**

Sim

## <a name="managed-disks-and-storage-service-encryption"></a>Encriptação de Discos Geridos e Serviço de Armazenamento

**A encriptação do lado do servidor é ativada por padrão quando crio um disco gerido?**

Sim. Os Discos Geridos são encriptados com encriptação do lado do servidor com teclas geridas pela plataforma. 

**O volume de arranque é encriptado por padrão num disco gerido?**

Sim. Por padrão, todos os discos geridos são encriptados, incluindo o disco SO.

**Quem gere as chaves de encriptação?**

As chaves geridas pela plataforma são geridas pela Microsoft. Também pode utilizar e gerir as suas próprias chaves armazenadas no Cofre da Chave Azure. 

**Posso desativar a encriptação do lado do servidor para os meus discos geridos?**

Não.

**A encriptação do lado do servidor só está disponível em regiões específicas?**

Não. A encriptação do lado do servidor com as chaves geridas pela plataforma e pelo cliente estão disponíveis em todas as regiões onde os Discos Geridos estão disponíveis. 

**A Recuperação do Site Azure suporta encriptação do lado do servidor com chave gerida pelo cliente para cenários de recuperação de desastres no local para Azure e Azure para cenários de recuperação de desastres do Azure?**

Sim. 

**Posso fazer backup de Discos Geridos encriptados com encriptação do lado do servidor com chave gerida pelo cliente utilizando o serviço de backup Azure?**

Sim.

**São encriptadas imagens e imagens geridas?**

Sim. Todas as imagens e imagens geridas são automaticamente encriptadas. 

**Posso converter VMs com discos não geridos que estão localizados em contas de armazenamento que são ou foram previamente encriptadas para discos geridos?**

Sim

**Será que um VHD exportado de um disco gerido ou de um instantâneo também será encriptado?**

Não. Mas se exportar um VHD para uma conta de armazenamento encriptada a partir de um disco ou instantâneo gerido encriptado, então está encriptado. 

## <a name="premium-disks-managed-and-unmanaged"></a>Discos premium: Geridos e não geridos

**Se um VM utilizar uma série de tamanhos que suporte discos Premium SSD, como um DSv2, posso anexar discos de dados premium e standard?** 

Sim.

**Posso anexar discos de dados premium e padrão a uma série de tamanho que não suporta discos Premium SSD, tais como séries D, Dv2, G ou F?**

Não. Só é possível anexar discos de dados padrão a VMs que não utilizem uma série de tamanhos que suporte discos SSD Premium.

**Se eu criar um disco de dados premium a partir de um VHD existente que foi de 80 GB, quanto é que isso vai custar?**

Um disco de dados premium criado a partir de um VHD de 80 GB é tratado como o próximo tamanho de disco premium disponível, que é um disco P10. É cobrado de acordo com o preço do disco P10.

**Existem custos de transação para utilizar discos Premium SSD?**

Há um custo fixo para cada tamanho do disco, que vem previsto com limites específicos para o IOPS e a produção. Os outros custos são a largura de banda de saída e a capacidade de instantâneo, se aplicável. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Quais são os limites para o IOPS e a produção que posso obter da cache do disco?**

Os limites combinados para cache e SSD local para uma série DS são 4.000 IOPS por núcleo e 33 MiB por segundo por núcleo. A série GS oferece 5.000 IOPS por núcleo e 50 MiB por segundo por núcleo.

**O SSD local é suportado para um VM de Discos Geridos?**

O SSD local é um armazenamento temporário que está incluído com um VM de Discos Geridos. Não há nenhum custo extra para este armazenamento temporário. Recomendamos que não utilize este SSD local para armazenar os dados da sua aplicação porque não persiste no armazenamento da Azure Blob.

**Existem repercussões na utilização de TRIM em discos premium?**

Não há desvantagem na utilização de TRIM em discos Azure em discos premium ou standard.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Novos tamanhos do disco: Gerido e não gerido

**Que regiões suportam a capacidade de rebentamento do tamanho do disco SSD premium aplicável?**

A capacidade de explosão é atualmente suportada em todas as regiões da Nuvem Pública de Azure, com apoio para nuvens soberanas em breve. 

**Em que regiões são suportadas as dimensões do disco gerido 4/8/16 GiB (P1/P2/P3, E1/E2/E3) ?**

Estes novos tamanhos de disco são atualmente suportados em todas as regiões da Nuvem Pública de Azure, com apoio para nuvens soberanas em breve. 

**Os tamanhos do disco P1/P2/P3 são suportados para discos não geridos ou bolhas de página?**

Não, só é suportado em discos geridos por SSD premium. 

**Os tamanhos do disco E1/E2/E3 são suportados para discos não geridos ou bolhas de página?**

Não, os discos geridos padrão SSD de qualquer tamanho não podem ser utilizados com discos não geridos ou bolhas de página.

**Qual é o maior tamanho do disco gerido suportado para sistema operativo e discos de dados em VMs da Gen1?**

O tipo de partição que o Azure suporta para os discos do sistema operativo Gen1 é o registo principal de arranque (MBR). Embora os discos Gen1 OS apenas suportem MBR, os discos de dados suportam GPT. Embora possa alocar até um disco de 4 TiB OS, o tipo de partição MBR só pode utilizar até 2 TiB deste espaço de disco para o sistema operativo. O Azure suporta até 32 TiB para discos de dados geridos.

**Qual é o maior tamanho do disco gerido suportado para sistema operativo e discos de dados em GMs Gen2?**

O tipo de partição que o Azure suporta para os discos do sistema operativo Gen2 é a Tabela de Partição GUID (GPT). Os VMs da Gen2 suportam até um disco 4 TiB OS. O Azure suporta até 32 TiB para discos de dados geridos.


**Qual é o maior tamanho de disco não gerido suportado para sistema operativo e discos de dados?**

O tipo de partição que o Azure suporta para um disco do sistema operativo utilizando discos não geridos é o registo principal de arranque (MBR).  Embora possa alocar até um disco de 4 TiB OS, o tipo de partição MBR só pode utilizar até 2 TiB deste espaço de disco para o sistema operativo. O Azure suporta até 4 TiB para discos de dados não geridos.


**Qual é o maior tamanho de bolha de página que é suportado?**

O maior tamanho de blob de página que a Azure suporta é 8 TiB (8.191 GiB). O tamanho máximo da bolha da página quando anexado a um VM como discos de dados ou sistema operativo é 4 TiB (4.095 GiB).

**Preciso de usar uma nova versão de ferramentas Azure para criar, anexar, redimensionar e carregar discos maiores que 1 TiB?**

Não precisa de atualizar as ferramentas Azure existentes para criar, prender ou redimensionar discos maiores do que 1 TiB. Para fazer o upload do seu ficheiro VHD de instalações diretamente para Azure como uma bolha de página ou disco não gerido, precisa de utilizar os mais recentes conjuntos de ferramentas listados abaixo. Só apoiamos uploads de VHD de até 8 TiB.

|Ferramentas do Azure      | Versões suportadas                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versão número 4.1.0: Lançamento de junho de 2017 ou posterior|
|Azure CLI v1     | Versão número 0.10.13: Lançamento de maio de 2017 ou posterior|
|Azure CLI v2     | Versão número 2.0.12: Lançamento de julho de 2017 ou posterior|
|AzCopy              | Versão número 6.1.0: Lançamento de junho de 2017 ou posterior|

**Os tamanhos do disco P4 e P6 são suportados para discos não geridos ou bolhas de página?**

Os tamanhos do disco P4 (32 GiB) e P6 (64 GiB) não são suportados como os níveis de disco predefinidos para discos e bolhas de página não geridos. É necessário definir explicitamente [o Blob Tier](/rest/api/storageservices/set-blob-tier) para P4 e P6 para ter o disco mapeado para estes níveis. Se implementar um disco ou uma bolha de página não geridos com o tamanho do disco ou comprimento de conteúdo inferior a 32 GiB ou entre 32 GiB e 64 GiB sem definir o Blob Tier, continuará a aterrar em P10 com 500 IOPS e 100 MiB/s e o nível de preços mapeado.

**Se o meu disco gerido com prémios a menos de 64 GiB foi criado antes do pequeno disco ser ativado (por volta de 15 de junho de 2017), como é cobrado?**

Os discos premium existentes com menos de 64 GiB continuam a ser faturados de acordo com o nível de preços P10.

**Como posso mudar o nível de disco de pequenos discos premium menos de 64 GiB de P10 para P4 ou P6?**

Pode tirar uma fotografia dos seus pequenos discos e, em seguida, criar um disco para mudar automaticamente o nível de preços para P4 ou P6 com base no tamanho previsto.

**Pode redimensionar os Discos Geridos existentes de tamanhos inferiores a 4 tebibytes (TiB) para novos tamanhos de disco recém-introduzidos até 32 TiB?**

Sim.

**Quais são os maiores tamanhos de disco suportados pelo serviço de backup de backup e Azure Site Recovery?**

O maior tamanho do disco suportado pelo Azure Backup é 32 TiB (4 TiB para discos encriptados). O maior tamanho do disco suportado pela Azure Site Recovery é 8 TiB. O suporte para discos maiores até 32 TiB ainda não está disponível na Recuperação do Site Azure.

**Quais são os tamanhos VM recomendados para tamanhos de disco maiores (>4 TiB) para discos Standard SSD e HDD standard para obter IOPS e largura de banda otimizada?**

Para obter a produção de discos de tamanhos de disco standard SSD e HDD standard (>4 TiB) para além de 500 IOPS e 60 MiB/s, recomendamos que implemente um novo VM de um dos seguintes tamanhos VM para otimizar o seu desempenho: série B, série DSv2, Série Dsv3, Série ESv3, Série F, Série Fsv2, Série M, Série GS, NCv2 série, série NCv3 ou VMs da série Ls. A fixação de discos grandes a VMs ou VMs existentes que não estejam a utilizar os tamanhos recomendados acima pode ter um desempenho mais baixo.

**Como posso atualizar os meus discos (>4 TiB) que foram implantados durante a pré-visualização dos tamanhos de disco maiores, a fim de obter a largura de banda & de IOPS mais alta em GA?**

Pode parar e iniciar o VM ao qual o disco está ligado ou, separar e voltar a prender o disco. Os objetivos de desempenho de tamanhos de disco maiores foram aumentados tanto para SSDs premium como para SSDs padrão em GA.

**Em que regiões são suportados os tamanhos de disco geridos de 8 TiB, 16 TiB e 32 TiB?**

Os SKUs de disco 8 TiB, 16 TiB e 32 TiB são suportados em todas as regiões sob o governo global de Azure, Microsoft Azure e Azure China 21Vianet.

**Apoiamos ativar o Caching do Anfitrião em todos os tamanhos dos discos?**

O Caching do Anfitrião **(ReadOnly** e **Read/Write)** é suportado em tamanhos de disco inferiores a 4 TiB. Isto significa que qualquer disco que esteja previsto até 4095 GiB pode tirar partido do Hospedeiro Caching. O cache do anfitrião não é suportado para tamanhos de disco mais ou iguais ou iguais a 4096 GiB. Por exemplo, um disco premium P50 a provisionado em 4095 GiB pode tirar partido do cache do hospedeiro e um disco P50 provisionado em 4096 GiB não pode tirar partido do Cache hospedeiro. Recomendamos alavancagem de cache para tamanhos de disco mais pequenos, onde pode esperar observar um melhor aumento de desempenho com dados em cache para o VM.

## <a name="private-links-for-securely-exporting-and-importing-managed-disks"></a>Links privados para exportar e importar discos geridos de forma segura

**Qual é o benefício da utilização de links privados para exportação e importação de Discos Geridos?**

Pode aproveitar as Ligações Privadas para restringir a exportação e importação de Discos Geridos apenas a partir da sua rede virtual Azure. 

**O que posso assegurar de que um disco possa ser exportado ou importado apenas através de ligações privadas?**

Você deve definir a `DiskAccessId` propriedade para uma instância de um objeto de acesso em disco e também definir a propriedade NetworkAccessPolicy para `AllowPrivate` .

**Posso ligar várias redes virtuais ao mesmo objeto de acesso ao disco?**

Não. Atualmente, pode ligar um objeto de acesso ao disco a apenas uma rede virtual.

**Posso ligar uma rede virtual a um objeto de acesso a disco noutra subscrição?**

Não. Atualmente, pode ligar um objeto de acesso a disco a uma rede virtual na mesma subscrição.

**Posso ligar uma rede virtual a um objeto de acesso a disco noutra subscrição?**

Não. Atualmente, pode ligar um objeto de acesso a disco a uma rede virtual na mesma subscrição.

**Quantas exportações ou importações que utilizam o mesmo objeto de acesso ao disco podem acontecer ao mesmo tempo?**

5

**Posso utilizar um SAS URI de um disco/instantâneo para descarregar o VHD subjacente de um VM na mesma sub-rede que a sub-rede do ponto final privado associado ao disco?**

Sim.

**Posso utilizar um SAS URI de um disco/instantâneo para descarregar o VHD subjacente de um VM não na mesma sub-rede que a sub-rede do ponto final privado não associado ao disco?**

Não.

## <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não for respondida aqui?

Se a sua pergunta não estiver listada aqui, avise-nos e nós o ajudaremos a encontrar uma resposta. Pode colocar uma pergunta no final deste artigo nos comentários. Para se envolver com a equipa de Armazenamento Azure e outros membros da comunidade sobre este artigo, utilize a [página de perguntas do Microsoft Q&A para o Azure Storage](/answers/products/azure?product=storage).

Para solicitar funcionalidades, envie os seus pedidos e ideias para o [fórum de feedback do Azure Storage](https://feedback.azure.com/forums/217298-storage).