---
title: 'CLI: Carregar e ligar o TLS/SSL cert a uma aplicação'
description: Saiba como utilizar o Azure CLI para automatizar a implementação e gestão da sua aplicação De Serviço de Aplicações. Esta amostra mostra como ligar um certificado TLS/SSL personalizado a uma aplicação.
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 1d03018cca9757c8a6ddddbff96738a407bba7d4
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006400"
---
# <a name="bind-a-custom-tlsssl-certificate-to-an-app-service-app-using-cli"></a>Ligue um certificado TLS/SSL personalizado a uma aplicação de Serviço de Aplicações utilizando o CLI

Este script de amostra cria uma aplicação no Serviço de Aplicações com os seus recursos relacionados, em seguida, liga o certificado TLS/SSL de um nome de domínio personalizado ao mesmo. Neste exemplo, precisa de:

* Acesso à página de configuração de DNS da sua entidade de registo de domínios.
* Um válido . Ficheiro PFX e a sua palavra-passe para o certificado TLS/SSL que pretende carregar e ligar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Cria uma aplicação de Serviço de Aplicações. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname#az-webapp-config-hostname-add) | Mapeia um domínio personalizado para uma aplicação de Serviço de Aplicações. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl#az-webapp-config-ssl-upload) | Envia um certificado TLS/SSL para uma aplicação de Serviço de Aplicações. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl#az-webapp-config-ssl-bind) | Liga um certificado TLS/SSL carregado a uma aplicação de Serviço de Aplicações. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).
