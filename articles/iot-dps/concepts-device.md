---
title: Conceitos de dispositivo no aprovisionamento de dispositivos do Azure | Documentos da Microsoft
description: Descreve os conceitos específicos a dispositivos com o serviço aprovisionamento de dispositivos e IoT Hub de aprovisionamento de dispositivos
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 2904da863707c5f653d774b0a480cc48c95c8d1c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049091"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Conceitos de dispositivo do IoT Hub serviço aprovisionamento de dispositivos

Serviço de aprovisionamento de dispositivo IoT Hub é um serviço auxiliar para o IoT Hub que utiliza para configurar dispositivos sem toques de aprovisionamento para um hub IoT especificado. Com o Serviço de Aprovisionamento de Dispositivos, pode aprovisionar milhões de dispositivos de forma segura e dimensionável.

Este artigo fornece uma visão geral do *dispositivo* conceitos envolvidos no aprovisionamento de dispositivos. Este artigo é mais relevante para as pessoas envolvidas na [manufacturing passo](about-iot-dps.md#manufacturing-step) de preparar para a implementação de um dispositivo.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestação é o método usado para confirmar a identidade de um dispositivo. O mecanismo de atestação também é relevante para a lista de inscrição, que informa ao serviço de aprovisionamento que método de atestado para utilizar com um determinado dispositivo.

> [!NOTE]
> O IoT Hub utiliza "esquema de autenticação" para um conceito semelhante desse serviço.

O serviço de aprovisionamento de dispositivos suporta os seguintes formulários do atestado de:
* **Certificados X.509** com base no fluxo de autenticação de certificado X.509 padrão.
* **Trusted Platform Module (TPM)** com base num desafio de valor de uso único, usando o padrão TPM para as chaves para apresentar um token de assinatura de acesso partilhado (SAS) assinado. Isso não requer que um TPM físico no dispositivo, mas espera que o serviço atestar a utilizar a chave de endossamento pela [especificação do TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chave simétrica** com base na assinatura de acesso partilhado (SAS) [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-tokens), que incluem uma assinatura com hash e uma expiração incorporada. Para obter mais informações, consulte [atestado de chave simétrico](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Módulo de hardware de segurança

O módulo de hardware de segurança, ou de um HSM, é utilizada para armazenamento seguro e baseado em hardware de segredos de dispositivo e é a forma mais segura de armazenamento secreta. Certificados X.509 e SAS tokens podem ser armazenados no HSM. HSMs podem ser utilizados com ambos os mecanismos de atestado o suporte do serviço de aprovisionamento.

> [!TIP]
> Recomendamos vivamente utilizar um HSM com dispositivos para armazenar com segurança segredos nos seus dispositivos.

Segredos do dispositivo também podem ser armazenados no software (memória), mas é uma forma menos segura do armazenamento de um HSM.

## <a name="registration-id"></a>ID de registo

O ID de registo é utilizado para identificar exclusivamente um dispositivo no serviço aprovisionamento de dispositivos. O ID do dispositivo tem de ser exclusivo no serviço de aprovisionamento [âmbito do ID](#id-scope). Cada dispositivo tem de ter um ID de registo. O ID de registo é de alfanuméricos, minúsculas e pode conter hífenes.

* No caso de TPM, o ID de registo é fornecido pelo próprio TPM.
* No caso de atestado baseados em X.509, o ID de registo é fornecido como o nome do requerente do certificado.

## <a name="device-id"></a>ID do dispositivo

O ID de dispositivo é o ID de como ele aparece no IoT Hub. O ID de dispositivo desejado pode ser definido na entrada de inscrição, mas não é necessário para ser definida. Se não for especificado nenhum ID de dispositivo desejado na lista de inscrição, o ID de registo é utilizado como o ID de dispositivo, ao registar o dispositivo. Saiba mais sobre [IDs do IoT Hub do dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Âmbito do ID

O âmbito do ID é atribuído a um serviço de aprovisionamento de dispositivos, quando ele é criado pelo utilizador e é usado para identificar exclusivamente o serviço de aprovisionamento específico, que o dispositivo será registado através do limite de tempo. O âmbito do ID é gerado pelo serviço e é imutável, que garante que é exclusivo.

> [!NOTE]
> Exclusividade é importante para operações de implementação de longa execução e cenários de fusão e aquisição.
