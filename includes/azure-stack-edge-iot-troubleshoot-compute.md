---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/23/2021
ms.openlocfilehash: 0d912d0ac3f0fcf4c52116e67909038a1973304b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104142"
---
Utilize as respostas de execução do agente IoT Edge para resolver erros relacionados com a computação. Aqui está uma lista de possíveis respostas:

* 200 - OK
* 400 - A configuração de implantação é mal formada ou inválida.
* 417 - O dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão de esquema na configuração de implementação é inválida.
* 406 - O dispositivo IoT Edge está offline ou não está a enviar relatórios de estado.
* 500 - Ocorreu um erro no tempo de execução do IoT Edge.

Para mais informações, consulte [o Agente IoT Edge.](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)

O seguinte erro está relacionado com o serviço IoT Edge no seu dispositivo Azure Stack Edge Pro.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Os módulos computacional têm estatuto desconhecido e não podem ser usados

#### <a name="error-description"></a>Descrição do erro

Todos os módulos do dispositivo mostram o estado desconhecido e não podem ser utilizados. O estado desconhecido persiste através de um reboot.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Solução sugerida

Elimine o serviço IoT Edge e, em seguida, reposicione o módulo(s). Para mais informações, consulte [o serviço Remove IoT Edge](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#remove-iot-edge-service).


### <a name="modules-show-as-running-but-are-not-working"></a>Os módulos mostram-se em execução, mas não funcionam

#### <a name="error-description"></a>Descrição do erro

O estado de funcionamento do módulo mostra como funcionamento, mas os resultados esperados não são vistos. 

Esta condição pode ser devido a um problema com a configuração da rota do módulo que não está a funcionar ou `edgehub` não está a encaminhar mensagens como esperado. Pode verificar os `edgehub` registos. Se você vê que existem erros como não ligar ao serviço IoT Hub, então a razão mais comum são os problemas de conectividade. Os problemas de conectividade podem ser porque a porta AMPQ que é usada como uma porta padrão pelo serviço IoT Hub para comunicação está bloqueada ou o servidor de procuração web está bloqueando estas mensagens.

#### <a name="suggested-solution"></a>Solução sugerida

Siga estes passos:
1. Para resolver o erro, aceda ao recurso IoT Hub para o seu dispositivo e, em seguida, selecione o seu dispositivo Edge. 
1. Ir para **definir módulos > configurações de tempo de execução**. 
1. Adicione a `Upstream protocol` variável ambiental e atribua-lhe um valor de `AMQPWS` . As mensagens configuradas neste caso são enviadas através da porta 443.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>Os módulos mostram como funcionamento mas não têm um IP atribuído

#### <a name="error-description"></a>Descrição do erro

O estado de funcionamento do módulo mostra como funcionamento, mas a aplicação contentorizada não tem um IP atribuído. 

Esta condição deve-se ao facto de o leque de IPs que forneceu para os IPs de serviço externo kubernetes não ser suficiente. É necessário alargar esta gama para garantir que cada recipiente ou VM que lançou estejam cobertos.

#### <a name="suggested-solution"></a>Solução sugerida

Na UI web local do seu dispositivo, faça os seguintes passos:
1. Vá à página do **Compute.** Selecione a porta para a qual ativou a rede de cálculo. 
1. Introduza uma gama estática e contígua de IPs para **os IPs de serviço externo Kubernetes**. Precisa de 1 IP para `edgehub` o serviço. Além disso, precisa de um IP para cada módulo IoT Edge e para cada VM que irá implementar. 
1. Selecione **Aplicar**. O intervalo de IP alterado deve entrar em vigor imediatamente.

Para obter mais informações, consulte [alterar os IPs de serviço externo para recipientes](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#change-external-service-ips-for-containers).

### <a name="configure-static-ips-for-iot-edge-modules"></a>Configure iPs estáticos para módulos IoT Edge

#### <a name="problem-description"></a>Descrição do problema

A Kubernetes atribui IPs dinâmicos a cada módulo IoT Edge no seu dispositivo GPU Azure Stack Edge Pro. É necessário um método para configurar os IPs estáticos para os módulos.

#### <a name="suggested-solution"></a>Solução sugerida

Pode especificar endereços IP fixos para os seus módulos IoT Edge através da secção K8s-experimental, conforme descrito abaixo: 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Exponha o serviço Kubernetes como serviço IP de cluster para comunicação interna

#### <a name="problem-description"></a>Descrição do problema

Por predefinição, o tipo de serviço IoT é de balanceador de carga tipo e atribuído externamente a endereços IP virados para o exterior. Pode não querer um endereço IP virado para o exterior para a sua aplicação. Pode ser necessário expor as cápsulas dentro do cluster KUbernetes para acesso como outras cápsulas e não como um serviço de balançador de carga exposto externamente. 

#### <a name="suggested-solution"></a>Solução sugerida

Pode utilizar as opções de criação através da secção K8s-experimental. A seguinte opção de serviço deve funcionar com encadernações portuárias.

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```