---
title: Certificados para segurança do dispositivo - Azure IoT Edge [ Microsoft Docs
description: O Azure IoT Edge utiliza um certificado para validar dispositivos, módulos e dispositivos de folhas e estabelecer ligações seguras entre eles.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58294c7afdf31ddd29611351d6442db1c4966157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269041"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Entenda como azure IoT Edge usa certificados

Os certificados IoT Edge são utilizados pelos módulos e dispositivos IoT a jusante para verificar a identidade e legitimidade do módulo de tempo de funcionamento do [hub IoT Edge.](iot-edge-runtime.md#iot-edge-hub) Estas verificações permitem uma ligação segura tLS (segurança da camada de transporte) entre o tempo de funcionação, os módulos e os dispositivos IoT. Tal como o IoT Hub em si, o IoT Edge requer uma ligação segura e encriptada a partir de dispositivos IoT a jusante (ou folha) e módulos IoT Edge. Para estabelecer uma ligação TLS segura, o módulo ioT Edge apresenta uma cadeia de certificados de servidor para ligar os clientes para que verifiquem a sua identidade.

Este artigo explica como os certificados IoT Edge podem funcionar em cenários de produção, desenvolvimento e teste. Embora os scripts sejam diferentes (Powershell vs. bash), os conceitos são os mesmos entre Linux e Windows.

## <a name="iot-edge-certificates"></a>Certificados do IoT Edge

Normalmente, os fabricantes não são os utilizadores finais de um dispositivo IoT Edge. Por vezes, a única relação entre os dois é quando o utilizador final, ou operador, compra um dispositivo genérico feito pelo fabricante. Outras vezes, o fabricante trabalha sob contrato para construir um dispositivo personalizado para o operador. O design de certificado IoT Edge tenta ter em conta ambos os cenários.

A figura que se segue ilustra a utilização de certificados por IoT Edge. Pode haver zero, um ou muitos certificados de assinatura intermédios entre o certificado ca raiz e o certificado CA do dispositivo, dependendo do número de entidades envolvidas. Aqui mostramos um caso.

![Diagrama de relações típicas de certificado](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Autoridade de certificação

A autoridade do certificado, ou 'CA' para abreviar, é uma entidade que emite certificados digitais. Uma autoridade de certificado saca como um terceiro de confiança entre o proprietário e o destinatário do certificado. Um certificado digital certifica a propriedade de uma chave pública pelo destinatário do certificado. A cadeia de certificados de fidedignidade funciona através da emissão inicial de um certificado de raiz, que é a base para a confiança em todos os certificados emitidos pela autoridade. Posteriormente, o proprietário pode utilizar o certificado de raiz para emitir certificados intermédios adicionais (certificados'folha').

### <a name="root-ca-certificate"></a>Certificado Root CA

Um certificado de ac raiz é a raiz da confiança de todo o processo. Em cenários de produção, este certificado de CA é geralmente comprado a uma autoridade de certificados comerciais confiáveis como Baltimore, Verisign ou DigiCert. Se tiver controlo total sobre os dispositivos que se ligam aos seus dispositivos IoT Edge, é possível utilizar uma autoridade de certificado de nível corporativo. Em qualquer dos casos, toda a cadeia de certificados do hub IoT Edge chega até ele, pelo que os dispositivos IoT de folha devem confiar no certificado de raiz. Pode armazenar o certificado ca raiz na loja de certificados de raiz fidedigno, ou fornecer os dados do certificado no seu código de aplicação.

### <a name="intermediate-certificates"></a>Certificados intermédios

Num processo típico de fabrico para a criação de dispositivos seguros, os certificados ca de raiz raramente são utilizados diretamente, principalmente devido ao risco de fugas ou exposição. O certificado ca raiz cria e assina digitalmente um ou mais certificados ca intermédios. Pode haver apenas um, ou pode haver uma cadeia destes certificados intermédios. Os cenários que exigiriam uma cadeia de certificados intermédios incluem:

* Uma hierarquia de departamentos dentro de um fabricante.

* Várias empresas envolvidas em série na produção de um dispositivo.

* Um cliente que adprece uma raiz ca e derive um certificado de assinatura para o fabricante assinar os dispositivos que faz em nome desse cliente.

Em todo o caso, o fabricante utiliza um certificado ca intermédio no final desta cadeia para assinar o certificado CA do dispositivo colocado no dispositivo final. Geralmente, estes certificados intermédios são guardados de perto na fábrica. Passam por processos rigorosos, físicos e eletrónicos para o seu uso.

### <a name="device-ca-certificate"></a>Certificado CA do dispositivo

O certificado CA do dispositivo é gerado e assinado pelo certificado de CA intermediário final no processo. Este certificado está instalado no próprio dispositivo IoT Edge, de preferência em armazenamento seguro, como um módulo de segurança de hardware (HSM). Além disso, um certificado CA do dispositivo identifica exclusivamente um dispositivo IoT Edge. O certificado CA do dispositivo pode assinar outros certificados.

### <a name="iot-edge-workload-ca"></a>IoT Edge Workload CA

O [IoT Edge Security Manager](iot-edge-security-manager.md) gera o certificado CA de carga de trabalho, o primeiro no lado do "operador" do processo, quando o IoT Edge começa pela primeira vez. Este certificado é gerado e assinado pelo "certificado CA do dispositivo". Este certificado, que é apenas mais um certificado de assinatura intermédio, é utilizado para gerar e assinar quaisquer outros certificados utilizados pelo tempo de execução do IoT Edge. Hoje em dia, este é principalmente o certificado de servidor do hub IoT Edge discutido na seguinte secção, mas no futuro pode incluir outros certificados para autenticar componentes IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certificado de servidor do hub IoT Edge

O certificado de servidor do hub IoT Edge é o certificado real apresentado aos dispositivos e módulos de folhas para verificação de identidade durante o estabelecimento da ligação TLS exigida pelo IoT Edge. Este certificado apresenta a cadeia completa de certificados de assinatura utilizados para o gerar até ao certificado CA raiz, em que o dispositivo Folha IoT deve confiar. Quando gerado pelo IoT Edge Security Manager, o nome comum (CN), deste certificado de hub IoT Edge é definido para a propriedade 'hostname' no ficheiro config.yaml após conversão para minúscula. Esta configuração é uma fonte comum de confusão com ioT Edge.

## <a name="production-implications"></a>Implicações na produção

Uma pergunta razoável pode ser "porque é que o IoT Edge precisa do certificado extra 'Workload CA'? Não poderia usar o certificado CA do dispositivo para gerar diretamente o certificado de servidor do hub IoT Edge?". Tecnicamente, pode. No entanto, o objetivo deste certificado intermédio de "carga de trabalho" é separar as preocupações entre o fabricante do dispositivo e o operador do dispositivo. Imagine um cenário em que um dispositivo IoT Edge é vendido ou transferido de um cliente para outro. Provavelmente desejaria que o certificado CA do dispositivo fornecido pelo fabricante fosse imutável. No entanto, os certificados de "carga de trabalho" específicos para o funcionamento do dispositivo devem ser limpos e recriados para a nova implantação.

Dado que os processos de fabrico e operação estão separados, considere as seguintes implicações na preparação dos dispositivos de produção:

* Com qualquer processo baseado em certificados, o certificado ca raiz e todos os certificados ca intermédios devem ser protegidos e monitorizados durante todo o processo de lançamento de um dispositivo IoT Edge. O fabricante do dispositivo IoT Edge deve ter processos fortes para armazenamento e utilização adequados dos seus certificados intermédios. Além disso, o certificado CA do dispositivo deve ser mantido no armazenamento o mais seguro possível no próprio dispositivo, de preferência um módulo de segurança de hardware.

* O certificado de servidor do hub IoT Edge é apresentado pelo hub IoT Edge aos dispositivos e módulos do cliente de ligação. O nome comum (CN) do certificado CA do dispositivo **não deve ser** o mesmo que o "nome de anfitrião" que será utilizado em config.yaml no dispositivo IoT Edge. O nome utilizado pelos clientes para ligar ao IoT Edge (por exemplo, através do parâmetro GatewayHostName da cadeia de ligação ou do comando CONNECT em MQTT) **não pode ser** o mesmo que o nome comum utilizado no certificado CA do dispositivo. Esta restrição deve-se ao facto de o hub IoT Edge apresentar toda a sua cadeia de certificados para verificação por parte dos clientes. Se o certificado de servidor do hub IoT Edge e o certificado CA do dispositivo tiverem o mesmo NC, entra num ciclo de verificação e o certificado invalida.

* Uma vez que o certificado CA do dispositivo é utilizado pelo daemon de segurança IoT Edge para gerar os certificados finais de IoT Edge, deve ser, por si só, um certificado de assinatura, o que significa que tem capacidades de assinatura de certificados. Aplicação "V3 Restrições básicas CA:True" no certificado CA do dispositivo configura automaticamente as propriedades de utilização das chaves necessárias.

>[!Tip]
> Se já passou pela configuração do IoT Edge como uma porta de entrada transparente num cenário de v/teste usando os nossos "scripts de conveniência" (ver secção seguinte) e usou o mesmo nome de anfitrião ao criar o certificado CA do dispositivo como fez para o nome de anfitrião em config.yaml, pode estar a perguntar-se por que funcionou. Num esforço para simplificar a experiência do desenvolvedor, os scripts de conveniência anexam um ".ca" na extremidade do nome que passa para o script. Assim, por exemplo, se usou "mygateway" para o nome do seu dispositivo nos scripts e nome de anfitrião em config.yaml, o primeiro será transformado em mygateway.ca antes de ser usado como O C para o dispositivo CA cert.

## <a name="devtest-implications"></a>Implicações de Dev/Teste

Para facilitar o desenvolvimento e os cenários de teste, a Microsoft fornece um conjunto de [scripts](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) de conveniência para gerar certificados de não produção adequados para IoT Edge no cenário transparente de gateway. Por exemplo, como funcionam os scripts, consulte [Create demo certificates to test ioT Edge device features](how-to-create-test-certificates.md).

>[!Tip]
> Para ligar os dispositivos e aplicações "folha" do seu dispositivo IoT que utilizam o nosso dispositivo IoT SDK através do IoT Edge, deve adicionar o parâmetro opcional GatewayHostName na extremidade da cadeia de ligação do dispositivo. Quando o Certificado de Servidor Edge Hub é gerado, baseia-se numa versão minúscula do nome de anfitrião de config.yaml, portanto, para que os nomes correspondam e a verificação do certificado TLS seja bem sucedida, deve introduzir o parâmetro GatewayHostName em maiúsculas.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exemplo da hierarquia de certificados IoT Edge

Para ilustrar um exemplo deste caminho de certificado, a seguinte imagem é de um dispositivo IoT Edge funcionando configurado como um gateway transparente. O OpenSSL é utilizado para ligar ao hub IoT Edge, validar e despejar os certificados.

![Screenshot da hierarquia do certificado em cada nível](./media/iot-edge-certs/iotedge-cert-chain.png)

Pode ver a hierarquia da profundidade do certificado representada na imagem:

| Certificado Root CA         | Teste de Cert do Hub Azure IoT                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificado Interintermédio ca | Teste de Cert Intermediário Azure IoT Hub                                                                 |
| Certificado CA dispositivo       | iotgateway.ca ("iotgateway" foi passado como o nome de anfitrião < gateway > para os scripts de conveniência)   |
| Certificado CA de carga de trabalho     | iotedge carga de trabalho ca                                                                                       |
| Certificado de servidor do Hub IoT Edge | iotedgegw.local (corresponde ao 'nome anfitrião' de config.yaml)                                            |

## <a name="next-steps"></a>Passos seguintes

[Compreender os módulos do Azure IoT Edge](iot-edge-modules.md)

[Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
