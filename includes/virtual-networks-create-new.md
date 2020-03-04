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
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244987"
---
## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, criará uma rede virtual e uma sub-rede.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso > Networking > Rede virtual** ou procurar rede **Virtual** na caixa de pesquisa.

2. Na **Create rede virtual,** introduza ou selecione estas informações no separador **Basics:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | Selecione a sua subscrição Azure                                  |
    | Grupo de Recursos   | Selecione **Criar novos**, insira **\<nome de grupo de recursos>** em seguida, selecione OK, ou selecione um\<nome de grupo de **recursos existente>** com base em parâmetros. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Introduza **\<nome de rede virtual>**                                    |
    | Região           | Selecione **\<nome da região>** |

3. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

4. No separador **Endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereçoI4 | **Insira\<IPv4-address-space>** |

5. Sob **o nome Subnet,** selecione a **palavra predefinido**.

6. Na **sub-rede Edit,** introduza esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **\<sub-nome>** |
    | Intervalo de endereços da sub-rede | Introduza **\<subnet-address-range>**

7. Selecione **Guardar**.

8. Selecione o **Review + crie** o separador ou selecione o botão **Rever + criar.**

9. Selecione **Criar**.