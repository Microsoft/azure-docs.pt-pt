---
title: Utilizar o Armazenamento de Blobs do Azure para conversão de modelos
description: Descreve passos comuns para configurar e utilizar o armazenamento de bolhas para conversão de modelos.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80681652"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Utilizar o Armazenamento de Blobs do Azure para conversão de modelos

O serviço [de conversão de modelos](model-conversion.md) requer acesso ao armazenamento de blob Azure para que possa recuperar dados de entrada e armazenar dados de saída. Este artigo descreve como fazer os passos mais comuns.

## <a name="prepare-azure-storage-accounts"></a>Preparar contas de armazenamento Azure

- Criar uma conta de armazenamento (StorageV2)
- Criar um recipiente de bolha de entrada na conta de armazenamento (por exemplo, denominado "arrinput")
- Criar um recipiente de bolha de saída na conta de armazenamento (por exemplo, denominado "arroutput")

> [!TIP]
> Para instruções passo a passo como configurar a sua conta de armazenamento, veja [o Quickstart: Converta um modelo de renderização](../../quickstarts/convert-model.md)

A criação da conta de armazenamento e dos recipientes blob pode ser feita com uma das seguintes ferramentas:

- [Portal do Azure](https://portal.azure.com)
- [linha de comando az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
- SDKs (C#, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Certifique-se de que a Renderização Remota Azure pode aceder à sua conta de armazenamento

O Azure Remote Rending precisa de recuperar dados de modelos da sua conta de armazenamento e de lhe escrever dados.

Pode conceder ao Azure Remote Rendering acesso à sua conta de armazenamento das seguintes duas formas:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Conecte a sua conta de Armazenamento Azure com a sua Conta de Renderização Remota Azure

Siga os passos dados na secção [Criar uma Conta.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>Recuperar SAS para os recipientes de armazenamento

As assinaturas de acesso armazenadas (SAS) são usadas para conceder acesso de leitura para entrada e escrever acesso para saída. Recomendamos a geração de novos URIs cada vez que um modelo é convertido. Uma vez que os URIs expiram após algum tempo, persistência deles por uma duração mais longa pode correr o risco de quebrar a sua aplicação inesperadamente.

Os detalhes sobre o SAS podem ser encontrados na [documentação sas.](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

Um SAS URI pode ser gerado usando um dos seguintes:

- módulo PowerShell az
  - ver o [exemplo Scripts PowerShell](../../samples/powershell-example-scripts.md)
- [linha de comando az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
  - clique no recipiente "Obter Assinatura de Acesso Partilhado" (ler, listar o acesso ao recipiente de entrada, escrever acesso para recipiente de saída)
- SDKs (C#, Python ... )

Um exemplo de utilização de Assinaturas de Acesso Partilhado na conversão de ativos é mostrado em Conversion.ps1 dos [Scripts exemplo powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Faça upload de um modelo de entrada

Para começar a converter um modelo, é necessário carregá-lo, utilizando uma das seguintes opções:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) - um UI conveniente para carregar/descarregar/gerir ficheiros no armazenamento de blob azul
- [Linha de comando Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - ver os [scripts Exemplo PowerShell](../../samples/powershell-example-scripts.md)
- [Utilizando um SDK de armazenamento (Python, C# ... )](https://docs.microsoft.com/azure/storage/)
- [Utilizando as APIs de REPOUSO DE ARMAZENAMENTO Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Para um exemplo de como carregar dados para conversão consulte Conversion.ps1 dos [Scripts exemplo powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Obtenha um SAS URI para o modelo convertido

Este passo é semelhante à [recuperação de SAS para os recipientes de armazenamento.](#retrieve-sas-for-the-storage-containers) No entanto, desta vez é necessário recuperar um SAS URI para o ficheiro do modelo, que foi escrito no recipiente de saída.

Por exemplo, para recuperar um SAS URI através do [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)clique com o botão direito no ficheiro do modelo e selecione "Obter Assinatura de Acesso Partilhado".

É necessária uma Assinatura de Acesso Partilhado (SAS) para carregar modelos se não tiver ligado a sua conta de armazenamento à sua conta de renderização remota Azure. Pode aprender a ligar a sua conta na [Criar uma Conta.](../create-an-account.md#link-storage-accounts)

## <a name="next-steps"></a>Passos seguintes

- [Configurar a conversão do modelo](configure-model-conversion.md)
- [A conversão do modelo REST API](conversion-rest-api.md)
