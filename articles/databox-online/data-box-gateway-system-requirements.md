---
title: Requisitos de sistema do Gateway de caixa de dados do Microsoft Azure | Documentos da Microsoft
description: Saiba mais sobre o software e os requisitos de rede para o Gateway de caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: 73f97128dc7b71179c0b0ab41340af6c0f371da1
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402971"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Requisitos de sistema de Gateway de caixa de dados do Azure

Este artigo descreve os requisitos de sistema importante para a sua solução de Gateway de caixa de dados do Microsoft Azure e para os clientes ligar ao Gateway de caixa de dados do Azure. Recomendamos que reveja as informações cuidadosamente antes de implementar o Gateway de caixa de dados e, em seguida, regressar mais tarde a ela conforme necessário durante a implantação e operação subseqüente.

Os requisitos de sistema para o dispositivo virtual de Gateway de caixa de dados incluem:

- **Requisitos de software para anfitriões** -descreve as plataformas suportadas, browsers para a configuração local da interface do Usuário, os clientes do SMB e quaisquer requisitos adicionais para os anfitriões que se ligam ao dispositivo.
- **Requisitos de rede para o dispositivo** -fornece informações sobre quaisquer requisitos de rede para a operação do dispositivo virtual.


## <a name="specifications-for-the-virtual-device"></a>Especificações para o dispositivo virtual

O sistema anfitrião subjacente para o Gateway de caixa de dados é capaz de dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual:

| Especificações                                          | Descrição              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |            
| Memória  | Mínimo de 8 GB|
| Disponibilidade|Nó único|
| Discos| Disco do SO: 250 GB <br> Disco de dados: Mínimo de 2 TB, fino aprovisionado e tem de ser apoiado por SSDs|
| Interfaces de rede|1 ou mais interfaces de rede virtual|


## <a name="supported-os-for-clients-connected-to-device"></a>SO suportado para os clientes ligados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos suportados para os clientes a aceder ao dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Plataformas de Virtualização suportado para o dispositivo

| **Sistema operativo/plataforma**  |**Versões**   |**Notas**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |As ferramentas VMware não são suportadas.         |


## <a name="supported-storage-accounts"></a>Contas de armazenamento suportadas

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Tipos de armazenamento suportadas

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Browsers suportados para a IU web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos de porta de rede

A tabela seguinte lista as portas que têm de ser aberta na firewall para permitir o tráfego de gestão, na cloud ou SMB. Nesta tabela, *no* ou *entrada* refere-se para a direção do que acesso de pedidos de cliente recebidos para o seu dispositivo. *Out* ou *saída* refere-se para a direção em que o seu dispositivo de Gateway de caixa de dados envia dados externamente, além da implementação: por exemplo, a saída à Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede, muitas vezes, podem configurar regras de firewall avançado com base nos padrões de URL para filtrar a entrada e o tráfego de saída. O dispositivo de Gateway de caixa de dados e o serviço de Gateway de caixa de dados dependem de outras aplicações da Microsoft, como o Azure Service Bus, controlo de acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a estas aplicações podem ser utilizados para configurar regras de firewall. É importante compreender que os padrões de URL associados a estas aplicações podem alterar. Isso por sua vez, irá exigir o administrador de rede monitorizar e atualizar as regras de firewall para o seu Gateway de caixa de dados como e quando necessário.

Recomendamos que defina as regras de firewall para tráfego de saída, com base no Gateway de caixa de dados fixa livremente os endereços IP, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir as regras de firewall avançado que são necessários para criar ambientes seguros.

> [!NOTE]
> - O dispositivo (origem) IPs deve ser sempre definido para todas as interfaces de rede com capacidade de nuvem.
> - O destino IPs deve ser definido como [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Largura de banda de Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Passo seguinte

* [Implementar o Gateway de caixa de dados do Azure](data-box-gateway-deploy-prep.md)

