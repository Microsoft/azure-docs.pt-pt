---
title: Construir aplicações altamente disponíveis com armazenamento redundante em zona (ZRS)
titleSuffix: Azure Storage
description: O armazenamento redundante em zonas (ZRS) oferece uma forma simples de construir aplicações altamente disponíveis. O ZRS protege contra falhas de hardware no datacenter, e contra algumas catástrofes regionais.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 0e6b87ff34d6555fda50518198f9ae3839aa56e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719097"
---
# <a name="build-highly-available-applications-with-zone-redundant-storage-zrs"></a>Construir aplicações altamente disponíveis com armazenamento redundante em zona (ZRS)

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Cobertura de apoio e disponibilidade regional

Atualmente, o ZRS suporta os tipos padrão de v2 de uso geral, FileStorage e BlockBlobStorage. Para obter mais informações sobre tipos de conta de armazenamento, consulte [visão geral da conta de armazenamento do Azure](storage-account-overview.md).

Contas de v2 ZRS de propósito geral suportam bolhas de blocos, bolhas de página não-disco, partilhas de ficheiros padrão, tabelas e filas.

Para contas v2 de uso geral, o ZRS está geralmente disponível nas seguintes regiões:

- Sudeste Asiático
- Norte da Europa
- Europa Ocidental
- França Central
- Este do Japão
- Norte da África do Sul
- Sul do Reino Unido
- Centro dos EUA
- Este dos EUA
- Este dos EUA 2
- E.U.A. Oeste 2

Para contas de FileStorage (ações de ficheiropremium) e contas BlockBlobStorage (blobs de blocopremium), o ZRS está geralmente disponível nas seguintes regiões:

- Europa Ocidental
- Este dos EUA

