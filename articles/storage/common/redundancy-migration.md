---
title: Alterar a forma como uma conta de armazenamento é replicada
titleSuffix: Azure Storage
description: Saiba como alterar a forma como os dados numa conta de armazenamento existente são replicados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337080"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Alterar a forma como uma conta de armazenamento é replicada

O Azure Storage armazena sempre várias cópias dos seus dados para que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. O despedimento garante que a sua conta de armazenamento cumpre o [Acordo de Nível de Serviço (SLA) para o Armazenamento Azure](https://azure.microsoft.com/support/legal/sla/storage/) mesmo face a falhas.

O Azure Storage oferece os seguintes tipos de replicação:

- Armazenamento localmente redundante (LRS)
- Armazenamento com redundância entre zonas (ZRS)
- Armazenamento geo-redundante (GRS) ou armazenamento geo-redundante de acesso à leitura (RA-GRS)
- Armazenamento geo-zona redundante (GZRS) ou armazenamento geozona-redundante de acesso à leitura (RA-GZRS) (pré-visualização)

Para uma visão geral de cada uma destas opções, consulte o [despedimento do Azure Storage.](storage-redundancy.md)

## <a name="switch-between-types-of-replication"></a>Alternar entre tipos de replicação

Pode mudar uma conta de armazenamento de um tipo de replicação para qualquer outro tipo, mas alguns cenários são mais simples do que outros. Se pretender adicionar ou remover a geo-replicação ou ler o acesso à região secundária, pode utilizar o portal Azure, PowerShell ou Azure CLI para atualizar a definição de replicação. No entanto, se quiser alterar a forma como os dados são replicados na região primária, movendo-se de LRS para ZRS ou vice-versa, então deve realizar uma migração manual.

A tabela seguinte fornece uma visão geral de como mudar de cada tipo de replicação para outro:

| Comutação | ... para LRS | ... ao GRS/RA-GRS | ... para zrs | ... para GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... de LRS</b> | N/D | Utilize o portal Azure, PowerShell ou CLI para alterar a definição de replicação<sup>1</sup> | Realizar uma migração manual <br /><br />Solicitar uma migração ao vivo | Realizar uma migração manual <br /><br /> OU <br /><br /> Mude para GRS/RA-GRS primeiro e, em seguida, solicite uma migração ao vivo<sup>1</sup> |
| <b>... de GRS/RA-GRS</b> | Utilize o portal Azure, PowerShell ou CLI para alterar a definição de replicação | N/D | Realizar uma migração manual <br /><br /> OU <br /><br /> Mude para LRS primeiro e depois solicite uma migração ao vivo | Realizar uma migração manual <br /><br /> Solicitar uma migração ao vivo |
| <b>... do ZRS</b> | Realizar uma migração manual | Realizar uma migração manual | N/D | Utilize o portal Azure, PowerShell ou CLI para alterar a definição de replicação<sup>1</sup> |
| <b>... de GZRS/RA-GZRS</b> | Realizar uma migração manual | Realizar uma migração manual | Utilize o portal Azure, PowerShell ou CLI para alterar a definição de replicação | N/D |

<sup>1</sup> Incorra numa carga única de saída.

> [!CAUTION]
> Se realizou uma falha de [conta](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance) para a sua conta (RA-)GRS ou (RA-)GZRS, está configurada para ser localmente redundante na nova região primária. A migração ao vivo para ZRS ou GZRS para tais contas LRS não é suportada. Terá de realizar a [migração manual.](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs)

## <a name="change-the-replication-setting"></a>Alterar a definição de replicação

Pode utilizar o portal Azure, PowerShell ou Azure CLI para alterar a definição de replicação para uma conta de armazenamento, desde que não esteja a alterar a forma como os dados são replicados na região primária. Se está a migrar do LRS na região primária para o ZRS na região primária ou vice-versa, então deve realizar uma [migração manual](#perform-a-manual-migration-to-zrs) ou uma [migração viva.](#request-a-live-migration-to-zrs)

Alterar a forma como a sua conta de armazenamento é replicada não resulta em tempo de paragem para as suas aplicações.

# <a name="portal"></a>[Portal](#tab/portal)

Para alterar a opção de despedimento para a sua conta de armazenamento no portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Selecione a definição de **Configuração.**
1. Atualize a definição de **Replicação.**

![Screenshot mostrando como mudar opção de replicação no portal](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para alterar a opção de redundância para a sua conta de armazenamento `-SkuName` com a PowerShell, ligue para o comando [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e especifique o parâmetro:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para alterar a opção de despedimento para a sua conta de armazenamento com `--sku` o Azure CLI, ligue para o comando de atualização da conta de armazenamento [az](/cli/azure/storage/account#az-storage-account-update) e especifique o parâmetro:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>Realizar uma migração manual para zRS

Se pretender alterar a forma como os dados na sua conta de armazenamento são replicados na região primária, passando de LRS para ZRS ou vice-versa, então poderá optar por realizar uma migração manual. A migração manual proporciona mais flexibilidade do que a migração em direto. Você controla o tempo de uma migração manual, por isso use esta opção se precisar da migração para completar até uma determinada data.

Quando realiza uma migração manual de LRS para ZRS na região primária ou vice-versa, a conta de armazenamento de destino pode ser geo-redundante e também pode ser configurada para ler o acesso à região secundária. Por exemplo, pode migrar uma conta LRS para uma conta GZRS ou RA-GZRS num só passo.

Uma migração manual pode resultar em tempo de inatividade de aplicação. Se a aplicação exigir elevada disponibilidade, a Microsoft fornece a opção de migração em direto. A migração em direto é uma migração no local sem tempo de inatividade.

Com uma migração manual, copia os dados da sua conta de armazenamento existente para uma nova conta de armazenamento que utiliza ZRS na região primária. Para realizar uma migração manual, pode utilizar uma das seguintes opções:

- Copiar dados utilizando uma ferramenta existente, como o AzCopy, uma das bibliotecas de clientes do Azure Storage, ou uma ferramenta de terceiros confiável.
- Se estiver familiarizado com Hadoop ou HDInsight, pode anexar a conta de armazenamento de origem e a conta de armazenamento de destino ao seu cluster. Em seguida, paraleleleleo o processo de cópia de dados com uma ferramenta como distCp.

## <a name="request-a-live-migration-to-zrs"></a>Solicite uma migração ao vivo para o ZRS

Se precisar de migrar a sua conta de armazenamento de LRS ou GRS para ZRS na região primária sem tempo de inatividade de aplicação, pode solicitar uma migração ao vivo da Microsoft. Durante uma migração ao vivo, pode aceder a dados na sua conta de armazenamento e sem perda de durabilidade ou disponibilidade. O Azure Storage SLA é mantido durante o processo de migração. Não há perda de dados associada a uma migração viva. Os pontos finais do serviço, as chaves de acesso, as assinaturas de acesso partilhado e outras opções de conta permanecem inalterados após a migração.

O ZRS suporta apenas contas v2 de uso geral, por isso certifique-se de atualizar a sua conta de armazenamento antes de submeter um pedido de migração ao vivo para zRS. Para mais informações, consulte [o Upgrade para uma conta de armazenamento v2 de uso geral](storage-account-upgrade.md). Uma conta de armazenamento deve conter dados a migrar através de migração viva.

A migração ao vivo é suportada apenas para contas de armazenamento que usam replicação LRS ou GRS. Se a sua conta utilizar RA-GRS, então tem de alterar primeiro o tipo de replicação da sua conta para LRS ou GRS antes de prosseguir. Este passo intermediário remove o ponto final secundário de leitura apenas fornecido por RA-GRS antes da migração.

Embora a Microsoft processe com o seu pedido para migração em direto imediatamente, não há nenhuma garantia em relação a quando uma migração em direto será concluída. Se precisar dos dados migrados para o ZRS até uma determinada data, a Microsoft recomenda que efetue antes uma migração manual. Em geral, quanto mais dados tiver na conta, mais tempo levará a migrar esses dados.

Deve efetuar uma migração manual se:

- Pretende migrar os seus dados para uma conta de armazenamento ZRS localizada numa região diferente da conta fonte.
- A sua conta de armazenamento é uma página premium blob ou conta de bloco blob.
- Pretende migrar dados de ZRS para LRS, GRS ou RA-GRS.
- A sua conta de armazenamento inclui dados no nível de arquivo.

Pode solicitar migração ao vivo através do [portal de suporte azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) A partir do portal, selecione a conta de armazenamento que pretende converter para ZRS.

1. Selecione **novo pedido de suporte**
2. Complete o **Básico** com base nas informações da sua conta. Na secção **Serviço,** selecione **Gestão** de Conta de Armazenamento e o recurso que pretende converter para ZRS.
3. Selecione **Seguinte**.
4. Especificar os seguintes valores na secção **Problema:**
    - **Gravidade**: Deixe o valor padrão como está.
    - **Tipo de problema:** Selecione **migração de dados**.
    - **Categoria**: **Selecione Migrar para ZRS**.
    - **Denominação**: Digite um título descritivo, por exemplo, migração da **conta ZRS**.
    - **Detalhes**: Digite detalhes adicionais na caixa **de Detalhes,** por exemplo, gostaria de migrar para \_ \_ ZRS de [LRS, GRS] na região.
5. Selecione **Seguinte**.
6. Verifique se as informações de contacto estão corretas na lâmina de informação do **contacto.**
7. Selecione **Criar**.

Uma pessoa de apoio entrará em contacto consigo e fornecerá toda a assistência que precisar.

> [!NOTE]
> A migração ao vivo não é atualmente apoiada para ações de ficheiros premium. Apenas os dados de cópia manual ou de movimento são suportados atualmente.
>
> As contas de armazenamento GZRS não suportam atualmente o nível de arquivo. Consulte [o armazenamento Azure Blob: hot, cool e archive access tiers](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para mais detalhes.
>
> Os discos geridos só estão disponíveis para LRS e não podem ser migrados para ZRS. Pode armazenar instantâneos e imagens para discos geridos por SSD padrão no armazenamento HDD padrão e [escolher entre opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Para obter informações sobre integração com conjuntos de disponibilidade, consulte [Introdução aos discos geridos pelo Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)

## <a name="switch-from-zrs-classic"></a>Mudar de ZRS Classic

> [!IMPORTANT]
> A Microsoft irá depreciar e migrar as contas ZRS Classic a 31 de março de 2021. Mais detalhes serão fornecidos aos clientes ZRS Classic antes da depreciação.
>
> Após a disponibilização do ZRS numa determinada região, os clientes deixarão de poder criar contas ZRS Classic a partir do portal Azure naquela região. Usar o Microsoft PowerShell e o Azure CLI para criar contas ZRS Classic é uma opção até que o ZRS Classic seja depreciado. Para obter informações sobre onde o ZRS está disponível, consulte o [despedimento do Azure Storage](storage-redundancy.md).

ZRS Classic replica sincronicamente dados em centros de dados dentro de uma a duas regiões. Os dados replicados podem não estar disponíveis a menos que a Microsoft inicie a falha no secundário. Uma conta ZRS Classic não pode ser convertida de ou de LRS, GRS ou RA-GRS. As contas ZRS Classic também não suportam métricas ou registos.

ZRS Classic está disponível apenas para **blocos** em contas de armazenamento V1 (GPv1) de uso geral. Para obter mais informações sobre contas de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](storage-account-overview.md)

Para migrar manualmente os dados da conta ZRS de ou para uma conta LRS, GRS, RA-GRS ou ZRS Classic, utilize uma das seguintes ferramentas: AzCopy, Azure Storage Explorer, PowerShell ou Azure CLI. Também pode construir a sua própria solução de migração com uma das bibliotecas de clientes do Azure Storage.

Também pode atualizar a sua conta de armazenamento ZRS Classic para ZRS utilizando o portal Azure, PowerShell ou Azure CLI em regiões onde o ZRS está disponível.

# <a name="portal"></a>[Portal](#tab/portal)

Para atualizar para ZRS no portal Azure, navegue para as definições de **Configuração** da conta e escolha **upgrade:**

![Upgrade ZRS Classic para ZRS no Portal](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para atualizar para ZRS utilizando powerShell, ligue para o seguinte comando:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para atualizar para ZRS utilizando o Azure CLI, ligue para o seguinte comando:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Custos associados à alteração da forma como os dados são replicados

Os custos associados à alteração da forma como os dados são replicados dependem da sua trajetória de conversão. Encomendas de pelo menos para as ofertas de despedimento seletiva seletiva seletiva supor LRS, ZRS, GRS, RA-GRS, GZRS e RA-GZRS.

Por exemplo, passar *de* LRS a qualquer outro tipo de replicação incorrerá em encargos adicionais porque você está movendo-se para um nível de redundância mais sofisticado. Migrar *para* GRS ou RA-GRS incorrerá numa carga de largura de banda de saída porque os seus dados (na região primária) estão a ser replicados para a sua região secundária remota. Esta taxa é um custo único na configuração inicial. Depois de copiados os dados, não há mais taxas de migração. Para mais detalhes sobre as tarifas de largura de banda, consulte a página de [Preços de Armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se migrar a sua conta de armazenamento de GRS para LRS, não há custoadicional, mas os seus dados replicados são eliminados da localização secundária.

> [!IMPORTANT]
> Se migrar a sua conta de armazenamento de RA-GRS para GRS ou LRS, essa conta é faturada como RA-GRS por mais 30 dias além da data em que foi convertida.

## <a name="see-also"></a>Consulte também

- [Redundância de armazenamento azure](storage-redundancy.md)
- [Verifique a propriedade Do Último Tempo sincronizado para obter uma conta de armazenamento](last-sync-time-get.md)
- [Conceber aplicações altamente disponíveis utilizando armazenamento geo-redundante de acesso de leitura](storage-designing-ha-apps-with-ragrs.md)
