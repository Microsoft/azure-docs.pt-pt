---
title: Utilizar o Armazenamento de Blobs do Azure para conversão de modelos
description: Descreve passos comuns para configurar e utilizar o armazenamento de bolhas para conversão de modelos.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: d8a6fd458cdcf79cdeb693b25acf72d4ec48def7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507522"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Utilizar o Armazenamento de Blobs do Azure para conversão de modelos

O serviço [de conversão de modelos](model-conversion.md) requer acesso ao Azure Blob Storage para que possa recuperar dados de entrada e armazenar dados de saída. Este artigo descreve como fazer os passos mais comuns.

## <a name="prepare-azure-storage-accounts"></a>Preparar contas de armazenamento Azure

- Criar uma conta de armazenamento (StorageV2)
- Criar um recipiente de bolha de entrada na conta de armazenamento (por exemplo, denominado "arrinput")
- Criar um recipiente de bolha de saída na conta de armazenamento (por exemplo, denominado "arroutput")

> [!TIP]
> Para instruções passo a passo como configurar a sua conta de armazenamento, veja [o Quickstart: Converta um modelo de renderização](../../quickstarts/convert-model.md)

A criação da conta de armazenamento e dos recipientes blob pode ser feita com uma das seguintes ferramentas:

- [Portal do Azure](https://portal.azure.com)
- [linha de comando az](/cli/azure/install-azure-cli)
- [Explorador do Storage do Azure](https://azure.microsoft.com/features/storage-explorer/)
- SDKs (C#, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Certifique-se de que a Renderização Remota Azure pode aceder à sua conta de armazenamento

O Azure Remote Rending precisa de recuperar dados de modelos da sua conta de armazenamento e de lhe escrever dados.

Pode conceder ao Azure Remote Rendering acesso à sua conta de armazenamento das seguintes duas formas:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Conecte a sua conta de Armazenamento Azure com a sua Conta de Renderização Remota Azure

Siga os passos dados na secção [Criar uma Conta.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>Recuperar SAS para os recipientes de armazenamento

As assinaturas de acesso armazenadas (SAS) são usadas para conceder acesso de leitura para entrada e escrever acesso para saída. Recomendamos a geração de novos URIs cada vez que um modelo é convertido. Uma vez que os URIs expiram após algum tempo, persistência deles por uma duração mais longa pode correr o risco de quebrar a sua aplicação inesperadamente.

Os detalhes sobre o SAS podem ser encontrados na [documentação sas.](../../../storage/common/storage-sas-overview.md)

Um SAS URI pode ser gerado usando um dos seguintes:

- módulo PowerShell az
  - ver o [exemplo Scripts PowerShell](../../samples/powershell-example-scripts.md)
- [linha de comando az](/cli/azure/install-azure-cli)
- [Explorador do Storage do Azure](https://azure.microsoft.com/features/storage-explorer/)
  - clique no recipiente "Obter Assinatura de Acesso Partilhado" (ler, listar o acesso ao recipiente de entrada, escrever acesso para recipiente de saída)
- SDKs (C#, Python ... )

Um exemplo de utilização de Assinaturas de Acesso Partilhado na conversão de ativos é mostrado em Conversion.ps1 dos [Scripts exemplo powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Faça upload de um modelo de entrada

Para começar a converter um modelo, é necessário carregá-lo, utilizando uma das seguintes opções:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) - um UI conveniente para carregar/descarregar/gerir ficheiros no armazenamento de blob azul
- [Linha de comando Azure](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Módulo do Azure PowerShell](/powershell/azure/install-az-ps)
  - ver os [scripts Exemplo PowerShell](../../samples/powershell-example-scripts.md)
- [Utilizando um SDK de armazenamento (Python, C# ... )](../../../storage/index.yml)
- [Utilizando as APIs de REPOUSO DE ARMAZENAMENTO Azure](/rest/api/storageservices/blob-service-rest-api)

Para um exemplo de como carregar dados para conversão consulte Conversion.ps1 dos [Scripts exemplo powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Obtenha um SAS URI para o modelo convertido

Este passo é semelhante à [recuperação de SAS para os recipientes de armazenamento.](#retrieve-sas-for-the-storage-containers) No entanto, desta vez é necessário recuperar um SAS URI para o ficheiro do modelo, que foi escrito no recipiente de saída.

Por exemplo, para recuperar um SAS URI através do [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)clique com o botão direito no ficheiro do modelo e selecione "Obter Assinatura de Acesso Partilhado".

É necessária uma Assinatura de Acesso Partilhado (SAS) para carregar modelos se não tiver ligado a sua conta de armazenamento à sua conta de renderização remota Azure. Pode aprender a ligar a sua conta na [Criar uma Conta.](../create-an-account.md#link-storage-accounts)

## <a name="next-steps"></a>Passos seguintes

- [Configurar a conversão do modelo](configure-model-conversion.md)
- [A conversão do modelo REST API](conversion-rest-api.md)
