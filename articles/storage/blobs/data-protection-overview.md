---
title: Descrição geral da proteção de dados
titleSuffix: Azure Storage
description: As opções de proteção de dados disponíveis para o seu armazenamento blob e dados da Azure Data Lake Storage Gen2 permitem-lhe proteger os seus dados de serem eliminados ou substituídos. Se precisar de recuperar dados que foram eliminados ou substituídos, este guia pode ajudá-lo a escolher a opção de recuperação que é melhor para o seu cenário.
services: storage
author: tamram
ms.service: storage
ms.date: 03/22/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: afd98e629500bc90cc9ddd1ed4ab2472f733e845
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803798"
---
# <a name="data-protection-overview"></a>Descrição geral da proteção de dados

O Azure Storage fornece proteção de dados para o Blob Storage e Azure Data Lake Storage Gen2 para ajudá-lo a preparar-se para cenários onde precisa de recuperar dados que foram eliminados ou substituídos. É importante pensar em como proteger melhor os seus dados antes que ocorra um incidente que possa comprometê-lo. Este guia pode ajudá-lo a decidir com antecedência quais as funcionalidades de proteção de dados que o seu cenário necessita e como implementá-los. Se precisar de recuperar dados que foram eliminados ou substituídos, esta visão geral também fornece orientações sobre como proceder, com base no seu cenário.

