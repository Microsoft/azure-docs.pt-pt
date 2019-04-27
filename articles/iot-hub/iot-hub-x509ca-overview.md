---
title: Descrição geral da segurança de AC X.509 do Azure IoT Hub | Documentos da Microsoft
description: Descrição geral - como fazer a autenticação de dispositivos no IoT Hub com autoridades de certificado X.509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: b7464e5cc052ecade4a10102de947d37a63c962a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615021"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Autenticação do dispositivo utilizando certificados de AC X.509

Este artigo descreve como utilizar certificados de autoridade de certificado X.509 (AC) para autenticar os dispositivos ligados do IoT Hub.  Neste artigo, ficará a saber:

* Como obter um certificado X.509 de AC
* Como registar o certificado X.509 de AC para o IoT Hub
* Como se inscrever dispositivos com certificados X.509 de AC
* Como os dispositivos que tem sessão iniciados com X.509 de AC são autenticados

## <a name="overview"></a>Descrição geral

A funcionalidade de AC X.509 permite a autenticação de dispositivos no IoT Hub com um certificado de autoridade (CA). Que simplifica o processo de inscrição de dispositivos inicial e logística de cadeia de fornecimento durante a produção de dispositivo. [Saiba mais neste artigo de cenário sobre o valor de utilização de certificados X.509 de AC](iot-hub-x509ca-concept.md) para autenticação do dispositivo.  É recomendável que leia este artigo de cenário antes de continuar, como explica por que os passos que se seguem existem.

## <a name="prerequisite"></a>Pré-requisito

Utilizar a funcionalidade de AC X.509 requer que tenha uma conta do IoT Hub.  [Saiba como criar uma instância do IoT Hub](quickstart-send-telemetry-dotnet.md) se ainda não tiver uma.

## <a name="how-to-get-an-x509-ca-certificate"></a>Como obter um certificado X.509 de AC

O certificado X.509 de AC está no topo da cadeia de certificados para cada um dos seus dispositivos.  Pode comprar ou criá-lo consoante a forma como pretende utilizá-lo.

Para o ambiente de produção, recomendamos que adquira um certificado X.509 de AC de uma autoridade de certificação de raiz público. Comprar um certificado de AC tem a vantagem da AC de raiz que atua como um terceiro confiável para comprovar a legitimidade dos seus dispositivos. Considere esta opção se pretende que os dispositivos para fazer parte de uma rede de IoT aberta onde que devem ser interagir com os produtos de terceiros ou serviços.

Também pode criar uma AC de X.509 autoassinado para a experimentação, ou para utilização em redes de IoT fechados.

Independentemente de como obter o certificado X.509 de AC, certifique-se de manter o respetivo correspondente privada chave segredo e protegidos, o tempo todo.  Isso é necessário para criar a confiança na autenticação de X.509 de AC de confiança.

Saiba como [criar um certificado de AC autoassinado](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), que pode utilizar para a experimentação ao longo desta descrição do recurso.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Dispositivos de início de sessão para a cadeia de certificados de confiança

O proprietário de um certificado X.509 de AC criptograficamente pode iniciar sessão uma AC intermédia que por sua vez pode se outra AC intermediária, e assim por diante, até o último intermediário AC termina este processo ao inscrever um dispositivo. O resultado é uma cadeia de cascata de certificados, conhecido como uma cadeia de certificados de confiança. Na vida real isso continua como delegação de confiança para inscrever dispositivos. Esta delegação é importante porque estabelece uma variável criptograficamente cadeia de custódia e evita a partilha de chaves de assinatura.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

O certificado do dispositivo (também chamado de um certificado de folha) tem de ter o *nome do requerente* definido como o **ID do dispositivo** que foi utilizado quando registar o dispositivo de IoT no IoT Hub do Azure. Esta definição é necessária para a autenticação.

Saiba aqui como [criar uma cadeia de certificados](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) como feito ao inscrever dispositivos.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Como registar o certificado X.509 de AC para o IoT Hub

Registe o seu certificado X.509 de AC para o IoT Hub em que irá ser utilizado para autenticar os seus dispositivos durante o registo e a ligação.  Registo do certificado X.509 de AC é um processo de dois passos, que abrange o carregamento do ficheiro de certificado e uma prova de posse.

O processo de carregamento envolve carregar um ficheiro que contém o certificado.  Este ficheiro nunca deve conter quaisquer chaves privadas.

A prova de passo de posse envolve um desafio de criptografia e o processo de resposta entre e o IoT Hub.  Dado que o conteúdo de certificado digital é pública e, portanto, suscetível à interceptação, o IoT Hub gostaria apurar que é proprietário, realmente, o certificado da AC.  Ele deverá fazê-lo através da geração de um desafio aleatório, que tem de iniciar sessão com a chave privada correspondente do certificado de AC.  Se manteve a chave privada secreta e protegido como anteriormente aconselhado, em seguida, apenas serão possuem o conhecimento para concluir este passo. Segredo de chaves privadas é a fonte de confiança nesse método.  Depois de iniciar o desafio, conclua este passo ao carregar um ficheiro que contém os resultados.

Saiba aqui como [registar o seu certificado de AC](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Como criar um dispositivo no IoT Hub

Para impedir a representação de dispositivo, o IoT Hub requer que ele saiba que dispositivos esperar.  Pode fazê-lo através da criação de uma entrada de dispositivo no registo de dispositivo do IoT Hub.  Este processo é automatizado ao utilizar o IoT Hub [serviço aprovisionamento de dispositivos](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/). 

Saiba aqui como [criar manualmente um dispositivo no IoT Hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Criar um dispositivo X.509 para o seu hub IoT

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Autenticar os dispositivos assinados com certificados X.509 de AC

Com o certificado de AC X.509 registado e dispositivos conectados a uma cadeia de certificados de confiança, o que permanece é a autenticação do dispositivo quando o dispositivo estabelece ligação, até mesmo pela primeira vez.  Quando uma AC X.509 assinado o dispositivo estabelece ligação, carrega sua cadeia de certificados para validação. A cadeia inclui todos os certificados de AC e dispositivo intermédios.  Com essas informações, o IoT Hub autentica o dispositivo num processo de dois passos.  IoT Hub criptograficamente valida a cadeia de certificados para consistência interna e, em seguida, emite um desafio de prova de posse no dispositivo.  IoT Hub declara o dispositivo autêntica numa resposta com êxito de uma prova de posse do dispositivo.  Essa declaração parte do princípio de que a chave privada do dispositivo está protegido e que apenas o dispositivo pode responder com êxito esse desafio.  Recomendamos a utilização de chips seguros, como o Hardware seguro módulos (HSM) nos dispositivos para proteger chaves privadas.

Uma ligação do dispositivo concluída com êxito para o IoT Hub conclui o processo de autenticação e também é indicativa de uma instalação correta.

Saiba aqui como [concluir este passo de ligação do dispositivo](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre [o valor da autenticação X.509 de AC](iot-hub-x509ca-concept.md) no IoT.

Introdução ao IoT Hub [serviço aprovisionamento de dispositivos](https://docs.microsoft.com/azure/iot-dps/).
