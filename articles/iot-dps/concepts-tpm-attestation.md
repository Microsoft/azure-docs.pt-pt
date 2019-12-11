---
title: Serviço de provisionamento de dispositivos no Hub IoT do Azure-atestado de TPM
description: Este artigo fornece uma visão geral conceitual do fluxo de atestado do TPM usando o DPS (serviço de provisionamento de dispositivos IoT).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 624171ffc10a06ac3089b6dceb1683c63c88dbda
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975283"
---
# <a name="tpm-attestation"></a>Atestado de TPM

O serviço de provisionamento de dispositivos no Hub IoT é um serviço auxiliar para o Hub IoT que você usa para configurar o provisionamento de dispositivos sem toque para um hub IoT especificado. Com o serviço de provisionamento de dispositivos, você pode provisionar milhões de dispositivos de maneira segura.

Este artigo descreve o processo de atestado de identidade ao usar um [TPM](./concepts-device.md). TPM significa Trusted Platform Module e é um tipo de HSM (módulo de segurança de hardware). Este artigo pressupõe que você esteja usando um TPM discreto, de firmware ou integrado. Os TPMs emulados de software são adequados para protótipos ou testes, mas eles não fornecem o mesmo nível de segurança que o TPMs integrado, de firmware ou discreto. Não recomendamos o uso de TPMs de software em produção. Para obter mais informações sobre os tipos de TPMs, consulte [uma breve introdução ao TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Este artigo só é relevante para dispositivos que usam o TPM 2,0 com suporte a chaves HMAC e suas chaves de endosso. Ele não é para dispositivos que usam certificados X. 509 para autenticação. O TPM é um padrão ISO, de todo o setor, da Trusted Computing Group, e você pode ler mais sobre o TPM na [especificação completa do tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) ou a [Especificação ISO/IEC 11889](https://www.iso.org/standard/66510.html). Este artigo também pressupõe que você esteja familiarizado com pares de chaves pública e privada e como eles são usados para criptografia.

Os SDKs do dispositivo do serviço de provisionamento de dispositivos lidam com tudo o que é descrito neste artigo para você. Não há necessidade de implementar nada adicional se você estiver usando os SDKs em seus dispositivos. Este artigo ajuda você a compreender conceitualmente o que está acontecendo com o chip de segurança do TPM quando o dispositivo é provisionado e por que ele é tão seguro.

## <a name="overview"></a>Visão geral

TPMs use algo chamado de EK (chave de endosso) como a raiz segura de confiança. O EK é exclusivo para o TPM e alterá-lo essencialmente altera o dispositivo para um novo.

Há outro tipo de chave que TPMs tem, chamado de SRK (chave raiz de armazenamento). Uma SRK pode ser gerada pelo proprietário do TPM depois de se apropriar do TPM. Assumir a propriedade do TPM é a maneira específica do TPM de dizer "alguém define uma senha no HSM". Se um dispositivo TPM for vendido a um novo proprietário, o novo proprietário poderá assumir a propriedade do TPM para gerar uma nova SRK. A nova geração de SRK garante que o proprietário anterior não possa usar o TPM. Como a SRK é exclusiva do proprietário do TPM, a SRK pode ser usada para lacrar dados no próprio TPM para esse proprietário. O SRK fornece uma área restrita para o proprietário armazenar suas chaves e fornece acesso revocability se o dispositivo ou TPM for vendido. É como se mover para uma nova casa: assumir a propriedade está alterando os bloqueios nas portas e destruindo todos os móveis deixados pelos proprietários anteriores (SRK), mas você não pode alterar o endereço da casa (EK).

Depois que um dispositivo tiver sido configurado e estiver pronto para uso, ele terá um EK e um SRK disponíveis para uso.

![Apropriando-se de um TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Uma observação sobre como assumir a propriedade do TPM: a apropriação de um TPM depende de muitas coisas, incluindo o fabricante do TPM, o conjunto de ferramentas do TPM que estão sendo usadas e o sistema operacional do dispositivo. Siga as instruções relevantes ao seu sistema para assumir a propriedade.

O serviço de provisionamento de dispositivos usa a parte pública do EK (EK_pub) para identificar e registrar dispositivos. O fornecedor do dispositivo pode ler o EK_pub durante o teste de fabricação ou final e carregar o EK_pub no serviço de provisionamento para que o dispositivo seja reconhecido quando se conectar ao provisionar. O serviço de provisionamento de dispositivos não verifica o SRK ou o proprietário; portanto, "limpar" o TPM apaga os dados do cliente, mas o EK (e outros dados do fornecedor) é preservado e o dispositivo ainda será reconhecido pelo serviço de provisionamento de dispositivos quando ele se conectar ao provisionar.

## <a name="detailed-attestation-process"></a>Processo de atestado detalhado

Quando um dispositivo com um TPM se conecta pela primeira vez ao serviço de provisionamento de dispositivos, o serviço primeiro verifica o EK_pub fornecido em relação ao EK_pub armazenado na lista de registro. Se os EK_pubs não corresponderem, o dispositivo não tem permissão para provisionar. Se o EK_pubs corresponder, o serviço exigirá que o dispositivo comprove a propriedade da parte privada da EK por meio de um desafio de nonce, que é um desafio seguro usado para provar a identidade. O serviço de provisionamento de dispositivos gera um nonce e, em seguida, criptografa-o com o SRK e, em seguida, o EK_pub, ambos fornecidos pelo dispositivo durante a chamada de registro inicial. O TPM sempre mantém a parte privada da EK segura. Isso impede a falsificação e garante que os tokens SAS sejam provisionados com segurança para dispositivos autorizados.

Vamos examinar detalhadamente o processo de atestado.

### <a name="device-requests-an-iot-hub-assignment"></a>O dispositivo solicita uma atribuição de Hub IoT

Primeiro, o dispositivo se conecta ao serviço de provisionamento de dispositivos e às solicitações para provisionar. Ao fazer isso, o dispositivo fornece o serviço com sua ID de registro, um escopo de ID e o EK_pub e SRK_pub do TPM. O serviço passa o nonce criptografado de volta para o dispositivo e solicita que o dispositivo descriptografe o nonce e use-o para assinar um token SAS para se conectar novamente e concluir o provisionamento.

![Provisionamento de solicitações de dispositivo](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Desafio de nonce

O dispositivo usa o nonce e usa as partes privadas do EK e do SRK para descriptografar o nonce no TPM; a ordem de criptografia nonce delega a confiança do EK, que é imutável, para o SRK, que pode ser alterado se um novo proprietário apropriar-se do TPM.

![Descriptografando o nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Validar as credenciais nonce e Receive

O dispositivo pode então assinar um token SAS usando o nonce descriptografado e restabelecer uma conexão com o serviço de provisionamento de dispositivos usando o token SAS assinado. Com o desafio de nonce concluído, o serviço permite que o dispositivo seja provisionado.

![O dispositivo restabelece a conexão com o serviço de provisionamento de dispositivos para validar a propriedade de EK](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Passos seguintes

Agora, o dispositivo se conecta ao Hub IoT e você fica seguro no conhecimento de que as chaves dos dispositivos são armazenadas com segurança. Agora que você sabe como o serviço de provisionamento de dispositivos verifica com segurança a identidade de um dispositivo usando o TPM, confira os seguintes artigos para saber mais:

* [Saiba mais sobre todos os conceitos no provisionamento automático](./concepts-auto-provisioning.md)
* Comece a [usar o provisionamento automático](./quick-setup-auto-provision.md) usando os SDKs para cuidar do fluxo.
