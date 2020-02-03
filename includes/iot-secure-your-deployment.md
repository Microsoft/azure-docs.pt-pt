---
title: incluir ficheiro
description: incluir ficheiro
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748957"
---
Este artigo fornece o próximo nível de detalhes para proteger a infraestrutura de IoT (Internet das Coisas baseada em IoT) do Azure. Ele é vinculado aos detalhes do nível de implementação para configurar e implantar cada componente. Ele também fornece comparações e escolhas entre vários métodos concorrentes.

A proteção da implantação do Azure IoT pode ser dividida em três áreas de segurança a seguir:

* **Segurança do dispositivo**: Fixar o dispositivo IoT enquanto este estiver implantado na natureza.

* **Segurança da Ligação**: Garantir que todos os dados transmitidos entre o dispositivo IoT e o IoT Hub são confidenciais e à prova de adulteração.

* **Segurança na Nuvem**: Fornecendo um meio para proteger os dados enquanto se move, e é armazenado na nuvem.

![Três áreas de segurança](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Provisionamento e autenticação de dispositivos seguros

Os aceleradores de solução de IoT protegem dispositivos IoT usando os dois métodos a seguir:

* Fornecendo uma chave de identidade exclusiva (tokens de segurança) para cada dispositivo, que pode ser usada pelo dispositivo para se comunicar com o Hub IoT.

* Utilizando um certificado [X.509](https://www.itu.int/rec/T-REC-X.509-201210-S) no dispositivo e uma chave privada como forma de autenticar o dispositivo ao IoT Hub. Esse método de autenticação garante que a chave privada no dispositivo não seja conhecida fora do dispositivo a qualquer momento, fornecendo um nível mais alto de segurança.

O método de token de segurança fornece autenticação para cada chamada feita pelo dispositivo ao Hub IoT associando a chave simétrica a cada chamada. A autenticação baseada em X. 509 permite a autenticação de um dispositivo IoT na camada física como parte do estabelecimento de conexão TLS. O método baseado em token de segurança pode ser usado sem a autenticação X. 509, que é um padrão menos seguro. A escolha entre os dois métodos é determinada principalmente pelo quão segura a autenticação do dispositivo precisa ser e a disponibilidade do armazenamento seguro no dispositivo (para armazenar a chave privada com segurança).

## <a name="iot-hub-security-tokens"></a>Fichas de segurança do IoT Hub

O Hub IoT usa tokens de segurança para autenticar dispositivos e serviços para evitar o envio de chaves na rede. Além disso, os tokens de segurança são limitados em tempo de validade e escopo. Os SDKs do Azure IoT geram automaticamente tokens sem a necessidade de qualquer configuração especial. No entanto, alguns cenários exigem que o usuário gere e use tokens de segurança diretamente. Esses cenários incluem o uso direto das superfícies MQTT, AMQP ou HTTP ou a implementação do padrão de serviço de token.

Mais detalhes sobre a estrutura do token de segurança e seu uso podem ser encontrados nos seguintes artigos:

* [Estrutura simbólica de segurança](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Usando tokens SAS como um dispositivo](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Cada IoT Hub tem um registo de [identidade](../articles/iot-hub/iot-hub-devguide-identity-registry.md) que pode ser usado para criar recursos por dispositivo no serviço, como uma fila que contém mensagens cloud-to-device a bordo, e para permitir o acesso aos pontos finais virados para o dispositivo. O registro de identidade do Hub IoT fornece armazenamento seguro de identidades de dispositivo e chaves de segurança para uma solução. Um indivíduo ou grupos de identidades de dispositivo podem ser adicionados a uma lista de permissões ou a uma lista de bloqueios, permitindo o controle completo sobre o acesso ao dispositivo. Os artigos a seguir fornecem mais detalhes sobre a estrutura do registro de identidade e as operações com suporte.

[O IoT Hub suporta protocolos como MQTT, AMQP e HTTP](../articles//iot-hub/iot-hub-devguide-security.md). Cada um desses protocolos usa tokens de segurança do dispositivo IoT para o Hub IoT de maneira diferente:

* AMQP: Segurança baseada em sinistros SASL PLAIN e AMQP (`{policyName}@sas.root.{iothubName}` com tokens de nível ioT; `{deviceId}` com tokens com mira de dispositivo).

* MQTT: O pacote CONNECT utiliza `{deviceId}` como o `{ClientId}`, `{IoThubhostname}/{deviceId}` no campo **Username** e um token SAS no campo **Password.**

* HTTP: o token válido está no cabeçalho da solicitação de autorização.

O registro de identidade do Hub IoT pode ser usado para configurar credenciais de segurança por dispositivo e controle de acesso. No entanto, se uma solução IoT já tiver um investimento significativo num sistema de [registo de identidade de dispositivo personalizado e/ou sistema de autenticação,](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)pode ser integrado numa infraestrutura existente com o IoT Hub através da criação de um serviço simbólico.

### <a name="x509-certificate-based-device-authentication"></a>Autenticação de dispositivo baseado em certificado X. 509

A utilização de um [certificado X.509 baseado no dispositivo](../articles/iot-hub/iot-hub-devguide-security.md) e o seu par de chaves particulares e públicas associados permite a autenticação adicional na camada física. A chave privada é armazenada com segurança no dispositivo e não é detectável fora do dispositivo. O certificado X. 509 contém informações sobre o dispositivo, como ID do dispositivo e outros detalhes organizacionais. Uma assinatura do certificado é gerada usando a chave privada.

Fluxo de provisionamento de dispositivos de alto nível:

* Associe um identificador a um dispositivo físico – identidade do dispositivo e/ou certificado X. 509 associado ao dispositivo durante a fabricação ou o encerramento do dispositivo.

* Crie uma entrada de identidade correspondente no Hub IoT – identidade do dispositivo e informações do dispositivo associado no registro de identidade do Hub IoT.

* Armazene com segurança a impressão digital do certificado X. 509 no registro de identidade do Hub IoT.

### <a name="root-certificate-on-device"></a>Certificado raiz no dispositivo

Ao estabelecer uma conexão TLS segura com o Hub IoT, o dispositivo IoT autentica o Hub IoT usando um certificado raiz que faz parte do SDK do dispositivo. Para o cliente C SDK, o certificado está localizado sob a pasta "\\c\\certs" sob a raiz do repo. Embora esses certificados raiz sejam de longa duração, eles ainda podem expirar ou ser revogados. Se não houver nenhuma maneira de atualizar o certificado no dispositivo, o dispositivo poderá não ser capaz de se conectar posteriormente ao Hub IoT (ou a qualquer outro serviço de nuvem). Ter um meio de atualizar o certificado raiz quando o dispositivo IoT é implantado efetivamente reduz esse risco.

## <a name="securing-the-connection"></a>Protegendo a conexão

A conexão com a Internet entre o dispositivo IoT e o Hub IoT é protegida usando o padrão TLS (Transport Layer Security). O Azure IoT suporta [TLS 1.2,](https://tools.ietf.org/html/rfc5246)TLS 1.1 e TLS 1.0, por esta ordem. O suporte para TLS 1,0 é fornecido apenas para fins de compatibilidade com versões anteriores. Verifique o [suporte TLS no IoT Hub](../articles/iot-hub/iot-hub-tls-support.md) para ver como configurar o seu hub para utilizar o TLS 1.2, uma vez que proporciona mais segurança.

## <a name="securing-the-cloud"></a>Protegendo a nuvem

O Azure IoT Hub permite a definição de políticas de controlo de [acesso](../articles/iot-hub/iot-hub-devguide-security.md) para cada chave de segurança. Ele usa o seguinte conjunto de permissões para conceder acesso a cada um dos pontos de extremidade do Hub IoT. As permissões limitam o acesso a um Hub IoT com base na funcionalidade.

* **RegistoRead**. Concede acesso de leitura ao registro de identidade. Para mais informações, consulte [o registo de identidade.](../articles/iot-hub/iot-hub-devguide-identity-registry.md)

* **RegistoReadWrite**. Concede acesso de leitura e gravação ao registro de identidade. Para mais informações, consulte [o registo de identidade.](../articles/iot-hub/iot-hub-devguide-identity-registry.md)

* **ServiceConnect**. Concede acesso a pontos de extremidade de comunicação e monitoramento voltados para o serviço de nuvem. Por exemplo, ele concede permissão aos serviços de nuvem de back-end para receber mensagens do dispositivo para a nuvem, enviar mensagens da nuvem para o dispositivo e recuperar as confirmações de entrega correspondentes.

* **DispositivoConnect**. Concede acesso a pontos de extremidade voltados para o dispositivo. Por exemplo, ele concede permissão para enviar mensagens do dispositivo para a nuvem e receber mensagens da nuvem para o dispositivo. Essa permissão é usada pelos dispositivos.

Existem duas formas de obter permissões **DeviceConnect** com fichas de [segurança:](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)utilizando uma chave de identidade do dispositivo ou uma chave de acesso partilhada. Além disso, é importante notar que todas as funcionalidades acessíveis a partir de dispositivos são expostas por design em pontos finais com prefixo `/devices/{deviceId}`.

[Os componentes de serviço só podem gerar fichas](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) de segurança utilizando políticas de acesso partilhado que concedem as permissões adequadas.

O Hub IoT do Azure e outros serviços que podem fazer parte da solução permitem o gerenciamento de usuários usando o Azure Active Directory.

Os dados ingeridos pelo Hub IoT do Azure podem ser consumidos por uma variedade de serviços, como Azure Stream Analytics e armazenamento de BLOBs do Azure. Esses serviços permitem o acesso de gerenciamento. Leia mais sobre esses serviços e as opções disponíveis:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Um serviço de base de dados escalável e totalmente indexado para dados semi-estruturados que gere metadados para os dispositivos que disponibiliza, tais como atributos, configuração e propriedades de segurança. O Azure Cosmos DB oferece processamento de alto desempenho e alta taxa de transferência, indexação independente de esquema de dados e uma interface de consulta SQL rica.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Processamento de fluxo em tempo real na nuvem que lhe permite desenvolver e implementar rapidamente uma solução de análise de baixo custo para descobrir insights em tempo real de dispositivos, sensores, infraestruturas e aplicações. Os dados desse serviço totalmente gerenciado podem ser dimensionados para qualquer volume e, ao mesmo tempo, obter alta taxa de transferência, baixa latência e resiliência.

* [Azure App Services](https://azure.microsoft.com/services/app-service/): Uma plataforma cloud para construir aplicações web e móveis poderosas que se conectam a dados em qualquer lugar; na nuvem ou no local. Construa aplicações móveis envolventes para iOS, Android e Windows. Integre-se com seu SaaS (software como serviço) e aplicativos empresariais com conectividade integrada a dezenas de serviços baseados em nuvem e aplicativos empresariais. Codifique em sua linguagem favorita e IDE (.NET, Node. js, PHP, Python ou Java) para criar aplicativos Web e APIs mais rápido do que nunca.

* [Aplicações Lógicas](https://azure.microsoft.com/services/app-service/logic/): A funcionalidade de Aplicações Lógicas do Serviço de Aplicações Azure ajuda a integrar a sua solução IoT aos seus sistemas de linha de negócio existentes e automatizaos processos de fluxo de trabalho. Os aplicativos lógicos permitem que os desenvolvedores projetem fluxos de trabalho que começam de um gatilho e, em seguida, executam uma série de etapas — regras e ações que usam conectores poderosos para integração com seus processos de negócios. Os aplicativos lógicos oferecem conectividade pronta para uso a um vasto ecossistema de aplicativos SaaS, baseados em nuvem e locais.

* [Armazenamento Azure Blob](https://azure.microsoft.com/services/storage/): Armazenamento fiável e económico em nuvem para os dados que os seus dispositivos enviam para a nuvem.

## <a name="conclusion"></a>Conclusão

Este artigo fornece uma visão geral dos detalhes do nível de implementação para projetar e implantar uma infraestrutura de IoT usando o Azure IoT. Configurar cada componente para ser seguro é fundamental para proteger a infraestrutura de IoT geral. As opções de design disponíveis no Azure IoT fornecem algum nível de flexibilidade e opção; no entanto, cada opção pode ter implicações de segurança. É recomendável que cada uma dessas opções seja avaliada por meio de uma avaliação de risco/custo.
