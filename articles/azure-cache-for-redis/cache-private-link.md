---
title: Cache Azure para Redis com Link Privado Azure (Pré-visualização)
description: Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura ao Azure Cache para Redis alimentado por Azure Private Link. Neste artigo, você aprenderá a criar um Azure Cache, uma Rede Virtual Azure e um Ponto Final Privado usando o portal Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 932d138a4b594aa51b73c365cc3e753f49f886f6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328999"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Cache Azure para Redis com Link Privado Azure (Visualização pública)
Neste artigo, você vai aprender a criar uma rede virtual e um Azure Cache para o caso Redis com um ponto final privado usando o portal Azure. Você também vai aprender a adicionar um ponto final privado a um Azure Cache existente para o exemplo de Redis.

Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura ao Azure Cache para Redis alimentado por Azure Private Link. 

## <a name="prerequisites"></a>Pré-requisitos
* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
> Esta funcionalidade encontra-se atualmente em pré-visualização pública para regiões limitadas. Se não tiver a opção de criar um ponto final privado, [contacte-nos](mailto:azurecache@microsoft.com). Para usar pontos finais privados, o seu Azure Cache para a instância Redis precisa de ter sido criado após 28 de julho de 2020.
>
> Regiões com acesso público de pré-visualização atualmente: West Central US, North Central US, West US, East US 2, Noruega East, Europe North, Asia East, Japan East e India Central.
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Crie um ponto final privado com um novo Azure Cache para a instância Redis 

Nesta secção, você vai criar um novo Azure Cache para o caso Redis com um ponto final privado.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual 

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecione Criar um recurso.":::

2. Na página **Nova,** selecione **Networking** e, em seguida, selecione **Rede Virtual**.

3. **Selecione Adicionar** para criar uma rede virtual.

4. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar esta rede virtual. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar a sua rede virtual e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Nome** | Introduza um nome de rede virtual. | O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado, e pode conter apenas letras, números, sublinhados, períodos ou hífenes. | 
   | **Região** | Desça e selecione uma região. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão a sua rede virtual. |

5. Selecione o separador **endereços IP** ou clique no botão **Seguinte: Endereços IP** na parte inferior da página.

6. No separador **endereços IP,** especifique o **espaço de endereço IPv4** como um ou mais prefixos de endereço na notação CIDR (por exemplo, 192.168.1.0/24).

7. No **nome da sub-rede,** clique no **padrão** para editar as propriedades da sub-rede.

8. No painel **de sub-redes Editar,** especifique um **nome de sub-rede,** bem como o **intervalo de endereços Sub-rede**. O intervalo de endereços da sub-rede deve estar na notação CIDR (por exemplo, 192.168.1.0/24). Deve ser contido no espaço de endereço da rede virtual.

9. Selecione **Guardar**.

10. Selecione o **separador 'Rever +' ou** clicar no botão **'Rever +' criar.**

11. Verifique se todas as informações estão corretas e clique em **Criar** para obter a rede virtual.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Crie uma Cache Azure para o caso Redis com um ponto final privado
Para criar uma instância de cache, siga estes passos.

1. Volte para a página inicial do portal Azure ou abra o menu da barra lateral e, em seguida, selecione **Criar um recurso**. 
   
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

1. Selecione o **separador 'Rede'** ou clique no botão **'Rede'** na parte inferior da página.

1. No **separador 'Rede',** selecione **Private Endpoint** para o método de conectividade.

1. Clique no botão **Adicionar** para criar o seu ponto final privado.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Em networking, adicione um ponto final privado.":::

1. Na página **de ponto final privado,** configufique as definições para o seu ponto final privado com a rede virtual e a sub-rede que criou na última secção e selecione **OK**. 

1. Selecione o **Seguinte: Separador avançado** ou clique no **seguinte: Botão avançado** na parte inferior da página.

1. No separador **Avançado** para uma instância de cache básica ou padrão, selecione o interruptor de ativação se pretender ativar uma porta não TLS.