A Microsoft continua a permitir o ZRS em regiões azure adicionais. Consulte regularmente a página de Atualizações de [Serviços Azure](https://azure.microsoft.com/updates/) para obter informações sobre novas regiões.

**Limitações conhecidas**

- O nível de arquivo não é atualmente suportado nas contas ZRS. Consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para obter mais detalhes.
- Os discos geridos não suportam ZRS. Você pode armazenar instantâneos e imagens para SSD Standard Managed Disks no armazenamento HDD Standard e [escolher entre as opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>O que acontece quando uma zona fica indisponível?

Os seus dados ainda estão acessíveis para operações de leitura e escrita, mesmo que uma zona fique indisponível. A Microsoft recomenda que continue a seguir práticas de manuseamento transitório de falhas. Essas práticas incluem a implementação de políticas de repetição com retirada exponencial.

Quando uma zona está indisponível, o Azure realiza atualizações de rede, como a repontação de DNS. Estas atualizações podem afetar a sua aplicação se estiver a aceder aos seus dados antes de as atualizações estarem concluídas.

O ZRS não pode proteger os seus dados contra uma catástrofe regional onde várias zonas são permanentemente afetadas. Em vez disso, o ZRS oferece resiliência para os seus dados se ficar temporariamente indisponível. Para proteção contra desastres regionais, a Microsoft recomenda a utilização de armazenamento geo-redundante (GRS). Para obter mais informações sobre grs, consulte [armazenamento geo-redundante (GRS): replicação inter-regional para armazenamento azure](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Conversão para replicação ZRS

Migrar de ou para LRS, GRS e RA-GRS é simples. Utilize o portal Azure ou a API do Fornecedor de Recursos de Armazenamento para alterar o tipo de despedimento da sua conta. O Azure irá então replicar os seus dados em conformidade. 

A migração de dados para o ZRS requer uma estratégia diferente. A migração do ZRS envolve o movimento físico de dados de um único carimbo de armazenamento para múltiplos selos dentro de uma região.

Existem duas opções primárias para a migração para zRS: 

- Copie ou mova manualmente os dados de uma conta existente para uma nova conta ZRS.
- Peça uma migração em direto.

> [!IMPORTANT]
> A migração ao vivo não é atualmente apoiada para ações de ficheiros premium. Apenas os dados de cópia manual ou de movimento são suportados atualmente.

Se precisar que a migração esteja concluída até uma determinada data, considere realizar uma migração manual. A migração manual proporciona mais flexibilidade do que a migração em direto. Com a migração manual, poderá controlar o momento da migração.

Para realizar uma migração manual, tem opções:
- Utilize ferramentas existentes como a AzCopy, uma das bibliotecas de clientes do Azure Storage, ou ferramentas de terceiros fiáveis.
- Se estiver familiarizado com Hadoop ou HDInsight, fixe a conta de origem e destino (ZRS) ao seu cluster. Em seguida, paraleleleleo o processo de cópia de dados com uma ferramenta como distCp.
- Crie suas próprias ferramentas usando uma das bibliotecas de cliente do armazenamento do Azure.

Uma migração manual pode resultar em tempo de inatividade do aplicativo. Se a aplicação exigir elevada disponibilidade, a Microsoft fornece a opção de migração em direto. A migração em direto é uma migração no local sem tempo de inatividade. 

Durante a migração em direto, poderá utilizar a sua conta de armazenamento enquanto os dados são migrados entre os carimbos de armazenamento de origem e destino. Durante o processo de migração, terá o mesmo nível de durabilidade e de disponibilidade do SLA que normalmente tem.

Tenha em mente as seguintes restrições na migração ao vivo:

- Embora a Microsoft processe com o seu pedido para migração em direto imediatamente, não há nenhuma garantia em relação a quando uma migração em direto será concluída. Se precisar dos dados migrados para o ZRS até uma determinada data, a Microsoft recomenda que efetue antes uma migração manual. Em geral, quanto mais dados tiver na conta, mais tempo levará a migrar esses dados. 
- A migração ao vivo é suportada apenas para contas de armazenamento que usam a replicação lRS. Se a sua conta utilizar GRS ou RA-GRS, então tem de alterar primeiro o tipo de replicação da sua conta para LRS antes de prosseguir. Este passo intermediário remove o ponto final secundário fornecido por GRS/RA-GRS.
- Sua conta deve conter dados.
- Você só pode migrar dados dentro da mesma região. Se pretender migrar os seus dados para uma conta ZRS localizada numa região diferente da conta fonte, então deve realizar uma migração manual.
- Somente os tipos de conta de armazenamento Standard dão suporte à migração dinâmica. As contas de armazenamento Premium devem ser migradas manualmente.
- A migração ao vivo de ZRS para LRS, GRS ou RA-GRS não é suportada. Terá de mover os dados manualmente para uma conta de armazenamento nova ou existente.
- Os discos geridos só estão disponíveis para LRS e não podem ser migrados para ZRS. Você pode armazenar instantâneos e imagens para SSD Standard Managed Disks no armazenamento HDD Standard e [escolher entre as opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Para integração com conjuntos de disponibilidade ver [Introdução aos discos geridos pelo Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)
- As contas LRS ou GRS com dados do Arquivo não podem ser migradas para ZRS.

Pode solicitar migração ao vivo através do [portal de suporte azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) A partir do portal, selecione a conta de armazenamento que pretende converter para ZRS.
1. Selecione **novo pedido de suporte**
2. Conclua os **conceitos básicos** com base nas informações da sua conta. Na secção **Serviço,** selecione **Gestão** de Conta de Armazenamento e o recurso que pretende converter para ZRS. 
3. Selecione **Seguinte**. 
4. Especifique os valores a seguir na seção **problema** : 
    - **Severidade**: Deixe o valor padrão como está.
    - **Tipo de problema**: selecione **migração de dados**.
    - **Categoria**: **Selecione Migrar para ZRS**.
    - **Denominação**: Digite um título descritivo, por exemplo, migração da **conta ZRS**.
    - **Detalhes**: Digite detalhes adicionais na caixa **de Detalhes,** por exemplo, gostaria de migrar para ZRS de [LRS, GRS] na região \_\_. 
5. Selecione **Seguinte**.
6. Verifique se as informações de contato estão corretas na folha **informações de contato** .
7. Selecione **Criar**.

Uma pessoa de apoio entrará em contacto consigo e fornecerá toda a assistência que precisar.

## <a name="live-migration-to-zrs-faq"></a>Migração ao vivo para zRS FAQ

**Devo planear algum tempo de inatividade durante a migração?**

Não há tempo de inatividade causado pela migração. Durante uma migração ao vivo, pode continuar a utilizar a sua conta de armazenamento enquanto os seus dados são migrados entre os selos de armazenamento de origem e destino. Durante o processo de migração, terá o mesmo nível de durabilidade e de disponibilidade do SLA que normalmente tem.

**Há alguma perda de dados associada à migração?**

Não há perda de dados associada à migração. Durante o processo de migração, terá o mesmo nível de durabilidade e de disponibilidade do SLA que normalmente tem.

**São necessárias atualizações para a(s) aplicação(s) uma vez concluída a migração?**

Uma vez concluída a migração, o tipo de replicação das contas altera-se para "Armazenamento redundante da zona (ZRS)". Os pontos finais do serviço, as teclas de acesso, o SAS e quaisquer outras opções de configuração da conta permanecem inalterados e intactos.

**Posso solicitar uma migração ao vivo das minhas contas v1 de propósito geral para zRS?**

O ZRS apenas suporta contas v2 de uso geral, por isso, antes de submeter um pedido de migração ao vivo para o ZRS, certifique-se de atualizar a sua conta(s) para v2 de propósito geral. Consulte a visão geral da conta de [armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) e atualize para uma conta de [armazenamento v2 de propósito geral](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para obter mais detalhes.

**Posso solicitar uma migração ao vivo da minha conta de armazenamento georedundante ou de acesso de leitura (GRS/RA-GRS) ao ZRS?**

A migração ao vivo é suportada apenas para contas de armazenamento que usam a replicação lRS. Se a sua conta utilizar GRS ou RA-GRS, então tem de alterar primeiro o tipo de replicação da sua conta para LRS antes de prosseguir. Este passo intermediário remove o ponto final secundário fornecido por GRS/RA-GRS. Além disso, antes de submeter um pedido de migração ao vivo para o ZRS certifique-se de que a sua aplicação ou carga de trabalho(s) já não requerem acesso ao ponto final de leitura secundário e altere o tipo de replicação da sua conta de armazenamento(s) para armazenamento redundante localmente (LRS). Consulte [a estratégia de replicação de Mudança](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) para mais detalhes.

**Posso solicitar uma migração viva da minha conta de armazenamento para zRS para outra região?**

Se pretender migrar os seus dados para uma conta ZRS localizada numa região diferente da região da conta fonte, então deve realizar uma migração manual.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS Classic: Uma opção de legado para a redundância de blocos blobs
> [!NOTE]
> A Microsoft irá depreciar e migrar as contas ZRS Classic a 31 de março de 2021. Mais detalhes serão fornecidos aos clientes ZRS Classic antes da depreciação. 
>
> Uma vez que o ZRS se torne [geralmente disponível](#support-coverage-and-regional-availability) numa região, os clientes não serão capazes de criar contas ZRS Classic a partir do Portal naquela região. Usar o Microsoft PowerShell e o Azure CLI para criar contas ZRS Classic é uma opção até que o ZRS Classic seja depreciado.

ZRS Classic replica sincronicamente dados em centros de dados dentro de uma a duas regiões. Os dados replicados podem não estar disponíveis a menos que a Microsoft inicie a falha no secundário. Uma conta ZRS Classic não pode ser convertida de ou de LRS, GRS ou RA-GRS. As contas ZRS Classic também não suportam métricas ou registos.

ZRS Classic está disponível apenas para **blocos** em contas de armazenamento V1 (GPv1) de uso geral. Para obter mais informações sobre as contas de armazenamento, veja [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

Para migrar manualmente os dados da conta ZRS de ou para uma conta LRS, ZRS Classic, GRS ou RA-GRS, utilize uma das seguintes ferramentas: AzCopy, Azure Storage Explorer, Azure PowerShell ou Azure CLI. Também pode construir a sua própria solução de migração com uma das bibliotecas de clientes do Azure Storage.

Também pode atualizar as suas contas ZRS Classic para ZRS no Portal ou utilizar o Azure PowerShell ou o Azure CLI nas regiões onde o ZRS está disponível. Para atualizar para ZRS no portal Azure, navegue para a secção de **Configuração** da conta e escolha **upgrade:**

![Upgrade ZRS Classic para ZRS no Portal](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

Para atualizar para ZRS usando powerShell ligue para o seguinte comando:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Para atualizar para ZRS utilizando cli ligue para o seguinte comando:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Ver também
- [Replicação do Armazenamento do Azure](storage-redundancy.md)
- [LRS (armazenamento com redundância local): redundância de dados de baixo custo para o armazenamento do Azure](storage-redundancy-lrs.md)
- [Armazenamento com redundância geográfica (GRS): replicação entre regiões para o armazenamento do Azure](storage-redundancy-grs.md)
