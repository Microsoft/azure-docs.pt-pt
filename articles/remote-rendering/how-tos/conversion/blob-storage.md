---
title: Utilize o Armazenamento Azure Blob para conversão de modelos
description: Descreve passos comuns para configurar e utilizar o armazenamento de blob para conversão de modelos.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681652"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Utilize o Armazenamento Azure Blob para conversão de modelos

O serviço de conversão do [modelo](model-conversion.md) requer acesso ao armazenamento de blob Azure para que possa recuperar dados de entrada e armazenar dados de saída. Este artigo descreve como fazer os passos mais comuns.

## <a name="prepare-azure-storage-accounts"></a>Prepare contas de armazenamento azure

- Criar uma conta de armazenamento (StorageV2)
- Criar um recipiente de entrada blob na conta de armazenamento (por exemplo, denominado "arrinput")
- Criar um recipiente de saída blob na conta de armazenamento (por exemplo, denominado "arroutput")

> [!TIP]
> Para instruções passo a passo sobre como configurar a sua conta de armazenamento, veja o [Quickstart: Converta um modelo para renderização](../../quickstarts/convert-model.md)

A criação da conta de armazenamento e dos recipientes de bolha pode ser feita com uma das seguintes ferramentas:

- [Portal do Azure](https://portal.azure.com)
- [linha de comando az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Explorador do Storage do Azure](https://azure.microsoft.com/features/storage-explorer/)
- SDKs (C#, Python... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Certifique-se de que a Renderização Remota Azure pode aceder à sua conta de armazenamento

O Azure Remote Rending precisa de recuperar os dados do modelo da sua conta de armazenamento e escrever dados de volta à sua conta.

Pode conceder ao Azure Remote Rendering acesso à sua conta de armazenamento nas seguintes duas formas:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Ligue a sua conta de Armazenamento Azure à sua conta de renderização remota Azure

Siga os passos dados na secção [Criar uma Conta.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>Recuperar SAS para os recipientes de armazenamento

As assinaturas de acesso armazenadas (SAS) são utilizadas para conceder acesso à leitura para entrada e escrever acesso para saída. Recomendamos a geração de novos URIs cada vez que um modelo é convertido. Uma vez que os URIs expiram após algum tempo, persistir nelas por uma duração mais longa pode correr o risco de quebrar a sua aplicação inesperadamente.

Os detalhes sobre o SAS podem ser encontrados na [documentação sas](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

Um SAS URI pode ser gerado usando um dos:

- módulo Az PowerShell
  - ver o [exemplo scripts PowerShell](../../samples/powershell-example-scripts.md)
- [linha de comando az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Explorador do Storage do Azure](https://azure.microsoft.com/features/storage-explorer/)
  - clique direito no recipiente "Obter Assinatura de Acesso Partilhado" (ler, listar acesso para recipiente de entrada, escrever acesso para recipiente de saída)
- SDKs (C#, Python... )

Um exemplo de utilização de Assinaturas de Acesso Partilhado na conversão de ativos é mostrado em Conversão.ps1 dos [Scripts exemplo powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Faça upload de um modelo de entrada

Para começar a converter um modelo, é necessário carregá-lo, utilizando uma das seguintes opções:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) - um UI conveniente para carregar/descarregar/gerir ficheiros no armazenamento de blob azure
- [Linha de comando Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - ver os [scripts Exemplo PowerShell](../../samples/powershell-example-scripts.md)
- [Usando um SDK de armazenamento (Python, C# ... )](https://docs.microsoft.com/azure/storage/)
- [Utilização das APIs de REPOUSO DE ARMAZENAMENTO Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Para um exemplo de como carregar dados para conversão consulte Conversão.ps1 dos [Scripts exemplo powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Obtenha um SAS URI para o modelo convertido

Este passo é semelhante ao [de recuperação de SAS para os recipientes](#retrieve-sas-for-the-storage-containers)de armazenamento . No entanto, desta vez é necessário recuperar um SAS URI para o ficheiro modelo, que foi escrito no recipiente de saída.

Por exemplo, para recuperar um SAS URI através do [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)clique à direita no ficheiro modelo e selecione "Obter Assinatura de Acesso Partilhado".

É necessária uma Assinatura de Acesso Partilhado (SAS) para carregar modelos se não tiver ligado a sua conta de armazenamento à sua conta de renderização remota Azure. Pode aprender a ligar a sua conta na [Create a Account](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Passos seguintes

- [Configurar a conversão do modelo](configure-model-conversion.md)
- [A conversão do modelo REST API](conversion-rest-api.md)
