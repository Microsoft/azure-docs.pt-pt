---
title: Conceitos de dispositivos no fornecimento de dispositivos Azure / Microsoft Docs
description: Descreve conceitos de aprovisionamento de dispositivos específicos de dispositivos com serviço de provisionamento de dispositivos (DPS) e Hub IoT
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285165"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub Device Provisioning Service device concepts (Conceitos de dispositivo do Serviço Aprovisionamento de Dispositivos no Hub IoT)

O Serviço de Provisionamento de Dispositivos IoT Hub é um serviço de ajuda para o IoT Hub que utiliza para configurar o fornecimento de dispositivos de toque zero a um hub IoT especificado. Com o Serviço de Aprovisionamento de Dispositivos, pode aprovisionar milhões de dispositivos de forma segura e dimensionável.

Este artigo dá uma visão geral dos conceitos do *dispositivo* envolvidos no fornecimento de dispositivos. Este artigo é mais relevante para as pessoas envolvidas na etapa de [fabrico](about-iot-dps.md#manufacturing-step) de obter um dispositivo pronto para a implantação.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestado é o método utilizado para confirmar a identidade de um dispositivo. O mecanismo de atestado também é relevante para a lista de inscrições, que indica ao serviço de provisionamento qual o método de atestado a utilizar com um determinado dispositivo.

> [!NOTE]
> O IoT Hub utiliza o "esquema de autenticação" para um conceito semelhante nesse serviço.

O Serviço de Provisionamento de Dispositivos suporta as seguintes formas de atestado:
* **Certificados X.509 baseados** no fluxo padrão de autenticação do certificado X.509.
* **Módulo de Plataforma Fidedigna (TPM)** baseado num desafio nonce, utilizando a norma TPM para as teclas apresentarem um símbolo assinado de Assinatura de Acesso Partilhado (SAS). Isto não requer um TPM físico no dispositivo, mas o serviço espera atestar a utilização da chave de averbamento de acordo com a [especificação TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chave simétrica** baseada em [fichas](../iot-hub/iot-hub-devguide-security.md#security-tokens)de segurança de assinatura de acesso partilhado (SAS), que incluem uma assinatura de hashed e uma expiração incorporada. Para mais informações, consulte o atestado da [chave simétrica](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro e baseado em hardware de segredos do dispositivo, e é a forma mais segura de armazenamento secreto. Tanto os certificados X.509 como as fichas SAS podem ser armazenados no HSM. Os HSMs podem ser utilizados com ambos os mecanismos de atestação que os suportes de serviço de fornecimento.

> [!TIP]
> Recomendamos vivamente a utilização de um HSM com dispositivos para armazenar segredos de forma segura nos seus dispositivos.

Os segredos do dispositivo também podem ser armazenados em software (memória), mas é uma forma de armazenamento menos segura do que um HSM.

## <a name="registration-id"></a>ID de registo

O ID de registo é utilizado para identificar de forma única um dispositivo no Serviço de Provisionamento de Dispositivos. O ID do dispositivo deve ser único no âmbito de [ID](#id-scope)do serviço de provisionamento . Cada dispositivo deve ter uma identificação de registo. O ID de registo é alfanumérico, caso insensível, e pode conter caracteres especiais, incluindo cólon, período, sublinhado e hífen.

* No caso da TPM, o ID de registo é fornecido pelo próprio TPM.
* No caso do atestado baseado em X.509, o ID de registo é fornecido como nome de assunto do certificado.

## <a name="device-id"></a>ID do dispositivo

O ID do dispositivo é o ID tal como aparece no IoT Hub. O ID do dispositivo pretendido pode ser definido na entrada de inscrição, mas não é necessário ser definido. A definição do ID do dispositivo desejado só é suportada em matrículas individuais. Se não for especificado o ID do dispositivo desejado na lista de inscrições, o ID de registo é utilizado como ID do dispositivo ao registar o dispositivo. Saiba mais sobre [iDs de dispositivo no IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Âmbito de identificação

O âmbito de identificação é atribuído a um Serviço de Provisionamento de Dispositivos quando é criado pelo utilizador e é utilizado para identificar de forma única o serviço de fornecimento específico através do qual o dispositivo irá registar. O âmbito de ID é gerado pelo serviço e é imutável, o que garante uma singularidade.

> [!NOTE]
> A singularidade é importante para operações de implantação a longo prazo e cenários de fusão e aquisição.
