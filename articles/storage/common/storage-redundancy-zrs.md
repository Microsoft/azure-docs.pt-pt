---
title: Crie aplicativos de armazenamento do Azure altamente disponíveis no armazenamento com redundância de zona (ZRS) | Microsoft Docs
description: O ZRS (armazenamento com redundância de zona) oferece uma maneira simples de criar aplicativos altamente disponíveis. O ZRS protege contra falhas de hardware no datacenter e em alguns desastres regionais.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5fefe469bfac4816a67c6ceb344f12c1e52de60c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68550452"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Armazenamento com redundância de zona (ZRS): Aplicativos de armazenamento do Azure altamente disponíveis
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Cobertura de suporte e disponibilidade regional
Atualmente, o ZRS dá suporte a tipos de conta v2 de uso geral padrão. Para obter mais informações sobre tipos de conta de armazenamento, consulte [visão geral da conta de armazenamento do Azure](storage-account-overview.md).

O ZRS está disponível para BLOBs de blocos, blobs de páginas que não são de disco, arquivos, tabelas e filas.

O ZRS está geralmente disponível nas seguintes regiões:

- Sudeste Asiático
- Europa Ocidental
- Norte da Europa
- França Central
- Leste do Japão
- Reino Unido Sul
- Centro dos E.U.A.
- Leste dos E.U.A.
- E.U.A Leste 2
- Oeste dos E.U.A 2

