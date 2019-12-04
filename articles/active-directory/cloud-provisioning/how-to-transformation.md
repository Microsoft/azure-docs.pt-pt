---
title: Azure AD Connect transformações de provisionamento de nuvem
description: Este documento descreve como usar transformações para alterar os mapeamentos de atributo padrão.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794474"
---
# <a name="transformations"></a>Transformações

Uma transformação permite que você altere o comportamento padrão de como um atributo é sincronizado com o Azure AD usando o provisionamento de nuvem.  

Para executar essa tarefa, você precisa editar o esquema e reenviá-lo por meio de uma solicitação da Web.

Para obter mais informações sobre os atributos de provisionamento de nuvem, consulte [noções básicas sobre o esquema do Azure ad](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Recuperar o esquema
Para recuperar o esquema, use as etapas descritas em [exibindo o esquema](concept-attributes.md#viewing-the-schema). 


## <a name="custom-attribute-mapping"></a>Mapeamento de atributo personalizado
Para adicionar um mapeamento de atributo personalizado, use o procedimento a seguir.

1. Copie o esquema em um editor de texto ou de código, como [Visual Studio Code](https://code.visualstudio.com/).  
2. Localize o objeto que você deseja atualizar no esquema ![](media/how-to-transformation/transform1.png)</br>
3. Localize o código para **ExtensionAttribute3** no objeto user.

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
 4.  Edite o código para que o atributo Company seja mapeado para ExtensionAttribute3.
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
 5. Copie o esquema de volta para o explorador do Graph, altere o tipo de solicitação para PUT e **Execute a consulta**.  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  Agora, na portal do Azure, navegue até a configuração de provisionamento de nuvem e **reinicie o provisionamento**.
 ![](media/how-to-transformation/transform3.png)</br>
 7.  Após alguns instantes, verifique se os atributos estão sendo populados executando a seguinte consulta no Gerenciador de gráficos: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 8.  Agora você deve ver o valor.
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>Mapeamento de atributo personalizado com função
Para um mapeamento mais avançado, você pode usar funções que permitirão que você manipule os dados e crie valores para o atributo de acordo com as necessidades de suas organizações.

Para executar essa tarefa, basta seguir as etapas acima e editar a função que é usada para construir o valor final.

Para obter informações sobre a sintaxe e exemplos de expressões, consulte [escrevendo expressões para mapeamentos de atributo no Azure Active Directory](reference-expressions.md)


## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
