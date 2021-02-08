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
ms.openlocfilehash: e1d4d29f8edca87ec1cca0ffced7b3e1bca90717
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808544"
---
## <a name="create-the-virtual-network-and-subnet"></a>Criar a rede virtual e a sub-rede

Nesta secção, irá criar uma rede virtual e uma sub-rede.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | **Selecione Criar novo,** **\<resource-group-name>** insira, em seguida, selecione OK ou selecione um existente **\<resource-group-name>** com base em parâmetros. |
    | **Detalhes da instância** |                                                                 |
    | Name             | Inserir **\<virtual-network-name>**                                    |
    | Region           | Selecione **\<region-name>** |

3. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

4. No separador **endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Inserir **\<IPv4-address-space>** |

5. No **nome da sub-rede,** selecione a palavra **predefinição**.

6. Na **sub-rede Editar,** insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Inserir **\<subnet-name>** |
    | Intervalo de endereços da sub-rede | Inserir **\<subnet-address-range>**

7. Selecione **Guardar**.

8. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

9. Selecione **Criar**.