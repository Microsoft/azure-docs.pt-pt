---
title: Azure Service Fabric networking as melhores práticas
description: Regras e considerações de design para gerir a conectividade da rede utilizando o Azure Service Fabric.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: de6a80f73a97a5732f00e10107fff2330038210a
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122021"
---
# <a name="networking"></a>Rede

À medida que cria e gere os clusters Azure Service Fabric, está a fornecer conectividade de rede para os seus nós e aplicações. Os recursos de rede incluem intervalos de endereços IP, redes virtuais, equilibradores de carga e grupos de segurança de rede. Neste artigo, aprenderá as melhores práticas para estes recursos.

Reveja [os Padrões de Rede de Tecidos de Serviço](./service-fabric-patterns-networking.md) Azure para aprender a criar clusters que utilizem as seguintes funcionalidades: Rede virtual ou sub-rede existentes, endereço IP público estático, equilibr de carga interno ou equilibrador de carga interno e externo.

## <a name="infrastructure-networking"></a>Rede de Infraestruturas
Maximize o desempenho da sua Máquina Virtual com rede acelerada, declarando ativação da propriedade *Denetworking ativada* no seu modelo de Gestor de Recursos, o seguinte corte é de um Conjunto de Escala de Máquina Virtual Configurar Configurações que permite networking acelerado:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
O cluster de tecido de serviço pode ser a provisionado em [Linux com Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md)e [Windows com Rede Acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md).

A Rede Acelerada é suportada para SKUs da Série de Máquinas Virtuais Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e Ms/Mms. O Accelerated Networking foi testado com sucesso utilizando o SKU Standard_DS8_v3 em 01/23/2019 para um Service Fabric Windows Cluster, e utilizando Standard_DS12_v2 em 01/29/2019 para um Cluster de Tecidos de Serviço Linux. Por favor, note que a rede acelerada requer pelo menos 4 vCPUs. 

Para ativar o networking acelerado num cluster de tecido de serviço existente, é necessário [primeiro escalar um cluster de tecido de serviço adicionando um Conjunto de Balança de Máquina Virtual,](./virtual-machine-scale-set-scale-node-type-scale-out.md)para executar o seguinte:
1. Provisionar um NodeType com rede acelerada ativada
2. Migrar os seus serviços e o seu estado para o NodeType provisionado com rede acelerada habilitado

A escala da infraestrutura é necessária para permitir a rede acelerada num cluster existente, pois permitir uma rede acelerada no local causaria tempo de inatividade, uma vez que requer que todas as máquinas virtuais num conjunto de disponibilidade sejam [paradas elocadas antes de permitir a ligação de rede acelerada em qualquer NIC existente.](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)

## <a name="cluster-networking"></a>Redes do Cluster

* Os clusters de tecido de serviço podem ser implantados numa rede virtual existente, seguindo os passos descritos nos [padrões de networking do Service Fabric](./service-fabric-patterns-networking.md).

* Os grupos de segurança da rede (NSGs) são recomendados para os tipos de nós para restringir o tráfego de entrada e saída ao seu cluster. Certifique-se de que as portas necessárias são abertas no NSG. 