1. No separador **Avançado** para a instância de cache premium, configurar as definições para a porta não-TLS, clustering e persistência de dados.


1. Selecione o **separador Seguinte: Tags** ou clique no botão **Seguinte: Tags** na parte inferior da página.

1. Opcionalmente, no separador **Tags, insira** o nome e o valor se desejar categorizar o recurso. 

1. Selecione **Review + criar**. É levado para o separador 'Rever +' onde o Azure valida a sua configuração.

1. Depois de aparecer a mensagem de validação verde, selecione **Criar**.

Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.**   Quando **o Estado**aparece como    **Running,** a cache está pronta a ser utilizada. 
    

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Crie um ponto final privado com uma cache Azure existente para a instância Redis 

Nesta secção, você adicionará um ponto final privado a um Azure Cache existente para a instância Redis. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual 
Para criar uma rede virtual, siga estes passos.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso**.

2. Na página **Nova,** selecione **Networking** e, em seguida, selecione **Rede Virtual**.

3. **Selecione Adicionar** para criar uma rede virtual.

4. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar esta rede virtual. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar a sua rede virtual e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Nome** | Introduza um nome de rede virtual. | O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado, e pode conter apenas letras, números, sublinhados, períodos ou hífenes. | 
   | **Região** | Desça e selecione uma região. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão a sua rede virtual. |

5. Selecione o separador **endereços IP** ou clique no botão **Seguinte: Endereços IP** na parte inferior da página.

6. No separador **endereços IP,** especifique o **espaço de endereço IPv4** como um ou mais prefixos de endereço na notação CIDR (por exemplo, 192.168.1.0/24).

7. No **nome da sub-rede,** clique no **padrão** para editar as propriedades da sub-rede.

8. No painel **de sub-redes Editar,** especifique um **nome de sub-rede,** bem como o **intervalo de endereços Sub-rede**. O intervalo de endereços da sub-rede deve estar na notação CIDR (por exemplo, 192.168.1.0/24). Deve ser contido no espaço de endereço da rede virtual.

9. Selecione **Guardar**.

10. Selecione o **separador 'Rever +' ou** clicar no botão **'Rever +' criar.**

11. Verifique se todas as informações estão corretas e clique em **Criar** para obter a rede virtual.

### <a name="create-a-private-endpoint"></a>Criar um ponto final privado 

Para criar um ponto final privado, siga estes passos.

1. No portal Azure, procure **a Cache Azure para Redis** e prima para entrar ou selecioná-la a partir das sugestões de pesquisa.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Procure por Azure Cache para Redis.":::

2. Selecione a instância de cache a que pretende adicionar um ponto final privado.

3. No lado esquerdo do ecrã, selecione **(PREVIEW) Private Endpoint**.

4. Clique no botão **Private Endpoint** para criar o seu ponto final privado.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Adicione o ponto final privado.":::

5. Na **página 'Criar' privado,** configure as definições para o seu ponto final privado.

   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este ponto final privado. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu ponto final privado e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Nome** | Insira um nome de ponto final privado. | O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado, e pode conter apenas letras, números, sublinhados, períodos ou hífenes. | 
   | **Região** | Desça e selecione uma região. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu ponto final privado. |

6. Clique no **seguinte: Botão de recurso** na parte inferior da página.

7. No separador **Recursos,** selecione a sua subscrição, escolha o tipo de recurso como `Microsoft.Cache/Redis` , e, em seguida, selecione a cache a que pretende ligar o ponto final privado.

8. Clique no **seguinte: Botão de configuração** na parte inferior da página.

9. No **separador Configuração,** selecione a rede virtual e a sub-rede criada na secção anterior.

10. Clique no botão **Seguinte: Tags** na parte inferior da página.

11. Opcionalmente, no separador **Tags, insira** o nome e o valor se desejar categorizar o recurso.

12. Selecione **Review + criar**. É levado para o **separador 'Rever +' onde**o   Azure valida a sua configuração.

13. Depois de aparecer a mensagem **de validação** verde, selecione **Criar**.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Private Link, consulte a documentação do [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview). 

