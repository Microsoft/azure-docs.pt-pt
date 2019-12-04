---
title: Recuperação de desastres e failover de conta de armazenamento (versão prévia) – armazenamento do Azure
description: O armazenamento do Azure dá suporte ao failover de conta (versão prévia) para contas de armazenamento com redundância geográfica. Com o failover de conta, você pode iniciar o processo de failover para sua conta de armazenamento se o ponto de extremidade primário ficar indisponível.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d9daff390aa1678c25f4bf9c29b0293d96c43f48
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775933"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Recuperação de desastres e failover de conta de armazenamento (versão prévia) no armazenamento do Azure

A Microsoft se esforça para garantir que os serviços do Azure estejam sempre disponíveis. No entanto, podem ocorrer interrupções de serviço não planejadas. Se seu aplicativo exigir resiliência, a Microsoft recomenda usar o armazenamento com redundância geográfica, para que os dados sejam replicados em uma segunda região. Além disso, os clientes devem ter um plano de recuperação de desastre em vigor para lidar com uma interrupção de serviço regional. Uma parte importante de um plano de recuperação de desastre está se preparando para fazer failover para o ponto de extremidade secundário no caso de o ponto de extremidade primário ficar indisponível. 

O armazenamento do Azure dá suporte ao failover de conta (versão prévia) para contas de armazenamento com redundância geográfica. Com o failover de conta, você pode iniciar o processo de failover para sua conta de armazenamento se o ponto de extremidade primário ficar indisponível. O failover atualiza o ponto de extremidade secundário para se tornar o ponto de extremidade primário da sua conta de armazenamento. Depois que o failover for concluído, os clientes poderão começar a gravar no novo ponto de extremidade primário.

Este artigo descreve os conceitos e o processo envolvidos em um failover de conta e discute como preparar sua conta de armazenamento para recuperação com a menor quantidade de impacto no cliente. Para saber como iniciar um failover de conta no portal do Azure ou no PowerShell, consulte [Iniciar um failover de conta (versão prévia)](storage-initiate-account-failover.md).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Escolha a opção de redundância correta

Todas as contas de armazenamento são replicadas para redundância. A opção de redundância escolhida para sua conta depende do grau de resiliência de que você precisa. Para proteção contra interrupções regionais, escolha armazenamento com redundância geográfica, com ou sem a opção de acesso de leitura da região secundária:  

O **armazenamento com redundância geográfica (GRS)** Replica seus dados de forma assíncrona em duas regiões geográficas que têm pelo menos centenas de quilômetros de distância. Se a região primária sofrer uma interrupção, a região secundária servirá como fonte redundante para seus dados. Você pode iniciar um failover para transformar o ponto de extremidade secundário no ponto de extremidade primário.

O **armazenamento com redundância geográfica com acesso de leitura (ra-grs)** fornece armazenamento com redundância geográfica com o benefício adicional de acesso de leitura ao ponto de extremidade secundário. Se ocorrer uma interrupção no ponto de extremidade primário, os aplicativos configurados para RA-GRS e projetados para alta disponibilidade poderão continuar a ler do ponto de extremidade secundário. A Microsoft recomenda RA-GRS para obter máxima resiliência para seus aplicativos.

Outras opções de redundância de armazenamento do Azure incluem ZRS (armazenamento com redundância de zona), que Replica seus dados entre zonas de disponibilidade em uma única região e LRS (armazenamento com redundância local), que Replica seus dados em um único data center em uma única região. Se sua conta de armazenamento estiver configurada para ZRS ou LRS, você poderá converter essa conta para usar GRS ou RA-GRS. Configurar sua conta para o armazenamento com redundância geográfica incorre em custos adicionais. Para obter mais informações, consulte [replicação do armazenamento do Azure](storage-redundancy.md).

> [!NOTE]
> O GZRS (armazenamento com redundância de zona geográfica) e o armazenamento com redundância de acesso de leitura (RA-GZRS) estão atualmente em versão prévia, mas ainda não estão disponíveis nas mesmas regiões que o failover de conta gerenciada pelo cliente. Por esse motivo, os clientes não podem atualmente gerenciar eventos de failover de conta com contas GZRS e RA-GZRS. Durante a versão prévia, a Microsoft gerenciará qualquer evento de failover que afete contas GZRS/RA-GZRS.

> [!WARNING]
> O armazenamento com redundância geográfica traz um risco de perda de dados. Os dados são replicados para a região secundária de forma assíncrona, o que significa que há um atraso entre o momento em que os dados gravados na região primária são gravados na região secundária. No caso de uma interrupção, as operações de gravação para o ponto de extremidade primário que ainda não foram replicadas para o ponto de extremidade secundário serão perdidas.

