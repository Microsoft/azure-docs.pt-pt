---
title: Os requisitos do sistema Microsoft Azure Data Box Gateway| Microsoft Docs
description: Conheça os requisitos de software e networking para o seu Gateway Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: e7c8653b39a3e0333ff6e98783a6e9a1437dba22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739216"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Requisitos do sistema Azure Data Box Gateway

Este artigo descreve os requisitos importantes do sistema para a sua solução Microsoft Azure Data Box Gateway e para os clientes que se ligam ao Azure Data Box Gateway. Recomendamos que reveja as informações cuidadosamente antes de implementar o seu Gateway de Caixa de Dados e, em seguida, volte a fazê-lo conforme necessário durante a implementação e posterior funcionamento. 

Os requisitos do sistema para o dispositivo virtual Data Box Gateway incluem:

- **Requisitos de software para anfitriões** - descreve as plataformas suportadas, navegadores para a configuração local UI, clientes SMB e quaisquer requisitos adicionais para os anfitriões que se conectam ao dispositivo.
- **Requisitos de rede para o dispositivo** - fornece informações sobre quaisquer requisitos de rede para o funcionamento do dispositivo virtual.


## <a name="specifications-for-the-virtual-device"></a>Especificações para o dispositivo virtual

O sistema de anfitrião subjacente ao Data Box Gateway é capaz de dedicar os seguintes recursos à prestação do seu dispositivo virtual:

| Especificações                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |
| Memória  | Mínimo de 8 GB. Recomendamos vivamente pelo menos 16 GB. |
| Disponibilidade|Nó único|
| Discos| Disco do SO: 250 GB <br> Disco de dados: mínimo de 2 TB, com aprovisionamento dinâmico e tem de ser apoiado por SSDs|
| Interfaces de rede|1 ou mais interfaces de rede virtual|


## <a name="supported-os-for-clients-connected-to-device"></a>SISTEMA suportado para clientes ligados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos suportados para clientes que acedem ao dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Plataformas de virtualização suportadas para dispositivos

| **Sistema operativo/plataforma**  |**Versions** (Versões)   |**Notas**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6,0 <br> 6.5 <br> 6.7       |As ferramentas VMware não são suportadas.         |


## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Tipos de armazenamento suportados

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Navegadores suportados para uI web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos portuários de rede

A tabela que se segue lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego de SMB, nuvem ou gestão. Nesta tabela, *dentro* ou *entrada* refere-se à direção a partir da qual o cliente que chega solicita o acesso ao seu dispositivo. *Para* *fora* ou para fora refere-se à direção em que o seu dispositivo Data Box Gateway envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede podem frequentemente configurar regras avançadas de firewall com base nos padrões de URL para filtrar a entrada e o tráfego de saída. O seu dispositivo Data Box Gateway e o serviço Data Box Gateway dependem de outras aplicações da Microsoft, tais como Azure Service Bus, Azure Ative Directory Access Control, contas de armazenamento e servidores Microsoft Update. Os padrões de URL associados a estas aplicações podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a estas aplicações podem mudar. Isto, por sua vez, exigirá que o administrador da rede monitorize e atualize as regras de firewall para o seu Data Box Gateway conforme e quando necessário.

Recomendamos que estabeleça as suas regras de firewall para tráfego de saída, com base em endereços IP fixos data box Gateway, liberalmente na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (fonte) devem ser sempre definidos para todas as interfaces de rede ativadas pela nuvem.
> - Os IPs de destino devem ser definidos para [as gamas IP do datacenter Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Padrões de URL para o Governo de Azure

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Largura de banda da Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Passo seguinte

* [Implemente o seu Gateway de Caixa de Dados Azure](data-box-gateway-deploy-prep.md)