A Microsoft continua a habilitar o ZRS em regiões adicionais do Azure. Verifique a página [atualizações de serviço do Azure](https://azure.microsoft.com/updates/) regularmente para obter informações sobre novas regiões.

**Limitações conhecidas**

- A camada de arquivo morto não tem suporte atualmente em contas ZRS. Consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para obter mais detalhes.
- Os discos gerenciados não dão suporte a ZRS. Você pode armazenar instantâneos e imagens para SSD Standard Managed Disks no armazenamento HDD Standard e [escolher entre as opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>O que acontece quando uma zona fica indisponível?
Seus dados ainda estarão acessíveis para operações de leitura e gravação, mesmo se uma zona ficar indisponível. A Microsoft recomenda que você continue seguindo as práticas de tratamento de falhas transitórias. Essas práticas incluem a implementação de políticas de repetição com retirada exponencial.

Quando uma zona não estiver disponível, o Azure assumirá as atualizações de rede, como a reapontação de DNS. Essas atualizações podem afetar seu aplicativo se você estiver acessando seus dados antes que as atualizações sejam concluídas.

O ZRS pode não proteger seus dados contra um desastre regional em que várias zonas são permanentemente afetadas. Em vez disso, o ZRS oferece resiliência para seus dados se ficar temporariamente indisponível. Para proteção contra desastres regionais, a Microsoft recomenda o uso do GRS (armazenamento com redundância geográfica). Para obter mais informações sobre grs, [consulte armazenamento com redundância geográfica (GRS): Replicação entre regiões para o armazenamento](storage-redundancy-grs.md)do Azure.

## <a name="converting-to-zrs-replication"></a>Convertendo para replicação ZRS
Migrar de ou para LRS, GRS e RA-GRS é simples. Use o portal do Azure ou a API do provedor de recursos de armazenamento para alterar o tipo de redundância da sua conta. O Azure replicará seus dados de forma adequada. 

A migração de dados para o ZRS requer uma estratégia diferente. A migração do ZRS envolve a movimentação física de dados de um único carimbo de armazenamento para vários carimbos em uma região.

Há duas opções principais para a migração para o ZRS: 

- Copiar ou mover dados manualmente para uma nova conta do ZRS de uma conta existente.
- Solicite uma migração ao vivo.

A Microsoft recomenda enfaticamente que você execute uma migração manual. Uma migração manual fornece mais flexibilidade do que uma migração dinâmica. Com uma migração manual, você está no controle do tempo.

Para executar uma migração manual, você tem opções:
- Use ferramentas existentes como o AzCopy, uma das bibliotecas de cliente de armazenamento do Azure ou ferramentas de terceiros confiáveis.
- Se você estiver familiarizado com o Hadoop ou o HDInsight, anexe a conta de origem e destino (ZRS) ao cluster. Em seguida, paralelizar o processo de cópia de dados com uma ferramenta como DistCp.
- Crie suas próprias ferramentas usando uma das bibliotecas de cliente do armazenamento do Azure.

Uma migração manual pode resultar em tempo de inatividade do aplicativo. Se seu aplicativo exigir alta disponibilidade, a Microsoft também fornecerá uma opção de migração dinâmica. Uma migração ao vivo é uma migração in-loco. 

Durante uma migração ao vivo, você pode usar sua conta de armazenamento enquanto os dados são migrados entre carimbos de armazenamento de origem e de destino. Durante o processo de migração, você tem o mesmo nível de SLA de durabilidade e disponibilidade que normalmente faz.

Tenha em mente as seguintes restrições na migração ao vivo:

- Embora a Microsoft processe com o seu pedido para migração em direto imediatamente, não há nenhuma garantia em relação a quando uma migração em direto será concluída. Se você precisar que seus dados sejam migrados para ZRS por uma determinada data, a Microsoft recomenda que você execute uma migração manual em vez disso. Em geral, quanto mais dados tiver na conta, mais tempo levará a migrar esses dados. 
- Há suporte para a migração dinâmica apenas para contas de armazenamento que usam a replicação LRS ou GRS. Se sua conta usar RA-GRS, você precisará primeiro alterar o tipo de replicação da sua conta para LRS ou GRS antes de continuar. Essa etapa intermediária remove o ponto de extremidade secundário somente leitura fornecido pelo RA-GRS antes da migração.
- Sua conta deve conter dados.
- Você só pode migrar dados dentro da mesma região. Se desejar migrar seus dados para uma conta do ZRS localizada em uma região diferente da conta de origem, você deverá executar uma migração manual.
- Somente os tipos de conta de armazenamento Standard dão suporte à migração dinâmica. As contas de armazenamento Premium devem ser migradas manualmente.
- Não há suporte para a migração dinâmica de ZRS para LRS, GRS ou RA-GRS. Será necessário mover os dados manualmente para uma conta de armazenamento nova ou existente.
- Os discos gerenciados estão disponíveis somente para LRS e não podem ser migrados para o ZRS. Você pode armazenar instantâneos e imagens para SSD Standard Managed Disks no armazenamento HDD Standard e [escolher entre as opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Para integração com conjuntos de disponibilidade, consulte [introdução aos Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Contas LRS ou GRS com dados de arquivo morto não podem ser migradas para ZRS.

Você pode solicitar a migração dinâmica por meio do [portal de suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). No portal, selecione a conta de armazenamento que você deseja converter em ZRS.
1. Selecionar **nova solicitação de suporte**
2. Conclua os **conceitos básicos** com base nas informações da sua conta. Na seção **serviço** , selecione **Gerenciamento de conta de armazenamento** e o recurso que você deseja converter em ZRS. 
3. Selecione **Seguinte**. 
4. Especifique os valores a seguir na seção **problema** : 
    - **Gravidade**: Deixe o valor padrão como está.
    - **Tipo de problema**: Selecione **migração de dados**.
    - **Categoria**: Selecione **migrar para ZRS em uma região**.
    - **Título**: Digite um título descritivo, por exemplo, **migração de conta do ZRS**.
    - **Detalhes**: Digite detalhes adicionais na caixa de **detalhes** , por exemplo, eu gostaria de migrar para ZRS de [lRS, grs] na \_ \_ região. 
5. Selecione **Seguinte**.
6. Verifique se as informações de contato estão corretas na folha **informações de contato** .
7. Selecione **Criar**.

Uma pessoa de suporte entrará em contato com você e fornecerá qualquer assistência necessária.

## <a name="live-migration-to-zrs-faq"></a>Perguntas frequentes sobre migração ao vivo para ZRS

**Devo planejar qualquer tempo de inatividade durante a migração?**

Não há nenhum tempo de inatividade causado pela migração. Durante uma migração ao vivo, você pode continuar usando sua conta de armazenamento enquanto os dados são migrados entre carimbos de armazenamento de origem e de destino. Durante o processo de migração, você tem o mesmo nível de SLA de durabilidade e disponibilidade que normalmente faz.

**Há alguma perda de dados associada à migração?**

Não há perda de dados associada à migração. Durante o processo de migração, você tem o mesmo nível de SLA de durabilidade e disponibilidade que normalmente faz.

**As atualizações são necessárias para os aplicativos quando a migração for concluída?**

Depois que a migração for concluída, o tipo de replicação das contas será alterado para "armazenamento com redundância de zona (ZRS)". Pontos de extremidade de serviço, chaves de acesso, SAS e quaisquer outras opções de configuração de conta permanecem inalterados e intactos.

**Posso solicitar uma migração dinâmica de minhas contas de uso geral v1 para ZRS?**

O ZRS só dá suporte a contas v2 de uso geral, portanto, antes de enviar uma solicitação de migração ao vivo para o ZRS, atualize suas contas para uso geral v2. Confira [visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) e [atualize para uma conta de armazenamento v2 de uso geral](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para obter mais detalhes.

**Posso solicitar uma migração dinâmica de minhas contas de armazenamento com redundância geográfica com acesso de leitura (RA-GRS) para ZRS?**

Antes de enviar uma solicitação de migração ao vivo para ZRS, verifique se seus aplicativos ou cargas de trabalho não exigem mais acesso ao ponto de extremidade secundário somente leitura e altere o tipo de replicação de suas contas de armazenamento para o armazenamento com redundância geográfica (GRS). Consulte [alterando a estratégia de replicação](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) para obter mais detalhes.

**Posso solicitar uma migração dinâmica de minhas contas de armazenamento para ZRS para outra região?**

Se desejar migrar seus dados para uma conta do ZRS localizada em uma região diferente da região da conta de origem, você deverá executar uma migração manual.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS clássico: Uma opção herdada para redundância de blobs de blocos
> [!NOTE]
> A Microsoft substituirá e migrará contas clássicas do ZRS em 31 de março de 2021. Mais detalhes serão fornecidos aos clientes ZRS clássicos antes da reprovação. 
>
> Depois que o ZRS se tornar [disponível](#support-coverage-and-regional-availability) em uma região, os clientes não poderão criar contas ZRS clássicas do portal nessa região. Usar o Microsoft PowerShell e o CLI do Azure para criar contas clássicas do ZRS é uma opção até que o ZRS clássico seja preterido.

O ZRS Classic replica dados de forma assíncrona em data centers em uma ou duas regiões. Os dados replicados podem não estar disponíveis, a menos que a Microsoft inicie o failover para o secundário. Uma conta clássica do ZRS não pode ser convertida de/para LRS, GRS ou RA-GRS. Contas clássicas do ZRS também não dão suporte a métricas ou log.

O ZRS clássico está disponível somente para BLOBs de **blocos** nas contas de armazenamento de uso geral V1 (GPv1). Para obter mais informações sobre as contas de armazenamento, veja [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

Para migrar manualmente os dados da conta do ZRS para ou de uma conta LRS, ZRS Classic, GRS ou RA-GRS, use uma das seguintes ferramentas: AzCopy, Gerenciador de Armazenamento do Azure, Azure PowerShell ou CLI do Azure. Você também pode criar sua própria solução de migração com uma das bibliotecas de cliente de armazenamento do Azure.

Você também pode atualizar suas contas ZRS clássicas para ZRS no portal ou usando Azure PowerShell ou CLI do Azure nas regiões em que ZRS está disponível.

Para atualizar para o ZRS no portal, acesse a seção de configuração da conta e escolha Atualizar:![Atualizar ZRS clássico para ZRS no portal](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.jpg)

Para atualizar para o ZRS usando o PowerShell, chame o seguinte comando:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Para atualizar para o ZRS usando a CLI, chame o seguinte comando:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Consulte também
- [Replicação do Armazenamento do Azure](storage-redundancy.md)
- [LRS (armazenamento com redundância local): Redundância de dados de baixo custo para o armazenamento do Azure](storage-redundancy-lrs.md)
- [Armazenamento com redundância geográfica (GRS): Replicação entre regiões para o armazenamento do Azure](storage-redundancy-grs.md)
