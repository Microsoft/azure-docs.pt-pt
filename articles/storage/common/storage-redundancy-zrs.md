---
title: Criar aplicações de armazenamento do Azure de elevada disponibilidade no armazenamento com redundância de zona (ZRS) | Documentos da Microsoft
description: Armazenamento com redundância de zona (ZRS) oferece uma forma simples de criar aplicações com elevada disponibilidade. O ZRS protege contra falhas de hardware no Centro de dados e contra alguns desastres regionais.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.subservice: common
ms.openlocfilehash: 8928e59b97143038e0850132196f1ce9a1da131d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337889"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Armazenamento com redundância de zona (ZRS): Aplicações de armazenamento do Azure de elevada disponibilidade
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Cobertura de suporte e a disponibilidade regional
Atualmente, o ZRS suporta tipos de conta padrão para fins gerais v2. Para obter mais informações sobre os tipos de conta de armazenamento, consulte [descrição geral da conta de armazenamento do Azure](storage-account-overview.md).

O ZRS está disponível para blobs de blocos, blobs de páginas sem disco, ficheiros, tabelas e filas.

O ZRS está disponível em geral nas seguintes regiões:

- Sudeste Asiático
- Europa Ocidental
- Norte da Europa
- França Central
- Leste do Japão
- Reino Unido Sul
- E.U.A Leste
- Este dos EUA 2
- E.U.A. Oeste 2
- Centro dos EUA