Na documentação de Armazenamento Azure, a *proteção de dados* refere-se a estratégias para proteger a conta de armazenamento e os dados dentro dela de serem eliminados ou modificados, ou para restaurar os dados após a sua aussição ou modificação. O Azure Storage também oferece opções para *a recuperação* de desastres, incluindo vários níveis de redundância para proteger os seus dados de falhas de serviço devido a problemas de hardware ou desastres naturais, e falhas geridas pelo cliente no caso de o centro de dados na região primária ficar indisponível. Para obter mais informações sobre como os seus dados estão protegidos contra falhas de serviço, consulte [a recuperação de desastres.](#disaster-recovery)

## <a name="recommendations-for-basic-data-protection"></a>Recomendações para a proteção de dados básicos

Se estiver à procura de uma cobertura básica de proteção de dados para a sua conta de armazenamento e os dados que contém, então a Microsoft recomenda que tome as seguintes medidas para começar com:

- Configure um bloqueio de gestor de recursos Azure na conta de armazenamento para proteger a conta de alterações de eliminação ou configuração. [Saiba mais...](../common/lock-account-resource.md)
- Permitir que o contentor elimine suavemente a conta de armazenamento para recuperar um recipiente apagado e o seu conteúdo. [Saiba mais...](soft-delete-container-enable.md)
- Salve o estado de uma bolha em intervalos regulares:
  - Para as cargas de trabalho de Armazenamento Blob, permita que a versão blob guarde automaticamente o estado dos seus dados sempre que uma bolha for eliminada ou substituída. [Saiba mais...](versioning-enable.md)
  - Para as cargas de trabalho de armazenamento do Lago de Dados Azure, tire fotos manuais para guardar o estado dos seus dados num determinado momento. [Saiba mais...](snapshots-overview.md)

Estas opções, bem como opções adicionais de proteção de dados para outros cenários, são descritas mais detalhadamente na secção seguinte.

Para uma visão geral dos custos envolvidos com estas características, consulte [resumo das considerações de custos.](#summary-of-cost-considerations)

## <a name="overview-of-data-protection-options"></a>Visão geral das opções de proteção de dados

O quadro seguinte resume as opções disponíveis no Azure Storage para cenários comuns de proteção de dados. Escolha os cenários aplicáveis à sua situação para saber mais sobre as opções disponíveis. Note que nem todas as funcionalidades estão disponíveis neste momento para contas de armazenamento com um espaço hierárquico habilitado.

| Scenario | Opção de proteção de dados | Recomendações | Benefício de proteção | Disponível para armazenamento de data lake |
|--|--|--|--|--|
| Evite que uma conta de armazenamento seja eliminada ou modificada. | Bloqueio do Gestor de Recursos Azure<br />[Saiba mais...](../common/lock-account-resource.md) | Bloqueie todas as suas contas de armazenamento com um bloqueio do Azure Resource Manager para evitar a eliminação da conta de armazenamento. | Protege a conta de armazenamento contra alterações de eliminação ou configuração.<br /><br />Não protege os recipientes ou bolhas na conta de serem apagados ou substituídos. | Sim |
| Evite que um recipiente e as suas bolhas sejam apagados ou modificados durante um intervalo que controle. | Política de imutabilidade num recipiente<br />[Saiba mais...](storage-blob-immutable-storage.md) | Definir uma política de imutabilidade num contentor para proteger documentos críticos de negócio, por exemplo, a fim de satisfazer os requisitos legais ou regulamentares de conformidade. | Protege um recipiente e as suas bolhas de todas as eliminações e substituições.<br /><br />Quando uma detenção legal ou uma política de retenção baseada no tempo bloqueado está em vigor, a conta de armazenamento também está protegida contra a supressão. Os contentores para os quais não tenha sido definida nenhuma política de imutabilidade não estão protegidos contra a supressão. | Sim, na pré-estreia |
| Restaurar um recipiente eliminado dentro de um intervalo especificado. | Eliminação suave do recipiente (pré-visualização)<br />[Saiba mais...](soft-delete-container-overview.md) | Ativar a eliminação suave do recipiente para todas as contas de armazenamento, com um intervalo mínimo de retenção de 7 dias.<br /><br />Ativar a versão blob e a eliminação suave do blob juntamente com a eliminação suave do recipiente para proteger as bolhas individuais num recipiente.<br /><br />Armazenar recipientes que exijam diferentes períodos de retenção em contas de armazenamento separadas. | Um recipiente eliminado e o seu conteúdo podem ser restaurados dentro do período de retenção.<br /><br />Apenas as operações ao nível do contentor (por exemplo, [Delete Container](/rest/api/storageservices/delete-container)) podem ser restauradas. A eliminação suave do recipiente não lhe permite restaurar uma bolha individual no recipiente se a bolha for apagada. | Sim, na pré-estreia |
| Guarde automaticamente o estado de uma bolha numa versão anterior quando esta for substituída ou eliminada. | Versão blob<br />[Saiba mais...](versioning-overview.md) | Ativar a versão blob, juntamente com a eliminação macia do recipiente e a eliminação suave de bolhas, para contas de armazenamento onde necessita de uma proteção ótima para os dados do blob.<br /><br />Armazenar dados blob que não exijam a versão numa conta separada para limitar os custos. | Cada operação de substituição ou eliminação de bolhas cria uma nova versão. Uma bolha pode ser restaurada a partir de uma versão anterior se a bolha for eliminada ou substituída. | No |
| Restaurar uma versão blob ou blob apagada dentro de um intervalo especificado. | Blob soft delete<br />[Saiba mais...](soft-delete-blob-overview.md) | Ativar a eliminação suave do blob para todas as contas de armazenamento, com um intervalo mínimo de retenção de 7 dias.<br /><br />Ativar a versão blob e a eliminação suave do recipiente juntamente com a eliminação macia de bolhas para uma proteção ótima dos dados blob.<br /><br />Armazenar bolhas que requerem diferentes períodos de retenção em contas de armazenamento separadas. | Uma versão blob ou blob eliminada pode ser restaurada dentro do período de retenção. | No |
| Restaurar um conjunto de bolhas de bloco para um ponto anterior no tempo. | Restauro para um ponto anterior no tempo<br />[Saiba mais...](point-in-time-restore-overview.md) | Para utilizar o ponto-a-tempo restaurar para reverter para um estado anterior, desenhe a sua aplicação para eliminar as bolhas de bloco individuais em vez de eliminar recipientes. | Um conjunto de bolhas de bloco pode ser revertido para o seu estado em um ponto específico no passado.<br /><br />Apenas as operações efetuadas em blocos são revertidas. As operações efetuadas em contentores, bolhas de página ou bolhas de apêndice não são revertidas. | No |
| Guardar manualmente o estado de uma bolha num dado momento. | Snapshot blob<br />[Saiba mais...](snapshots-overview.md) | Recomendado como uma alternativa à versão blob quando a versão não é apropriada para o seu cenário, devido a custos ou outras considerações, ou quando a conta de armazenamento tem um espaço hierárquico habilitado. | Uma bolha pode ser restaurada a partir de um instantâneo se a bolha for substituída. Se a bolha for apagada, as imagens também são eliminadas. | Sim, na pré-estreia |
| Uma bolha pode ser eliminada ou substituída, mas os dados são regularmente copiados para uma segunda conta de armazenamento. | Roll-your-your-own solução para copiar dados para uma segunda conta usando a replicação de objetos de armazenamento Azure ou uma ferramenta como AzCopy ou Azure Data Factory. | Recomendado para proteção da paz de espírito contra ações intencionais inesperadas ou cenários imprevisíveis.<br /><br />Crie a segunda conta de armazenamento na mesma região que a conta principal para evitar incorrer em encargos de saída. | Os dados podem ser restaurados a partir da segunda conta de armazenamento se a conta primária estiver comprometida de alguma forma. | A AzCopy e Azure Data Factory são suportados.<br /><br />A replicação do objeto não é suportada. |

## <a name="data-protection-by-resource-type"></a>Proteção de dados por tipo de recurso

O quadro seguinte resume as opções de proteção de dados do Azure Storage de acordo com os recursos que protegem.

| Opção de proteção de dados | Protege uma conta da eliminação | Protege um recipiente da eliminação | Protege uma bolha da eliminação | Protege uma bolha de substituições |
|--|--|--|--|--|
| Bloqueio do Gestor de Recursos Azure | Sim | Não<sup>1</sup> | No | No |
| Política de imutabilidade num recipiente | Sim<sup>2</sup> | Sim | Sim | Sim |
| Eliminação macia do recipiente | No | Sim | No | No |
| Versão blob<sup>3</sup> | No | No | Sim | Sim |
| Blob soft delete<sup>3</sup> | No | No | Sim | Sim |
| Restauro pontual<sup>3</sup> | No | No | Sim | Sim |
| Snapshot blob | No | No | No | Sim |
| Roll-your-your-own solução para copiar dados para uma segunda conta<sup>4</sup> | No | Sim | Sim | Sim |

<sup>1</sup> Um bloqueio do Gestor de Recursos Azure não protege um recipiente da eliminação.<br />
<sup>2</sup> Enquanto uma política de retenção por tempo ou de tempo bloqueado estiver em vigor para um contentor, a conta de armazenamento também está protegida contra a supressão.<br />
<sup>3</sup> Atualmente não suportado para cargas de trabalho de armazenamento de data lake.<br />
<sup>4</sup> AzCopy e Azure Data Factory são opções que são suportadas tanto para o armazenamento de blob como para o armazenamento de dados. A replicação de objetos é suportada apenas para cargas de trabalho de armazenamento blob.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Recuperar dados eliminados ou substituídos

Se tiver de recuperar dados que tenham sido substituídos ou eliminados, a forma como procede depende das opções de proteção de dados que ativou e de que recurso foi afetado. A tabela seguinte descreve as ações que pode tomar para recuperar dados.

| Recurso apagado ou substituído | Possíveis ações de recuperação | Requisitos de recuperação |
|--|--|--|
| Conta de armazenamento | Tentativa de recuperar a conta de armazenamento eliminada<br />[Saiba mais...](../common/storage-account-recover.md) | A conta de armazenamento foi originalmente criada com o modelo de implementação do Azure Resource Manager e foi eliminada nos últimos 14 dias. Não foi criada uma nova conta de armazenamento com o mesmo nome desde que a conta original foi eliminada. |
| Contentor | Recuperar o recipiente de sossar e o seu conteúdo<br />[Saiba mais...](soft-delete-container-enable.md) | A eliminação suave do recipiente está ativada e o período de retenção suave do contentor ainda não expirou. |
| Contentores e blobs | Restaurar dados a partir de uma segunda conta de armazenamento | Todas as operações de contentores e blob foram efetivamente replicadas numa segunda conta de armazenamento. |
| Blob (qualquer tipo) | Restaurar uma bolha de uma versão anterior<sup>1</sup><br />[Saiba mais...](versioning-enable.md) | A versão blob está ativada e a bolha tem uma ou mais versões anteriores. |
| Blob (qualquer tipo) | Recuperar uma bolha de apagação suave<sup>1</sup><br />[Saiba mais...](soft-delete-blob-enable.md) | A eliminação macia da bolha está ativada e o intervalo de retenção de eliminação suave não expirou. |
| Blob (qualquer tipo) | Restaurar uma bolha de um instantâneo<br />[Saiba mais...](snapshots-manage-dotnet.md) | A bolha tem uma ou mais fotos. |
| Conjunto de bolhas de bloco | Recupere um conjunto de bolhas de bloco para o seu estado em um ponto anterior no tempo<sup>1</sup><br />[Saiba mais...](point-in-time-restore-manage.md) | A restauração pontual está ativada e o ponto de restauro está dentro do intervalo de retenção. A conta de armazenamento não foi comprometida ou corrompida. |
| Versão blob | Recuperar uma versão<sup>1</sup> de apagação suave<br />[Saiba mais...](soft-delete-blob-enable.md) | A eliminação macia da bolha está ativada e o intervalo de retenção de eliminação suave não expirou. |

<sup>1</sup> Atualmente não suportado para cargas de trabalho de armazenamento de data lake.

## <a name="summary-of-cost-considerations"></a>Resumo das considerações de custos

O quadro seguinte resume as considerações de custos das várias opções de proteção de dados descritas neste guia.

| Opção de proteção de dados | Considerações de custos |
|-|-|
| Bloqueio do Gestor de Recursos Azure para uma conta de armazenamento | Sem custos para configurar um cadeado numa conta de armazenamento. |
| Política de imutabilidade num recipiente | Sem custos para configurar uma política de imutabilidade num recipiente. |
| Eliminação macia do recipiente | Sem custos para permitir a eliminação suave do recipiente para uma conta de armazenamento. Os dados num recipiente eliminado são faturados ao mesmo ritmo que os dados ativos até que o recipiente eliminado seja permanentemente eliminado. |
| Versão blob | Sem custos para permitir a versão blob para uma conta de armazenamento. Após a visualização da versão blob, cada operação de escrita ou eliminação numa bolha na conta cria uma nova versão, o que pode levar a um aumento dos custos de capacidade.<br /><br />Uma versão blob é faturada com base em blocos ou páginas únicos. Por conseguinte, os custos aumentam à medida que a bolha de base diverge de uma determinada versão. Mudar o nível de uma versão blob ou blob pode ter um impacto de faturação. Para mais informações, consulte [preços e faturação.](versioning-overview.md#pricing-and-billing)<br /><br />Utilize a gestão do ciclo de vida para eliminar versões mais antigas, conforme necessário para controlar os custos. Para obter mais informações, consulte [os custos da Otimização automatizando os níveis de acesso ao armazenamento Azure Blob](storage-lifecycle-management-concepts.md). |
| Blob soft delete | Sem custos para permitir a eliminação suave da bolha para uma conta de armazenamento. Os dados numa bolha depagou-os é faturado ao mesmo ritmo que os dados ativos até que a bolha apagada seja permanentemente eliminada. |
| Restauro para um ponto anterior no tempo | Sem custos para permitir a restauração pontual para uma conta de armazenamento; no entanto, permitir a restauração pontual também permite a versão blob, a eliminação suave e o feed de alteração, cada um dos quais pode resultar em encargos adicionais.<br /><br />É cobrado para restauro pontual quando executa uma operação de restauro. O custo de uma operação de restauro depende da quantidade de dados que estão a ser restaurados. Para mais informações, consulte [preços e faturação.](point-in-time-restore-overview.md#pricing-and-billing) |
| Snapshots blob | Os dados de uma imagem instantânea são faturados com base em blocos ou páginas únicos. Os custos aumentam, portanto, à medida que a bolha de base diverge do instantâneo. Mudar o nível de uma bolha ou instantâneo pode ter um impacto de faturação. Para mais informações, consulte [preços e faturação.](snapshots-overview.md#pricing-and-billing)<br /><br />Utilize a gestão do ciclo de vida para eliminar as imagens mais antigas, conforme necessário para controlar os custos. Para obter mais informações, consulte [os custos da Otimização automatizando os níveis de acesso ao armazenamento Azure Blob](storage-lifecycle-management-concepts.md). |
| Copiar dados para uma segunda conta de armazenamento | A manutenção de dados numa segunda conta de armazenamento incorrerá em capacidade e custos de transação. Se a segunda conta de armazenamento estiver localizada numa região diferente da conta-fonte, então a cópia de dados para essa segunda conta incorrerá adicionalmente em encargos de erupção. |

## <a name="disaster-recovery"></a>Recuperação após desastre

O Azure Storage mantém sempre várias cópias dos seus dados de modo a que seja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. A redundância garante que a sua conta de armazenamento cumpre os seus objetivos de disponibilidade e durabilidade mesmo face a falhas. Para obter mais informações sobre como configurar a sua conta de armazenamento para uma elevada disponibilidade, consulte [a redundância do Azure Storage](../common/storage-redundancy.md).

No caso de ocorrer uma falha num centro de dados, se a sua conta de armazenamento for redundante em duas regiões geográficas (geo-redundantes), então tem a opção de falhar sobre a sua conta desde a região primária até à região secundária. Para obter mais informações, consulte [a recuperação de desastres e a falha da conta de armazenamento.](../common/storage-disaster-recovery-guidance.md)

O failover gerido pelo cliente não é atualmente suportado para contas de armazenamento com um espaço hierárquico de nome ativado. Para mais informações, consulte [as funcionalidades de armazenamento Blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="next-steps"></a>Passos seguintes

- [Redundância do Armazenamento do Azure](../common/storage-redundancy.md)
- [Recuperação após desastre e ativação pós-falha de contas de armazenamento](../common/storage-disaster-recovery-guidance.md)
