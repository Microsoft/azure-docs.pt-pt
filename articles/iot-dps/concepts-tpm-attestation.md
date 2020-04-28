---
title: Serviço de Provisionamento de Dispositivos Hub Azure IoT - Attestation TPM
description: Este artigo fornece uma visão geral conceptual do fluxo de atestado TPM utilizando o Serviço de Provisionamento de Dispositivos IoT (DPS).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 624171ffc10a06ac3089b6dceb1683c63c88dbda
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74975283"
---
# <a name="tpm-attestation"></a>Atestado de TPM

O Serviço de Provisionamento de Dispositivos IoT Hub é um serviço de ajuda para o IoT Hub que utiliza para configurar o fornecimento de dispositivos de toque zero a um hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, pode fornecer milhões de dispositivos de forma segura.

Este artigo descreve o processo de atesta de identidade ao utilizar um [TPM](./concepts-device.md). TPM significa Módulo de Plataforma Fidedigna e é um tipo de módulo de segurança de hardware (HSM). Este artigo assume que está a usar um TPM discreto, firmware ou integrado. Os TPMs emulados pelo software são adequados para prototipagem ou teste, mas não fornecem o mesmo nível de segurança que os TPMs discretos, firmware ou integrados. Não recomendamos a utilização de TPMs de software em produção. Para obter mais informações sobre tipos de TPMs, consulte [Uma Breve Introdução ao TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Este artigo só é relevante para dispositivos que utilizem TPM 2.0 com suporte de teclaHMac e suas chaves de averbamento. Não se trata de dispositivos que utilizem certificados X.509 para autenticação. TPM é um padrão ISO em toda a indústria do Grupo de Computação Fidedigno, e você pode ler mais sobre TPM na [especificação completa TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) ou a [especificação ISO/IEC 11889](https://www.iso.org/standard/66510.html). Este artigo também assume que você está familiarizado com os pares de chaves públicos e privados, e como eles são usados para encriptação.

Os SDKs do dispositivo de fornecimento de dispositivos tratam de tudo o que é descrito neste artigo para si. Não há necessidade de implementar algo adicional se estiver a utilizar os SDKs nos seus dispositivos. Este artigo ajuda-o a entender conceptualmente o que se passa com o seu chip de segurança TPM quando o seu dispositivo está disposto e por que é tão seguro.

## <a name="overview"></a>Descrição geral

Os TPMs usam algo chamado chave de apoio (EK) como a raiz segura da confiança. O EK é exclusivo do TPM e o transforma essencialmente em novo.

Há outro tipo de chave que os TPMs têm, chamado chave raiz de armazenamento (SRK). Um SRK pode ser gerado pelo proprietário do TPM depois de tomar posse do TPM. Tomar posse do TPM é a forma específica de dizer "alguém define uma senha no HSM." Se um dispositivo TPM for vendido a um novo proprietário, o novo proprietário pode tomar posse do TPM para gerar um novo SRK. A nova geração SRK garante que o anterior proprietário não pode usar o TPM. Como o SRK é exclusivo do proprietário do TPM, o SRK pode ser usado para selar dados no próprio TPM para esse proprietário. O SRK fornece uma caixa de areia para o proprietário armazenar as suas chaves e proporciona revogabilidade de acesso se o dispositivo ou TPM forem vendidos. É como mudar-se para uma nova casa: tomar posse é mudar as fechaduras das portas e destruir todos os móveis deixados pelos proprietários anteriores (SRK), mas não se pode mudar o endereço da casa (EK).

Uma vez configurado e pronto a utilizar um dispositivo, terá um EK e um SRK disponíveis para utilização.

![Tomar posse de um TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Uma nota sobre a tomada de posse do TPM: A posse de um TPM depende de muitas coisas, incluindo o fabricante tpm, o conjunto de ferramentas TPM que estão a ser utilizadas, e o dispositivo OS. Siga as instruções relevantes para o seu sistema para tomar posse.

O Serviço de Provisionamento de Dispositivos utiliza a parte pública do EK (EK_pub) para identificar e inscrever dispositivos. O fornecedor do dispositivo pode ler o EK_pub durante o fabrico ou testes finais e enviar o EK_pub para o serviço de provisionamento para que o dispositivo seja reconhecido quando se ligar à disposição. O Serviço de Provisionamento de Dispositivos não verifica o SRK ou o proprietário, pelo que "limpar" o TPM apaga os dados dos clientes, mas o EK (e outros dados do fornecedor) é preservado e o dispositivo continuará a ser reconhecido pelo Serviço de Fornecimento de Dispositivos quando se conecta à provisão.

## <a name="detailed-attestation-process"></a>Processo de atestação detalhado

Quando um dispositivo com um TPM se conecta pela primeira vez ao Serviço de Provisionamento de Dispositivos, o serviço verifica primeiro o EK_pub fornecido contra o EK_pub armazenado na lista de inscrições. Se o EK_pubs não corresponder, o dispositivo não está autorizado a fornecer. Se o EK_pubs corresponder, o serviço requer então que o dispositivo prove a propriedade da parte privada do EK através de um desafio nonce, que é um desafio seguro usado para provar a identidade. O Serviço de Provisionamento de Dispositivos gera um nonce e depois encripta-o com o SRK e, em seguida, o EK_pub, ambos fornecidos pelo dispositivo durante a chamada de registo inicial. O TPM mantém sempre a parte privada do EK segura. Isto impede a contrafação e assegura que as fichas SAS são aprovisionadas de forma segura a dispositivos autorizados.

Vamos percorrer o processo de atestado em detalhe.

### <a name="device-requests-an-iot-hub-assignment"></a>Dispositivo solicita uma atribuição do Hub IoT

Primeiro, o dispositivo liga-se ao Serviço de Provisionamento de Dispositivos e solicita a provisionamento. Ao fazê-lo, o dispositivo fornece ao serviço o seu ID de registo, um âmbito de identificação e o EK_pub e SRK_pub do TPM. O serviço passa o nonce encriptado de volta ao dispositivo e pede ao dispositivo para desencriptar o nonce e usá-lo para assinar um token SAS para voltar a ligar e terminar o fornecimento.

![Fornecimento de pedidos de dispositivos](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Desafio Nonce

O dispositivo pega no nonce e utiliza as partes privadas do EK e sRK para desencriptar o nonce para o TPM; a ordem dos delegados de encriptação nonce confia do EK, que é imutável, para o SRK, que pode mudar se um novo proprietário tomar posse do TPM.

![Desencriptando o nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Validar o nonce e receber credenciais

O dispositivo pode então assinar um token SAS utilizando o nonce desencriptado e restabelecer uma ligação ao Serviço de Provisionamento de Dispositivos utilizando o token SAS assinado. Com o desafio Nonce concluído, o serviço permite que o dispositivo aprovisione.

![Dispositivo restabelece ligação ao Serviço de Provisionamento de Dispositivos para validar a propriedade ek](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Passos seguintes

Agora o dispositivo liga-se ao IoT Hub e fica seguro sabendo que as chaves dos seus dispositivos estão armazenadas de forma segura. Agora que sabe como o Serviço de Provisionamento de Dispositivos verifica de forma segura a identidade de um dispositivo através do TPM, consulte os seguintes artigos para saber mais:

* [Conheça todos os conceitos de fornecimento automático](./concepts-auto-provisioning.md)
* [Começar a usar](./quick-setup-auto-provision.md) o fornecimento automático usando os SDKs para cuidar do fluxo.
