---
title: Alterar a forma como uma conta de armazenamento é replicada
titleSuffix: Azure Storage
description: Saiba como alterar a forma como os dados numa conta de armazenamento existente é replicado.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce3bda82e634cd80560d7915a08fa33218173779
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967204"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Alterar a forma como uma conta de armazenamento é replicada

O Azure Storage armazena sempre várias cópias dos seus dados para que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. A redundância garante que a sua conta de armazenamento cumpre o [Acordo de Nível de Serviço (SLA) para o Armazenamento Azure,](https://azure.microsoft.com/support/legal/sla/storage/) mesmo face a falhas.

O Azure Storage oferece os seguintes tipos de replicação:

- Armazenamento localmente redundante (LRS)
- Armazenamento com redundância entre zonas (ZRS)
- Armazenamento geodusu redundante (GRS) ou armazenamento geo-redundante de acesso à leitura (RA-GRS)
- Armazenamento redundante de zonas geo-zona (GZRS) ou armazenamento de zonas de acesso à leitura redundante (RA-GZRS)

Para uma visão geral de cada uma destas opções, consulte [a redundância do Azure Storage](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Alternar entre tipos de replicação

Pode mudar uma conta de armazenamento de um tipo de replicação para qualquer outro tipo, mas alguns cenários são mais simples do que outros. Se pretender adicionar ou remover a geo-replicação ou ler o acesso à região secundária, pode utilizar o portal Azure, PowerShell ou Azure CLI para atualizar a definição de replicação. No entanto, se quiser alterar a forma como os dados são replicados na região primária, movendo-se de LRS para ZRS ou vice-versa, então deve realizar uma migração manual.

A tabela a seguir fornece uma visão geral de como mudar de cada tipo de replicação para outro:

| Comutação | ... para o LRS | ... a GRS/RA-GRS | ... para ZRS | ... para GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... do LRS</b> | N/D | Utilize o portal Azure, PowerShell ou CLI para alterar a definição de replicação<sup>1,2</sup> | Realizar uma migração manual <br /><br /> OR <br /><br /> Solicite uma migração ao vivo | Realizar uma migração manual <br /><br /> OR <br /><br /> Mude primeiro para GRS/RA-GRS e, em seguida, solicite uma migração ao vivo<sup>1</sup> |
| <b>... de GRS/RA-GRS</b> | Use o portal Azure, PowerShell ou CLI para alterar a definição de replicação | N/D | Realizar uma migração manual <br /><br /> OR <br /><br /> Mude primeiro para LRS e, em seguida, solicite uma migração ao vivo | Realizar uma migração manual <br /><br /> OR <br /><br /> Solicite uma migração ao vivo |
| <b>... da ZRS</b> | Realizar uma migração manual | Realizar uma migração manual | N/D | Solicite uma migração ao vivo |
| <b>... de GZRS/RA-GZRS</b> | Realizar uma migração manual | Realizar uma migração manual | Use o portal Azure, PowerShell ou CLI para alterar a definição de replicação | N/D |

<sup>1</sup> Incorre numa carga de saída única.<br />
<sup>2</sup> A migração de LRS para GRS não é suportada se a conta de armazenamento contiver bolhas no nível de arquivo.<br />
<sup>3</sup> A conversão de ZRS para GZRS/RA-GZRS ou vice-versa não é apoiada nas seguintes regiões: US East 2, US East, Europe West.

> [!CAUTION]
> Se tiver feito uma falha de [conta](storage-disaster-recovery-guidance.md) na sua conta (RA-)GRS ou (RA-)GZRS, a conta é localmente redundante (LRS) na nova região primária após a falência. A migração ao vivo para ZRS ou GZRS para uma conta LRS resultante de um failover não é suportada. Isto é verdade mesmo no caso das chamadas operações de repusão. Por exemplo, se efetuar uma falha de conta de RA-GZRS para o LRS na região secundária, e depois configurá-la novamente para RA-GRS e executar outra falha de conta para a região primária original, não pode contactar o suporte para a migração original ao vivo para RA-GZRS na região primária. Em vez disso, terá de realizar uma migração manual para ZRS ou GZRS.

## <a name="change-the-replication-setting"></a>Alterar a definição de replicação

Pode utilizar o portal Azure, PowerShell ou Azure CLI para alterar a definição de replicação para uma conta de armazenamento, desde que não esteja a alterar a forma como os dados são replicados na região primária. Se você está migrando de LRS na região primária para ZRS na região primária ou vice-versa, então você deve realizar uma migração manual ou uma migração ao vivo.

Alterar a forma como a sua conta de armazenamento é replicada não resulta em tempo de inação para as suas aplicações.

# <a name="portal"></a>[Portal](#tab/portal)

Para alterar a opção de redundância para a sua conta de armazenamento no portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Selecione a **definição de configuração.**
1. Atualize a **definição de replicação.**

![Screenshot mostrando como alterar opção de replicação no portal](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para alterar a opção de redundância da sua conta de armazenamento com o PowerShell, ligue para o comando [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e especifique o `-SkuName` parâmetro:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para alterar a opção de redundância da sua conta de armazenamento com o Azure CLI, ligue para o comando de atualização da [conta de armazenamento AZ](/cli/azure/storage/account#az-storage-account-update) e especifique o `--sku` parâmetro:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Realizar uma migração manual para ZRS, GZRS ou RA-GZRS

Se quiser alterar a forma como os dados na sua conta de armazenamento são replicados na região primária, movendo-se de LRS para ZRS ou vice-versa, então pode optar por realizar uma migração manual. A migração manual proporciona mais flexibilidade do que a migração em direto. Você controla o tempo de uma migração manual, então use esta opção se precisar que a migração complete até uma determinada data.

Quando realiza uma migração manual de LRS para ZRS na região primária ou vice-versa, a conta de armazenamento de destino pode ser geo-redundante e também pode ser configurada para o acesso lido à região secundária. Por exemplo, pode migrar uma conta LRS para uma conta GZRS ou RA-GZRS num só passo.

Uma migração manual pode resultar em tempo de inatividade da aplicação. Se a aplicação exigir elevada disponibilidade, a Microsoft fornece a opção de migração em direto. A migração em direto é uma migração no local sem tempo de inatividade.

Com uma migração manual, copia os dados da sua conta de armazenamento existente para uma nova conta de armazenamento que utiliza ZRS na região primária. Para realizar uma migração manual, pode utilizar uma das seguintes opções:

- Copie os dados utilizando uma ferramenta existente, como a AzCopy, uma das bibliotecas de clientes do Azure Storage ou uma ferramenta de terceiros fiável.
- Se estiver familiarizado com Hadoop ou HDInsight, pode anexar a conta de armazenamento de fonte e a conta de armazenamento de destino ao seu cluster. Em seguida, paralelize o processo de cópia de dados com uma ferramenta como o DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Solicite uma migração ao vivo para ZRS, GZRS ou RA-GZRS

Se precisar de migrar a sua conta de armazenamento de LRS para ZRS na região primária sem tempo de inatividade da aplicação, pode solicitar uma migração ao vivo da Microsoft. Para migrar de LRS para GZRS ou RA-GZRS, mude primeiro para GRS ou RA-GRS e, em seguida, solicite uma migração ao vivo. Da mesma forma, você pode solicitar uma migração ao vivo de GRS ou RA-GRS para GZRS ou RA-GZRS. Para migrar de GRS ou RA-GRS para ZRS, mude primeiro para LRS e, em seguida, solicite uma migração ao vivo.

Durante uma migração ao vivo, pode aceder a dados na sua conta de armazenamento sem perda de durabilidade ou disponibilidade. O Azure Storage SLA é mantido durante o processo de migração. Não há perda de dados associada a uma migração ao vivo. Os pontos finais do serviço, as chaves de acesso, as assinaturas de acesso partilhado e outras opções de conta permanecem inalteradas após a migração.

O ZRS suporta apenas contas v2 para fins gerais, por isso certifique-se de atualizar a sua conta de armazenamento antes de submeter um pedido de migração ao vivo para zRS. Para obter mais informações, consulte [upgrade para uma conta de armazenamento v2 para fins gerais.](storage-account-upgrade.md) Uma conta de armazenamento deve conter dados a serem migrados através da migração ao vivo.

A migração ao vivo é suportada apenas para contas de armazenamento que usam replicação LRS ou GRS. Se a sua conta utilizar RA-GRS, primeiro tem de alterar o tipo de replicação da sua conta para LRS ou GRS antes de prosseguir. Este passo intermediário remove o ponto final secundário apenas de leitura fornecido pela RA-GRS antes da migração.

Embora a Microsoft processe com o seu pedido para migração em direto imediatamente, não há nenhuma garantia em relação a quando uma migração em direto será concluída. Se precisar dos dados migrados para o ZRS até uma determinada data, a Microsoft recomenda que efetue antes uma migração manual. Em geral, quanto mais dados tiver na conta, mais tempo levará a migrar esses dados.

Deve efetuar uma migração manual se:

- Pretende migrar os seus dados para uma conta de armazenamento ZRS que está localizada numa região diferente da conta de origem.
- A sua conta de armazenamento é uma bolha de página premium ou uma conta blob de bloco.
- Pretende migrar dados de ZRS para LRS, GRS ou RA-GRS.
- A sua conta de armazenamento inclui dados no nível de arquivo.

Pode solicitar migração ao vivo através do [portal Azure Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). 

> [!IMPORTANT]
> Se precisar de migrar mais de uma conta de armazenamento, crie um bilhete de suporte único e especifique os nomes das contas para converter no separador **Detalhes.**

Siga estes passos para solicitar uma migração ao vivo:

1. No portal Azure, navegue para uma conta de armazenamento que pretende migrar.
1. No **suporte + resolução de problemas,** selecione **Novo Pedido de Suporte**.
1. Preencha o **separador Básicos** com base nas informações da sua conta:
    - **Tipo de emissão**: Selecione **Técnico**.
    - **Serviço**: Selecione **os meus serviços,** em seguida, **Gestão de Conta de Armazenamento**.
    - **Recurso**: Selecione uma conta de armazenamento para migrar. Se precisar de especificar várias contas de armazenamento, pode fazê-lo na secção **Detalhes.**
    - **Tipo de problema**: Escolha **a migração de dados.**
    - **Subtipo de problema:** Escolha **migrar para ZRS, GZRS ou RA-GZRS**.

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="Screenshot mostrando como solicitar uma migração ao vivo - Separador Básico":::

1. Selecione **Seguinte**. No separador **Soluções,** pode verificar a elegibilidade das suas contas de armazenamento para migração.
1. Selecione **Seguinte**. Se tiver mais de uma conta de armazenamento para migrar, então no separador **Detalhes,** especifique o nome de cada conta, separada por um ponto e vírgula.

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="Screenshot mostrando como solicitar uma migração ao vivo - Separador de detalhes":::

1. Preencha as informações adicionais **necessárias** no separador Detalhes e, em seguida, selecione **Review + create** para rever e submeter o seu bilhete de apoio. Uma pessoa de apoio entrará em contacto consigo para prestar qualquer assistência que possa necessitar.

> [!NOTE]
> As ações de ficheiros premium (contas FileStorage) só estão disponíveis para LRS e ZRS.
>
> As contas de armazenamento GZRS não suportam atualmente o nível de arquivo. Consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e arquivados](../blobs/storage-blob-storage-tiers.md) para obter mais detalhes.
>
> Os discos geridos só estão disponíveis para ORS e não podem ser migrados para ZRS. Pode armazenar instantâneos e imagens para discos geridos SSD padrão no armazenamento HDD padrão e [escolher entre as opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Para obter informações sobre a integração com conjuntos de disponibilidade, consulte [discos geridos introdução ao Azure](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Switch do ZRS Classic

> [!IMPORTANT]
> A Microsoft vai depreciar e migrar as contas ZRS Classic a 31 de março de 2021. Mais detalhes serão fornecidos aos clientes ZRS Classic antes da depreciação.
>
> Depois de o ZRS se tornar geralmente disponível numa determinada região, os clientes deixarão de poder criar contas ZRS Classic a partir do portal Azure naquela região. Usar o Microsoft PowerShell e o Azure CLI para criar contas ZRS Classic é uma opção até que o ZRS Classic seja depreciado. Para obter informações sobre onde o ZRS está disponível, consulte [a redundância do Azure Storage](storage-redundancy.md).

ZRS Classic reproduz assíncronamente dados em centros de dados dentro de uma a duas regiões. Os dados replicados podem não estar disponíveis a menos que a Microsoft inicie a falha no secundário. Uma conta ZRS Classic não pode ser convertida para ou de LRS, GRS ou RA-GRS. As contas ZRS Classic também não suportam métricas ou registos.

A ZRS Classic está disponível apenas para bolhas de **blocos** em contas de armazenamento V1 (GPv1) de uso geral. Para obter mais informações sobre contas de armazenamento, consulte [a visão geral da conta de armazenamento Azure](storage-account-overview.md).

Para migrar manualmente os dados da conta ZRS de ou para uma conta LRS, GRS, RA-GRS ou ZRS Classic, utilize uma das seguintes ferramentas: AzCopy, Azure Storage Explorer, PowerShell ou Azure CLI. Também pode construir a sua própria solução de migração com uma das bibliotecas de clientes do Azure Storage.

Também pode atualizar a sua conta de armazenamento ZRS Classic para zRS utilizando o portal Azure, PowerShell ou Azure CLI em regiões onde o ZRS está disponível.

# <a name="portal"></a>[Portal](#tab/portal)

Para fazer o upgrade para ZRS no portal Azure, navegue para as definições de **Configuração** da conta e escolha **Upgrade**:

![Atualizar do ZRS Clássico para o ZRS no Portal](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para atualizar para ZRS usando PowerShell, ligue para o seguinte comando:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para atualizar para ZRS usando Azure CLI, ligue para o seguinte comando:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Custos associados à alteração da forma como os dados são replicados

Os custos associados à alteração da forma como os dados são replicados dependem da sua trajetória de conversão. As ofertas de despedimentos da Azure Storage incluem LRS, ZRS, GRS, RA-GRS, GZRS e RA-GZRS.

Por exemplo, passar *de* LRS para qualquer outro tipo de replicação irá incorrer em encargos adicionais porque você está se movendo para um nível de redundância mais sofisticado. Migrar *para* GRS ou RA-GRS incorrerá numa carga de largura de banda de saída porque os seus dados (na sua região primária) estão a ser replicados para a sua região secundária remota. Esta carga é um custo único na configuração inicial. Depois de os dados serem copiados, não há mais taxas de migração. Para obter detalhes sobre as taxas de largura de banda, consulte [a página de preços de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se migrar a sua conta de armazenamento de GRS para LRS, não há custo adicional, mas os seus dados replicados são eliminados da localização secundária.

> [!IMPORTANT]
> Se migrar a sua conta de armazenamento de RA-GRS para GRS ou LRS, essa conta é faturada como RA-GRS por mais 30 dias além da data em que foi convertida.

## <a name="see-also"></a>Ver também

- [Redundância do Armazenamento do Azure](storage-redundancy.md)
- [Verifique a propriedade da Última Hora do Sincronização para obter uma conta de armazenamento](last-sync-time-get.md)
- [Use geo-redundância para projetar aplicações altamente disponíveis](geo-redundant-design.md)
