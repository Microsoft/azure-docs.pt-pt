---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520806"
---
### <a name="create-the-cluster"></a>Criar o cluster

Siga o tutorial para [criar um cluster Azure Red Hat OpenShift](../tutorial-create-cluster.md). Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão 2.6.0 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

### <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Azure Red Hat OpenShift, utilize [oc,](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)o cliente da linha de comando OpenShift.

> [!NOTE]
> Recomendamos que [instale a linha de comando OpenShift](../tutorial-connect-cluster.md) no [Azure Cloud Shell](https://shell.azure.com/) e a utilize para todas as operações da linha de comando abaixo. Lance a sua concha a partir de shell.azure.com ou clicando no link:
>
> [![Lançamento incorporado](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com/bash)

Siga o tutorial para instalar o CLI, recupere as credenciais de cluster e [ligue-se ao cluster](../tutorial-connect-cluster.md) utilizando a consola web e o CLI OpenShift.

Assim que iniciar sessão, deve ver uma mensagem a dizer que está a usar o `default` projeto.

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
