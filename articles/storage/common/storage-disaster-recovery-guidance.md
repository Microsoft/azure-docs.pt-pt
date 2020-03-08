---
title: Falha na conta de recuperação e armazenamento de desastres (pré-visualização)
titleSuffix: Azure Storage
description: O Azure Storage suporta a falha da conta (pré-visualização) para contas de armazenamento georedundantes. Com o failover da conta, pode iniciar o processo de failover para a sua conta de armazenamento se o ponto final principal ficar indisponível.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 8442d3f7ed3e73dc5d7358a9bc1d3ee31d7668cd
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894532"
---
# <a name="disaster-recovery-and-account-failover-preview"></a>Recuperação de desastres e falha na conta (pré-visualização)

A Microsoft esforça-se por garantir que os serviços Do Azure estão sempre disponíveis. No entanto, podem ocorrer interrupções de serviço não planeadas. Se a sua aplicação necessitar de resiliência, a Microsoft recomenda a utilização de armazenamento geo-redundante, para que os seus dados sejam copiados para uma segunda região. Além disso, os clientes devem ter um plano de recuperação de desastres em vigor para lidar com uma interrupção do serviço regional. Uma parte importante de um plano de recuperação de desastres está a preparar-se para falhar até ao ponto final secundário no caso de o ponto final principal ficar indisponível.

O Azure Storage suporta a falha da conta (pré-visualização) para contas de armazenamento georedundantes. Com o failover da conta, pode iniciar o processo de failover para a sua conta de armazenamento se o ponto final principal ficar indisponível. O failover atualiza o ponto final secundário para se tornar o ponto final principal da sua conta de armazenamento. Uma vez concluída a falha, os clientes podem começar a escrever para o novo ponto final primário.

