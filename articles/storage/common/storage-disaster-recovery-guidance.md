---
title: Recuperação após desastre e ativação pós-falha de contas de armazenamento
titleSuffix: Azure Storage
description: O Azure Storage suporta a falha da conta para contas de armazenamento geo-redundantes. Com o failover da conta, pode iniciar o processo de failover para a sua conta de armazenamento se o ponto final principal ficar indisponível.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 11d9b38d71d428a3c6c829b508318389338f5a15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800354"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>Recuperação após desastre e ativação pós-falha de contas de armazenamento

A Microsoft esforça-se por garantir que os serviços Azure estão sempre disponíveis. No entanto, podem ocorrer interrupções de serviço não planeadas. Se a sua aplicação necessitar de resiliência, a Microsoft recomenda a utilização de armazenamento geo-redundante, para que os seus dados sejam copiados para uma segunda região. Além disso, os clientes devem ter um plano de recuperação de desastres em vigor para lidar com uma interrupção do serviço regional. Uma parte importante de um plano de recuperação de desastres está a preparar-se para falhar até ao ponto final secundário no caso de o ponto final principal ficar indisponível.

O Azure Storage suporta a falha da conta para contas de armazenamento geo-redundantes. Com o failover da conta, pode iniciar o processo de failover para a sua conta de armazenamento se o ponto final principal ficar indisponível. O failover atualiza o ponto final secundário para se tornar o principal ponto final da sua conta de armazenamento. Uma vez que o failover esteja concluído, os clientes podem começar a escrever para o novo ponto final primário.

A ativação pós-falha de contas está disponível para os tipos de conta de armazenamento de fins gerais v1, fins gerais v2 e de Blobs com implementações do Azure Resource Manager. A falta de contas é apoiada em todas as regiões públicas, mas não está disponível em nuvens soberanas ou nacionais neste momento. A falha da conta não é suportada para contas de armazenamento com um espaço hierárquico habilitado.

