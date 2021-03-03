---
title: Visão geral da segurança de Azure Percept
description: Saiba mais sobre a segurança da Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: b5a345139114842c83cb1f11792076efb1461870
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663715"
---
# <a name="azure-percept-security-overview"></a>Visão geral da segurança de Azure Percept

Os dispositivos Azure Percept DK são projetados com uma raiz de hardware de confiança - segurança incorporada em todos os dispositivos. Ajuda a proteger sensores sensíveis à privacidade, como câmaras e microfones, dados de inferência e permite a autenticação e autorização do dispositivo para os serviços do Azure Percept Studio.

> [!NOTE]
> O Azure Percept DK destina-se a ser utilizado em ambientes de desenvolvimento e teste e para cenários de prova de conceito.

## <a name="devices"></a>Dispositivos

### <a name="azure-percept-dk"></a>Azure Percept DK

O Azure Percept DK inclui uma versão 2.0 do Módulo de Plataforma Fidedigna (TPM) que pode ser utilizada para ligar o dispositivo de forma segura aos Serviços de Provisionamento de Dispositivos Azure. TPM é um padrão ISO em toda a indústria do Grupo de Computação Fidedigna, e você pode ler mais sobre TPM na [especificação completa de TPM 2.0](https://trustedcomputinggroup.org/resource/tpm-library-specification/) ou na especificação ISO/IEC 11889. Para obter mais informações sobre como o DPS pode fornecer dispositivos de forma segura consulte [o Serviço de Provisionamento de Dispositivos Azure IoT Hub - Atestado TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation).

### <a name="azure-percept-system-on-module-som"></a>Sistema Azure Percept no módulo (SOM)

O sistema ativado pela visão Azure Percept DK no módulo (SOM) e o acessório som áudio Azure Percept incluem uma Unidade de MicroControlador (MCU) para proteger o acesso aos sensores de IA incorporados. Em cada arranque, o firmware MCU autentica e autoriza o acelerador de IA com os serviços Azure Percept Studio utilizando a arquitetura do Motor de Composição do Identificador de Dispositivos (DICE). A DICE funciona dividindo o arranque em camadas e criando segredos únicos para cada camada e configuração com base num Exclusivo Dispositivo Secreto (UDS). Se um código ou configuração diferente for iniciado, em qualquer ponto da cadeia, os segredos serão diferentes. Pode ler mais sobre DICE na [especificação](https://trustedcomputinggroup.org/work-groups/dice-architectures/)do grupo de trabalho DICE . Para configurar o acesso ao Azure Percept Studio e os serviços necessários consulte as **firewalls configurantes para Azure Percept DK** abaixo.

Os dispositivos Azure Percept utilizam a confiança de raiz de hardware para garantir o firmware. A ROM de arranque garante a integridade do firmware entre rom e o carregador do sistema operativo (OS) que, por sua vez, garante a integridade dos outros componentes do software criando uma cadeia de confiança.

## <a name="services"></a>Serviços

### <a name="iot-edge"></a>IoT Edge

A Azure Percept DK conecta-se seguramente ao Azure Percept Studio e a outros serviços Azure utilizando o protocolo de Segurança da Camada de Transporte (TLS). Azure Percept DK é um dispositivo azure IoT Edge habilitado. O tempo de execução IoT Edge é uma coleção de programas que transformam um dispositivo num dispositivo IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para funcionar na borda e comunicar os resultados. A Azure Percept DK utiliza recipientes Docker para isolar cargas de trabalho IoT Edge do sistema operativo do anfitrião e aplicações ativadas pela borda. Para obter mais informações sobre o quadro de segurança Azure IoT Edge, leia sobre o [gestor de segurança IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager?view=iotedge-2018-06).

### <a name="device-update-for-iot-hub"></a>Atualização do dispositivo para ioT hub

A Atualização do Dispositivo para O Hub IoT permite uma atualização segura, escalável e fiável no ar que traz segurança renovável aos dispositivos Azure Percept. Fornece controlos de gestão ricos e atualiza a conformidade através de insights. O Azure Percept DK inclui uma solução de atualização de dispositivos pré-integrada que fornece uma atualização resiliente (A/B) do firmware às camadas de SO.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Configurar firewalls para Azure Percept DK

Se a sua configuração de rede exigir que permita explicitamente ligações feitas a partir de dispositivos Azure Percept DK, reveja a seguinte lista de componentes.

Esta lista de verificação é um ponto de partida para as regras de firewall:

|URL (* = wildcard) |Portas TCP de saída|    Utilização|
|-------------------|------------------|---------|
|*.auth.azureperceptdk.azure.net|   443|    Autenticação e Autorização Azure DK SOM|
|*.auth.projectsantacruz.azure.net| 443|    Autenticação e Autorização Azure DK SOM|

Além disso, reveja a lista de [ligações utilizadas pela Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist?view=iotedge-2018-06#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Recomendações adicionais de implantação à produção

Azure Percept DK oferece uma grande variedade de capacidades de segurança fora da caixa. Além das poderosas funcionalidades de segurança incluídas na versão atual, a Microsoft também sugere as seguintes orientações ao considerar as implementações de produção:

- Forte proteção física do próprio dispositivo
- Garantir que a encriptação de dados em repouso está ativada
- Monitorização contínua da postura do dispositivo e resposta rápida aos alertas
- Limitação do número de administradores que têm acesso ao dispositivo

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [modelos Azure Percept AI](./overview-ai-models.md)disponíveis.