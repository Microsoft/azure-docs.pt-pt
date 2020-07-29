---
title: Cache Azure para Redis com Link Privado Azure (Pré-visualização)
description: Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura ao Azure Cache para Redis alimentado por Azure Private Link. Neste artigo, você vai aprender a criar um Azure Cache, uma rede virtual Azure, e um Private Endpoint usando o portal Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: d85fe36bb948ae9a0c81fa25f87450c7f5fe93b7
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337267"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Cache Azure para Redis com Link Privado Azure (Pré-visualização)
Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura ao Azure Cache para Redis alimentado por Azure Private Link. 

Neste artigo, você vai aprender a criar um Azure Cache, uma rede virtual Azure, e um Private Endpoint usando o portal Azure.  

## <a name="prerequisites"></a>Pré-requisitos
* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
  > Esta funcionalidade está neste momento em pré-visualização - [contacte-nos](mailto:azurecache@microsoft.com) se estiver interessado.
  >


## <a name="create-a-cache"></a>Criar uma cache
1. Para criar uma cache, inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso.** 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecione Criar um recurso.":::
   
1. Na página **Nova,** selecione **Bases de Dados** e, em seguida, selecione **Azure Cache para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecione Azure Cache para Redis.":::
   
1. Na página **New Redis Cache,** configufique as definições para o seu novo cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será * \<DNS name> .redis.cache.windows.net*. | 
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Localização** | Desça e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu cache. |
   | **Escalão de preço** | Desça e selecione um [nível de preços](https://azure.microsoft.com/pricing/details/cache/). |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [Azure Cache para ver visão geral do Redis](cache-overview.md). |
   
1. Selecione **Criar**. 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Crie cache Azure para Redis.":::
   
   Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Azure Cache para Redis criado.":::

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Nesta secção, irá criar uma rede virtual e uma sub-rede.

1. Selecione **Criar um recurso**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecione Criar um recurso.":::

2. Na página **Nova,** selecione **Networking** e, em seguida, selecione **Rede Virtual**.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="Criar uma rede virtual.":::

3. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | Desça e selecione a sua subscrição.                                  |
    | Grupo de Recursos   | Desça e selecione um grupo de recursos. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Inserir**\<virtual-network-name>**                                    |
    | Região           | Selecione**\<region-name>** |

4. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

5. No separador **endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Inserir**\<IPv4-address-space>** |

6. No **nome da sub-rede,** selecione a palavra **predefinição**.

7. Na **sub-rede Editar,** insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Inserir**\<subnet-name>** |
    | Intervalo de endereços da sub-rede | Inserir**\<subnet-address-range>**

8. Selecione **Guardar**.

9. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

10. Selecione **Criar**.


## <a name="create-a-private-endpoint"></a>Criar um ponto final privado 

Nesta secção, irá criar um ponto final privado e conectá-lo à cache que criou anteriormente.

1. Procure **por Private Link** e prima para entrar ou selecioná-lo a partir das sugestões de pesquisa.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Procure um link privado.":::

2. No lado esquerdo do ecrã, selecione **Pontos finais privados**.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="Selecione link privado.":::

3. Selecione o botão **+ Adicionar** para criar o seu ponto final privado. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="Adicione um link privado.":::

4. Na **página 'Criar' privado,** configure as definições para o seu ponto final privado.

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Desça e selecione a sua subscrição. |
    | Grupo de recursos | Desça e selecione um grupo de recursos. |
    | **DETALHES DE INSTÂNCIA** |  |
    | Nome |Insira um nome para o seu ponto final privado.  |
    | Região |Desça e selecione um local. |
    |||

5. Selecione o **seguinte: Botão de recurso** na parte inferior da página.

6. No separador **Recursos,** selecione a sua subscrição, escolha o tipo de recurso como Microsoft.Cache/Redis e, em seguida, selecione a cache que escoou na secção anterior.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="Recursos para ligação privada.":::

7. Selecione o **seguinte: Botão de configuração** na parte inferior da página.

8. No **separador Configuração,** selecione a rede virtual e a sub-rede criada na secção anterior.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="Configuração para ligação privada.":::

9. Selecione o **seguinte: Botão tags** na parte inferior da página.

10. No separador **Tags, insira** o nome e o valor se desejar categorizar o recurso. Este passo é opcional.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="Etiquetas para ligação privada.":::

11. Selecione **Review + criar**. É levado para o **separador 'Rever +' onde**o   Azure valida a sua configuração.

12. Assim que aparecer a mensagem **verde da validação,** selecione **Criar**.


## <a name="next-steps"></a>Próximos Passos

Para saber mais sobre o Private Link, consulte a [documentação do Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview). 

