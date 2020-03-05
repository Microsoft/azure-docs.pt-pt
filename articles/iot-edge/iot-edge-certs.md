---
title: Certificados para a segurança de dispositivo - Azure IoT Edge | Documentos da Microsoft
description: O Azure IoT Edge utiliza o certificado para validar a dispositivos, módulos e dispositivos de folha e estabelecer ligações seguras entre eles.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58294c7afdf31ddd29611351d6442db1c4966157
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269041"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Entenda como azure IoT Edge usa certificados

Os certificados IoT Edge são utilizados pelos módulos e dispositivos IoT a jusante para verificar a identidade e legitimidade do módulo de tempo de funcionamento do [hub IoT Edge.](iot-edge-runtime.md#iot-edge-hub) Estas verificações ativar uma ligação segura TLS (transport layer security) entre o tempo de execução, os módulos e os dispositivos de IoT. Como o IoT Hub em si, IoT Edge requer segura e encriptada ligação a partir do IoT downstream (ou folha) dispositivos e os módulos do IoT Edge. Para estabelecer uma ligação TLS segura, o módulo ioT Edge apresenta uma cadeia de certificados de servidor para ligar os clientes para que verifiquem a sua identidade.

Este artigo explica como os certificados IoT Edge podem funcionar em cenários de produção, desenvolvimento e teste. Embora os scripts sejam diferente (Powershell vs. bash), os conceitos são os mesmos entre o Linux e Windows.

## <a name="iot-edge-certificates"></a>Certificados do IoT Edge

Normalmente, os fabricantes não são os utilizadores finais de um dispositivo IoT Edge. Por vezes, a única relação entre os dois é quando o utilizador final, ou operador, compra um dispositivo genérico feito pelo fabricante. Outras vezes, o fabricante trabalha sob contrato para construir um dispositivo personalizado para o operador. O design de certificado do IoT Edge tenta considerar os dois cenários.

A figura seguinte ilustra a utilização do IoT Edge de certificados. Pode haver zero, um ou muitos certificados de assinatura intermédios entre o certificado ca raiz e o certificado CA do dispositivo, dependendo do número de entidades envolvidas. Aqui mostramos um caso.

![Diagrama de relações de certificado típico](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Autoridade de certificação

A autoridade de certificação, ou 'CA', para abreviar, é uma entidade que emite certificados digitais. Uma autoridade de certificação atua como um terceiro confiável entre o proprietário e o destinatário do certificado. Um certificado digital certifica a propriedade de uma chave pública pelo destinatário do certificado. A cadeia de certificados de confiança funciona ao inicialmente emitir um certificado de raiz, que é a base para a confiança em todos os certificados emitidos pela autoridade. Depois disso, o proprietário pode utilizar o certificado de raiz para emitir certificados intermédios adicionais (certificados de "folha").

### <a name="root-ca-certificate"></a>Certificado de AC de raiz

Um certificado de AC de raiz é a raiz de confiança de todo o processo. Em cenários de produção, este certificado de CA é geralmente comprado a uma autoridade de certificados comerciais confiáveis como Baltimore, Verisign ou DigiCert. Se tiver controlo total sobre os dispositivos que se ligam aos seus dispositivos IoT Edge, é possível utilizar uma autoridade de certificado de nível corporativo. Em qualquer dos casos, toda a cadeia de certificados do hub IoT Edge chega até ele, pelo que os dispositivos IoT de folha devem confiar no certificado de raiz. Pode armazenar o certificado ca raiz na loja de certificados de raiz fidedigno, ou fornecer os dados do certificado no seu código de aplicação.

### <a name="intermediate-certificates"></a>Certificados intermédios

Num processo de fabrico típico para a criação de proteger os dispositivos, os certificados de AC de raiz raramente são usados diretamente, principalmente devido ao risco de fuga ou exposição. O certificado ca raiz cria e assina digitalmente um ou mais certificados ca intermédios. Pode haver apenas um, ou pode haver uma cadeia destes certificados intermédios. Os cenários que necessitem de uma cadeia de certificados intermédios incluem:

* Uma hierarquia de departamentos dentro de um fabricante.

* Várias empresas envolvidas em série na produção de um dispositivo.

* Um cliente comprar uma AC de raiz e efetuar a derivação de um certificado de assinatura para o fabricante para se inscrever os dispositivos fazer em nome do cliente.

De qualquer forma, o fabricante utiliza um certificado de AC intermediária no final desta cadeia para assinar os certificados de AC de dispositivo colocado no dispositivo final. Em geral, esses certificados intermédios estreitamente são protegidos da fábrica de produção. Eles passam por processos strict, físicos e Eletrónicos para a utilização das mesmas.

### <a name="device-ca-certificate"></a>Certificado de acesso condicional de dispositivo

O certificado de AC do dispositivo é gerado a partir do e assinado pelo certificado de AC intermediário final do processo. Este certificado está instalado no próprio dispositivo IoT Edge, de preferência em armazenamento seguro, como um módulo de segurança de hardware (HSM). Além disso, um certificado de AC de dispositivo identifica exclusivamente um dispositivo IoT Edge. O certificado CA do dispositivo pode assinar outros certificados.

### <a name="iot-edge-workload-ca"></a>Carga de trabalho do IoT Edge AC

O [IoT Edge Security Manager](iot-edge-security-manager.md) gera o certificado CA de carga de trabalho, o primeiro no lado do "operador" do processo, quando o IoT Edge começa pela primeira vez. Este certificado é gerado a partir do e assinado pelo certificado"AC de dispositivo". Este certificado, o que é apenas outro certificado de assinatura intermediário, é utilizado para gerar e assinar todos os certificados utilizados pelo runtime do IoT Edge. Hoje em dia, este é principalmente o certificado de servidor do hub IoT Edge discutido na seguinte secção, mas no futuro pode incluir outros certificados para autenticar componentes IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certificado de servidor do hub IoT Edge

O certificado de servidor do hub IoT Edge é o certificado real apresentado aos dispositivos e módulos de folhas para verificação de identidade durante o estabelecimento da ligação TLS exigida pelo IoT Edge. Este certificado apresenta a cadeia completa de certificados de assinatura utilizados para gerá-lo até o certificado de AC de raiz, que o dispositivo de IoT de folha tem de confiar. Quando gerado pelo IoT Edge Security Manager, o nome comum (CN), deste certificado de hub IoT Edge é definido para a propriedade 'hostname' no ficheiro config.yaml após conversão para minúscula. Esta configuração é uma fonte comum de confusão com ioT Edge.

## <a name="production-implications"></a>Implicações de produção

Uma pergunta razoável pode ser "por que motivo do IoT Edge tem a"carga de trabalho AC"certificado extra? Não poderia usar o certificado CA do dispositivo para gerar diretamente o certificado de servidor do hub IoT Edge?". Tecnicamente, poderia. No entanto, o objetivo deste certificado intermédio "carga de trabalho" é separar questões entre o fabricante do dispositivo e o operador de dispositivo. Imagine um cenário em que um dispositivo IoT Edge é vendido ou transferido de um cliente para outro. Provavelmente desejaria o certificado de AC do dispositivo fornecido pelo fabricante para ser um imutável. No entanto, os certificados de "carga de trabalho" específicos para o funcionamento do dispositivo devem ser eliminados e recriados para a nova implementação.

Dado que os processos de fabrico e operação estão separados, considere as seguintes implicações na preparação dos dispositivos de produção:

* Com qualquer processo com base em certificado, o certificado de AC de raiz e todos os certificados de AC intermediários devem ser protegidos e monitorizados durante todo o processo de implementar um dispositivo IoT Edge. O fabricante do dispositivo IoT Edge deve ter fortes processos num local de armazenamento adequado e a utilização dos seus certificados intermédios. Além disso, o certificado de AC do dispositivo deve ser mantido em como o armazenamento seguro quanto possível no próprio dispositivo, preferencialmente, um módulo de segurança de hardware.

* O certificado de servidor do hub IoT Edge é apresentado pelo hub IoT Edge aos dispositivos e módulos do cliente de ligação. O nome comum (CN) do certificado CA do dispositivo **não deve ser** o mesmo que o "nome de anfitrião" que será utilizado em config.yaml no dispositivo IoT Edge. O nome utilizado pelos clientes para ligar ao IoT Edge (por exemplo, através do parâmetro GatewayHostName da cadeia de ligação ou do comando CONNECT em MQTT) **não pode ser** o mesmo que o nome comum utilizado no certificado CA do dispositivo. Esta restrição deve-se ao facto de o hub IoT Edge apresentar toda a sua cadeia de certificados para verificação por parte dos clientes. Se o certificado de servidor do hub IoT Edge e o certificado CA do dispositivo tiverem o mesmo NC, entra num ciclo de verificação e o certificado invalida.

* Uma vez que o certificado CA do dispositivo é utilizado pelo daemon de segurança IoT Edge para gerar os certificados finais de IoT Edge, deve ser, por si só, um certificado de assinatura, o que significa que tem capacidades de assinatura de certificados. Aplicação "V3 Restrições básicas CA:True" no certificado CA do dispositivo configura automaticamente as propriedades de utilização das chaves necessárias.

>[!Tip]
> Se já foi à configuração do IoT Edge como gateway transparente num cenário de programador/teste com o nosso "scripts de conveniência" (consulte a secção seguinte) e usado o mesmo nome de anfitrião ao criar o certificado de AC do dispositivo, como fez com o nome de anfitrião config.yaml , deve estar se perguntando por que ele funcionava. Num esforço para simplificar a experiência do desenvolvedor, os scripts de conveniência acrescenta um ".ca" no final do nome do que se passar para o script. Então, por exemplo, se tiver utilizado "mygateway" para ambos os seu nome de dispositivo os scripts e config.yaml de nome de anfitrião, o primeiro será convertido em mygateway.ca antes de a ser utilizado como o CN para o certificado de AC do dispositivo.

## <a name="devtest-implications"></a>Implicações de Dev/Test

Para facilitar o desenvolvimento e os cenários de teste, a Microsoft fornece um conjunto de [scripts](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) de conveniência para gerar certificados de não produção adequados para IoT Edge no cenário transparente de gateway. Por exemplo, como funcionam os scripts, consulte [Create demo certificates to test ioT Edge device features](how-to-create-test-certificates.md).

>[!Tip]
> Para ligar os seus dispositivos de "folha" de IoT de dispositivos e aplicações que utilizam o nosso SDK de dispositivo de IoT através do IoT Edge, tem de adicionar o parâmetro GatewayHostName opcional para o fim da cadeia de ligação do dispositivo. Quando o certificado de servidor de Hub do Edge é gerado, baseia-se numa versão em minúsculas do nome de anfitrião do config.yaml, portanto, para os nomes a correspondência e a verificação de certificado TLS com êxito, deve introduzir o parâmetro GatewayHostName em letra minúscula.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exemplo de hierarquia de certificados do IoT Edge

Para ilustrar um exemplo deste caminho do certificado, a seguinte captura de ecrã é de um dispositivo IoT Edge definido como um gateway transparente em funcionamento. O OpenSSL é utilizado para ligar ao hub IoT Edge, validar e despejar os certificados.

![Captura de ecrã da hierarquia de certificados em cada nível](./media/iot-edge-certs/iotedge-cert-chain.png)

Pode ver a hierarquia de profundidade de certificado representada na captura de ecrã:

| Certificado de AC de raiz         | Teste de certificado de AC do Hub de IoT do Azure apenas                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificado de AC intermediária | Apenas o teste de certificado intermédio Hub IoT do Azure                                                                 |
| Certificado de acesso condicional de dispositivo       | iotgateway.CA ("iotgateway" foi passado como o < nome de anfitrião de gateway > para os scripts de conveniência)   |
| Certificado de AC da carga de trabalho     | AC de carga de trabalho de iotedge                                                                                       |
| Certificado de servidor do Hub IoT Edge | iotedgegw.local (corresponde ao nome "anfitrião" de config.yaml)                                            |

## <a name="next-steps"></a>Passos seguintes

[Compreender os módulos Azure IoT Edge](iot-edge-modules.md)

[Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
