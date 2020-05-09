---
title: Fotos blob
titleSuffix: Azure Storage
description: Aprenda a criar uma foto apenas de uma bolha para fazer o back-up dos dados blob num dado momento no tempo.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 26646ad5ae42d2fa301a7f894084a78a64c73bd4
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884673"
---
# <a name="blob-snapshots"></a>Fotos blob

Um instantâneo é uma versão só de leitura de uma bolha que é tirada em um momento de tempo.

> [!NOTE]
> A versão blob (pré-visualização) oferece uma forma alternativa de manter cópias históricas de uma bolha. Para mais informações, consulte [a versão Blob (pré-visualização)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Sobre fotos blob

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Uma imagem de uma bolha é idêntica à sua bolha base, exceto que o blob URI tem um valor **DateTime** anexado ao blob URI para indicar o tempo em que o instantâneo foi tirado. Por exemplo, se uma página `http://storagesample.core.blob.windows.net/mydrives/myvhd`blob URI é `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`, o uri snapshot é semelhante a .

> [!NOTE]
> Todos os instantâneos partilham o URI da bolha base. A única distinção entre a bolha base e o instantâneo é o valor de **DataTime** anexado.
>

Uma bolha pode ter várias fotos. Os instantâneos persistem até serem explicitamente eliminados, o que significa que um instantâneo não pode sobreviver à sua bolha base. Pode enumerar as imagens associadas à bolha base para rastrear as suas imagens atuais.

Quando se cria uma imagem instantânea de uma bolha, as propriedades do sistema da bolha são copiadas para o instantâneo com os mesmos valores. Os metadados da bolha base também são copiados para o instantâneo, a menos que especifique metadados separados para o instantâneo quando o criar. Depois de criar uma imagem instantânea, pode ler, copiar ou apagar, mas não pode modificá-la.

Quaisquer locações associadas à bolha base não afetam o instantâneo. Não pode adquirir um contrato de arrendamento numa foto.

Um ficheiro VHD é utilizado para armazenar as informações e o estado atuais para um disco VM. Pode desmontar um disco de dentro do VM ou desligar o VM e, em seguida, tirar uma foto do seu ficheiro VHD. Pode utilizar esse ficheiro instantâneo mais tarde para recuperar o ficheiro VHD nesse momento e recriar o VM.

## <a name="understand-how-snapshots-accrue-charges"></a>Entenda como as imagens acumulam encargos

A criação de um instantâneo, que é uma cópia apenas de uma bolha, pode resultar em custos adicionais de armazenamento de dados na sua conta. Ao conceber a sua aplicação, é importante estar ciente de como estes encargos podem acumular-se para que possa minimizar os custos.

### <a name="important-billing-considerations"></a>Considerações importantes de faturação

A lista seguinte inclui pontos-chave a considerar na criação de um instantâneo.

- A sua conta de armazenamento incorre em encargos para blocos ou páginas únicos, quer estejam na bolha ou no instantâneo. A sua conta não incorre em encargos adicionais para instantâneos associados a uma bolha até atualizar a bolha em que se baseiam. Depois de atualizar a bolha base, diverge das suas imagens. Quando isto acontece, é cobrado pelos blocos ou páginas únicos em cada bolha ou instantâneo.
- Quando se substitui um bloco dentro de uma bolha de bloco, esse bloco é posteriormente carregado como um bloco único. Isto é verdade mesmo que o bloco tenha o mesmo ID de bloco e os mesmos dados que tem no instantâneo. Depois de o bloco ser novamente comprometido, diverge da sua congénere em qualquer instantâneo, e será cobrado pelos seus dados. O mesmo se aplica a uma página numa página blob que é atualizada com dados idênticos.
- Substituir uma bolha de bloco, ligando para [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], ou [UploadFromByteArray][dotnet_UploadFromByteArray] substitui todos os blocos da bolha. Se tiver uma foto associada a essa bolha, todos os blocos na bolha base e instantâneo saem agora, e será cobrado por todos os blocos em ambas as bolhas. Isto é verdade mesmo que os dados na bolha base e no instantâneo permaneçam idênticos.
- O serviço Azure Blob não dispõe de meios para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e comprometido é tratado como único, mesmo que tenha os mesmos dados e o mesmo bloco DE IDENTIFICAção. Como as taxas acumulam-se para blocos únicos, é importante considerar que atualizar uma bolha que tem um instantâneo resulta em blocos únicos adicionais e encargos adicionais.

### <a name="minimize-cost-with-snapshot-management"></a>Minimizar o custo com a gestão instantânea

Recomendamos que gere cuidadosamente as suas fotos para evitar encargos adicionais. Pode seguir estas boas práticas para ajudar a minimizar os custos incorridos pelo armazenamento das suas fotos:

- Elimine e recrie instantâneos associados a uma bolha sempre que atualizar a bolha, mesmo que esteja a atualizar com dados idênticos, a menos que o design da sua aplicação exija que mantenha instantâneos. Ao eliminar e recriar as imagens da bolha, pode garantir que a bolha e as imagens não divergem.
- Se estiver a manter instantâneos para uma bolha, evite ligar [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], ou [UploadFromByteArray][dotnet_UploadFromByteArray] para atualizar a bolha. Estes métodos substituem todos os blocos da bolha, fazendo com que a sua bolha base e os seus instantâneos diverjam significativamente. Em vez disso, atualize o menor número possível de blocos utilizando os métodos [PutBlock][dotnet_PutBlock] e [PutBlockList][dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Cenários de faturação instantânea

Os seguintes cenários demonstram como as taxas acumulam-se para uma bolha de bloco e as suas fotos.

#### <a name="scenario-1"></a>Cenário 1

No cenário 1, a bolha base não foi atualizada após a tomada do instantâneo, pelo que as taxas são incorridas apenas para os blocos 1, 2 e 3 únicos.

![Recursos de Armazenamento Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Cenário 2

No cenário 2, a bolha base foi atualizada, mas o instantâneo não. O bloco 3 foi atualizado, e mesmo que contenha os mesmos dados e o mesmo ID, não é o mesmo que o bloco 3 no instantâneo. Como resultado, a conta é cobrada por quatro quarteirões.

![Recursos de Armazenamento Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Cenário 3

No cenário 3, a bolha base foi atualizada, mas o instantâneo não. O bloco 3 foi substituído pelo bloco 4 na bolha base, mas o instantâneo ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro quarteirões.

![Recursos de Armazenamento Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Cenário 4

No cenário 4, a bolha base foi completamente atualizada e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada por todos os oito blocos únicos. Este cenário pode ocorrer se estiver a utilizar um método de atualização como [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], ou [UploadFromByteArray][dotnet_UploadFromByteArray], porque estes métodos substituem todos os conteúdos de uma bolha.

![Recursos de Armazenamento Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Passos seguintes

- [Crie e gerencie um instantâneo blob em .NET](snapshots-manage-dotnet.md)
- [Back up Discos VM não geridos Azure com instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md)
