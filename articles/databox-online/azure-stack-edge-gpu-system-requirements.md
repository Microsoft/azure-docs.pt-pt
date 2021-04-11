---
title: Os requisitos do sistema Microsoft Azure Stack Edge| Microsoft Docs
description: Saiba mais sobre os requisitos do sistema para a sua solução Microsoft Azure Stack Edge e para os clientes que se conectam ao Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alkohli
ms.custom: contperf-fy21q3
ms.openlocfilehash: b6984f27ae0ae81c3bf8d98efcce5fb23909c36d
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226095"
---
# <a name="system-requirements-for-azure-stack-edge-pro-with-gpu"></a>Requisitos do sistema para Azure Stack Edge Pro com GPU 

Este artigo descreve os requisitos importantes do sistema para a sua solução GPU Microsoft Azure Stack Edge Pro e para os clientes que se conectam ao Azure Stack Edge Pro. Recomendamos que reveja as informações cuidadosamente antes de implementar o Azure Stack Edge Pro. Pode remeter para estas informações conforme necessário durante a implantação e posterior funcionamento.

Os requisitos do sistema para o Azure Stack Edge Pro incluem:

- **Requisitos de software para anfitriões** - descreve as plataformas suportadas, navegadores para a configuração local UI, clientes SMB e quaisquer requisitos adicionais para os clientes que acedem ao dispositivo.
- **Requisitos de rede para o dispositivo** - fornece informações sobre quaisquer requisitos de rede para o funcionamento do dispositivo físico.

## <a name="supported-os-for-clients-connected-to-device"></a>SISTEMA suportado para clientes ligados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos suportados para clientes que acedem ao dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-azure-storage-accounts"></a>Contas de armazenamento suportadas do Azure

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>Contas de armazenamento suportadas edge

