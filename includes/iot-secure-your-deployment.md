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
ms.openlocfilehash: e5acb8e0f8805da7f14bbce58b4bfd2acdc24f23
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65815520"
---
# <a name="secure-your-internet-of-things-iot-deployment"></a>Proteger a sua implementação de Internet das coisas (IoT)

Este artigo fornece o próximo nível de detalhe para proteger a infraestrutura do Azure IoT com base em Internet das coisas (IoT). Ele contém ligações para detalhes de nível de implementação para configurar e implantar cada componente. Ele também fornece comparações de preços e opções de entre vários métodos concorrentes.

Proteger a implementação de IoT do Azure pode ser dividido em áreas de três segurança seguintes:

* **Segurança de dispositivo**: Proteger o dispositivo de IoT quando está a ser implementada por aí.

* **Segurança da ligação**: Garantir que todos os dados transmitidos entre o dispositivo de IoT e IoT Hub é confidencial e à prova.

* **Segurança da cloud**: Fornecer um meio para proteger os dados enquanto se movimenta e são armazenado na cloud.

![Três áreas de segurança](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Proteger o aprovisionamento de dispositivos e de autenticação

Os Aceleradores de solução de IoT proteger dispositivos de IoT com os seguintes dois métodos:

* Ao fornecer uma chave de identidade exclusiva (tokens de segurança) para cada dispositivo, o que pode ser utilizado pelo dispositivo para comunicar com o IoT Hub.

* Ao utilizar um dispositivo [certificado X.509](https://www.itu.int/rec/T-REC-X.509-201210-S) e a chave privada como um meio para autenticar o dispositivo ao IoT Hub. Este método de autenticação garante que a chave privada no dispositivo não é conhecida fora do dispositivo em qualquer altura, fornecendo um nível mais elevado de segurança.

O método de token de segurança fornece autenticação para cada chamada feita pelo dispositivo ao IoT Hub, associando a chave simétrica para cada chamada. Autenticação com base em X.509 permite a autenticação de um dispositivo de IoT na camada física, como parte do estabelecimento da conexão de TLS. O método baseado em token de segurança pode ser utilizado sem a autenticação X.509, o que é um padrão de menos seguro. A escolha entre os dois métodos principalmente é ditada pelo quão segura a necessidades de autenticação do dispositivo para ser e a disponibilidade de armazenamento seguro no dispositivo (para armazenar em segurança a chave privada).

## <a name="iot-hub-security-tokens"></a>Tokens de segurança do Hub IoT

IoT Hub utiliza tokens de segurança para autenticar dispositivos e serviços para evitar o envio de chaves na rede. Além disso, os tokens de segurança são limitados em âmbito e validade de tempo. Os SDKs IoT do Azure automaticamente gerar tokens sem a necessidade de nenhuma configuração especial. No entanto, alguns cenários, exigem que o utilizador gerar e utilizar tokens de segurança diretamente. Estes cenários incluem o uso direto das superfícies MQTT, AMQP ou HTTP, ou a implementação do padrão de serviço de token.

Obter mais detalhes sobre a estrutura de token de segurança e seu uso podem ser encontrados nos seguintes artigos:

* [Estrutura de token de segurança](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Utilizar SAS tokens como um dispositivo](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Cada IoT Hub tem um [registo de identidade](../articles/iot-hub/iot-hub-devguide-identity-registry.md) que podem ser utilizados para criar recursos por dispositivo no serviço, por exemplo, uma fila que contém as mensagens na cloud para o dispositivo em trânsito e para permitir o acesso aos pontos finais com acesso de dispositivo. O registo de identidade do IoT Hub fornece armazenamento seguro de identidades de dispositivo e chaves de segurança para uma solução. Pessoa ou grupos de identidades de dispositivos podem ser adicionados a uma lista de permissões ou uma lista de bloqueios, ativar o controlo total sobre o acesso do dispositivo. Os artigos seguintes fornecem mais detalhes sobre a estrutura do registo de identidade e operações com suporte.

[IoT Hub suporta protocolos como MQTT, AMQP e HTTP](../articles//iot-hub/iot-hub-devguide-security.md). Cada um desses protocolos de forma diferente, utiliza tokens de segurança do dispositivo IoT ao IoT Hub:

* AMQP: SASL simples e a segurança baseada em declarações de AMQP (`{policyName}@sas.root.{iothubName}` com tokens de ao nível do hub IoT; `{deviceId}` com tokens no âmbito do dispositivo).

* MQTT: Utiliza o pacote do CONNECT `{deviceId}` como o `{ClientId}`, `{IoThubhostname}/{deviceId}` no **nome de utilizador** campo e SAS do token no **palavra-passe** campo.

* HTTP: Token válido é no cabeçalho de pedido de autorização.

Registo de identidade do IoT Hub pode ser utilizado para configurar as credenciais de segurança por dispositivo e controlo de acesso. No entanto, se uma solução de IoT que já tem um investimento significativo [esquema de registo e/ou a autenticação de identidade de dispositivo personalizado](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication), ela pode ser integrada numa infra-estrutura existente com o IoT Hub através da criação de um serviço de token.

### <a name="x509-certificate-based-device-authentication"></a>Autenticação de dispositivos baseada em certificado X.509

A utilização de um [certificado X.509 com base no dispositivo](../articles/iot-hub/iot-hub-devguide-security.md) e respetivo associado pública e privada par de chaves permite autenticação adicional na camada física. A chave privada é armazenada em segurança no dispositivo e não é detetável fora do dispositivo. O certificado X.509 contém informações sobre o dispositivo, como ID de dispositivo e outros detalhes organizacionais. Uma assinatura do certificado é gerada por meio da chave privada.

Fluxo de aprovisionamento de dispositivos de alto nível:

* Associe um identificador para um dispositivo físico – identidade de dispositivo e/ou certificado X.509 associados para o dispositivo durante a fabricação ou comissionamento de dispositivo.

* Crie uma entrada de identidade correspondente no IoT Hub – identidade de dispositivo e as informações de dispositivos associados no registo de identidade do IoT Hub.

* Armazene em segurança o thumbprint do certificado X.509 no registo de identidade do IoT Hub.

### <a name="root-certificate-on-device"></a>Certificado de raiz no dispositivo

Ao estabelecer uma conexão TLS segura com o IoT Hub, o dispositivo de IoT autentica o IoT Hub com um certificado de raiz que faz parte do SDK de dispositivo. Para o cliente de C, SDK, o certificado está localizado na pasta "\\c\\certificados" na raiz do repositório. Embora estes certificados de raiz são vida útil longa, eles ainda poderão expirar ou ser revogados. Se não houver nenhuma forma de atualizar o certificado no dispositivo, o dispositivo não poderá, em seguida, ligar para o IoT Hub (ou qualquer outro serviço em nuvem). Ter um meio para atualizar o certificado de raiz, assim que o dispositivo de IoT é implementado efetivamente diminui esse risco.

## <a name="securing-the-connection"></a>Protegendo a ligação

Ligação à Internet entre o dispositivo de IoT e IoT Hub é protegida através do padrão de Transport Layer Security (TLS). O Azure IoT suporta [TLS 1.2](https://tools.ietf.org/html/rfc5246), TLS 1.1 e TLS 1.0, por esta ordem. Suporte para TLS 1.0 é fornecido para apenas a compatibilidade com versões anteriores. Se possível, utilize TLS 1.2, pois ele fornece a maior segurança.

## <a name="securing-the-cloud"></a>Protegendo a nuvem

O IoT Hub do Azure permite a definição da [políticas de controlo de acesso](../articles/iot-hub/iot-hub-devguide-security.md) para cada chave de segurança. Ele usa o seguinte conjunto de permissões para conceder acesso a cada um dos pontos de extremidade do IoT Hub. As permissões limitam o acesso a um Hub IoT com base na funcionalidade.

* **RegistryRead**. Concede acesso de leitura ao registo de identidade. Para obter mais informações, consulte [registo de identidade](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. As concessões de leitura e escrita ao registo de identidade. Para obter mais informações, consulte [registo de identidade](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Concede acesso para a cloud voltado para o serviço de comunicação e monitorização de pontos finais. Por exemplo, ele concede a permissão para serviços cloud de back-end para receber mensagens dispositivo-para-cloud, enviar mensagens da cloud para dispositivo e obter as confirmações de entrega correspondente.

* **DeviceConnect**. Concede acesso a pontos finais de voltado para o dispositivo. Por exemplo, ele concede permissão para enviar mensagens dispositivo-para-cloud e receber mensagens da cloud para o dispositivo. Esta permissão é utilizado pelos dispositivos.

Existem duas formas de obter **DeviceConnect** permissões com o IoT Hub com [tokens de segurança](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): com uma chave de identidade de dispositivo, ou uma chave de acesso partilhado. Além disso, é importante observar que toda a funcionalidade acessível a partir de dispositivos é exposta por design em pontos de extremidade com prefixo `/devices/{deviceId}`.

[Componentes do serviço apenas podem gerar tokens de segurança](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) usando partilhado conceder as permissões adequadas de políticas de acesso.

O IoT Hub do Azure e outros serviços que podem ser parte da solução permitem a gestão de utilizadores que utilizam o Azure Active Directory.

Dados ingeridos pelo IoT Hub do Azure podem ser consumidos por uma variedade de serviços como o Azure Stream Analytics e o armazenamento de Blobs do Azure. Estes serviços permitem o acesso de gestão. Leia mais sobre estes serviços e as opções disponíveis:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Um serviço de base de dados dimensionável e totalmente indexadas para dados semi-estruturados que gere os metadados para os dispositivos, aprovisionar, como atributos, configuração e propriedades de segurança. O Azure Cosmos DB oferece processamento de alto débito e de elevado desempenho, a indexação independente de esquema de dados e uma interface de consulta SQL avançada.

* [O Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Fluxo em tempo real de processamento na cloud que permite-lhe desenvolver e implementar uma solução de análise de baixo custo para revelar informações em tempo real de dispositivos, sensores, infraestrutura e aplicações rapidamente. Os dados a partir deste serviço totalmente gerido podem ser dimensionado para qualquer volume mantendo ainda um débito elevado, baixa latência e resiliência.

* [Serviços aplicacionais do Azure](https://azure.microsoft.com/services/app-service/): Uma plataforma de cloud para criar poderosas aplicações web e móveis que se ligam a dados em qualquer lugar; na cloud ou no local. Crie aplicações móveis cativantes para iOS, Android e Windows. Integre com o seu Software como serviço (SaaS) e aplicações empresariais a conectividade de out-of-the-box com dezenas de serviços baseados na nuvem e aplicações empresariais. Programar em sua linguagem preferida e IDE (.NET, node. js, PHP, Python ou Java) para criar aplicações web e APIs mais rápido do que nunca.

* [Aplicações lógicas](https://azure.microsoft.com/services/app-service/logic/): A funcionalidade de aplicações lógicas do serviço de aplicações do Azure ajuda a integrar a sua solução de IoT para seus sistemas de linha de negócio existentes e automatizar processos de fluxo de trabalho. O Logic Apps permite aos programadores conceber fluxos de trabalho que começam com um acionador e, em seguida, executam uma série de passos, regras e ações que utilizam conectores poderosas para integrar com seus processos de negócios. O Logic Apps oferece a conectividade de out-of-the-box para um vasto ecossistema de SaaS, com base na cloud e aplicações no local.

* [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/): Armazenamento de cloud fiável e económico para dados que os seus dispositivos enviam para a cloud.

## <a name="conclusion"></a>Conclusão

Este artigo fornece detalhes de nível para estruturar e implementar uma infraestrutura IoT na utilização do Azure IoT de descrição geral da implementação. Configurar cada componente para ser seguro é a chave protegendo a infra-estrutura de IoT global. As escolhas de design disponíveis no Azure IoT fornecerem algum nível de flexibilidade e poder de escolha; No entanto, cada escolha pode ter implicações de segurança. Recomenda-se que cada uma dessas opções avaliadas através de uma avaliação de risco/custo.
