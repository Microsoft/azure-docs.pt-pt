---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4f7ec9bbf7877d8067b3d8b1566a5cd2c2075281
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050478"
---
A Internet das Coisas (IoT) coloca desafios únicos de segurança, privacidade e conformidade para as empresas de todo o mundo. Ao contrário da tecnologia cibernética tradicional, onde estas questões giram em torno do software e da forma como é implementada, a IoT diz respeito ao que acontece quando os mundos cibernéticos e físicos convergem. Proteger as soluções IoT requer garantir o fornecimento seguro de dispositivos, garantir a conectividade segura entre estes dispositivos e a nuvem, e proteger a proteção de dados na nuvem durante o processamento e armazenamento. Trabalhando contra esta funcionalidade, no entanto, são dispositivos com recursos limitados, distribuição geográfica de implementações, e um grande número de dispositivos dentro de uma solução.

Este artigo explora como os aceleradores de solução IoT fornecem uma solução segura e privada de internet das coisas em nuvem. Os aceleradores de solução oferecem uma solução completa de ponta a ponta, com segurança incorporada em todas as fases desde o zero. Na Microsoft, o desenvolvimento de software seguro faz parte da prática de engenharia de software, enraizada na longa experiência da Microsoft em desenvolver software seguro. Para garantir isso, o Ciclo de Vida para Desenvolvimento de Segurança (SDL) é a metodologia de desenvolvimento fundamental, juntamente com uma série de serviços de segurança ao nível da infraestrutura, tais como a Operational Security Assurance (OSA) e a Unidade de Crimes Digitais da Microsoft, o Microsoft Security Response Center e o Microsoft Malware Protection Center.

Os aceleradores de solução oferecem características únicas que tornam o provisionamento, a ligação e armazenamento de dados de dispositivos IoT fáceis e transparentes e, acima de tudo, seguros. Este artigo examina a solução Azure IoT acelera funcionalidades de segurança e estratégias de implementação para garantir que os desafios de segurança, privacidade e conformidade são abordados.

## <a name="introduction"></a>Introdução

A Internet das Coisas (IoT) é a onda do futuro, oferecendo às empresas oportunidades imediatas e reais para reduzir custos, aumentar as receitas e transformar o seu negócio. Muitas empresas, no entanto, hesitam em implementar ioT nas suas organizações devido a preocupações sobre segurança, privacidade e conformidade. Um dos principais pontos de preocupação vem da singularidade da infraestrutura IoT, que une os mundos cibernético e físico, agravando os riscos individuais inerentes a estes dois mundos. A segurança da IoT diz respeito a garantir a integridade do código em execução nos dispositivos, fornecendo dispositivos e autenticação do utilizador, definindo uma clara propriedade dos dispositivos (bem como dados gerados por esses dispositivos) e sendo resiliente a ciberataques e ataques físicos.

Depois, há a questão da privacidade. As empresas querem transparência na recolha de dados, como no que está a ser recolhido e porquê, quem pode vê-lo, quem controla o acesso, e assim por diante. Por último, existem questões gerais de segurança do equipamento, juntamente com as pessoas que os operam, e questões de manutenção dos padrões de conformidade da indústria.

Dadas as preocupações de segurança, privacidade, transparência e conformidade, escolher o fornecedor de solução IoT certo continua a ser um desafio. A costura de peças individuais de software e serviços IoT fornecidos por uma variedade de fornecedores introduz lacunas na segurança, privacidade, transparência e conformidade, que podem ser difíceis de detetar, quanto mais corrigir. A escolha do software e prestador de serviços IoT certos baseia-se em encontrar fornecedores com vasta experiência em serviços de execução, que se estendem por verticais e geografias, mas também são capazes de escalar de forma segura e transparente. Da mesma forma, ajuda o fornecedor selecionado a ter décadas de experiência com o desenvolvimento de software seguro em milhares de milhões de máquinas em todo o mundo, e tem a capacidade de apreciar o cenário de ameaça colocado por este novo mundo da Internet das Coisas.

## <a name="secure-infrastructure-from-the-ground-up"></a>Infraestrutura segura do zero

