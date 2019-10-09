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
ms.openlocfilehash: 99def93a20a365dd0ff5fc27e9c52909ee30bd83
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028131"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Crie aplicativos de armazenamento do Azure altamente disponíveis com GZRS (armazenamento com redundância de zona geográfica) (visualização)

Armazenamento com redundância de zona geográfica (GZRS) (visualização) casasse a alta disponibilidade de [armazenamento com redundância de zona (ZRS)](storage-redundancy-zrs.md) com proteção contra interrupções regionais, conforme fornecido pelo [armazenamento com REDUNDÂNCIA geográfica (GRS)](storage-redundancy-grs.md). Os dados em uma conta de armazenamento GZRS são replicados em três [zonas de disponibilidade do Azure](../../availability-zones/az-overview.md) na região primária e também são replicados para uma região geográfica secundária para proteção contra desastres regionais. Cada região do Azure é emparelhada com outra região na mesma geografia, juntos criando um par regional. Para obter mais detalhes e exceções, consulte a [documentação](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Com uma conta de armazenamento GZRS, você pode continuar lendo e gravando dados se uma zona de disponibilidade ficar indisponível ou não puder ser recuperada. Além disso, seus dados também são duráveis no caso de uma interrupção regional completa ou um desastre no qual a região primária não seja recuperável. O GZRS foi projetado para fornecer pelo menos a durabilidade de objetos de 99.99999999999999% (16 9) em um determinado ano. O GZRS também oferece as mesmas [metas de escalabilidade](storage-scalability-targets.md) que lRS, ZRS, grs ou ra-grs. Opcionalmente, você pode habilitar o acesso de leitura aos dados na região secundária com armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS) se seus aplicativos precisarem ser capazes de ler dados no caso de um desastre na região primária.

A Microsoft recomenda o uso do GZRS para aplicativos que exigem consistência, durabilidade, alta disponibilidade, excelente desempenho e resiliência para a recuperação de desastres. Para obter a segurança adicional de acesso de leitura para a região secundária no caso de um desastre regional, habilite o RA-GZRS para sua conta de armazenamento.

## <a name="about-the-preview"></a>Sobre a versão prévia

Somente as contas de armazenamento de uso geral v2 dão suporte a GZRS e RA-GZRS. Para obter mais informações sobre tipos de conta de armazenamento, consulte [visão geral da conta de armazenamento do Azure](storage-account-overview.md). GZRS e RA-GZRS dão suporte a blobs de blocos, blobs de páginas que não são discos VHD, arquivos, tabelas e filas.

GZRS e RA-GZRS estão disponíveis atualmente para visualização nas seguintes regiões:

- Norte da Europa
- Europa Ocidental
- E.U.A Leste
- Este dos EUA 2
- Centro dos EUA

