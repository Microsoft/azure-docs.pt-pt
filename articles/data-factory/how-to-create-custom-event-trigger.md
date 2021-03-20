---
title: Criar gatilhos de eventos personalizados na Azure Data Factory
description: Saiba como criar um gatilho personalizado na Azure Data Factory que executa um pipeline em resposta a um evento personalizado publicado na Event Grid.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: e372808250b1d5b21cd5f6c5226922d5d6ad0f77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609696"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Crie um gatilho que executa um pipeline em resposta a um evento personalizado (Preview)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve os Gatilhos de Eventos Personalizados que pode criar nos seus oleodutos data factory.

A arquitetura orientada para eventos (EDA) é um padrão comum de integração de dados que envolve produção, deteção, consumo e reação a eventos. Os cenários de integração de dados exigem frequentemente que os clientes da Data Factory desencadeiem oleodutos com base em determinados eventos que ocorram. A integração nativa da Data Factory com [a Azure Event Grid](https://azure.microsoft.com/services/event-grid/) abrange agora [eventos personalizados](../event-grid/custom-topics.md): os clientes enviam eventos arbitrários para um tópico de grelha de eventos, e a Data Factory subscreve e ouve o tópico e desencadeia os oleodutos em conformidade.

> [!NOTE]
> A integração descrita neste artigo depende da [Grelha de Eventos Azure.](https://azure.microsoft.com/services/event-grid/) Certifique-se de que a sua subscrição está registada no fornecedor de recursos da Grade de Eventos. Para obter mais informações, consulte [fornecedores e tipos de recursos.](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) Você deve ser capaz de fazer a ação *Microsoft.EventGrid/eventSubscriptions/** Esta ação faz parte do papel integrado do Colaborador de Subscrição de Eventos Da EventGrid.

Além disso, combinando parâmetros de pipeline e Custom Event Trigger, os clientes podem analisar e referenciar a carga de _dados_ personalizadas em cursos de pipeline. o campo de _dados_ na carga útil do Evento Personalizado é uma estrutura de valor-chave json de forma livre, dando aos clientes o máximo controlo sobre as corridas de gasodutos conduzidos pelo evento.

> [!IMPORTANT]
> De vez em quando, uma chave referenciada na parametrização pode estar em falta na carga útil de eventos personalizados. O _gatilho_ falhará com um erro, afirmando que a expressão não pode ser avaliada porque _a chave de propriedadeName_ não existe. __Nenhum__ _gasoduto_ será desencadeado pelo evento.

## <a name="setup-event-grid-custom-topic"></a>Tópico personalizado da grelha de eventos de configuração

Para utilizar o Trigger de Evento Personalizado na Fábrica de Dados, tem de configurar _primeiro_ um [tópico personalizado na grelha de eventos.](../event-grid/custom-topics.md) O fluxo de trabalho é diferente do Detonador de Eventos de Armazenamento, onde a Data Factory irá definir o tópico para si. Aqui você precisa navegar na Grelha de Eventos Azure e criar o tópico por si mesmo. Para obter mais informações sobre como criar o tópico personalizado, consulte [tutoriais do Portal](../event-grid/custom-topics.md#azure-portal-tutorials) da Grelha de Eventos Azure e [Tutoriais CLI](../event-grid/custom-topics.md#azure-cli-tutorials)

As Fábricas de Dados esperam que os eventos sigam [o esquema de eventos da Grade de Eventos.](../event-grid/event-schema.md) Certifique-se de que as cargas de eventos têm campos seguintes.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>IU do Data Factory

Esta secção mostra-lhe como criar um gatilho de evento de armazenamento dentro da Interface de Utilizador da Fábrica de Dados Azure.

1. Mude para o **separador Editar,** mostrado com um símbolo de lápis.

1. Selecione **Trigger** no menu e, em seguida, selecione **Novo/Editar**.

1. Na página **'Adicionar Gatilhos',** **selecione Escolha o gatilho...** e, em seguida, selecione **+New**.

1. Selecione **eventos personalizados** do tipo gatilho

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Screenshot da página author para criar um novo gatilho de evento personalizado na UI da Data Factory." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Selecione o seu tópico personalizado a partir do dropdown de subscrição do Azure ou introduza manualmente o âmbito do tópico do evento.

   > [!NOTE]
   > Para criar um novo ou modificar um Gatilho de Evento Personalizado existente, a conta Azure utilizada para iniciar sessão na Data Factory e publicar o gatilho do evento de armazenamento deve ter uma permissão adequada de controlo de acesso baseado em funções (Azure RBAC) sobre o tópico. Não é necessária qualquer autorização adicional: O diretor de serviço da Azure Data Factory _não_ necessita de autorização especial para a Grelha de Eventos. Para obter mais informações sobre o controlo de acesso, consulte a secção [de controlo de acesso baseada em](#role-based-access-control) funções.

1. O **Assunto começa** e o Assunto termina **com** propriedades que lhe permitem filtrar eventos para os quais pretende acionar o gasoduto. Ambas as propriedades são opcionais.

1. Utilizar **+ Novos** tipos de **eventos** que pretende filtrar. Evento personalizado desencadeia uma relação de OR para a lista: se um evento personalizado tiver uma propriedade _eventType_ que corresponda a qualquer um listado aqui, irá desencadear uma corrida de pipeline. O tipo de evento é caso insensível. Por exemplo, na imagem abaixo, o gatilho corresponde a todos os eventos _copiados_ ou _copiados_ com o sujeito começa com _fábricas_

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Screenshot da página Editar Trigger para explicar tipos de eventos e filtragem de assuntos na UI da Fábrica de Dados.":::

1. O gatilho de eventos personalizados pode analisar e enviar carga personalizada de _dados_ para o seu pipeline. Primeiro crie os parâmetros do pipeline e preencha os valores na página **Parâmetros.** Use o formato **@triggerBody ().event.data._nome chave_** para analisar a carga útil de dados, e passar valores para parâmetros de pipeline. Para obter uma explicação detalhada, consulte [metadados de gatilho de referência em pipelines](how-to-use-trigger-parameterization.md) e [variáveis de sistema no trigger de evento personalizado](control-flow-system-variables.md#custom-event-trigger-scope)

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Screenshot da definição de parâmetros do gasoduto.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Screenshot da página parâmetros para fazer referência à carga útil de dados em evento personalizado.":::

1. Clique **em OK** assim que terminar.

## <a name="json-schema"></a>Esquema JSON

A tabela a seguir fornece uma visão geral dos elementos de esquema que estão relacionados com os gatilhos personalizados do evento:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores Permitidos** | **Obrigatório** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **âmbito** | O ID de recursos do Azure Resource Manager do tópico da grelha de eventos. | String | ID do gestor de recursos Azure | Yes |
| **eventos** | O tipo de eventos que causam este gatilho a disparar. | Matriz de cordas    |  | Sim, pelo menos um valor é esperado |
| **assuntoScom** | O campo de objetos deve começar com o padrão previsto para o gatilho disparar. Por exemplo, `factories` apenas dispara o gatilho para o sujeito do evento a começar `factories` por . | String   | | No |
| **AssuntoEndsWith** | O campo de objetos deve terminar com o padrão previsto para o gatilho disparar. | String   | | No |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

A Azure Data Factory utiliza o controlo de acesso baseado em funções Azure (Azure RBAC) para garantir que o acesso não autorizado para ouvir, subscrever atualizações e acionar os oleodutos ligados a eventos personalizados, são estritamente proibidos.

* Para criar com sucesso um novo ou atualizar um Gatilho de Eventos Personalizados existente, a conta Azure assinada na Data Factory necessita de ter acesso adequado à conta de armazenamento relevante. Caso contrário, a operação com falha com _acesso negado_.
* A Data Factory não necessita de nenhuma permissão especial para a sua Grelha de Eventos, e _não_ precisa de atribuir autorização especial do Azure RBAC ao diretor de serviço da Data Factory para a operação.

Especificamente, o cliente precisa do _Microsoft.EventGrid/EventSubscriptions/Escrever_ permissão em _/subscrições/##/resourceGroups//#/providers/Microsoft.EventGrid/topics/someTopics_

## <a name="next-steps"></a>Passos seguintes

* Para obter informações detalhadas sobre os gatilhos, consulte [a execução do Pipeline e os gatilhos](concepts-pipeline-execution-triggers.md#trigger-execution).
* Saiba como fazer referência aos metadados do gatilho no pipeline, ver [Metadados de Gatilho de Referência em Pipeline Runs](how-to-use-trigger-parameterization.md)
