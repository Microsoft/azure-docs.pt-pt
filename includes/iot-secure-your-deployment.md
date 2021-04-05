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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010708"
---
Este artigo fornece o próximo nível de detalhe para garantir a infraestrutura internet das coisas (IoT) baseada em Azure IoT. Liga-se aos detalhes do nível de implementação para configurar e implantar cada componente. Também fornece comparações e escolhas entre vários métodos concorrentes.

A fixação do Azure IoT pode ser dividida nas seguintes três áreas de segurança:

* **Segurança do dispositivo**: Fixar o dispositivo IoT enquanto este é implantado na natureza.

* **Garantia de que** todos os dados transmitidos entre o dispositivo IoT e o IoT Hub são confidenciais e à prova de adulteração.

* **Cloud Security**: Fornecendo um meio para proteger os dados enquanto este se move, e é armazenado na nuvem.

![Três áreas de segurança](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Provisão e autenticação de dispositivos seguros

Os aceleradores de solução IoT asseguram dispositivos IoT utilizando os seguintes dois métodos:

* Ao fornecer uma chave de identidade única (fichas de segurança) para cada dispositivo, que pode ser usada pelo dispositivo para comunicar com o Hub IoT.

* Utilizando um certificado [X.509](https://www.itu.int/rec/T-REC-X.509-201210-S) no dispositivo e uma chave privada como forma de autenticar o dispositivo no IoT Hub. Este método de autenticação garante que a chave privada do dispositivo não seja conhecida fora do dispositivo em momento algum, proporcionando um nível de segurança mais elevado.

O método do token de segurança fornece autenticação para cada chamada feita pelo dispositivo ao IoT Hub associando a chave simétrica a cada chamada. A autenticação baseada em X.509 permite a autenticação de um dispositivo IoT na camada física como parte do estabelecimento de ligação TLS. O método baseado em fichas de segurança pode ser usado sem a autenticação X.509, que é um padrão menos seguro. A escolha entre os dois métodos é principalmente ditada pela segurança da autenticação do dispositivo e pela disponibilidade de armazenamento seguro no dispositivo (para armazenar a chave privada de forma segura).

## <a name="iot-hub-security-tokens"></a>Fichas de segurança IoT Hub

O IoT Hub utiliza fichas de segurança para autenticar dispositivos e serviços para evitar o envio de chaves na rede. Além disso, as fichas de segurança são limitadas na validade e âmbito de tempo. Os SDKs Azure IoT geram automaticamente fichas sem necessitar de qualquer configuração especial. Alguns cenários, no entanto, exigem que o utilizador gere e utilize fichas de segurança diretamente. Estes cenários incluem a utilização direta das superfícies MQTT, AMQP ou HTTP, ou a implementação do padrão de serviço simbólico.

Mais detalhes sobre a estrutura do token de segurança e a sua utilização podem ser encontrados nos seguintes artigos:

* [Estrutura simbólica de segurança](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Usando fichas SAS como dispositivo](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Cada IoT Hub tem um [registo de identidade](../articles/iot-hub/iot-hub-devguide-identity-registry.md) que pode ser usado para criar recursos por dispositivo no serviço, como uma fila que contém mensagens nuvem-para-dispositivo em voo, e para permitir o acesso aos pontos finais voltados para o dispositivo. O registo de identidade IoT Hub fornece armazenamento seguro de identidades do dispositivo e chaves de segurança para uma solução. Indivíduos ou grupos de identidades do dispositivo podem ser adicionados a uma lista de autorizações, ou a uma lista de blocos, permitindo o controlo total sobre o acesso ao dispositivo. Os seguintes artigos fornecem mais detalhes sobre a estrutura do registo de identidade e operações apoiadas.

[O IoT Hub suporta protocolos como MQTT, AMQP e HTTP](../articles//iot-hub/iot-hub-devguide-security.md). Cada um destes protocolos utiliza fichas de segurança do dispositivo IoT para ioT Hub de forma diferente:

* AMQP: SEGURANÇA baseada em SASL PLAIN e AMQP Claims `{policyName}@sas.root.{iothubName}` (com fichas de nível IoT; `{deviceId}` com fichas de dispositivo).

* MQTT: O pacote CONNECT utiliza `{deviceId}` como , no campo `{ClientId}` `{IoThubhostname}/{deviceId}` **username** e um token SAS no campo **Palavra-passe.**

* HTTP: O token válido está no cabeçalho do pedido de autorização.

O registo de identidade ioT Hub pode ser usado para configurar credenciais de segurança por dispositivo e controlo de acesso. No entanto, se uma solução IoT já tiver um investimento significativo num [registo de identidade de dispositivo personalizado e/ou esquema de autenticação,](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)pode ser integrado numa infraestrutura existente com o IoT Hub através da criação de um serviço simbólico.

### <a name="x509-certificate-based-device-authentication"></a>Autenticação do dispositivo baseado em certificado X.509

A utilização de um [certificado X.509 baseado no dispositivo](../articles/iot-hub/iot-hub-devguide-security.md) e o seu par de chaves privados e públicos associados permitem a autenticação adicional na camada física. A chave privada é armazenada de forma segura no dispositivo e não é detetável fora do dispositivo. O certificado X.509 contém informações sobre o dispositivo, como o ID do dispositivo, e outros detalhes organizacionais. A assinatura do certificado é gerada utilizando a chave privada.

Fluxo de provisão de dispositivos de alto nível:

* Associar um identificador a um dispositivo físico – identidade do dispositivo e/ou certificado X.509 associado ao dispositivo durante o fabrico ou comissionamento do dispositivo.

* Crie uma correspondente entrada de identidade no IoT Hub – identidade do dispositivo e informações associadas do dispositivo no registo de identidade do IoT Hub.

* Armazenar de forma segura a impressão digital do certificado X.509 no registo de identidade do IoT Hub.

### <a name="root-certificate-on-device"></a>Certificado de raiz no dispositivo

Ao estabelecer uma ligação TLS segura com o IoT Hub, o dispositivo IoT autentica o IoT Hub utilizando um certificado de raiz que faz parte do dispositivo SDK. Para o cliente C SDK, o certificado está localizado sob a pasta " \\ c \\ certs" sob a raiz do repo. Embora estes certificados de raiz sejam de longa duração, ainda podem expirar ou ser revogados. Se não houver forma de atualizar o certificado no dispositivo, o dispositivo poderá não conseguir ligar-se posteriormente ao IoT Hub (ou a qualquer outro serviço de nuvem). Ter um meio de atualizar o certificado raiz uma vez que o dispositivo IoT é implantado eficazmente atenua este risco.

## <a name="securing-the-connection"></a>Assegurar a ligação

A ligação à Internet entre o dispositivo IoT e o IoT Hub é protegida utilizando a norma de Segurança da Camada de Transporte (TLS). A azure IoT suporta [TLS 1.2](https://tools.ietf.org/html/rfc5246), TLS 1.1 e TLS 1.0, por esta ordem. O suporte para TLS 1.0 é previsto apenas para retrocompatibilidade. Consulte o [suporte do TLS no IoT Hub](../articles/iot-hub/iot-hub-tls-support.md) para ver como configurar o seu hub para utilizar o TLS 1.2, uma vez que proporciona mais segurança.

## <a name="securing-the-cloud"></a>Proteger a nuvem

O Azure IoT Hub permite a definição de políticas de controlo de [acesso](../articles/iot-hub/iot-hub-devguide-security.md) para cada chave de segurança. Utiliza o seguinte conjunto de permissões para conceder acesso a cada um dos pontos finais do IoT Hub. As permissões limitam o acesso a um Hub IoT baseado na funcionalidade.

* **RegistryRead**. Os subsídios lêem o acesso ao registo de identidade. Para mais informações, consulte [o registo de identidade.](../articles/iot-hub/iot-hub-devguide-identity-registry.md)

* **RegistroReadWrite**. Os subsídios lêem e escrevem acesso ao registo de identidade. Para mais informações, consulte [o registo de identidade.](../articles/iot-hub/iot-hub-devguide-identity-registry.md)

* **ServiceConnect**. Concede acesso a pontos finais de comunicação e monitorização de serviços na nuvem. Por exemplo, concede permissão aos serviços de cloud back-end para receber mensagens de dispositivo para nuvem, enviar mensagens cloud-to-device e recuperar os correspondentes reconhecimentos de entrega.

* **DispositivoConnect**. Concede acesso a pontos finais voltados para o dispositivo. Por exemplo, concede permissão para enviar mensagens de dispositivo para nuvem e receber mensagens nuvem-a-dispositivo. Esta permissão é utilizada por dispositivos.

Existem duas formas de obter permissões **DeviceConnect** com IoT Hub com [fichas de segurança](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): usando uma chave de identidade do dispositivo ou uma chave de acesso partilhada. Além disso, é importante notar que todas as funcionalidades acessíveis a partir de dispositivos são expostas por design em pontos finais com `/devices/{deviceId}` prefixo.

[Os componentes de serviço só podem gerar fichas de segurança](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) utilizando políticas de acesso partilhado que concedem as permissões adequadas.

O Azure IoT Hub e outros serviços que possam fazer parte da solução permitem a gestão dos utilizadores utilizando o Diretório Ativo Azure.

Os dados ingeridos pelo Azure IoT Hub podem ser consumidos por uma variedade de serviços, tais como Azure Stream Analytics e Azure blob storage. Estes serviços permitem o acesso à gestão. Leia mais sobre estes serviços e opções disponíveis:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Um serviço de base de dados escalável e totalmente indexado para dados semi-estruturados que gere metadados para os dispositivos que fornece, tais como atributos, configuração e propriedades de segurança. A Azure Cosmos DB oferece processamento de alto desempenho e alta produção, indexação de dados schema-agnóstico e uma interface de consulta SQL rica.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Processamento de fluxo em tempo real na nuvem que lhe permite desenvolver e implementar rapidamente uma solução de análise de baixo custo para descobrir insights em tempo real de dispositivos, sensores, infraestruturas e aplicações. Os dados deste serviço totalmente gerido podem escalar para qualquer volume, enquanto ainda alcançam alta produção, baixa latência e resiliência.

* [Azure App Services](https://azure.microsoft.com/services/app-service/): Uma plataforma em nuvem para construir poderosas aplicações web e móveis que se conectam a dados em qualquer lugar; na nuvem ou no local. Crie aplicativos móveis envolventes para iOS, Android e Windows. Integre-se com o seu Software como um Serviço (SaaS) e aplicações empresariais com conectividade fora da caixa a dezenas de serviços baseados na nuvem e aplicações empresariais. Código na sua língua favorita e IDE (.NET, Node.js, PHP, Python ou Java) para construir aplicações web e APIs mais rapidamente do que nunca.

* [Aplicações lógicas](https://azure.microsoft.com/services/app-service/logic/): A funcionalidade de Aplicações Lógicas do Azure App Service ajuda a integrar a sua solução IoT nos seus sistemas de linha de negócio existentes e automatiza processos de fluxo de trabalho. As Aplicações Lógicas permitem aos desenvolvedores conceber fluxos de trabalho que começam a partir de um gatilho e depois executam uma série de passos - regras e ações que usam conectores poderosos para se integrarem com os seus processos de negócio. A Logic Apps oferece conectividade fora da caixa a um vasto ecossistema de aplicações SaaS, baseadas em nuvem e no local.

* [Armazenamento Azure Blob](https://azure.microsoft.com/services/storage/): Armazenamento em nuvem fiável e económico para os dados que os seus dispositivos enviam para a nuvem.

## <a name="conclusion"></a>Conclusão

Este artigo fornece uma visão geral dos detalhes do nível de implementação para a conceção e implantação de uma infraestrutura IoT utilizando a Azure IoT. Configurar cada componente para ser seguro é fundamental para assegurar a infraestrutura IoT global. As opções de design disponíveis no Azure IoT proporcionam algum nível de flexibilidade e escolha; no entanto, cada escolha pode ter implicações de segurança. Recomenda-se que cada uma destas escolhas seja avaliada através de uma avaliação de risco/custo.
