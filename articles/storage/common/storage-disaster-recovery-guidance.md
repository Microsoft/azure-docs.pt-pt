---
title: Após desastre recuperação e o armazenamento de conta ativação pós-falha (pré-visualização) - armazenamento do Azure
description: O armazenamento do Azure suporta a ativação pós-falha de conta (pré-visualização) para contas de armazenamento georredundante. Com a ativação pós-falha de conta, pode iniciar o processo de ativação pós-falha para a sua conta de armazenamento se o ponto final primário ficar indisponível.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e14e35cc8589bb524bae791ccd74952da90bdb04
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871541"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Após desastre recuperação e o armazenamento de conta ativação pós-falha (pré-visualização) no armazenamento do Azure

A Microsoft está empenhada garantir que os serviços do Azure estão sempre disponíveis. No entanto, poderão ocorrer interrupções de serviço não planeada. Se a sua aplicação necessitar de resiliência, a Microsoft recomenda utilizar o armazenamento georredundante, para que os dados são replicados numa segunda região. Além disso, os clientes devem ter uma recuperação após desastre, plano em funcionamento para manipular uma indisponibilidade do serviço regional. Uma parte importante de um plano de recuperação após desastre está a preparar para a ativação pós-falha para o ponto final secundário no caso do ponto final primário fica indisponível. 

O armazenamento do Azure suporta a ativação pós-falha de conta (pré-visualização) para contas de armazenamento georredundante. Com a ativação pós-falha de conta, pode iniciar o processo de ativação pós-falha para a sua conta de armazenamento se o ponto final primário ficar indisponível. A ativação pós-falha atualiza o ponto final secundário para se tornar o ponto final primário para a sua conta de armazenamento. Depois de concluída a ativação pós-falha, os clientes podem começar a escrever para o novo ponto final primário.

Este artigo descreve os conceitos e o processo envolvido com uma conta de ativação pós-falha e explica como preparar a sua conta de armazenamento para a recuperação com o mínimo de impacto de cliente. Para saber como iniciar uma ativação pós-falha de conta no portal do Azure ou do PowerShell, veja [inicie uma ativação pós-falha de conta (pré-visualização)](storage-initiate-account-failover.md).

## <a name="choose-the-right-redundancy-option"></a>Escolha a opção de redundância certo

Todas as contas de armazenamento são replicadas para redundância. Que opção de redundância que escolher para a sua conta depende o grau de resiliência de que necessita. Para proteção contra falhas regionais, escolha armazenamento georredundante, com ou sem a opção de acesso de leitura a partir da região secundária:  

**Armazenamento georredundante (GRS)** replica os dados de forma assíncrona em duas regiões geográficas, pelo menos, centenas de quilómetros de distância. Se a região primária sofre uma falha, em seguida, a região secundária serve como uma origem redundante para os seus dados. Pode iniciar uma ativação pós-falha para transformar o ponto final secundário para o ponto final primário.

**Armazenamento georredundante com acesso de leitura (RA-GRS)** proporciona armazenamento geograficamente redundante com o benefício adicional de acesso de leitura para o ponto final secundário. Se ocorrer uma falha no ponto final primário, configurado para o RA-GRS e foi concebido para elevada disponibilidade de aplicações podem continuar a ler a partir do ponto final secundário. A Microsoft recomenda o RA-GRS para resiliência máxima para as suas aplicações.

Outras opções de redundância de armazenamento do Azure incluem o armazenamento com redundância de zona (ZRS), que replica os dados em zonas de disponibilidade numa única região e o armazenamento localmente redundante (LRS), que replica os seus dados num único centro de dados numa única região. Se a sua conta de armazenamento está configurada para ZRS ou LRS, pode converter essa conta para utilizar o GRS ou RA-GRS. Configurar a sua conta de armazenamento georredundante, incorre em custos adicionais. Para obter mais informações, consulte [replicação de armazenamento do Azure](storage-redundancy.md).

> [!WARNING]
> Armazenamento georredundante acarreta um risco de perda de dados. Dados são replicados para a região secundária de forma assíncrona, que significa que existe um atraso entre quando os dados escritos para a região primária são escritos para a região secundária. Em caso de interrupção, escreva operações para o ponto final primário que ainda não tem sido replicadas para o ponto final secundário serão perdidas. 

## <a name="design-for-high-availability"></a>Conceção para elevada disponibilidade

É importante projetar seu aplicativo para elevada disponibilidade, desde o início. Consulte estes recursos do Azure para obter orientações sobre a criação do seu aplicativo e o planejamento para recuperação após desastre:

