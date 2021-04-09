---
title: Snapshots blob
titleSuffix: Azure Storage
description: Entenda como as fotos blob funcionam e como são cobradas.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: debce0a1b4c09bb89cdceb1cd29e59e1976c939a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539162"
---
# <a name="blob-snapshots"></a>Snapshots blob

Uma foto é uma versão só de leitura de uma bolha que é tirada num ponto do tempo.

> [!NOTE]
> A versão blob oferece uma forma superior de manter versões anteriores de uma bolha. Para obter mais informações, consulte [a versão Blob](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Sobre fotos blob

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Uma imagem instantânea de uma bolha é idêntica à sua bolha de base, exceto que o blob URI tem um valor **DateTime** anexado ao URI blob para indicar o momento em que o instantâneo foi tirado. Por exemplo, se uma página blob URI `http://storagesample.core.blob.windows.net/mydrives/myvhd` for, o snapshot URI é semelhante a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Todas as fotos partilham o URI da bolha da base. A única distinção entre a bolha de base e o instantâneo é o valor **de DateTime** anexado.

Uma bolha pode ter várias fotos. As imagens persistem até serem explicitamente eliminadas, independentemente ou como parte de uma operação [Delete Blob](/rest/api/storageservices/delete-blob) para a bolha de base. Pode enumerar os instantâneos associados à bolha de base para rastrear as suas imagens atuais.

Quando se cria uma imagem instantânea de uma bolha, as propriedades do sistema da bolha são copiadas para o instantâneo com os mesmos valores. Os metadados da bolha de base também são copiados para o instantâneo, a menos que especifique metadados separados para a imagem quando o criar. Depois de criar uma imagem, pode ler, copiar ou apagar, mas não pode modificá-la.

Quaisquer locações associadas à bolha de base não afetam o instantâneo. Não pode adquirir um contrato de arrendamento com uma foto instantânea.

Um ficheiro VHD é utilizado para armazenar as informações e o estado atuais de um disco VM. Pode retirar um disco de dentro do VM ou desligar o VM e, em seguida, tirar uma fotografia do seu ficheiro VHD. Pode utilizar o ficheiro instantâneo mais tarde para recuperar o ficheiro VHD nessa altura e recriar o VM.

## <a name="understand-how-snapshots-accrue-charges"></a>Entenda como os instantâneos acumulam encargos

### <a name="important-billing-considerations"></a>Considerações importantes de faturação

A lista a seguir inclui pontos-chave a ter em conta ao criar um instantâneo.

- A sua conta de armazenamento incorre em encargos para blocos ou páginas únicos, quer estejam na bolha ou na fotografia. A sua conta não incorre em encargos adicionais para instantâneos associados a uma bolha até atualizar a bolha em que estão baseadas. Depois de atualizar a bolha de base, diverge das suas fotos. Quando isto acontece, é cobrado pelos blocos ou páginas únicos em cada bolha ou instantâneo.
- Quando se substitui um bloco dentro de uma bolha de bloco, esse bloco é posteriormente carregado como um bloco único. Isto é verdade mesmo que o bloco tenha o mesmo ID do bloco e os mesmos dados que tem no instantâneo. Depois de o bloco ser cometido novamente, diverge da sua contraparte em qualquer instantâneo, e será cobrado pelos seus dados. O mesmo se aplica a uma página numa bolha de página que é atualizada com dados idênticos.
- Atualizar uma bolha de bloco chamando um método que sobrepor todo o conteúdo da bolha substituirá todos os blocos na bolha. Se tiver uma foto associada a essa bolha, todos os blocos na bolha da base e instantâneo agora divergem, e serão carregados por todos os blocos em ambas as bolhas. Isto é verdade mesmo que os dados na bolha de base e no instantâneo permaneçam idênticos.
- O serviço Azure Blob não tem meios para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e comprometido é tratado como único, mesmo que tenha os mesmos dados e o mesmo ID do bloco. Como os encargos acumulam-se para blocos únicos, é importante considerar que atualizar uma bolha que tem um instantâneo resulta em blocos únicos adicionais e custos adicionais.

### <a name="minimize-costs-with-snapshot-management"></a>Minimizar custos com gestão de instantâneos

Recomendamos que se gere cuidadosamente as suas fotos para evitar custos adicionais. Pode seguir estas boas práticas para ajudar a minimizar os custos incorridos pelo armazenamento das suas fotos:

- Elimine e re-crie instantâneos associados a uma bolha sempre que atualizar a bolha, mesmo que esteja a atualizar dados idênticos, a menos que o design da sua aplicação exija que mantenha instantâneos. Ao eliminar e recriar as imagens da bolha, pode garantir que a bolha e os instantâneos não divergem.
- Se estiver a manter instantâneos para uma bolha, evite ligar métodos que substituam toda a bolha quando atualizar a bolha. Em vez disso, atualize o menor número possível de blocos para manter os custos baixos.

### <a name="snapshot-billing-scenarios"></a>Cenários de faturação instantânea

Os seguintes cenários demonstram como os encargos acumulam-se por uma bolha de bloco e seus instantâneos.

## <a name="pricing-and-billing"></a>Preços e faturação

A criação de um instantâneo, que é uma cópia apenas de leitura de uma bolha, pode resultar em encargos adicionais de armazenamento de dados na sua conta. Ao conceber a sua aplicação, é importante estar ciente de como estes encargos podem acumular-se para que possa minimizar os custos.

Os instantâneos blob, como as versões blob, são faturados ao mesmo ritmo que os dados ativos. A forma como as imagens são faturadas depende se definiu explicitamente o nível para a bolha de base ou para qualquer uma das suas imagens (ou versões). Para obter mais informações sobre os níveis de blob, consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo](storage-blob-storage-tiers.md).

