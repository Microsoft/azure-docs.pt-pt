---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482143"
---
1. Certifique-se de que tem uma [conta Azure com uma subscrição ativa.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

1. Instale [python 2.7+ ou 3.5.3+](https://www.python.org/downloads).

1. Instale o [Azure CLI](/cli/azure/install-azure-cli).

1. Siga as instruções sobre [a autenticação configure para o desenvolvimento local,](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication)com a qual cria um diretor de serviço local e disponibilize-o ao Cliente Azure Key Vault para Python através de variáveis ambientais. 

    Ao executar o código diretamente no Azure, não é necessário um principal de serviço separado se a aplicação utilizar a identidade gerida.

1. Num terminal ou pedido de comando, crie uma pasta de projeto adequada e, em seguida, crie e ative um ambiente virtual Python como descrito em [ambientes virtuais Use Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Instalar a biblioteca de identidade do Diretório Ativo Azure:

    ```terminal
    pip install azure.identity
    ```
