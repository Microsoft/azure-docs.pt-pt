---
title: Azure AD Connect transformações de provisão de nuvem
description: Este artigo descreve como usar transformações para alterar os mapeamentos de atributos padrão.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: a15a25ce4fbe1a43c2a5065bf88f5c40c5865721
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85360695"
---
# <a name="transformations"></a>Transformações

Com uma transformação, pode alterar o comportamento padrão de como um atributo é sincronizado com o Azure Ative Directory (Azure AD) utilizando o fornecimento em nuvem.

Para fazer esta tarefa, você precisa editar o esquema e, em seguida, reenviar-lo através de um pedido web.

Para obter mais informações sobre atributos de provisão de nuvem, consulte [compreender o esquema AD Azure](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Recupere o esquema
Para recuperar o esquema, siga os passos em [Ver o esquema](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapeamento de atributo personalizado
Para adicionar um mapeamento de atributos personalizados, siga estes passos.

1. Copie o esquema num texto ou editor de código, como [o Código do Estúdio Visual.](https://code.visualstudio.com/)
1. Localize o objeto que pretende atualizar no esquema.

   ![Objeto no esquema](media/how-to-transformation/transform1.png)</br>
1. Localize o código para `ExtensionAttribute3` debaixo do objeto do utilizador.

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
1. Editar o código de modo a que o atributo da empresa seja mapeado para `ExtensionAttribute3` .

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
 1. Copie o esquema de volta para o Graph Explorer, altere o **Tipo de Pedido** para **PUT**, e selecione A consulta **de execução**.

    ![Executar Consulta](media/how-to-transformation/transform2.png)

 1. Agora, no portal Azure, vá para a configuração de provisionamento em nuvem e selecione **Reatamento**de Provisionamento .

    ![Reiniciar o provisionamento](media/how-to-transformation/transform3.png)

 1. Após algum tempo, verifique se os atributos estão a ser preenchidos executando a seguinte consulta no Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` .
 1. Agora deve ver o valor.

    ![O valor aparece](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapeamento de atributo personalizado com função
Para um mapeamento mais avançado, pode utilizar funções que lhe permitem manipular os dados e criar valores para atributos que se adequam às necessidades da sua organização.

Para fazer esta tarefa, siga os passos anteriores e, em seguida, edite a função que é usada para construir o valor final.

Para obter informações sobre a sintaxe e exemplos de expressões, consulte expressões de [escrita para mapeamentos de atributos no Azure Ative Directory](reference-expressions.md).


## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
