---
title: incluir ficheiro
description: incluir ficheiro
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 14e8eb7c73a1e2cf5047410d3571008c1cd1e1ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626484"
---
# <a name="security-for-internet-of-things-iot-from-the-ground-up"></a>Segurança para Internet das coisas (IoT) desde o início

A Internet das coisas (IoT) apresenta desafios únicos de segurança, privacidade e conformidade para empresas em todo o mundo. Ao contrário de tecnologia de cibersegurança tradicional onde esses problemas giram em torno de software e como ele é implementado, IoT diz respeito o que acontece quando a informáticas e os mundos físicos convergem. Proteger soluções de IoT requer a garantia de seguro de aprovisionamento de dispositivos, conectividade segura entre estes dispositivos e a cloud e a proteção de proteger os dados na cloud durante o processamento e armazenamento. No entanto, trabalhar em relação a essa funcionalidade, são dispositivos de restrição de recursos, a distribuição geográfica de implementações e um grande número de dispositivos numa solução.

Este artigo explora a forma como os Aceleradores de solução de IoT fornecem uma solução de nuvem de Internet das coisas segura e privada. Os solution accelerators fornecem uma solução ponto-a-ponto completa, com segurança incorporada em cada fase, a partir do zero. Na Microsoft, o desenvolvimento de software seguro é a parte a prática de engenharia de software, enraizada em décadas da Microsoft há muito tempo a experiência de desenvolvimento de software seguro. Para garantir esta situação, o Security Development Lifecycle (SDL) é a metodologia de desenvolvimento fundamentais, juntamente com um host de serviços de segurança ao nível da infraestrutura, como a garantia de segurança operacional (OSA) e a Microsoft Digital Crimes Unit, Microsoft Security Response Center e Microsoft Malware Protection Center.

Os Aceleradores de solução oferecem recursos exclusivos, esse tipo de aprovisionamento de marca, a estabelecer ligação e armazenar dados a partir de dispositivos de IoT simples e transparentes e, mais de tudo, segura. Este artigo examina os recursos de segurança de Aceleradores de solução de IoT do Azure e estratégias de implantação para garantir a segurança, privacidade e desafios de compatibilidade são resolvidas.

## <a name="introduction"></a>Introdução

A Internet das coisas (IoT) é a onda do futuro, oferecendo as empresas imediatas e oportunidades de mundo real para reduzir os custos, aumentar a receita e transforme o seu negócio. Muitas empresas, no entanto, são reticentes quanto ao implementar o IoT em suas organizações devido a preocupações com segurança, privacidade e conformidade. Um ponto de principais de preocupação é proveniente da exclusividade da infra-estrutura de IoT, que une o informáticas e mundos físicos em conjunto, para piorar individuais riscos inerentes a esses dois mundos. Segurança de IoT que diz respeito a garantir a integridade de código em execução em dispositivos, fornecendo autenticação de dispositivo e utilizador, definindo a propriedade clara dos dispositivos (bem como dados gerados por esses dispositivos) e a ser resiliente a informáticas e ataques físicos.

Em seguida, há o problema de privacidade. As empresas desejam transparência sobre a recolha de dados, como no qual estão a ser recolhido e por isso, que pode ver, que controla o acesso e assim por diante. Por fim, existem problemas de manutenção de padrões da indústria de conformidade e resolver problemas de segurança geral dos equipamentos, juntamente com as pessoas operacional-los.

Tendo em conta a segurança, privacidade, transparência e preocupações de conformidade, escolha o fornecedor de solução IoT certo permanece um desafio. Em conjunto clips partes individuais de software de IoT e serviços fornecidos por uma variedade de fornecedores apresenta lacunas na segurança, privacidade, transparência e conformidade, o que pode ser difícil de detetar, sem falar em corrigir. A escolha do fornecedor de software e serviço baseia-se sobre como encontrar os fornecedores que têm vasta experiência a executar os serviços, que abrangem as aplicações e localizações geográficas, mas também são capazes de dimensionar de forma segura e transparente de IoT correta. Da mesma forma, é útil para o fornecedor selecionado ter décadas de experiência com o desenvolvimento de software seguro em execução em milhares de milhões de máquinas em todo o mundo e ter a capacidade para apreciar o Panorama de ameaças imposto por neste novo mundo da Internet das coisas.

## <a name="secure-infrastructure-from-the-ground-up"></a>Infraestrutura segura desde o início

