---
title: Crie aplicativos de armazenamento do Azure altamente disponíveis com armazenamento com redundância de zona geográfica (GZRS) (visualização) | Microsoft Docs
description: O GZRS (armazenamento com redundância de zona geográfica) casasse a alta disponibilidade de armazenamento com redundância de zona (ZRS) com proteção contra interrupções regionais, conforme fornecido pelo armazenamento com redundância geográfica (GRS). Os dados em uma conta de armazenamento GZRS são replicados entre as zonas de disponibilidade do Azure na região primária e também são replicados para uma região geográfica secundária para proteção contra desastres regionais.
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: c102941920d2b8746a49be47ef4c5613fa0bc281
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719080"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Crie aplicativos de armazenamento do Azure altamente disponíveis com GZRS (armazenamento com redundância de zona geográfica) (visualização)

O armazenamento geo-zona-redundante (GZRS) (pré-visualização) casa com a elevada disponibilidade de [armazenamento redundante (ZRS)](storage-redundancy-zrs.md) com proteção contra interrupções regionais, conforme fornecido pelo [armazenamento geo-redundante (GRS)](storage-redundancy-grs.md). Os dados de uma conta de armazenamento GZRS são replicados em três zonas de [disponibilidade do Azure](../../availability-zones/az-overview.md) na região primária e também replicados para uma região geográfica secundária para proteção contra desastres regionais. Cada região do Azure é emparelhada com outra região na mesma geografia, juntos criando um par regional. Para mais detalhes e exceções consulte a [documentação](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Com uma conta de armazenamento GZRS, você pode continuar lendo e gravando dados se uma zona de disponibilidade ficar indisponível ou não puder ser recuperada. Além disso, seus dados também são duráveis no caso de uma interrupção regional completa ou um desastre no qual a região primária não seja recuperável. A GZRS foi concebida para fornecer pelo menos 99.9999999999999999999999999% (16 9's) durabilidade de objetos durante um determinado ano. O GZRS também oferece as mesmas metas de escalabilidade que LRS, ZRS, GRS ou RA-GRS. Opcionalmente, você pode habilitar o acesso de leitura aos dados na região secundária com armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS) se seus aplicativos precisarem ser capazes de ler dados no caso de um desastre na região primária.

A Microsoft recomenda o uso do GZRS para aplicativos que exigem consistência, durabilidade, alta disponibilidade, excelente desempenho e resiliência para a recuperação de desastres. Para obter a segurança adicional de acesso de leitura para a região secundária no caso de um desastre regional, habilite o RA-GZRS para sua conta de armazenamento.

## <a name="about-the-preview"></a>Sobre a versão prévia

Somente as contas de armazenamento de uso geral v2 dão suporte a GZRS e RA-GZRS. Para obter mais informações sobre os tipos de conta de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](storage-account-overview.md) GZRS e RA-GZRS dão suporte a blobs de blocos, blobs de páginas (que não são discos VHD), arquivos, tabelas e filas.

GZRS e RA-GZRS estão disponíveis atualmente para visualização nas seguintes regiões:

- Sudeste Asiático
- Norte da Europa
- Europa Ocidental
- Leste do Japão
- Reino Unido Sul
- E.U.A Leste
- E.U.A. Leste 2
- E.U.A. Central

A Microsoft continua a habilitar GZRS e RA-GZRS em regiões adicionais do Azure. Consulte regularmente a página de Atualizações de [Serviços Azure](https://azure.microsoft.com/updates/) para obter informações sobre regiões apoiadas.

Para obter informações sobre os preços de pré-visualização, consulte os preços de pré-visualização da GZRS para [Blobs,](https://azure.microsoft.com/pricing/details/storage/blobs) [Files,](https://azure.microsoft.com/pricing/details/storage/files/)Filas e [Tabelas](https://azure.microsoft.com/pricing/details/storage/queues/). [](https://azure.microsoft.com/pricing/details/storage/tables/)

> [!IMPORTANT]
> A Microsoft recomenda o uso de recursos de visualização para cargas de trabalho de produção.

## <a name="how-gzrs-and-ra-gzrs-work"></a>Como funciona o GZRS e o RA-GZRS

Quando os dados são gravados em uma conta de armazenamento com GZRS ou RA-GZRS habilitados, esses dados são replicados primeiro de forma síncrona na região primária em três zonas de disponibilidade. Os dados são então replicados de forma assíncrona para uma segunda região que está a centenas de quilômetros de distância. Quando os dados são escritos para a região secundária, é ainda replicado sincronizadamente três vezes naquela região usando [armazenamento localmente redundante (LRS)](storage-redundancy-lrs.md).

> [!IMPORTANT]
> A replicação assíncrona envolve um atraso entre a hora em que os dados são gravados na região primária e quando eles são replicados para a região secundária. No caso de um desastre regional, as alterações que ainda não foram replicadas para a região secundária poderão ser perdidas se esses dados não puderem ser recuperados da região primária.

Ao criar uma conta de armazenamento, você especifica como os dados dessa conta serão replicados e também especifica a região primária dessa conta. A região secundária emparelhada para uma conta replicada geograficamente é determinada com base na região primária e não pode ser alterada. Para obter informações atualizadas sobre regiões apoiadas pelo Azure, consulte a continuidade do negócio e a recuperação de [desastres (BCDR): Regiões emparelhadas com Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Para obter informações sobre a criação de uma conta de armazenamento utilizando GZRS ou RA-GZRS, consulte [Criar uma conta](storage-account-create.md)de armazenamento .

### <a name="use-ra-gzrs-for-high-availability"></a>Usar RA-GZRS para alta disponibilidade

Quando você habilita o RA-GZRS para sua conta de armazenamento, seus dados podem ser lidos do ponto de extremidade secundário, bem como do ponto de extremidade primário para sua conta de armazenamento. O ponto final secundário afixa o sufixo *– secundário* ao nome da conta. Por exemplo, se o seu principal ponto final para o serviço Blob for `myaccount.blob.core.windows.net`, então o seu ponto final secundário é `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primários e secundários.

Para aproveitar o RA-GZRS no caso de uma interrupção regional, você deve projetar seu aplicativo com antecedência para lidar com esse cenário. Seu aplicativo deve ler e gravar no ponto de extremidade primário, mas alternar para o uso do ponto de extremidade secundário no caso de a região primária ficar indisponível. Para obter orientações sobre a conceção de alta disponibilidade com RA-GZRS, consulte A Conceção de [Aplicações Altamente Disponíveis utilizando RA-GZRS ou RA-GRS](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Como os dados são replicados para a região secundária de forma assíncrona, a região secundária geralmente está atrás da região primária. Para determinar quais operações de gravação foram replicadas para a região secundária, seu aplicativo verifica a hora da última sincronização para sua conta de armazenamento. Todas as operações de gravação gravadas na região primária antes da hora da última sincronização foram replicadas com êxito para a região secundária, o que significa que elas estão disponíveis para serem lidas a partir do secundário. Qualquer operação de gravação gravada na região primária após a hora da última sincronização pode ou não ter sido replicada para a região secundária, o que significa que elas podem não estar disponíveis para operações de leitura.

Pode consultar o valor da propriedade **Last Sync Time** utilizando o Azure PowerShell, o Azure CLI ou uma das bibliotecas de clientes do Azure Storage. A propriedade **Last Sync Time** é um valor GMT data/hora.

Para obter orientações adicionais sobre desempenho e escalabilidade com RA-GZRS, consulte o desempenho do [Armazenamento Microsoft Azure e](storage-performance-checklist.md)a lista de verificação de escalabilidade .

### <a name="availability-zone-outages"></a>Interrupções da zona de disponibilidade

No caso de uma falha que afete uma zona de disponibilidade na região primária, os aplicativos podem continuar a ler e gravar diretamente em sua conta de armazenamento usando as outras zonas de disponibilidade para essa região. A Microsoft recomenda que você continue seguindo as práticas de tratamento de falhas transitórias ao usar GZRS ou ZRS. Essas práticas incluem a implementação de políticas de repetição com retirada exponencial.

Quando uma zona de disponibilidade torna-se indisponível, o Azure utiliza atualizações de rede, como o DNS. Essas atualizações podem afetar seu aplicativo se você estiver acessando dados antes que as atualizações sejam concluídas.

### <a name="regional-outages"></a>Interrupções regionais

Se uma falha afetar toda a região primária, a Microsoft tentará primeiro restaurar a região primária. Se a restauração não for possível, a Microsoft fará failover para a região secundária, para que a região secundária se torne a nova região primária. Se a conta de armazenamento tiver o RA-GZRS habilitado, os aplicativos criados para esse cenário poderão ler a partir da região secundária enquanto aguardam o failover. Se a conta de armazenamento não tiver o RA-GZRS habilitado, os aplicativos não poderão ler a partir do secundário até que o failover seja concluído.

> [!NOTE]
> GZRS e RA-GZRS estão atualmente em visualização apenas na região leste dos EUA. O failover de conta gerenciada pelo cliente (versão prévia) ainda não está disponível no leste dos EUA 2, de modo que os clientes não podem atualmente gerenciar eventos de failover de conta com contas GZRS e RA-GZRS. Durante a versão prévia, a Microsoft gerenciará todos os eventos de failover que afetam as contas GZRS e RA-GZRS.

Como os dados são replicados para a região secundária de forma assíncrona, uma falha que afeta a região primária poderá resultar em perda de dados se a região primária não puder ser recuperada. O intervalo entre as gravações mais recentes na região primária e a última gravação na região secundária é conhecido como o RPO (objetivo de ponto de recuperação). O RPO indica o ponto no tempo em que os dados podem ser recuperados. O armazenamento do Azure normalmente tem um RPO de menos de 15 minutos, embora atualmente não haja SLA quanto ao tempo necessário para replicar dados para a região secundária.

O RTO (objetivo de tempo de recuperação) é uma medida de quanto tempo leva para executar o failover e colocar a conta de armazenamento online novamente. Essa medida indica o tempo exigido pelo Azure para executar o failover, alterando as entradas DNS primárias para apontar para o local secundário.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>Migrar uma conta de armazenamento para GZRS ou RA-GZRS

Você pode migrar qualquer conta de armazenamento existente para GZRS ou RA-GZRS. A migração de uma conta existente do ZRS para o GZRS ou o RA-GZRS é simples, ao mesmo tempo em que a migração de uma conta LRS, GRS ou RA-GRS é mais envolvida. As seções a seguir descrevem como migrar em qualquer caso.

**Limitações conhecidas**

- A camada de arquivo morto não tem suporte no momento em contas (RA-) GZRS. Consulte [o armazenamento Azure Blob: hot, cool e archive access tiers](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para mais detalhes.
- Os discos gerenciados não dão suporte a (RA-) GZRS. Pode armazenar instantâneos e imagens para discos geridos standard SSD no armazenamento Standard HDD e [escolher entre opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="migrating-from-a-zrs-account"></a>Migrando de uma conta do ZRS

Para converter uma conta ZRS existente em RA-GZRS, utilize o cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para alterar o SKU para a conta. Lembre-se de substituir os valores de espaço reservado pelos seus próprios valores:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>Migrando de uma conta LRS, GRS ou RA-GRS

Há duas opções para migrar para GZRS ou RA-GZRS de uma conta LRS, GRS ou RA-GRS:

- Você pode copiar ou mover dados manualmente para uma nova conta GZRS ou RA-GZRS de uma conta existente.
- Você pode solicitar uma migração ao vivo.

#### <a name="perform-a-manual-migration"></a>Executar uma migração manual

Se você precisar que a migração seja concluída em uma determinada data, considere executar uma migração manual. A migração manual proporciona mais flexibilidade do que a migração em direto. Com a migração manual, poderá controlar o momento da migração.

Para migrar manualmente os dados de uma conta existente para uma conta GZRS ou RA-GZRS, use uma ferramenta que possa copiar dados com eficiência. Alguns exemplos incluem:

- Use um utilitário como o AzCopy ou uma ferramenta de terceiros confiável. Para obter informações sobre a AzCopy, consulte Iniciar com [AzCopy](storage-use-azcopy-v10.md).
- Se você estiver familiarizado com o Hadoop ou o HDInsight, anexe as contas de armazenamento de origem e de destino ao cluster. Em seguida, paralelizar o processo de cópia de dados com uma ferramenta como DistCp.
- Crie suas próprias ferramentas usando uma das bibliotecas de cliente do armazenamento do Azure.

#### <a name="perform-a-live-migration"></a>Executar uma migração ao vivo

Uma migração manual pode resultar em tempo de inatividade do aplicativo. Se a aplicação exigir elevada disponibilidade, a Microsoft fornece a opção de migração em direto. A migração em direto é uma migração no local sem tempo de inatividade.

Durante uma migração ao vivo, você pode usar sua conta de armazenamento enquanto os dados são migrados entre as contas de armazenamento de origem e de destino. Durante o processo de migração ao vivo, sua conta continua a atender seu SLA quanto à durabilidade e disponibilidade. Não há nenhum tempo de inatividade ou perda de dados causado pela migração ao vivo.

Somente as contas de uso geral v2 dão suporte a GZRS/RA-GZRS, portanto, antes de enviar uma solicitação de migração ao vivo para GZRS/RA-GZRS, você deve atualizar sua conta para a finalidade geral v2. Para mais informações, consulte a visão geral da conta de [armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) e [atualize para uma conta de armazenamento v2 de uso geral](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

Uma vez concluída a migração, a definição de replicação da conta de armazenamento será atualizada para **armazenamento redundante geo-zona (GZRS)** ou **armazenamento geo-zona-redundante de acesso de leitura (RA-GZRS)** . Pontos de extremidade de serviço, chaves de acesso, SAS (assinaturas de acesso compartilhado) e quaisquer outras opções de configuração de conta permanecem inalterados e intactos.

Tenha em mente as seguintes restrições na migração ao vivo:

- Embora a Microsoft processe com o seu pedido para migração em direto imediatamente, não há nenhuma garantia em relação a quando uma migração em direto será concluída. Se você precisar que seus dados sejam migrados para GZRS ou RA-GZRS em uma determinada data, a Microsoft recomenda que você execute uma migração manual. Em geral, quanto mais dados tiver na conta, mais tempo levará a migrar esses dados.
- A migração ao vivo é suportada apenas para contas de armazenamento que utilizam a replicação GRS ou RA-GRS. Se a sua conta utilizar LRS, então tem de alterar primeiro o tipo de replicação da sua conta para GRS ou RA-GRS antes de prosseguir. Este passo intermediário adiciona o ponto final secundário fornecido por GRS/RA-GRS.
- Sua conta deve conter dados.
- Você só pode migrar dados dentro da mesma região.
- Somente os tipos de conta de armazenamento Standard dão suporte à migração dinâmica. As contas de armazenamento Premium devem ser migradas manualmente.
- Não há suporte para a migração dinâmica de uma conta GZRS ou RA-GZRS para uma conta LRS, GRS ou RA-GRS. Terá de mover os dados manualmente para uma conta de armazenamento nova ou existente.
- Você pode solicitar uma migração dinâmica de RA-GRS para RA-GZRS. No entanto, não há suporte para a migração de RA-GRS para GZRS. Nesse caso, você deve solicitar uma migração ao vivo para RA-GZRS e, em seguida, converter manualmente a conta de armazenamento para usar o GZRS.
- O Managed disks dá suporte apenas a LRS e não pode ser migrado para GZRS ou RA-GZRS. Para integração com conjuntos de disponibilidade, consulte [Introdução aos discos geridos pelo Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)
- Pode armazenar instantâneos e imagens para discos geridos standard SSD no armazenamento Standard HDD e [escolher entre opções LRS, ZRS, GZRS e RA-GZRS](https://azure.microsoft.com/pricing/details/managed-disks/).
- As contas que contêm grandes compartilhamentos de arquivos não têm suporte para GZRS.

Para solicitar uma migração ao vivo, utilize o [portal Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) No portal, selecione a conta de armazenamento para migrar para GZRS ou RA-GZRS e siga estas instruções:

1. Selecione **novo pedido de suporte**.
2. Complete o **Básico** com base nas informações da sua conta. Na secção **serviço,** selecione **Gestão de Conta de Armazenamento** e especifique a conta a migrar.
3. Selecione **Seguinte**.
4. Especificar os seguintes valores na secção **Problema:**
    - **Gravidade**: Deixe o valor padrão como está.
    - **Tipo de problema:** Selecione **migração de dados**.
    - **Categoria**: **Selecione Migrar para (RA-)GZRS dentro de uma região**.
    - **Denominação**: Digite um título descritivo, por exemplo, **(migração da conta RA-)GZRS**.
    - **Detalhes**: Digite detalhes adicionais na caixa **de Detalhes,** por exemplo, "Gostaria de migrar para gZRS de [LRS, GRS] na região \_\_." ou "Gostaria de migrar para RA-GZRS de [LRS, RA-GRS] na \_\_ região."
5. Selecione **Seguinte**.
6. Verifique se as informações de contacto estão corretas na lâmina de informação do **contacto.**
7. Selecione **Criar**.

Um representante de suporte entrará em contato com você para fornecer assistência.

## <a name="see-also"></a>Consulte também

- [Replicação do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [Armazenamento localmente redundante (LRS): redundância de dados de baixo custo para armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [Armazenamento redundante de zona (ZRS): Aplicações de armazenamento azure altamente disponíveis](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
- [Metas de escalabilidade e desempenho para contas de armazenamento padrão](scalability-targets-standard-account.md)
