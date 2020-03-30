---
title: Requisitos do sistema Microsoft Azure Data Box Gateway Microsoft Docs
description: Conheça os requisitos de software e networking para o seu Portal de Caixa de Dados Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 05/08/2019
ms.author: alkohli
ms.openlocfilehash: e8932097bcdef782b1a551d386c2872e02d8abfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260192"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Requisitos do sistema De Gateway de Caixa de Dados Azure

Este artigo descreve os requisitos importantes do sistema para a sua solução Microsoft Azure Data Box Gateway e para os clientes que se conectam ao Portal da Caixa de Dados Do Azure. Recomendamos que reveja cuidadosamente as informações antes de implementar o Portal da Caixa de Dados e, em seguida, remeta-a de volta para ela conforme necessário durante a implementação e posterior operação.

Os requisitos do sistema para o dispositivo virtual Data Box Gateway incluem:

- **Requisitos de software para anfitriões** - descreve as plataformas suportadas, navegadores para a configuração local UI, clientes SMB, e quaisquer requisitos adicionais para os anfitriões que se conectam ao dispositivo.
- **Requisitos** de networking para o dispositivo - fornece informações sobre quaisquer requisitos de rede para o funcionamento do dispositivo virtual.


## <a name="specifications-for-the-virtual-device"></a>Especificações para o dispositivo virtual

O sistema de hospedagem subjacente ao Portal da Caixa de Dados é capaz de dedicar os seguintes recursos para fornecer o seu dispositivo virtual:

| Especificações                                          | Descrição              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |
| Memória  | Mínimo de 8 GB|
| Disponibilidade|Nó único|
| Discos| Disco do SO: 250 GB <br> Disco de dados: mínimo de 2 TB, com aprovisionamento dinâmico e tem de ser apoiado por SSDs|
| Interfaces de rede|1 ou mais interfaces de rede virtual|


## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operativo suportado para clientes ligados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos suportados para clientes que acedem ao dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Plataformas de virtualização suportadas para dispositivo

| **Sistema/plataforma operacional**  |**Versões**   |**Notas**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |As ferramentas VMware não são suportadas.         |


## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Tipos de armazenamento suportados

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Navegadores suportados para UI web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos portuário de rede

A tabela seguinte lista as portas que precisam de ser abertas na sua firewall para permitir tráfego de SMB, nuvem ou gestão. Nesta tabela, *dentro* ou *à entrada* refere-se à direção a partir da qual o cliente que entra solicita acesso ao seu dispositivo. *out* or *out refere-se* à direção em que o seu dispositivo Data Box Gateway envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede podem configurar frequentemente regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e saída. O seu dispositivo Data Box Gateway e o serviço Data Box Gateway dependem de outras aplicações da Microsoft, tais como O Autocarro de Serviços Azure, Controlo de Acesso ativo do Diretório Azure, contas de armazenamento e servidores microsoft Update. Os padrões de URL associados a estas aplicações podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a estas aplicações podem mudar. Isto, por sua vez, exigirá que o administrador de rede monitorize e atualize as regras de firewall para o seu Portal de Data Box, conforme e quando necessário.

Recomendamos que estabeleça as suas regras de firewall para tráfego de saída, com base em endereços IP fixos data Box Gateway, liberalmente na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (fonte) devem ser sempre definidos para todas as interfaces de rede ativadas pela nuvem.
> - Os IPs de destino devem ser definidos para [as gamas IP](https://www.microsoft.com/download/confirmation.aspx?id=41653)do centro de dados Azure .

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Padrões de URL para governo azure

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Largura de banda da Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Passo seguinte

* [Implemente o seu Portal de Caixa de Dados Azure](data-box-gateway-deploy-prep.md)

