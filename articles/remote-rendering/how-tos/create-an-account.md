---
title: Criar uma conta do Azure Remote Rendering
description: Descreve os passos para criar uma conta para renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fb321aeeae1f0ef5aaadce22e228024895176143
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202655"
---
# <a name="create-an-azure-remote-rendering-account"></a>Criar uma conta do Azure Remote Rendering

Este capítulo guia-o através dos passos para criar uma conta para o serviço **de renderização remota Azure.** Uma conta válida é obrigatória para o preenchimento de qualquer um dos quickstarts ou tutoriais.

## <a name="create-an-account"></a>Criar uma conta

São necessários os seguintes passos para criar uma conta para o serviço de renderização remota Azure:

1. Aceda à [página de pré-visualização da realidade mista](https://aka.ms/MixedRealityPrivatePreview)
1. Clique no botão 'Criar um recurso'
1. No campo de pesquisa ("Procurar no mercado"), digite em "Renderização Remota" e acerte 'entrar'.
1. Na lista de resultados, clique no azulejo "Renderização Remota"
1. No ecrã seguinte, clique no botão "Criar". Abre-se um formulário para criar uma nova conta de renderização remota:
    1. Definir 'Nome de Recurso' para o nome da conta
    1. Atualizar 'Subscrição' se necessário
    1. Desaprote o 'grupo de recursos' para um grupo de recursos à sua escolha
    1. Selecione uma região a partir do dropdown 'Location' onde este recurso deve ser criado. Consulte as observações nas [regiões de conta](create-an-account.md#account-regions) abaixo.
1. Assim que a conta for criada, navegue para ela e:
    1. No *separador 'Visão Geral',* note o 'ID da conta'
    1. No *separador 'Definições > Chaves de Acesso',* note a 'chave principal' - esta é a chave secreta da conta da conta

### <a name="account-regions"></a>Regiões de conta
A localização especificada durante o tempo de criação de conta de uma conta determina a região a que o recurso de conta é atribuído. Isto não pode ser alterado após a criação. No entanto, a conta pode ser usada para se ligar a uma sessão de Renderização Remota em qualquer [região suportada](./../reference/regions.md), independentemente da localização da conta.

### <a name="retrieve-the-account-information"></a>Recuperar a informação da conta

As amostras e tutoriais requerem que forneça a identificação da conta e uma chave. Por exemplo, no **arrconfig.jsno** ficheiro que é usado para os scripts da amostra PowerShell:

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

Consulte a [lista das regiões disponíveis](../reference/regions.md) para preencher a opção *região.*

Os valores para **`arrAccountId`** e **`arrAccountKey`** podem ser encontrados no portal conforme descrito nos seguintes passos:

* Aceda ao [Portal do Azure](https://www.portal.azure.com)
* Encontre a sua **"Conta de Renderização Remota"** - deve estar na lista **de "Recursos Recentes".** Também pode pesquisar na barra de pesquisa no topo. Nesse caso, certifique-se de que a subscrição que pretende utilizar está selecionada no filtro de subscrição Predefinido (ícone de filtro ao lado da barra de pesquisa):

![Filtro de assinatura](./media/azure-subscription-filter.png)

Clicar na sua conta leva-o a este ecrã, que mostra imediatamente o **ID da Conta:**

![ID da conta Azure](./media/azure-account-id.png)

Para a tecla, selecione **Teclas de acesso** no painel à esquerda. A página seguinte mostra uma chave primária e secundária:

![Chaves de acesso Azure](./media/azure-account-primary-key.png)

O valor para **`arrAccountKey`** pode ser a chave primária ou secundária.

## <a name="link-storage-accounts"></a>Contas de armazenamento de ligação

Este parágrafo explica como ligar as contas de armazenamento à sua conta de Renderização Remota. Quando uma conta de armazenamento está ligada não é necessário gerar um SAS URI sempre que pretende interagir com os dados na sua conta, por exemplo, ao carregar um modelo. Em vez disso, pode utilizar os nomes da conta de armazenamento diretamente como descrito na [secção de carregamento de um modelo](../concepts/models.md#loading-models).

Os passos deste parágrafo devem ser realizados para cada conta de armazenamento que deve utilizar este método de acesso alternativo. Se ainda não criou contas de armazenamento, pode percorrer o respetivo passo na [conversão de um modelo para renderização rápida](../quickstarts/convert-model.md#storage-account-creation).

Agora presume-se que tem uma conta de armazenamento. Navegue para a conta de armazenamento no portal e vá ao **separador Controlo de Acesso (IAM)** para essa conta de armazenamento:

![Conta de armazenamento IAM](./media/azure-storage-account.png)

 Certifique-se de que tem permissões do proprietário sobre esta conta de armazenamento para garantir que pode adicionar atribuições de funções. Se não tiver acesso, a opção Adicionar uma tarefa de **função** será desativada.

 É necessário adicionar três papéis distintos, como descrito nos próximos passos. Se não fornecer os três níveis de acesso, haverá problemas de permissão enquanto tenta aceder à conta de armazenamento.

 Clique no botão **Adicionar** no azulejo "Adicionar uma tarefa de função" para adicionar a primeira função:

![IAM de conta de armazenamento adicionar atribuição de função](./media/azure-add-role-assignment.png)

* A primeira função a atribuir é **o Proprietário,** como mostrado na imagem acima.
* Selecione **Conta de Renderização Remota** a partir do acesso de Atribuição **ao** dropdown.
* Selecione a sua conta de subscrição e renderização remota nas últimas desistências.

> [!WARNING]
> Caso a sua conta de renderização remota não esteja listada, consulte esta [secção de resolução de problemas](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account).

Repita a adição de novas funções mais duas vezes para as respetivas seleções da queda do **papel:**

* **Contribuidor de Conta de Armazenamento**
* **Contribuinte de Dados do Armazenamento de Blobs**

As outras descidas são selecionadas como no primeiro passo.

Se adicionou as três funções, a sua conta de Renderização Remota Azure tem acesso à sua conta de armazenamento utilizando identidades de serviço gerido atribuídos pelo sistema.
> [!IMPORTANT]
> As atribuições de funções Azure são armazenadas pelo Azure Storage, pelo que pode haver um atraso de até 30 minutos entre quando concede acesso à sua conta de renderização remota e quando pode ser usada para aceder à sua conta de armazenamento. Consulte a documentação de [controlo de acesso à base da função](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

* [Autenticação](authentication.md)
* [Utilização das APIs frontendas Azure para autenticação](frontend-apis.md)
* [Scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md)