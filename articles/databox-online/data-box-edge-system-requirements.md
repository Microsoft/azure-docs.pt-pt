---
title: Requisitos de sistema do Edge de caixa de dados do Microsoft Azure | Documentos da Microsoft
description: Saiba mais sobre o software e os requisitos de rede para a sua caixa de dados do Azure Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 90c60d586d505ca0c9bd787c37e137f7a38ee1f7
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996752"
---
# <a name="azure-data-box-edge-system-requirements"></a>Requisitos de sistema de borda de caixa de dados do Azure

Este artigo descreve os requisitos de sistema importante para a sua solução de borda de caixa de dados do Microsoft Azure e para os clientes conectados à borda de caixa de dados do Azure. Recomendamos que reveja as informações cuidadosamente antes de implementar o seu limite de caixa de dados. Pode consultar novamente a essas informações conforme necessário durante a implantação e operação subseqüente.

Os requisitos de sistema para o Edge de caixa de dados incluem:

- **Requisitos de software para anfitriões** -descreve as plataformas suportadas, browsers para a configuração local da interface do Usuário, os clientes do SMB e quaisquer requisitos adicionais para os clientes que aceder ao dispositivo.
- **Requisitos de rede para o dispositivo** -fornece informações sobre quaisquer requisitos de rede para a operação do dispositivo físico.

## <a name="supported-os-for-clients-connected-to-device"></a>SO suportado para os clientes ligados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos suportados para os clientes a aceder ao dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Contas de armazenamento suportadas

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Tipos de armazenamento suportadas

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Browsers suportados para a IU web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos de porta de rede

### <a name="port-requirements-for-data-box-edge"></a>Requisitos de portas para dados de caixa de borda

A tabela seguinte lista as portas que têm de ser aberta na firewall para permitir o tráfego de gestão, na cloud ou SMB. Nesta tabela, *no* ou *entrada* refere-se para a direção do que acesso de pedidos de cliente recebidos para o seu dispositivo. *Out* ou *saída* refere-se para a direção em que o dispositivo do Edge de caixa de dados envia dados externamente, além da implementação, por exemplo, com a Internet à saída.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisitos de portas para o IoT Edge

O Azure IoT Edge permite a comunicação de saída a partir de um dispositivo do Edge no local para a cloud do Azure através de protocolos suportados do IoT Hub. Comunicação de entrada só é necessário para cenários específicos em que o IoT Hub do Azure precisa para enviar mensagens para o dispositivo Azure IoT Edge (por exemplo, a Cloud para mensagens do dispositivo).

Utilize a seguinte tabela para a configuração da porta para os servidores que alojam o runtime do Azure IoT Edge:

| Porta não. | Dentro ou para fora | Âmbito de porta | Necessário | Orientação |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Saída       | WAN        | Sim      | Saída aberto para o aprovisionamento do IoT Edge. Esta configuração é necessária quando usar scripts manuais ou do Azure IoT dispositivo aprovisionamento DPS (serviço).|

Para obter informações completas, aceda a [Firewall e regras de configuração de porta para a implementação do IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede, muitas vezes, podem configurar regras de firewall avançado com base nos padrões de URL para filtrar a entrada e o tráfego de saída. O dispositivo de limite de caixa de dados e o serviço dependem de outros aplicativos da Microsoft, como o Azure Service Bus, controlo de acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a estas aplicações podem ser utilizados para configurar regras de firewall. É importante compreender que os padrões de URL associados a estas aplicações podem alterar. Estas alterações exigem que o administrador de rede monitorizar e atualizar as regras de firewall para o seu limite de caixa de dados como e quando necessário.

Recomendamos que defina as regras de firewall para tráfego de saída, com base no limite de caixa de dados fixa livremente os endereços IP, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir as regras de firewall avançado que são necessários para criar ambientes seguros.

> [!NOTE]
> - O dispositivo (origem) IPs deve ser sempre definido para todas as interfaces de rede com capacidade de nuvem.
> - O destino IPs deve ser definido como [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Padrões de URL para a funcionalidade de gateway

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Padrões de URL para o recurso de computação

| Padrão de URL                      | Componente ou funcionalidade                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Registo de contentor da Microsoft (obrigatório)               |
| https://\*.azurecr.io                     | Registos de contentores de pessoal e de terceiros (opcionais) | 
| https://\*.azure-devices.net              | Acesso do IoT Hub (obrigatório)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Padrões de URL para o gateway para o Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Padrões de URL para computação para o Azure Government

| Padrão de URL                      | Componente ou funcionalidade                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Registo de contentor da Microsoft (obrigatório)               |
| https://\*.azure-devices.us              | Acesso do IoT Hub (obrigatório)           |
| https://\*.azurecr.us                    | Registos de contentores de pessoal e de terceiros (opcionais) | 

## <a name="internet-bandwidth"></a>Largura de banda de Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considerações de dimensionamento de computação

Utilize a sua experiência de desenvolvimento e teste a sua solução para garantir que existe capacidade suficiente no seu dispositivo do Edge de caixa de dados e obter o desempenho ideal do seu dispositivo.

Deve considerar os fatores incluem:

- **Especificações de contentor** -pensar sobre o seguinte.

    - O número de contentores é na carga de trabalho? Poderia ter muita contentores leves versus alguns aqueles de muitos recursos.
    - Quais são os recursos alocados a estes contentores versus o que são os recursos que estão sendo consumidos?
    - Como muitas camadas deve partilhar os contentores?
    - Existem contentores não utilizados? Um contentor de parada ainda ocupa de espaço em disco.
    - Em que idioma são os contentores escritos?
- **Tamanho dos dados processados** -a quantidade de dados serão seus contentores estar a processar? Estes dados consumirá espaço em disco ou os dados serão processados na memória?
- **Desempenho esperado** -quais são as características de desempenho pretendido da sua solução? 

Para compreender e otimizar o desempenho da sua solução, poderia usar:

- As métricas de computação disponíveis no portal do Azure. Aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **monitorização > métricas**. Examinar os **Edge de computação - utilização de memória** e **Edge de computação - percentagem de CPU** para compreender os recursos disponíveis e como são os recursos de introdução consumidos.
- Os comandos de monitoramento disponíveis por meio da interface do PowerShell do dispositivo, tais como:

    - `dkr` estatísticas para obter uma transmissão em direto de contentor (es) de estatísticas de utilização de recursos. O comando suporta da CPU, utilização da memória, limite de memória e métricas de e/s de rede.
    - `dkr system df` Para obter informações sobre a quantidade de espaço em disco utilizado. 
    - `dkr image [prune]` Para limpar as imagens não utilizadas e libertar espaço.
    - `dkr ps --size` Para ver o número aproximado de um contentor em execução. 

    Para obter mais informações sobre os comandos disponíveis, aceda a [monitorizar e resolver problemas de módulos de computação](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Por fim, certifique-se de que validar a sua solução no seu conjunto de dados e quantificar o desempenho do Edge de caixa de dados antes de implementar na produção.


## <a name="next-step"></a>Passo seguinte

- [Implementar o seu limite de caixa de dados do Azure](data-box-edge-deploy-prep.md)
