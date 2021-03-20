---
title: Cache Azure para Redis com Link Privado Azure (Pré-visualização)
description: Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura ao Azure Cache para Redis alimentado por Azure Private Link. Neste artigo, você aprenderá a criar um Azure Cache, uma Rede Virtual Azure e um Ponto Final Privado usando o portal Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 22bdf93e7236ae5220a6bb7c6ead898628bb51a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007590"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Cache Azure para Redis com Link Privado Azure (Visualização pública)
Neste artigo, você vai aprender a criar uma rede virtual e um Azure Cache para o caso Redis com um ponto final privado usando o portal Azure. Você também vai aprender a adicionar um ponto final privado a um Azure Cache existente para o exemplo de Redis.

Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura ao Azure Cache para Redis alimentado por Azure Private Link. 

## <a name="prerequisites"></a>Pré-requisitos
* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Para usar pontos finais privados, o seu Azure Cache para a instância Redis precisa de ter sido criado após 28 de julho de 2020.
> Atualmente, a geo-replicação, as regras de firewall, o suporte para consolas do portal, vários pontos finais por cache agrupado, persistência na firewall e caches injetados VNet não são suportados. 
>
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
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será *\<DNS name> .redis.cache.windows.net*. | 
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

1. Selecione **Rever + criar**. É levado para o separador 'Rever +' onde o Azure valida a sua configuração.

1. Depois de aparecer a mensagem de validação verde, selecione **Criar**.

Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada. 
    
> [!IMPORTANT]
> 
> Há uma `publicNetworkAccess` bandeira que é por `Disabled` defeito. 
> Esta bandeira destina-se a permitir opcionalmente o acesso ao ponto final público e privado à cache, se estiver definido para `Enabled` . Se estiver `Disabled` definido, só permitirá o acesso ao ponto final privado. Pode definir o valor para `Disabled` ou com o seguinte pedido `Enabled` PATCH. Edite o valor para refletir qual a bandeira que deseja para o seu cache.
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> Para se ligar a uma cache agrupada, `publicNetworkAccess` tem de ser configurada e só pode `Disabled` haver uma ligação de ponto final privado. 
>

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

12. Selecione **Rever + criar**. É levado para o **separador 'Rever +' onde** o Azure valida a sua configuração.

13. Depois de aparecer a mensagem **de validação** verde, selecione **Criar**.

## <a name="faq"></a>FAQ

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Por que não posso ligar-me a um ponto final privado?
Se o seu cache já for uma cache injetada em VNet, os pontos finais privados não podem ser utilizados com a sua instância de cache. Se a sua instância de cache estiver a utilizar uma funcionalidade não suportada (listada abaixo), não poderá ligar-se à sua instância de ponto final privado. Além disso, as instâncias cache precisam de ser criadas após 27 de julho para utilizar pontos finais privados.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>Que características não são suportadas com pontos finais privados?
Geo-replicação, regras de firewall, suporte para consolas de portal, múltiplos pontos finais por cache agrupado, persistência nas regras de firewall e redundância de zona. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Como posso alterar o meu ponto final privado para ser desativado ou habilitado a partir do acesso à rede pública?
Há uma `publicNetworkAccess` bandeira que é por `Disabled` defeito. Esta bandeira destina-se a permitir opcionalmente o acesso ao ponto final público e privado à cache, se estiver definido para `Enabled` . Se estiver `Disabled` definido, só permitirá o acesso ao ponto final privado. Pode definir o valor para `Disabled` ou com o seguinte pedido `Enabled` PATCH. Edite o valor para refletir qual a bandeira que deseja para o seu cache.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Os grupos de segurança de rede (NSG) estão habilitados para pontos finais privados?
Não, são deficientes para pontos finais privados. Embora as sub-redes que contenham o ponto final privado possam ter NSG associada, as regras não serão eficazes no tráfego processado pelo ponto final privado. Deve ter [políticas de rede desativadas](../private-link/disable-private-endpoint-network-policy.md) para implantar pontos finais privados numa sub-rede. O NSG ainda é aplicado em outras cargas de trabalho alojoadas na mesma sub-rede. As rotas em qualquer sub-rede de clientes utilizarão um prefixo /32, alterando o comportamento de encaminhamento padrão requer um UDR semelhante. 

Controle o tráfego utilizando as regras NSG para tráfego de saída em clientes de origem. Implementar rotas individuais com prefixo /32 para substituir rotas privadas de ponto final. Os registos do NSG Flow e as informações de monitorização das ligações de saída ainda são suportados e podem ser utilizados

### <a name="can-i-use-firewall-rules-with-private-endpoints"></a>Posso usar regras de firewall com pontos finais privados?
Não, esta é uma limitação atual de pontos finais privados. O ponto final privado não funcionará corretamente se as regras de firewall estiverem configuradas na cache.

### <a name="how-can-i-connect-to-a-clustered-cache"></a>Como posso ligar-me a uma cache agrupada?
`publicNetworkAccess` precisa de ser definido `Disabled` e só pode haver uma ligação privada de ponto final.

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>Como o meu caso de ponto final privado não está no meu VNet, como está associado ao meu VNet?
Só está ligado ao seu VNet. Uma vez que não está no seu VNet, as regras NSG não precisam de ser modificadas para pontos finais dependentes.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>Como posso migrar a minha cache injetada em VNet para uma cache de ponto final privado?
Terá de eliminar a cache injetada do VNet e criar uma nova instância de cache com um ponto final privado.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Azure Private Link, consulte a documentação do [Azure Private Link](../private-link/private-link-overview.md).
* Para comparar várias opções de isolamento de rede para o seu cache, consulte [a Azure Cache para a documentação das opções de isolamento da rede Redis.](cache-network-isolation.md)