---
title: Como definir ou editar políticas de gerenciamento de API do Azure | Microsoft Docs
description: Este tópico mostra como definir ou editar políticas de gerenciamento de API do Azure.
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
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071708"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Como definir ou editar políticas de gerenciamento de API do Azure

A definição de política é um documento XML que descreve uma sequência de instruções de entrada e saída. O XML pode ser editado diretamente na janela de definição. Você também pode selecionar uma política predefinida na lista que é fornecida à direita da janela de política. As instruções aplicáveis ao escopo atual são habilitadas e realçadas. Clicar em uma instrução habilitada adiciona o XML apropriado no local do cursor na exibição de definição. 

Para obter informações detalhadas sobre políticas, consulte [políticas no gerenciamento de API do Azure](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Definir ou editar uma política

Para definir ou editar uma política, siga as seguintes etapas:

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até à sua instância APIM.
3. Clique no separador **APIs**.

    ![Editar política](./media/set-edit-policies/code-editor.png)

4. Selecione uma das APIs que importou anteriormente.
5. Selecione o separador **Design**.
6. Selecione uma operação à qual você deseja aplicar a política. Se você quiser aplicar a política a todas as operações, selecione **todas as operações**.
7. Selecione o **</>** ícone (editor de código) na seção **processamento de entrada** ou processamento de **saída** .
8. Cole o código de política desejado em um dos blocos apropriados.

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
 
## <a name="configure-scope"></a>Configurar escopo

As políticas podem ser configuradas globalmente ou no escopo de um produto, uma API ou uma operação. Para começar a configurar uma política, primeiro você deve selecionar o escopo no qual a política deve ser aplicada.

Os escopos de política são avaliados na seguinte ordem:

1. Escopo global
2. Escopo do produto
3. Escopo da API
4. Escopo da operação

As instruções nas políticas são avaliadas de acordo com o posicionamento `base` do elemento, se estiver presente. A política global não tem nenhuma política pai e `<base>` o uso do elemento não tem nenhum efeito.

Para ver as políticas no escopo atual no editor de políticas, clique em **recalcular a política efetiva para o escopo selecionado**.

### <a name="global-scope"></a>Escopo global

O escopo global é configurado para **todas as APIs** em sua instância do APIM.

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até sua instância do APIM.
2. Clique em **todas as APIs**.

    ![Escopo global](./media/api-management-howto-policies/global-scope.png)

3. Clique no ícone de triângulo.
4. Selecione o **Editor de código**.
5. Adicionar ou editar políticas.
6. Prima **Guardar**. 

    As alterações são propagadas para o gateway de gerenciamento de API imediatamente.

### <a name="product-scope"></a>Escopo do produto

O escopo do produto está configurado para o produto selecionado.

1. Clique em **produtos**.

    ![Escopo do produto](./media/api-management-howto-policies/product-scope.png)

2. Selecione o produto ao qual você deseja aplicar políticas.
3. Clique em **políticas**.
4. Adicionar ou editar políticas.
5. Prima **Guardar**. 

### <a name="api-scope"></a>Escopo da API

O escopo da API está configurado para **todas as operações** da API selecionada.

1. Selecione a **API** à qual você deseja aplicar políticas.

    ![Escopo da API](./media/api-management-howto-policies/api-scope.png)

2. Selecione **Todas as operações**.
3. Clique no ícone de triângulo.
4. Selecione o **Editor de código**.
5. Adicionar ou editar políticas.
6. Prima **Guardar**. 

### <a name="operation-scope"></a>Escopo da operação 

O escopo da operação está configurado para a operação selecionada.

1. Selecione uma **API**.
2. Selecione a operação à qual você deseja aplicar políticas.

    ![Escopo da operação](./media/api-management-howto-policies/operation-scope.png)

3. Clique no ícone de triângulo.
4. Selecione o **Editor de código**.
5. Adicionar ou editar políticas.
6. Prima **Guardar**. 

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes tópicos relacionados:

+ [APIs de transformação](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