Este artigo descreve os conceitos e processos envolvidos com uma falha de conta e discute como preparar a sua conta de armazenamento para recuperação com o menor impacto do cliente. Para aprender a iniciar uma falha de conta no portal Azure ou PowerShell, consulte [Iniciar uma falha de conta](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Escolha a opção de redundância certa

O Azure Storage mantém várias cópias da sua conta de armazenamento para garantir a durabilidade e alta disponibilidade. Qual a opção de redundância que escolhe para a sua conta depende do grau de resiliência de que necessita. Para proteção contra interrupções regionais, configuure a sua conta para armazenamento geo-redundante, com ou sem opção de acesso lido a partir da região secundária:  

**O armazenamento geo-redundante (GRS) ou o armazenamento redundante de geo-zona (GZRS)** copia os seus dados assíncronamente em duas regiões geográficas que estão a pelo menos centenas de milhas de distância. Se a região primária sofre uma paragem, então a região secundária serve como uma fonte redundante para os seus dados. Pode iniciar uma falha para transformar o ponto final secundário no ponto final primário.

**O armazenamento geo-redundante de acesso à leitura (RA-GRS) ou o armazenamento redundante em zona de acesso à leitura (RA-GZRS)** proporciona armazenamento geo-redundante com o benefício adicional de acesso lido ao ponto final secundário. Se ocorrer uma paragem no ponto final primário, as aplicações configuradas para o acesso de leitura ao secundário e projetadas para uma elevada disponibilidade podem continuar a ler a partir do ponto final secundário. A Microsoft recomenda o RA-GZRS para máxima disponibilidade e durabilidade para as suas aplicações.

Para obter mais informações sobre a redundância no Azure Storage, consulte [a redundância do Azure Storage](storage-redundancy.md).

> [!WARNING]
> O armazenamento geo-redundante comporta um risco de perda de dados. Os dados são copiados para a região secundária de forma assíncrona, o que significa que há um atraso entre quando os dados escritos na região primária são escritos para a região secundária. Em caso de paralisação, as operações de escrita para o ponto final primário que ainda não tenham sido copiadas para o ponto final secundário serão perdidas.

## <a name="design-for-high-availability"></a>Criar para elevada disponibilidade

É importante projetar a sua aplicação para alta disponibilidade desde o início. Consulte estes recursos Azure para obter orientação na conceção da sua aplicação e planeamento para a recuperação de desastres:

- [Conceber aplicações resilientes para o Azure](/azure/architecture/framework/resiliency/app-design): Uma visão geral dos conceitos-chave para a arquiteta de aplicações altamente disponíveis em Azure.
- [Lista de verificação de resiliência](/azure/architecture/checklist/resiliency-per-service): Uma lista de verificação para verificar se a sua aplicação implementa as melhores práticas de design para uma elevada disponibilidade.
- [Utilizar geo-redundância para conceber aplicações altamente disponíveis](geo-redundant-design.md): Conceber orientações para aplicações de construção para tirar partido do armazenamento geo-redundante.
- [Tutorial: Construa uma aplicação altamente disponível com armazenamento Blob](../blobs/storage-create-geo-redundant-storage.md): Um tutorial que mostra como construir uma aplicação altamente disponível que alterna automaticamente entre pontos finais à medida que falhas e recuperações são simuladas. 

Além disso, tenha em mente estas boas práticas para manter a alta disponibilidade para os seus dados de Armazenamento Azure:

- **Discos:** Utilize [a Cópia de Segurança Azure](https://azure.microsoft.com/services/backup/) para fazer cópia de segurança dos discos VM utilizados pelas suas máquinas virtuais Azure. Considere também utilizar [a Recuperação do Sítio Azure](https://azure.microsoft.com/services/site-recovery/) para proteger os seus VMs em caso de desastre regional.
- **Bolhas de bloco:** Ligue [a eliminação suave](../blobs/soft-delete-blob-overview.md) para proteger contra supressões e substituições ao nível de objetos, ou copie bolhas de blocos para outra conta de armazenamento numa região diferente usando [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell,](/powershell/module/az.storage/)ou a [biblioteca Azure Data Movement](storage-use-data-movement-library.md).
- **Ficheiros:** Utilize [o Azure Backup](../../backup/azure-file-share-backup-overview.md) para fazer backup das suas ações de ficheiros. Também permita eliminar [suavemente](../files/storage-files-prevent-file-share-deletion.md) para proteger contra eliminações acidentais de partilha de ficheiros. Para a geo-redundância quando o GRS não estiver disponível, utilize [a AzCopy](./storage-use-azcopy-v10.md) ou [a Azure PowerShell](/powershell/module/az.storage/) para copiar os seus ficheiros para outra conta de armazenamento numa região diferente.
- **Quadros:** utilize [a AzCopy](./storage-use-azcopy-v10.md) para exportar dados de tabelas para outra conta de armazenamento numa região diferente.

## <a name="track-outages"></a>Interrupções nas faixas

Os clientes podem subscrever o [Azure Service Health Dashboard](https://azure.microsoft.com/status/) para acompanhar a saúde e o estado do Azure Storage e outros serviços Azure.

A Microsoft também recomenda que desenhe a sua aplicação para se preparar para a possibilidade de escrever falhas. A sua aplicação deve expor falhas de escrita de uma forma que o alerte para a possibilidade de uma paragem na região primária.

## <a name="understand-the-account-failover-process"></a>Compreender o processo de falha da conta

O failover da conta gerida pelo cliente permite-lhe falhar toda a sua conta de armazenamento na região secundária se a primária ficar indisponível por qualquer motivo. Quando forçar uma falha na região secundária, os clientes podem começar a escrever dados para o ponto final secundário após a conclusão do failover. O fracasso normalmente demora cerca de uma hora.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

### <a name="how-an-account-failover-works"></a>Como funciona a ativação pós-falha de uma conta

Em circunstâncias normais, um cliente escreve dados para uma conta de Armazenamento Azure na região primária, e esses dados são copiados assíncronamente para a região secundária. A imagem a seguir mostra o cenário quando a região primária está disponível:

![Os clientes escrevem dados para a conta de armazenamento na região primária](media/storage-disaster-recovery-guidance/primary-available.png)

Se o principal ponto final ficar indisponível por qualquer motivo, o cliente já não poderá escrever na conta de armazenamento. A imagem a seguir mostra o cenário em que a primária ficou indisponível, mas ainda não houve recuperação:

![O primário está indisponível, por isso os clientes não podem escrever dados](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

O cliente inicia a falha da conta até ao ponto final secundário. O processo de failover atualiza a entrada de DNS fornecida pela Azure Storage para que o ponto final secundário se torne o novo ponto final primário para a sua conta de armazenamento, como mostra a seguinte imagem:

![Cliente inicia falha na conta até ao ponto final secundário](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

O acesso por escrito é restaurado para contas geo-redundantes uma vez que a entrada de DNS foi atualizada e os pedidos estão sendo direcionados para o novo ponto final primário. Os pontos finais do serviço de armazenamento existentes para bolhas, mesas, filas e ficheiros permanecem os mesmos após a falha.

> [!IMPORTANT]
> Após a conclusão do failover, a conta de armazenamento é configurada para ser localmente redundante no novo ponto final primário. Para retomar a replicação do novo secundário, configuure a conta de geo-redundância novamente.
>
> Tenha em mente que a conversão de uma conta LRS para utilizar o geo-redundância incorre num custo. Este custo aplica-se à atualização da conta de armazenamento na nova região primária após um fracasso.  

### <a name="anticipate-data-loss"></a>Antecipar a perda de dados

> [!CAUTION]
> Uma falha na conta geralmente envolve alguma perda de dados. É importante entender as implicações de iniciar uma falha de conta.  

Uma vez que os dados são escritos assíncronamente da região primária para a região secundária, há sempre um atraso antes de uma escrita para a região primária ser copiada para a região secundária. Se a região primária ficar indisponível, as mais recentes escritas podem ainda não ter sido copiadas para a região secundária.

Quando se força um fracasso, todos os dados na região primária perdem-se à medida que a região secundária se torna a nova região primária e a conta de armazenamento está configurada para ser localmente redundante. Todos os dados já copiados para o secundário são mantidos quando o failover acontece. No entanto, todos os dados escritos para o primário que não tenham sido também copiados para o secundário perdem-se permanentemente.

A propriedade **Last Sync Time** indica a última vez que os dados da região primária são garantidos ter sido escritos para a região secundária. Todos os dados escritos antes da última hora de sincronização estão disponíveis no secundário, enquanto os dados escritos após a última hora de sincronização podem não ter sido escritos para o secundário e podem ser perdidos. Utilize esta propriedade em caso de paragem para estimar a quantidade de perda de dados que poderá incorrer ao iniciar uma falha na conta.

Como uma boa prática, desenhe a sua aplicação para que possa utilizar o último tempo de sincronização para avaliar a perda de dados esperada. Por exemplo, se estiver a registar todas as operações de escrita, então pode comparar o tempo das suas últimas operações de escrita com a última hora de sincronização para determinar quais as escritas que não foram sincronizadas com as secundárias.

Para obter mais informações sobre a verificação da propriedade **Last Sync Time,** consulte [a propriedade Last Sync Time para obter uma conta de armazenamento.](last-sync-time-get.md)

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Tenha cuidado ao voltar às primárias originais

Depois de ter falhado da primária para a região secundária, a sua conta de armazenamento está configurada para ser localmente redundante na nova região primária. Em seguida, pode configurar a conta para a redundância geo-redundância novamente. Quando a conta é configurada para o geo-redundância novamente após um failover, a nova região primária começa imediatamente a copiar dados para a nova região secundária, que foi a principal antes do fracasso original. No entanto, pode levar algum tempo até que os dados existentes nas primárias seja totalmente copiado para o novo secundário.

Depois de a conta de armazenamento ser reconfigurada para geo-redundância, é possível iniciar outra falha desde a nova primária de volta ao novo secundário. Neste caso, a região primária original antes do fracasso torna-se novamente a região primária, e está configurada para ser localmente redundante. Todos os dados na região primária pós-falha (o secundário original) são então perdidos. Se a maioria dos dados na conta de armazenamento não tiver sido copiado para o novo secundário antes de voltar a falhar, poderá sofrer uma grande perda de dados.

Para evitar uma grande perda de dados, verifique o valor da propriedade **Last Sync Time** antes de recuar. Compare o último tempo de sincronização com o da última vez que os dados foram escritos para a nova primária para avaliar a perda de dados esperada. 

## <a name="initiate-an-account-failover"></a>Iniciar a ativação pós-falha de uma conta

Pode iniciar uma falha de conta a partir do portal Azure, PowerShell, Azure CLI ou do fornecedor de recursos de armazenamento Azure API. Para obter mais informações sobre como iniciar uma falha, consulte [Iniciar uma falha na conta](storage-initiate-account-failover.md).

## <a name="additional-considerations"></a>Considerações adicionais

Reveja as considerações adicionais descritas nesta secção para entender como as suas aplicações e serviços podem ser afetados quando forçar uma falha.

### <a name="storage-account-containing-archived-blobs"></a>Conta de armazenamento contendo bolhas arquivadas

As contas de armazenamento que contêm bolhas arquivadas suportam falha na conta. Após a conclusão do failover, todas as bolhas arquivadas precisam de ser rehidratadas para um nível online antes que a conta possa ser configurada para geo-redundância.

### <a name="storage-resource-provider"></a>Fornecedor de recursos de armazenamento

Depois de um failover estar completo, os clientes podem voltar a ler e escrever dados de Armazenamento Azure na nova região primária. No entanto, o fornecedor de recursos de armazenamento Azure não falha, pelo que as operações de gestão de recursos devem continuar a ter lugar na região primária. Se a região primária não estiver disponível, não poderá realizar operações de gestão na conta de armazenamento.

Como o fornecedor de recursos de armazenamento Azure não falha, a propriedade [Localização](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) devolverá a localização primária original após a conclusão do failover.

### <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

As máquinas virtuais Azure (VMs) não falham como parte de uma falha de conta. Se a região primária ficar indisponível, e você falhar na região secundária, então você precisará recriar quaisquer VMs após o failover. Além disso, existe uma potencial perda de dados associada à falha da conta. A Microsoft recomenda a seguinte [orientação de alta disponibilidade](../../virtual-machines/availability.md) e [recuperação de desastres](../../virtual-machines/backup-recovery.md) específica para máquinas virtuais em Azure.

### <a name="azure-unmanaged-disks"></a>Discos não geridos de Azure

Como uma boa prática, a Microsoft recomenda a conversão de discos não geridos para discos geridos. No entanto, se precisar de falhar com uma conta que contenha discos não geridos ligados aos VMs Azure, terá de desligar o VM antes de iniciar a falha.

Os discos não geridos são armazenados como bolhas de página no Azure Storage. Quando um VM está em funcionamento em Azure, quaisquer discos não geridos ligados ao VM são alugados. Uma falha na conta não pode prosseguir quando há um arrendamento numa bolha. Para realizar o failover, siga estes passos:

1. Antes de começar, observe os nomes de quaisquer discos não geridos, os seus números de unidade lógica (LUN) e o VM ao qual estão ligados. Ao fazê-lo, facilitará a recolocação dos discos após a falha.
2. Desligue o VM.
3. Elimine o VM, mas mantenha os ficheiros VHD para os discos não geridos. Note a hora em que apagou o VM.
4. Aguarde até que a **Última Hora de Sincronização** tenha sido atualizada, e é mais tarde do que a hora em que apagou o VM. Este passo é importante, porque se o ponto final secundário não tiver sido totalmente atualizado com os ficheiros VHD quando ocorrer a falha, então o VM pode não funcionar corretamente na nova região primária.
5. Inicie o failover da conta.
6. Aguarde até que a conta esteja completa e a região secundária se tenha tornado a nova região primária.
7. Crie um VM na nova região primária e recoloque os VHDs.
8. Comece o novo VM.

Tenha em mente que todos os dados armazenados num disco temporário perdem-se quando o VM é desligado.

## <a name="unsupported-features-and-services"></a>Funcionalidades e serviços não suportados

As seguintes funcionalidades e serviços não são suportados para a falta de conta:

- O Azure File Sync não suporta o failover da conta de armazenamento. As contas de armazenamento que contêm partilhas de ficheiros do Azure e estão a ser utilizadas como pontos finais da cloud no Azure File Sync não devem efetuar a ativação pós-falha. Se a fizer, fará com que a sincronização deixe de funcionar e poderá também causar perdas de dados inesperadas em caso de ficheiros com novo escalão.
- As contas de armazenamento da ADLS Gen2 (contas que têm espaço hierárquico habilitado) não são suportadas neste momento.
- Uma conta de armazenamento que contenha bolhas de bloco premium não pode ser chumbada. As contas de armazenamento que suportam bolhas de bloco premium não suportam atualmente o geo-redundância.
- Uma conta de armazenamento que contenha qualquer [política de imutabilidade worm](../blobs/storage-blob-immutable-storage.md) habilitada a contentores não pode ser chumbada. As políticas de retenção ou detenção por tempo desbloqueado/bloqueados impedem a sua insusitada falha, a fim de manter o cumprimento.

## <a name="copying-data-as-an-alternative-to-failover"></a>Copiar dados como alternativa à ativação pós-falha

Se a sua conta de armazenamento estiver configurada para ler o acesso ao secundário, então pode desenhar a sua aplicação para ler a partir do ponto final secundário. Se preferir não falhar em caso de paragem na região primária, pode utilizar ferramentas como [a AzCopy,](./storage-use-azcopy-v10.md) [Azure PowerShell](/powershell/module/az.storage/)ou a [biblioteca Azure Data Movement](../common/storage-use-data-movement-library.md) para copiar dados da sua conta de armazenamento na região secundária para outra conta de armazenamento numa região não afetada. Em seguida, pode apontar as suas aplicações para essa conta de armazenamento para ler e escrever disponibilidade.

> [!CAUTION]
> Uma falha na conta não deve ser utilizada como parte da sua estratégia de migração de dados.

## <a name="microsoft-managed-failover"></a>Falha gerida pela Microsoft

Em circunstâncias extremas em que uma região se perde devido a um desastre significativo, a Microsoft pode iniciar um fracasso regional. Neste caso, não é necessária qualquer ação da sua parte. Até que o failover gerido pela Microsoft esteja concluído, não terá acesso à sua conta de armazenamento. As suas aplicações podem ser lidas na região secundária se a sua conta de armazenamento estiver configurada para RA-GRS ou RA-GZRS.

## <a name="see-also"></a>Ver também

- [Use geo-redundância para projetar aplicações altamente disponíveis](geo-redundant-design.md)
- [Iniciar a ativação pós-falha de uma conta](storage-initiate-account-failover.md)
- [Verifique a propriedade da Última Hora do Sincronização para obter uma conta de armazenamento](last-sync-time-get.md)
- [Tutorial: Construa uma aplicação altamente disponível com armazenamento Blob](../blobs/storage-create-geo-redundant-storage.md)