* O tipo de nó primário, que contém os serviços do sistema Service Fabric, não precisa de ser exposto através do balançador de carga externo e pode ser exposto por um [equilibrador de carga interno](./service-fabric-patterns-networking.md#internal-only-load-balancer)

* Utilize um [endereço IP público estático](./service-fabric-patterns-networking.md#static-public-ip-address-1) para o seu cluster.

## <a name="network-security-rules"></a>Regras de Segurança de Rede

As regras básicas aqui são o mínimo para um bloqueio de segurança de um cluster de tecido de serviço gerido Azure. A não abertura das seguintes portas ou a aprovação do IP/URL impedirá o bom funcionamento do cluster e poderá não ser suportada. Com esta regra definida é estritamente necessário utilizar [atualizações automáticas](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)de imagem de SO, caso contrário, portas adicionais terão de ser abertas.

### <a name="inbound"></a>Entrada 
|Prioridade   |Name               |Porta        |Protocolo  |Origem             |Destino       |Ação   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |Permitir
|3910       |Cliente             |19000       |TCP       |Internet           |VirtualNetwork    |Permitir
|3920       |Cluster            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |Permitir
|3930       |Efémero          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |Permitir
|3940       |Aplicação        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |Permitir
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |Permitir
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |Negar
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |Negar
|3980       |Ponto de final personalizado    |80          |TCP       |Internet           |VirtualNetwork    |Permitir
|4100       |Entrada de Bloco      |443         |Qualquer       |Qualquer                |Qualquer               |Permitir

Mais informações sobre as regras de segurança de entrada:

* **Azure.** Esta porta é utilizada pelo Service Fabric Explorer para navegar e gerir o seu cluster, sendo também utilizada pelo Service Fabric Resource Provider para consultar informações sobre o seu cluster de forma a exibir no Portal de Gestão Azure. Se esta porta não estiver acessível a partir do Fornecedor de Recursos de Tecido de Serviço, verá uma mensagem como 'Nós Não Encontrados' ou 'UpgradeServiceNotReachable' no portal Azure e a sua lista de nós e de aplicações aparecerão vazias. Isto significa que, se deseja ter visibilidade do seu cluster no Portal de Gestão Azure, então o seu balanceador de carga deve expor um endereço IP público e o seu NSG deve permitir a entrada de tráfego de 19080.  

* **Cliente.** O ponto final de ligação ao cliente para APIs como REST/PowerShell/CLI. 

* **Cluster**. Utilizado para comunicação inter-nódoa; nunca deve ser bloqueado.

* **Efémero.** O Service Fabric utiliza uma parte destas portas como portas de aplicação, e os restantes estão disponíveis para o SO. Também mapeia esta gama para a gama existente presente no SO, para todos os efeitos, você pode usar as gamas dadas na amostra aqui. Certifique-se de que a diferença entre as portas de arranque e de extremidade é de, pelo menos, 255. Podes desatenção de conflitos se esta diferença for demasiado baixa, porque esta gama é partilhada com o SISTEMA. Para ver a gama dinâmica de portas configurada, executar *netsh int ipv4 show tcp dinâmico do porto.* Estas portas não são necessárias para os aglomerados Linux.

* **Aplicação**. A gama de portas de aplicação deve ser suficientemente grande para cobrir os requisitos do ponto final das suas aplicações. Esta gama deve ser exclusiva da gama de portas dinâmicas da máquina, ou seja, a gama efémera DePorts como definida na configuração. O Tecido de Serviço utiliza estas portas sempre que são necessárias novas portas e cuida de abrir a firewall para estas portas nos nós.

* **SMB**. Opcional, a versão de tempo de execução 7.1+ já não utiliza SMB por padrão. O protocolo SMB está em uso pelo serviço ImageStore para dois cenários. Esta porta é necessária para descarregar os pacotes da ImageStore pelos nós, bem como para os replicar entre as réplicas. 

* **PDR**. Opcional, se o PDR for necessário da Internet ou da VirtualNetwork para cenários de jumpbox. 

* **SSH.** Opcional, se o SSH for necessário da Internet ou da VirtualNetwork para cenários de jumpbox.

* **Ponto final personalizado.** Um exemplo para a sua aplicação para ativar um ponto final acessível à Internet.

### <a name="outbound"></a>Saída

|Prioridade   |Name               |Porta        |Protocolo  |Origem             |Destino       |Ação   
|---        |---                |---         |---       |---                |---               |---
|3900       |Rede            |Qualquer         |TCP       |VirtualNetwork     |VirtualNetwork    |Permitir
|3910       |Fornecedor de Recursos  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Permitir
|3920       |Atualizar            |443         |TCP       |VirtualNetwork     |Internet          |Permitir
|3950       |Bloco de Saída     |Qualquer         |Qualquer       |Qualquer                |Qualquer               |Negar

Mais informações sobre as regras de segurança de saída:

* **Rede**. Canal de comunicação para sub-redes e para outras redes virtuais.

* **Fornecedor de Recursos**. Ligação pelo UpgradeService para executar todas as implementações ARM pelo fornecedor de recursos de Tecido de Serviço.

* **Atualizar**. O serviço de atualização utilizando o endereço download.microsoft.com para obter os bits, este é necessário para configuração, re-imagem e atualizações de tempo de execução. O serviço funciona com endereços IP dinâmicos. No cenário de um equilibrador de carga "apenas interno", deve ser adicionado um equilibrador de carga externo adicional ao gabarito com uma regra que permite o tráfego de saída para a porta 443. Opcionalmente, esta porta pode ser bloqueada após uma configuração bem sucedida, mas neste caso o pacote de upgrade deve ser distribuído para os nós ou a porta tem que ser aberta por um curto período de tempo, depois é necessária uma atualização manual.

Utilize o Azure Firewall com [registo de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) e [análise de tráfego](../network-watcher/traffic-analytics.md) para rastrear problemas com o bloqueio de segurança. O tecido de serviço do modelo ARM [com NSG](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) é um bom exemplo para começar. 


## <a name="application-networking"></a>Rede de aplicações

* Para executar as cargas de trabalho dos contentores do Windows, utilize [o modo de rede aberto](./service-fabric-networking-modes.md#set-up-open-networking-mode) para facilitar a comunicação de serviço ao serviço.

* Utilize um representante inverso, como [o Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) ou o [proxy reverso do Tecido de Serviço,](./service-fabric-reverseproxy.md) para expor portas de aplicação comuns como 80 ou 443.

* Para os recipientes windows alojados em máquinas com lacunas de ar que não conseguem puxar camadas de base do armazenamento em nuvem Azure, sobreponsem o comportamento da camada estrangeira, utilizando a bandeira [de artefactos não-tristribuíveis](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) no daemon do Docker.

## <a name="next-steps"></a>Passos seguintes

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação de cluster de tecido de serviço para windows server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou computadores que executam o Linux: [Criar um cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
