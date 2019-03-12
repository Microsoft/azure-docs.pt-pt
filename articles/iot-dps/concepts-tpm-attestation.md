---
title: -Serviço de atestado de TPM de aprovisionamento de dispositivos no Hub IoT do Azure
description: Este artigo fornece uma descrição geral conceptual do fluxo de atestado de TPM com o serviço de aprovisionamento de dispositivos de IoT.
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: e4a86585fbf1e00512e9e8e111a9a259663f8a26
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536783"
---
# <a name="tpm-attestation"></a>Atestado de TPM

Serviço de aprovisionamento de dispositivo IoT Hub é um serviço auxiliar para o IoT Hub que utiliza para configurar dispositivos sem toques de aprovisionamento para um hub IoT especificado. Com o serviço de aprovisionamento de dispositivos, pode aprovisionar milhões de dispositivos de forma segura.

Este artigo descreve o processo de atestado de identidade, ao utilizar um [TPM](./concepts-device.md). TPM significa Trusted Platform Module e é um tipo de módulo de segurança de hardware (HSM). Este artigo pressupõe que está a utilizar um firmware discreto, ou integrado TPM. Software TPMs emulados são adequadas para criar protótipos ou testar, mas eles não fornecer o mesmo nível de segurança do que o firmware discreto, ou não TPMs integrados. Recomendamos que não utilize o software TPMs na produção. Para obter mais informações sobre os tipos de TPMs, consulte [uma breve introdução ao TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Este artigo é relevante apenas para dispositivos com o TPM 2.0 com suporte de chave do HMAC e as chaves de endossamento. Não é para utilizar certificados X.509 para autenticação de dispositivos. TPM é um nível da indústria, a norma ISO do Trusted Computing Group e pode ler mais sobre TPM no [especificação do TPM 2.0 completa](https://trustedcomputinggroup.org/tpm-library-specification/) ou o [especificação ISO/IEC 11889](https://www.iso.org/standard/66510.html). Este artigo também pressupõe que está familiarizado com pares de chaves públicas e privadas e como elas são usadas para a encriptação.

O dispositivo de serviço aprovisionamento de dispositivos SDKs lidar com tudo o que é descrito neste artigo para. Não é necessário para que implemente nada adicional, se estiver a utilizar os SDKs nos seus dispositivos. Este artigo ajuda-o a compreender conceitualmente, o que está acontecendo com o seu chip de segurança TPM quando Aprovisiona o dispositivo e por que isso é tão seguro.

## <a name="overview"></a>Descrição geral

Os TPMs usam algo chamado a chave de endossamento (EK) como a raiz de segura de confiança. O EK é exclusivo para o TPM e alterá-lo, essencialmente, altera o dispositivo para um novo.

Há outro tipo de chave que TPMs tiverem, a chamada a chave de raiz de armazenamento (SRK). Depois de demora de propriedade do TPM, um SRK pode ser gerado pelo proprietário do TPM. A propriedade do TPM é a forma de TPM específicas dizer "alguém define uma palavra-passe no HSM." Se um dispositivo TPM é vendido para um novo proprietário, o novo proprietário pode assumir a propriedade do TPM para gerar um novo SRK. A nova geração de SRK garante que o proprietário anterior não é possível utilizar o TPM. Como o SRK é exclusivo para o proprietário do TPM, o SRK pode ser utilizado para selar dados no TPM para que esse proprietário. O SRK fornece uma área de segurança para o proprietário armazenar as chaves e fornece acesso revocability se o dispositivo ou o TPM é vendido. É como mover para uma nova casa: a propriedade é alterar os cadeados nas portas e destruição de mobiliário de todos os restantes, os proprietários anteriores (SRK), mas não é possível alterar o endereço da casa (EK).

Assim que um dispositivo tiver sido configurado e pronto a utilizar, que pode ter um EK e um SRK disponível para utilização.

![A propriedade de um TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Uma observação sobre a propriedade do TPM: A propriedade de um TPM depende muitas coisas, incluindo o fabricante TPM, o conjunto de ferramentas TPM, que está a ser utilizado e o SO do dispositivo. Siga as instruções relevantes para o seu sistema para obter a propriedade.

O serviço de aprovisionamento de dispositivos utiliza a parte pública do EK (EK_pub) para identificar e inscrever dispositivos. Fabricante do dispositivo pode ler o EK_pub durante o fabricante ou testes finais e carregar o EK_pub ao serviço de aprovisionamento, para que o dispositivo será reconhecido quando se liga a aprovisionar. O serviço de aprovisionamento de dispositivos não verifica o SRK ou proprietário, para que "Limpar" o TPM apaga os dados dos clientes, mas o EK (e outros dados de fornecedor) são preservados e o dispositivo ainda será reconhecido pelo serviço de aprovisionamento de dispositivos quando se liga a aprovisionar.

## <a name="detailed-attestation-process"></a>Processo de atestado detalhadas

Quando um dispositivo com um TPM ligado pela primeira vez para o serviço de aprovisionamento de dispositivos, o serviço verifica primeiro o EK_pub fornecido contra EK_pub armazenados na lista de inscrição. Se o EK_pubs não corresponderem, o dispositivo não tem permissão para aprovisionar. Se o EK_pubs forem iguais, o serviço, em seguida, requer que o dispositivo para provar a propriedade da parte privada de EK por meio de um desafio de valor de uso único, que é um desafio seguro, usado para provar a identidade. O serviço de aprovisionamento de dispositivo gera um valor de uso único e, em seguida, criptografa com a SRK e, em seguida, EK_pub, que são fornecidos pelo dispositivo durante a chamada de registo inicial. O TPM sempre mantém a parte privada o EK seguros. Isso impede a falsificação e garante a SAS tokens com segurança são aprovisionados para dispositivos autorizados.

Vamos percorrer o processo de atestado em detalhes.

### <a name="device-requests-an-iot-hub-assignment"></a>Dispositivo solicitar uma atribuição de IoT Hub

Primeiro o dispositivo estabelece ligação ao serviço aprovisionamento de dispositivos e os pedidos de aprovisionamento. Ao fazer isso, o dispositivo fornece o serviço com o seu ID de registo, um âmbito de ID e o EK_pub e SRK_pub do TPM. O serviço transmite o valor de uso único encriptado novamente para o dispositivo e pede-lhe o dispositivo para desencriptar o valor de uso único e usá-lo para assinar um token SAS para ligar novamente e concluir o aprovisionamento.

![Pedidos de aprovisionamento de dispositivos](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Valor de uso único desafio

O dispositivo assume o valor de uso único e utiliza as partes privadas do EK e SRK para desencriptar o valor de uso único no TPM; a ordem de confiança de delegados de encriptação do nonce do EK, o que é imutável, para SRK, que pode alterar, se um novo proprietário assumirá a propriedade do TPM.

![Desencriptar o valor de uso único](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Validar o valor de uso único e receber as credenciais

O dispositivo, em seguida, pode inscrever-se um token SAS com o valor de uso único desencriptada e restabelecer uma ligação para o serviço de aprovisionamento de dispositivos com o token SAS assinado. Com o desafio de valor de uso único concluído, o serviço permite ao dispositivo para aprovisionamento.

![Dispositivo reestablishes ligação ao serviço de aprovisionamento de dispositivos para validar a propriedade EK](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Passos Seguintes

Agora, o dispositivo estabelece ligação ao IoT Hub e rest seguro sabendo que as chaves dos seus dispositivos estão armazenadas em segurança. Agora que sabe como o serviço de aprovisionamento de dispositivo verifica em segurança a identidade de um dispositivo com o TPM, consulte os artigos seguintes para obter mais informações:

* [Saiba mais sobre todos os conceitos de aprovisionamento automático](./concepts-auto-provisioning.md)
* [Começar a utilizar o aprovisionamento automático](./quick-setup-auto-provision.md) com os SDKs para cuidar do fluxo.
