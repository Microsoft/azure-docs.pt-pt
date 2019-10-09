---
title: Conceitos de dispositivo no provisionamento de dispositivos do Azure | Microsoft Docs
description: Descreve os conceitos de provisionamento de dispositivos específicos para dispositivos com o serviço de provisionamento de dispositivos e o Hub IoT
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 8ea1be02dee0e0ef00010e8ac7a4dfb75eadbe96
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173390"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Conceitos de dispositivo do serviço de provisionamento de dispositivos no Hub IoT

O serviço de provisionamento de dispositivos no Hub IoT é um serviço auxiliar para o Hub IoT que você usa para configurar o provisionamento de dispositivos sem toque para um hub IoT especificado. Com o Serviço de Aprovisionamento de Dispositivos, pode aprovisionar milhões de dispositivos de forma segura e dimensionável.

Este artigo fornece uma visão geral dos conceitos de *dispositivo* envolvidos no provisionamento de dispositivos. Este artigo é mais relevante para as pessoas envolvidas na [etapa de fabricação](about-iot-dps.md#manufacturing-step) de preparar um dispositivo para implantação.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestado é o método usado para confirmar a identidade de um dispositivo. O mecanismo de atestado também é relevante para a lista de registro, que informa ao serviço de provisionamento qual método de atestado usar com um determinado dispositivo.

> [!NOTE]
> O Hub IoT usa "esquema de autenticação" para obter um conceito semelhante nesse serviço.

O serviço de provisionamento de dispositivos dá suporte às seguintes formas de atestado:
* **Certificados x. 509** com base no fluxo de autenticação de certificado x. 509 padrão.
* **Trusted Platform Module (TPM)** com base em um desafio de nonce, usando o padrão do TPM para que as chaves apresentem um token de assinatura de acesso compartilhado (SAS) assinado. Isso não requer um TPM físico no dispositivo, mas o serviço espera atestar usando a chave de endosso de acordo com a [especificação do TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chave simétrica** baseada em [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-tokens)SAS (assinatura de acesso compartilhado), que incluem uma assinatura com hash e uma expiração incorporada. Para obter mais informações, consulte [atestado de chave simétrica](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro baseado em hardware de segredos de dispositivo e é a forma mais segura de armazenamento secreto. Os certificados X. 509 e os tokens SAS podem ser armazenados no HSM. Os HSMs podem ser usados com ambos os mecanismos de atestado aos quais o serviço de provisionamento dá suporte.

> [!TIP]
> É altamente recomendável usar um HSM com dispositivos para armazenar segredos com segurança em seus dispositivos.

Os segredos do dispositivo também podem ser armazenados em software (memória), mas é uma forma menos segura de armazenamento do que um HSM.

## <a name="registration-id"></a>ID de registro

A ID de registro é usada para identificar exclusivamente um dispositivo no serviço de provisionamento de dispositivos. A ID de registro deve ser exclusiva no [escopo da ID](#id-scope)do serviço de provisionamento. Cada dispositivo deve ter uma ID de registro. A ID de registro é alfanumérica, em letras minúsculas e pode conter hifens.

* No caso do TPM, a ID de registro é fornecida pelo próprio TPM.
* No caso do atestado baseado em X. 509, a ID de registro é fornecida como o nome da entidade do certificado.

## <a name="device-id"></a>ID do dispositivo

A ID do dispositivo é a ID como ela aparece no Hub IoT. A ID de dispositivo desejada pode ser definida na entrada de registro, mas não é necessário defini-la. Se nenhuma ID de dispositivo desejada for especificada na lista de registro, a ID de registro será usada como a ID do dispositivo ao registrar o dispositivo. Saiba mais sobre as [IDs de dispositivo no Hub IOT](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Escopo da ID

O escopo de ID é atribuído a um serviço de provisionamento de dispositivos quando ele é criado pelo usuário e é usado para identificar exclusivamente o serviço de provisionamento específico pelo qual o dispositivo será registrado. O escopo de ID é gerado pelo serviço e é imutável, o que garante a exclusividade.

> [!NOTE]
> A exclusividade é importante para operações de implantação de longa execução e cenários de fusão e aquisição.
