---
title: Como definir ou editar as políticas de gestão de API do Azure | Documentos da Microsoft
description: Este tópico mostra como definir ou editar as políticas de gestão de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 3d1847b6001ef8e32f00a4e1cd9728d5ca0662f8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205303"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Como definir ou editar as políticas de gestão de API do Azure

A definição de política é um documento XML que descreve uma seqüência de instruções de entrada e saídas. O XML pode ser editado diretamente na janela de definição. Também pode selecionar uma política predefinida na lista que é fornecida para a direita da janela de política. As instruções aplicáveis ao âmbito atual forem ativadas e realçadas. Clicar numa instrução ativada adiciona o XML adequado no local do cursor na vista de definição. 

Para obter informações detalhadas sobre as políticas, consulte [políticas de gestão de API do Azure](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Definir ou editar uma política

Para definir ou editar uma política, siga os passos seguintes:

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até à sua instância APIM.
3. Clique no separador **APIs**.

    ![Editar política](./media/set-edit-policies/code-editor.png)

4. Selecione uma das APIs que importou anteriormente.
5. Selecione o separador **Design**.
6. Selecione uma operação para o qual pretende aplicar a política. Se pretender aplicar a política a todas as operações, selecione **todas as operações**.
7. Selecione o **</>** ícone (editor de código) no **processamento de entrada** ou **processamento de saída** secção.
8. Cole o código de diretiva desejado em um dos blocos de apropriado.
         
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
 
## <a name="configure-scope"></a>Configurar o âmbito

Políticas podem ser configuradas de forma global ou no âmbito de um produto, API ou operação. Para começar a configurar uma política, primeiro tem de selecionar o âmbito em que deve ser aplicada a política.

Âmbitos de política são avaliados na seguinte ordem:

1. Âmbito global
2. Âmbito do produto
3. Âmbito de API
4. Âmbito da operação

As instruções dentro as políticas são avaliadas de acordo com a colocação do `base` elemento, se estiver presente. Política global não tem elemento principal de política e utilizar o `<base>` elemento no mesmo não tem qualquer efeito.

Para ver as políticas no âmbito atual no editor de políticas, clique em **recalcular política em vigor para âmbito selecionado**.

### <a name="global-scope"></a>Âmbito global

Escopo global está configurado para **todas as APIs** na sua instância APIM.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) e navegue para a instância APIM.
2. Clique em **todas as APIs**.

    ![Âmbito global](./media/api-management-howto-policies/global-scope.png)

3. Clique no ícone do triângulo.
4. Selecione o **Editor de código**.
5. Adicionar ou editar as políticas.
6. Prima **Guardar**. 

    As alterações sejam propagadas para o gateway de gestão de API imediatamente.

### <a name="product-scope"></a>Âmbito do produto

Âmbito do produto está configurado para o produto selecionado.

1. Clique em **produtos**.

    ![Âmbito do produto](./media/api-management-howto-policies/product-scope.png)

2. Selecione o produto ao qual pretende aplicar as políticas.
3. Clique em **políticas**.
4. Adicionar ou editar as políticas.
5. Prima **Guardar**. 

### <a name="api-scope"></a>Âmbito de API

Âmbito de API está configurado para **todas as operações** da API selecionada.

1. Selecione o **API** que pretende aplicar políticas para.

    ![Âmbito de API](./media/api-management-howto-policies/api-scope.png)

2. Selecione **Todas as operações**.
3. Clique no ícone do triângulo.
4. Selecione o **Editor de código**.
5. Adicionar ou editar as políticas.
6. Prima **Guardar**. 

### <a name="operation-scope"></a>Âmbito da operação 

Âmbito da operação está configurado para a operação selecionada.

1. Selecione um **API**.
2. Selecione a operação que pretende aplicar políticas para.

    ![Âmbito da operação](./media/api-management-howto-policies/operation-scope.png)

3. Clique no ícone do triângulo.
4. Selecione o **Editor de código**.
5. Adicionar ou editar as políticas.
6. Prima **Guardar**. 

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes tópicos relacionados:

+ [Transforme as APIs](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações
+ [Exemplos de política](policy-samples.md)