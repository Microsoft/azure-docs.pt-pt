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
ms.openlocfilehash: 128a2e8b200f1323b88aad635f27c1b686ecbed2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72789722"
---
A Internet das Coisas (IoT) coloca desafios únicos de segurança, privacidade e conformidade às empresas em todo o mundo. Ao contrário da tecnologia cibernética tradicional, onde estas questões giram em torno do software e como é implementado, a IoT diz respeito ao que acontece quando o cibere e os mundos físicos convergem. Proteger as soluções IoT requer garantir o fornecimento seguro de dispositivos, uma conectividade segura entre estes dispositivos e a nuvem, e proteger os dados seguros na nuvem durante o processamento e armazenamento. No entanto, a trabalhar contra esta funcionalidade estão dispositivos limitados por recursos, distribuição geográfica de implementações e um grande número de dispositivos dentro de uma solução.

Este artigo explora como os aceleradores de soluções IoT fornecem uma solução segura e privada de nuvem de Internet das Coisas. Os aceleradores de solução oferecem uma solução completa de ponta a ponta, com segurança incorporada em todas as fases desde o zero. Na Microsoft, o desenvolvimento de software seguro faz parte da prática de engenharia de software, enraizada na experiência de décadas da Microsoft no desenvolvimento de software seguro. Para garantir isto, o Ciclo de Vida de Desenvolvimento de Segurança (SDL) é a metodologia de desenvolvimento fundamental, juntamente com uma série de serviços de segurança ao nível da infraestrutura, como a Operational Security Assurance (OSA) e a Microsoft Digital Crimes Unit, Microsoft Centro de Resposta de Segurança e Microsoft Malware Protection Center.

Os aceleradores de solução oferecem características únicas que tornam o fornecimento, a ligação e armazenamento de dados de dispositivos IoT fáceis e transparentes e, acima de tudo, seguros. Este artigo examina as funcionalidades de segurança dos aceleradores de soluções Azure IoT e estratégias de implementação para garantir que os desafios de segurança, privacidade e conformidade sejam abordados.

## <a name="introduction"></a>Introdução

A Internet das Coisas (IoT) é a onda do futuro, oferecendo às empresas oportunidades imediatas e reais para reduzir custos, aumentar as receitas e transformar o seu negócio. Muitas empresas, no entanto, hesitam em implementar IoT nas suas organizações devido a preocupações com segurança, privacidade e conformidade. Um dos principais pontos de preocupação provém da singularidade da infraestrutura IoT, que une os mundos cibernético e físico, agravando os riscos individuais inerentes a estes dois mundos. A segurança do IoT diz respeito a garantir a integridade do código em execução nos dispositivos, fornecendo dispositivo e autenticação do utilizador, definindo a propriedade clara dos dispositivos (bem como os dados gerados por esses dispositivos), e sendo resiliente a ciberataques e ataques físicos.

Depois, há a questão da privacidade. As empresas querem transparência na recolha de dados, como no que está a ser recolhido e porquê, quem pode vê-lo, quem controla o acesso, e assim por diante. Por último, existem questões gerais de segurança do equipamento, juntamente com as pessoas que os operam, e questões de manutenção dos padrões de conformidade do sector.

Dadas as preocupações de segurança, privacidade, transparência e conformidade, a escolha do fornecedor de soluções IoT certo continua a ser um desafio. Coser peças individuais de software e serviços IoT fornecidos por uma variedade de fornecedores introduz lacunas na segurança, privacidade, transparência e conformidade, que podem ser difíceis de detetar, quanto mais corrigir. A escolha do software e prestador de serviços IoT certo baseia-se na procura de fornecedores que possuam serviços de execução de vasta experiência, que se estendem por verticais e geografias, mas também são capazes de escalar de forma segura e transparente. Da mesma forma, ajuda o fornecedor selecionado a ter décadas de experiência no desenvolvimento de software seguro que funciona em biliões de máquinas em todo o mundo, e tem a capacidade de apreciar o cenário de ameaça colocado por este novo mundo da Internet das Coisas.

## <a name="secure-infrastructure-from-the-ground-up"></a>Infraestrutura segura do zero

