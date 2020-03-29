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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748957"
---
Este artigo fornece o próximo nível de detalhe para assegurar a infraestrutura de Internet das Coisas (IoT) baseada em Azure IoT. Liga-se a detalhes do nível de implementação para configurar e implementar cada componente. Também fornece comparações e escolhas entre vários métodos concorrentes.

A fixação da implantação do Azure IoT pode ser dividida nas seguintes três áreas de segurança:

* **Segurança do dispositivo**: Fixar o dispositivo IoT enquanto este estiver implantado na natureza.

* **Segurança da Ligação**: Garantir que todos os dados transmitidos entre o dispositivo IoT e o IoT Hub são confidenciais e à prova de adulteração.

* **Segurança na Nuvem**: Fornecendo um meio para proteger os dados enquanto se move, e é armazenado na nuvem.

![Três áreas de segurança](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Fornecimento e autenticação de dispositivos seguros

Os aceleradores de soluções IoT protegem os dispositivos IoT utilizando os seguintes dois métodos:

* Fornecendo uma chave de identidade única (fichas de segurança) para cada dispositivo, que pode ser usado pelo dispositivo para comunicar com o IoT Hub.

* Utilizando um certificado [X.509](https://www.itu.int/rec/T-REC-X.509-201210-S) no dispositivo e uma chave privada como forma de autenticar o dispositivo ao IoT Hub. Este método de autenticação garante que a chave privada do dispositivo não é conhecida em nenhum momento fora do dispositivo, proporcionando um nível de segurança mais elevado.

O método de ficha de segurança fornece a autenticação para cada chamada feita pelo dispositivo ao IoT Hub associando a chave simétrica a cada chamada. A autenticação baseada em X.509 permite a autenticação de um dispositivo IoT na camada física como parte do estabelecimento de ligação TLS. O método baseado em token de segurança pode ser usado sem a autenticação X.509, que é um padrão menos seguro. A escolha entre os dois métodos é principalmente ditada pela segurança da autenticação do dispositivo e pela disponibilidade de armazenamento seguro no dispositivo (para armazenar a chave privada de forma segura).

## <a name="iot-hub-security-tokens"></a>Fichas de segurança do IoT Hub

O IoT Hub utiliza fichas de segurança para autenticar dispositivos e serviços para evitar o envio de chaves na rede. Além disso, as fichas de segurança são limitadas na validade e âmbito do tempo. Os SDKs Azure IoT geram automaticamente fichas sem necessitar de qualquer configuração especial. Alguns cenários, no entanto, exigem que o utilizador gere e utilize fichas de segurança diretamente. Estes cenários incluem a utilização direta das superfícies MQTT, AMQP ou HTTP, ou a implementação do padrão de serviço simbólico.

Mais detalhes sobre a estrutura do símbolo de segurança e a sua utilização podem ser encontrados nos seguintes artigos:

* [Estrutura simbólica de segurança](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Usando tokens SAS como um dispositivo](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Cada IoT Hub tem um registo de [identidade](../articles/iot-hub/iot-hub-devguide-identity-registry.md) que pode ser usado para criar recursos por dispositivo no serviço, como uma fila que contém mensagens cloud-to-device a bordo, e para permitir o acesso aos pontos finais virados para o dispositivo. O registo de identidade IoT Hub fornece armazenamento seguro de identidades do dispositivo e chaves de segurança para uma solução. As identidades individuais ou grupos de dispositivos podem ser adicionadas a uma lista de licenciamento, ou a uma lista de blocos, permitindo o controlo total do acesso ao dispositivo. Os seguintes artigos fornecem mais detalhes sobre a estrutura do registo de identidade e operações apoiadas.

[O IoT Hub suporta protocolos como MQTT, AMQP e HTTP](../articles//iot-hub/iot-hub-devguide-security.md). Cada um destes protocolos utiliza fichas de segurança do dispositivo IoT para IoT Hub de forma diferente:

* AMQP: Segurança baseada em sinistros SASL`{policyName}@sas.root.{iothubName}` PLAIN e AMQP (com fichas de nível ioT; `{deviceId}` com fichas com mira de dispositivo).

* MQTT: O `{deviceId}` pacote `{ClientId}`CONNECT `{IoThubhostname}/{deviceId}` utiliza como o , no campo **Username** e um token SAS no campo **Password.**

* HTTP: Ficha válida está no cabeçalho do pedido de autorização.

O registo de identidade do IoT Hub pode ser utilizado para configurar credenciais de segurança por dispositivo e controlo de acesso. No entanto, se uma solução IoT já tiver um investimento significativo num sistema de [registo de identidade de dispositivo personalizado e/ou sistema de autenticação,](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)pode ser integrado numa infraestrutura existente com o IoT Hub através da criação de um serviço simbólico.

### <a name="x509-certificate-based-device-authentication"></a>Autenticação do dispositivo baseado em certificadoX.509

A utilização de um [certificado X.509 baseado no dispositivo](../articles/iot-hub/iot-hub-devguide-security.md) e o seu par de chaves particulares e públicas associados permite a autenticação adicional na camada física. A chave privada é armazenada de forma segura no dispositivo e não é detetável fora do dispositivo. O certificado X.509 contém informações sobre o dispositivo, tais como identificação do dispositivo, e outros detalhes organizacionais. Uma assinatura do certificado é gerada através da utilização da chave privada.

Fluxo de provisionamento de dispositivos de alto nível:

* Associar um identificador a um dispositivo físico – identidade do dispositivo e/ou certificado X.509 associado ao dispositivo durante o fabrico ou comissionamento do dispositivo.

* Criar uma entrada de identidade correspondente no IoT Hub – identidade do dispositivo e informações associadas ao dispositivo no registo de identidade IoT Hub.

* Guarde de forma segura a impressão digital do certificado X.509 no registo de identidade do IoT Hub.

### <a name="root-certificate-on-device"></a>Certificado de raiz no dispositivo

Ao estabelecer uma ligação TLS segura com o IoT Hub, o dispositivo IoT autentica o IoT Hub utilizando um certificado de raiz que faz parte do dispositivo SDK. Para o cliente C SDK, o certificado\\\\está localizado sob a pasta "c certs" sob a raiz do repo. Embora estes certificados de raiz sejam de longa duração, ainda podem expirar ou ser revogados. Se não houver forma de atualizar o certificado no dispositivo, o dispositivo poderá não conseguir ligar-se posteriormente ao IoT Hub (ou a qualquer outro serviço na nuvem). Ter um meio de atualizar o certificado de raiz uma vez que o dispositivo IoT é implantado eficazmente atenua este risco.

## <a name="securing-the-connection"></a>Assegurar a ligação

A ligação à Internet entre o dispositivo IoT e o IoT Hub é segura utilizando a norma Transport Layer Security (TLS). O Azure IoT suporta [TLS 1.2,](https://tools.ietf.org/html/rfc5246)TLS 1.1 e TLS 1.0, por esta ordem. O suporte para TLS 1.0 é fornecido apenas para retrocompatibilidade. Verifique o [suporte TLS no IoT Hub](../articles/iot-hub/iot-hub-tls-support.md) para ver como configurar o seu hub para utilizar o TLS 1.2, uma vez que proporciona mais segurança.

## <a name="securing-the-cloud"></a>Proteger a nuvem

O Azure IoT Hub permite a definição de políticas de controlo de [acesso](../articles/iot-hub/iot-hub-devguide-security.md) para cada chave de segurança. Utiliza o seguinte conjunto de permissões para dar acesso a cada um dos pontos finais do IoT Hub. As permissões limitam o acesso a um Hub IoT baseado na funcionalidade.

* **RegistoRead**. As subvenções lêem o acesso ao registo de identidade. Para mais informações, consulte [o registo de identidade.](../articles/iot-hub/iot-hub-devguide-identity-registry.md)

* **RegistoReadWrite**. As subvenções lêem e escrevem o acesso ao registo de identidade. Para mais informações, consulte [o registo de identidade.](../articles/iot-hub/iot-hub-devguide-identity-registry.md)

* **ServiceConnect**. Concede acesso à comunicação virada para o serviço na nuvem e aos pontos finais de monitorização. Por exemplo, concede permissão para serviços de nuvem de back-end para receber mensagens de dispositivo-para-nuvem, enviar mensagens cloud-to-device e recuperar os correspondentes reconhecimentos de entrega.

* **DispositivoConnect**. Concede acesso a pontos finais virados para dispositivos. Por exemplo, concede permissão para enviar mensagens de dispositivo-nuvem e receber mensagens cloud-to-device. Esta permissão é utilizada por dispositivos.

Existem duas formas de obter permissões **DeviceConnect** com fichas de [segurança:](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)utilizando uma chave de identidade do dispositivo ou uma chave de acesso partilhada. Além disso, é importante notar que todas as funcionalidades acessíveis a partir `/devices/{deviceId}`de dispositivos são expostas por design em pontos finais com prefixo .

[Os componentes de serviço só podem gerar fichas](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) de segurança utilizando políticas de acesso partilhado que concedem as permissões adequadas.

O Azure IoT Hub e outros serviços que podem fazer parte da solução permitem a gestão dos utilizadores utilizando o Diretório Ativo Azure.

Os dados ingeridos pelo Azure IoT Hub podem ser consumidos por uma variedade de serviços como o Azure Stream Analytics e o armazenamento de blob Azure. Estes serviços permitem o acesso à gestão. Leia mais sobre estes serviços e opções disponíveis:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Um serviço de base de dados escalável e totalmente indexado para dados semi-estruturados que gere metadados para os dispositivos que disponibiliza, tais como atributos, configuração e propriedades de segurança. A Azure Cosmos DB oferece processamento de alto desempenho e alta perputa, indexação schema-agnóstica de dados e uma rica interface de consulta SQL.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Processamento de fluxo em tempo real na nuvem que lhe permite desenvolver e implementar rapidamente uma solução de análise de baixo custo para descobrir insights em tempo real de dispositivos, sensores, infraestruturas e aplicações. Os dados deste serviço totalmente gerido podem escalar para qualquer volume, enquanto ainda alcançam alta sucusão, baixa latência e resiliência.

* [Azure App Services](https://azure.microsoft.com/services/app-service/): Uma plataforma cloud para construir aplicações web e móveis poderosas que se conectam a dados em qualquer lugar; na nuvem ou no local. Construa aplicações móveis envolventes para iOS, Android e Windows. Integre com o seu Software como Serviço (SaaS) e aplicações empresariais com conectividade fora da caixa a dezenas de serviços baseados na nuvem e aplicações empresariais. Código na sua língua favorita e IDE (.NET, Node.js, PHP, Python ou Java) para construir aplicações web e APIs mais rápido do que nunca.

* [Aplicações Lógicas](https://azure.microsoft.com/services/app-service/logic/): A funcionalidade de Aplicações Lógicas do Serviço de Aplicações Azure ajuda a integrar a sua solução IoT aos seus sistemas de linha de negócio existentes e automatizaos processos de fluxo de trabalho. As Aplicações Lógicas permitem aos desenvolvedores conceber fluxos de trabalho que partam de um gatilho e depois executar uma série de etapas — regras e ações que utilizam conectores poderosos para se integrarem com os seus processos de negócio. As Aplicações Lógicas oferecem conectividade fora da caixa a um vasto ecossistema de aplicações SaaS, baseadas em nuvem e no local.

* [Armazenamento Azure Blob](https://azure.microsoft.com/services/storage/): Armazenamento fiável e económico em nuvem para os dados que os seus dispositivos enviam para a nuvem.

## <a name="conclusion"></a>Conclusão

Este artigo fornece uma visão geral dos detalhes do nível de implementação para a conceção e implantação de uma infraestrutura IoT utilizando o Azure IoT. Configurar cada componente para ser seguro é fundamental para assegurar a infraestrutura ioT global. As opções de design disponíveis no Azure IoT proporcionam algum nível de flexibilidade e escolha; no entanto, cada escolha pode ter implicações de segurança. Recomenda-se que cada uma destas escolhas seja avaliada através de uma avaliação de risco/custo.
