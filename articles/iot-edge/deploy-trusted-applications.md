---
title: Aplicações fidedignas como módulos Azure IoT Edge
description: Utilize o Open Enclave SDK e a API para escrever aplicações fidedignas e implementá-las como módulos IoT Edge para computação confidencial
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: kgremban
ms.openlocfilehash: d81cc6c94c04c683362fd12cd6777c304a4b0a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361981"
---
# <a name="confidential-computing-at-the-edge"></a>Computação confidencial no limite

O Azure IoT Edge suporta aplicações fidedignas que funcionam dentro de enclaves seguros no dispositivo. A encriptação fornece segurança para os dados durante o trânsito ou em repouso, mas os enclaves fornecem segurança para dados e cargas de trabalho durante a utilização. O IoT Edge suporta o Open Enclave como um padrão para o desenvolvimento de aplicações fidedignas.

A segurança sempre foi um importante foco da Internet das Coisas (IoT) porque muitas vezes os dispositivos IoT estão muitas vezes no mundo em vez de estarem protegidos dentro de uma instalação privada. Esta exposição coloca os dispositivos em risco de adulteração e falsificação por serem fisicamente acessíveis a maus atores. Os dispositivos IoT Edge têm ainda mais necessidade de confiança e integridade porque permitem que cargas de trabalho sensíveis sejam executadas no limite. Ao contrário dos sensores e actuadores comuns, estes dispositivos inteligentes de borda estão potencialmente expondo cargas de trabalho sensíveis que anteriormente eram apenas executadas dentro de ambientes de nuvem protegida ou no local.

O [gestor de segurança IoT Edge](iot-edge-security-manager.md) aborda uma parte do desafio de computação confidencial. O gestor de segurança utiliza um módulo de segurança de hardware (HSM) para proteger as cargas de trabalho de identidade e os processos em curso de um dispositivo IoT Edge.

Outro aspeto da computação confidencial é proteger os dados em uso no limite. Um *ambiente de execução fidedigno* (TEE) é um ambiente seguro e isolado num processador e é por vezes referido como um *enclave*. Uma *aplicação de confiança* é uma aplicação que funciona num enclave. Devido à natureza dos enclaves, as aplicações fidedignas estão protegidas contra outras aplicações que estão a ser executando no processador principal ou no TEE.

## <a name="trusted-applications-on-iot-edge"></a>Aplicações fidedignas no IoT Edge

As aplicações fidedignas são encriptadas em trânsito e em repouso, e apenas desencriptadas para serem executadas dentro de um ambiente de execução fidedigno. Esta norma é fiel às aplicações fidedignas implementadas como módulos IoT Edge.

O desenvolvedor cria a aplicação fidedigna e embala-a como um módulo IoT Edge. A aplicação é encriptada antes de ser empurrada para o registo do contentor. A aplicação permanece encriptada durante todo o processo de implementação IoT Edge até que o módulo seja iniciado no dispositivo IoT Edge. Uma vez que a aplicação fidedigna esteja dentro do TEE do dispositivo, é desencriptada e pode começar a ser executada.

![Diagrama - Aplicações fidedignas são encriptadas dentro de módulos IoT Edge até serem implantadas no enclave seguro](./media/deploy-trusted-applications/trusted-applications-encrypted.png)

Aplicações fidedignas no IoT Edge são uma extensão lógica da [computação confidencial Azure](../confidential-computing/overview.md). Cargas de trabalho que funcionam dentro de enclaves seguros na nuvem também podem ser implementadas para funcionar dentro de enclaves seguros na borda.

## <a name="open-enclave"></a>Enclave Aberto

O [Open Enclave SDK](https://openenclave.io/sdk/) é um projeto de código aberto que ajuda os desenvolvedores a criar aplicações fidedignas para várias plataformas e ambientes. O Open Enclave SDK funciona dentro do ambiente de execução fidedigno de um dispositivo, enquanto a API do Enclave Aberto atua como uma interface entre o tee e o ambiente de processamento não TEE.

Open Enclave suporta várias plataformas de hardware. O suporte IoT Edge para enclaves requer atualmente o sistema operativo TEE portátil aberto (OP-TEE OS). Para saber mais, consulte [Open Enclave SDK para OP-TEE OS](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

O repositório Open Enclave também inclui amostras para ajudar os desenvolvedores a começar. Para mais informações, escolha um dos artigos introdutórios:

* [Edifício Open Enclave SDK amostras em Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Edifício Open Enclave SDK amostras em Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Hardware

Atualmente, [o TrustBox by Scalys](https://scalys.com/trustbox-industrial/) é o único dispositivo suportado com acordos de serviço do fabricante para implementar aplicações fidedignas como módulos IoT Edge. O TrustBox é construído nos dispositivos TrustBox Edge e TrustBox EdgeXL ambos pré-carregados com o Open Enclave SDK e Azure IoT Edge.

Para obter mais informações, consulte [Começar com o Open Enclave para o Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started).

## <a name="develop-and-deploy"></a>Desenvolver e implementar

Quando estiver pronto para desenvolver e implementar a sua aplicação fidedigna, a extensão [do Microsoft Open Enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) para Código do Estúdio Visual pode ajudar. Pode utilizar o Linux ou o Windows como máquina de desenvolvimento para desenvolver módulos para o TrustBox.

## <a name="next-steps"></a>Passos seguintes

Saiba como começar a desenvolver aplicações fidedignas como módulos IoT Edge com a [extensão Open Enclave para Código do Estúdio Visual](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
