---
title: Terminologia utilizada com serviço de provisionamento de dispositivos Azure IoT Hub Microsoft Docs
description: Descreve terminologia comum utilizada com o Serviço de Provisionamento de Dispositivos (DPS) e IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019451"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>Terminologia do Serviço de Provisionamento de Dispositivos IoT Hub (DPS)

O IoT Hub Device Provisioning Service é um serviço de ajuda para o IoT Hub que utiliza para configurar o fornecimento de dispositivos de toque zero a um hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, pode [forcar](about-iot-dps.md#provisioning-process) milhões de dispositivos de forma segura e escalável.

O fornecimento de dispositivos é um processo de duas partes. A primeira parte é estabelecer a ligação inicial entre o dispositivo e a solução IoT *registando* o dispositivo. A segunda parte é aplicar a *configuração* adequada ao dispositivo com base nos requisitos específicos da solução. Uma vez concluídos os dois passos, o aparelho foi completamente *provisionado*. O Serviço de Aprovisionamento de Dispositivos automatiza os dois passos para fornecer uma experiência totalmente integrada de aprovisionamento do dispositivo.

Este artigo apresenta uma visão geral dos conceitos de provisionamento mais aplicáveis à gestão do *serviço.* Este artigo é mais relevante para as pessoas envolvidas no passo de [configuração](about-iot-dps.md#cloud-setup-step) da nuvem de preparar um dispositivo para a implementação.

## <a name="service-operations-endpoint"></a>Ponto final das operações de serviço

O ponto final das operações de serviço é o ponto final para gerir as definições de serviço e manter a lista de inscrições. Este ponto final é utilizado apenas pelo administrador de serviço; não é utilizado por dispositivos.

## <a name="device-provisioning-endpoint"></a>Dispositivo que a provisiona ponto final

O dispositivo que fornece o ponto final é o único ponto final que todos os dispositivos utilizam para o fornecimento automático. O URL é o mesmo para todas as instâncias de prestação de serviços, para eliminar a necessidade de reflash dispositivos com novas informações de conexão em cenários de cadeia de fornecimento. O âmbito de identificação garante o isolamento do inquilino.

## <a name="linked-iot-hubs"></a>Hubs IoT ligados

O Serviço de Provisionamento de Dispositivos só pode prestar dispositivos a centros IoT que tenham sido ligados ao mesmo. A ligação de um hub IoT a uma instância do serviço de provisionamento de dispositivos dá ao serviço permissões de leitura/escrita para o registo de dispositivos do hub IoT; com a ligação, um serviço de Provisionamento de Dispositivos pode registar um ID do dispositivo e definir a configuração inicial no twin do dispositivo. Os centros IoT ligados podem estar em qualquer região de Azure. Pode ligar os hubs de outras subscrições ao seu serviço de fornecimento.


## <a name="allocation-policy"></a>Política de atribuição

A definição de nível de serviço que determina como o Serviço de Provisionamento de Dispositivos atribui dispositivos a um hub IoT. Existem três políticas de alocação suportadas:

* **Distribuição equilibrada:** os centros IoT ligados são igualmente propensos a ter dispositivos a eles a forrados. A definição predefinida. Se estiver a aprovisionar dispositivos apenas para um hub IoT, pode manter esta definição.

* **Latência mais baixa**: os dispositivos são a provisionados a um hub IoT com a latência mais baixa do dispositivo. Se vários hubs IoT ligados forneceriam a mesma latência mais baixa, o serviço de fornecimento hashes dispositivos através desses centros

* **Configuração estática através da lista** de inscrições : a especificação do hub IoT desejado na lista de inscrições tem prioridade sobre a política de atribuição ao nível do serviço.

* **Custom (Use Azure Function)**: Uma política de atribuição personalizada dá-lhe mais controlo sobre a forma como os dispositivos são atribuídos a um hub IoT. Isto é conseguido utilizando código personalizado numa Função Azure para atribuir dispositivos a um hub IoT. O serviço de fornecimento de dispositivos chama o seu código Azure Function fornecendo todas as informações relevantes sobre o dispositivo e a inscrição no seu código. O seu código de função é executado e devolve as informações do hub IoT utilizadas para o fornecimento do dispositivo.

## <a name="enrollment"></a>Inscrição

Uma inscrição é o registo de dispositivos ou grupos de dispositivos que podem registar-se através do provisionamento automático. O registo de inscrições contém informações sobre o dispositivo ou grupo de dispositivos, incluindo:
- o [mecanismo de atestado](#attestation-mechanism) utilizado pelo dispositivo
- a configuração opcional inicial desejada
- hub IoT desejado
- o ID do dispositivo desejado

Existem dois tipos de matrículas suportadas pelo Serviço de Provisionamento de Dispositivos:

### <a name="enrollment-group"></a>Grupo de inscrições

Um grupo de inscrições é um grupo de dispositivos que partilham um mecanismo específico de atestado. Os grupos de inscrições suportam tanto o X.509 como o simétrico. Todos os dispositivos do grupo de matrículas X.509 apresentam certificados X.509 que tenham sido assinados pela mesma Autoridade de Certificados raiz ou intermédio (CA). Cada dispositivo do grupo de inscrição de chaves simétrica apresenta fichas SAS derivadas da chave simétrica do grupo. O nome do grupo de inscrição e o nome do certificado devem ser alfanuméricos, minúsculos e podem conter hífens.

> [!TIP]
> Recomendamos a utilização de um grupo de inscrições para um grande número de dispositivos que partilham uma configuração inicial desejada, ou para dispositivos que vão todos para o mesmo inquilino.

### <a name="individual-enrollment"></a>Inscrição individual

Uma inscrição individual é uma entrada para um único dispositivo que pode registar-se. As inscrições individuais podem utilizar certificados de folha X.509 ou fichas SAS (de um TPM físico ou virtual) como mecanismos de atestação. O ID de inscrição individual é alfanumérico, minúsculo, e pode conter hífenes. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

> [!TIP]
> Recomendamos a utilização de inscrições individuais para dispositivos que exijam configurações iniciais únicas, ou para dispositivos que só podem autenticar usando fichas SAS através de atestado de TPM.


## <a name="attestation-mechanism"></a>Mecanismo de atestação

Um mecanismo de atestado é o método utilizado para confirmar a identidade de um dispositivo. O mecanismo de atestação está configurado numa entrada de inscrição e diz ao serviço de provisionamento qual o método a utilizar ao verificar a identidade de um dispositivo durante o registo.

> [!NOTE]
> O IoT Hub utiliza "esquema de autenticação" para um conceito semelhante nesse serviço.

O Serviço de Provisionamento de Dispositivos suporta as seguintes formas de atestado:
* **Certificados X.509** com base no fluxo padrão de autenticação de certificadoS X.509. Para mais informações, consulte [o atestado X.509.](concepts-x509-attestation.md)
* **Módulo de Plataforma Fidedigna (TPM)** baseado num desafio de nonce, utilizando a norma TPM para as chaves apresentarem um token assinado assinatura de acesso partilhado (SAS). Isto não requer um TPM físico no dispositivo, mas o serviço espera atestar a utilização da chave de averbamento de acordo com a [especificação TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/). Para mais informações, consulte [o atestado TPM](concepts-tpm-attestation.md).
* **Chave simétrica** baseada em [fichas de segurança](../iot-hub/iot-hub-devguide-security.md#security-tokens)de assinatura de acesso partilhado (SAS), que incluem uma assinatura hashed e uma expiração incorporada. Para obter mais informações, consulte [a estação de teclas simétricas.](concepts-symmetric-key-attestation.md)


## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro e baseado em hardware de segredos do dispositivo, e é a forma mais segura de armazenamento secreto. Tanto os certificados X.509 como os tokens SAS podem ser armazenados no HSM. Os HSMs podem ser utilizados com ambos os mecanismos de atestação que o serviço de fornecimento suporta.

> [!TIP]
> Recomendamos vivamente a utilização de um HSM com dispositivos para armazenar segredos de forma segura nos seus dispositivos.

Os segredos do dispositivo também podem ser armazenados em software (memória), mas é uma forma de armazenamento menos segura do que um HSM.



## <a name="id-scope"></a>Âmbito de ID

O âmbito de identificação é atribuído a um Serviço de Provisionamento de Dispositivos quando é criado pelo utilizador e é utilizado para identificar de forma única o serviço específico de fornecimento através do qual o dispositivo irá registar.. O âmbito de identificação é gerado pelo serviço e é imutável, o que garante a singularidade.

> [!NOTE]
> A singularidade é importante para operações de implantação de longa duração e cenários de fusão e aquisição.


## <a name="registration"></a>Registo

Um registo é o registo de um dispositivo que regista/a provisiona com sucesso um Hub IoT através do Serviço de Provisionamento de Dispositivos. Os registos de registo são criados automaticamente; podem ser eliminados, mas não podem ser atualizados.


## <a name="registration-id"></a>ID de inscrição

O ID de registo é utilizado para identificar de forma única um registo de dispositivo com o Serviço de Provisionamento de Dispositivos. O ID do dispositivo deve ser único no âmbito de [identificação](#id-scope)do serviço de fornecimento . Cada dispositivo deve ter uma identificação de registo. O ID de registo é alfanumérico, caso insensível, e pode conter caracteres especiais, incluindo cólon, período, sublinhado e hífen.

* No caso do TPM, o ID de registo é fornecido pelo próprio TPM.
* No caso do atestado baseado em X.509, o ID de registo é fornecido como nome de assunto do certificado.

## <a name="device-id"></a>ID do Dispositivo

O ID do dispositivo é o ID tal como aparece no IoT Hub. O ID do dispositivo pretendido pode ser definido na entrada de inscrição, mas não é necessário definir. A definição do ID do dispositivo pretendido só é suportada em matrículas individuais. Se não for especificado nenhum ID do dispositivo desejado na lista de inscrição, o ID de registo é utilizado como ID do dispositivo ao registar o dispositivo. Saiba mais sobre [os IDs do dispositivo no IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).



## <a name="operations"></a>Operações

As operações são a unidade de faturação do Serviço de Provisionamento de Dispositivos. Uma operação é a conclusão bem sucedida de uma instrução para o serviço. As operações incluem registos de dispositivos e re-registos; as operações também incluem alterações do lado do serviço, tais como a adição de inscrições na lista de inscrições e a atualização de inscrições na lista de inscrições.
