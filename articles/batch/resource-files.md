---
title: Criar e utilizar ficheiros de recursos
description: Saiba como criar ficheiros de recursos batch a partir de várias fontes de entrada. Este artigo abrange alguns métodos comuns sobre como criá-los e colocá-los num VM.
ms.date: 03/18/2020
ms.topic: how-to
ms.openlocfilehash: 22c0220d08660402bef3fd4aaf0add6adc12a295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230947"
---
# <a name="creating-and-using-resource-files"></a>Criar e utilizar ficheiros de recursos

Uma tarefa do Azure Batch requer frequentemente alguma forma de dados para processar. Os ficheiros de recursos são a forma de fornecer estes dados à sua máquina virtual Batch (VM) através de uma tarefa. Todos os tipos de tarefas suportam ficheiros de recursos: tarefas, tarefas de início, tarefas de preparação de emprego, tarefas de libertação de emprego, etc. Este artigo abrange alguns métodos comuns de como criar ficheiros de recursos e colocá-los num VM.  

Os ficheiros de recursos colocam dados num VM em Batch, mas o tipo de dados e como são usados é flexível. Existem, no entanto, alguns casos de uso comum:

1. Disponibilização de ficheiros comuns em cada VM utilizando ficheiros de recursos numa tarefa inicial
1. Fornecimento de dados de entrada a serem tratados por tarefas

Os ficheiros comuns podem ser, por exemplo, ficheiros sobre uma tarefa inicial utilizada para instalar aplicações que as suas tarefas executam. Os dados de entrada podem ser dados de imagem ou vídeo brutos, ou qualquer informação a ser processada pelo Batch.

## <a name="types-of-resource-files"></a>Tipos de ficheiros de recursos

Existem algumas opções diferentes disponíveis para gerar ficheiros de recursos. O processo de criação de ficheiros de recursos varia consoante o local onde os dados originais são armazenados.

Opções para criar um ficheiro de recursos:

