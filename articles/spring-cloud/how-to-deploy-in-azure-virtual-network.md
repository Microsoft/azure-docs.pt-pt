---
title: Implementar Azure Spring Cloud numa rede virtual
description: Implementar a nuvem de mola Azure numa rede virtual (injeção VNet).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 82dcd8c59c55a2866b51fd6dee896ea1298b6cf6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878249"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Implementar Azure Spring Cloud numa rede virtual

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Este tutorial explica como implementar uma instância Azure Spring Cloud na sua rede virtual. Esta implementação às vezes é chamada de injeção VNet.

A implantação permite:

* Isolamento das aplicações Azure Spring Cloud e tempo de funcionamento da internet na sua rede corporativa.
* Azure Spring Cloud interação com sistemas em centros de dados no local ou serviços Azure em outras redes virtuais.
* Capacitação dos clientes para controlar as comunicações de rede de entrada e saída para a Azure Spring Cloud.

> [!Note]
> Só pode selecionar a sua rede virtual Azure quando criar uma nova instância de serviço Azure Spring Cloud. Não é possível alterar para utilizar outra rede virtual depois da criação do Azure Spring Cloud.

## <a name="prerequisites"></a>Pré-requisitos

Registe o fornecedor de recursos Azure Spring Cloud **Microsoft.AppPlatform** e **Microsoft.ContainerService** de acordo com as instruções no [fornecedor de recursos Register no portal Azure](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou executando o seguinte comando Azure CLI:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Requisitos de rede virtual

A rede virtual à qual implementa a sua instância Azure Spring Cloud deve satisfazer os seguintes requisitos:

* **Localização**: A rede virtual deve residir no mesmo local que a instância Azure Spring Cloud.
* **Subscrição**: A rede virtual deve estar na mesma subscrição que a instância Azure Spring Cloud.
* **Sub-redes**: A rede virtual deve incluir duas sub-redes dedicadas a uma instância Azure Spring Cloud:

    * Um para o tempo de serviço.
    * Um para as suas aplicações de microserviços Spring Boot.
    * Há uma relação um-para-um entre estas sub-redes e um exemplo de Azure Spring Cloud. Utilize uma nova sub-rede para cada instância de serviço que implementar. Cada sub-rede só pode incluir uma única instância de serviço.
* **Espaço de endereço**: CIDR bloqueia até */28* tanto para a sub-rede de tempo de funcionamento como para a sub-rede de micro-serviços spring boot.
* **Tabela de rotas**: Por predefinição, as sub-redes não necessitam de tabelas de rota existentes associadas. Pode [trazer a sua própria mesa de rota.](#bring-your-own-route-table)

Os seguintes procedimentos descrevem a configuração da rede virtual para conter a instância de Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Se já tem uma rede virtual para hospedar uma instância Azure Spring Cloud, salte os passos 1, 2 e 3. Pode começar do passo 4 para preparar sub-redes para a rede virtual.

1. No menu do portal do Azure, selecione **Criar um recurso**. A partir do Azure Marketplace, **selecione rede** virtual de  >  rede.

1. Na caixa de diálogo **de rede virtual Criar,** insira ou selecione as seguintes informações:

    |Definição          |Valor                                             |
    |-----------------|--------------------------------------------------|
    |Subscrição     |Selecione a sua subscrição.                         |
    |Grupo de recursos   |Selecione o seu grupo de recursos ou crie um novo.  |
    |Name             |**Insira azure-spring-cloud-vnet**.                 |
    |Localização         |Selecione **East US**.                               |

1. Selecione **Seguinte: Endereços IP**.

1. Para o espaço de endereço IPv4, insira **10.1.0.0/16**.

1. **Selecione Adicionar sub-rede**. Em seguida, introduza **a sub-rede de funcionação de serviço** para o nome da **sub-rede** e introduza **10.1.0.0/28** para a gama de **endereços Sub-rede**. Em seguida, selecione **Adicionar**.

1. **Selecione Adicionar novamente a sub-rede** e, em seguida, introduzir **o nome da Sub-rede** e **o intervalo de endereços Sub-rede**. Por exemplo, introduza **apps-subnet** e **10.1.1.0/28**. Em seguida, selecione **Adicionar**.

1. Selecione **Rever + criar**. Deixe o resto como padrão e selecione **Criar**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Conceder permissão de serviço à rede virtual
O Azure Spring Cloud requer permissão **do Proprietário** para a sua rede virtual, de forma a conceder um chefe de serviço dedicado e dinâmico na rede virtual para posterior implementação e manutenção.

Selecione a rede virtual **azure-spring-cloud-vnet** que criou anteriormente.

1. Selecione **o controlo de acesso (IAM)** e, em seguida, selecione **Adicionar**  >  **a função** de função .

    ![Screenshot que mostra o ecrã de controlo access.](./media/spring-cloud-v-net-injection/access-control.png)

1. Na caixa de diálogo de **atribuição de funções Adicionar,** insira ou selecione as seguintes informações:

    |Definição  |Valor                                             |
    |---------|--------------------------------------------------|
    |Função     |Selecione **Proprietário**.                                 |
    |Selecione   |Insira **o fornecedor de recursos em nuvem de mola Azure**.   |

    Em seguida, selecione **Azure Spring Cloud Resource Provider** e selecione **Save**.

    ![Screenshot que mostra a seleção do Fornecedor de Recursos da Nuvem de primavera Azure.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Também pode fazer este passo executando o seguinte comando Azure CLI:

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

## <a name="deploy-an-azure-spring-cloud-instance"></a>Implementar um exemplo de Nuvem de primavera Azure

Para implementar uma instância Azure Spring Cloud na rede virtual:

1. Abra o [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa superior, procure **a Azure Spring Cloud.** Selecione **Azure Spring Cloud** do resultado.

1. Na página **Azure Spring Cloud,** selecione **+ Adicionar**.

1. Preencha o formulário na página Azure Spring Cloud **Create.**

1. Selecione o mesmo grupo de recursos e região que a rede virtual.

1. Para **obter nome** em detalhes de **serviço**, selecione **azure-spring-cloud-vnet**.

1. Selecione o **separador 'Rede'** e selecione os seguintes valores:

    |Definição                                |Valor                                             |
    |---------------------------------------|--------------------------------------------------|
    |Implementar na sua própria rede virtual     |Selecione **Yes** (Sim).                                   |
    |Rede virtual                        |Selecione **azure-spring-cloud-vnet**.               |
    |Sub-rede de tempo de execução de serviço                 |Selecione **a sub-rede de execução do serviço.**                |
    |Sub-rede de aplicações de microserviços de botas de mola   |Selecione **apps-sub-redes**.                           |

    ![Screenshot que mostra o separador de Rede na página Azure Spring Cloud Create.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Selecione **Rever e criar**.

1. Verifique as suas especificações e selecione **Criar**.

    ![Screenshot que mostra especificações de verificação.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Após a implementação, dois grupos de recursos adicionais serão criados na sua subscrição para hospedar os recursos da rede para a instância Azure Spring Cloud. Vá para **casa** e, em seguida, selecione **grupos** de recursos a partir dos itens de menu superiores para encontrar os seguintes novos grupos de recursos.

O grupo de recursos nomeado como **ap-svc-rt_{nome de instância de serviço}_{região de instância de serviço}** contém recursos de rede para o tempo de funcionação do serviço da instância de serviço.

  ![Screenshot que mostra o tempo de execução do serviço.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

O grupo de recursos nomeado como **ap-app_{service instance name}_{service instance region}** contém recursos de rede para as suas aplicações de microserviços Spring Boot da instância de serviço.

  ![Screenshot que mostra grupo de recursos de aplicativos.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Esses recursos de rede estão ligados à sua rede virtual criada na imagem anterior.

  ![Screenshot que mostra a rede virtual com dispositivos conectados.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Os grupos de recursos são totalmente geridos pelo serviço Azure Spring Cloud. *Não* elimine manualmente ou modifique qualquer recurso no seu interior.

## <a name="using-smaller-subnet-ranges"></a>Usando gamas de sub-redes mais pequenas

Esta tabela mostra o número máximo de instâncias de aplicações que o Azure Spring Cloud suporta utilizando gamas de sub-redes mais pequenas.

| Sub-rede de aplicações CIDR | Total IPs | IPs disponíveis | Ocorrências máximas de aplicações                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> App com 1 núcleo: 96 <br/> App com 2 núcleos: 48<br/>  App com 3 núcleos: 32 <br/> App com 4 núcleos: 24 </p> |
| /27             | 32        | 24            | <p> App com 1 núcleo: 228<br/> App com 2 núcleos: 144<br/>  App com 3 núcleos: 96 <br/>  App com 4 núcleos: 72</p> |
| /26             | 64        | 56            | <p> App com 1 núcleo: 500<br/> App com 2 núcleos: 336<br/>  App com 3 núcleos: 224<br/>  App com 4 núcleos: 168</p> |
| /25             | 128       | 120           | <p> App com 1 núcleo: 500<br> App com 2 núcleos: 500<br>  App com 3 núcleos: 480<br>  App com 4 núcleos: 360</p> |
| /24             | 256       | 248           | <p> App com 1 núcleo: 500<br/> App com 2 núcleos: 500<br/>  App com 3 núcleos: 500<br/>  App com 4 núcleos: 500</p> |

Para sub-redes, cinco endereços IP são reservados pela Azure, e pelo menos quatro endereços são exigidos pela Azure Spring Cloud. São necessários pelo menos nove endereços IP, pelo que /29 e /30 não são operacionais.

Para uma sub-rede de tempo de funcionação de serviço, o tamanho mínimo é /28. Este tamanho não tem qualquer influência no número de instâncias de aplicações.

## <a name="bring-your-own-route-table"></a>Traga a sua própria mesa de rota

Azure Spring Cloud suporta a utilização de sub-redes e tabelas de rotas existentes.

Se as suas sub-redes personalizadas não contiverem tabelas de rotas, a Azure Spring Cloud cria-as para cada uma das sub-redes e adiciona-lhes regras ao longo do ciclo de vida do caso. Se as suas subnetas personalizadas contiverem tabelas de rotas, a Azure Spring Cloud reconhece as tabelas de rotas existentes durante as operações de exemplo e adiciona/atualizações e/ou regras em conformidade para as operações.

> [!Warning] 
> As regras personalizadas podem ser adicionadas às tabelas de rotas personalizadas e atualizadas. No entanto, as regras são adicionadas pela Azure Spring Cloud e estas não devem ser atualizadas ou removidas. Regras como 0.0.0.0/0 devem existir sempre numa tabela de rota e mapa para o alvo do seu portal de internet, como um gateway NVA ou outro gateway de saída. Tenha cuidado ao atualizar as regras quando apenas as suas regras personalizadas estiverem a ser modificadas.


### <a name="route-table-requirements"></a>Requisitos do quadro de rotas

As tabelas de rota a que o seu vnet personalizado está associado devem satisfazer os seguintes requisitos:

* Só pode associar as suas tabelas de rotas Azure ao seu vnet quando criar uma nova instância de serviço Azure Spring Cloud. Não é possível alterar para usar outra tabela de rotas depois da criação da Azure Spring Cloud.
* Tanto a sub-rede de aplicação de micro-serviço como a sub-rede de tempo de funcionamento de serviço devem associar-se a diferentes tabelas de rota ou nenhuma delas.
* As permissões devem ser atribuídas antes da criação de exemplos. Certifique-se de conceder permissão ao *Azure Spring Cloud Owner* para as suas mesas de rota.
* O recurso de tabela de rotas associado não pode ser atualizado após a criação do cluster. Embora o recurso da tabela de rotas não possa ser atualizado, as regras personalizadas podem ser modificadas na tabela de rotas.
* Não é possível reutilizar uma tabela de rotas com múltiplas instâncias devido a potenciais regras de encaminhamento conflituosas.

## <a name="next-steps"></a>Passos seguintes

[Implementar aplicação para Azure Spring Cloud no seu VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Ver também

- [Resolução de problemas Azure Spring Cloud em VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Responsabilidades do cliente para correr Azure Spring Cloud em VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