Se não mudou o nível de uma bolha ou instantâneo, então é cobrado por blocos únicos de dados através dessa bolha, das suas fotos e de quaisquer versões que possa ter. Para obter mais informações, consulte [Billing quando o nível de bolha não tiver sido explicitamente definido](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Se mudou o nível de uma bolha ou instantâneo, então é cobrado por todo o objeto, independentemente de a bolha e o instantâneo estarem eventualmente no mesmo nível novamente. Para obter mais informações, consulte [Billing quando o nível de bolhas tiver sido explicitamente definido](#billing-when-the-blob-tier-has-been-explicitly-set).

Para obter mais informações sobre detalhes de faturação para versões [blob,](versioning-overview.md)consulte a versão Blob .

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Faturação quando o nível blob não foi explicitamente definido

Se não tiver definido explicitamente o nível blob para uma bolha de base ou qualquer uma das suas imagens, então é cobrado por blocos ou páginas únicos através da bolha, suas fotos e quaisquer versões que possa ter. Os dados que são partilhados através de uma bolha e as suas imagens são cobrados apenas uma vez. Quando uma bolha é atualizada, os dados numa bolha de base divergem dos dados armazenados nas suas imagens, e os dados únicos são carregados por bloco ou página.

Quando se substitui um bloco dentro de uma bolha de bloco, esse bloco é posteriormente carregado como um bloco único. Isto é verdade mesmo que o bloco tenha o mesmo ID do bloco e os mesmos dados que tem no instantâneo. Depois de o bloco ser cometido novamente, diverge da sua contraparte no instantâneo, e será cobrado pelos seus dados. O mesmo se aplica a uma página numa bolha de página que é atualizada com dados idênticos.

O armazenamento de bolhas não tem meios para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e comprometido é tratado como único, mesmo que tenha os mesmos dados e o mesmo ID do bloco. Como os encargos acumulam-se para blocos únicos, é importante ter em mente que atualizar uma bolha quando essa bolha tem instantâneos ou versões resultará em blocos exclusivos adicionais e custos adicionais.

Quando uma bolha tiver instantâneos, ligue para as operações de atualização em blobs de blocos para que atualizem o menor número possível de blocos. As operações de escrita que permitem um controlo fino sobre blocos são [Put Block](/rest/api/storageservices/put-block) and Put [Block List](/rest/api/storageservices/put-block-list). A operação [Put Blob,](/rest/api/storageservices/put-blob) por outro lado, substitui todo o conteúdo de uma bolha, pelo que pode levar a custos adicionais.

Os seguintes cenários demonstram como os encargos acumulam-se para uma bolha de bloco e suas instantâneas quando o nível do blob não foi explicitamente definido.

#### <a name="scenario-1"></a>Cenário 1

No cenário 1, a bolha de base não foi atualizada após a tomada do instantâneo, pelo que as cargas são incorridos apenas para blocos únicos 1, 2 e 3.

![Diagrama 1 mostrando faturação para blocos únicos em bolha de base e instantâneo.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Cenário 2

No cenário 2, a bolha de base foi atualizada, mas o instantâneo não. O Bloco 3 foi atualizado, e apesar de conter os mesmos dados e o mesmo ID, não é o mesmo que o bloco 3 no instantâneo. Como resultado, a conta é cobrada por quatro blocos.

![Diagrama 2 mostrando faturação para blocos únicos em bolha de base e instantâneo.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Cenário 3

No cenário 3, a bolha base foi atualizada, mas o instantâneo não. O bloco 3 foi substituído pelo bloco 4 na bolha da base, mas o instantâneo ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro blocos.

![Diagrama 3 mostrando faturação para blocos únicos em bolha de base e instantâneo.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Cenário 4

No cenário 4, a bolha de base foi completamente atualizada e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada para todos os oito blocos únicos.

![Diagrama 4 mostrando faturação para blocos únicos em bolha de base e instantâneo.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Evite ligar métodos que substituam toda a bolha e, em vez disso, atualize os blocos individuais para manter os custos baixos.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Faturação quando o nível blob foi explicitamente definido

Se tiver definido explicitamente o nível blob para uma bolha ou instantâneo (ou versão), então é cobrado o comprimento total do conteúdo do objeto no novo nível, independentemente de partilhar blocos com um objeto no nível original. Também é cobrado o comprimento total do conteúdo da versão mais antiga do nível original. Quaisquer versões ou instantâneos que permaneçam no nível original são cobrados por blocos únicos que possam partilhar, como descrito em [Billing quando o nível blob não foi explicitamente definido](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Mover uma bolha para um novo nível

A tabela seguinte descreve o comportamento de faturação para uma bolha ou instantâneo quando é movido para um novo nível.

| Quando o nível do blob é definido explicitamente em... | Então é cobrado por... |
|-|-|
| Uma bolha de base com um instantâneo | A bolha de base no novo nível e o instantâneo mais antigo no nível original, além de quaisquer blocos únicos em outros instantâneos. <sup>1</sup> |
| Uma bolha de base com uma versão anterior e um instantâneo | A bolha base no novo nível, a versão mais antiga do nível original, e o instantâneo mais antigo no nível original, além de quaisquer blocos únicos em outras versões ou instantâneos<sup>1</sup>. |
| Um instantâneo | O instantâneo no novo nível e a bolha de base no nível original, além de quaisquer blocos únicos em outros instantâneos. <sup>1</sup> |

<sup>1</sup> Se existirem outras versões ou instantâneos anteriores que não tenham sido retirados do seu nível original, essas versões ou instantâneos são carregados com base no número de blocos únicos que contêm, como descrito em [Billing quando o nível de bolhas não foi explicitamente definido](#billing-when-the-blob-tier-has-not-been-explicitly-set).

O diagrama seguinte ilustra como os objetos são faturados quando uma bolha com instantâneos é movida para um nível diferente.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Diagrama mostrando como os objetos são faturados quando uma bolha com instantâneos é explicitamente nivelada.":::

Definir explicitamente o nível para uma bolha, versão ou instantâneo não pode ser desfeito. Se mover uma bolha para um novo nível e depois movê-la de volta para o seu nível original, é cobrado o comprimento total do conteúdo do objeto, mesmo que partilhe blocos com outros objetos no nível original.

As operações que definem explicitamente o nível de uma bolha, versão ou instantâneo incluem:

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Definir Camada de Blob)
- [Coloque blob](/rest/api/storageservices/put-blob) com nível especificado
- [Colocar lista de blocos](/rest/api/storageservices/put-block-list) com nível especificado
- [Bolha de cópia](/rest/api/storageservices/copy-blob) com nível especificado

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Apagar uma bolha quando a eliminação suave está ativada

Quando a eliminação suave do blob estiver ativada, se eliminar ou substituir uma bolha de base que tenha definido explicitamente o seu nível, então quaisquer versões ou instantâneos anteriores da bolha de eliminação suave são faturadas em pleno comprimento de conteúdo. Para obter mais informações sobre como a versão blob e o soft delete funcionam em conjunto, consulte [a versão Blob e a eliminação suave](versioning-overview.md#blob-versioning-and-soft-delete).

A tabela seguinte descreve o comportamento de faturação de uma bolha que é apagada suavemente, dependendo se a versão está ativada ou desativada. Quando a versão está ativada, uma nova versão é criada quando uma bolha é apagada suavemente. Quando a versão é desativada, a eliminação suave de uma bolha cria um instantâneo de eliminação suave.

| Quando se substitui uma bolha de base com o seu nível explicitamente definido... | Então é cobrado por... |
|-|-|
| Se a eliminação suave e a versão macias estão ativadas | Todas as versões existentes com o comprimento total do conteúdo, independentemente do nível. |
| Se a eliminação suave blob estiver ativada, mas a versão é desativada | Todos os instantâneos de exclusão suave existentes com todo o comprimento do conteúdo, independentemente do nível. |

## <a name="next-steps"></a>Passos seguintes

- [Versão blob](versioning-overview.md)
- [Criar e gerir um instantâneo blob em .NET](snapshots-manage-dotnet.md)
- [Apoiar discos VM não geridos da Azure com instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md)
