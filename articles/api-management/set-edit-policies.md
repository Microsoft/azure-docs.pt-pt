---
title: Como definir ou editar políticas de Gestão API da Azure Microsoft Docs
description: Saiba como definir ou editar políticas de Gestão API da Azure. Estas políticas são documentos XML que descrevem uma sequência de declarações de entrada e saída.
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
ms.openlocfilehash: a737fada3e019029967bc752aaa0dedc354fa880
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078328"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Como definir ou editar as políticas de Gestão de API do Azure

A definição de política é um documento XML que descreve uma sequência de declarações de entrada e saída. O XML pode ser editado diretamente na janela de definição. Também pode selecionar uma política predefinida da lista que é fornecida à direita da janela de política. As declarações aplicáveis ao âmbito atual estão ativadas e destacadas. Clicar numa declaração ativa adiciona o XML apropriado na localização do cursor na vista de definição. 

Para obter informações detalhadas sobre políticas, consulte [Políticas na Gestão da API da Azure.](api-management-howto-policies.md)

## <a name="set-or-edit-a-policy"></a>Definir ou editar uma política

Para definir ou editar uma política, siga os seguintes passos:

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até à sua instância APIM.
3. Clique no separador **APIs**.

    ![Editar política](./media/set-edit-policies/code-editor.png)

4. Selecione uma das APIs que importou anteriormente.
5. Selecione o separador **Design**.
6. Selecione uma operação à qual pretende aplicar a apólice. Se pretender aplicar a apólice a todas as operações, selecione **Todas as operações**.
7. Selecione o **</>** ícone (editor de código) na secção **de processamento de entrada** ou **saída.**
8. Cole o código de política desejado num dos blocos apropriados.

    ```xml
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
 
## <a name="configure-scope"></a>Âmbito de configuração

As políticas podem ser configuradas globalmente ou no âmbito de um Produto, API ou Operação. Para começar a configurar uma política, tem primeiro de selecionar o âmbito em que a política deve ser aplicada.

Os âmbitos de política são avaliados na seguinte ordem:

1. Âmbito global
2. Âmbito do produto
3. Âmbito API
4. Âmbito de funcionamento

As declarações dentro das políticas são avaliadas de acordo com a colocação do `base` elemento, se estiver presente. A política global não tem política de pais e a utilização do `<base>` elemento nela não tem efeito.

Para ver as políticas no âmbito atual no editor de política, clique em **Recalcular a política eficaz para o âmbito selecionado**.

### <a name="global-scope"></a>Âmbito global

O âmbito global está configurado para **todas as APIs** no seu caso APIM.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue para a sua instância APIM.
2. Clique **em Todas as APIs**.

    ![Âmbito global](./media/api-management-howto-policies/global-scope.png)

3. Clique no ícone do triângulo.
4. Selecione o **Editor de código**.
5. Adicione ou edite políticas.
6. Prima **Guardar**. 

    As alterações são imediatamente propagadas ao portal de Gestão da API.

### <a name="product-scope"></a>Âmbito do produto

O âmbito do produto está configurado para o produto selecionado.

1. Clique em **Produtos.**

    ![Âmbito do produto](./media/api-management-howto-policies/product-scope.png)

2. Selecione o produto ao qual pretende aplicar políticas.
3. Clique **em Políticas**.
4. Adicione ou edite políticas.
5. Prima **Guardar**. 

### <a name="api-scope"></a>Âmbito API

O âmbito API está configurado para **todas as operações** da API selecionada.

1. Selecione a **API** a que pretende aplicar políticas.

    ![Âmbito API](./media/api-management-howto-policies/api-scope.png)

2. Selecione **Todas as operações**.
3. Clique no ícone do triângulo.
4. Selecione o **Editor de código**.
5. Adicione ou edite políticas.
6. Prima **Guardar**. 

### <a name="operation-scope"></a>Âmbito de funcionamento 

O âmbito de funcionamento está configurado para a operação selecionada.

1. Selecione uma **API**.
2. Selecione a operação a que pretende aplicar políticas.

    ![Âmbito de funcionamento](./media/api-management-howto-policies/operation-scope.png)

3. Clique no ícone do triângulo.
4. Selecione o **Editor de código**.
5. Adicione ou edite políticas.
6. Prima **Guardar**. 

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes tópicos relacionados:

+ [Transformar APIs](transform-api.md)
+ [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
+ [Amostras de política](./policy-reference.md)