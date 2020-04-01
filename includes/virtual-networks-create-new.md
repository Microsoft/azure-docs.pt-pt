---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244987"
---
## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, criará uma rede virtual e uma sub-rede.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso > Rede > rede Virtual** ou procurar a rede **Virtual** na caixa de pesquisa.

2. Na **Create rede virtual,** introduza ou selecione estas informações no separador **Basics:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | Selecione **Criar novos**, introduza ** \<>de nome de grupo de recursos, **em seguida, selecione OK, ou selecione um>de ** \<grupo de recursos** existente com base em parâmetros. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Introduza>** \<de nome de rede virtual**                                    |
    | Região           | Selecione>** \<de nome de região** |

3. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

4. No separador **Endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereçoI4 | Introduza>** \<de endereço-endereço IPv4** |

5. Sob **o nome Subnet,** selecione a **palavra predefinido**.

6. Na **sub-rede Edit,** introduza esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Introduza>** \<de nome de subnet** |
    | Intervalo de endereços da sub-rede | Introduza>** \<de endereços de sub-rede**

7. Selecione **Guardar**.

8. Selecione o **Review + crie** o separador ou selecione o botão **Rever + criar.**

9. Selecione **Criar**.