A Microsoft continua a habilitar GZRS e RA-GZRS em regiões adicionais do Azure. Verifique as [atualizações de serviço do Azure](https://azure.microsoft.com/updates/) Page regularmente para obter informações sobre as regiões com suporte.

Para obter informações sobre preços de visualização, consulte preços de visualização do GZRS para [BLOBs](https://azure.microsoft.com/pricing/details/storage/blobs), [arquivos](https://azure.microsoft.com/pricing/details/storage/files/), [filas](https://azure.microsoft.com/pricing/details/storage/queues/)e [tabelas](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> A Microsoft recomenda o uso de recursos de visualização para cargas de trabalho de produção.

## <a name="how-gzrs-and-ra-gzrs-work"></a>Como funciona o GZRS e o RA-GZRS

Quando os dados são gravados em uma conta de armazenamento com GZRS ou RA-GZRS habilitados, esses dados são replicados primeiro de forma síncrona na região primária em três zonas de disponibilidade. Os dados são então replicados de forma assíncrona para uma segunda região que está a centenas de quilômetros de distância. Quando os dados são gravados na região secundária, eles são replicados de forma síncrona em três vezes dentro dessa região usando o [LRS (armazenamento com redundância local)](storage-redundancy-lrs.md).

> [!IMPORTANT]
> A replicação assíncrona envolve um atraso entre a hora em que os dados são gravados na região primária e quando eles são replicados para a região secundária. No caso de um desastre regional, as alterações que ainda não foram replicadas para a região secundária poderão ser perdidas se esses dados não puderem ser recuperados da região primária.

Ao criar uma conta de armazenamento, você especifica como os dados dessa conta serão replicados e também especifica a região primária dessa conta. A região secundária emparelhada para uma conta replicada geograficamente é determinada com base na região primária e não pode ser alterada. Para obter informações atualizadas sobre regiões com suporte do Azure, consulte @ no__t-0Business Continuity and Disaster Recovery (BCDR): Regiões emparelhadas do Azure @ no__t-0. Para obter informações sobre como criar uma conta de armazenamento usando GZRS ou RA-GZRS, consulte [criar uma conta de armazenamento](storage-quickstart-create-account.md).

### <a name="use-ra-gzrs-for-high-availability"></a>Usar RA-GZRS para alta disponibilidade

Quando você habilita o RA-GZRS para sua conta de armazenamento, seus dados podem ser lidos do ponto de extremidade secundário, bem como do ponto de extremidade primário para sua conta de armazenamento. O ponto de extremidade secundário acrescenta o sufixo *– secundário* ao nome da conta. Por exemplo, se o ponto de extremidade primário para o serviço blob for @ no__t-0, o ponto de extremidade secundário será @ no__t-1. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primários e secundários.

Para aproveitar o RA-GZRS no caso de uma interrupção regional, você deve projetar seu aplicativo com antecedência para lidar com esse cenário. Seu aplicativo deve ler e gravar no ponto de extremidade primário, mas alternar para o uso do ponto de extremidade secundário no caso de a região primária ficar indisponível. Para obter orientação sobre como projetar para alta disponibilidade com o RA-GZRS, consulte [criando aplicativos altamente disponíveis usando ra-GZRS ou ra-grs](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Como os dados são replicados para a região secundária de forma assíncrona, a região secundária geralmente está atrás da região primária. Para determinar quais operações de gravação foram replicadas para a região secundária, seu aplicativo verifica a hora da última sincronização para sua conta de armazenamento. Todas as operações de gravação gravadas na região primária antes da hora da última sincronização foram replicadas com êxito para a região secundária, o que significa que elas estão disponíveis para serem lidas a partir do secundário. Qualquer operação de gravação gravada na região primária após a hora da última sincronização pode ou não ter sido replicada para a região secundária, o que significa que elas podem não estar disponíveis para operações de leitura.

Você pode consultar o valor da última propriedade de **hora de sincronização** usando Azure PowerShell, CLI do Azure ou uma das bibliotecas de cliente de armazenamento do Azure. A propriedade **hora da última sincronização** é um valor de data/hora GMT.

Para obter diretrizes adicionais sobre o desempenho e a escalabilidade com o RA-GZRS, consulte a [lista de verificação de desempenho e escalabilidade do armazenamento do Microsoft Azure](storage-performance-checklist.md).

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

### <a name="migrating-from-a-zrs-account"></a>Migrando de uma conta do ZRS

Para converter uma conta ZRS existente em RA-GZRS, use o cmdlet [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para alterar a SKU da conta. Lembre-se de substituir os valores de espaço reservado pelos seus próprios valores:

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

- Use um utilitário como o AzCopy ou uma ferramenta de terceiros confiável. Para obter informações sobre AzCopy, consulte Introdução [ao AzCopy](storage-use-azcopy-v10.md).
- Se você estiver familiarizado com o Hadoop ou o HDInsight, anexe as contas de armazenamento de origem e de destino ao cluster. Em seguida, paralelizar o processo de cópia de dados com uma ferramenta como DistCp.
- Crie suas próprias ferramentas usando uma das bibliotecas de cliente do armazenamento do Azure.

#### <a name="perform-a-live-migration"></a>Executar uma migração ao vivo

Uma migração manual pode resultar em tempo de inatividade do aplicativo. Se a aplicação exigir elevada disponibilidade, a Microsoft fornece a opção de migração em direto. A migração em direto é uma migração no local sem tempo de inatividade.

Durante uma migração ao vivo, você pode usar sua conta de armazenamento enquanto os dados são migrados entre as contas de armazenamento de origem e de destino. Durante o processo de migração ao vivo, sua conta continua a atender seu SLA quanto à durabilidade e disponibilidade. Não há nenhum tempo de inatividade ou perda de dados causado pela migração ao vivo.

Somente as contas de uso geral v2 dão suporte a GZRS/RA-GZRS, portanto, antes de enviar uma solicitação de migração ao vivo para GZRS/RA-GZRS, você deve atualizar sua conta para a finalidade geral v2. Para obter mais informações, consulte [visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) and [atualizar para uma conta de armazenamento v2 de uso geral](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

Depois que a migração for concluída, a configuração de replicação da conta de armazenamento será atualizada para **armazenamento com redundância de zona geográfica (GZRS)** ou **armazenamento com redundância de acesso de leitura (ra-GZRS)** . Pontos de extremidade de serviço, chaves de acesso, SAS (assinaturas de acesso compartilhado) e quaisquer outras opções de configuração de conta permanecem inalterados e intactos.

Tenha em mente as seguintes restrições na migração ao vivo:

- Embora a Microsoft processe com o seu pedido para migração em direto imediatamente, não há nenhuma garantia em relação a quando uma migração em direto será concluída. Se você precisar que seus dados sejam migrados para GZRS ou RA-GZRS em uma determinada data, a Microsoft recomenda que você execute uma migração manual. Em geral, quanto mais dados tiver na conta, mais tempo levará a migrar esses dados.
- Sua conta deve conter dados.
- Você só pode migrar dados dentro da mesma região.
- Somente os tipos de conta de armazenamento Standard dão suporte à migração dinâmica. As contas de armazenamento Premium devem ser migradas manualmente.
- Não há suporte para a migração dinâmica de uma conta GZRS ou RA-GZRS para uma conta LRS, GRS ou RA-GRS. Terá de mover os dados manualmente para uma conta de armazenamento nova ou existente.
- Você pode solicitar uma migração dinâmica de RA-GRS para RA-GZRS. No entanto, não há suporte para a migração de RA-GRS para GZRS. Nesse caso, você deve solicitar uma migração ao vivo para RA-GZRS e, em seguida, converter manualmente a conta de armazenamento para usar o GZRS.
- O Managed disks dá suporte apenas a LRS e não pode ser migrado para GZRS ou RA-GZRS. Para integração com conjuntos de disponibilidade, consulte [introdução aos Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Você pode armazenar instantâneos e imagens para SSD Standard Managed Disks no armazenamento HDD Standard e [escolher entre as opções lRS, ZRS, GZRS e ra-GZRS](https://azure.microsoft.com/pricing/details/managed-disks/).
- As contas que contêm grandes compartilhamentos de arquivos não têm suporte para GZRS.

Para solicitar uma migração ao vivo, use o [portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). No portal, selecione a conta de armazenamento para migrar para GZRS ou RA-GZRS e siga estas instruções:

1. Selecione **nova solicitação de suporte**.
2. Conclua os **conceitos básicos** com base nas informações da sua conta. Na seção **serviço** , selecione **Gerenciamento de conta de armazenamento** e especifique a conta a ser migrada.
3. Selecione **Seguinte**.
4. Especifique os valores a seguir na seção **problema** :
    - **Gravidade**: Deixe o valor padrão como está.
    - **Tipo de problema**: Selecione **migração de dados**.
    - **Categoria**: Selecione **migrar para (ra-) GZRS em uma região**.
    - **Título**: Digite um título descritivo, por exemplo, **(ra-) GZRS de migração de conta**.
    - **Detalhes**: Digite detalhes adicionais na caixa de **detalhes** , por exemplo, "Eu gostaria de migrar para GZRS de [lRS, grs] na região \_ @ no__t-2". ou "Eu gostaria de migrar para o RA-GZRS de [LRS, RA-GRS] na região \_ @ no__t-1".
5. Selecione **Seguinte**.
6. Verifique se as informações de contato estão corretas na folha **informações de contato** .
7. Selecione **Criar**.

Um representante de suporte entrará em contato com você para fornecer assistência.

## <a name="see-also"></a>Consulte também

- [Replicação do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- 0Locally-armazenamento com redundância de @no__t (LRS): Redundância de dados de baixo custo para o armazenamento do Azure @ no__t-0
- 0Zone-ZRS (armazenamento com redundância de @no__t): Aplicativos de armazenamento do Azure altamente disponíveis @ no__t-0 