A infraestrutura [microsoft Cloud](https://azure.microsoft.com) suporta mais de mil milhões de clientes em 127 países/regiões. Baseando-se na experiência de décadas da Microsoft na construção de software empresarial e na execução de alguns dos maiores serviços online do mundo, o Microsoft Cloud fornece níveis mais elevados de práticas de segurança, privacidade, conformidade e mitigação de ameaças mais elevadas do que a maioria dos clientes poderia alcançar por si próprio.

O [Ciclo de Vida para o Desenvolvimento de Segurança (SDL)](https://www.microsoft.com/sdl/) fornece um processo de desenvolvimento obrigatório em toda a empresa que incorpora requisitos de segurança em todo o ciclo de vida do software. Para ajudar a garantir que as atividades operacionais seguem o mesmo nível de práticas de segurança, a SDL utiliza rigorosas diretrizes de segurança estabelecidas no processo de Garantia de Segurança Operacional (OSA) da Microsoft. A Microsoft também trabalha com empresas de auditoria de terceiros para verificação contínua de que cumpre as suas obrigações de conformidade, e a Microsoft envolve esforços de segurança amplos através da criação de centros de excelência, incluindo a Unidade de Crimes Digitais da Microsoft, o Microsoft Security Response Center e o Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure - infraestrutura IoT segura para o seu negócio

O Microsoft Azure oferece uma solução em nuvem completa, que combina uma coleção em constante crescimento de serviços integrados na nuvem - análise, machine learning, armazenamento, segurança, networking e web - com um compromisso líder na indústria para a proteção e privacidade dos seus dados. A estratégia de [violação](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) da Microsoft usa uma *equipa vermelha* dedicada de especialistas em segurança de software que simulam ataques, testam a capacidade do Azure de detetar, proteger contra ameaças emergentes e recuperar de violações. A equipa global de [resposta a incidentes](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) da Microsoft trabalha 24 horas por dia para mitigar os efeitos de ataques e atividades maliciosas. A equipa segue os procedimentos estabelecidos para a gestão, comunicação e recuperação de incidentes, e utiliza interfaces detetáveis e previsíveis com parceiros internos e externos.

Os sistemas da Microsoft fornecem deteção e prevenção contínua de intrusões, prevenção de ataques de serviço, testes regulares de penetração e ferramentas forenses que ajudam a identificar e mitigar ameaças. [A autenticação multi-factor](../articles/active-directory/authentication/multi-factor-authentication.md) fornece uma camada extra de segurança para os utilizadores finais acederem à rede. E para a aplicação e o fornecedor anfitrião, a Microsoft oferece controlo de acesso, monitorização, anti-malware, digitalização de vulnerabilidades, patches e gestão de configuração.

Os aceleradores de solução aproveitam a segurança e privacidade incorporadas na plataforma Azure juntamente com os processos SDL e OSA para um desenvolvimento e funcionamento seguros de todo o software da Microsoft. Estes procedimentos proporcionam proteção de infraestruturas, proteção das redes e identidade e gestão que são fundamentais para a segurança de qualquer solução.

O [Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) dentro dos aceleradores de [solução IoT](../articles/iot-fundamentals/iot-introduction.md) oferece um serviço totalmente gerido que permite uma comunicação biderecional fiável e segura entre dispositivos IoT e serviços Azure [como Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) e [Azure Stream Analytics,](../articles/stream-analytics/stream-analytics-introduction.md) utilizando credenciais de segurança por dispositivo e controlo de acesso.

Para melhor comunicar as funcionalidades de segurança e privacidade incorporadas nos aceleradores de solução Azure IoT, este artigo divide a suite nas três áreas de segurança primárias.

![Aceleradores de soluções do Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Provisão e autenticação de dispositivos seguros

Os aceleradores de solução protegem os dispositivos enquanto estão no terreno, fornecendo uma chave de identidade única para cada dispositivo, que pode ser usada pela infraestrutura IoT para comunicar com o dispositivo enquanto este está em funcionamento. O processo é rápido e fácil de configurar. A chave gerada com um ID de dispositivo selecionado pelo utilizador forma a base de um símbolo utilizado em toda a comunicação entre o dispositivo e o Azure IoT Hub.

Os IDs do dispositivo podem ser associados a um dispositivo durante o fabrico (isto é, intermitentes num módulo de hardware) ou podem utilizar uma identidade fixa existente como procuração (por exemplo, números de série do CPU). Uma vez que a alteração desta informação de identificação no dispositivo não é simples, é importante introduzir iDs de dispositivos lógicos no caso de o hardware do dispositivo subjacente mudar, mas o dispositivo lógico permanece o mesmo. Em alguns casos, a associação de uma identidade do dispositivo pode acontecer no tempo de implementação do dispositivo (por exemplo, um engenheiro de campo autenticado configura fisicamente um novo dispositivo enquanto comunica com o backend da solução). O [registo de identidade Azure IoT Hub](../articles/iot-hub/iot-hub-devguide.md) fornece armazenamento seguro de identidades do dispositivo e chaves de segurança para uma solução. Indivíduos ou grupos de identidades do dispositivo podem ser adicionados a uma lista de autorizações, ou a uma lista de blocos, permitindo o controlo total sobre o acesso ao dispositivo.

As políticas de controlo de acesso Azure IoT Hub na nuvem permitem ativar e desativar qualquer identidade do dispositivo, fornecendo uma forma de desassociar um dispositivo de uma implementação IoT quando necessário. Esta associação e desassociação de dispositivos baseia-se na identidade de cada dispositivo.

As funcionalidades adicionais de segurança do dispositivo incluem:

* Os dispositivos não aceitam ligações de rede não solicitadas. Estabelecem todas as ligações e rotas de forma única. Para que um dispositivo receba um comando a partir do backend, o dispositivo deve iniciar uma ligação para verificar se eventuais comandos pendentes devem ser processados. Uma vez estabelecida uma ligação entre o dispositivo e o IoT Hub, as mensagens da nuvem para o dispositivo e dispositivo para a nuvem podem ser enviadas de forma transparente.

* Os dispositivos apenas se ligam ou estabelecem rotas para serviços bem conhecidos com os quais são espreitados, como um Hub Azure IoT.

* Autorização de nível do sistema e utilização de autenticação por dispositivo, tornando as credenciais de acesso e permissões quase instantaneamente revogáveis.

### <a name="secure-connectivity"></a>Conectividade segura

A durabilidade das mensagens é uma característica importante de qualquer solução IoT. A necessidade de fornecer duramente comandos e/ou receber dados de dispositivos é sublinhada pelo facto de os dispositivos IoT estarem ligados através da Internet, ou outras redes similares que podem não ser fiáveis. O Azure IoT Hub oferece durabilidade de mensagens entre nuvem e dispositivos através de um sistema de reconhecimentos em resposta a mensagens. A durabilidade adicional das mensagens é conseguida através do caching de mensagens no IoT Hub durante até sete dias para telemetria e dois dias para comandos.

A eficiência é importante para garantir a conservação dos recursos e o funcionamento num ambiente limitado a recursos. HTTPS (HTTP Secure), a versão segura padrão da indústria do popular protocolo http, é suportada pelo Azure IoT Hub, permitindo uma comunicação eficiente. O Protocolo avançado de Fila de Mensagens (AMQP) e o Transporte de Telemetria de Fila de Mensagens (MQTT), suportados pelo Azure IoT Hub, são projetados não só para eficiência em termos de utilização de recursos, mas também para a entrega de mensagens fiáveis. 

A escalabilidade requer a capacidade de interoperar de forma segura com uma ampla gama de dispositivos. O hub Azure IoT permite a ligação segura a dispositivos ip-activados e não ativados por IP. Os dispositivos ativados por IP são capazes de ligar e comunicar diretamente com o Hub IoT sobre uma ligação segura. Os dispositivos não habilitados para o IP são limitados a recursos e conectam-se apenas através de protocolos de comunicação de curta distância, tais como Zwave, ZigBee e Bluetooth. Um gateway de campo é usado para agregar estes dispositivos e executar a tradução de protocolo para permitir uma comunicação bidirecional segura com a nuvem.

As funcionalidades adicionais de segurança da ligação incluem:

* A trajetória de comunicação entre dispositivos e Azure IoT Hub, ou entre gateways e Azure IoT Hub, é assegurada usando a segurança da camada de transporte padrão da indústria (TLS) com Azure IoT Hub autenticado usando o protocolo X.509.

* Para proteger os dispositivos de ligações de entrada não solicitadas, o Azure IoT Hub não abre qualquer ligação ao dispositivo. O aparelho inicia todas as ligações.

* O Azure IoT Hub armazena duramente mensagens para dispositivos e aguarda a ligação do dispositivo. Estes comandos são armazenados durante dois dias, permitindo que os dispositivos que se conectem esporadicamente, devido a problemas de energia ou conectividade, recebam estes comandos. O Azure IoT Hub mantém uma fila por dispositivo para cada dispositivo.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Processamento e armazenamento seguro na nuvem

Desde comunicações encriptadas até dados de processamento na nuvem, os aceleradores de solução ajudam a manter os dados seguros. Proporciona flexibilidade para implementar encriptação adicional e gestão de chaves de segurança.

Utilizando o Azure Ative Directory (AAD) para autenticação e autorização do utilizador, os aceleradores de soluções Azure IoT podem fornecer um modelo de autorização baseado em políticas para dados na nuvem, permitindo uma gestão fácil de acesso que possa ser auditada e revista. Este modelo também permite a revogação quase instantânea do acesso aos dados na nuvem, e de dispositivos ligados aos aceleradores de solução Azure IoT.

Uma vez que os dados estão na nuvem, pode ser processado e armazenado em qualquer fluxo de trabalho definido pelo utilizador. O acesso a cada parte dos dados é controlado com o Azure Ative Directory, dependendo do serviço de armazenamento utilizado.

Todas as teclas utilizadas pela infraestrutura IoT são armazenadas na nuvem em armazenamento seguro, com a capacidade de rolar para o caso de as chaves precisarem de ser reprovisionadas. Os dados podem ser armazenados em [Azure Cosmos DB](../articles/cosmos-db/introduction.md) ou na [BASE de Dados SQL,](../articles/sql-database/sql-database-faq.md)permitindo a definição do nível de segurança desejado. Além disso, o Azure fornece uma forma de monitorizar e auditar todo o acesso aos seus dados para alertá-lo de qualquer intrusão ou acesso não autorizado.

## <a name="conclusion"></a>Conclusão

A Internet das Coisas começa com as suas coisas - as coisas que mais importam para as empresas. A IoT pode entregar um valor incrível a um negócio, reduzindo custos, aumentando as receitas e transformando o negócio. O sucesso desta transformação depende em grande parte da escolha do software e fornecedor de serviços IoT certos. Isto significa encontrar um fornecedor que não só catalise esta transformação compreendendo as necessidades e requisitos das empresas, mas também fornece serviços e software construído com segurança, privacidade, transparência e conformidade como considerações de design principais. A Microsoft tem uma vasta experiência no desenvolvimento e implementação de software e serviços seguros e continua a ser um líder nesta nova era da Internet das Coisas.

Os aceleradores de solução constroem-se em medidas de segurança através do design, permitindo uma monitorização segura dos ativos para melhorar a eficiência, impulsionar o desempenho operacional para permitir a inovação e empregar análises avançadas de dados para transformar as empresas. Com a sua abordagem em camadas em direção à segurança, múltiplas funcionalidades de segurança e padrões de design, os aceleradores de solução ajudam a implementar uma infraestrutura que pode ser confiável para transformar qualquer negócio.

## <a name="additional-information"></a>Informações adicionais

Cada acelerador de soluções cria casos de serviços Azure, tais como:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): O seu portal que liga a nuvem aos dispositivos. Pode escalar para milhões de ligações por hub e processar volumes maciços de dados com suporte de autenticação por dispositivo ajudando-o a proteger a sua solução.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): Um serviço de base de dados escalável e totalmente indexado para dados semi-estruturados que gere metadados para os dispositivos que fornece, tais como atributos, configuração e propriedades de segurança. A Azure Cosmos DB oferece processamento de alto desempenho e alta produção, indexação de dados schema-agnóstico e uma interface de consulta SQL rica.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Processamento de fluxo em tempo real na nuvem que lhe permite desenvolver e implementar rapidamente uma solução de análise de baixo custo para descobrir insights em tempo real de dispositivos, sensores, infraestruturas e aplicações. Os dados deste serviço totalmente gerido podem escalar para qualquer volume, enquanto ainda alcançam alta produção, baixa latência e resiliência.

* [**Azure App Services**](https://azure.microsoft.com/services/app-service/): Uma plataforma em nuvem para construir poderosas aplicações web e móveis que se conectam a dados em qualquer lugar; na nuvem ou no local. Crie aplicativos móveis envolventes para iOS, Android e Windows. Integre-se com o seu Software como um Serviço (SaaS) e aplicações empresariais com conectividade fora da caixa a dezenas de serviços baseados na nuvem e aplicações empresariais. Código na sua língua favorita e IDE -NET, Node.js, PHP, Python ou Java - para construir aplicações web e APIs mais rapidamente do que nunca.

* [**Aplicações lógicas**](https://azure.microsoft.com/services/app-service/logic/): A funcionalidade de Aplicações Lógicas do Azure App Service ajuda a integrar a sua solução IoT nos seus sistemas de linha de negócio existentes e automatiza processos de fluxo de trabalho. As Aplicações Lógicas permitem aos desenvolvedores conceber fluxos de trabalho que começam a partir de um gatilho e depois executam uma série de passos - regras e ações que usam conectores poderosos para se integrarem com os seus processos de negócio. A Logic Apps oferece conectividade fora da caixa a um vasto ecossistema de aplicações SaaS, baseadas em nuvem e no local.

* [**Armazenamento Azure Blob**](https://azure.microsoft.com/services/storage/): Armazenamento em nuvem fiável e económico para os dados que os seus dispositivos enviam para a nuvem.