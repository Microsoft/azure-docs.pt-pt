---
título: Upload media : Descrição dos Serviços Azure Media: Saiba como carregar os meios de comunicação para streaming ou codificação.
serviços: media-services documentationcenter: '' autor: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: how-to ms.date: 08/31/2020 ms.author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>Upload mídia para streaming ou codificação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos Serviços de Comunicação Social, faça o upload dos seus ficheiros digitais (media) para um recipiente de bolhas associado a um ativo. A entidade [Asset](/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, recolhas de miniaturas, faixas de texto e ficheiros de legendas fechados (e os metadados sobre estes ficheiros). Uma vez que os ficheiros são enviados para o recipiente do ativo, o seu conteúdo é armazenado de forma segura na nuvem para posterior processamento e streaming.

Antes de começar, terá de recolher ou pensar em alguns valores.

1. O caminho do arquivo local para o ficheiro que pretende carregar
1. O ID do ativo para o ativo (recipiente)
1. O nome que pretende usar para o ficheiro carregado, incluindo a extensão
1. O nome da conta de armazenamento que está a usar
1. A chave de acesso para a sua conta de armazenamento

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Uma vez [criado um ativo utilizando o carteiro ou outro método REST e obteve o URL SAS para o ativo,](how-to-create-asset.md?tabs=rest)utilize as APIs de Armazenamento Azure ou SDKs (por exemplo, a [API de Armazenamento REST](../../storage/common/storage-rest-api-auth.md) ou [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Para outros métodos consulte a [documentação de Armazenamento Azure](../../storage/blobs/index.yml) para trabalhar com bolhas em [.NET,](../../storage/blobs/storage-quickstart-blobs-dotnet.md) [Java,](../../storage/blobs/storage-quickstart-blobs-java.md) [Python](../../storage/blobs/storage-quickstart-blobs-python.md)e [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Próximos passos

> [Visão geral dos Serviços de Mídia](media-services-overview.md)