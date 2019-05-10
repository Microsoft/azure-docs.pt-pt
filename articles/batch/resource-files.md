---
title: Criar e utilizar ficheiros de recursos – Azure Batch | Documentos da Microsoft
description: Saiba como criar arquivos de recursos do Azure Batch de várias origens de entrada.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 113faffb0ebac50a67c96ce21e0ee2c1564bb4fc
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405647"
---
# <a name="creating-and-using-resource-files"></a>Criar e utilizar ficheiros de recursos

Uma tarefa do Batch muitas vezes requer alguma forma de dados a serem processados. Arquivos de recursos são os meios para fornecer esses dados para o Batch máquina virtual (VM) através de uma tarefa. Todos os tipos de tarefas suportam ficheiros de recursos: começar a tarefas, tarefas, tarefas de preparação, tarefas de lançamento, etc. Este artigo aborda alguns métodos comuns sobre como criar arquivos de recursos e colocá-los numa VM.  

Arquivos de recursos são um mecanismo para colocar dados numa VM no Batch, mas o tipo de dados e como são utilizadas é flexível. No entanto, existem alguns casos de utilização comuns:

1. Aprovisionar ficheiros comuns em cada VM usando arquivos de recurso numa tarefa de início
1. Aprovisionar os dados de entrada a ser processado por tarefas

Ficheiros comuns poderiam ser, por exemplo, arquivos numa tarefa de início utilizado para instalar aplicações que as tarefas executam. Dados de entrada podem ser imagens raw ou dados de vídeo ou qualquer informação a ser processado pelo Batch.

## <a name="types-of-resource-files"></a>Tipos de arquivos de recursos

Existem algumas opções diferentes disponíveis para gerar arquivos de recursos. O processo de criação de arquivos de recurso varia consoante onde estão armazenados os dados originais.

Opções para criar um arquivo de recursos:

- [URL do contentor de armazenamento](#storage-container-url): Gera um arquivo de recursos a partir de qualquer contentor de armazenamento do Azure
- [Nome do contentor de armazenamento](#storage-container-name): Gera um arquivo de recursos do nome de um contentor numa conta de armazenamento do Azure ligado ao Batch
- [Ponto final Web](#web-endpoint): Gera um arquivo de recursos a partir de qualquer URL HTTP válido

### <a name="storage-container-url"></a>URL do contentor de armazenamento

Através de um URL do contentor de armazenamento significa que poder aceder a ficheiros em qualquer contentor de armazenamento do Azure com as permissões corretas.

Neste C# exemplo, os ficheiros já foram carregados para um contentor de armazenamento do Azure como armazenamento de Blobs. Para acessar os dados necessários para criar um arquivo de recursos, primeiro é necessário aceder ao contentor de armazenamento.

Crie uma assinatura de acesso partilhado (SAS) URI com as permissões corretas para aceder ao contentor de armazenamento. Defina a hora de expiração e as permissões para a SAS. Neste caso, não hora de início for especificada, para que a SAS se torna válida imediatamente e expira após ser gerado de duas horas.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Para obter acesso de contentor, tem de ter ambos `Read` e `List` permissões, ao passo que o acesso do blob, só precisa `Read` permissão.

Uma vez configuradas as permissões, criar o token SAS e formate o URL de SAS para aceder ao contentor de armazenamento. Utilizar o URL de SAS formatado para o contentor de armazenamento, gerar um arquivo de recursos com [ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Uma alternativa para gerar um URL de SAS é para ativar o acesso de leitura anónimo, público para um contentor e respetivos blobs no armazenamento de Blobs do Azure. Ao fazê-lo, pode conceder acesso só de leitura a esses recursos sem partilhar a sua chave de conta e sem a necessidade de uma SAS. Acesso de leitura público é normalmente utilizado para cenários onde pretende que determinados blobs para estar sempre disponíveis para acesso de leitura anónimo. Se este cenário se adequa a sua solução, consulte a [anónimo aceder aos blobs](../storage/blobs/storage-manage-access-to-resources.md) artigo para saber mais sobre como gerir o acesso aos seus dados de Blobs.

### <a name="storage-container-name"></a>Nome do contentor de armazenamento

Em vez de configurar e criar um URL de SAS, pode utilizar o nome do seu contentor de armazenamento do Azure para aceder aos seus dados de Blobs. O contentor de armazenamento utilizado tem na conta de armazenamento do Azure que está ligada à sua conta do Batch, conhecida como a conta de armazenamento automático. Usando o nome do contentor de armazenamento de uma conta de armazenamento automático permite-lhe ignorar a configurar e criar um URL de SAS para aceder a um contentor de armazenamento.

Neste exemplo, partimos do princípio de que os dados a ser utilizado para a criação do arquivo de recursos já estão a ser uma conta de armazenamento do Azure ligada a sua conta do Batch. Se não tiver uma conta de armazenamento automático, veja os passos em [criar uma conta do Batch](batch-account-create-portal.md) para obter detalhes sobre como criar e associar uma conta.

Ao utilizar uma conta de armazenamento ligada, não precisa criar e configurar um URL de SAS para um contentor de armazenamento. Em vez disso, forneça o nome do contentor de armazenamento na sua conta de armazenamento ligada.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Ponto final de Web

Dados que não são carregados para o armazenamento do Azure ainda podem ser utilizados para criar arquivos de recursos. Pode especificar qualquer URL HTTP válido, que contém os dados de entrada. O URL é fornecido para a API do Batch e, em seguida, os dados são utilizados para criar um arquivo de recursos.

A seguir C# exemplo, os dados de entrada está alojado no ponto de extremidade de GitHub fictício. A API obtém o ficheiro a partir do ponto final web válido e gera um arquivo de recursos para serem consumidos por sua tarefa. São necessárias credenciais para este cenário.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Cada tarefa do Batch utiliza ficheiros de forma diferente, por isso Batch tem as opções disponíveis para gerir ficheiros de em tarefas. Os cenários seguintes não são deve ser abrangente, mas em vez disso, cobrir algumas situações comuns e fornecer recomendações.

### <a name="many-resource-files"></a>Muitos arquivos de recursos

Sua tarefa do Batch pode conter várias tarefas que utilizem os mesmo ficheiros comuns. Se os ficheiros de tarefas comuns são partilhados entre muitas tarefas, utilizar um pacote de aplicação para incluir os ficheiros em vez de usar arquivos de recursos pode ser uma opção melhor. Pacotes de aplicações fornecem otimização para a velocidade de transferência. Além disso, os dados em pacotes de aplicações é colocado em cache entre tarefas, portanto, se os ficheiros de tarefas não são alterados frequentemente, os pacotes de aplicações podem ser uma boa opção para a sua solução. Com pacotes de aplicações, não precisa gerenciar vários arquivos de recursos ou gerar URLs de SAS para aceder aos ficheiros no armazenamento do Azure manualmente. O batch funciona em segundo plano com o armazenamento do Azure para armazenar e implementar pacotes de aplicações em nós de computação.

Se cada tarefa tem muitos ficheiros exclusivos para essa tarefa, arquivos de recursos são principalmente provavelmente a melhor opção. Tarefas que utilizam ficheiros exclusivos, muitas vezes, tem de ser atualizadas ou substituídas, que não é tão fácil fazê-lo com conteúdo de pacotes de aplicação. Arquivos de recursos fornecem flexibilidade adicional para a atualização, adicionar ou editar ficheiros individuais.

### <a name="number-of-resource-files-per-task"></a>Número de ficheiros de recursos por tarefa

Se existirem vários arquivos de centenas de recursos especificados numa tarefa, o Batch pode rejeitar a tarefa para que ele se torne demasiado grande. É melhor manter as suas tarefas pequenas diminuindo o número de ficheiros de recursos sobre a tarefa propriamente dita.

Se não é possível minimizar o número de ficheiros a tarefa precisa, pode otimizar a tarefa através da criação de um arquivo de recursos único que faz referência a um contentor de armazenamento de arquivos de recursos. Para tal, guarde os seus ficheiros de recursos para um contentor de armazenamento do Azure e utilizar os diferentes modos de "Contentor" de arquivos de recursos. Utilize as opções de prefixo de BLOBs para especificar as coleções de arquivos a serem baixados para as suas tarefas.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [pacotes de aplicações](batch-application-packages.md) como uma alternativa para arquivos de recursos.
- Para obter mais informações sobre como utilizar contentores para arquivos de recursos, consulte [cargas de trabalho do contentor](batch-docker-container-workloads.md).
- Para saber como recolher e guardar os dados de saída a partir de suas tarefas, veja [manter a saída de trabalhos e tarefas](batch-task-output.md).
- Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
