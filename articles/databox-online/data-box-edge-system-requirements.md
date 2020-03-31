---
title: Requisitos do sistema Microsoft Azure Data Box Edge Microsoft Docs
description: Conheça os requisitos de software e networking para o seu Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260231"
---
# <a name="azure-data-box-edge-system-requirements"></a>Requisitos do sistema De Caixa de Dados Azure

Este artigo descreve os requisitos importantes do sistema para a sua solução Microsoft Azure Data Box Edge e para os clientes que se conectam ao Azure Data Box Edge. Recomendamos que reveja a informação cuidadosamente antes de implementar o seu Data Box Edge. Pode remeter para esta informação conforme necessário durante a implantação e posterior operação.

Os requisitos do sistema para o Limite da Caixa de Dados incluem:

- **Requisitos de software para anfitriões** - descreve as plataformas suportadas, navegadores para a configuração local UI, clientes SMB, e quaisquer requisitos adicionais para os clientes que acedem ao dispositivo.
- **Requisitos** de rede para o dispositivo - fornece informações sobre quaisquer requisitos de rede para o funcionamento do dispositivo físico.

## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operativo suportado para clientes ligados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos suportados para clientes que acedem ao dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Tipos de armazenamento suportados

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Navegadores suportados para UI web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos portuário de rede

### <a name="port-requirements-for-data-box-edge"></a>Requisitos de porta para Data Box Edge

A tabela seguinte lista as portas que precisam de ser abertas na sua firewall para permitir tráfego de SMB, nuvem ou gestão. Nesta tabela, *dentro* ou *à entrada* refere-se à direção a partir da qual o cliente que entra solicita acesso ao seu dispositivo. *out* or *out refere-se* à direção em que o seu dispositivo Data Box Edge envia dados externamente, para além da implementação, por exemplo, de saída para a internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisitos portuários para IoT Edge

O Azure IoT Edge permite a comunicação de saída de um dispositivo Edge no local para a nuvem Azure utilizando protocolos IoT Hub suportados. A comunicação de entrada só é necessária para cenários específicos em que o Hub Azure IoT precisa de empurrar mensagens para o dispositivo Azure IoT Edge (por exemplo, mensagens Cloud To Device).

Utilize a tabela seguinte para a configuração da porta para os servidores que hospedam o tempo de execução do Edge Azure IoT:

| Porto nº. | Dentro ou fora | Âmbito do porto | Necessário | Orientação |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Saída       | WAN        | Sim      | Outbound aberto para fornecimento de IoT Edge. Esta configuração é necessária quando se utilizam scripts manuais ou serviço de provisionamento de dispositivos Azure IoT (DPS).|

Para obter informações completas, vá às regras de [configuração da Firewall e da porta para a implementação do IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede podem configurar frequentemente regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e saída. O seu dispositivo Data Box Edge e o serviço dependem de outras aplicações da Microsoft, como o Azure Service Bus, o Azure Ative Directory Access Control, as contas de armazenamento e os servidores microsoft Update. Os padrões de URL associados a estas aplicações podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a estas aplicações podem mudar. Estas alterações exigem que o administrador de rede monitorize e atualize as regras de firewall para o seu Data Box Edge conforme e quando necessário.

Recomendamos que estabeleça as suas regras de firewall para tráfego de saída, com base em endereços IP fixos Data Box Edge, liberalmente na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (fonte) devem ser sempre definidos para todas as interfaces de rede ativadas pela nuvem.
> - Os IPs de destino devem ser definidos para [as gamas IP](https://www.microsoft.com/download/confirmation.aspx?id=41653)do centro de dados Azure .

### <a name="url-patterns-for-gateway-feature"></a>Padrões de URL para recurso de gateway

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Padrões de URL para recurso computacional

| Padrão URL                      | Componente ou funcionalidade                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Registo de contentores da Microsoft (obrigatório)               |
| https://\*.azurecr.io                     | Registos pessoais e de terceiros (opcional) | 
| https://\*.azure-devices.net              | Acesso ioT Hub (obrigatório)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Padrões de URL para gateway para Governo Azure

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Padrões de URL para computação para governo Azure

| Padrão URL                      | Componente ou funcionalidade                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Registo de contentores da Microsoft (obrigatório)               |
| https://\*.azure-devices.us              | Acesso ioT Hub (obrigatório)           |
| https://\*.azurecr.us                    | Registos pessoais e de terceiros (opcional) | 

## <a name="internet-bandwidth"></a>Largura de banda da Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considerações de dimensionamento de cálculo

Use a sua experiência enquanto desenvolve e testa a sua solução para garantir que há capacidade suficiente no seu dispositivo Data Box Edge e obtém o desempenho ideal do seu dispositivo.

Os fatores que deve considerar incluem:

- **Detalhes** do recipiente - Pense no seguinte.

    - Quantos contentores têm na sua carga de trabalho? Pode saqueá-lo com muitos recipientes leves contra alguns intensivos de recursos.
    - Quais são os recursos atribuídos a estes contentores contra quais são os recursos que estão a consumir?
    - Quantas camadas os seus contentores partilham?
    - Há contentores não utilizados? Um contentor parado ainda ocupa o espaço do disco.
    - Em que língua estão escritos os seus recipientes?
- **Tamanho dos dados tratados** - Quantos dados os seus contentores vão ser processados? Estes dados consumirão espaço em disco ou os dados serão processados na memória?
- **Desempenho esperado** - Quais são as características de desempenho desejadas da sua solução? 

Para compreender e aperfeiçoar o desempenho da sua solução, pode utilizar:

- As métricas computadas disponíveis no portal Azure. Vá ao seu recurso Data Box Edge e depois vá para **a Monitorização > Métricas**. Veja o **cálculo Edge - Uso** da memória e **computação Edge - CPU percentual** para entender os recursos disponíveis e como os recursos estão a ser consumidos.
- Os comandos de monitorização disponíveis através da interface PowerShell do dispositivo tais como:

    - `dkrdbe stats`para obter um fluxo vivo de estatísticas de utilização de recursos de contentores. O comando suporta CPU, uso de memória, limite de memória e métricas iO da rede.
    - `dkrdbe system df`para obter informações sobre a quantidade de espaço em disco utilizado. 
    - `dkrdbe image prune`para limpar imagens não utilizadas e libertar espaço.
    - `dkrdbe ps --size`para ver o tamanho aproximado de um recipiente de corrida. 

    Para obter mais informações sobre os comandos disponíveis, vá ao Monitor e aos [módulos de computação de resolução de problemas.](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)

Por fim, certifique-se de que valida a sua solução no seu conjunto de dados e quantifica o desempenho no Data Box Edge antes de ser implementado em produção.


## <a name="next-step"></a>Passo seguinte

- [Implemente a sua borda de caixa de dados Azure](data-box-edge-deploy-prep.md)