Este artigo descreve os conceitos e processos envolvidos com uma falha de conta e discute como preparar a sua conta de armazenamento para recuperação com o menor impacto do cliente. Para aprender a iniciar uma falha de conta no portal Azure ou powerShell, consulte Iniciar uma falha de [conta (pré-visualização)](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Escolha a opção de redundância certa

O Azure Storage mantém várias cópias da sua conta de armazenamento para garantir durabilidade e elevada disponibilidade. A opção de redundância que escolher para a sua conta depende do grau de resiliência de que precisa. Para proteção contra interrupções regionais, escolha armazenamento geo-redundante, com ou sem opção de ler o acesso da região secundária:  

**O armazenamento geo-redundante (GRS) ou o armazenamento geo-zona redundante (GZRS) (pré-visualização)** copiam os seus dados assincronicamente em duas regiões geográficas que estão a pelo menos centenas de quilómetros de distância. Se a região primária sofrer uma paragem, então a região secundária serve como uma fonte redundante para os seus dados. Pode iniciar uma falha para transformar o ponto final secundário no ponto final primário.

**O armazenamento geo-redundante de acesso de leitura (RA-GRS) ou o armazenamento geo-zona-redundante de acesso à leitura (RA-GZRS) (pré-visualização)** fornecem armazenamento geo-redundante com o benefício adicional de ler o acesso ao ponto final secundário. Se ocorrer uma paragem no ponto final primário, as aplicações configuradas para RA-GRS e concebidas para alta disponibilidade podem continuar a ler a partir do ponto final secundário. A Microsoft recomenda RA-GRS para máxima resiliência para as suas aplicações.

Para obter mais informações sobre redundância no Armazenamento Azure, consulte o [despedimento do Azure Storage.](storage-redundancy.md)

> [!WARNING]
> O armazenamento geo-redundante apresenta um risco de perda de dados. Os dados são copiados para a região secundária assincronicamente, o que significa que há um atraso entre quando os dados escritos para a região primária são escritos para a região secundária. Em caso de paralisação, as operações de escrita para o ponto final primário que ainda não foram copiadas para o ponto final secundário serão perdidas.

## <a name="design-for-high-availability"></a>Criar para elevada disponibilidade

É importante projetar a sua aplicação para alta disponibilidade desde o início. Consulte estes recursos Azure para orientação na conceção da sua aplicação e planeamento para a recuperação de desastres:

- [Conceber aplicações resilientes para o Azure](/azure/architecture/checklist/resiliency-per-service): Uma visão geral dos conceitos-chave para a arquiteta de aplicações altamente disponíveis em Azure.
- [Lista de verificação](/azure/architecture/checklist/resiliency-per-service)de disponibilidade : Uma lista de verificação para verificar se a sua aplicação implementa as melhores práticas de design para alta disponibilidade.
- [Conceber aplicações altamente disponíveis utilizando RA-GRS](storage-designing-ha-apps-with-ragrs.md): Orientação de design para aplicações de construção para tirar partido do RA-GRS.
- [Tutorial: Construa uma aplicação altamente disponível com armazenamento Blob](../blobs/storage-create-geo-redundant-storage.md): Um tutorial que mostra como construir uma aplicação altamente disponível que alterna automaticamente entre pontos finais à medida que as falhas e recuperações são simuladas. 

Além disso, tenha em mente estas boas práticas para manter a elevada disponibilidade para os seus dados de Armazenamento Azure:

- **Discos:** Utilize [o Backup Azure](https://azure.microsoft.com/services/backup/) para fazer backup os discos VM utilizados pelas suas máquinas virtuais Azure. Considere também utilizar a Recuperação do [Site Azure](https://azure.microsoft.com/services/site-recovery/) para proteger os seus VMs em caso de desastre regional.
- **Bolhas de bloco:** Ligue [o soft delete](../blobs/storage-blob-soft-delete.md) para proteger contra supressões e substituições ao nível de objetos, ou copiar bolhas de blocos para outra conta de armazenamento numa região diferente utilizando [a AzCopy,](storage-use-azcopy.md) [Azure PowerShell](storage-powershell-guide-full.md)ou a biblioteca do Movimento de [Dados Azure.](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- **Ficheiros:** Utilize [o AzCopy](storage-use-azcopy.md) ou [o Azure PowerShell](storage-powershell-guide-full.md) para copiar os seus ficheiros para outra conta de armazenamento numa região diferente.
- **Tabelas:** utilize a [AzCopy](storage-use-azcopy.md) para exportar dados de tabelas para outra conta de armazenamento numa região diferente.

## <a name="track-outages"></a>Interrupções na pista

Os clientes podem subscrever o Painel de [Saúde do Serviço Azure](https://azure.microsoft.com/status/) para acompanhar a saúde e o estado do Armazenamento Azure e outros serviços Azure.

A Microsoft também recomenda que desenhe a sua aplicação para se preparar para a possibilidade de escrever falhas. A sua aplicação deve expor falhas de escrita de uma forma que o alerte para a possibilidade de uma interrupção na região primária.

## <a name="understand-the-account-failover-process"></a>Compreender o processo de falha da conta

A falha na conta gerida pelo cliente (pré-visualização) permite-lhe falhar toda a sua conta de armazenamento na região secundária se a primária ficar indisponível por qualquer motivo. Quando se força uma falha na região secundária, os clientes podem começar a escrever dados para o ponto final secundário após a falha estar completa. O fracasso normalmente demora cerca de uma hora.

### <a name="how-an-account-failover-works"></a>Como funciona uma falha de conta

Em circunstâncias normais, um cliente escreve dados para uma conta de Armazenamento Azure na região primária, e esses dados são copiados assíncronamente para a região secundária. A imagem que se segue mostra o cenário quando a região primária está disponível:

![Clientes escrevem dados para a conta de armazenamento na região primária](media/storage-disaster-recovery-guidance/primary-available.png)

Se o ponto final principal ficar indisponível por qualquer motivo, o cliente já não é capaz de escrever na conta de armazenamento. A imagem que se segue mostra o cenário em que as primárias ficaram indisponíveis, mas ainda não aconteceu qualquer recuperação:

![A primária está indisponível, por isso os clientes não podem escrever dados](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

O cliente inicia a falha da conta no ponto final secundário. O processo de failover atualiza a entrada de DNS fornecida pelo Armazenamento Azure para que o ponto final secundário se torne o novo ponto final primário para a sua conta de armazenamento, como mostra a seguinte imagem:

![Cliente inicia falha de conta no ponto final secundário](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

O acesso por escrito é restaurado para contas GRS e RA-GRS uma vez que a entrada de DNS foi atualizada e os pedidos estão sendo direcionados para o novo ponto final primário. Os pontos finais do serviço de armazenamento existentes para bolhas, mesas, filas e ficheiros permanecem os mesmos após a falha.

> [!IMPORTANT]
> Após a falha estar completa, a conta de armazenamento está configurada para ser localmente redundante no novo ponto final primário. Para retomar a replicação ao novo secundário, configure a conta para voltar a utilizar o armazenamento geo-redundante (seja RA-GRS ou GRS).
>
> Tenha em mente que a conversão de uma conta LRS para RA-GRS ou GRS incorre num custo. Este custo aplica-se à atualização da conta de armazenamento na nova região primária para utilizar RA-GRS ou GRS após uma falha.  

### <a name="anticipate-data-loss"></a>Antecipar a perda de dados

> [!CAUTION]
> Uma falha de conta geralmente envolve alguma perda de dados. É importante entender as implicações de dar início a uma conta.  

Uma vez que os dados são escritos assincronicamente da região primária para a região secundária, há sempre um atraso antes de uma escrita para a região primária ser copiada para a região secundária. Se a região primária ficar indisponível, as mais recentes escritas podem ainda não ter sido copiadas para a região secundária.

Quando se força uma falha, todos os dados na região primária perdem-se à medida que a região secundária se torna a nova região primária e a conta de armazenamento está configurada para ser localmente redundante. Todos os dados já copiados para o secundário são mantidos quando a falha acontece. No entanto, quaisquer dados escritos para as primárias que também não foram copiados para o secundário perdem-se permanentemente.

A propriedade **Last Sync Time** indica o momento mais recente em que os dados da região primária são garantidos ter sido escritos para a região secundária. Todos os dados escritos antes do último tempo de sincronização estão disponíveis no secundário, enquanto os dados escritos após o último tempo de sincronização podem não ter sido escritos para o secundário e podem ser perdidos. Utilize esta propriedade em caso de interrupção para estimar o valor da perda de dados que poderá incorrer ao dar início a uma falha na conta.

Como uma boa prática, desenhe a sua aplicação para que possa usar o último tempo de sincronização para avaliar a perda de dados esperada. Por exemplo, se estiver a registar todas as operações de escrita, pode comparar o tempo das suas últimas operações de escrita com o último tempo de sincronização para determinar quais as escritas que não foram sincronizadas com o secundário.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Tenha cuidado ao não voltar à primária original

Depois de falhar desde a primeira até à região secundária, a sua conta de armazenamento está configurada para ser localmente redundante na nova região primária. Pode configurar novamente a conta de georedundância atualizando-a para utilizar GRS ou RA-GRS. Quando a conta é configurada para o geo-redundância novamente após uma falha, a nova região primária começa imediatamente a copiar dados para a nova região secundária, que foi a primária antes da falha original. No entanto, pode levar algum tempo até que os dados existentes na primária sejam totalmente copiados para o novo secundário.

Depois de a conta de armazenamento ser reconfigurada para geo-redundância, é possível iniciar outra falha desde a nova primária de volta ao novo secundário. Neste caso, a região primária original antes da falha torna-se novamente a região primária, e está configurada para ser localmente redundante. Todos os dados da região primária pós-failover (o secundário original) são então perdidos. Se a maioria dos dados na conta de armazenamento não tiver sido copiado para o novo secundário antes de falhar, poderá sofrer uma grande perda de dados.

Para evitar uma grande perda de dados, verifique o valor da propriedade **Do Último Tempo sincronizado** antes de falhar. Compare o último tempo de sincronização com as últimas vezes que os dados foram escritos para a nova primária para avaliar a perda de dados esperada. 

## <a name="initiate-an-account-failover"></a>Iniciar a ativação pós-falha de uma conta

Pode iniciar uma falha de conta a partir do portal Azure, PowerShell, Azure CLI ou do fornecedor de recursos azure Storage API. Para obter mais informações sobre como iniciar uma falha, consulte Iniciar uma falha de [conta (pré-visualização)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Sobre a pré-visualização

O failover da conta está disponível na pré-visualização para todos os clientes que utilizem GRS ou RA-GRS com implementações do Gestor de Recursos Azure. Os tipos de contas de armazenamento v1 de uso geral, v2 de propósito geral e de depósito blob são suportados. O failover da conta está atualmente disponível em todas as regiões públicas. A falha da conta não está disponível em nuvens soberanas/nacionais neste momento.

A pré-visualização destina-se apenas à utilização não produção. Os acordos de nível de serviço de produção (SLAs) não estão atualmente disponíveis.

### <a name="additional-considerations"></a>Considerações adicionais

Reveja as considerações adicionais descritas nesta secção para entender como as suas aplicações e serviços podem ser afetados quando força uma falha durante o período de pré-visualização.

#### <a name="storage-account-containing-archived-blobs"></a>Conta de armazenamento contendo bolhas arquivadas

Contas de armazenamento contendo blobs arquivados suportam falha na conta. Uma vez concluída a falha, para converter a conta de volta a GRS ou RA-GRS, todas as bolhas archieved precisam de ser rehidratadas a um nível online primeiro.

#### <a name="storage-resource-provider"></a>Fornecedor de recursos de armazenamento

Depois de uma falha estar completa, os clientes podem voltar a ler e escrever dados do Armazenamento Azure na nova região primária. No entanto, o fornecedor de recursos de armazenamento Azure não falha, pelo que as operações de gestão de recursos devem continuar a decorrer na região primária. Se a região primária não estiver disponível, não poderá realizar operações de gestão na conta de armazenamento.

Uma vez que o fornecedor de recursos de armazenamento Azure não falha, a propriedade [de Localização](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) devolverá a localização primária original após a falha estar completa.

#### <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

As máquinas virtuais Azure (VMs) não falham como parte de uma falha de conta. Se a região primária ficar indisponível, e falhar na região secundária, terá de recriar quaisquer VMs após a falha. Além disso, existe uma potencial perda de dados associada à falha da conta. A Microsoft recomenda a seguinte [orientação](../../virtual-machines/windows/manage-availability.md) de alta disponibilidade e recuperação de [desastres](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md) específica para máquinas virtuais em Azure.

#### <a name="azure-unmanaged-disks"></a>Discos não geridos do Azure

Como uma boa prática, a Microsoft recomenda a conversão de discos não geridos para discos geridos. No entanto, se precisar de falhar numa conta que contenha discos não geridos ligados aos VMs Azure, terá de desligar o VM antes de dar início à falha.

Os discos não geridos são armazenados como bolhas de página no Armazenamento Azure. Quando um VM está em execução em Azure, quaisquer discos não geridos ligados ao VM são alugados. Uma falha de conta não pode prosseguir quando há um arrendamento numa bolha. Para realizar a falha, siga estes passos:

1. Antes de começar, note os nomes de quaisquer discos não geridos, os seus números de unidade lógica (LUN) e o VM a que estão ligados. Ao fazê-lo, será mais fácil voltar a colocar os discos após a falha.
2. Desligue o VM.
3. Elimine o VM, mas guarde os ficheiros VHD para os discos não geridos. Tenha em anotado o tempo em que apagou o VM.
4. Aguarde até que o **Último Tempo de Sincronização** seja atualizado e seja mais tarde do que o momento em que apagou o VM. Este passo é importante, porque se o ponto final secundário não tiver sido totalmente atualizado com os ficheiros VHD quando a falha ocorre, então o VM pode não funcionar corretamente na nova região primária.
5. Inicie a falha da conta.
6. Aguarde até que a conta falhe e a região secundária se torne a nova região primária.
7. Crie um VM na nova região primária e religue os VHDs.
8. Inicie o novo VM.

Tenha em mente que todos os dados armazenados num disco temporário se perdem quando o VM é desligado.

### <a name="unsupported-features-and-services"></a>Recursos e serviços não suportados

As seguintes funcionalidades e serviços não são suportados para a falha da conta para a versão de pré-visualização:

- O Azure File Sync não suporta a falha da conta de armazenamento. As contas de armazenamento que contêm partilhas de ficheiros do Azure e estão a ser utilizadas como pontos finais da cloud no Azure File Sync não devem efetuar a ativação pós-falha. Se a fizer, fará com que a sincronização deixe de funcionar e poderá também causar perdas de dados inesperadas em caso de ficheiros com novo escalão.
- As contas de armazenamento da ADLS Gen2 (contas que têm espaço de nome hierárquico habilitado) não são suportadas neste momento.
- Uma conta de armazenamento contendo bolhas de blocopremium não pode ser chumbada. As contas de armazenamento que suportam bolhas de blocopremium não suportam atualmente a georedundância.
- Uma conta de armazenamento que contenha qualquer política de [imutabilidade WORM](../blobs/storage-blob-immutable-storage.md) ativada por recipientes não pode ser chumbada. Políticas de retenção ou detenção legal desbloqueadas/bloqueadas impedem a falha, a fim de manter o cumprimento.
- Após a falha estar completa, as seguintes funcionalidades podem deixar de funcionar se ativadas originalmente: Assinaturas de [eventos,](../blobs/storage-blob-event-overview.md) [Change Feed,](../blobs/storage-blob-change-feed.md)Políticas de Ciclo de [Vida](../blobs/storage-lifecycle-management-concepts.md)e Registo de [Armazenamento Analytics](storage-analytics-logging.md).

## <a name="copying-data-as-an-alternative-to-failover"></a>Copiar dados como alternativa ao failover

Se a sua conta de armazenamento estiver configurada para RA-GRS, então leu o acesso aos seus dados utilizando o ponto final secundário. Se preferir não falhar em caso de avaria na região primária, pode utilizar ferramentas como [a AzCopy,](storage-use-azcopy.md) [Azure PowerShell](storage-powershell-guide-full.md)ou a biblioteca do Movimento de [Dados Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar dados da sua conta de armazenamento na região secundária para outra conta de armazenamento numa região não afetada. Em seguida, pode apontar as suas aplicações para essa conta de armazenamento tanto para ler como escrever disponibilidade.

> [!CAUTION]
> Uma falha de conta não deve ser usada como parte da sua estratégia de migração de dados.


## <a name="microsoft-managed-failover"></a>Falha gerida pela Microsoft

Em circunstâncias extremas em que uma região se perde devido a um desastre significativo, a Microsoft pode iniciar uma falha regional. Neste caso, não é necessária qualquer ação da sua parte. Até que a falha gerida pela Microsoft esteja concluída, não terá acesso à sua conta de armazenamento. As suas aplicações podem ser lidas a partir da região secundária se a sua conta de armazenamento estiver configurada para RA-GRS. 

## <a name="see-also"></a>Consulte também

- [Iniciar uma falha na conta (pré-visualização)](storage-initiate-account-failover.md)
- [Conceber aplicações de elevada disponibilidade com o RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Tutorial: Construir uma aplicação altamente disponível com armazenamento Blob](../blobs/storage-create-geo-redundant-storage.md) 
