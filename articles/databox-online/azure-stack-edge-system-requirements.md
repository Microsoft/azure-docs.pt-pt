---
title: Requisitos do sistema Microsoft Azure Stack Edge Pro/ Microsoft Docs
description: Conheça os requisitos de software e networking para o seu Azure Stack Edge Pro
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: a1a0462b82629b16bd8ccbe269fb1a917357d542
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883504"
---
# <a name="azure-stack-edge-pro-system-requirements"></a>Requisitos do sistema Azure Stack Edge Pro

Este artigo descreve os requisitos importantes do sistema para a sua solução Microsoft Azure Stack Edge Pro e para os clientes que se ligam ao Azure Stack Edge Pro. Recomendamos que reveja as informações cuidadosamente antes de implementar o Azure Stack Edge Pro. Pode remeter para estas informações conforme necessário durante a implantação e posterior funcionamento.

Os requisitos do sistema para o Azure Stack Edge Pro incluem:

- **Requisitos de software para anfitriões** - descreve as plataformas suportadas, navegadores para a configuração local UI, clientes SMB e quaisquer requisitos adicionais para os clientes que acedem ao dispositivo.
- **Requisitos de rede para o dispositivo** - fornece informações sobre quaisquer requisitos de rede para o funcionamento do dispositivo físico.

## <a name="supported-os-for-clients-connected-to-device"></a>SISTEMA suportado para clientes ligados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos suportados para clientes que acedem ao dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Tipos de armazenamento suportados

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Navegadores suportados para uI web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos portuários de rede

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Requisitos portuários para Azure Stack Edge Pro

A tabela que se segue lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego de SMB, nuvem ou gestão. Nesta tabela, *dentro* ou *entrada* refere-se à direção a partir da qual o cliente que chega solicita o acesso ao seu dispositivo. *Para* *fora* ou para fora refere-se à direção em que o seu dispositivo Azure Stack Edge Pro envia dados externamente, para além da implementação, por exemplo, de saída para a internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisitos portuários para ioT Edge

O Azure IoT Edge permite a comunicação de saída de um dispositivo Edge no local para a nuvem Azure utilizando protocolos IoT Hub suportados. A comunicação de entrada só é necessária para cenários específicos onde o Azure IoT Hub precisa de enviar mensagens para o dispositivo Azure IoT Edge (por exemplo, mensagens Cloud To Device).

Utilize a seguinte tabela para configuração de porta para os servidores que hospedam o tempo de execução Azure IoT Edge:

| Porto não. | Dentro ou fora | Âmbito portuário | Necessário | Orientação |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Saída       | WAN        | Yes      | Saída aberta para provisão IoT Edge. Esta configuração é necessária quando se utilizam scripts manuais ou serviço de provisionamento de dispositivos Azure IoT (DPS).|

Para obter informações completas, aceda às [regras de configuração de Firewall e porta para a implementação do IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede podem frequentemente configurar regras avançadas de firewall com base nos padrões de URL para filtrar a entrada e o tráfego de saída. O seu dispositivo Azure Stack Edge Pro e o serviço dependem de outras aplicações da Microsoft, tais como Azure Service Bus, Azure Ative Directory Access Control, contas de armazenamento e servidores Microsoft Update. Os padrões de URL associados a estas aplicações podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a estas aplicações podem mudar. Estas alterações requerem que o administrador da rede monitorize e atualize as regras de firewall para o seu Azure Stack Edge Pro conforme e quando necessário.

Recomendamos que estabeleça as suas regras de firewall para tráfego de saída, com base em endereços IP fixos Azure Stack Edge Pro, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (fonte) devem ser sempre definidos para todas as interfaces de rede ativadas pela nuvem.
> - Os IPs de destino devem ser definidos para [as gamas IP do datacenter Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Padrões de URL para recurso de gateway

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Padrões de URL para recurso de computação

| Padrão url                      | Componente ou funcionalidade                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /mcr.microsoft.com<br></br>https:// \* .cdn.mscr.io | Registo de contentores da Microsoft (obrigatório)               |
| https:// \* .azurecr.io                     | Registos pessoais e de contentores de terceiros (opcional) | 
| https:// \* .azure-devices.net              | Acesso ao Hub IoT (obrigatório)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Padrões de URL para porta de entrada para o Governo de Azure

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Padrões de URL para computação para o Governo de Azure

| Padrão url                      | Componente ou funcionalidade                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /mcr.microsoft.com<br></br>https:// \* .cdn.mscr.com | Registo de contentores da Microsoft (obrigatório)               |
| https:// \* .azure-devices.us              | Acesso ao Hub IoT (obrigatório)           |
| https:// \* .azurecr.us                    | Registos pessoais e de contentores de terceiros (opcional) | 

## <a name="internet-bandwidth"></a>Largura de banda da Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considerações de dimensionamento de cálculo

Use a sua experiência ao desenvolver e testar a sua solução para garantir que existe capacidade suficiente no seu dispositivo Azure Stack Edge Pro e obtém o melhor desempenho do seu dispositivo.

Os fatores que deve considerar incluem:

- **Especificidades do contentor** - Pense no seguinte.

    - Quantos contentores tem na sua carga de trabalho? Pode ter muitos recipientes leves contra alguns intensivos de recursos.
    - Quais são os recursos atribuídos a estes contentores contra quais são os recursos que estão a consumir?
    - Quantas camadas os seus contentores partilham?
    - Há contentores não usados? Um contentor parado ainda ocupa espaço no disco.
    - Em que língua estão os seus recipientes escritos?
- **Dimensão dos dados processados** - Quantos dados os seus contentores serão processados? Estes dados consumirão espaço em disco ou os dados serão processados na memória?
- **Desempenho esperado** - Quais são as características de desempenho desejadas da sua solução? 

Para compreender e aperfeiçoar o desempenho da sua solução, pode utilizar:

- As métricas de cálculo disponíveis no portal Azure. Vá ao seu recurso Azure Stack Edge e, em seguida, vá para **Monitoring > Metrics**. Veja o **compute Edge - Utilização da memória** e cálculo Edge - **Percentagem CPU** para entender os recursos disponíveis e como estão a ser consumidos os recursos.
- Os comandos de monitorização disponíveis através da interface PowerShell do dispositivo, tais como:

    - `dkr` estatísticas para obter uma transmissão ao vivo das estatísticas de utilização de recursos de contentores. O comando suporta CPU, utilização da memória, limite de memória e métricas de IO de rede.
    - `dkr system df` para obter informações sobre a quantidade de espaço em disco utilizado. 
    - `dkr image [prune]` para limpar imagens não usused e libertar espaço.
    - `dkr ps --size` para ver o tamanho aproximado de um recipiente de corrida. 

    Para obter mais informações sobre os comandos disponíveis, aceda aos [módulos de computação monitor e resolução de problemas.](azure-stack-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)

Por fim, certifique-se de que valida a sua solução no seu conjunto de dados e quantifique o desempenho no Azure Stack Edge Pro antes de ser implementado na produção.


## <a name="next-step"></a>Passo seguinte

- [Implemente o seu Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
