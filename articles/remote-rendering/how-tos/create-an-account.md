---
title: Criar uma conta do Azure Remote Rendering
description: Descreve os passos para criar uma conta para a renderização remota de Azure
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681301"
---
# <a name="create-an-azure-remote-rendering-account"></a>Criar uma conta do Azure Remote Rendering

Este capítulo guia-o através dos passos para criar uma conta para o serviço de **renderização remota Azure.** Uma conta válida é obrigatória para completar qualquer um dos quickstarts ou tutoriais.

## <a name="create-an-account"></a>Criar uma conta

São necessários os seguintes passos para criar uma conta para o serviço de renderização remota Azure:

1. Vá à página de [pré-visualização](https://aka.ms/MixedRealityPrivatePreview) da realidade mista
1. Clique no botão 'Criar um recurso'
1. No campo de pesquisa ("Search the marketplace"), escreva em "Remote Rendering" e bata 'enter'.
1. Na lista de resultados, clique no azulejo "Remote Rendering"
1. No ecrã seguinte, clique no botão "Criar". Um formulário abre-se para criar uma nova conta de renderização remota:
    1. Definir 'Nome de recurso' para o nome da conta
    1. Atualizar 'Subscrição' se necessário
    1. Desloque o 'grupo de recursos' para um grupo de recursos à sua escolha
1. Uma vez criada a conta, navegue para ela e:
    1. No separador *'Visão Geral',* note o 'ID da conta'
    1. No separador *Definições > Access Keys,* note a 'Chave Primária' - esta é a chave da conta secreta da conta

### <a name="retrieve-the-account-information"></a>Recuperar a informação da conta

As amostras e tutoriais requerem que forneça o ID da conta e uma chave. Por exemplo, no ficheiro **arrconfig.json** que é usado para os scripts da amostra PowerShell:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Consulte a [lista das regiões disponíveis](../reference/regions.md) para o preenchimento da opção da *região.*

Os valores para **`arrAccountId`** e **`arrAccountKey`** podem ser encontrados no portal, conforme descrito nos seguintes passos:

* Aceda ao [Portal do Azure](https://www.portal.azure.com)
* Encontre a sua "Conta de **Renderização Remota"** - deve estar na lista **de "Recursos Recentes".** Pode também procurá-lo na barra de pesquisa no topo. Nesse caso, certifique-se de que a subscrição que pretende utilizar é selecionada no filtro de subscrição Predefinido (ícone de filtro ao lado da barra de pesquisa):

![Filtro de assinatura](./media/azure-subscription-filter.png)

Clicar na sua conta leva-o a este ecrã, que mostra imediatamente o ID da **Conta:**

![ID da conta Azure](./media/azure-account-id.png)

Para a tecla, selecione **Access Keys** no painel à esquerda. A página seguinte mostra uma chave primária e secundária:

![Chaves de acesso Azure](./media/azure-account-primary-key.png)

O valor **`arrAccountKey`** para pode ser a chave primária ou secundária.

## <a name="link-storage-accounts"></a>Contade armazenamento de ligação

Este parágrafo explica como ligar as contas de armazenamento à sua conta de Renderização Remota. Quando uma conta de armazenamento está ligada não é necessário gerar um SAS URI sempre que pretende interagir com os dados da sua conta, por exemplo, ao carregar um modelo. Em vez disso, pode utilizar os nomes da conta de armazenamento diretamente conforme descrito na [secção de carregamento de um modelo](../concepts/models.md#loading-models).

Os passos deste parágrafo devem ser executados para cada conta de armazenamento que deve utilizar este método de acesso alternativo. Se ainda não criou contas de armazenamento, pode percorrer o respetivo passo na [conversão de um modelo para renderização rápida](../quickstarts/convert-model.md#storage-account-creation).

Agora presume-se que tem uma conta de armazenamento. Navegue para a conta de armazenamento no portal e aceda ao separador Controlo de **Acesso (IAM)** para essa conta de armazenamento:

![Conta de armazenamento IAM](./media/azure-storage-account.png)

 Certifique-se de que tem permissões do proprietário sobre esta conta de armazenamento para garantir que pode adicionar atribuições de funções. Se não tiver acesso, a opção **adicionar uma função** de atribuição será desativada.

 É necessário adicionar três funções distintas, como descrito nos próximos passos. Se não fornecer os três níveis de acesso, haverá problemas de permissão enquanto tenta aceder à conta de armazenamento.

 Clique no botão **Adicionar** no azulejo "Adicionar uma função" para adicionar a primeira função:

![Conta de armazenamento IAM](./media/azure-add-role-assignment.png)

* O primeiro papel a atribuir é **proprietário** como mostrado na imagem acima. 
* Selecione **Conta de Renderização Remota** a partir do * Atribuir acesso**ao** dropdown.
* Selecione a sua conta de subscrição e renderização remota nas últimas descidas.

Repita a adição de novos papéis mais duas vezes para as respetivas seleções do abandono do **papel:**
* **Contribuidor de Conta de Armazenamento**
* **Contribuinte de dados blob de armazenamento**

As outras descidas são selecionadas como no primeiro passo.

Se tiver adicionado as três funções, a sua conta de Renderização Remota Azure tem acesso à sua conta de armazenamento utilizando identidades de serviço geridas.

## <a name="next-steps"></a>Passos seguintes

* [Autenticação](authentication.md)
* [Utilização das APIs Frontend Azure para autenticação](frontend-apis.md)
* [Scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md)
