---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013139"
---
O método DefaultAzureCredential na nossa aplicação baseia-se em três variáveis ambientais: `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET` , e `AZURE_TENANT_ID` . Dedreta estas variáveis aos valores clienteId, clienteSecret e tenantId que foram devolvidos no passo "Criar um principal de serviço" usando o `export VARNAME=VALUE` formato. (Este método apenas define as variáveis para a sua concha atual e processos criados a partir da concha; para adicionar permanentemente estas variáveis ao seu ambiente, editar o seu `/etc/environment ` ficheiro.) 

Também terá de guardar o nome do cofre como uma variável ambiental chamada `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
