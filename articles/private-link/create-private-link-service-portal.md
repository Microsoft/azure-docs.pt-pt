---
title: Quickstart - Criar um serviço de ligação privada utilizando o portal Azure
titlesuffix: Azure Private Link
description: Saiba como criar um serviço de Ligação Privada utilizando o portal Azure neste arranque rápido
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: allensu
ms.openlocfilehash: d394a475c5121607f70c03437382e104a5d0cbee
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746412"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Quickstart: Criar um serviço de ligação privada utilizando o portal Azure

Começa a criar um serviço de Ligação Privada que se refere ao teu serviço.  Dê acesso ao Link Privado ao seu serviço ou recurso implantado atrás de um Equilibr de Carga Padrão Azure.  Os utilizadores do seu serviço têm acesso privado a partir da sua rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Nesta secção, irá criar uma rede virtual e um Equilibrador de Carga Azure interno.

### <a name="virtual-network"></a>Rede virtual

Nesta secção, cria uma rede virtual e uma sub-rede para hospedar o equilibrador de carga que acede ao seu serviço Private Link.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | Selecione **CreatePrivLinkService-rg** |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNet**                                    |
    | Region           | Selecione **East US 2** |

3. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

4. No separador **endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **10.1.0.0/16** |

5. No **nome da sub-rede,** selecione a palavra **predefinição**.

6. Na **sub-rede Editar,** insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **mySubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

7. Selecione **Guardar**.

8. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

9. Selecione **Criar**.

### <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga standard

Utilize o portal para criar um balanceador interno padrão. 

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  equilibrador de carga de **rede de** recursos  >  .

2. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações: 

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **CreatePrivLinkService-rg** criado no passo anterior.|
    | Name                   | Insira **o myLoadBalancer**                                   |
    | Region         | Selecione **E.U.A. Leste 2**.                                        |
    | Tipo          | Selecione **Interno**.                                        |
    | SKU           | Selecione **Standard** |
    | Rede virtual | Selecione **myVNet** criado no passo anterior. |
    | Sub-rede  | Selecione **mySubnet** criado no passo anterior. |
    | Atribuição de endereços IP | Selecione **Dynamic**. |
    | Zona de disponibilidade | Selecione **Zona redundante** |

3. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**.

4. No **separador 'Rever + criar',** selecione **Criar.**   

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, configura:

* Ajustes do balançador de carga para um pool de endereço de backend.
* Uma sonda de saúde.
* Uma regra do equilíbrio de carga.

### <a name="create-a-backend-pool"></a>Criar um conjunto de back-ends

Um conjunto de endereços de backend contém os endereços IP dos (NICs) virtuais ligados ao equilibrador de carga. 

Crie o pool de endereços de backend **myBackendPool** para incluir máquinas virtuais para equilibrar o tráfego de internet.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **pools backend** e, em seguida, selecione **Adicionar**.

3. Na página de **piscina de backend,** para nome, escreva **myBackendPool,** como o nome da sua piscina de backend e, em seguida, selecione **Add**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

O equilibrador de carga monitoriza o estado da sua aplicação com uma sonda de saúde. 

A sonda de saúde adiciona ou remove VMs do equilibrador de carga com base na sua resposta aos controlos de saúde. 

Crie uma sonda de estado de funcionamento com o nome **myHealthProbe**, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **sondas de saúde** e, em seguida, selecione **Adicionar**.
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **o myHealthProbe**. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira **80**.|
    | Intervalo | Introduza **15** para o número de **intervalos** em segundos entre tentativas de sonda. |
    | Limiar com funcionamento incorreto | Selecione **2** para o número de **falhas de sonda insalubres** ou falhas de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|
    | | |

3. Deixe o resto as predefinições e Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Você define a configuração IP frontend para o tráfego de entrada e o pool IP backend para receber o tráfego. A origem e a porta de destino estão definidas na regra. 

Nesta secção, irá criar uma regra do balançador de carga:

* Chamado **myHTTPRule.**
* Na frontend chamada **LoadBalancerFrontEnd**.
* Escuta no **Porto 80**.
* Direciona o tráfego equilibrado para o backendpool chamado **myBackendPool** na **Porta 80**.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **regras de equilíbrio de carga** e, em seguida, selecione **Adicionar**.

3. Utilize estes valores para configurar a regra de equilíbrio de carga:
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **myHTTPRule**. |
    | Versão IP | Selecione **IPv4** |
    | Endereço IP frontend | **Selecione LoadBalancerFrontEnd** |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira **80**.|
    | Porta de back-end | Insira **80**. |
    | Conjunto de back-end | Selecione **myBackendPool**.|
    | Sonda de estado de funcionamento | Selecione **myHealthProbe**. |
    | Tempo de 20 minutos (minutos) | Mova o deslizador para **15** minutos. |
    | Reset TCP | Selecione **Ativado**. |

4. Deixe o resto dos predefinidos e, em seguida, selecione **OK**.

## <a name="create-a-private-link-service"></a>Criar um serviço de ligação privada

Nesta secção, irá criar um serviço de Ligação Privada por trás de um balanceador de carga padrão.

1. Na parte superior esquerda da página no portal Azure, selecione **Criar um recurso**.

2. Procure **por Link Privado** na caixa **'Pesquisar o Mercado'.**

3. Selecione **Criar**.

4. Em **Visão Geral** no **Private Link Center,** selecione o botão de serviço de **ligação privada** Azul Create.

5. No separador Básicos ao abrigo **do serviço de ligação privada** Create, insira ou selecione as **seguintes** informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |  |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de Recursos | Selecione **CreatePrivLinkService-rg**. |
    | **Detalhes da instância** |  |
    | Name | Insira **o myPrivateLinkService**. |
    | Region | Selecione **E.U.A. Leste 2**. |

