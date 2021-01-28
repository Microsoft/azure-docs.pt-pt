---
title: Ativar uma identidade gerida para eventos de encaminhamento (pré-visualização)
titleSuffix: Azure Digital Twins
description: Veja como ativar uma identidade atribuída ao sistema para a Azure Digital Twins e use-a para encaminhar eventos.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7de8e1eb1fd5311059bfb35d22b679a8c1f5ba9d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948879"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview"></a>Ativar uma identidade gerida para encaminhamento de eventos Azure Digital Twins (pré-visualização)

Este artigo descreve como permitir uma identidade atribuída ao [sistema para uma instância Azure Digital Twins](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (atualmente em pré-visualização), e usar a identidade ao encaminhar eventos para destinos apoiados como [Event Hub,](../event-hubs/event-hubs-about.md) [Destinos de Autocarros](../service-bus-messaging/service-bus-messaging-overview.md)de Serviço   e Contentor de Armazenamento [Azure.](../storage/blobs/storage-blobs-introduction.md)

Aqui estão os passos que estão cobertos neste artigo: 

1. Crie uma instância Azure Digital Twins com uma identidade atribuída ao sistema ou permita a identidade atribuída ao sistema numa instância Azure Digital Twins existente. 
1. Adicione um papel ou papéis adequados à identidade. Por exemplo, atribua o papel **de remetente de dados do Azure Event Hub** à identidade se o ponto final for o Event Hub ou o papel de **Remetente de dados de autocarros do serviço Azure** se o ponto final for o Service Bus.
1. Crie um ponto final em Azure Digital Twins que seja capaz de usar identidades atribuídas ao sistema para autenticação.

## <a name="enable-system-managed-identities-for-an-instance"></a>Permitir identidades geridas pelo sistema, por exemplo 

Quando ativa uma identidade atribuída ao sistema na sua instância Azure Digital Twins, a Azure cria automaticamente uma identidade para ela no [Azure Ative Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Essa identidade pode então ser usada para autenticar os pontos finais da Azure Digital Twins para reencaminhamento de eventos.

Pode ativar identidades geridas pelo sistema para uma instância Azure Digital Twins **como parte da configuração inicial do caso**, ou **capacitá-la mais tarde num caso que já existe**.

Qualquer um destes métodos de criação dará as mesmas opções de configuração e o mesmo resultado final para o seu exemplo. Esta secção descreve como fazer as duas coisas.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Adicione uma identidade gerida pelo sistema durante a criação de exemplos

Nesta secção, você vai aprender como ativar uma identidade gerida pelo sistema em um caso Azure Digital Twins que está atualmente a ser criado. Esta secção centra-se na etapa de identidade gerida do processo de criação; para uma passagem completa da criação de um novo exemplo de Azure Digital Twins, consulte [*Como-a- Configurar um exemplo e autenticação*](how-to-set-up-instance-portal.md).

A opção de identidade gerida pelo sistema está localizada na configuração do separador **avançado** de instância.

Neste separador, selecione a opção **On** para **a identidade gerida** do Sistema para ligar esta funcionalidade.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Screenshot do portal Azure mostrando o separador Avançado do diálogo Criar Recursos para Gémeos Digitais Azure. Há um destaque em torno do nome do separador, a opção On para identidade gerida do Sistema, e os botões de navegação (Revisão + criar, Anterior, Seguinte: Avançado).":::

Em seguida, pode utilizar os botões de navegação inferiores para continuar com o resto da configuração do caso.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Adicione uma identidade gerida pelo sistema a uma instância existente

Nesta secção, utilizará o [portal Azure](https://portal.azure.com) para adicionar uma identidade gerida pelo sistema a uma instância Azure Digital Twins que já existe.

1. Primeiro, navegue até ao [portal Azure](https://portal.azure.com) num browser.

1. Procure o nome do seu caso na barra de pesquisa do portal e selecione-o para ver os seus dados.

1. Selecione **Identidade (pré-visualização)** no menu da esquerda.

1. Nesta página, selecione a opção **On** para ligar esta função.

1. Acerte no botão **Guardar,** e **Sim** para confirmar.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Screenshot do portal Azure mostrando a página Identidade (pré-visualização) para uma instância Azure Digital Twins. Há um destaque em torno do nome da página no menu de exemplos Azure Digital Twins, a opção On para Status, o botão Guardar e o botão de confirmação Sim.":::

Após a alteração ser guardada, aparecerão mais campos nesta página para o **ID** e **Permissões** de Objeto da nova identidade.

Pode copiar o **ID** do objeto a partir daqui, se necessário, e usar o botão **Permissões** para visualizar as funções Azure que são atribuídas à identidade. Para definir alguns papéis, continue para a próxima secção.

## <a name="assign-azure-roles-to-the-identity"></a>Atribuir funções de Azure à identidade 

Uma vez criada uma identidade atribuída ao sistema para a sua instância Azure Digital Twins, terá de atribuí-la a funções apropriadas para autenticar com diferentes tipos de [pontos finais](concepts-route-events.md) para encaminhar eventos para destinos apoiados. Esta secção descreve as opções de função e como atribuí-las à identidade atribuída pelo sistema.

>[!NOTE]
> Este é um passo importante - sem ele, a identidade não será capaz de aceder aos seus pontos finais e os eventos não serão entregues.

### <a name="supported-destinations-and-azure-roles"></a>Destinos apoiados e funções de Azure 

Aqui estão as funções mínimas que uma identidade precisa para aceder a um ponto final, dependendo do tipo de destino. As funções com permissões mais elevadas (como funções de Data Owner) também funcionarão.

| Destino | Função do Azure |
| --- | --- |
| Azure Event Hubs | Ender de dados do Azure Event Hubs |
| Service Bus do Azure | Remetente de dados de ônibus de serviço Azure |
| Recipiente de armazenamento Azure | Contribuinte de Dados do Armazenamento de Blobs |

Para mais informações sobre pontos finais, rotas e tipos de destinos suportados para encaminhamento em Azure Digital Twins, consulte [*Concepts: Rotas de eventos*](concepts-route-events.md).

### <a name="assign-the-role"></a>Atribuir o papel

>[!NOTE]
> Esta secção deve ser completada por um utilizador da Azure com permissões para gerir o acesso do utilizador aos recursos Azure (incluindo a concessão e delegação de permissões). As funções comuns que satisfazem este requisito são *Proprietário,* *Administrador de Conta,* ou a combinação de Administrador de Acesso ao *Utilizador* e *Colaborador.* Para obter mais informações sobre os requisitos de permissão para as funções de Azure Digital Twins, consulte [*Como-a-fazer: Configurar instância e autenticação*](how-to-set-up-instance-portal.md#prerequisites-permission-requirements).

Para atribuir um papel à identidade, comece por abrir o [portal Azure](https://portal.azure.com).

1. Navegue para o seu recurso de ponto final (o seu centro de eventos, tópico de Service Bus ou recipiente de armazenamento) procurando o seu nome na barra de pesquisa do portal. 
1. Selecione **o controlo de acesso (IAM)** no menu da esquerda.
1. Selecione o botão **+ Adicionar** para adicionar uma nova atribuição de função.

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Screenshot do portal Azure mostrando a página de Controlo de Acesso (IAM) para um centro de eventos. O botão + Adicionar é realçado." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. Na seguinte página **de atribuição de funções adicionar,** preencha os valores:
    * **Função**: Selecione o papel desejado do menu suspenso
    * **Atribuir acesso a**: Escolha **utilizador, grupo ou principal de serviço**
    * **Selecione**: Aqui, você irá selecionar a identidade gerida da sua instância Azure Digital Twins que está a ser atribuída ao papel. O nome da identidade gerida corresponde ao nome do caso, por isso procure o nome da sua instância Azure Digital Twins. Quando selecionar o resultado, a identidade, por exemplo, aparecerá na secção **de membros Selecionados.**

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="Preenchendo os campos listados no diálogo &quot;Adicionar função&quot;":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Quando terminar de introduzir os detalhes, **selecione Save**.

## <a name="create-an-endpoint-with-identity-based-authorization"></a>Criar um ponto final com autorização baseada na identidade

Depois de configurar uma identidade gerida pelo sistema para a sua instância Azure Digital Twins e atribuindo-lhe o papel apropriado, pode criar [pontos finais](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) Azure Digital Twins que sejam capazes de usar a identidade para autenticação. Esta opção só está disponível para pontos finais do tipo Event Hub e Service Bus (não é suportado para a Grelha de Eventos).

>[!NOTE]
> Não é possível editar um ponto final que já tenha sido criado com identidade baseada em chaves para mudar para a autenticação baseada na identidade. Deve escolher o tipo de autenticação quando o ponto final for criado pela primeira vez.

Siga as [instruções para criar um ponto final Azure Digital Twins](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

Quando chegar ao passo de completar os detalhes necessários para o seu tipo de ponto final, **certifique-se de** selecionar a identidade para o tipo de autenticação.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Screenshot de criar um ponto final do tipo Event Hub." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Termine de configurar o seu ponto final e **selecione Save**.

## <a name="considerations-for-disabling-system-managed-identities"></a>Considerações para desativar identidades geridas pelo sistema

Como uma identidade é gerida separadamente dos pontos finais que a utilizam, é importante considerar os efeitos que quaisquer alterações à identidade ou seus papéis podem ter nos pontos finais na sua instância Azure Digital Twins. Se a identidade for desativada, ou se um papel necessário para um ponto final for removido dela, o ponto final pode tornar-se inacessível e o fluxo de eventos será interrompido.

Para continuar a utilizar um ponto final que foi configurado com uma identidade gerida que foi agora desativada, terá de eliminar o ponto final e [reutivá-lo](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) com um tipo de autenticação diferente. Pode levar até uma hora para os eventos retomarem a entrega no ponto final após esta alteração.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre identidades geridas em Azure AD: 
* [*Identidades geridas para os recursos do Azure*](../active-directory/managed-identities-azure-resources/overview.md)