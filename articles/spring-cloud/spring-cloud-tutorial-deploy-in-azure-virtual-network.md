---
title: Tutorial - Implementar Azure Spring Cloud em rede virtual
description: Implementar Azure Spring Cloud em rede virtual (injeção em v-net).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 979ecf77fe53238dfd377c5fd2baf394de985c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892889"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Tutorial: Implementar Azure Spring Cloud em rede virtual Azure (injeção VNet)

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Este tutorial explica como implementar uma instância de serviço Azure Spring Cloud na sua rede virtual. Isto às vezes chama-se injeção de VNet.  

A implantação permite:

* Isolamento das aplicações da Azure Spring Cloud e tempo de funcionamento da Internet na sua rede corporativa
* Interação da Nuvem de primavera Azure com sistemas em centros de dados no local e/ou serviços Azure em outras redes virtuais
* Capacitação dos clientes para controlar comunicações de rede de entrada e saída para a Azure Spring Cloud

## <a name="prerequisites"></a>Pré-requisitos
Deve registar o fornecedor de recursos Azure Spring Cloud `Microsoft.AppPlatform` de acordo com as instruções Registar Fornecedor de Recursos no portal [Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) ou executando o seguinte comando Az CLI:

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>Requisitos de rede virtual
A rede virtual à qual implementa a sua instância de serviço Azure Spring Cloud deve satisfazer os seguintes requisitos:

* **Localização**: A rede virtual deve residir no mesmo local que a instância de serviço Azure Spring Cloud.
* **Subscrição**: A rede virtual deve estar na mesma subscrição que a instância de serviço Azure Spring Cloud.
* **Sub-redes**: A rede virtual deve incluir duas sub-redes dedicadas a uma instância de serviço Azure Spring Cloud: 
    * Um para o tempo de funcionação do serviço
    * Um para as suas aplicações de microserviço de botas de mola. 
    * Existe uma relação um-para-um entre estas sub-redes e uma instância de serviço Azure Spring Cloud. Tem de utilizar uma nova sub-rede para cada instância de serviço que implementa e cada sub-rede só pode incluir uma única instância de serviço.
* **Espaço de endereço**: Um bloco CIDR até /28 para a sub-rede de tempo de funcionamento do serviço e outro bloco CIDR até /24 para a sub-rede de aplicações de microserviços de botas de mola.
* **Tabela de rotas**: As sub-redes não devem ter uma tabela de rotas existente associada.

Os seguintes procedimentos descrevem a configuração da rede virtual para conter a instância de Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Se já tem uma rede virtual para hospedar a instância de serviço Azure Spring Cloud, salte os passos 1, 2 e 3. Pode começar do passo 4 para preparar sub-redes para a rede virtual.

1. No menu do portal do Azure, selecione **Criar um recurso**. A partir do Mercado Azure, **selecione rede**Virtual de Rede  >  **Virtual network**.

1. No diálogo **de rede virtual Criar,** insira ou selecione as seguintes informações:

    |Definição          |Valor                                             |
    |-----------------|--------------------------------------------------|
    |Subscrição     |Selecione a sua subscrição.                         |
    |Grupo de recursos   |Selecione o seu grupo de recursos ou crie um novo.  |
    |Nome             |*Insira azure-spring-cloud-vnet*                   |
    |Localização         |Selecione **E.U.A. Leste**.                                |

1. Clique **em seguida: endereços IP >**. 
 
1. Para o espaço de endereço IPv4, insira 10.1.0.0/16.

1. **Selecione Adicionar a sub-rede**, em seguida, introduza *a sub-rede de funcionação do serviço* para o nome da **sub-rede** e 10.1.0.0/24 para **a gama de endereços sub-rede**. Em seguida, clique em **Adicionar**.

1. **Selecione Novamente Adicione a sub-rede** e introduza *as aplicações-sub-redes* para **o nome sub-rede** e 10.1.1.0/24 para **a gama de endereços Subnet**.  Clique em **Adicionar**.

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

    Em seguida, selecione *Azure Spring Cloud Resource Provider*e clique em **Guardar**.

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

1. Abra o portal Azure utilizando em https://ms.portal.azure.com .

1. A partir da caixa de pesquisa superior, procure **por Azure Spring Cloud**e selecione **Azure Spring Cloud** a partir do resultado.

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

Após a implementação, dois grupos de recursos adicionais serão criados na sua subscrição para hospedar os recursos da rede para a instância do serviço Azure Spring Cloud.  Navegue para **casa** e, em seguida, selecione **grupos** de recursos a partir dos itens de menu superiores para encontrar os seguintes novos grupos de recursos.

O grupo de recursos nomeado como *azure-spring-cloud-service-runtime_{service instance name}_{service instance region}* contém recursos de rede para o tempo de execução do serviço da instância de serviço.

  ![Tempo de execução do serviço](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

O grupo de recursos nomeado como *azure-spring-cloud-service-runtime_{service instance name}_{service instance region}* contém recursos de rede para as suas aplicações de microserviço de botas de mola da instância de serviço.

  ![Grupo de recursos de aplicativos](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Esses recursos de rede estão ligados à sua rede virtual criada acima.

  ![Rede V com dispositivo conectado](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Os grupos de recursos são totalmente geridos pelo serviço Azure Spring Cloud. Não elimine manualmente ou modifique qualquer recurso no seu interior.

## <a name="next-steps"></a>Passos seguintes

[Implementar aplicação para Azure Spring Cloud no seu VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Consulte também

- [Resolução de problemas Azure Spring Cloud em VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Responsabilidades do cliente para correr Azure Spring Cloud em VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