* [Conceber aplicações resilientes para o Azure](https://docs.microsoft.com/azure/architecture/resiliency/): Uma visão geral dos principais conceitos de arquitetura de aplicações de elevada disponibilidade no Azure.
* [Lista de verificação de disponibilidade](https://docs.microsoft.com/azure/architecture/checklist/availability): Uma lista de verificação para verificar que a aplicação implemente as melhores práticas de design para elevada disponibilidade.
* [Conceber aplicações de elevada disponibilidade com RA-GRS](storage-designing-ha-apps-with-ragrs.md): Documentação de orientação para a criação de aplicações para tirar partido de RA-GRS.
* [Tutorial: Criar uma aplicação de elevada disponibilidade com armazenamento de BLOBs](../blobs/storage-create-geo-redundant-storage.md): Um tutorial que mostra como criar uma aplicação de elevada disponibilidade que muda automaticamente entre pontos finais como falhas e recuperações são simulados. 

Além disso, tenha em atenção estas práticas recomendadas para manutenção da alta disponibilidade para os seus dados de armazenamento do Azure:

* **Discos:** Uso [Azure Backup](https://azure.microsoft.com/services/backup/) para criar cópias de segurança os discos VM utilizados pelas máquinas virtuais do Azure. Considere também a utilização [do Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) para proteger as VMs em caso de desastre regional.
* **Blobs de blocos:** Ative [eliminação de forma recuperável](../blobs/storage-blob-soft-delete.md) para proteger contra as eliminações ao nível do objeto e substitui ou copiar os blobs de blocos para outra conta de armazenamento numa região diferente com [AzCopy](storage-use-azcopy.md), [Azure PowerShell ](storage-powershell-guide-full.md), ou o [biblioteca de movimento de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Ficheiros:** Uso [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) para copiar os ficheiros para outra conta de armazenamento numa região diferente.
* **Tabelas:** utilizar [AzCopy](storage-use-azcopy.md) para exportar dados de tabela para outra conta de armazenamento numa região diferente.

## <a name="track-outages"></a>Controlar as falhas

Os clientes podem subscrever os [Dashboard de estado de funcionamento do serviço de Azure](https://azure.microsoft.com/status/) para controlar o estado de funcionamento e o estado do armazenamento do Azure e outros serviços do Azure.

A Microsoft também recomenda que crie uma aplicação para se preparar para a possibilidade de falhas de escrita. Seu aplicativo deve expor as falhas de escrita de forma que o alerta para a possibilidade de uma falha na região primária.

## <a name="understand-the-account-failover-process"></a>Compreender o processo de ativação pós-falha de conta

Ativação pós-falha de conta gerida pelo cliente (pré-visualização) permite-lhe um failover sua conta de armazenamento completo para a região secundária se o principal ficar indisponível por qualquer motivo. Quando forçar uma ativação pós-falha para a região secundária, os clientes podem começar a escrever dados para o ponto final secundário depois de concluída a ativação pós-falha. A ativação pós-falha normalmente demora cerca de uma hora.

### <a name="how-an-account-failover-works"></a>Como funciona uma ativação pós-falha de conta

Em circunstâncias normais, um cliente escreve dados para uma conta de armazenamento do Azure na região primária e que os dados são replicados de forma assíncrona para a região secundária. A imagem seguinte mostra o cenário quando a região primária está disponível:

![Os clientes escreverem dados para a conta de armazenamento na região primária](media/storage-disaster-recovery-guidance/primary-available.png)

Se o ponto final primário ficar indisponível por qualquer motivo, o cliente já não é possível escrever para a conta de armazenamento. A imagem seguinte mostra o cenário em que o primário ficou indisponível, mas sem recuperação tiver ocorrido ainda:

![O principal não estiver disponível, para que os clientes não é possível escrever dados](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

O cliente inicia o failover de conta para o ponto final secundário. O processo de ativação pós-falha atualiza a entrada DNS fornecida pelo armazenamento do Azure, para que o ponto final secundário se torna o novo ponto de final primário da sua conta de armazenamento, conforme mostrado na imagem seguinte:

![Cliente inicia a ativação pós-falha de conta para o ponto final secundário](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Assim que a entrada DNS foi atualizada e pedidos que está a ser direcionados para o novo ponto final primário o acesso de escrita é restaurado para contas GRS e RA-GRS. Pontos finais de serviço armazenamento existente para blobs, tabelas, filas e ficheiros permanecem inalterados após a ativação pós-falha.

> [!IMPORTANT]
> Depois de concluída a ativação pós-falha, a conta de armazenamento está configurada para ser localmente redundante no novo ponto final primário. Para retomar a replicação para o novo secundário, configure a conta a utilizar o armazenamento georredundante novamente (RA-GRS ou GRS).
>
> Tenha em atenção que a conversão de uma conta LRS para RA-GRS ou GRS incorre num custo. Este custo aplica-se para atualizar a conta de armazenamento na região primária nova para usar o RA-GRS ou GRS após uma ativação pós-falha.  

### <a name="anticipate-data-loss"></a>Prever a perda de dados

> [!CAUTION]
> Uma ativação pós-falha de conta normalmente envolve alguma perda de dados. É importante compreender as implicações de iniciar uma ativação pós-falha de conta.  

Uma vez que dados são escritos de forma assíncrona da região primária para a região secundária, há sempre um atraso antes de uma gravação para a região primária é replicada para a região secundária. Se a região primária ficar indisponível, as gravações mais recentes podem não ainda ter sido replicadas para a região secundária.

Quando forçar uma ativação pós-falha, todos os dados na região primária é perdido, conforme a região secundária torna-se a nova região primária e a conta de armazenamento está configurada para ser localmente redundante. Todos os dados que já são replicados para o secundário é mantida quando ocorre a ativação pós-falha. No entanto, todos os dados gravados para o primário que também não tiverem sido replicado para o secundário são perdidos permanentemente. 

O **hora da última sincronização** propriedade indica o máximo tempo recente que os dados da região primária são garantidos que foram escritos para a região secundária. Todos os dados gravados antes da hora da última sincronização está disponível no secundário, enquanto os dados gravados após a hora da última sincronização não pode ter sido escrita para o secundário e pode ser perdida. Utilize esta propriedade em caso de interrupção para estimar a quantidade de perda de dados que pode ser cobrado, iniciando uma ativação pós-falha de conta. 

Como melhor prática, crie uma aplicação para que possa utilizar a hora da última sincronização para avaliar a perda de dados esperado. Por exemplo, se está a iniciar sessão todas as operações de escrita, em seguida, pode comparar o tempo de sua última operações de escrita para a última sincronização de hora para determinar quais escritas não foram sincronizadas para o secundário.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Tenha cuidado ao reativa para a primária original

Depois de efetuar a ativação pós-falha dos principais para a região secundária, a sua conta de armazenamento está configurada para ser localmente redundante na região primária nova. Pode configurar a conta para redundância geográfica novamente ao atualizá-los para utilizar o GRS ou RA-GRS. Quando a conta está configurada para redundância geográfica novamente após uma ativação pós-falha, a região primária nova começa imediatamente a replicação de dados para a nova região secundária, que foi o principal antes da ativação pós-falha original. No entanto, poderá demorar um período de tempo antes de dados existentes na primária totalmente são replicados para o novo secundário.

Depois da conta de armazenamento é reconfigurada para redundância geográfica, é possível iniciar a ativação pós-falha de outra do novo principal de cópia para o novo secundário. Neste caso, a região primária original antes da ativação pós-falha novamente torna-se a região primária e está configurada para ser localmente redundante. Todos os dados na região primária após a ativação pós-falha (secundário original) são, em seguida, perdido. Se não tiverem sido replicados a maioria dos dados na conta de armazenamento para o novo secundário antes de efetuar a reativação pós-falha, poderá sofrer uma perda de dados principais. 

Para evitar uma perda de dados principais, verifique o valor do **hora da última sincronização** propriedade antes da reativação pós-falha. Compare a hora da última sincronização para as últimas vezes que os dados foi escrito para a nova principal para avaliar a perda de dados esperado. 

## <a name="initiate-an-account-failover"></a>Iniciar a ativação pós-falha de uma conta

Pode iniciar uma ativação pós-falha de conta do portal do Azure, PowerShell, CLI do Azure ou a API do fornecedor de recursos de armazenamento do Azure. Para obter mais informações sobre como iniciar uma ativação pós-falha, consulte [inicie uma ativação pós-falha de conta (pré-visualização)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Sobre a pré-visualização

ativação pós-falha de conta está disponível em pré-visualização para todos os clientes com o GRS ou RA-GRS com implementações do Azure Resource Manager. Para fins gerais v1, para fins gerais v2 e tipos de conta de armazenamento de BLOBs são suportados. ativação pós-falha de conta está atualmente disponível nestas regiões:

- E.U.A. Oeste 2
- E.U.A. Centro-Oeste

A pré-visualização destina-se apenas a utilização de não produção. Contratos de nível de serviço de produção (SLAs) não estão atualmente disponíveis.

### <a name="register-for-the-preview"></a>Registre-se para a pré-visualização

Para se registar na pré-visualização, execute os seguintes comandos no PowerShell. Certifique-se substituir o marcador de posição entre parênteses Retos com o seu próprio ID de subscrição:

```PowerShell
Connect-AzureRmAccount -SubscriptionId <subscription-id>
Register-AzureRmProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Pode demorar 1 a 2 dias para receber a aprovação para a pré-visualização. Para verificar que o registo foi aprovado, execute o seguinte comando:

```PowerShell
Get-AzureRmProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Considerações adicionais 

Reveja as considerações adicionais descritas nesta secção para compreender como as aplicações e serviços podem ser afetados quando forçada uma ativação pós-falha durante o período de pré-visualização.

#### <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

Máquinas virtuais do Azure (VMs) não efetuar a ativação pós-falha como parte de uma ativação pós-falha de conta. Se a região primária fica indisponível e efetuar a ativação pós-falha para a região secundária, em seguida, precisa de recriar todas as VMs após a ativação pós-falha. 

#### <a name="azure-unmanaged-disks"></a>Discos não geridos do Azure

Como melhor prática, a Microsoft recomenda a conversão de discos não geridos para discos geridos. No entanto, se tiver de efetuar a ativação pós-falha de uma conta com discos não geridos anexados a VMs do Azure, terá de encerrar a VM antes de iniciar a ativação pós-falha.

Discos não geridos são armazenados como blobs de páginas no armazenamento do Azure. Quando uma VM está em execução no Azure, são concedidos os discos não geridos ligados à VM. Uma ativação pós-falha de conta não pode continuar quando existe uma concessão num blob. Para efetuar a ativação pós-falha, siga estes passos:

1. Antes de começar, tenha em atenção os nomes de quaisquer discos não geridos, os números de unidade lógica (LUN) e a VM à qual estão ligados. Se o fizer, irá facilitar religar os discos após a ativação pós-falha. 
2. Encerre a VM.
3. Elimine a VM, mas manter os ficheiros VHD para os discos não geridos. Tenha em atenção o tempo em que eliminou a VM.
4. Aguarde até que o **hora da última sincronização** foi atualizado e é posterior à hora em que eliminou a VM. Este passo é importante, porque se o ponto final secundário não foi totalmente atualizado com os ficheiros VHD quando ocorre a ativação pós-falha, em seguida, a VM poderá não funcionar corretamente na nova região primária.
5. Inicie a ativação pós-falha de conta.
6. Aguarde até que a ativação pós-falha de conta está concluída e a região secundária tornou-se a região primária nova.
7. Criar uma VM na região primária nova e voltar a anexar o VHD.
8. Inicie a VM nova.

Tenha em atenção que todos os dados armazenados num disco temporário são perdidos quando a VM é encerrada.

### <a name="unsupported-features-or-services"></a>Funcionalidades não suportadas ou serviços
Os seguintes recursos ou serviços não são suportados para a ativação pós-falha de conta para a versão de pré-visualização:

- O Azure File Sync não suporta a ativação pós-falha de conta de armazenamento. Contas de armazenamento que contém as partilhas de ficheiros do Azure a ser utilizadas como pontos finais da cloud no Azure File Sync devem não efetuar a ativação pós-falha. Se o fizer, sincronização causa para parar o trabalho e Maio também causará perda de dados inesperado no caso de ficheiros recentemente em camadas.  
- Contas de armazenamento com espaço de nomes hierárquico de geração 2 de armazenamento do Azure Data Lake não é possível efetuar a ativação pós-falha.
- Uma conta de armazenamento que contém blobs arquivados não é possível efetuar a ativação pós-falha. Manter blobs arquivados numa conta de armazenamento separado que não planeja fazer a ativação pós-falha.
- Uma conta de armazenamento que contém os blobs de blocos de premium não é possível efetuar a ativação pós-falha. Contas de armazenamento que suportam blobs de blocos de premium não suportam atualmente pela georredundância.

## <a name="copying-data-as-an-alternative-to-failover"></a>Cópia de dados como uma alternativa para ativação pós-falha

Se a conta de armazenamento está configurada para o RA-GRS, em seguida, terá acesso de leitura aos seus dados com o ponto final secundário. Se preferir não fazer a ativação pós-falha em caso de interrupção na região primária, pode utilizar ferramentas como [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), ou o [biblioteca de movimento de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar dados da sua conta de armazenamento na região secundária para outra conta de armazenamento numa região afetada. Pode, em seguida, do ponto de seus aplicativos para essa conta de armazenamento para as opções de leitura e escrita de disponibilidade.

## <a name="microsoft-managed-failover"></a>Ativação pós-falha gerida pela Microsoft

Extreme circunstâncias em que uma região é perdida devido a um desastre significativo, a Microsoft pode iniciar uma ativação pós-falha. Neste caso, nenhuma ação da sua parte é necessária. Até que seja concluída a ativação pós-falha gerida pela Microsoft, não terá acesso de escrita para a sua conta de armazenamento. As aplicações podem ler da região secundária se a conta de armazenamento está configurada para o RA-GRS. 

## <a name="see-also"></a>Consulte também

* [Inicie uma ativação pós-falha de conta (pré-visualização)](storage-initiate-account-failover.md)
* [Conceber aplicações de elevada disponibilidade com o RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Tutorial: Criar uma aplicação de elevada disponibilidade com armazenamento de BLOBs](../blobs/storage-create-geo-redundant-storage.md) 