As seguintes contas de armazenamento Edge são suportadas com a interface REST do dispositivo. As contas de armazenamento Edge são criadas no dispositivo. Para obter mais informações, consulte [as contas de armazenamento Edge.](azure-stack-edge-gpu-manage-storage-accounts.md#about-edge-storage-accounts)

|Tipo  |Conta de armazenamento  |Comentários  |
|---------|---------|---------|
|Standard     |GPv1: Blob de Bloco         |         |

*As bolhas de página e os ficheiros Azure não estão atualmente suportados.

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>Contas de armazenamento do Gestor de Recursos Azure

Estas contas de armazenamento são criadas através do dispositivo APIs local quando está a ligar-se ao Gestor de Recursos Azure local. São suportadas as seguintes contas de armazenamento:

|Tipo  |Conta de armazenamento  |Comentários  |
|---------|---------|---------|
|Standard     |GPv1: Blob de Bloco, Blob de Página        | O tipo SKU é Standard_LRS       |
|Premium     |GPv1: Blob de Bloco, Blob de Página        | O tipo SKU é Premium_LRS        |


## <a name="supported-storage-types"></a>Tipos de armazenamento suportados

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Navegadores suportados para uI web local

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos portuários de rede

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Requisitos portuários para Azure Stack Edge Pro

A tabela que se segue lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego de SMB, nuvem ou gestão. Nesta tabela, *dentro* ou *entrada* refere-se à direção a partir da qual o cliente que chega solicita o acesso ao seu dispositivo. *Para* *fora* ou para fora refere-se à direção em que o seu dispositivo Azure Stack Edge Pro envia dados externamente, para além da implementação, por exemplo, de saída para a internet.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisitos portuários para ioT Edge

O Azure IoT Edge permite a comunicação de saída de um dispositivo Edge no local para a nuvem Azure utilizando protocolos IoT Hub suportados. A comunicação de entrada só é necessária para cenários específicos onde o Azure IoT Hub precisa de enviar mensagens para o dispositivo Azure IoT Edge (por exemplo, mensagens Cloud To Device).

Utilize a seguinte tabela para configuração de porta para os servidores que hospedam o tempo de execução Azure IoT Edge:

| Porto não. | Dentro ou fora | Âmbito portuário | Necessário | Orientação |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Saída       | WAN        | Yes      | Saída aberta para provisão IoT Edge. Esta configuração é necessária quando se utilizam scripts manuais ou serviço de provisionamento de dispositivos Azure IoT (DPS).|

Para obter informações completas, aceda às [regras de configuração de Firewall e porta para a implementação do IoT Edge](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede podem frequentemente configurar regras avançadas de firewall com base nos padrões de URL para filtrar a entrada e o tráfego de saída. O seu dispositivo Azure Stack Edge Pro e o serviço dependem de outras aplicações da Microsoft, tais como Azure Service Bus, Azure Ative Directory Access Control, contas de armazenamento e servidores Microsoft Update. Os padrões de URL associados a estas aplicações podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a estas aplicações podem mudar. Estas alterações requerem que o administrador da rede monitorize e atualize as regras de firewall para o seu Azure Stack Edge Pro conforme e quando necessário.

Recomendamos que estabeleça as suas regras de firewall para tráfego de saída, com base em endereços IP fixos Azure Stack Edge Pro, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (fonte) devem ser sempre definidos para todas as interfaces de rede ativadas pela nuvem.
> - Os IPs de destino devem ser definidos para [as gamas IP do datacenter Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Padrões de URL para recurso de gateway

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Padrões de URL para recurso de computação

| Padrão url                      | Componente ou funcionalidade                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /mcr.microsoft.com<br></br>https:// \* .cdn.mscr.io | Registo de contentores da Microsoft (obrigatório)               |
| https:// \* .azurecr.io                     | Registos pessoais e de contentores de terceiros (opcional) | 
| https:// \* .azure-devices.net              | Acesso ao Hub IoT (obrigatório)                             | 

### <a name="url-patterns-for-monitoring"></a>Padrões de URL para monitorização

Adicione os seguintes padrões de URL para O Azure Monitor se estiver a utilizar a versão contentorizada do agente Log Analytics para o Linux.

| Padrão url | Porta | Componente ou funcionalidade |
|-------------|-------------|----------------------------|
| http:// \* ods.opinsights.azure.com | 443 | Ingestão de dados |
| http:// \* .oms.opinsights.azure.com | 443 | Suíte de Gestão de Operações (OMS) a bordo |
| http:// \* .dc.services.visualstudio.com | 443 | Telemetria de agente que usa Azure Public Cloud Application Insights |

Para obter mais informações, consulte [os requisitos de firewall da rede para monitorizar os conhecimentos dos recipientes](../azure-monitor/containers/container-insights-onboard.md#network-firewall-requirements).

### <a name="url-patterns-for-gateway-for-azure-government"></a>Padrões de URL para porta de entrada para o Governo de Azure

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Padrões de URL para computação para o Governo de Azure

| Padrão url                      | Componente ou funcionalidade                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /mcr.microsoft.com<br></br>https:// \* .cdn.mscr.com | Registo de contentores da Microsoft (obrigatório)               |
| https:// \* .azure-devices.us              | Acesso ao Hub IoT (obrigatório)           |
| https:// \* .azurecr.us                    | Registos pessoais e de contentores de terceiros (opcional) | 

### <a name="url-patterns-for-monitoring-for-azure-government"></a>Padrões de URL para monitorização do Governo de Azure

Adicione os seguintes padrões de URL para O Azure Monitor se estiver a utilizar a versão contentorizada do agente Log Analytics para o Linux.

| Padrão url | Porta | Componente ou funcionalidade |
|-------------|-------------|----------------------------|
| http:// \* ods.opinsights.azure.us | 443 | Ingestão de dados |
| http:// \* .oms.opinsights.azure.us | 443 | Suíte de Gestão de Operações (OMS) a bordo |
| http:// \* .dc.services.visualstudio.com | 443 | Telemetria de agente que usa Azure Public Cloud Application Insights |


## <a name="internet-bandwidth"></a>Largura de banda da Internet

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considerações de dimensionamento de cálculo

Use a sua experiência ao desenvolver e testar a sua solução para garantir que existe capacidade suficiente no seu dispositivo Azure Stack Edge Pro e obtém o melhor desempenho do seu dispositivo.

Os fatores que deve considerar incluem:

- **Especificidades do contentor** - Pense no seguinte.

    - Qual é a sua pegada de contentor? Quanta memória, armazenamento e CPU está a consumir o seu contentor?
    - Quantos contentores tem na sua carga de trabalho? Pode ter muitos recipientes leves contra alguns intensivos de recursos.
    - Quais são os recursos atribuídos a estes contentores contra quais são os recursos que estão a consumir (a pegada)?
    - Quantas camadas os seus contentores partilham? As imagens dos contentores são um conjunto de ficheiros organizados numa pilha de camadas. Para a sua imagem de recipiente, determine quantas camadas e respetivos tamanhos para calcular o consumo de recursos.
    - Há contentores não usados? Um contentor parado ainda ocupa espaço no disco.
    - Em que língua estão os seus recipientes escritos?
- **Dimensão dos dados processados** - Quantos dados os seus contentores serão processados? Estes dados consumirão espaço em disco ou os dados serão processados na memória?
- **Desempenho esperado** - Quais são as características de desempenho desejadas da sua solução? 

Para compreender e aperfeiçoar o desempenho da sua solução, pode utilizar:

- As métricas de cálculo disponíveis no portal Azure. Vá ao seu recurso Azure Stack Edge e, em seguida, vá para **Monitoring > Metrics**. Veja o **compute Edge - Utilização da memória** e cálculo Edge - **Percentagem CPU** para entender os recursos disponíveis e como estão a ser consumidos os recursos.
- Para monitorizar e resolver os módulos de computação, aceda às [questões de Debug Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Por fim, certifique-se de que valida a sua solução no seu conjunto de dados e quantifique o desempenho no Azure Stack Edge Pro antes de ser implementado na produção.

## <a name="next-step"></a>Passo seguinte

- [Implemente o seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)