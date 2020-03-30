---
title: Como definir ou editar políticas de Gestão da API Azure [ Microsoft Docs
description: Este tópico mostra como definir ou editar políticas de Gestão De API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 2df57477ae5270405a1774b7a4f04ed185fea396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70071708"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Como definir ou editar as políticas de Gestão de API do Azure

A definição de política é um documento XML que descreve uma sequência de declarações de entrada e saída. O XML pode ser editado diretamente na janela de definição. Pode também selecionar uma política predefinida da lista que é fornecida à direita da janela de política. As declarações aplicáveis ao âmbito atual estão ativadas e destacadas. Clicar numa declaração ativada adiciona o XML apropriado na localização do cursor na vista de definição. 

Para obter informações detalhadas sobre políticas, consulte [Políticas na Gestão da API azure.](api-management-howto-policies.md)

## <a name="set-or-edit-a-policy"></a>Definir ou editar uma política

Para definir ou editar uma política, siga os seguintes passos:

1. Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .
2. Navegue até à sua instância APIM.
3. Clique no separador **APIs**.

    ![Editar política](./media/set-edit-policies/code-editor.png)

4. Selecione uma das APIs que importou anteriormente.
5. Selecione o separador **Design**.
6. Selecione uma operação à qual pretende aplicar a apólice. Se pretender aplicar a apólice a todas as operações, selecione **Todas as operações**.
7. Selecione o **</>** ícone (editor de código) na secção de **processamento** de entrada ou de **saída.**
8. Colar o código político desejado num dos blocos apropriados.

    ```XML
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>Configurar o âmbito

As políticas podem ser configuradas globalmente ou no âmbito de um Produto, API ou Operação. Para começar a configurar uma apólice, primeiro deve selecionar o âmbito a que a política deve ser aplicada.

Os âmbitos de política são avaliados na seguinte ordem:

1. Âmbito global
2. Âmbito do produto
3. Âmbito da API
4. Âmbito de funcionamento

As declarações dentro das políticas são `base` avaliadas de acordo com a colocação do elemento, se estiver presente. A política global não tem `<base>` política parental e a utilização do elemento não tem qualquer efeito.

Para ver as políticas no âmbito atual no editor de política, clique em **Recalcular uma política eficaz para o âmbito selecionado**.

### <a name="global-scope"></a>Âmbito global

O âmbito global está configurado para **todas as APIs** na sua instância APIM.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue para a sua instância APIM.
2. Clique em **todos os APIs**.

    ![Âmbito global](./media/api-management-howto-policies/global-scope.png)

3. Clique no ícone do triângulo.
4. Selecione o **Editor de código**.
5. Adicionar ou editar políticas.
6. Prima **Guardar**. 

    As alterações são imediatamente propagadas à porta de entrada da API Management.

### <a name="product-scope"></a>Âmbito do produto

O âmbito do produto está configurado para o produto selecionado.

1. Clique em **Produtos**.

    ![Âmbito do produto](./media/api-management-howto-policies/product-scope.png)

2. Selecione o produto ao qual pretende aplicar políticas.
3. Clique em **Políticas**.
4. Adicionar ou editar políticas.
5. Prima **Guardar**. 

### <a name="api-scope"></a>Âmbito da API

O âmbito DaPi está configurado para **todas as operações** da API selecionada.

1. Selecione a **API** a que pretende aplicar políticas.

    ![Âmbito da API](./media/api-management-howto-policies/api-scope.png)

2. Selecione **Todas as operações**.
3. Clique no ícone do triângulo.
4. Selecione o **Editor de código**.
5. Adicionar ou editar políticas.
6. Prima **Guardar**. 

### <a name="operation-scope"></a>Âmbito de funcionamento 

O âmbito de funcionamento está configurado para o funcionamento selecionado.

1. Selecione um **API**.
2. Selecione a operação a que pretende aplicar políticas.

    ![Âmbito de funcionamento](./media/api-management-howto-policies/operation-scope.png)

3. Clique no ícone do triângulo.
4. Selecione o **Editor de código**.
5. Adicionar ou editar políticas.
6. Prima **Guardar**. 

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes tópicos relacionados:

+ [Transforme APIs](transform-api.md)
+ [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
+ [Amostras políticas](policy-samples.md)