## <a name="design-for-high-availability"></a>Criar para elevada disponibilidade

É importante projetar seu aplicativo para alta disponibilidade desde o início. Consulte estes recursos do Azure para obter orientação sobre como projetar seu aplicativo e planejar a recuperação de desastres:

* [Criando aplicativos resilientes para o Azure](/azure/architecture/checklist/resiliency-per-service): uma visão geral dos principais conceitos para a arquitetura de aplicativos altamente disponíveis no Azure.
* [Lista de verificação de disponibilidade](/azure/architecture/checklist/resiliency-per-service): uma lista de verificação para verificar se o aplicativo implementa as melhores práticas de design para alta disponibilidade.
* [Criando aplicativos altamente disponíveis usando o Ra-grs](storage-designing-ha-apps-with-ragrs.md): diretrizes de design para a criação de aplicativos para tirar proveito do Ra-grs.
* [Tutorial: criar um aplicativo altamente disponível com o armazenamento de BLOBs](../blobs/storage-create-geo-redundant-storage.md): um tutorial que mostra como criar um aplicativo altamente disponível que alterna automaticamente entre pontos de extremidade como falhas e recuperações são simuladas. 

Além disso, tenha em mente essas práticas recomendadas para manter a alta disponibilidade para os dados do armazenamento do Azure:

* **Discos:** Use o [backup do Azure](https://azure.microsoft.com/services/backup/) para fazer backup dos discos de VM usados por suas máquinas virtuais do Azure. Considere também o uso de [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) para proteger suas VMs no caso de um desastre regional.
* **Blobs de blocos:** Ative a [exclusão reversível](../blobs/storage-blob-soft-delete.md) para proteger contra exclusões em nível de objeto e substituições ou copie blobs de blocos para outra conta de armazenamento em uma região diferente usando [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)ou a [biblioteca de movimentação de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Arquivos:** Use [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) para copiar os arquivos para outra conta de armazenamento em uma região diferente.
* **Tabelas:** use [AzCopy](storage-use-azcopy.md) para exportar dados de tabela para outra conta de armazenamento em uma região diferente.

## <a name="track-outages"></a>Acompanhar interrupções

Os clientes podem assinar o [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/) para acompanhar a integridade e o status do armazenamento do Azure e outros serviços do Azure.

A Microsoft também recomenda que você projete seu aplicativo para se preparar para a possibilidade de falhas de gravação. Seu aplicativo deve expor falhas de gravação de uma maneira que o alerta para a possibilidade de uma interrupção na região primária.

## <a name="understand-the-account-failover-process"></a>Entender o processo de failover da conta

O failover de conta gerenciada pelo cliente (versão prévia) permite que você falhe toda a conta de armazenamento para a região secundária se a primária ficar indisponível por algum motivo. Quando você força um failover para a região secundária, os clientes podem começar a gravar dados no ponto de extremidade secundário após a conclusão do failover. O failover normalmente leva cerca de uma hora.

### <a name="how-an-account-failover-works"></a>Como funciona um failover de conta

Em circunstâncias normais, um cliente grava dados em uma conta de armazenamento do Azure na região primária e esses dados são replicados assincronamente para a região secundária. A imagem a seguir mostra o cenário quando a região primária está disponível:

![Os clientes gravam dados na conta de armazenamento na região primária](media/storage-disaster-recovery-guidance/primary-available.png)

Se o ponto de extremidade primário ficar indisponível por algum motivo, o cliente não poderá mais gravar na conta de armazenamento. A imagem a seguir mostra o cenário em que o primário tornou-se indisponível, mas nenhuma recuperação ocorreu ainda:

![O primário não está disponível, portanto os clientes não podem gravar dados](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

O cliente inicia o failover da conta para o ponto de extremidade secundário. O processo de failover atualiza a entrada DNS fornecida pelo armazenamento do Azure para que o ponto de extremidade secundário se torne o novo ponto de extremidade primário para sua conta de armazenamento, conforme mostrado na imagem a seguir:

![O cliente inicia o failover da conta para o ponto de extremidade secundário](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

O acesso de gravação é restaurado para contas GRS e RA-GRS assim que a entrada DNS é atualizada e as solicitações são direcionadas para o novo ponto de extremidade primário. Os pontos de extremidade de serviço de armazenamento existentes para BLOBs, tabelas, filas e arquivos permanecem os mesmos após o failover.

> [!IMPORTANT]
> Depois que o failover for concluído, a conta de armazenamento será configurada para ser localmente redundante no novo ponto de extremidade primário. Para retomar a replicação para o novo secundário, configure a conta para usar o armazenamento com redundância geográfica novamente (RA-GRS ou GRS).
>
> Tenha em mente que a conversão de uma conta LRS para RA-GRS ou GRS incorre em um custo. Esse custo se aplica à atualização da conta de armazenamento na nova região primária para usar RA-GRS ou GRS após um failover.  

### <a name="anticipate-data-loss"></a>Prever a perda de dados

> [!CAUTION]
> Um failover de conta geralmente envolve alguma perda de dados. É importante entender as implicações de iniciar um failover de conta.  

Como os dados são gravados de forma assíncrona da região primária para a região secundária, sempre há um atraso antes que uma gravação na região primária seja replicada para a região secundária. Se a região primária ficar indisponível, as gravações mais recentes talvez ainda não tenham sido replicadas para a região secundária.

Quando você força um failover, todos os dados na região primária são perdidos, pois a região secundária se torna a nova região primária e a conta de armazenamento é configurada para ser localmente redundante. Todos os dados já replicados para o secundário são mantidos quando o failover ocorre. No entanto, todos os dados gravados no primário que também não foram replicados para o secundário são perdidos permanentemente. 

A propriedade **hora da última sincronização** indica a hora mais recente em que os dados da região primária têm a garantia de serem gravados na região secundária. Todos os dados gravados antes da hora da última sincronização estão disponíveis no secundário, enquanto os dados gravados após a hora da última sincronização podem não ter sido gravados no secundário e podem ser perdidos. Use essa propriedade no caso de uma interrupção para estimar a quantidade de perda de dados que você pode incorrer ao iniciar um failover de conta. 

Como prática recomendada, crie seu aplicativo para que você possa usar a hora da última sincronização para avaliar a perda de dados esperada. Por exemplo, se você estiver registrando em log todas as operações de gravação, poderá comparar a hora da última operação de gravação com a hora da última sincronização para determinar quais gravações não foram sincronizadas com o secundário.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Tenha cuidado ao executar failback para o primário original

Após o failover da região primária para a secundária, sua conta de armazenamento será configurada para ser localmente redundante na nova região primária. Você pode configurar a conta para redundância geográfica novamente atualizando-a para usar GRS ou RA-GRS. Quando a conta é configurada para redundância geográfica novamente após um failover, a nova região primária imediatamente começa a replicar dados para a nova região secundária, que era a primária antes do failover original. No entanto, pode levar um período de tempo antes que os dados existentes no primário sejam totalmente replicados para o novo secundário.

Depois que a conta de armazenamento é reconfigurada para redundância geográfica, é possível iniciar outro failover a partir do novo primário de volta para o novo secundário. Nesse caso, a região primária original antes do failover se torna a região primária novamente e é configurada para ser localmente redundante. Todos os dados na região primária após o failover (o secundário original) são perdidos. Se a maioria dos dados na conta de armazenamento não tiver sido replicada para o novo secundário antes de realizar o failback, você poderá sofrer uma grande perda de dados. 

Para evitar uma grande perda de dados, verifique o valor da propriedade **hora da última sincronização** antes de realizar o failback. Compare a hora da última sincronização com as últimas vezes que os dados foram gravados no novo primário para avaliar a perda de dados esperada. 

## <a name="initiate-an-account-failover"></a>Iniciar a ativação pós-falha de uma conta

Você pode iniciar um failover de conta do portal do Azure, do PowerShell, do CLI do Azure ou da API do provedor de recursos de armazenamento do Azure. Para obter mais informações sobre como iniciar um failover, consulte [Iniciar um failover de conta (versão prévia)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Sobre a versão prévia

O failover de conta está disponível em versão prévia para todos os clientes que usam GRS ou RA-GRS com implantações Azure Resource Manager. Há suporte para os tipos de conta de armazenamento de blob v1, de finalidade geral V2 e de uso geral. o failover de conta está disponível atualmente nestas regiões:

- Ásia Oriental
- Sudeste Asiático
- Leste da Austrália
- Sudeste da Austrália
- Centro dos EUA
- Este dos EUA 2
- E.U.A. Centro-Oeste
- E.U.A. Oeste 2

A versão prévia destina-se apenas ao uso de não produção. Os SLAs (contratos de nível de serviço) de produção não estão disponíveis no momento.

### <a name="register-for-the-preview"></a>Registrar-se para a versão prévia

Para se registrar para a versão prévia, execute os seguintes comandos no PowerShell. Certifique-se de substituir o espaço reservado entre colchetes por sua própria ID de assinatura:

```powershell
Connect-AzAccount -SubscriptionId <subscription-id>
Register-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Pode levar de 5-7 dias para receber aprovação para a versão prévia. Para verificar se o registro foi aprovado, execute o seguinte comando:

```powershell
Get-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Considerações adicionais 

Examine as considerações adicionais descritas nesta seção para entender como seus aplicativos e serviços podem ser afetados quando você força um failover durante o período de versão prévia.

#### <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

As VMs (máquinas virtuais) do Azure não fazem failover como parte de um failover de conta. Se a região primária ficar indisponível e você fizer failover para a região secundária, será necessário recriar as VMs após o failover. 

#### <a name="azure-unmanaged-disks"></a>Discos não gerenciados do Azure

Como prática recomendada, a Microsoft recomenda converter discos não gerenciados em discos gerenciados. No entanto, se você precisar fazer failover de uma conta que contenha discos não gerenciados anexados às VMs do Azure, será necessário desligar a VM antes de iniciar o failover.

Os discos não gerenciados são armazenados como BLOBs de páginas no armazenamento do Azure. Quando uma VM está em execução no Azure, todos os discos não gerenciados anexados à VM são concedidos. Um failover de conta não pode continuar quando há uma concessão em um blob. Para executar o failover, siga estas etapas:

1. Antes de começar, observe os nomes de todos os discos não gerenciados, seus números de unidade lógica (LUN) e a VM à qual eles estão anexados. Isso facilitará a anexação dos discos após o failover. 
2. Desligue a VM.
3. Exclua a VM, mas mantenha os arquivos VHD para os discos não gerenciados. Observe a hora em que você excluiu a VM.
4. Aguarde até que a **hora da última sincronização** seja atualizada e seja posterior à hora em que você excluiu a VM. Esta etapa é importante, porque se o ponto de extremidade secundário não tiver sido totalmente atualizado com os arquivos VHD quando o failover ocorrer, a VM poderá não funcionar corretamente na nova região primária.
5. Inicie o failover da conta.
6. Aguarde até que o failover da conta seja concluído e a região secundária se torne a nova região primária.
7. Crie uma VM na nova região primária e anexe novamente os VHDs.
8. Inicie a nova VM.

Tenha em mente que todos os dados armazenados em um disco temporário são perdidos quando a VM é desligada.

### <a name="unsupported-features-or-services"></a>Recursos ou serviços sem suporte
Os seguintes recursos ou serviços não têm suporte para failover de conta para a versão de visualização:

- Sincronização de Arquivos do Azure não dá suporte ao failover da conta de armazenamento. As contas de armazenamento que contêm compartilhamentos de arquivos do Azure que estão sendo usados como pontos de extremidade de nuvem no Sincronização de Arquivos do Azure não devem passar pelo failover. Fazer isso fará com que a sincronização pare de funcionar e também pode causar perda de dados inesperada no caso de arquivos recentemente em camadas.  
- Não é possível fazer failover de uma conta de armazenamento contendo BLOBs arquivados. Manter BLOBs arquivados em uma conta de armazenamento separada que você não planeja fazer failover.
- Não é possível fazer failover de uma conta de armazenamento contendo blobs de blocos Premium. As contas de armazenamento que dão suporte a blobs de blocos Premium atualmente não dão suporte à redundância geográfica.
- Após a conclusão do failover, os seguintes recursos deixarão de funcionar se originalmente habilitados: [assinaturas de evento](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview), políticas de [ciclo de vida](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) [análise de armazenamento log](https://docs.microsoft.com/rest/api/storageservices/about-storage-analytics-logging).

## <a name="copying-data-as-an-alternative-to-failover"></a>Copiando dados como uma alternativa ao failover

Se sua conta de armazenamento estiver configurada para RA-GRS, você terá acesso de leitura aos seus dados usando o ponto de extremidade secundário. Se preferir não fazer failover no caso de uma interrupção na região primária, você poderá usar ferramentas como [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)ou a biblioteca de movimentação de dados do [Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar dados de sua conta de armazenamento na região secundária para outra conta de armazenamento em uma região não afetada. Em seguida, você pode apontar seus aplicativos para essa conta de armazenamento para disponibilidade de leitura e gravação.

## <a name="microsoft-managed-failover"></a>Failover gerenciado pela Microsoft

Em circunstâncias extremas em que uma região é perdida devido a um desastre significativo, a Microsoft pode iniciar um failover regional. Nesse caso, nenhuma ação de sua parte é necessária. Até que o failover gerenciado pela Microsoft seja concluído, você não terá acesso de gravação à sua conta de armazenamento. Seus aplicativos poderão ler a partir da região secundária se sua conta de armazenamento estiver configurada para RA-GRS. 

## <a name="see-also"></a>Ver também

* [Iniciar um failover de conta (versão prévia)](storage-initiate-account-failover.md)
* [Conceber aplicações de elevada disponibilidade com o RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Tutorial: criar um aplicativo altamente disponível com o armazenamento de BLOBs](../blobs/storage-create-geo-redundant-storage.md) 
