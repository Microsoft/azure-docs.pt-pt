---
title: Conceitos de dispositivo no provisionamento de dispositivos Azure / Microsoft Docs
description: Descreve conceitos de fornecimento de dispositivos específicos aos dispositivos com Serviço de Provisionamento de Dispositivos (DPS) e IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79285165"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub Device Provisioning Service device concepts (Conceitos de dispositivo do Serviço Aprovisionamento de Dispositivos no Hub IoT)

O IoT Hub Device Provisioning Service é um serviço de ajuda para o IoT Hub que utiliza para configurar o fornecimento de dispositivos de toque zero a um hub IoT especificado. Com o Serviço de Aprovisionamento de Dispositivos, pode aprovisionar milhões de dispositivos de forma segura e dimensionável.

Este artigo apresenta uma visão geral dos conceitos do *dispositivo* envolvidos no fornecimento de dispositivos. Este artigo é mais relevante para as personalidades envolvidas no passo de [fabrico](about-iot-dps.md#manufacturing-step) de obter um dispositivo pronto para a implementação.

## <a name="attestation-mechanism"></a>Mecanismo de atestação

O mecanismo de atestado é o método utilizado para confirmar a identidade de um dispositivo. O mecanismo de atestado também é relevante para a lista de inscrições, que indica ao serviço de fornecimento qual o método de atestado a utilizar com um determinado dispositivo.

> [!NOTE]
> O IoT Hub utiliza "esquema de autenticação" para um conceito semelhante nesse serviço.

O Serviço de Provisionamento de Dispositivos suporta as seguintes formas de atestado:
* **Certificados X.509** com base no fluxo padrão de autenticação de certificadoS X.509.
* **Módulo de Plataforma Fidedigna (TPM)** baseado num desafio de nonce, utilizando a norma TPM para as chaves apresentarem um token assinado assinatura de acesso partilhado (SAS). Isto não requer um TPM físico no dispositivo, mas o serviço espera atestar a utilização da chave de averbamento de acordo com a [especificação TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chave simétrica** baseada em [fichas de segurança](../iot-hub/iot-hub-devguide-security.md#security-tokens)de assinatura de acesso partilhado (SAS), que incluem uma assinatura hashed e uma expiração incorporada. Para obter mais informações, consulte [a estação de teclas simétricas.](concepts-symmetric-key-attestation.md)

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro e baseado em hardware de segredos do dispositivo, e é a forma mais segura de armazenamento secreto. Tanto os certificados X.509 como os tokens SAS podem ser armazenados no HSM. Os HSMs podem ser utilizados com ambos os mecanismos de atestação que o serviço de fornecimento suporta.

> [!TIP]
> Recomendamos vivamente a utilização de um HSM com dispositivos para armazenar segredos de forma segura nos seus dispositivos.

Os segredos do dispositivo também podem ser armazenados em software (memória), mas é uma forma de armazenamento menos segura do que um HSM.

## <a name="registration-id"></a>ID de inscrição

O ID de registo é utilizado para identificar de forma única um dispositivo no Serviço de Provisionamento de Dispositivos. O ID do dispositivo deve ser único no âmbito de [identificação](#id-scope)do serviço de fornecimento . Cada dispositivo deve ter uma identificação de registo. O ID de registo é alfanumérico, caso insensível, e pode conter caracteres especiais, incluindo cólon, período, sublinhado e hífen.

* No caso do TPM, o ID de registo é fornecido pelo próprio TPM.
* No caso do atestado baseado em X.509, o ID de registo é fornecido como nome de assunto do certificado.

## <a name="device-id"></a>ID do dispositivo

O ID do dispositivo é o ID tal como aparece no IoT Hub. O ID do dispositivo pretendido pode ser definido na entrada de inscrição, mas não é necessário definir. A definição do ID do dispositivo pretendido só é suportada em matrículas individuais. Se não for especificado nenhum ID do dispositivo desejado na lista de inscrição, o ID de registo é utilizado como ID do dispositivo ao registar o dispositivo. Saiba mais sobre [os IDs do dispositivo no IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Âmbito de ID

O âmbito de identificação é atribuído a um Serviço de Provisionamento de Dispositivos quando é criado pelo utilizador e é utilizado para identificar de forma única o serviço específico de fornecimento através do qual o dispositivo irá registar.. O âmbito de identificação é gerado pelo serviço e é imutável, o que garante a singularidade.

> [!NOTE]
> A singularidade é importante para operações de implantação de longa duração e cenários de fusão e aquisição.
