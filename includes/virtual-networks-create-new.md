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
ms.openlocfilehash: 43ede7f3340f190f9eb37ca90fb74f2215db9062
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227384"
---
## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, criará uma rede virtual e uma sub-rede.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso > Networking > Rede virtual** ou procurar rede **Virtual** na caixa de pesquisa.

2. Na **Create rede virtual,** introduza ou selecione estas informações no separador **Basics:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | Selecione a sua subscrição Azure                                  |
    | Grupo de Recursos   | Selecione **Criar novos**, introduza **\<nome de grupo de recursos>** em seguida, selecione OK. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Introduza **\<nome de rede virtual>**                                    |
    | Região           | Selecione **\<nome da região>** |

3. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

4. No separador **Endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereçoI4 | Insira\<espaço de **endereçoIPv4>** |

5. Sob **o nome Subnet,** selecione a **palavra predefinido**.

6. Na **sub-rede Edit,** introduza esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **\<sub-nome>** |
    | Intervalo de endereços da sub-rede | Introduza **\<subnet-address-range>**

7. Selecione **Guardar**.

8. Selecione o **Review + crie** o separador ou selecione o botão **Rever + criar.**

9. Selecione **Criar**.