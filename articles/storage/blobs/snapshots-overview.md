---
title: Snapshots blob
titleSuffix: Azure Storage
description: Aprenda a criar uma imagem apenas de leitura de uma bolha para fazer o back-up dados blob num dado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/19/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4c6c2774e0d71ec33449565efab797c040aa264f
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640604"
---
# <a name="blob-snapshots"></a>Snapshots blob

Uma foto é uma versão só de leitura de uma bolha que é tirada num ponto do tempo.

> [!NOTE]
> A versão blob (pré-visualização) oferece uma forma alternativa de manter versões anteriores de uma bolha. Para obter mais informações, consulte [a versão Blob (pré-visualização)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Sobre fotos blob

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Uma imagem instantânea de uma bolha é idêntica à sua bolha de base, exceto que o blob URI tem um valor **DateTime** anexado ao URI blob para indicar o momento em que o instantâneo foi tirado. Por exemplo, se uma página blob URI `http://storagesample.core.blob.windows.net/mydrives/myvhd` for, o snapshot URI é semelhante a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Todas as fotos partilham o URI da bolha da base. A única distinção entre a bolha de base e o instantâneo é o valor **de DateTime** anexado.
>

Uma bolha pode ter várias fotos. As imagens persistem até serem explicitamente eliminadas, independentemente ou como parte de uma operação [Delete Blob](/rest/api/storageservices/delete-blob) para a bolha de base. Pode enumerar os instantâneos associados à bolha de base para rastrear as suas imagens atuais.

Quando se cria uma imagem instantânea de uma bolha, as propriedades do sistema da bolha são copiadas para o instantâneo com os mesmos valores. Os metadados da bolha de base também são copiados para o instantâneo, a menos que especifique metadados separados para a imagem quando o criar. Depois de criar uma imagem, pode ler, copiar ou apagar, mas não pode modificá-la.

Quaisquer locações associadas à bolha de base não afetam o instantâneo. Não pode adquirir um contrato de arrendamento com uma foto instantânea.

Um ficheiro VHD é utilizado para armazenar as informações e o estado atuais de um disco VM. Pode retirar um disco de dentro do VM ou desligar o VM e, em seguida, tirar uma fotografia do seu ficheiro VHD. Pode utilizar o ficheiro instantâneo mais tarde para recuperar o ficheiro VHD nessa altura e recriar o VM.

## <a name="understand-how-snapshots-accrue-charges"></a>Entenda como os instantâneos acumulam encargos

A criação de um instantâneo, que é uma cópia apenas de leitura de uma bolha, pode resultar em encargos adicionais de armazenamento de dados na sua conta. Ao conceber a sua aplicação, é importante estar ciente de como estes encargos podem acumular-se para que possa minimizar os custos.

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

#### <a name="scenario-1"></a>Cenário 1

No cenário 1, a bolha de base não foi atualizada após a tomada do instantâneo, pelo que as cargas são incorridos apenas para blocos únicos 1, 2 e 3.

![Recursos de armazenamento Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Cenário 2

No cenário 2, a bolha de base foi atualizada, mas o instantâneo não. O Bloco 3 foi atualizado, e apesar de conter os mesmos dados e o mesmo ID, não é o mesmo que o bloco 3 no instantâneo. Como resultado, a conta é cobrada por quatro blocos.

![Recursos de armazenamento Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Cenário 3

No cenário 3, a bolha base foi atualizada, mas o instantâneo não. O bloco 3 foi substituído pelo bloco 4 na bolha da base, mas o instantâneo ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro blocos.

![Recursos de armazenamento Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Cenário 4

No cenário 4, a bolha de base foi completamente atualizada e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada para todos os oito blocos únicos.

![Recursos de armazenamento Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Evite ligar métodos que substituam toda a bolha e, em vez disso, atualize os blocos individuais para manter os custos baixos.

## <a name="next-steps"></a>Passos seguintes

- [Criar e gerir um instantâneo blob em .NET](snapshots-manage-dotnet.md)
- [Apoiar discos VM não geridos da Azure com instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md)
