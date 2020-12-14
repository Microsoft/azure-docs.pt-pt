---
title: Tutorial - Implementar Azure Spring Cloud em rede virtual
description: Implementar Azure Spring Cloud em rede virtual (injeção em v-net).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1e16c984e48c11961dba0c977d3bdbddbd6bdf36
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400324"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Tutorial: Implementar Azure Spring Cloud em rede virtual Azure (injeção VNet)

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Este tutorial explica como implementar uma instância de serviço Azure Spring Cloud na sua rede virtual. Isto às vezes chama-se injeção de VNet.  

A implantação permite:

* Isolamento das aplicações da Azure Spring Cloud e tempo de funcionamento da Internet na sua rede corporativa
* Interação da Nuvem de primavera Azure com sistemas em centros de dados no local e/ou serviços Azure em outras redes virtuais
* Capacitação dos clientes para controlar comunicações de rede de entrada e saída para a Azure Spring Cloud

## <a name="prerequisites"></a>Pré-requisitos
Deve registar-se o fornecedor de recursos Azure Spring Cloud *Microsoft.AppPlatform* e *Microsoft.ContainerService* de acordo com as instruções [Register Resource Provider no portal Azure](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou executando o seguinte comando Az CLI:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```
## <a name="virtual-network-requirements"></a>Requisitos de rede virtual
A rede virtual à qual implementa a sua instância de serviço Azure Spring Cloud deve satisfazer os seguintes requisitos:

* **Localização**: A rede virtual deve residir no mesmo local que a instância de serviço Azure Spring Cloud.
* **Subscrição**: A rede virtual deve estar na mesma subscrição que a instância de serviço Azure Spring Cloud.
* **Sub-redes**: A rede virtual deve incluir duas sub-redes dedicadas a uma instância de serviço Azure Spring Cloud: 
    * Um para o tempo de funcionação do serviço
    * Um para as suas aplicações de microserviço de botas de mola. 
    * Existe uma relação um-para-um entre estas sub-redes e uma instância de serviço Azure Spring Cloud. Tem de utilizar uma nova sub-rede para cada instância de serviço que implementa e cada sub-rede só pode incluir uma única instância de serviço.
* **Espaço de endereço**: CIDR bloqueia até **/28** para a sub-rede de tempo de execução de serviço e sub-rede de micro-serviços de arranque de mola.
* **Tabela de rotas**: As sub-redes não devem ter uma tabela de rotas existente associada.

Os seguintes procedimentos descrevem a configuração da rede virtual para conter a instância de Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Se já tem uma rede virtual para hospedar a instância de serviço Azure Spring Cloud, salte os passos 1, 2 e 3. Pode começar do passo 4 para preparar sub-redes para a rede virtual.

1. No menu do portal do Azure, selecione **Criar um recurso**. A partir do Mercado Azure, **selecione rede** Virtual de Rede  >  .

1. No diálogo **de rede virtual Criar,** insira ou selecione as seguintes informações:

    |Definição          |Valor                                             |
    |-----------------|--------------------------------------------------|
    |Subscrição     |Selecione a sua subscrição.                         |
    |Grupo de recursos   |Selecione o seu grupo de recursos ou crie um novo.  |
    |Nome             |*Insira azure-spring-cloud-vnet*                   |
    |Localização         |Selecione **E.U.A. Leste**.                                |

1. Clique **em seguida: endereços IP >**. 
 
1. Para o espaço de endereço IPv4, insira 10.1.0.0/16.

1. **Selecione Adicionar a sub-rede**, em seguida, introduza *a sub-rede de funcionação do serviço* para o nome da **sub-rede** e 10.1.0.0/28 para **a gama de endereços sub-rede**. Em seguida, clique em **Adicionar**.

1. Selecione Adicionar novamente a **sub-rede** e, em seguida, insira **o nome da sub-rede** e **a gama de endereços Sub-rede**, por exemplo, *aplicações-sub-redes* e 10.1.1.0/28 .  Clique em **Adicionar**.

1. Clique em **Rever + criar**. Deixe o resto como padrão e clique em **Criar**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Conceder permissão de serviço à rede virtual

Selecione a rede virtual *azure-spring-cloud-vnet* que criou anteriormente.

1. Selecione **o controlo de acesso (IAM)** e, em seguida, selecione Adicionar > Adicionar a atribuição de **funções**.

    ![Controlo de acesso para v-net](./media/spring-cloud-v-net-injection/access-control.png)

2. No diálogo **de atribuição de funções Adicionar,** insira ou selecione estas informações:

    |Definição  |Valor                                             |
    |---------|--------------------------------------------------|
    |Função     |Selecione **Proprietário**                                  |
    |Selecione   |Insira *o fornecedor de recursos cloud da mola Azure*      |

    Em seguida, selecione *Azure Spring Cloud Resource Provider* e clique em **Guardar**.

    ![Grant azure spring cloud resource provider to v-net](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Você também pode alcançá-lo executando o seguinte comando AZ CLI

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Implementar a instância de serviço Azure Spring Cloud na rede virtual

1. Abra o portal Azure utilizando em https://portal.azure.com .

1. A partir da caixa de pesquisa superior, procure **por Azure Spring Cloud** e selecione **Azure Spring Cloud** a partir do resultado.

1. Na página **Azure Spring Cloud,** selecione **+ Adicionar**.

1. Preencha o formulário na página Azure Spring Cloud **Create.** 

1. Selecione o mesmo grupo de recursos e região que a rede virtual.

1. Para **obter nome** em detalhes de **serviço**, selecione *azure-spring-cloud-vnet*.

1. Selecione **o separador de rede** e selecione o seguinte:

    |Definição                                |Valor                                             |
    |---------------------------------------|--------------------------------------------------|
    |Implementar na sua própria rede virtual     |Selecione **Sim**                                    |
    |Rede virtual                        |Selecione *azure-spring-cloud-vnet*                  |
    |Sub-rede de tempo de execução de serviço                 |Selecione *sub-rede de execução de serviço*                   |
    |Sub-rede de aplicações de microserviços de botas de mola   |Selecione *apps-subnet*                              |

    ![Separador de rede de criação](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Clique em **Rever e criar**.

1. Verifique as suas especificações e clique em **Criar**.

    ![Verificar especificações](./media/spring-cloud-v-net-injection/verify-specifications.png)

Após a implementação, dois grupos de recursos adicionais serão criados na sua subscrição para hospedar os recursos da rede para a instância do serviço Azure Spring Cloud.  Navegue para **casa** e, em seguida, selecione **grupos** de recursos a partir dos itens de menu superiores para encontrar os seguintes novos grupos de recursos.

O grupo de recursos nomeado como *ap-svc-rt_{nome de instância de serviço}_{região de instância de serviço}* contém recursos de rede para o tempo de execução de serviço da instância de serviço.

  ![Tempo de execução do serviço](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

O grupo de recursos nomeado como *ap-app_{service instance name}_{service instance region}* contém recursos de rede para as suas aplicações de microserviço de botas de mola da instância de serviço.

  ![Grupo de recursos de aplicativos](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Esses recursos de rede estão ligados à sua rede virtual criada acima.

  ![Rede V com dispositivo conectado](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Os grupos de recursos são totalmente geridos pelo serviço Azure Spring Cloud. Não elimine manualmente ou modifique qualquer recurso no seu interior.

## <a name="limitations"></a>Limitações

A pequena gama de sub-redes guarda endereços IP, mas traz limitações ao número máximo de Instâncias de Aplicação que a Nuvem de primavera Azure pode conter. 

| CIDR de sub-rede de aplicações | Total IPs | IPs disponíveis | Ocorrências máximas de aplicações                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> App com 1 núcleo: 96 <br/> App com 2 núcleos: 48<br/>  App com 3 núcleos: 32 <br/> App com 4 núcleos: 24 </p> |
| /27             | 32        | 24            | <p> App com 1 núcleo: 228<br/> App com 2 núcleos: 144<br/>  App com 3 núcleos: 96 <br/>  App com 4 núcleos: 72</p> |
| /26             | 64        | 56            | <p> App com 1 núcleo: 500<br/> App com 2 núcleos: 336<br/>  App com 3 núcleos: 224<br/>  App com 4 núcleos: 168</p> |
| /25             | 128       | 120           | <p> App com 1 núcleo: 500<br> App com 2 núcleos: 500<br>  App com 3 núcleos: 480<br>  App com 4 núcleos: 360</p> |
| /24             | 256       | 248           | <p> App com 1 núcleo: 500<br/> App com 2 núcleos: 500<br/>  App com 3 núcleos: 500<br/>  App com 4 núcleos: 500</p> |

Para sub-redes, 5 endereços IP são reservados pela Azure, e pelo menos 4 endereços são necessários pela Azure Spring Cloud. São necessários pelo menos 9 endereços IP, pelo que /29 e /30 não estão operacionais.

Para a sub-rede de tempo de execução do serviço, o tamanho min é de um /28 e este não tem qualquer influência no número de instâncias da aplicação.
## <a name="next-steps"></a>Passos seguintes

[Implementar aplicação para Azure Spring Cloud no seu VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Veja também

- [Resolução de problemas Azure Spring Cloud em VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Responsabilidades do cliente para correr Azure Spring Cloud em VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
