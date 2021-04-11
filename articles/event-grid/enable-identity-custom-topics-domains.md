---
title: Ativar a identidade gerida em tópicos e domínios personalizados da Azure Event Grid
description: Este artigo descreve como permitir a identidade de serviço gerida para um tópico ou domínio personalizado da Azure Event Grid.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278223"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Atribua uma identidade gerida pelo sistema a um tópico ou domínio personalizado da Grade de Eventos 
Este artigo mostra-lhe como ativar uma identidade gerida pelo sistema para um tópico personalizado da Grade de Eventos ou um domínio. Para conhecer as identidades geridas, veja [o que são identidades geridas para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-identity-at-the-time-of-creation"></a>Ativar a identidade no momento da criação

### <a name="using-azure-portal"></a>Com o Portal do Azure
Pode ativar a identidade atribuída ao sistema para um tópico personalizado ou um domínio enquanto o cria no portal Azure. A imagem a seguir mostra como ativar uma identidade gerida pelo sistema para um tópico personalizado. Basicamente, selecione a opção **Ativar** a identidade atribuída no sistema atribuído na página **Avançada** do assistente de criação de tópicos. Você verá esta opção na página **Avançada** do assistente de criação de domínio também. 

![Ativar a identidade ao criar um tópico personalizado](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Também pode utilizar o CLI Azure para criar um tópico ou domínio personalizado com uma identidade atribuída ao sistema. Utilize o `az eventgrid topic create` comando com o parâmetro definido para `--identity` `systemassigned` . Se não especificar um valor para este parâmetro, o valor predefinido `noidentity` é utilizado. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Da mesma forma, pode utilizar o `az eventgrid domain create` comando para criar um domínio com uma identidade gerida pelo sistema.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Ativar a identidade para um tópico ou domínio personalizado existente
Nesta secção, aprende-se a ativar uma identidade gerida pelo sistema para um tópico ou domínio personalizado existente. 

### <a name="using-azure-portal"></a>Com o Portal do Azure
O procedimento que se segue mostra-lhe como ativar a identidade gerida pelo sistema para um tópico personalizado. Os passos para permitir uma identidade para um domínio são semelhantes. 

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Procure **tópicos de grelha de eventos** na barra de pesquisa no topo.
3. Selecione o **tópico personalizado** para o qual deseja ativar a identidade gerida. 
4. Mude para o **separador Identidade.** 
5. Ligue  o interruptor para ativar a identidade. 
1. **Selecione Guarde** na barra de ferramentas para guardar a definição. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Página de identidade para um tópico personalizado"::: 

Pode utilizar passos semelhantes para permitir uma identidade para um domínio de grelha de eventos.

### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Utilize o `az eventgrid topic update` comando com conjunto para permitir a identidade atribuída ao sistema para um tópico personalizado `--identity` `systemassigned` existente. Se quiser desativar a identidade, especifique `noidentity` como o valor. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

O comando para a atualização de um domínio existente é semelhante `az eventgrid domain update` ().


## <a name="next-steps"></a>Passos seguintes
Adicione a identidade a um papel apropriado (por exemplo, Service Bus Data Sender) no destino (por exemplo, uma fila de autocarros de serviço). Para etapas detalhadas, consulte [grant geriu a identidade o acesso ao destino Event Grid](add-identity-roles.md). 
