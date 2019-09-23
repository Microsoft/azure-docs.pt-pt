---
title: WebHooks do registro de contêiner do Azure
description: Saiba como usar WebHooks para disparar eventos quando determinadas ações ocorrem em seus repositórios de registro.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/24/2019
ms.author: danlep
ms.openlocfilehash: 59e8d4979e7be02d6097e1c3eccc44e64da87e95
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311592"
---
# <a name="using-azure-container-registry-webhooks"></a>Usando WebHooks do registro de contêiner do Azure

Um registro de contêiner do Azure armazena e gerencia imagens de contêiner privadas do Docker, de forma semelhante ao modo como o Docker Hub armazena imagens públicas do Docker. Ele também pode hospedar repositórios para [gráficos Helm](container-registry-helm-repos.md) (visualização), um formato de empacotamento para implantar aplicativos no kubernetes. Você pode usar WebHooks para disparar eventos quando determinadas ações ocorrerem em um de seus repositórios de registro. WebHooks podem responder a eventos no nível do registro ou podem ter o escopo reduzido para uma marca de repositório específica. Com um [](container-registry-geo-replication.md) registro replicado geograficamente, você configura cada webhook para responder a eventos em uma réplica regional específica.

Para obter detalhes sobre solicitações de webhook, consulte [referência de esquema de webhook do registro de contêiner do Azure](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Pré-requisitos

* Registro de contêiner do Azure-crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md). As [SKUs do registro de contêiner do Azure](container-registry-skus.md) têm cotas de webhook diferentes.
* CLI do Docker – para configurar seu computador local como um host do Docker e acessar os comandos da CLI do Docker, instale o [mecanismo](https://docs.docker.com/engine/installation/)do Docker.

## <a name="create-webhook---azure-portal"></a>Criar webhook-portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até o registro de contêiner no qual você deseja criar um webhook.
1. Em **Serviços**, selecione **WebHooks**.
1. Selecione **Adicionar** na barra de ferramentas do webhook.
1. Conclua o formulário *criar webhook* com as seguintes informações:

| Value | Descrição |
|---|---|
| Nome do webhook | O nome que você deseja dar ao webhook. Ele pode conter apenas letras e números e deve ter de 5-50 caracteres de comprimento. |
| Location | Para um [](container-registry-geo-replication.md) registro replicado geograficamente, especifique a região do Azure da réplica de registro. 
| URI do serviço | O URI em que o webhook deve enviar notificações POST. |
| Cabeçalhos personalizados | Cabeçalhos que você deseja passar junto com a solicitação POST. Eles devem estar no formato "chave: valor". |
| Ações de gatilho | Ações que disparam o webhook. As ações incluem envio por push de imagem, exclusão de imagem, push de gráfico de Helm, exclusão de gráfico de Helm e quarentena de imagem. Você pode escolher uma ou mais ações para disparar o webhook. |
| Estado | O status do webhook após sua criação. Ele é habilitado por padrão. |
| Scope | O escopo no qual o webhook funciona. Se não for especificado, o escopo será para todos os eventos no registro. Ele pode ser especificado para um repositório ou uma marca usando o formato "Repository: tag" ou "Repository: *" para todas as marcas em um repositório. |

Formulário de webhook de exemplo:

![Interface do usuário de criação de webhook do ACR no portal do Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Criar webhook-CLI do Azure

Para criar um webhook usando o CLI do Azure, use o comando [AZ ACR webhook Create](/cli/azure/acr/webhook#az-acr-webhook-create) . O comando a seguir cria um webhook para todos os eventos de exclusão de imagem no registro *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testar webhook

### <a name="azure-portal"></a>Portal do Azure

Antes de usar o webhook, você pode testá-lo com o botão **ping** . O ping envia uma solicitação POST genérica para o ponto de extremidade especificado e registra a resposta. O uso do recurso ping pode ajudá-lo a verificar se você configurou corretamente o webhook.

1. Selecione o webhook que você deseja testar.
2. Na barra de ferramentas superior, selecione **ping**.
3. Verifique a resposta do ponto de extremidade na coluna **status http** .

![Interface do usuário de criação de webhook do ACR no portal do Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>CLI do Azure

Para testar um webhook ACR com o CLI do Azure, use o comando [AZ ACR webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) .

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Para ver os resultados, use o comando [AZ ACR webhook List-Events](/cli/azure/acr/webhook) .

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Excluir webhook

### <a name="azure-portal"></a>Portal do Azure

Cada webhook pode ser excluído selecionando-se o webhook e, em seguida, o botão **excluir** na portal do Azure.

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Passos Seguintes

### <a name="webhook-schema-reference"></a>Referência de esquema de webhook

Para obter detalhes sobre o formato e as propriedades dos conteúdos de evento JSON emitidos pelo registro de contêiner do Azure, consulte a referência de esquema do webhook:

[Referência de esquema de webhook do registro de contêiner do Azure](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Eventos da grade de eventos

Além dos eventos de webhook do registro nativo discutidos neste artigo, o registro de contêiner do Azure pode emitir eventos para a grade de eventos:

[Quickstart: Enviar eventos de registro de contêiner para a grade de eventos](container-registry-event-grid-quickstart.md)
