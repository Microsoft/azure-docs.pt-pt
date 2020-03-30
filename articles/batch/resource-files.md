---
title: Criação e utilização de ficheiros de recursos - Lote Azure
description: Saiba como criar ficheiros de recursos do Lote a partir de várias fontes de entrada. Este artigo abrange alguns métodos comuns sobre como criá-los e colocá-los num VM.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531146"
---
# <a name="creating-and-using-resource-files"></a>Criação e utilização de ficheiros de recursos

Uma tarefa do Lote Azure requer frequentemente alguma forma de dados para processar. Os ficheiros de recursos são a forma de fornecer estes dados à sua máquina virtual do Lote (VM) através de uma tarefa. Todos os tipos de tarefas suportam ficheiros de recursos: tarefas, tarefas de início, tarefas de preparação de emprego, tarefas de libertação de emprego, etc. Este artigo abrange alguns métodos comuns de como criar ficheiros de recursos e colocá-los num VM.  

Os ficheiros de recursos colocam dados num VM em Batch, mas o tipo de dados e como é usado é flexível. Existem, no entanto, alguns casos de uso comum:

1. Fornecer ficheiros comuns em cada VM utilizando ficheiros de recursos numa tarefa inicial
1. Dados de entrada de provisão a serem tratados por tarefas

Os ficheiros comuns podem ser, por exemplo, ficheiros sobre uma tarefa inicial utilizada para instalar aplicações que as suas tarefas executam. Os dados de entrada podem ser dados de imagem ou vídeo brutos, ou qualquer informação a ser processada pelo Batch.

## <a name="types-of-resource-files"></a>Tipos de ficheiros de recursos

Existem algumas opções diferentes disponíveis para gerar ficheiros de recursos. O processo de criação de ficheiros de recursos varia consoante os dados originais sejam armazenados.

Opções para criar um ficheiro de recursos:

- [URL](#storage-container-url)do recipiente de armazenamento : Gera um ficheiro de recursos de qualquer recipiente de armazenamento em Azure
- [Nome](#storage-container-name)do recipiente de armazenamento : Gera um ficheiro de recurso a partir do nome de um contentor numa conta de armazenamento Azure ligada ao Lote
- [Ponto final da Web](#web-endpoint): Gera um ficheiro de recursos de qualquer URL HTTP válido

### <a name="storage-container-url"></a>URL do recipiente de armazenamento

Utilizando um URL de recipiente de armazenamento, com as permissões corretas, pode aceder a ficheiros em qualquer recipiente de armazenamento em Azure. 

Neste exemplo C#, os ficheiros já foram enviados para um contentor de armazenamento Azure como armazenamento de bolhas. Para aceder aos dados necessários para criar um ficheiro de recursos, precisamos primeiro de ter acesso ao recipiente de armazenamento.

Crie uma assinatura de acesso partilhado (SAS) URI com as permissões corretas para aceder ao recipiente de armazenamento. Detete o tempo de validade e as permissões para o SAS. Neste caso, não é especificada a hora de início, pelo que o SAS torna-se válido imediatamente e expira duas horas após a sua geração.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Para acesso ao contentor, `Read` `List` deve ter ambas as permissões e `Read` permissões, enquanto que com acesso à bolha, só precisa de permissão.

Uma vez configuradas as permissões, crie o token SAS e forme o URL SAS para acesso ao recipiente de armazenamento. Utilizando o URL SAS formatado para o recipiente [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)de armazenamento, gere um ficheiro de recursos com .

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Uma alternativa para gerar um URL SAS é permitir o acesso anónimo e público a um contentor e seus blobs no armazenamento Azure Blob. Ao fazê-lo, pode conceder acesso apenas a leitura a estes recursos sem partilhar a chave da sua conta, e sem exigir um SAS. O acesso à leitura do público é normalmente utilizado para cenários em que você quer que certas bolhas estejam sempre disponíveis para acesso de leitura anónimo. Se este cenário se adequa à sua solução, consulte o artigo de [acesso anónimo ao artigo da Blobs](../storage/blobs/storage-manage-access-to-resources.md) para saber mais sobre como gerir o acesso aos seus dados blob.

### <a name="storage-container-name"></a>Nome do recipiente de armazenamento

Em vez de configurar e criar um URL SAS, pode utilizar o nome do seu recipiente de armazenamento Azure para aceder aos seus dados blob. O recipiente de armazenamento que utiliza deve estar na conta de armazenamento Azure que está ligada à sua conta Batch. A conta de armazenamento é conhecida como a conta de armazenamento automático. A utilização do recipiente de armazenamento automático permite-lhe contornar a configuração e a criação de um URL SAS para aceder a um recipiente de armazenamento.

Neste exemplo, assumimos que os dados a utilizar para a criação de ficheiros de recursos já se encontra numa conta de Armazenamento Azure ligada à sua conta Batch. Se não tiver uma conta de armazenamento automático, consulte os passos em [Criar uma conta de Lote](batch-account-create-portal.md) para obter detalhes sobre como criar e ligar uma conta.

Ao utilizar uma conta de armazenamento ligada, não precisa de criar e configurar um URL SAS para um recipiente de armazenamento. Em vez disso, forneça o nome do recipiente de armazenamento na sua conta de armazenamento ligada.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Ponto final da web

Os dados que não são enviados para o Armazenamento Azure ainda podem ser usados para criar ficheiros de recursos. Pode especificar qualquer URL HTTP válido que contenha os seus dados de entrada. O URL é fornecido à API do lote e, em seguida, os dados são usados para criar um ficheiro de recursos.

No exemplo c# seguinte, os dados de entrada são hospedados num ponto final fictício do GitHub. A API recupera o ficheiro a partir do ponto final da web válido e gera um ficheiro de recursos a ser consumido pela sua tarefa. Não são necessárias credenciais para este cenário.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Cada tarefa do Lote Azure utiliza ficheiros de forma diferente, razão pela qual o Batch tem opções disponíveis para gerir ficheiros em tarefas. Os seguintes cenários não são para ser abrangentes, mas sim cobrir algumas situações comuns e fornecer recomendações.

### <a name="many-resource-files"></a>Muitos ficheiros de recursos

O seu trabalho em Lote pode conter várias tarefas que todos utilizam os mesmos ficheiros comuns. Se os ficheiros de tarefas comuns forem partilhados entre muitas tarefas, usar um pacote de aplicação para conter os ficheiros em vez de utilizar ficheiros de recursos pode ser uma melhor opção. Os pacotes de aplicações fornecem otimização para a velocidade de descarregamento. Além disso, os dados nos pacotes de aplicações estão em cache entre tarefas, por isso, se os seus ficheiros de tarefas não mudarem com frequência, os pacotes de aplicação podem ser adequados para a sua solução. Com pacotes de aplicações, não precisa de gerir manualmente vários ficheiros de recursos ou gerar URLs SAS para aceder aos ficheiros no Armazenamento Azure. O lote trabalha em segundo plano com o Armazenamento Azure para armazenar e implementar pacotes de aplicações para calcular os nódosos.

Se cada tarefa tiver muitos ficheiros exclusivos dessa tarefa, os ficheiros de recursos são a melhor opção porque as tarefas que utilizam ficheiros únicos muitas vezes precisam de ser atualizadas ou substituídas, o que não é tão fácil de fazer com o conteúdo dos pacotes de aplicações. Os ficheiros de recursos proporcionam flexibilidade adicional para atualizar, adicionar ou editar ficheiros individuais.

### <a name="number-of-resource-files-per-task"></a>Número de ficheiros de recursos por tarefa

Se houver várias centenas de ficheiros de recursos especificados numa tarefa, o Batch pode rejeitar a tarefa como sendo demasiado grande. É melhor manter as suas tarefas pequenas minimizando o número de ficheiros de recursos na própria tarefa.

Se não houver forma de minimizar o número de ficheiros que a sua tarefa necessita, pode otimizar a tarefa criando um único ficheiro de recursos que referencia um recipiente de armazenamento de ficheiros de recursos. Para tal, coloque os seus ficheiros de recursos num recipiente de armazenamento Azure e utilize os diferentes [métodos](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) de "contentor" para ficheiros de recursos. Utilize as opções de prefixo blob para especificar coleções de ficheiros a serem descarregados para as suas tarefas.

## <a name="next-steps"></a>Passos seguintes

- Conheça os pacotes de [aplicações](batch-application-packages.md) como alternativa aos ficheiros de recursos.
- Para obter mais informações sobre a utilização de contentores para ficheiros de recursos, consulte as [cargas de trabalho](batch-docker-container-workloads.md)do Contentor .
- Para aprender a recolher e salvar os dados de saída das suas tarefas, consulte a [saída de trabalho e tarefa sinuosa](batch-task-output.md).
- Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