A infraestrutura [microsoft Cloud](https://azure.microsoft.com) suporta mais de mil milhões de clientes em 127 países/regiões. Baseando-se na experiência de décadas da Microsoft na construção de software empresarial e na gestão de alguns dos maiores serviços online do mundo, o Microsoft Cloud fornece níveis mais elevados de práticas de segurança, privacidade, conformidade e mitigação de ameaças. do que a maioria dos clientes poderia alcançar por si só.

O Ciclo de Vida de Desenvolvimento de [Segurança (SDL)](https://www.microsoft.com/sdl/) fornece um processo obrigatório de desenvolvimento em toda a empresa que incorpora requisitos de segurança em todo o ciclo de vida do software. Para ajudar a garantir que as atividades operacionais seguem o mesmo nível de práticas de segurança, a SDL utiliza diretrizes de segurança rigorosas estabelecidas no processo de Segurança Operacional (OSA) da Microsoft. A Microsoft também trabalha com empresas de auditoria de terceiros para verificação contínua de que cumpre as suas obrigações de conformidade, e a Microsoft dedica-se a amplos esforços de segurança através da criação de centros de excelência, incluindo a Unidade de Crimes Digitais da Microsoft, Microsoft Security Response Center e Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure - infraestrutura ioT segura para o seu negócio

O Microsoft Azure oferece uma solução cloud completa, que combina uma coleção em constante crescimento de serviços integrados em nuvem - analytics, machine learning, armazenamento, segurança, networking e web - com um compromisso líder da indústria para a proteção e privacidade dos seus dados. A estratégia de [violação](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) da Microsoft usa uma *equipa vermelha* dedicada de especialistas em segurança de software que simulam ataques, testam a capacidade do Azure de detetar, proteger contra ameaças emergentes e recuperar de violações. A equipa global de resposta a [incidentes](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) da Microsoft trabalha 24 horas por dia para mitigar os efeitos de ataques e atividades maliciosas. A equipa segue os procedimentos estabelecidos para a gestão de incidentes, comunicação e recuperação, e utiliza interfaces descobriveis e previsíveis com parceiros internos e externos.

Os sistemas da Microsoft fornecem deteção e prevenção contínua de intrusões, prevenção de ataques de serviço, testes regulares de penetração e ferramentas forenses que ajudam a identificar e mitigar ameaças. [A autenticação de vários fatores](../articles/active-directory/authentication/multi-factor-authentication.md) proporciona uma camada extra de segurança para os utilizadores finais acederem à rede. E para a aplicação e para o fornecedor anfitrião, a Microsoft oferece controlo de acesso, monitorização, anti-malware, verificação de vulnerabilidades, patches e gestão de configuração.

Os aceleradores de solução aproveitam a segurança e privacidade incorporadas na plataforma Azure juntamente com os processos SDL e OSA para desenvolvimento e operação seguros de todo o software da Microsoft. Estes procedimentos proporcionam proteção de infraestruturas, proteção de redes e identidade e gestão fundamentais para a segurança de qualquer solução.

O [Hub Azure IoT](../articles/iot-hub/about-iot-hub.md) dentro dos aceleradores de [solução IoT](../articles/iot-fundamentals/iot-introduction.md) oferece um serviço totalmente gerido que permite uma comunicação bidirecional fiável e segura entre dispositivos IoT e serviços Azure, como [O Machine Learning Azure](../articles/machine-learning/studio/what-is-machine-learning.md) e [Azure Stream Analytics,](../articles/stream-analytics/stream-analytics-introduction.md) utilizando credenciais de segurança por dispositivo e controlo de acesso.

Para melhor comunicar as funcionalidades de segurança e privacidade incorporadas nos aceleradores de soluções Azure IoT, este artigo decompõe a suite nas três áreas de segurança primárias.

![Aceleradores de soluções do Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Fornecimento e autenticação de dispositivos seguros

Os aceleradores de solução protegem os dispositivos enquanto estão no campo, fornecendo uma chave de identidade única para cada dispositivo, que pode ser utilizado pela infraestrutura IoT para comunicar com o dispositivo enquanto este estiver em funcionamento. O processo é rápido e fácil de configurar. A chave gerada com um ID de dispositivo selecionado pelo utilizador forma a base de um símbolo utilizado em toda a comunicação entre o dispositivo e o Hub Azure IoT.

Os IDs do dispositivo podem ser associados a um dispositivo durante o fabrico (isto é, flashed em um módulo de confiança de hardware) ou podem usar uma identidade fixa existente como procuração (por exemplo, números de série CPU). Uma vez que alterar esta informação de identificação no dispositivo não é simples, é importante introduzir iDs de dispositivológico sintetizados no caso de o hardware do dispositivo subjacente mudar, mas o dispositivo lógico permanece o mesmo. Em alguns casos, a associação de uma identidade de dispositivo pode acontecer no momento de implantação do dispositivo (por exemplo, um engenheiro de campo autenticado configura fisicamente um novo dispositivo enquanto comunica com o backend da solução). O [registo de identidade Do Hub Azure IoT](../articles/iot-hub/iot-hub-devguide.md) fornece armazenamento seguro de identidades do dispositivo e chaves de segurança para uma solução. As identidades individuais ou grupos de dispositivos podem ser adicionadas a uma lista de licenciamento, ou a uma lista de blocos, permitindo o controlo total do acesso ao dispositivo.

As políticas de controlo de acesso do Azure IoT Hub na nuvem permitem ativar e desativar qualquer identidade do dispositivo, fornecendo uma forma de dissociar um dispositivo a partir de uma implementação ioT quando necessário. Esta associação e desassociação de dispositivos baseia-se na identidade de cada dispositivo.

As funcionalidades adicionais de segurança do dispositivo incluem:

* Os dispositivos não aceitam ligações de rede não solicitadas. Estabelecem todas as ligações e rotas de forma apenas de saída. Para que um dispositivo receba um comando a partir do backend, o dispositivo deve iniciar uma ligação para verificar se os comandos pendentes devem ser processados. Uma vez estabelecida uma ligação entre o dispositivo e o IoT Hub, as mensagens da nuvem para o dispositivo e o dispositivo para a nuvem podem ser enviadas de forma transparente.

* Os dispositivos apenas se ligam ou estabelecem rotas para serviços bem conhecidos com os quais são espreitados, como um Hub Azure IoT.

* Autorização de nível de sistema e uso de autenticação por identidade por dispositivo, tornando credenciais de acesso e permissões quase instantaneamente revogáveis.

### <a name="secure-connectivity"></a>Conectividade segura

A durabilidade das mensagens é uma característica importante de qualquer solução IoT. A necessidade de fornecer duramente comandos e/ou receber dados de dispositivos é sublinhada pelo facto de os dispositivos IoT estarem ligados através da Internet, ou outras redes semelhantes que podem não ser fiáveis. O Azure IoT Hub oferece durabilidade de mensagens entre a nuvem e os dispositivos através de um sistema de reconhecimento em resposta a mensagens. A durabilidade adicional para as mensagens é conseguida através de mensagens de cache no Hub IoT por até sete dias para telemetria e dois dias para comandos.

A eficiência é importante para garantir a conservação dos recursos e o funcionamento num ambiente restrito aos recursos. HTTPS (HTTP Secure), a versão segura padrão da indústria do popular protocolo http, é suportada pelo Azure IoT Hub, permitindo uma comunicação eficiente. O Protocolo avançado de Fila de Mensagens (AMQP) e o Transporte de Telemetria de Telemetria de Fila de Mensagens (MQTT), apoiado saqueado pelo Azure IoT Hub, são projetados não só para eficiência em termos de utilização de recursos, mas também para uma entrega fiável de mensagens. 

A escalabilidade requer a capacidade de interoperar de forma segura com uma vasta gama de dispositivos. O hub Azure IoT permite uma ligação segura a dispositivos ip-enabled e não ip. Os dispositivos ip-enabled são capazes de ligar e comunicar diretamente com o Hub IoT sobre uma ligação segura. Os dispositivos não ativados pelo IP são limitados por recursos e conectam-se apenas através de protocolos de comunicação de curta distância, tais como Zwave, ZigBee e Bluetooth. Um gateway de campo é usado para agregar estes dispositivos e executa a tradução protocolar para permitir uma comunicação bidirecional segura com a nuvem.

As funcionalidades adicionais de segurança da ligação incluem:

* A via de comunicação entre dispositivos e Hub Azure IoT, ou entre gateways e Hub Azure IoT, é assegurada utilizando a segurança da camada de transporte (TLS) padrão da indústria com o Hub Azure IoT autenticado usando o protocolo X.509.

* Para proteger os dispositivos de ligações de entrada não solicitadas, o Azure IoT Hub não abre qualquer ligação ao dispositivo. O dispositivo inicia todas as ligações.

* O Azure IoT Hub guarda duramente mensagens para dispositivos e aguarda que o dispositivo se conecte. Estes comandos são armazenados durante dois dias, permitindo que os dispositivos que ligam esporadicamente, devido a problemas de potência ou conectividade, recebam estes comandos. O Azure IoT Hub mantém uma fila por dispositivo para cada dispositivo.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Processamento e armazenamento seguros na nuvem

Desde comunicações encriptadas até ao processamento de dados na nuvem, os aceleradores da solução ajudam a manter os dados seguros. Proporciona flexibilidade para implementar encriptação adicional e gestão de chaves de segurança.

Utilizando o Azure Ative Directory (AAD) para autenticação e autorização do utilizador, os aceleradores de soluções Azure IoT podem fornecer um modelo de autorização baseado em políticas para dados na nuvem, permitindo uma gestão de fácil acesso que pode ser auditada e revista. Este modelo também permite a revogação quase instantânea do acesso aos dados na nuvem, e de dispositivos ligados aos aceleradores de solução Azure IoT.

Uma vez que os dados estejam na nuvem, pode ser processado e armazenado em qualquer fluxo de trabalho definido pelo utilizador. O acesso a cada parte dos dados é controlado com o Azure Ative Directory, dependendo do serviço de armazenamento utilizado.

Todas as chaves utilizadas pela infraestrutura IoT são armazenadas na nuvem em armazenamento seguro, com a capacidade de capotar no caso de as chaves precisarem de ser reprovisionadas. Os dados podem ser armazenados em [Azure Cosmos DB](../articles/cosmos-db/introduction.md) ou em bases de [dados SQL,](../articles/sql-database/sql-database-faq.md)permitindo a definição do nível de segurança desejado. Além disso, o Azure fornece uma forma de monitorizar e auditar todos os acessos aos seus dados para alertá-lo de qualquer intrusão ou acesso não autorizado.

## <a name="conclusion"></a>Conclusão

A Internet das Coisas começa com as suas coisas - as coisas que mais importam para as empresas. A IoT pode oferecer um valor incrível a um negócio, reduzindo custos, aumentando as receitas e transformando negócios. O sucesso desta transformação depende em grande parte da escolha do software e prestador de serviços IoT certos. Isto significa encontrar um fornecedor que não só catalisa esta transformação através da compreensão das necessidades e requisitos das empresas, mas também fornece serviços e software construído com segurança, privacidade, transparência e conformidade como grandes considerações de design. A Microsoft tem uma vasta experiência em desenvolver e implementar software e serviços seguros e continua a ser um líder nesta nova era da Internet das Coisas.

Os aceleradores de solução constroem medidas de segurança através do design, permitindo uma monitorização segura dos ativos para melhorar a eficiência, impulsionar o desempenho operacional para permitir a inovação e empregar análises avançadas de dados para transformar empresas. Com a sua abordagem em camadas em direção à segurança, múltiplas funcionalidades de segurança e padrões de design, os aceleradores de solução ajudam a implementar uma infraestrutura que é de confiança para transformar qualquer negócio.

## <a name="additional-information"></a>Informações adicionais

Cada acelerador de soluções cria casos de serviços Azure, tais como:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): O seu portal que liga a nuvem aos dispositivos. Pode escalar milhões de ligações por hub e processar volumes maciços de dados com suporte de autenticação por dispositivo ajudando-o a proteger a sua solução.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): Um serviço de base de dados escalável e totalmente indexado para dados semi-estruturados que gere metadados para os dispositivos que disponibiliza, tais como atributos, configuração e propriedades de segurança. A Azure Cosmos DB oferece processamento de alto desempenho e alta perputa, indexação schema-agnóstica de dados e uma rica interface de consulta SQL.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Processamento de fluxo em tempo real na nuvem que lhe permite desenvolver e implementar rapidamente uma solução de análise de baixo custo para descobrir insights em tempo real de dispositivos, sensores, infraestruturas e aplicações. Os dados deste serviço totalmente gerido podem escalar para qualquer volume, enquanto ainda alcançam alta sucusão, baixa latência e resiliência.

* [**Azure App Services**](https://azure.microsoft.com/services/app-service/): Uma plataforma cloud para construir aplicações web e móveis poderosas que se conectam a dados em qualquer lugar; na nuvem ou no local. Construa aplicações móveis envolventes para iOS, Android e Windows. Integre com o seu Software como Serviço (SaaS) e aplicações empresariais com conectividade fora da caixa a dezenas de serviços baseados na nuvem e aplicações empresariais. Código na sua língua favorita e IDE -.NET, Node.js, PHP, Python ou Java - para construir aplicações web e APIs mais rápido do que nunca.

* [**Aplicações Lógicas**](https://azure.microsoft.com/services/app-service/logic/): A funcionalidade de Aplicações Lógicas do Serviço de Aplicações Azure ajuda a integrar a sua solução IoT aos seus sistemas de linha de negócio existentes e automatizaos processos de fluxo de trabalho. As Aplicações Lógicas permitem aos desenvolvedores conceber fluxos de trabalho que partam de um gatilho e depois executar uma série de etapas — regras e ações que utilizam conectores poderosos para se integrarem com os seus processos de negócio. As Aplicações Lógicas oferecem conectividade fora da caixa a um vasto ecossistema de aplicações SaaS, baseadas em nuvem e no local.

* [**Armazenamento Azure Blob**](https://azure.microsoft.com/services/storage/): Armazenamento fiável e económico em nuvem para os dados que os seus dispositivos enviam para a nuvem.