---
title: Certificados de segurança do dispositivo - Azure IoT Edge | Microsoft Docs
description: O Azure IoT Edge utiliza certificado para validar dispositivos, módulos e dispositivos de folha e estabelecer ligações seguras entre eles.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: e5b1950935e6279995b44c2e07931519e82359d2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040639"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Entenda como Azure IoT Edge usa certificados

Os certificados IoT Edge são utilizados pelos módulos e dispositivos IoT a jusante para verificar a identidade e a legitimidade do módulo de tempo de funcionaamento do [hub IoT Edge.](iot-edge-runtime.md#iot-edge-hub) Estas verificações permitem uma ligação segura TLS (segurança da camada de transporte) entre o tempo de funcionaamento, os módulos e os dispositivos IoT. Tal como o próprio IoT Hub, o IoT Edge requer uma ligação segura e encriptada a partir de dispositivos IoT a jusante (ou folha) e módulos IoT Edge. Para estabelecer uma ligação TLS segura, o módulo hub IoT Edge apresenta uma cadeia de certificados de servidor para ligar clientes para verificar a sua identidade.

>[!NOTE]
>Este artigo fala sobre os certificados utilizados para garantir ligações entre os diferentes componentes de um dispositivo IoT Edge ou entre um dispositivo IoT Edge e quaisquer dispositivos de folha. Também pode utilizar certificados para autenticar o seu dispositivo IoT Edge no IoT Hub. Estes certificados de autenticação são diferentes e não são discutidos neste artigo. Para obter mais informações sobre a autenticação do seu dispositivo com certificados, consulte [Criar e forneça um dispositivo IoT Edge utilizando certificados X.509](how-to-auto-provision-x509-certs.md).

Este artigo explica como os certificados IoT Edge podem funcionar em cenários de produção, desenvolvimento e teste. Embora os scripts sejam diferentes (PowerShell vs. bash), os conceitos são os mesmos entre Linux e Windows.

## <a name="iot-edge-certificates"></a>Certificados do IoT Edge

Existem dois cenários comuns para a criação de certificados num dispositivo IoT Edge. Por vezes, o utilizador final, ou operador, de um dispositivo compra um dispositivo genérico feito por um fabricante e gere os próprios certificados. Outras vezes, o fabricante trabalha sob contrato para construir um dispositivo personalizado para o operador e faz algumas assinaturas de certificados iniciais antes de entregar o dispositivo. O design do certificado IoT Edge tenta ter em conta ambos os cenários.

A seguinte figura ilustra a utilização de certificados da IoT Edge. Pode haver zero, um ou muitos certificados de assinatura intermédia entre o certificado de CA raiz e o certificado de CA do dispositivo, dependendo do número de entidades envolvidas. Aqui mostramos um caso.

![Diagrama de relações típicas de certificado](./media/iot-edge-certs/edgeCerts-general.png)

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Atualmente, uma limitação no libiothsmo impede a utilização de certificados que expirem em ou após 1 de janeiro de 2038. Esta limitação aplica-se ao certificado de CA do dispositivo, a quaisquer certificados no pacote fiduciário e aos certificados de identificação do dispositivo utilizados para métodos de provisionamento X.509.

:::moniker-end

### <a name="certificate-authority"></a>Autoridade de certificação

A autoridade de certificados, ou 'CA' para abreviar, é uma entidade que emite certificados digitais. Uma autoridade de certificado atua como um terceiro de confiança entre o proprietário e o destinatário do certificado. Um certificado digital certifica a propriedade de uma chave pública pelo destinatário do certificado. A cadeia de certificados trust funciona através da emissão inicial de um certificado de raiz, que constitui a base para a confiança em todos os certificados emitidos pela autoridade. Posteriormente, o proprietário pode utilizar o certificado raiz para emitir certificados intermédios adicionais (certificados 'folha').

### <a name="root-ca-certificate"></a>Certificado root CA

Um certificado de CA raiz é a raiz da confiança de todo o processo. Em cenários de produção, este certificado de CA é geralmente comprado a uma autoridade de certificados comerciais fidedignas como Baltimore, Verisign ou DigiCert. Caso tenha controlo total sobre os dispositivos que se ligam aos seus dispositivos IoT Edge, é possível utilizar uma autoridade de certificados de nível corporativo. Em qualquer dos casos, toda a cadeia de certificados do hub IoT Edge passa a rolar até ele, pelo que os dispositivos IoT da folha devem confiar no certificado raiz. Pode armazenar o certificado de CA raiz na loja de certificados de raiz fidedigna ou fornecer os dados do certificado no seu código de aplicação.

### <a name="intermediate-certificates"></a>Certificados intermédios

Num processo de fabrico típico para a criação de dispositivos seguros, os certificados de CA de raiz raramente são utilizados diretamente, principalmente devido ao risco de fuga ou exposição. O certificado de CA raiz cria e assina digitalmente um ou mais certificados de CA intermédios. Pode haver apenas um, ou pode haver uma cadeia destes certificados intermédios. Os cenários que exigiriam uma cadeia de certificados intermédios incluem:

* Uma hierarquia de departamentos dentro de um fabricante.

* Várias empresas envolveram-se em série na produção de um dispositivo.

* Um cliente que compra uma CA de raiz e deriva um certificado de assinatura para o fabricante assinar os dispositivos que faz em nome desse cliente.

Em todo o caso, o fabricante utiliza um certificado de CA intermédio no final desta cadeia para assinar o certificado de CA do dispositivo colocado no dispositivo final. Geralmente, estes certificados intermédios estão bem guardados na fábrica. São submetidos a processos rigorosos, tanto físicos como eletrónicos para o seu uso.

### <a name="device-ca-certificate"></a>Certificado ca dispositivo

O certificado ca do dispositivo é gerado e assinado pelo certificado de AC intermédio final no processo. Este certificado está instalado no próprio dispositivo IoT Edge, de preferência em armazenamento seguro, como um módulo de segurança de hardware (HSM). Além disso, um certificado de CA do dispositivo identifica exclusivamente um dispositivo IoT Edge. O certificado de CA do dispositivo pode assinar outros certificados.

### <a name="iot-edge-workload-ca"></a>IoT Edge Workload CA

O [IoT Edge Security Manager](iot-edge-security-manager.md) gera o certificado de CA de carga de trabalho, o primeiro no lado "operador" do processo, quando o IoT Edge começa pela primeira vez. Este certificado é gerado e assinado pelo certificado de CA do dispositivo. Este certificado, que é apenas mais um certificado de assinatura intermédia, é utilizado para gerar e assinar quaisquer outros certificados utilizados pelo tempo de execução IoT Edge. Hoje em dia, este é principalmente o certificado de servidor hub IoT Edge discutido na seguinte secção, mas no futuro pode incluir outros certificados para autenticação de componentes IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certificado de servidor de hub IoT Edge

O certificado de servidor do hub IoT Edge é o certificado real apresentado aos dispositivos e módulos de folha para verificação de identidade durante o estabelecimento da ligação TLS exigida pela IoT Edge. Este certificado apresenta toda a cadeia de certificados de assinatura utilizados para o gerar até ao certificado de CA raiz, em que o dispositivo IoT da folha deve confiar. Quando gerado pela IoT Edge, o nome comum (CN), deste certificado hub IoT Edge é definido como a propriedade 'hostname' no ficheiro config após conversão para caso inferior. Esta configuração é uma fonte comum de confusão com IoT Edge.

## <a name="production-implications"></a>Implicações na produção

Uma pergunta razoável pode ser "por que ioT Edge precisa do certificado extra 'workload CA'? Não poderia usar o certificado de CA do dispositivo para gerar diretamente o certificado de servidor do hub IoT Edge?". Tecnicamente, pode. No entanto, o objetivo deste certificado intermédio de "carga de trabalho" é separar as preocupações entre o fabricante do dispositivo e o operador do dispositivo. Imagine um cenário em que um dispositivo IoT Edge é vendido ou transferido de um cliente para outro. É provável que o certificado de CA do dispositivo fornecido pelo fabricante seja imutável. No entanto, os certificados de "carga de trabalho" específicos para o funcionamento do dispositivo devem ser limpos e recriados para a nova implantação.

Uma vez que os processos de fabrico e operação estão separados, considere as seguintes implicações na preparação dos dispositivos de produção:

* Com qualquer processo baseado em certificados, o certificado de CA raiz e todos os certificados de CA intermédios devem ser protegidos e monitorizados durante todo o processo de lançamento de um dispositivo IoT Edge. O fabricante do dispositivo IoT Edge deve dispor de processos fortes para o armazenamento e utilização adequados dos seus certificados intermédios. Além disso, o certificado ca do dispositivo deve ser mantido no armazenamento o mais seguro possível no próprio dispositivo, de preferência um módulo de segurança de hardware.

* O certificado de servidor do hub IoT Edge é apresentado pelo hub IoT Edge aos dispositivos e módulos do cliente de ligação. O nome comum (CN) do certificado CA do dispositivo **não deve ser** o mesmo que o "nome de hospedeiro" que será utilizado no ficheiro config no dispositivo IoT Edge. O nome utilizado pelos clientes para ligar ao IoT Edge (por exemplo, através do parâmetro GatewayHostName da cadeia de ligação ou do comando CONNECT em MQTT) **não pode ser** o mesmo que o nome comum usado no certificado CA do dispositivo. Esta restrição deve-se ao facto de o hub IoT Edge apresentar toda a sua cadeia de certificados para verificação por parte dos clientes. Se o certificado de servidor do hub IoT Edge e o certificado ca do dispositivo ambos tiverem o mesmo CN, você recebe um loop de verificação e o certificado invalida.

* Uma vez que o certificado CA do dispositivo é utilizado pelo daemon de segurança IoT Edge para gerar os certificados finais IoT Edge, ele próprio deve ser um certificado de assinatura, o que significa que tem capacidades de assinatura de certificado. Aplicando "V3 Restrições básicas CA:True" no certificado ca do dispositivo configura automaticamente as propriedades de utilização de chaves necessárias.

>[!Tip]
> Se já passou pela configuração do IoT Edge como uma porta de entrada transparente num cenário dev/teste usando os nossos "scripts de conveniência" (ver secção seguinte) e usou o mesmo nome de anfitrião ao criar o certificado de CA do dispositivo como fez para o nome de anfitrião no ficheiro config, pode estar a perguntar-se porque é que funcionou. Num esforço para simplificar a experiência do desenvolvedor, os scripts de conveniência anexam um ".ca" no final do nome que você passa para o script. Assim, por exemplo, se usou "mygateway" tanto para o nome do seu dispositivo nos scripts como no nome de anfitrião no ficheiro config, o primeiro será transformado em mygateway.ca antes de ser usado como CN para o dispositivo CA cert.

## <a name="devtest-implications"></a>Implicações de dev/teste

Para facilitar o desenvolvimento e os cenários de teste, a Microsoft fornece um conjunto de [scripts](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) de conveniência para gerar certificados de não produção adequados para ioT Edge no cenário transparente gateway. Por exemplo, como funcionam os scripts, consulte [criar certificados de demonstração para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md).

>[!Tip]
> Para ligar os dispositivos IoT "leaf" e aplicações que utilizam o nosso dispositivo IoT SDK através do IoT Edge, deve adicionar o parâmetro opcional GatewayHostName na extremidade da cadeia de ligação do dispositivo. Quando o Certificado do Servidor Edge Hub é gerado, baseia-se numa versão minúscula do nome anfitrião a partir do ficheiro config, portanto, para que os nomes correspondam e a verificação do certificado TLS seja bem sucedida, deve introduzir o parâmetro GatewayHostName em caso inferior.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exemplo da hierarquia do certificado IoT Edge

Para ilustrar um exemplo deste percurso de certificado, a imagem a seguir é de um dispositivo IoT Edge em funcionamento configurado como um gateway transparente. O OpenSSL é utilizado para ligar ao hub IoT Edge, validar e despejar os certificados.

![Screenshot da hierarquia do certificado em cada nível](./media/iot-edge-certs/iotedge-cert-chain.png)

Pode ver a hierarquia da profundidade do certificado representada na imagem:

| Certificado root CA         | Teste de Azure IoT Hub CA Cert                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificado intermédio de CA | Teste de cert intermédio Azure IoT Hub                                                                 |
| Certificado ca dispositivo       | iotgateway.ca ("iotgateway" foi passado como o nome de anfitrião de gateway < > para os scripts de conveniência)   |
| Certificado ca de carga de trabalho     | iotedge carga de trabalho ca                                                                                       |
| Certificado de servidor IoT Edge Hub | iotedgegw.local (corresponde ao 'nome anfitrião' do ficheiro config)                                            |

## <a name="next-steps"></a>Passos seguintes

[Compreender os módulos do Azure IoT Edge](iot-edge-modules.md)

[Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
