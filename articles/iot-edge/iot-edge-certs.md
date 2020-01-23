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
ms.openlocfilehash: a222f72e705184c5a7ba6701cfda41073c7eba57
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548752"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Entender como Azure IoT Edge usa certificados

IoT Edge certificados são usados pelos módulos e dispositivos IoT downstream para verificar a identidade e a legitimidade do módulo [IOT Edge](iot-edge-runtime.md#iot-edge-hub) tempo de execução do Hub. Estas verificações ativar uma ligação segura TLS (transport layer security) entre o tempo de execução, os módulos e os dispositivos de IoT. Como o IoT Hub em si, IoT Edge requer segura e encriptada ligação a partir do IoT downstream (ou folha) dispositivos e os módulos do IoT Edge. Para estabelecer uma conexão TLS segura, o módulo Hub IoT Edge apresenta uma cadeia de certificados de servidor para conectar clientes para que eles verifiquem sua identidade.

Este artigo explica como IoT Edge certificados podem funcionar em cenários de produção, desenvolvimento e teste. Embora os scripts sejam diferente (Powershell vs. bash), os conceitos são os mesmos entre o Linux e Windows.

## <a name="iot-edge-certificates"></a>Certificados do IoT Edge

Normalmente, os fabricantes não são os usuários finais de um dispositivo IoT Edge. Às vezes, a única relação entre os dois é quando o usuário final, ou operador, adquire um dispositivo genérico feito pelo fabricante. Em outras ocasiões, o fabricante trabalha sob contrato para criar um dispositivo personalizado para o operador. O design de certificado do IoT Edge tenta considerar os dois cenários.

A figura seguinte ilustra a utilização do IoT Edge de certificados. Pode haver zero, um ou muitos certificados de assinatura intermediários entre o certificado de autoridade de certificação raiz e o certificado de autoridade de certificação do dispositivo, dependendo do número de entidades envolvidas. Aqui, mostramos um caso.

![Diagrama de relações de certificado típico](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Autoridade de certificação

A autoridade de certificação, ou 'CA', para abreviar, é uma entidade que emite certificados digitais. Uma autoridade de certificação atua como um terceiro confiável entre o proprietário e o destinatário do certificado. Um certificado digital certifica a propriedade de uma chave pública pelo destinatário do certificado. A cadeia de certificados de confiança funciona ao inicialmente emitir um certificado de raiz, que é a base para a confiança em todos os certificados emitidos pela autoridade. Depois disso, o proprietário pode utilizar o certificado de raiz para emitir certificados intermédios adicionais (certificados de "folha").

### <a name="root-ca-certificate"></a>Certificado de AC de raiz

Um certificado de AC de raiz é a raiz de confiança de todo o processo. Em cenários de produção, esse certificado de autoridade de certificação geralmente é adquirido de uma autoridade de certificação comercial confiável como Baltimore, VeriSign ou DigiCert. Se você tiver controle total sobre os dispositivos que se conectam aos dispositivos IoT Edge, é possível usar uma autoridade de certificação de nível corporativo. Em qualquer evento, toda a cadeia de certificados do hub de IoT Edge é acumulada até ele, de modo que os dispositivos IoT folha devem confiar no certificado raiz. Você pode armazenar o certificado de autoridade de certificação raiz no repositório de autoridade de certificação raiz confiável ou fornecer os detalhes do certificado no código do aplicativo.

### <a name="intermediate-certificates"></a>Certificados intermediários

Num processo de fabrico típico para a criação de proteger os dispositivos, os certificados de AC de raiz raramente são usados diretamente, principalmente devido ao risco de fuga ou exposição. O certificado de autoridade de certificação raiz cria e assina digitalmente um ou mais certificados de autoridade de certificação intermediários. Pode haver apenas um, ou pode haver uma cadeia destes certificados intermédios. Os cenários que necessitem de uma cadeia de certificados intermédios incluem:

* Uma hierarquia de departamentos dentro de um fabricante.

* Várias empresas envolvidas em série na produção de um dispositivo.

* Um cliente comprar uma AC de raiz e efetuar a derivação de um certificado de assinatura para o fabricante para se inscrever os dispositivos fazer em nome do cliente.

De qualquer forma, o fabricante utiliza um certificado de AC intermediária no final desta cadeia para assinar os certificados de AC de dispositivo colocado no dispositivo final. Em geral, esses certificados intermédios estreitamente são protegidos da fábrica de produção. Eles passam por processos strict, físicos e Eletrónicos para a utilização das mesmas.

### <a name="device-ca-certificate"></a>Certificado de acesso condicional de dispositivo

O certificado de AC do dispositivo é gerado a partir do e assinado pelo certificado de AC intermediário final do processo. Esse certificado é instalado no próprio dispositivo IoT Edge, preferencialmente no armazenamento seguro, como um módulo de segurança de hardware (HSM). Além disso, um certificado de AC de dispositivo identifica exclusivamente um dispositivo IoT Edge. O certificado de autoridade de certificação do dispositivo pode assinar outros certificados.

### <a name="iot-edge-workload-ca"></a>Carga de trabalho do IoT Edge AC

O [IOT Edge Security Manager](iot-edge-security-manager.md) gera o certificado de AC de carga de trabalho, o primeiro no lado "operador" do processo, quando IOT Edge é iniciado pela primeira vez. Este certificado é gerado a partir do e assinado pelo certificado"AC de dispositivo". Este certificado, o que é apenas outro certificado de assinatura intermediário, é utilizado para gerar e assinar todos os certificados utilizados pelo runtime do IoT Edge. Hoje, isso é principalmente o certificado de servidor Hub IoT Edge discutido na seção a seguir, mas no futuro pode incluir outros certificados para autenticar IoT Edge componentes.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge certificado do servidor Hub

O certificado do servidor Hub IoT Edge é o certificado real apresentado aos dispositivos folha e módulos para verificação de identidade durante o estabelecimento da conexão TLS exigida pelo IoT Edge. Este certificado apresenta a cadeia completa de certificados de assinatura utilizados para gerá-lo até o certificado de AC de raiz, que o dispositivo de IoT de folha tem de confiar. Quando gerado pelo Gerenciador de segurança IoT Edge, o CN (nome comum) desse certificado de IoT Edge Hub é definido como a propriedade ' hostname ' no arquivo config. YAML após a conversão em minúsculas. Essa configuração é uma fonte comum de confusão com IoT Edge.

## <a name="production-implications"></a>Implicações de produção

Uma pergunta razoável pode ser "por que motivo do IoT Edge tem a"carga de trabalho AC"certificado extra? Não foi possível usar o certificado de autoridade de certificação do dispositivo para gerar diretamente o certificado do servidor do hub de IoT Edge? ". Tecnicamente, poderia. No entanto, o objetivo deste certificado intermédio "carga de trabalho" é separar questões entre o fabricante do dispositivo e o operador de dispositivo. Imagine um cenário em que um dispositivo IoT Edge é vendido ou transferido de um cliente para outro. Provavelmente desejaria o certificado de AC do dispositivo fornecido pelo fabricante para ser um imutável. No entanto, os certificados de "carga de trabalho" específicos para o funcionamento do dispositivo devem ser eliminados e recriados para a nova implementação.

Como os processos de fabricação e operação são separados, considere as seguintes implicações ao preparar os dispositivos de produção:

* Com qualquer processo com base em certificado, o certificado de AC de raiz e todos os certificados de AC intermediários devem ser protegidos e monitorizados durante todo o processo de implementar um dispositivo IoT Edge. O fabricante do dispositivo IoT Edge deve ter fortes processos num local de armazenamento adequado e a utilização dos seus certificados intermédios. Além disso, o certificado de AC do dispositivo deve ser mantido em como o armazenamento seguro quanto possível no próprio dispositivo, preferencialmente, um módulo de segurança de hardware.

* O certificado do servidor Hub de IoT Edge é apresentado pelo IoT Edge Hub para os dispositivos e módulos de cliente de conexão. O nome comum (CN) do certificado de autoridade de certificação do dispositivo **não deve ser** o mesmo que o "nome do host" que será usado em config. YAML no dispositivo IOT Edge. O nome usado pelos clientes para se conectar ao IoT Edge (por exemplo, por meio do parâmetro GatewayHostName da cadeia de conexão ou do comando CONNECT em MQTT) **não pode ser** o mesmo que o nome comum usado no certificado de autoridade de certificação do dispositivo. Essa restrição ocorre porque o Hub de IoT Edge apresenta toda a cadeia de certificados para verificação pelos clientes. Se o certificado do servidor Hub IoT Edge e o certificado de autoridade de certificação do dispositivo tiverem o mesmo CN, você obterá um loop de verificação e o certificado será invalidado.

* Como o certificado de autoridade de certificação do dispositivo é usado pelo daemon de segurança do IoT Edge para gerar os certificados de IoT Edge finais, ele deve ser um certificado de autenticação, o que significa que ele tem recursos de assinatura de certificado. Aplicar "V3 Basic Constraints CA: true" ao certificado de autoridade de certificação do dispositivo configura automaticamente as propriedades de uso de chave necessárias.

>[!Tip]
> Se já foi à configuração do IoT Edge como gateway transparente num cenário de programador/teste com o nosso "scripts de conveniência" (consulte a secção seguinte) e usado o mesmo nome de anfitrião ao criar o certificado de AC do dispositivo, como fez com o nome de anfitrião config.yaml , deve estar se perguntando por que ele funcionava. Num esforço para simplificar a experiência do desenvolvedor, os scripts de conveniência acrescenta um ".ca" no final do nome do que se passar para o script. Então, por exemplo, se tiver utilizado "mygateway" para ambos os seu nome de dispositivo os scripts e config.yaml de nome de anfitrião, o primeiro será convertido em mygateway.ca antes de a ser utilizado como o CN para o certificado de AC do dispositivo.

## <a name="devtest-implications"></a>Implicações de Dev/Test

Para facilitar o desenvolvimento e testar cenários, a Microsoft fornece um conjunto de [scripts de conveniência](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) para gerar certificados de não produção adequados para o IoT Edge no cenário de gateway transparente. Para obter exemplos de como os scripts funcionam, consulte [criar certificados de demonstração para testar IOT Edge recursos do dispositivo](how-to-create-test-certificates.md).

>[!Tip]
> Para ligar os seus dispositivos de "folha" de IoT de dispositivos e aplicações que utilizam o nosso SDK de dispositivo de IoT através do IoT Edge, tem de adicionar o parâmetro GatewayHostName opcional para o fim da cadeia de ligação do dispositivo. Quando o certificado de servidor de Hub do Edge é gerado, baseia-se numa versão em minúsculas do nome de anfitrião do config.yaml, portanto, para os nomes a correspondência e a verificação de certificado TLS com êxito, deve introduzir o parâmetro GatewayHostName em letra minúscula.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exemplo de hierarquia de certificados do IoT Edge

Para ilustrar um exemplo deste caminho do certificado, a seguinte captura de ecrã é de um dispositivo IoT Edge definido como um gateway transparente em funcionamento. O OpenSSL é usado para se conectar ao Hub de IoT Edge, validar e despejar os certificados.

![Captura de ecrã da hierarquia de certificados em cada nível](./media/iot-edge-certs/iotedge-cert-chain.png)

Pode ver a hierarquia de profundidade de certificado representada na captura de ecrã:

| Certificado de AC de raiz         | Teste de certificado de AC do Hub de IoT do Azure apenas                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificado de AC intermediária | Apenas o teste de certificado intermédio Hub IoT do Azure                                                                 |
| Certificado de acesso condicional de dispositivo       | iotgateway.CA ("iotgateway" foi passado como o < nome de anfitrião de gateway > para os scripts de conveniência)   |
| Certificado de AC da carga de trabalho     | AC de carga de trabalho de iotedge                                                                                       |
| IoT Edge certificado do servidor Hub | iotedgegw.local (corresponde ao nome "anfitrião" de config.yaml)                                            |

## <a name="next-steps"></a>Passos seguintes

[Compreender os módulos do Azure IoT Edge](iot-edge-modules.md)

[Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
