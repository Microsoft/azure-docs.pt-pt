---
title: Serviço de Provisionamento de Dispositivos Azure IoT Hub - Atestado TPM
description: Este artigo fornece uma visão geral conceptual do fluxo de atestado de TPM utilizando o Serviço de Provisionamento de Dispositivos IoT (DPS).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 12860629d78391ed271306daba29a51aeb326c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531598"
---
# <a name="tpm-attestation"></a>Atestado de TPM

O IoT Hub Device Provisioning Service é um serviço de ajuda para o IoT Hub que utiliza para configurar o fornecimento de dispositivos de toque zero a um hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, pode for-se a disponibilização de milhões de dispositivos de forma segura.

Este artigo descreve o processo de atestado de identidade quando se utiliza um Módulo de Plataforma Fidedigna (TPM). Um TPM é um tipo de módulo de segurança de hardware (HSM). Este artigo pressupõe que está a usar um TPM discreto, firmware ou integrado. Os TPMs emulados por software são adequados para prototipagem ou teste, mas não fornecem o mesmo nível de segurança que os TPMs discretos, firmware ou TPMs integrados. Não recomendamos a utilização de TPMs de software em produção. Para obter mais informações sobre tipos de TPMs, consulte [Uma Breve Introdução à TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Este artigo só é relevante para dispositivos que utilizem TPM 2.0 com suporte de chave HMAC e respetivas chaves de apoio. Não se trata de dispositivos que utilizem certificados X.509 para autenticação. TPM é um padrão ISO em toda a indústria do Grupo de Computação Fidedigna, e você pode ler mais sobre TPM na [especificação completa de TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) ou na [especificação ISO/IEC 11889](https://www.iso.org/standard/66510.html). Este artigo também assume que você está familiarizado com os pares de chaves públicos e privados, e como eles são usados para encriptação.

Os SDKs do dispositivo de fornecimento de dispositivos tratam tudo o que é descrito neste artigo para si. Não é necessário implementar algo adicional se estiver a utilizar os SDKs nos seus dispositivos. Este artigo ajuda-o a compreender conceptualmente o que se passa com o seu chip de segurança TPM quando o seu dispositivo fornece e porque é tão seguro.

## <a name="overview"></a>Descrição Geral

Os TPMs usam algo chamado chave de endosso (EK) como a raiz segura da confiança. O EK é exclusivo do TPM e mudá-lo essencialmente transforma o dispositivo num novo.

Há outro tipo de chave que os TPMs têm, chamado a chave raiz de armazenamento (SRK). Um SRK pode ser gerado pelo proprietário do TPM depois de tomar posse do TPM. Tomar posse do TPM é a maneira específica do TPM de dizer "alguém define uma senha no HSM." Se um dispositivo TPM for vendido a um novo proprietário, o novo proprietário pode tomar posse do TPM para gerar um novo SRK. A nova geração SRK garante que o anterior proprietário não pode usar o TPM. Como o SRK é exclusivo do proprietário do TPM, o SRK pode ser usado para selar dados no próprio TPM para esse proprietário. O SRK fornece uma caixa de areia para o proprietário armazenar as suas chaves e fornecer revólde o acesso se o dispositivo ou TPM for vendido. É como mudar-se para uma nova casa: tomar posse é mudar as fechaduras das portas e destruir todos os móveis deixados pelos anteriores proprietários (SRK), mas não se pode mudar a morada da casa (EK).

Uma vez configurado e pronto a ser utilizado um dispositivo, terá um EK e um SRK disponíveis para utilização.

![Tomar posse de um TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Uma nota sobre a apropriação do TPM: A apropriação de um TPM depende de muitas coisas, incluindo o fabricante de TPM, o conjunto de ferramentas TPM que estão a ser utilizadas, e o dispositivo OS. Siga as instruções relevantes para o seu sistema para tomar posse.

O Serviço de Provisionamento de Dispositivos utiliza a parte pública do EK (EK_pub) para identificar e inscrever dispositivos. O fornecedor do dispositivo pode ler o EK_pub durante o fabrico ou teste final e carregar a EK_pub para o serviço de fornecimento para que o dispositivo seja reconhecido quando se ligar à provisão. O Serviço de Provisionamento de Dispositivos não verifica o SRK ou o proprietário, pelo que a "compensação" do TPM apaga os dados dos clientes, mas o EK (e outros dados do fornecedor) é preservado e o dispositivo continuará a ser reconhecido pelo Serviço de Provisionamento de Dispositivos quando se ligar à provisão.

## <a name="detailed-attestation-process"></a>Processo de atestado detalhado

Quando um dispositivo com um TPM liga-se pela primeira vez ao Serviço de Provisionamento de Dispositivos, o serviço verifica primeiro o EK_pub fornecido contra o EK_pub armazenado na lista de inscrições. Se os EK_pubs não corresponderem, o aparelho não pode provisões. Se o EK_pubs corresponder, o serviço requer então que o dispositivo prove a propriedade da parte privada do EK através de um desafio nonce, que é um desafio seguro usado para provar a identidade. O Serviço de Provisionamento de Dispositivos gera um nonce e, em seguida, encripta-o com o SRK e, em seguida, o EK_pub, ambos fornecidos pelo dispositivo durante a chamada de registo inicial. O TPM mantém sempre a parte privada do EK segura. Isto evita a contrafação e garante que os tokens SAS são protegidos de forma segura aos dispositivos autorizados.

Vamos percorrer o processo de atestado em detalhe.

### <a name="device-requests-an-iot-hub-assignment"></a>Dispositivo solicita uma atribuição de IoT Hub

Em primeiro lugar, o dispositivo liga-se ao Serviço de Provisionamento de Dispositivos e solicita a prestação. Ao fazê-lo, o dispositivo fornece ao serviço o seu ID de registo, um âmbito de identificação, e o EK_pub e SRK_pub do TPM. O serviço transmite o nó encriptado de volta ao dispositivo e pede ao dispositivo para desencriptar o nonce e usá-lo para assinar um token SAS para ligar novamente e terminar o fornecimento.

![Pedidos de dispositivos](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Desafio Nonce

O dispositivo pega no nonce e utiliza as porções privadas do EK e SRK para desencriptar o nó nonce no TPM; a ordem de nonce encriptação delega confiança do EK, que é imutável, para o SRK, que pode mudar se um novo proprietário tomar posse do TPM.

![Desencriptando o nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Validar o nonce e receber credenciais

O dispositivo pode então assinar um token SAS utilizando o nonce desencriptado e restabelecer uma ligação ao Serviço de Provisionamento de Dispositivos utilizando o token SAS assinado. Com o desafio Nonce concluído, o serviço permite que o dispositivo o preveva.

![Dispositivo restabelece ligação ao Serviço de Provisionamento de Dispositivos para validar a propriedade da EK](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Passos seguintes

Agora, o dispositivo liga-se ao IoT Hub e mantém-se seguro, sabendo que as chaves dos seus dispositivos estão bem armazenadas. Agora que sabe como o Serviço de Provisionamento de Dispositivos verifica de forma segura a identidade de um dispositivo utilizando o TPM, consulte os seguintes artigos para saber mais:

* [Conheça os conceitos de provisionamento](about-iot-dps.md#provisioning-process)
* [Começa a utilizar](./quick-setup-auto-provision.md) o fornecimento automático utilizando os SDKs para cuidar do fluxo.
