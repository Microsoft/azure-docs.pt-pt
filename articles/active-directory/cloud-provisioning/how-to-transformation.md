---
title: Azure AD Connect transformações de fornecimento de nuvens
description: Este artigo descreve como usar transformações para alterar os mapeamentos de atributos padrão.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75549300"
---
# <a name="transformations"></a>Transformações

Com uma transformação, pode alterar o comportamento padrão de como um atributo é sincronizado com o Azure Ative Directory (Azure AD) utilizando o fornecimento de nuvem.

Para fazer esta tarefa, é necessário editar o esquema e, em seguida, reenvie-o através de um pedido web.

Para obter mais informações sobre os atributos de fornecimento de nuvens, consulte [Understanding the Azure AD schema](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Recupere o esquema
Para recuperar o esquema, siga os passos em [Ver o esquema](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapeamento personalizado de atributos
Para adicionar um mapeamento personalizado de atributos, siga estes passos.

1. Copie o esquema num texto ou editor de código, como [visual studio code](https://code.visualstudio.com/).
1. Localize o objeto que pretende atualizar no esquema.

   ![Objeto no esquema](media/how-to-transformation/transform1.png)</br>
1. Localize o `ExtensionAttribute3` código para debaixo do objeto do utilizador.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Editar o código para que o atributo `ExtensionAttribute3`da empresa seja mapeado para .

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Copie o esquema de volta ao Graph Explorer, altere o Tipo de **Pedido** para **PUT**, e selecione's **Run Query**.

    ![Executar Consulta](media/how-to-transformation/transform2.png)

 1. Agora, no portal Azure, vá à configuração de fornecimento de nuvem e selecione **o fornecimento de Reiniciar**.

    ![Reinício do fornecimento](media/how-to-transformation/transform3.png)

 1. Depois de um pouco de tempo, verifique se os atributos `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`estão a ser preenchidos executando a seguinte consulta no Graph Explorer: .
 1. Agora devia ver o valor.

    ![O valor aparece](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapeamento de atributo personalizado com função
Para mapeamento mais avançado, pode utilizar funções que lhe permitam manipular os dados e criar valores para atributos que se adequam às necessidades da sua organização.

Para fazer esta tarefa, siga os passos anteriores e, em seguida, edite a função que é usada para construir o valor final.

Para obter informações sobre a sintaxe e exemplos de expressões, consulte [expressões escritas para mapeamento de atributos no Diretório Ativo Azure](reference-expressions.md).


## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