O [Microsoft Cloud](https://azure.microsoft.com) infraestrutura suporta mais de mil milhões de clientes em 127 países. Desenhando em décadas de experiência da Microsoft desenvolvimento de software empresarial e gestão de alguns dos maiores serviços online do mundo, a Cloud da Microsoft fornece níveis mais altos de maior segurança, privacidade, conformidade e ameaças práticas de atenuação a maioria dos clientes obteria por conta própria.

O [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) fornece um processo de desenvolvimento de toda a empresa obrigatório que incorpora requisitos de segurança no ciclo de vida do software completo. Para ajudar a garantir que as atividades operacionais seguem o mesmo nível de práticas de segurança, o SDL utiliza as diretrizes de segurança rigorosas descritas no processo de garantia de segurança operacional (OSA) da Microsoft. A Microsoft também trabalha com empresas de auditoria de terceiros para efeitos de verificação em curso que ele atenda às suas obrigações reguladoras de conformidade e Microsoft trabalha com esforços de segurança abrangente por meio da criação dos centros de excelência, incluindo a Microsoft Digital Crimes Unit, Microsoft Security Response Center e Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure - infraestrutura IoT segura para a sua empresa

O Microsoft Azure oferece uma solução na cloud completa, que combina uma coleção crescente de serviços cloud integrados — análise, machine learning, armazenamento, segurança, redes e web — com um compromisso com o líder da indústria para a proteção e privacidade dos seus dados. Da Microsoft [assumir que houve violação](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) estratégia usa um dedicado *red team* de especialistas de segurança de software que simular ataques, teste a capacidade do Azure para detetar, proteger contra ameaças emergentes e recuperar contra falhas. Da Microsoft [resposta a incidentes globais](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) equipe trabalha ininterruptamente para atenuar os efeitos dos ataques e atividade maliciosa. A equipe segue procedimentos estabelecidos para gestão de incidentes, a comunicação e a recuperação e utiliza interfaces Detetáveis e previsíveis com parceiros internos e externos.

Sistemas da Microsoft fornecem deteção de intrusões contínua e prevenção, prevenção de ataques do serviço, os testes de penetração regular e ferramentas forenses que ajudam a identificar e atenuar ameaças. [Autenticação multifator](../articles/active-directory/authentication/multi-factor-authentication.md) fornece uma camada extra de segurança para os utilizadores finais acedam à rede. E para a aplicação e o fornecedor de anfitrião, a Microsoft oferece o controlo de acesso, monitorização, antimalware, análise de vulnerabilidade, patches e gerenciamento de configuração.

Os solution accelerators tirar partido da segurança e privacidade incorporadas na plataforma do Azure, juntamente com os processos SDL e OSA para desenvolvimento seguro e o funcionamento do software da Microsoft. Esses procedimentos fornecem proteção de infraestrutura e funcionalidades de gestão de identidades e fundamentais para a segurança de qualquer solução de proteção de rede.

O [IoT Hub do Azure](../articles/iot-hub/about-iot-hub.md) dentro do [Aceleradores de solução de IoT](../articles/iot-fundamentals/iot-introduction.md) oferece um serviço totalmente gerido que permite a comunicação de bidirecional de confiável e segura entre dispositivos IoT e os serviços do Azure, tal como [Do azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) e [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) utilizando as credenciais de segurança por dispositivo e controlo de acesso.

Para comunicar melhor segurança e as funcionalidades de privacidade incorporadas nos Aceleradores de solução de IoT do Azure, neste artigo divide o conjunto em três áreas de segurança principal.

![Aceleradores de soluções do Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Proteger o aprovisionamento de dispositivos e de autenticação

Os solution accelerators proteger dispositivos enquanto estiverem fora no campo, fornecendo uma chave de identidade exclusiva para cada dispositivo, que pode ser usado pela infra-estrutura de IoT para comunicar com o dispositivo enquanto está em operação. O processo é rápido e fácil de configurar. A chave gerada com um ID de dispositivo selecionados pelo usuário compõe a base de um token utilizado em toda a comunicação entre o dispositivo e o IoT Hub do Azure.

Identificações de dispositivo podem ser associadas um dispositivo durante a produção (o que é, flashed num módulo de hardware de confiança) ou podem utilizar um existente fixado identidade como um proxy (por exemplo números de série de CPU). Como alterar estas informações de identificação no dispositivo não é simple, é importante introduzir os IDs de dispositivo lógico, no caso das alterações de hardware do dispositivo subjacentes, mas o dispositivo lógico permanece o mesmo. Em alguns casos, a associação de uma identidade de dispositivo pode acontecer no momento da implementação de dispositivo (por exemplo, um engenheiro de campo autenticado fisicamente configura um novo dispositivo ao comunicar com o back-end de solução). O [registo de identidade do IoT Hub do Azure](../articles/iot-hub/iot-hub-devguide.md) fornece armazenamento seguro de identidades de dispositivo e chaves de segurança para uma solução. Pessoa ou grupos de identidades de dispositivos podem ser adicionados a uma lista de permissões ou uma lista de bloqueios, ativar o controlo total sobre o acesso do dispositivo.

Políticas de controlo do IoT Hub acesso do Azure na cloud permitem a ativação e desativação de qualquer identidade de dispositivo, oferecendo uma forma de desassociação de um dispositivo de uma implementação de IoT quando necessário. Esta associação e desassociação de dispositivos é baseado em cada identidade de dispositivo.

As funcionalidades de segurança de dispositivo adicionais incluem:

* Os dispositivos não aceitam ligações de rede não solicitado. Estes estabeleçam todas as ligações e rotas de uma forma apenas de saída. Para um dispositivo receba um comando do back-end, o dispositivo tem de iniciar uma ligação para verificar a existência de quaisquer comandos pendentes processar. Depois de estabelecer uma ligação entre o dispositivo e o IoT Hub é em segurança, mensagens a partir da cloud para o dispositivo e dispositivo para a cloud pode ser enviado de forma transparente.

* Dispositivos apenas estabelecem a ligação ou rotas a serviços bem conhecidos com os quais estão emparelhados, como um IoT Hub do Azure.

* Autenticação e autorização ao nível do sistema utilizam identidades por dispositivo, tornando as credenciais de acesso e permissões perto-instantaneamente revogáveis.

### <a name="secure-connectivity"></a>Conectividade segura

A durabilidade de mensagens é um recurso importante de qualquer solução de IoT. A necessidade de maneira duradoura disponibilizar comandos de e/ou receber dados a partir de dispositivos está sublinhada pelo fato de que os dispositivos IoT estiverem ligados através da Internet, ou a outras redes semelhante que podem não ser confiáveis. O IoT Hub do Azure oferece a durabilidade de mensagens entre a cloud e dispositivos através de um sistema de agradecimentos em resposta a mensagens. Uma durabilidade adicional para mensagens é obtida ao colocar em cache as mensagens no IoT Hub até sete dias para telemetria e de dois dias para comandos.

Eficiência é importante certificar-se a conservação dos recursos e a operação num ambiente de restrição de recursos. HTTPS (HTTP seguro), a versão segura de norma da indústria do protocolo http populares, é suportada pelo IoT Hub do Azure, ativar a comunicação eficiente. Advanced Message Queuing Protocol (AMQP) e mensagem de colocação em fila Telemetry Transport (MQTT), suportado pelo IoT Hub do Azure, destinam-se não apenas para uma eficiência em termos de uso de recursos, mas também a entrega de mensagens confiável. 

Escalabilidade requer a capacidade de forma segura de interagir com uma vasta gama de dispositivos. O hub IoT do Azure permite a conexão segura com dispositivos habilitados para IP tanto não ativado IP. Dispositivos com capacidade de IP conseguem ligar diretamente e se comunicar com o IoT Hub através de uma ligação segura. Dispositivos com capacidade de IP-não são a restrição de recursos e ligam apenas através de protocolos de comunicação de curta distância, como Zwave, ZigBee e Bluetooth. Um gateway de campo é utilizado para agregar estes dispositivos e realiza a tradução do protocolo para ativar a comunicação bidirecional segura com a cloud.

As funcionalidades de segurança de ligação adicionais incluem:

* O caminho de comunicação entre dispositivos e IoT Hub do Azure ou entre gateways e o IoT Hub do Azure, é protegido usando o padrão da indústria Transport Layer Security (TLS) com o Azure IoT Hub autenticado utilizando o protocolo X.509.

* Para proteger os dispositivos de ligações de entrada não solicitadas, o IoT Hub do Azure não abrir qualquer ligação para o dispositivo. O dispositivo inicia todas as ligações.

* IoT Hub do Azure armazena mensagens para dispositivos de maneira duradoura e aguarda que o dispositivo ligar. Estes comandos são armazenados durante dois dias, permitindo que os dispositivos que ligam esporadicamente, devido a problemas de energia ou conectividade, para receber estes comandos. O IoT Hub do Azure mantém uma fila por dispositivo para cada dispositivo.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Proteger o processamento e armazenamento na cloud

De comunicações encriptadas para processar os dados na cloud, os solution accelerators ajudam a manter os dados seguros. Ele fornece a flexibilidade para implementar a encriptação adicional e a gestão de chaves de segurança.

Utilizar o Azure Active Directory (AAD) para autorização e autenticação de utilizador, aceleradores de solução de IoT do Azure podem fornecer um modelo de autorização baseada em política para dados na cloud, a ativação da gestão de acesso fácil que pode ser auditada e revisto. Este modelo também permite a revogação de quase imediata de acesso a dados na cloud e de dispositivos ligados aos Aceleradores de solução de IoT do Azure.

Quando os dados estiverem na cloud, podem ser processado e armazenado em qualquer fluxo de trabalho definidos pelo utilizador. Acesso a cada parte dos dados é controlado com o Azure Active Directory, dependendo do serviço de armazenamento utilizado.

Todas as chaves usadas pela infra-estrutura IoT são armazenadas na cloud no armazenamento seguro, com a capacidade de fazer o rollover no caso de chaves tem de ser reaprovisionada. Dados podem ser armazenados no [do Azure Cosmos DB](../articles/cosmos-db/introduction.md) ou no [bases de dados SQL](../articles/sql-database/sql-database-faq.md), ativar a definição do nível de segurança pretendido. Além disso, o Azure proporciona uma forma para monitorizar e auditar todo o acesso aos seus dados para o alertar de nenhuma intrusão ou de acesso não autorizado.

## <a name="conclusion"></a>Conclusão

A Internet das coisas começa com suas coisas — as coisas mais importantes para as empresas. IoT pode fornecer valor incrível para uma empresa, reduzindo os custos, aumentar a receita e empresas a transformar. O sucesso dessa transformação depende em grande parte escolher o fornecedor de software e serviço de IoT correto. Isso significa que encontrar um fornecedor que não apenas catalyzes essa transformação Noções básicas sobre necessidades de negócio e requisitos, mas também fornece serviços e software criadas com segurança, privacidade, transparência e conformidade como as considerações de design principais. A Microsoft ter ampla experiência com o desenvolvimento e a implementação de software seguro e os serviços e continua a ser um líder nesta nova era da Internet das coisas.

Os Aceleradores de solução crie em medidas de segurança por design, ativar a monitorização segura de ativos para melhorar a eficiência, desempenho operacional de unidade para ativar a inovação e empregar a análise avançada de dados para transformar as empresas. Com sua abordagem em camadas para segurança, os vários recursos de segurança, padrões de design, os solution accelerators ajudar a implementar uma infraestrutura que pode ser confiável para transformar qualquer empresa.

## <a name="additional-information"></a>Informações adicionais

Cada acelerador de solução cria instâncias dos serviços do Azure, tais como:

* [**O Hub IoT do Azure**](https://azure.microsoft.com/services/iot-hub/): O gateway que liga-se a cloud aos dispositivos. Pode dimensionar para milhões de conexões por hub e o processo de grandes volumes de dados com suporte de autenticação por dispositivo ajudá-lo a proteger a sua solução.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): Um serviço de base de dados dimensionável e totalmente indexadas para dados semi-estruturados que gere os metadados para os dispositivos, aprovisionar, como atributos, configuração e propriedades de segurança. O Azure Cosmos DB oferece processamento de alto débito e de elevado desempenho, a indexação independente de esquema de dados e uma interface de consulta SQL avançada.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Fluxo em tempo real de processamento na cloud que permite-lhe desenvolver e implementar uma solução de análise de baixo custo para revelar informações em tempo real de dispositivos, sensores, infraestrutura e aplicações rapidamente. Os dados a partir deste serviço totalmente gerido podem ser dimensionado para qualquer volume mantendo ainda um débito elevado, baixa latência e resiliência.

* [**Serviços aplicacionais do Azure**](https://azure.microsoft.com/services/app-service/): Uma plataforma de cloud para criar poderosas aplicações web e móveis que se ligam a dados em qualquer lugar; na cloud ou no local. Crie aplicações móveis cativantes para iOS, Android e Windows. Integre com o seu Software como serviço (SaaS) e aplicações empresariais a conectividade de out-of-the-box com dezenas de serviços baseados na nuvem e aplicações empresariais. O código na sua linguagem preferida e IDE — .NET, node. js, PHP, Python ou Java – para criar aplicações web e APIs mais rápido do que nunca.

* [**Aplicações lógicas**](https://azure.microsoft.com/services/app-service/logic/): A funcionalidade de aplicações lógicas do serviço de aplicações do Azure ajuda a integrar a sua solução de IoT para seus sistemas de linha de negócio existentes e automatizar processos de fluxo de trabalho. O Logic Apps permite aos programadores conceber fluxos de trabalho que começam com um acionador e, em seguida, executam uma série de passos, regras e ações que utilizam conectores poderosas para integrar com seus processos de negócios. O Logic Apps oferece a conectividade de out-of-the-box para um vasto ecossistema de SaaS, com base na cloud e aplicações no local.

* [**Armazenamento de Blobs do Azure**](https://azure.microsoft.com/services/storage/): Armazenamento de cloud fiável e económico para dados que os seus dispositivos enviam para a cloud.