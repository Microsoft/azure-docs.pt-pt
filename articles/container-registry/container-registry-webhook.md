---
title: Webhooks para responder a ações de registo
description: Aprenda a usar webhooks para desencadear eventos quando as ações de impulso ou de puxar ocorrem nos seus repositórios de registo.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74454371"
---
# <a name="using-azure-container-registry-webhooks"></a>Utilização de webhooks de registo de contentores Azure

Os registos de contentores do Azure armazenam e gerem imagens de contentores do Docker privadas, de forma semelhante a como o Docker Hub armazena imagens do Docker públicas. Também pode acolher repositórios para [gráficos Helm](container-registry-helm-repos.md) (pré-visualização), um formato de embalagem para implementar aplicações para Kubernetes. Você pode usar webhooks para desencadear eventos quando certas ações ocorrem em um dos seus repositórios de registo. Os webhooks podem responder a eventos ao nível do registo, ou podem ser examinados até uma etiqueta de repositório específica. Com um registo [geo-replicado,](container-registry-geo-replication.md) configura cada webhook para responder a eventos numa réplica regional específica.

Para mais informações sobre pedidos de webhook, consulte a referência do esquema do webhook do Registo de [Contentores do Azure](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Pré-requisitos

* Registo de contentores do Azure - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilize o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md). As SKUs do Registo de [Contentores Azure](container-registry-skus.md) têm diferentes quotas de webhooks.
* Docker CLI - para configurar o seu computador local como anfitrião Docker e aceder aos comandos da CLI do Docker, instale o [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Criar webhook - Portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o registo de contentores no qual pretende criar um webhook.
1. Em **Serviços,** selecione **Webhooks**.
1. **Selecione Adicionar** na barra de ferramentas webhook.
1. Preencha o formulário *Create webhook* com as seguintes informações:

| Valor | Descrição |
|---|---|
| Nome webhook | O nome que quer dar ao gancho. Pode conter apenas letras e números, e deve ter 5-50 caracteres de comprimento. |
| Localização | Para um registo [geo-replicado,](container-registry-geo-replication.md) especifique a região azure da réplica do registo. 
| Serviço URI | O URI onde o webhook deve enviar notificações POST. |
| Cabeçalhos personalizados | Os cabeçalhos que pretende passar junto com o pedido do POST. Devem estar no formato "chave: valor". |
| Desencadear ações | Ações que desencadeiam o gancho. As ações incluem impulso de imagem, eliminação de imagem, impulso de gráfico de helm, eliminação do gráfico helme e quarentena de imagem. Pode escolher uma ou mais ações para desencadear o webhook. |
| Estado | O estado do webhook depois de criado. É ativado por defeito. |
| Âmbito | O âmbito em que o webhook funciona. Se não especificado, o âmbito é para todos os eventos no registo. Pode ser especificado para um repositório ou uma etiqueta utilizando o formato "repositório:tag", ou "repositório:*" para todas as etiquetas sob um repositório. |

Forma de webhook exemplo:

![ACR webhook criação UI no portal Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Criar webhook - Azure CLI

Para criar um webhook utilizando o Azure CLI, utilize o [webhook az acr criar](/cli/azure/acr/webhook#az-acr-webhook-create) comando. O seguinte comando cria um webhook para todas as imagens apagar eventos no *registo de registo sinuoso:*

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Teste webhook

### <a name="azure-portal"></a>Portal do Azure

Antes de utilizar o webhook, pode testá-lo com o botão **Ping.** Ping envia um pedido de POST genérico para o ponto final especificado e regista a resposta. A utilização da função de ping pode ajudá-lo a verificar se configura corretamente o webhook.

1. Selecione o webhook que pretende testar.
2. Na barra de ferramentas superior, selecione **Ping**.
3. Verifique a resposta do ponto final na coluna **HTTP STATUS.**

![ACR webhook criação UI no portal Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>CLI do Azure

Para testar um webhook ACR com o Azure CLI, utilize o comando de [ping webhook az acr.](/cli/azure/acr/webhook#az-acr-webhook-ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Para ver os resultados, utilize o comando de [eventos webhook az acr.](/cli/azure/acr/webhook)

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Eliminar webhook

### <a name="azure-portal"></a>Portal do Azure

Cada webhook pode ser eliminado selecionando o webhook e, em seguida, o botão **Delete** no portal Azure.

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Passos seguintes

### <a name="webhook-schema-reference"></a>Referência do esquema webhook

Para mais informações sobre o formato e as propriedades das cargas do evento JSON emitidas pelo Registo de Contentores Azure, consulte a referência do esquema do webhook:

[Referência do esquema do webhook do registo de contentores azure](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Eventos da Grelha de Eventos

Para além dos eventos de webhook de registo nativo discutidos neste artigo, o Registo de Contentores Azure pode emitir eventos para event grid:

[Quickstart: Envie eventos de registo de contentores para a Grelha de Eventos](container-registry-event-grid-quickstart.md)
