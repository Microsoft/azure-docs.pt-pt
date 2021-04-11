---
title: Webhooks para responder a ações de registo
description: Aprenda a usar webhooks para desencadear eventos quando as ações de pressão ou de puxar ocorrem nos repositórios de registo.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5374b58ba72727500294a173c26e9a131b29fe34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722250"
---
# <a name="using-azure-container-registry-webhooks"></a>Usando webhooks de registo de contentores Azure

Os registos de contentores do Azure armazenam e gerem imagens de contentores do Docker privadas, de forma semelhante a como o Docker Hub armazena imagens do Docker públicas. Também pode acolher repositórios para [gráficos Helm](container-registry-helm-repos.md) (pré-visualização), um formato de embalagem para implementar aplicações para Kubernetes. Você pode usar webhooks para desencadear eventos quando certas ações ocorrem em um dos seus repositórios de registo. Os webhooks podem responder a eventos ao nível do registo, ou podem ser examinados até uma etiqueta de repositório específica. Com um registo  [geo-replicado,](container-registry-geo-replication.md) configura cada webhook para responder a eventos numa réplica regional específica.

O ponto final de um webhook deve ser acessível ao público a partir do registo. Pode configurar pedidos de webhook de registo para autenticar para um ponto final seguro.

Para obter mais informações sobre os pedidos do webhook, consulte [a referência do esquema do webhook do registo do contentor Azure.](container-registry-webhook-reference.md)

## <a name="prerequisites"></a>Pré-requisitos

* Registo de contentores do Azure - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilize o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md). Os [níveis de serviço de registo de contentores Azure](container-registry-skus.md) têm diferentes quotas de webhooks.
* Docker CLI - para configurar o seu computador local como anfitrião Docker e aceder aos comandos da CLI do Docker, instale o [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Criar webhook - Portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o registo do contentor no qual pretende criar um webhook.
1. Em **Serviços**, selecione **Webhooks**.
1. **Selecione Adicione** na barra de ferramentas webhook.
1. Preencha o formulário *Criar webhook* com as seguintes informações:

| Valor | Descrição |
|---|---|
| Nome Webhook | O nome que quer dar ao webhook. Pode conter apenas letras e números, e deve ter 5-50 caracteres de comprimento. |
| Localização | Para um registo [geo-replicado,](container-registry-geo-replication.md) especifique a região de Azure da réplica do registo. 
| Serviço URI | O URI onde o webhook deve enviar notificações POST. |
| Cabeçalhos personalizados | Cabeçalhos que pretende passar junto com o pedido DOM. Devem estar no formato "chave: valor". |
| Desencadear ações | Ações que desencadeiam o webhook. As ações incluem impulso de imagem, exclusão de imagem, push de gráfico de helm, eliminação de gráfico de leme e quarentena de imagem. Pode escolher uma ou mais ações para desencadear o webhook. |
| Estado | O estado para o webhook depois de ser criado. É ativado por defeito. |
| Âmbito | O âmbito em que funciona o webhook. Se não for especificado, o âmbito é para todos os eventos do registo. Pode ser especificado para um repositório ou uma etiqueta utilizando o formato "repositório:tag", ou "repositório:*" para todas as tags sob um repositório. |

Exemplo webhook formulário:

![Screenshot que mostra a criação de webhook U I no portal Azure.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Criar webhook - Azure CLI

Para criar um webhook utilizando o Azure CLI, utilize o comando [az acr webhook.](/cli/azure/acr/webhook#az-acr-webhook-create) O seguinte comando cria um webhook para todos os eventos de eliminação de imagem no *meu registo de registo:*

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook de teste

### <a name="azure-portal"></a>Portal do Azure

Antes de utilizar o webhook, pode testá-lo com o botão **Ping.** Ping envia um pedido de CORREIO genérico para o ponto final especificado e regista a resposta. A utilização da função de ping pode ajudá-lo a verificar se configura corretamente o webhook.

1. Selecione o webhook que pretende testar.
2. Na barra de ferramentas superior, selecione **Ping**.
3. Verifique a resposta do ponto final na coluna **STATUS HTTP.**

![UI de criação webhook da ACR no portal Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>CLI do Azure

Para testar um webhook ACR com o Azure CLI, utilize o comando [de ping az acr webhook.](/cli/azure/acr/webhook#az-acr-webhook-ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Para ver os resultados, use o comando [az acr webhook list-events.](/cli/azure/acr/webhook)

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Apagar webhook

### <a name="azure-portal"></a>Portal do Azure

Cada webhook pode ser eliminado selecionando o webhook e, em seguida, o botão **Eliminar** no portal Azure.

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Passos seguintes

### <a name="webhook-schema-reference"></a>Referência de esquema webhook

Para obter mais informações sobre o formato e as propriedades das cargas do evento JSON emitidas pelo Registo do Contentor de Azure, consulte a referência do esquema webhook:

[Referência de esquema de webhook do registo de contentores Azure](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Eventos da Grelha de Eventos

Além dos eventos de webhook de registo nativo discutidos neste artigo, o Registo de Contentores Azure pode emitir eventos para a Grade de Eventos:

[Quickstart: Enviar eventos de registo de contentores para a Grade de Eventos](container-registry-event-grid-quickstart.md)
