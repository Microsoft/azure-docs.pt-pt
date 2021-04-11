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

[!INCLUDE [Upload files with the CLI](./includes/task-upload-file-to-asset-cli.md)]

## <a name="python"></a>[Python](#tab/python)

Assumindo que o seu código já estabeleceu a autenticação e já criou um Ativo de entrada, use o seguinte código snippet para carregar ficheiros locais para esse ativo (in_container).

```python
#The storage objects
from azure.storage.blob import BlobServiceClient, BlobClient

#Establish storage variables
storage_account_name = '<your storage account name'
storage_account_key = '<your storage account key'
storage_blob_url = 'https://<your storage account name>.blob.core.windows.net/'

in_container = 'asset-' + inputAsset.asset_id

#The file path of local file you want to upload
source_file = "ignite.mp4"

# Use the Storage SDK to upload the video
blob_service_client = BlobServiceClient(account_url=storage_blob_url, credential=storage_account_key)
blob_client = blob_service_client.get_blob_client(in_container,source_file)

# Upload the video to storage as a block blob
with open(source_file, "rb") as data:
    blob_client.upload_blob(data, blob_type="BlockBlob")
```

---
<!-- add these to the tabs when available -->
Para outros métodos consulte a [documentação de Armazenamento Azure](../../storage/blobs/index.yml) para trabalhar com bolhas em [.NET,](../../storage/blobs/storage-quickstart-blobs-dotnet.md) [Java,](../../storage/blobs/storage-quickstart-blobs-java.md) [Python](../../storage/blobs/storage-quickstart-blobs-python.md)e [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Passos seguintes

> [Visão geral dos Serviços de Mídia](media-services-overview.md)
