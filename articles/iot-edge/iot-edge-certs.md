---
title: Certificados para a segurança de dispositivo - Azure IoT Edge | Documentos da Microsoft
description: O Azure IoT Edge utiliza o certificado para validar a dispositivos, módulos e dispositivos de folha e estabelecer ligações seguras entre eles.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 91cde6965f3635d6d2acfaf581f570779020f8ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60445320"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Detalhes de utilização de certificados do Azure IoT Edge

Certificados de IoT Edge são utilizados para os módulos e a jusante dispositivos de IoT para verificar a identidade e a legitimidade dos [hub do IoT Edge](iot-edge-runtime.md#iot-edge-hub) módulo de runtime que se ligam ao. Estas verificações ativar uma ligação segura TLS (transport layer security) entre o tempo de execução, os módulos e os dispositivos de IoT. Como o IoT Hub em si, IoT Edge requer segura e encriptada ligação a partir do IoT downstream (ou folha) dispositivos e os módulos do IoT Edge. Para estabelecer uma conexão TLS protegida, o módulo de hub do IoT Edge apresenta um servidor de cadeia de certificados para ligação de clientes para que eles para verificar a sua identidade.

Este artigo explica como os certificados do IoT Edge podem trabalhar em produção, desenvolvimento e cenários de teste. Embora os scripts sejam diferente (Powershell vs. bash), os conceitos são os mesmos entre o Linux e Windows.

## <a name="iot-edge-certificates"></a>Certificados do IoT Edge

Normalmente, os fabricantes não são os utilizadores finais de um dispositivo IoT Edge. Às vezes, a relação única entre os dois é quando o utilizador final, ou o operador, compra um dispositivo genérico feito pelo fabricante. Outras vezes, o fabricante funciona sob contrato para criar um dispositivo personalizado em nome do operador. O design de certificado do IoT Edge tenta considerar os dois cenários.

A figura seguinte ilustra a utilização do IoT Edge de certificados. Pode ser zero, um ou vários certificados de assinatura intermediários entre o certificado de AC de raiz e o certificado de AC do dispositivo, consoante o número de entidades envolvidas. Aqui mostramos um caso.

![Diagrama de relações de certificado típico](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Autoridade de certificação

A autoridade de certificação, ou 'CA', para abreviar, é uma entidade que emite certificados digitais. Uma autoridade de certificação atua como um terceiro confiável entre o proprietário e o destinatário do certificado. Um certificado digital certifica a propriedade de uma chave pública pelo destinatário do certificado. A cadeia de certificados de confiança funciona ao inicialmente emitir um certificado de raiz, que é a base para a confiança em todos os certificados emitidos pela autoridade. Depois disso, o proprietário pode utilizar o certificado de raiz para emitir certificados intermédios adicionais (certificados de "folha").

### <a name="root-ca-certificate"></a>Certificado de AC de raiz

Um certificado de AC de raiz é a raiz de confiança de todo o processo. Em cenários de produção, este certificado da AC, normalmente, é comprado numa autoridade de certificado comercial fidedigno como Baltimore, a Verisign ou a DigiCert. Tem controle completo sobre os dispositivos a que se ligam aos seus dispositivos IoT Edge, é possível utilizar uma autoridade de certificação de nível empresarial. Optar por eventos, a cadeia de certificados inteira do hub do IoT Edge, cópia de segurança agrega ao mesmo, para que os dispositivos de IoT de folha tem de confiar no certificado de raiz. Pode armazenar o certificado de AC de raiz no arquivo de autoridade de certificados de raiz fidedigna ou fornecer os detalhes do certificado no código da aplicação.

### <a name="intermediate-certificates"></a>Certificados intermédios

Num processo de fabrico típico para a criação de proteger os dispositivos, os certificados de AC de raiz raramente são usados diretamente, principalmente devido ao risco de fuga ou exposição. O certificado de AC de raiz cria e assina digitalmente um ou mais certificados de AC intermediários. Pode haver apenas um, ou pode haver uma cadeia destes certificados intermédios. Os cenários que necessitem de uma cadeia de certificados intermédios incluem:

* Uma hierarquia de departamentos dentro de um fabricante.

* Várias empresas envolvidas em série na produção de um dispositivo.

* Um cliente comprar uma AC de raiz e efetuar a derivação de um certificado de assinatura para o fabricante para se inscrever os dispositivos fazer em nome do cliente.

De qualquer forma, o fabricante utiliza um certificado de AC intermediária no final desta cadeia para assinar os certificados de AC de dispositivo colocado no dispositivo final. Em geral, esses certificados intermédios estreitamente são protegidos da fábrica de produção. Eles passam por processos strict, físicos e Eletrónicos para a utilização das mesmas.

### <a name="device-ca-certificate"></a>Certificado de acesso condicional de dispositivo

O certificado de AC do dispositivo é gerado a partir do e assinado pelo certificado de AC intermediário final do processo. Este certificado é instalado no dispositivo do IoT Edge, preferencialmente no armazenamento seguro, como um módulo de segurança de hardware (HSM). Além disso, um certificado de AC de dispositivo identifica exclusivamente um dispositivo IoT Edge. Para o IoT Edge, o certificado de AC do dispositivo pode emitir outros certificados. Por exemplo, o certificado do dispositivo AC emite folha certificados de dispositivo que são utilizados para autenticar dispositivos para o [serviço aprovisionamento de dispositivos IoT do Azure](../iot-dps/about-iot-dps.md).

### <a name="iot-edge-workload-ca"></a>Carga de trabalho do IoT Edge AC

O [Gestor de segurança do Microsoft Edge IoT](iot-edge-security-manager.md) gera o certificado de AC da carga de trabalho, o primeiro no lado "operador" do processo, quando o IoT Edge é iniciado pela primeira vez. Este certificado é gerado a partir do e assinado pelo certificado"AC de dispositivo". Este certificado, o que é apenas outro certificado de assinatura intermediário, é utilizado para gerar e assinar todos os certificados utilizados pelo runtime do IoT Edge. Hoje em dia, que é principalmente o IoT Edge hub certificado de servidor discutido na seção a seguir, mas no futuro, pode incluir outros certificados para autenticar os componentes do IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certificado de servidor de hub do IoT Edge

O certificado de servidor de hub do IoT Edge é o certificado de real apresentado para dispositivos de folha e módulos para verificar a identidade durante o estabelecimento da conexão TLS necessária para o IoT Edge. Este certificado apresenta a cadeia completa de certificados de assinatura utilizados para gerá-lo até o certificado de AC de raiz, que o dispositivo de IoT de folha tem de confiar. Quando gerado, o IoT Edge Security Manager, o nome comum (CN), deste hub IoT Edge certificado está definido para a propriedade "hostname" no ficheiro config.yaml após a conversão em minúsculas. Esta é uma fonte comum de confusão com o IoT Edge.

## <a name="production-implications"></a>Implicações de produção

Uma pergunta razoável pode ser "por que motivo do IoT Edge tem a"carga de trabalho AC"certificado extra? Não foi, utilizar o certificado de AC de dispositivo para diretamente gerar o certificado de servidor de hub do IoT Edge? ". Tecnicamente, poderia. No entanto, o objetivo deste certificado intermédio "carga de trabalho" é separar questões entre o fabricante do dispositivo e o operador de dispositivo. Imagine um cenário em que um dispositivo IoT Edge é vendido ou transferido de um cliente para outro. Provavelmente desejaria o certificado de AC do dispositivo fornecido pelo fabricante para ser um imutável. No entanto, os certificados de "carga de trabalho" específicos para o funcionamento do dispositivo devem ser eliminados e recriados para a nova implementação.

Como os processos de produção e de operação estão separados, considere as seguintes implicações ao preparar os dispositivos de produção:

* Com qualquer processo com base em certificado, o certificado de AC de raiz e todos os certificados de AC intermediários devem ser protegidos e monitorizados durante todo o processo de implementar um dispositivo IoT Edge. O fabricante do dispositivo IoT Edge deve ter fortes processos num local de armazenamento adequado e a utilização dos seus certificados intermédios. Além disso, o certificado de AC do dispositivo deve ser mantido em como o armazenamento seguro quanto possível no próprio dispositivo, preferencialmente, um módulo de segurança de hardware.

* O certificado de servidor de hub do IoT Edge é apresentado pelo hub IoT Edge para os dispositivos de cliente de ligação e módulos. O nome comum (CN) do certificado de AC do dispositivo **não tem de ser** o mesmo que o "nome de anfitrião" que será utilizado no config.yaml no dispositivo IoT Edge. O nome utilizado pelos clientes para ligar ao IoT Edge (por exemplo, por meio do parâmetro de GatewayHostName de cadeia de ligação ou o comando CONNECT MQTT) **não pode ser** o mesmo nome comum utilizado no certificado de AC do dispositivo. Esta restrição é porque o hub IoT Edge apresenta sua cadeia de certificados inteira para verificação pelos clientes. Se o certificado de servidor de hub do IoT Edge e o certificado de AC de dispositivo têm o mesmo CN, obtém num loop de verificação e invalida o certificado.

* Como o certificado de AC do dispositivo é usado pelo daemon de segurança de IoT Edge para gerar os certificados de IoT Edge finais, tem-se de ser um certificado de assinatura, que significa que ele tem capacidades de assinatura de certificado. Aplicar automaticamente "V3 restrições básicas CA:True" para o certificado de AC do dispositivo define as propriedades de utilização de chave necessárias.

>[!Tip]
> Se já foi à configuração do IoT Edge como gateway transparente num cenário de programador/teste com o nosso "scripts de conveniência" (consulte a secção seguinte) e usado o mesmo nome de anfitrião ao criar o certificado de AC do dispositivo, como fez com o nome de anfitrião config.yaml , deve estar se perguntando por que ele funcionava. Num esforço para simplificar a experiência do desenvolvedor, os scripts de conveniência acrescenta um ".ca" no final do nome do que se passar para o script. Então, por exemplo, se tiver utilizado "mygateway" para ambos os seu nome de dispositivo os scripts e config.yaml de nome de anfitrião, o primeiro será convertido em mygateway.ca antes de a ser utilizado como o CN para o certificado de AC do dispositivo.

## <a name="devtest-implications"></a>Implicações de Dev/Test

Para facilitar o desenvolvimento e testar cenários, a Microsoft fornece um conjunto de [scripts de conveniência](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) para gerar certificados de não produção adequados para o IoT Edge no cenário de gateway transparente. Para obter exemplos de como funcionam os scripts, consulte [configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md).

Estes scripts geram certificados que seguem a estrutura de cadeia de certificado explicada neste artigo. Os seguintes comandos geram o "certificado de AC de raiz" e um único "certificado de AC intermediária".

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

Da mesma forma, estes comandos geram o "certificado de AC de dispositivo".

```bash
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* O **\<nome do dispositivo de gateway\>** passado para esses scripts **tem não** ser o mesmo que o parâmetro "nome de anfitrião" no config.yaml. Os scripts de ajudá-lo a evitar quaisquer problemas ao acrescentar uma cadeia de caracteres ".ca" para o **\<nome do dispositivo de gateway\>** para evitar a colisão de nome, no caso de um utilizador configura o IoT Edge com o mesmo nome em ambos os locais. No entanto, é boa prática evitar usar o mesmo nome.

>[!Tip]
> Para ligar os seus dispositivos de "folha" de IoT de dispositivos e aplicações que utilizam o nosso SDK de dispositivo de IoT através do IoT Edge, tem de adicionar o parâmetro GatewayHostName opcional para o fim da cadeia de ligação do dispositivo. Quando o certificado de servidor de Hub do Edge é gerado, baseia-se numa versão em minúsculas do nome de anfitrião do config.yaml, portanto, para os nomes a correspondência e a verificação de certificado TLS com êxito, deve introduzir o parâmetro GatewayHostName em letra minúscula.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exemplo de hierarquia de certificados do IoT Edge

Para ilustrar um exemplo deste caminho do certificado, a seguinte captura de ecrã é de um dispositivo IoT Edge definido como um gateway transparente em funcionamento. OpenSSL é utilizado para ligar ao hub IoT Edge, validar e Despejar os certificados.

![Captura de ecrã da hierarquia de certificados em cada nível](./media/iot-edge-certs/iotedge-cert-chain.png)

Pode ver a hierarquia de profundidade de certificado representada na captura de ecrã:

| Certificado de AC de raiz         | Teste de certificado de AC do Hub de IoT do Azure apenas                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificado de AC intermediária | Apenas o teste de certificado intermédio Hub IoT do Azure                                                                 |
| Certificado de acesso condicional de dispositivo       | iotgateway.CA ("iotgateway" foi passado como o < nome de anfitrião de gateway > para os scripts de conveniência)      |
| Certificado de AC da carga de trabalho     | AC de carga de trabalho de iotedge                                                                                       |
| Certificado de servidor de Hub do IoT Edge | iotedgegw.local (corresponde ao nome "anfitrião" de config.yaml)                                                |

## <a name="next-steps"></a>Passos Seguintes

[Compreender os módulos do Azure IoT Edge](iot-edge-modules.md)

[Configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md)