A Microsoft continua a ativar o ZRS em mais regiões do Azure. Verifique os [atualizações de serviço do Azure](https://azure.microsoft.com/updates/) página regularmente para obter informações sobre novas regiões.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>O que acontece quando uma zona não estiver disponível?
Os dados estão continua acessíveis para leitura e escrita operações mesmo quando a zona fica indisponível. A Microsoft recomenda que continue a seguir práticas recomendadas para processamento de falhas transitórias. Essas práticas incluem a implementação de políticas de repetição com término exponencial.

Quando uma zona não estiver disponível, o Azure realiza a atualizações de sistema de rede, como repointing de DNS. Estas atualizações podem afetar seu aplicativo se estão a aceder aos seus dados antes de concluíram as atualizações.

O ZRS não pode proteger os seus dados contra um desastre regional em que várias zonas de forma permanente afetadas. Em vez disso, o ZRS oferece resiliência para os seus dados se torna-se temporariamente indisponível. Para proteção contra desastres regionais, a Microsoft recomenda a utilização de armazenamento georredundante (GRS). Para obter mais informações sobre o GRS, consulte [armazenamento georredundante (GRS): A replicação do armazenamento do Azure entre regiões](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Converter para replicação ZRS
Migrar para ou do LRS, GRS e RA-GRS é simples. Utilize o portal do Azure ou a API de fornecedor de recursos de armazenamento para alterar o tipo de redundância da sua conta. Azure irá, em seguida, replicar os seus dados em conformidade. 

Migrar dados de ou para ZRS requerem uma estratégia diferente. A migração de ZRS envolve o movimento físico dos dados de um carimbo de armazenamento única para vários carimbos de data / dentro de uma região.

Existem duas opções principais para a migração para ou a partir de ZRS: 

- Manualmente, copiar ou mover dados para uma conta ZRS nova de uma conta existente.
- Pedir uma migração em direto.

A Microsoft recomenda vivamente que efetue uma migração manual. Uma migração manual fornece mais flexibilidade do que uma migração em direto. Com uma migração manual, está no controle do período de tempo.

Para efetuar uma migração manual, terá opções:
- Utilize as ferramentas existentes, como o AzCopy, uma das bibliotecas de cliente de armazenamento do Azure ou ferramentas de terceiros confiáveis.
- Se estiver familiarizado com o Hadoop ou o HDInsight, anexar conta de origem e de destino (ZRS) ao seu cluster. Em seguida, executar em paralelo o processo de cópia de dados com uma ferramenta como o DistCp.
- Crie suas próprias ferramentas através de uma das bibliotecas de cliente do armazenamento do Azure.

Uma migração manual pode resultar num período de indisponibilidade de aplicação. Se a sua aplicação necessitar de elevada disponibilidade, a Microsoft também fornece uma opção de migração em direto. Uma migração em direto é uma migração no local. 

Durante uma migração em direto, pode usar a sua conta de armazenamento, enquanto os dados são migrados entre carimbos de armazenamento de origem e de destino. Durante o processo de migração, tem o mesmo nível de durabilidade e disponibilidade SLA à medida que normalmente realizar.

Tenha em atenção as seguintes restrições sobre a migração em direto:

- Embora a Microsoft lida com o seu pedido para migração em direto imediatamente, não há nenhuma garantia em relação a quando uma migração em direto será concluída. Se precisar dos dados migrados para o ZRS por uma determinada data, em seguida, a Microsoft recomenda que efetue uma migração manual. Em geral, quanto mais dados tiver na sua conta, quanto mais tempo que demora a migrar esses dados. 
- Migração em direto é suportada apenas para contas de armazenamento que utilizam a replicação LRS ou GRS. Se a sua conta utilizar RA-GRS, terá de alterar primeiro o tipo de replicação da sua conta LRS ou GRS antes de continuar. Este passo intermediário remove só de leitura ponto final secundário fornecido pelo RA-GRS antes da migração.
- Sua conta tem de conter dados.
- Só é possível migrar os dados na mesma região. Se pretender migrar os dados para uma conta ZRS localizada numa região diferente da conta de origem, tem de efetuar uma migração manual.
- Tipos de conta de armazenamento standard só suportam a migração em direto. Contas de armazenamento Premium têm de ser migradas manualmente.

Pode pedir uma migração em direto através da [portal de suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). No portal, selecione a conta de armazenamento que pretende converter em ZRS.
1. Selecione **novo pedido de suporte**
2. Concluir o **Noções básicas** com base nas informações da sua conta. Na **serviço** secção, selecione **gestão de contas de armazenamento** e o recurso que pretende converter em ZRS. 
3. Selecione **Seguinte**. 
4. Especifique os seguintes valores da **problema** secção: 
    - **Gravidade**: Deixe o valor predefinido como-é.
    - **Tipo de problema**: Selecione **migração de dados**.
    - **Categoria**: Selecione **migre para o ZRS dentro de uma região**.
    - **Título**: Escreva um título descritivo, por exemplo, **migração de contas ZRS**.
    - **Detalhes**: Escreva detalhes adicionais na **detalhes** caixa, por exemplo, eu gostaria de migrar para o ZRS do [LRS, GRS] no \_ \_ região. 
5. Selecione **Seguinte**.
6. Certifique-se de que as informações de contacto estão corretas no **informações de contacto** painel.
7. Selecione **Criar**.

Um técnico de suporte irá contactá-lo e fornecer qualquer assistência que é necessário.

## <a name="live-migration-to-zrs-faq"></a>Migração em direto ZRS FAQ

**Devo planear qualquer período de indisponibilidade durante a migração?**

Não há nenhum tempo de inatividade causado pela migração. Durante uma migração em direto, é possível continuar a sua conta de armazenamento, enquanto os dados são migrados entre carimbos de armazenamento de origem e de destino. Durante o processo de migração, tem o mesmo nível de durabilidade e disponibilidade SLA à medida que normalmente realizar.

**Existe qualquer perda de dados associada com a migração?**

Não existe nenhuma perda de dados associada com a migração. Durante o processo de migração, tem o mesmo nível de durabilidade e disponibilidade SLA à medida que normalmente realizar.

**Depois da migração estiver concluída, são todas as atualizações necessárias para as aplicações?**

Depois de concluída a migração do tipo de replicação da conta (s) será alterado para "armazenamento com redundância de zona (ZRS)". Pontos finais de serviço, aceder a chaves, SAS e quaisquer outras opções de configuração de conta permanecem inalteradas e intactos.

**Pode pedir uma migração em direto da minha conta de fins gerais v1 (s) para ZRS?**

ZRS suporta apenas contas para fins gerais v2, portanto, antes de submeter um pedido para uma migração em direto para ZRS Certifique-se atualizar a sua conta (s) para fins gerais v2. Ver [descrição geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) e [atualizar para uma conta de armazenamento para fins gerais v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para obter mais detalhes.

**Pode pedir uma migração em direto da minha conta de armazenamento georredundante de acesso de leitura (RA-GRS) (s) para ZRS?**

Antes de submeter um pedido para uma migração em direto para ZRS, certifique-se de suas aplicações ou workload(s) já não necessitam de acesso para o ponto final secundário de só de leitura e altere o tipo de replicação de suas contas de armazenamento para armazenamento georredundante (GRS). Ver [alterando a estratégia de replicação](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) para obter mais detalhes.

**Pode pedir uma migração em direto da minha conta de armazenamento para ZRS para outra região?**

Se pretender migrar os dados para uma conta ZRS localizada numa região diferente da região da conta de origem, tem de efetuar uma migração manual.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>O ZRS clássico: Uma opção de legado para redundância de blobs de bloco
> [!NOTE]
> A Microsoft irá preterir e migrar as contas ZRS Classic no dia 31 de Março de 2021. Obter mais detalhes serão fornecidos aos clientes ZRS Classic antes de preterição. 
>
> Assim que for ZRS [em disponibilidade geral](#support-coverage-and-regional-availability) numa região, os clientes não será possível criar as contas ZRS clássico a partir do portal nessa região. Usando o Microsoft PowerShell e CLI do Azure para criar as contas ZRS Classic é uma opção até que o ZRS Classic foi preterido.

ZRS Classic assincronamente replica os dados em centros de dados dentro de uma ou duas regiões. Os dados replicados podem não estar disponíveis, a menos que a Microsoft inicie a ativação pós-falha para o secundário. Uma conta ZRS Classic não pode ser convertida para ou do LRS, GRS ou RA-GRS. As contas ZRS Classic também não suportam métricas ou de registo.

ZRS Classic só está disponível para **blobs de blocos** para fins gerais V1 (GPv1) contas de armazenamento. Para obter mais informações sobre as contas de armazenamento, veja [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

Para migrar manualmente os dados da conta ZRS para ou a partir de uma conta LRS, ZRS Classic, GRS ou RA-GRS, utilize uma das seguintes ferramentas: AzCopy, o Explorador de armazenamento do Azure, o Azure PowerShell ou o Azure CLI. Também pode criar sua própria solução de migração com uma das bibliotecas de cliente do armazenamento do Azure.

Também pode atualizar sua conta ZRS Classic (s) para o ZRS no Portal ou utilizando o Azure PowerShell ou na CLI do Azure.

## <a name="see-also"></a>Consulte também
- [Replicação do Armazenamento do Azure](storage-redundancy.md)
- [Armazenamento localmente redundante (LRS): Redundância de dados de baixo custo do armazenamento do Azure](storage-redundancy-lrs.md)
- [Armazenamento georredundante (GRS): Replicação de inter-regional do armazenamento do Azure](storage-redundancy-grs.md)