- [URL do recipiente de armazenamento:](#storage-container-url)Gera um ficheiro de recursos a partir de qualquer recipiente de armazenamento em Azure
- [Nome do recipiente de armazenamento](#storage-container-name): Gera um ficheiro de recursos a partir do nome de um recipiente numa conta de armazenamento Azure ligada ao Lote
- [Ponto final da Web](#web-endpoint): Gera um ficheiro de recursos a partir de qualquer URL HTTP válido

### <a name="storage-container-url"></a>URL do recipiente de armazenamento

A utilização de um URL de contentor de armazenamento significa que, com as permissões corretas, pode aceder a ficheiros em qualquer recipiente de armazenamento em Azure. 

Neste exemplo C#, os ficheiros já foram enviados para um recipiente de armazenamento Azure como armazenamento de bolhas. Para aceder aos dados necessários para criar um ficheiro de recursos, primeiro precisamos de ter acesso ao contentor de armazenamento.

Crie uma assinatura de acesso partilhado (SAS) URI com as permissões corretas de acesso ao recipiente de armazenamento. Desa estada o tempo de validade e as permissões para o SAS. Neste caso, não é especificado nenhum tempo de início, pelo que o SAS torna-se válido imediatamente e expira duas horas após a sua geração.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Para acesso ao contentor, tem de ter ambos `Read` e `List` permissões, enquanto que com acesso a bolhas, só precisa de `Read` permissão.

Uma vez configuradas as permissões, crie o token SAS e formate o URL SAS para acesso ao recipiente de armazenamento. Utilizando o URL SAS formatado para o recipiente de armazenamento, gere um ficheiro de recursos com [`FromStorageContainerUrl`](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl) .

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Uma alternativa para gerar um URL SAS é permitir o acesso de leitura pública anónimo a um recipiente e suas bolhas no armazenamento de Azure Blob. Ao fazê-lo, pode conceder acesso apenas de leitura a estes recursos sem partilhar a sua chave de conta, e sem necessitar de um SAS. O acesso de leitura pública é normalmente usado para cenários em que você quer que certas bolhas estejam sempre disponíveis para acesso de leitura anónimo. Se este cenário se adequa à sua solução, consulte o artigo [de acesso anónimo a blobs](../storage/blobs/storage-manage-access-to-resources.md) para saber mais sobre como gerir o acesso aos seus dados blob.

### <a name="storage-container-name"></a>Nome do recipiente de armazenamento

Em vez de configurar e criar um URL SAS, pode usar o nome do seu recipiente de armazenamento Azure para aceder aos seus dados blob. O recipiente de armazenamento que utiliza deve estar na conta de armazenamento Azure que está ligada à sua conta Batch. A conta de armazenamento é conhecida como a conta de auto-armazenamento. A utilização do recipiente de auto-armazenamento permite-lhe contornar a configuração e a criação de um URL SAS para aceder a um recipiente de armazenamento.

Neste exemplo, assumimos que os dados a utilizar para a criação de ficheiros de recursos já se encontra numa conta de Armazenamento Azure ligada à sua conta Batch. Se não tiver uma conta de auto-armazenamento, consulte os passos na [conta Criar um Lote](batch-account-create-portal.md) para obter detalhes sobre como criar e ligar uma conta.

Ao utilizar uma conta de armazenamento ligada, não precisa de criar e configurar um URL SAS para um recipiente de armazenamento. Em vez disso, forneça o nome do recipiente de armazenamento na sua conta de armazenamento ligada.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Ponto final da Web

Os dados que não são enviados para o Azure Storage ainda podem ser utilizados para criar ficheiros de recursos. Pode especificar qualquer URL HTTP válido que contenha os seus dados de entrada. O URL é fornecido à API do Lote e, em seguida, os dados são usados para criar um ficheiro de recursos.

No exemplo C# a seguir, os dados de entrada são apresentados num ponto final fictício do GitHub. A API recupera o ficheiro do ponto final web válido e gera um ficheiro de recursos para ser consumido pela sua tarefa. Não são necessárias credenciais para este cenário.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Cada tarefa do Azure Batch utiliza ficheiros de forma diferente, razão pela qual o Batch tem opções disponíveis para gerir ficheiros em tarefas. Os seguintes cenários não devem ser abrangentes, mas sim cobrir algumas situações comuns e fornecer recomendações.

### <a name="many-resource-files"></a>Muitos ficheiros de recursos

O seu trabalho em Lote pode conter várias tarefas que todos utilizam os mesmos ficheiros comuns. Se os ficheiros de tarefas comuns forem partilhados entre muitas tarefas, usar um pacote de aplicações para conter os ficheiros em vez de utilizar ficheiros de recursos pode ser uma opção melhor. Os pacotes de aplicações fornecem otimização para a velocidade de descarregamento. Além disso, os dados em pacotes de aplicações estão em cache entre tarefas, por isso, se os seus ficheiros de tarefas não mudarem frequentemente, os pacotes de aplicações podem ser um bom ajuste para a sua solução. Com pacotes de aplicações, não precisa de gerir manualmente vários ficheiros de recursos ou gerar URLs SAS para aceder aos ficheiros no Azure Storage. O lote funciona em segundo plano com o Azure Storage para armazenar e implementar pacotes de aplicações para calcular nós.

Se cada tarefa tem muitos ficheiros exclusivos dessa tarefa, os ficheiros de recursos são a melhor opção porque as tarefas que utilizam ficheiros únicos muitas vezes precisam de ser atualizadas ou substituídas, o que não é tão fácil de fazer com o conteúdo de pacotes de aplicações. Os ficheiros de recursos proporcionam flexibilidade adicional para atualizar, adicionar ou editar ficheiros individuais.

### <a name="number-of-resource-files-per-task"></a>Número de ficheiros de recursos por tarefa

Se existirem várias centenas de ficheiros de recursos especificados numa tarefa, o Batch pode rejeitar a tarefa como sendo demasiado grande. É melhor manter as suas tarefas pequenas minimizando o número de ficheiros de recursos na própria tarefa.

Se não houver forma de minimizar o número de ficheiros de que a sua tarefa necessita, pode otimizar a tarefa criando um único ficheiro de recursos que faz referência a um recipiente de armazenamento de ficheiros de recursos. Para isso, coloque os seus ficheiros de recursos num recipiente de armazenamento Azure e utilize os [diferentes métodos](/dotnet/api/microsoft.azure.batch.resourcefile#methods) de "recipiente" para ficheiros de recursos. Utilize as opções de prefixo blob para especificar as recolhas de ficheiros a serem descarregados para as suas tarefas.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os pacotes de aplicações](batch-application-packages.md) como alternativa aos ficheiros de recursos.
- Para obter mais informações sobre a utilização de recipientes para ficheiros de recursos, consulte [as cargas de trabalho do Contentor](batch-docker-container-workloads.md).
- Para aprender a recolher e guardar os dados de saída das suas tarefas, consulte [a produção de trabalho e tarefas persiste](batch-task-output.md).
- Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
