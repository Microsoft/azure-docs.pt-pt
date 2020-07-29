---
title: Referências Azure CLI para Azure Data Share
description: Página de aterragem de referência do Azure CLI para Azure Data Share
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298483"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure CLI para partilha de dados Azure

A Interface da Linha de Comando[Azure (Azure CLI)](/cli/azure/what-is-azure-cli)é um conjunto de comandos utilizados para criar e gerir recursos Azure.  Está disponível em muitos serviços da Azure, incluindo a Azure Data Share.  Existem mais de 65 comandos diferentes para partilha de dados!  Estes comandos dão-lhe a capacidade de trabalhar eficazmente com o serviço a partir de uma linha de comando.

## <a name="references-for-data-share"></a>Referências para partilha de dados

Todos os comandos Azure CLI para Azure Data Share são atualmente extensões ao Azure CLI.  Uma extensão dá-lhe acesso a comandos experimentais e pré-lançamento.  Saiba mais sobre as referências de extensão de extensão de [utilização com Azure CLI](/cli/azure/azure-cli-extensions-overview).

|Referência Azure CLI |Descrição
|-|-|-|
| [az datashare](/cli/azure/ext/datashare/datashare) | Todos os comandos para gerir a Azure Data Share.
| [conta de datashare az](/cli/azure/ext/datashare/datashare/account) | Comandos para gerir as contas Azure Data Share.
| [consumidor de datashare az](/cli/azure/ext/datashare/datashare/consumer) | Comandos para os consumidores gerirem a Azure Data Share.
| [conjunto de dados az datashare](/cli/azure/ext/datashare/datashare/dataset) | Comandos para os fornecedores gerirem conjuntos de dados Azure Data Share.
| [convite de datashare az](/cli/azure/ext/datashare/datashare/invitation) | Comandos para os consumidores gerirem os convites da Azure Data Share.
| [az datashare fornecedor-share-subscrição](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Comandos para os fornecedores gerirem as subscrições do Azure Data Share.
| [sincronização de datashare az](/cli/azure/ext/datashare/datashare/synchronization)  | Comandos para gerir a sincronização do Azure Data Share.
| [az datashare sincronização-definição](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Comandos para os fornecedores gerirem as definições de sincronização do Azure Data Share.

## <a name="reference-examples"></a>Exemplos de referência

São fornecidos exemplos com todas as referências Azure CLI. Embora também possa completar estas tarefas através do portal Azure, a utilização do CLI Azure requer uma única linha de comando.  Aqui estão alguns blocos de código para lhe dar uma ideia de como é fácil usar o Azure CLI.

Para trabalhar com o Azure Data Share, primeiro precisará de um grupo de recursos.  Os grupos de recursos Azure são simples de criar e gerir com o Azure CLI.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

É tão simples criar uma conta de partilha de dados.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>Veja também

* [Começa com o Azure CLI](/cli/azure/get-started-with-azure-cli) para aprender sobre a instalação e iniciar súm.

* Descubra referências adicionais [de núcleo](/cli/azure/reference-index) e [extensão](/cli/azure/azure-cli-extensions-list) na documentação do Azure CLI.
