---
title: Aplicações confidenciais como módulos Azure IoT Edge
description: Utilize o Open Enclave SDK e a API para escrever aplicações confidenciais e implementá-las como módulos IoT Edge para computação confidencial
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: f9dff1b4c6b2489edd3cd685e3546618961d9757
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487722"
---
# <a name="confidential-computing-at-the-edge"></a>Computação confidencial no limite

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O Azure IoT Edge suporta aplicações confidenciais que funcionam dentro de enclaves seguros no dispositivo. A encriptação fornece segurança para os dados durante o trânsito ou em repouso, mas os enclaves fornecem segurança para dados e cargas de trabalho durante a utilização. O IoT Edge suporta o Open Enclave como padrão para o desenvolvimento de aplicações confidenciais.

A segurança sempre foi um importante foco da Internet das Coisas (IoT) porque muitas vezes os dispositivos IoT estão muitas vezes no mundo em vez de estarem protegidos dentro de uma instalação privada. Esta exposição coloca os dispositivos em risco de adulteração e falsificação por serem fisicamente acessíveis a maus atores. Os dispositivos IoT Edge têm ainda mais necessidade de confiança e integridade porque permitem que cargas de trabalho sensíveis sejam executadas no limite. Ao contrário dos sensores e actuadores comuns, estes dispositivos inteligentes de borda estão potencialmente expondo cargas de trabalho sensíveis que anteriormente eram apenas executadas dentro de ambientes de nuvem protegida ou no local.

O [gestor de segurança IoT Edge](iot-edge-security-manager.md) aborda uma parte do desafio de computação confidencial. O gestor de segurança utiliza um módulo de segurança de hardware (HSM) para proteger as cargas de trabalho de identidade e os processos em curso de um dispositivo IoT Edge.

Outro aspeto da computação confidencial é proteger os dados em uso no limite. Um *ambiente de execução fidedigno* (TEE) é um ambiente seguro e isolado num processador e é por vezes referido como um *enclave*. Um *pedido confidencial* é uma aplicação que corre num enclave. Devido à natureza dos enclaves, as aplicações confidenciais estão protegidas contra outras aplicações que são executando no processador principal ou no TEE.

## <a name="confidential-applications-on-iot-edge"></a>Aplicações confidenciais no IoT Edge

As aplicações confidenciais são encriptadas em trânsito e em repouso, e apenas desencriptadas para serem executadas dentro de um ambiente de execução fidedigno. Esta norma aplica-se para aplicações confidenciais implementadas como módulos IoT Edge.

O desenvolvedor cria a aplicação confidencial e embala-a como um módulo IoT Edge. A aplicação é encriptada antes de ser empurrada para o registo do contentor. A aplicação permanece encriptada durante todo o processo de implementação IoT Edge até que o módulo seja iniciado no dispositivo IoT Edge. Uma vez que a aplicação confidencial esteja dentro do TEE do dispositivo, é desencriptada e pode começar a executar.

![Diagrama - Aplicações confidenciais são encriptadas dentro de módulos IoT Edge até implantadas no enclave seguro](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

Aplicações confidenciais no IoT Edge são uma extensão lógica da [computação confidencial Azure](../confidential-computing/overview.md). Cargas de trabalho que funcionam dentro de enclaves seguros na nuvem também podem ser implementadas para funcionar dentro de enclaves seguros na borda.

## <a name="open-enclave"></a>Enclave Aberto

O [Open Enclave SDK](https://openenclave.io/sdk/) é um projeto de código aberto que ajuda os desenvolvedores a criar aplicações confidenciais para várias plataformas e ambientes. O Open Enclave SDK funciona dentro do ambiente de execução fidedigno de um dispositivo, enquanto a API do Enclave Aberto atua como uma interface entre o tee e o ambiente de processamento não TEE.

Open Enclave suporta várias plataformas de hardware. O suporte IoT Edge para enclaves requer atualmente o sistema operativo TEE portátil aberto (OP-TEE OS). Para saber mais, consulte [Open Enclave SDK para OP-TEE OS](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

O repositório Open Enclave também inclui amostras para ajudar os desenvolvedores a começar. Para mais informações, escolha um dos artigos introdutórios:

* [Edifício Open Enclave SDK amostras em Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Edifício Open Enclave SDK amostras em Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Hardware

Atualmente, [o TrustBox by Scalys](https://scalys.com/trustbox-industrial/) é o único dispositivo suportado com acordos de serviço do fabricante para a implementação de aplicações confidenciais como módulos IoT Edge. O TrustBox é construído nos dispositivos TrustBox Edge e TrustBox EdgeXL ambos pré-carregados com o Open Enclave SDK e Azure IoT Edge.

Para obter mais informações, consulte [Começar com o Open Enclave para o Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started).

## <a name="develop-and-deploy"></a>Desenvolver e implementar

Quando estiver pronto para desenvolver e implementar a sua aplicação confidencial, a extensão do [Microsoft Open Enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) para o Código do Estúdio Visual pode ajudar. Pode utilizar o Linux ou o Windows como máquina de desenvolvimento para desenvolver módulos para o TrustBox.

## <a name="next-steps"></a>Passos seguintes

Saiba como começar a desenvolver aplicações confidenciais como módulos IoT Edge com a [extensão Open Enclave para Código do Estúdio Visual](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