6. Selecione o **separador definições de saída** ou selecione **Seguinte: Definições de saída** na parte inferior da página.

7. No **separador Definições de Saída,** introduza ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Balanceador de carga | Selecione **myLoadBalancer**. |
    | Endereço IP frontend do balançador de carga | **Selecione LoadBalancerFrontEnd (10.1.0.4)**. |
    | Sub-rede NAT de origem | Selecione **mySubnet (10.1.0.0/24)**. |
    | Ativar o proxy V2 da TCP | Deixe o padrão de **Nº**. </br> Se a sua aplicação espera um cabeçalho V2 de procuração de TCP, selecione **Sim**. |
    | **Definições privadas de endereço IP** |  |
    | Deixe as definições predefinidos |  |

8. Selecione o separador **de segurança Access** ou selecione **Seguinte: Aceda à segurança** na parte inferior da página.

9. Deixe o controlo **de acesso baseado em funções apenas** no separador de segurança **Access.**

10. Selecione o separador **Tags** ou selecione **Seguinte: Tags** na parte inferior da página.

11. Selecione o separador **'Rever +' criar** ou selecionar **Seguinte: Rever + criar** na parte inferior da página.

12. Selecione **Criar** no **separador 'Revisão+' criar.**

O seu serviço de ligação privada foi criado e pode receber tráfego. Se quiser ver fluxos de tráfego, configuure a sua aplicação por trás do seu balanceador de carga padrão.


## <a name="create-private-endpoint"></a>Criar ponto final privado

Nesta secção, você irá mapear o serviço de ligação privada para um ponto final privado. Uma rede virtual contém o ponto final privado para o serviço de ligação privada. Esta rede virtual contém os recursos que irão aceder ao seu serviço de ligação privada.

### <a name="create-private-endpoint-virtual-network"></a>Criar rede virtual de ponto final privado

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | Selecione **CreatePrivLinkService-rg** |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNetPE**                                    |
    | Region           | Selecione **East US 2** |

3. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

4. No separador **endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **11.1.0.0/16** |

5. No **nome da sub-rede,** selecione a palavra **predefinição**.

6. Na **sub-rede Editar,** insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **mySubnetPE** |
    | Intervalo de endereços da sub-rede | Insira **11.1.0.0/24** |

7. Selecione **Guardar**.

8. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

9. Selecione **Criar**.

### <a name="create-private-endpoint"></a>Criar ponto final privado

1. No lado superior esquerdo do ecrã no portal, selecione **Criar um Link** Privado de Rede de Recursos , ou na caixa de pesquisa entrar No Link  >    >   **Privado**.

2. Selecione **Criar**.

3. No **Private Link Center**, selecione **Pontos finais privados** no menu da esquerda.

4. Em **pontos finais privados**, selecione **+ Adicionar**.

5. No separador **Básico de** Criar um ponto **final privado,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **CreatePrivLinkService-rg**. Criou este grupo de recursos na secção anterior.|
    | **Detalhes da instância** |  |
    | Name  | Introduza **myPrivateEndpoint**. |
    | Region | Selecione **E.U.A. Leste 2**. |

6. Selecione o **separador Recursos** ou o **seguinte: Botão de recurso** na parte inferior da página.
    
7. Em **Recurso,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Método de ligação | Selecione **Ligar a um recurso Azure no meu diretório**. |
    | Subscrição | Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.Network/privateLinkServices**. |
    | Recurso | Selecione **myPrivateLinkService**. |

8. Selecione o **separador Configuração** ou o **seguinte: Botão de configuração** na parte inferior do ecrã.

9. Na **Configuração,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **Rede** |  |
    | Rede Virtual | Selecione **myVNetPE**. |
    | Sub-rede | Selecione **mySubnetPE**. |

10. Selecione o **separador 'Rever +'** ou o botão **'Rever +'** na parte inferior do ecrã.

11. Selecione **Criar**.

### <a name="ip-address-of-private-endpoint"></a>Endereço IP do ponto final privado

Nesta secção, encontrará o endereço IP do ponto final privado que corresponde ao equilibrador de carga e ao serviço de ligação privada.

1. Na coluna à esquerda do portal Azure, selecione **grupos de recursos**.

2. Selecione o grupo de recursos **CreatePrivLinkService-rg.**

3. No grupo de recursos **CreatePrivLinkService-rg,** selecione **myPrivateEndpoint**.

4. Na página **de visão geral** do **myPrivateEndpoint,** selecione o nome da interface de rede associada ao ponto final privado.  O nome da interface de rede começa com **myPrivateEndpoint.nic**.

5. Na página **geral** do nic de ponto final privado, o endereço IP do ponto final é apresentado no **endereço IP privado**.
    

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar o serviço de ligação privada, elimine o grupo de recursos para limpar os recursos utilizados neste arranque rápido.

1. Introduza **CreatePrivLinkService-rg** na caixa de pesquisa no topo do portal e selecione **CreatePrivLinkService-rg** a partir dos resultados da pesquisa.
1. Selecione **Eliminar grupo de recursos**.
1. No **TIPO O NOME DO GRUPO DE RECURSOS**, insira **CreatePrivLinkService-rg**.
1. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Neste início rápido, irá:

* Criei uma rede virtual e um Equilibrador de Carga Azure interno.
* Criei um serviço de ligação privada.
* Criou uma rede virtual e um ponto final privado para o serviço de ligação privada.

Para saber mais sobre o ponto final da Azure Private, continue a:
> [!div class="nextstepaction"]
> [Quickstart: Criar um ponto final privado utilizando o portal Azure](create-private-endpoint-portal.md)