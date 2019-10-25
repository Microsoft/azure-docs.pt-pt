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
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789722"
---
O Internet das Coisas (IoT) traz desafios exclusivos de segurança, privacidade e conformidade para as empresas no mundo todo. Ao contrário da tecnologia Cyber tradicional onde esses problemas giram em relação ao software e como eles são implementados, a IoT se preocupa com o que acontece quando os mundos virtuais e da CyberSource convergem. A proteção das soluções de IoT exige a garantia de um provisionamento seguro de dispositivos, a conectividade segura entre esses dispositivos e a nuvem e a proteção segura de dados na nuvem durante o processamento e o armazenamento. No entanto, trabalhar com essas funcionalidades são dispositivos com restrição de recursos, distribuição geográfica de implantações e um grande número de dispositivos em uma solução.

Este artigo explora como os aceleradores de solução de IoT fornecem uma solução de nuvem Internet das Coisas segura e privada. Os aceleradores de solução fornecem uma solução completa de ponta a ponta, com segurança incorporada em todos os estágios desde o início. Na Microsoft, desenvolver software seguro faz parte da prática de engenharia de software, com a raiz de décadas de experiência longa de desenvolvimento de software seguro da Microsoft. Para garantir isso, o SDL (ciclo de vida de desenvolvimento da segurança) é a metodologia de desenvolvimento fundamental, aliada a um host de serviços de segurança de nível de infraestrutura, como o OSA (garantia de segurança operacional) e a unidade de crimes digitais da Microsoft, a Microsoft Security Response Center e centro de proteção contra malware da Microsoft.

Os aceleradores de solução oferecem recursos exclusivos que tornam o provisionamento, a conexão e o armazenamento de dados de dispositivos IoT fáceis e transparentes e, a maior parte, seguros. Este artigo examina os recursos de segurança dos aceleradores de soluções de IoT do Azure e estratégias de implantação para garantir que os desafios de segurança, privacidade e conformidade sejam resolvidos.

## <a name="introduction"></a>Introdução

A IoT (Internet das Coisas) é a onda do futuro, oferecendo às empresas oportunidades imediatas e reais para reduzir custos, aumentar a receita e transformar seus negócios. No entanto, muitas empresas são hesitar para implantar IoT em suas organizações devido a questões sobre segurança, privacidade e conformidade. Um grande ponto de preocupação vem da exclusividade da infraestrutura de IoT, que mescla os mundos virtuais e cibernéticos juntos, compondo riscos individuais inerentes a esses dois mundos. A segurança da IoT diz respeito à garantia da integridade do código em execução em dispositivos, fornecendo autenticação de dispositivo e de usuário, definindo a propriedade clara de dispositivos (bem como os dados gerados por esses dispositivos) e sendo resilientes a ataques físicos e virtuais.

Em seguida, há o problema de privacidade. As empresas desejam transparência sobre a coleta de dados, como no que está sendo coletado e por quê, quem pode vê-la, quem controla o acesso e assim por diante. Por fim, há problemas gerais de segurança do equipamento junto com as pessoas que os operam e problemas de manutenção dos padrões de conformidade do setor.

Devido a questões de segurança, privacidade, transparência e conformidade, escolher o provedor de solução de IoT correto continua sendo um desafio. Reunir partes individuais de software e serviços de IoT fornecidos por uma variedade de fornecedores apresenta lacunas de segurança, privacidade, transparência e conformidade, o que pode ser difícil de detectar, permitindo uma correção sozinha. A escolha do software e do provedor de serviços de IoT corretos baseia-se na localização de provedores com ampla experiência em execução de serviços, que se estendem por verticais e geográficas, mas também podem ser dimensionados de maneira segura e transparente. Da mesma forma, ele ajuda o provedor selecionado a ter décadas de experiência com o desenvolvimento de software seguro executado em bilhões de computadores em todo o mundo e tem a capacidade de apreciar o panorama de ameaças apresentado por esse novo mundo da Internet das Coisas.

## <a name="secure-infrastructure-from-the-ground-up"></a>Proteger a infraestrutura desde o início

A infraestrutura de [Microsoft Cloud](https://azure.microsoft.com) dá suporte a mais de 1.000.000.000 clientes em 127 países/regiões. Com base em décadas de experiência da Microsoft na criação de software corporativo e na execução de algumas das maiores serviços onlines do mundo, o Microsoft Cloud fornece níveis mais altos de segurança aprimorada, privacidade, conformidade e práticas de mitigação de ameaças do que a maioria dos clientes pode obter por conta própria.

O [SDL (ciclo de vida de desenvolvimento da segurança)](https://www.microsoft.com/sdl/) fornece um processo de desenvolvimento obrigatório em toda a empresa que incorpora os requisitos de segurança em todo o ciclo de vida do software. Para ajudar a garantir que as atividades operacionais sigam o mesmo nível de práticas de segurança, o SDL usa diretrizes de segurança rigorosas dispostas no processo OSA (garantia de segurança operacional) da Microsoft. A Microsoft também trabalha com empresas de auditoria de terceiros para verificação contínua de que atende a suas obrigações de conformidade, e a Microsoft se envolve em grandes esforços de segurança por meio da criação de centros de excelência, incluindo a unidade de crimes digitais da Microsoft, Microsoft Security Response Center e centro de proteção contra malware da Microsoft.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure-infraestrutura de IoT segura para sua empresa

A Microsoft Azure oferece uma solução de nuvem completa, uma que combina uma coleção em constante crescimento de serviços de nuvem integrados — análise, aprendizado de máquina, armazenamento, segurança, rede e Web, com um compromisso líder do setor com a proteção e privacidade dos seus dados. A estratégia [supor violação](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) da Microsoft usa uma *equipe vermelha* dedicada de especialistas em segurança de software que simulam ataques, testando a capacidade do Azure de detectar, proteger contra ameaças emergentes e se recuperar de violações. A equipe de [resposta a incidentes globais](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) da Microsoft trabalha em todo o relógio para reduzir os efeitos de ataques e atividades mal-intencionadas. A equipe segue os procedimentos estabelecidos para gerenciamento de incidentes, comunicação e recuperação e usa interfaces detectáveis e previsíveis com parceiros internos e externos.

Os sistemas da Microsoft fornecem detecção e prevenção contínua de intrusão, prevenção de ataques de serviço, teste de penetração regular e ferramentas forenses que ajudam a identificar e atenuar ameaças. A [autenticação multifator](../articles/active-directory/authentication/multi-factor-authentication.md) fornece uma camada extra de segurança para que os usuários finais acessem a rede. E para o aplicativo e o provedor de host, a Microsoft oferece controle de acesso, monitoramento, anti-malware, verificação de vulnerabilidade, patches e gerenciamento de configuração.

Os aceleradores de solução aproveitam a segurança e a privacidade incorporadas à plataforma Azure, juntamente com os processos de SDL e OSA para o desenvolvimento e a operação seguros de todos os softwares da Microsoft. Esses procedimentos fornecem proteção de infraestrutura, proteção de rede e recursos de identidade e gerenciamento fundamentais para a segurança de qualquer solução.

O [Hub IOT do Azure](../articles/iot-hub/about-iot-hub.md) dentro dos [aceleradores de solução de IOT](../articles/iot-fundamentals/iot-introduction.md) oferece um serviço totalmente gerenciado que permite a comunicação bidirecional confiável e segura entre os dispositivos IOT e os serviços do Azure, como [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) e [ Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) usando credenciais de segurança por dispositivo e controle de acesso.

Para comunicar melhor os recursos de segurança e privacidade incorporados aos aceleradores de solução do Azure IoT, este artigo divide o pacote nas três áreas de segurança principais.

![Aceleradores de soluções do Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Provisionamento e autenticação de dispositivos seguros

Os aceleradores de solução protegem os dispositivos enquanto eles estão fora do campo fornecendo uma chave de identidade exclusiva para cada dispositivo, que pode ser usada pela infraestrutura de IoT para se comunicar com o dispositivo enquanto ele está em operação. O processo é rápido e fácil de configurar. A chave gerada com uma ID de dispositivo selecionada pelo usuário forma a base de um token usado em toda a comunicação entre o dispositivo e o Hub IoT do Azure.

As IDs de dispositivo podem ser associadas a um dispositivo durante a fabricação (ou seja, atualizadas em um módulo de confiança de hardware) ou podem usar uma identidade fixa existente como um proxy (por exemplo, números de série de CPU). Como alterar essas informações de identificação no dispositivo não é simples, é importante introduzir IDs de dispositivo lógicos caso o hardware do dispositivo subjacente seja alterado, mas o dispositivo lógico permaneça o mesmo. Em alguns casos, a associação de uma identidade de dispositivo pode ocorrer no momento da implantação do dispositivo (por exemplo, um engenheiro de campo autenticado configura fisicamente um novo dispositivo durante a comunicação com o back-end da solução). O [registro de identidade do Hub IOT do Azure](../articles/iot-hub/iot-hub-devguide.md) fornece armazenamento seguro de identidades de dispositivo e chaves de segurança para uma solução. Um indivíduo ou grupos de identidades de dispositivo podem ser adicionados a uma lista de permissões ou a uma lista de bloqueios, permitindo o controle completo sobre o acesso ao dispositivo.

As políticas de controle de acesso do Hub IoT do Azure na nuvem habilitam a ativação e a desabilitação de qualquer identidade do dispositivo, fornecendo uma maneira de desassociar um dispositivo de uma implantação de IoT quando necessário. Essa associação e desassociação de dispositivos se baseiam em cada identidade de dispositivo.

Os recursos de segurança de dispositivo adicionais incluem:

* Os dispositivos não aceitam conexões de rede não solicitadas. Eles estabelecem todas as conexões e rotas de modo somente saída. Para que um dispositivo receba um comando do back-end, o dispositivo deve iniciar uma conexão para verificar se há comandos pendentes a serem processados. Quando uma conexão entre o dispositivo e o Hub IoT é estabelecida com segurança, as mensagens da nuvem para o dispositivo e o dispositivo para a nuvem podem ser enviadas de forma transparente.

* Os dispositivos só se conectam ou estabelecem rotas para serviços conhecidos com os quais eles são emparelhados, como um hub IoT do Azure.

* A autorização e a autenticação no nível do sistema usam identidades por dispositivo, tornando as credenciais de acesso e as permissões irrevogável imediatamente.

### <a name="secure-connectivity"></a>Conectividade segura

A durabilidade das mensagens é um recurso importante de qualquer solução de IoT. A necessidade de permanentemente entregar comandos e/ou receber dados de dispositivos é sublinhada pelo fato de que os dispositivos IoT estão conectados pela Internet ou outras redes semelhantes que podem não ser confiáveis. O Hub IoT do Azure oferece durabilidade de mensagens entre a nuvem e os dispositivos por meio de um sistema de confirmações em resposta a mensagens. A durabilidade adicional para mensagens é obtida armazenando em cache as mensagens no Hub IoT por até sete dias para a telemetria e dois dias para os comandos.

A eficiência é importante para garantir a conservação dos recursos e da operação em um ambiente com restrição de recursos. O HTTPS (HTTP Secure), a versão segura padrão do setor do protocolo http popular, tem suporte do Hub IoT do Azure, permitindo uma comunicação eficiente. Advanced Message Queuing Protocol (AMQP) e MQTT (transporte de telemetria de enfileiramento de mensagens), com suporte do Hub IoT do Azure, são projetados não apenas para eficiência em termos de uso de recursos, mas também entrega de mensagens confiável. 

A escalabilidade exige a capacidade de interoperar com segurança com uma ampla variedade de dispositivos. O Hub IoT do Azure permite uma conexão segura com dispositivos habilitados para IP e não habilitados para IP. Os dispositivos habilitados para IP podem se conectar diretamente e se comunicar com o Hub IoT por meio de uma conexão segura. Os dispositivos não habilitados para IP têm restrições de recursos e se conectam somente a protocolos de comunicação de curta distância, como Zwave, ZigBee e Bluetooth. Um gateway de campo é usado para agregar esses dispositivos e executa a conversão de protocolo para habilitar a comunicação bidirecional segura com a nuvem.

Os recursos de segurança de conexão adicionais incluem:

* O caminho de comunicação entre os dispositivos e o Hub IoT do Azure, ou entre gateways e o Hub IoT do Azure, é protegido usando TLS (segurança de camada de transporte) padrão do setor com o Hub IoT do Azure autenticado usando o protocolo X. 509.

* Para proteger dispositivos de conexões de entrada não solicitadas, o Hub IoT do Azure não abre nenhuma conexão com o dispositivo. O dispositivo inicia todas as conexões.

* O permanentemente do Hub IoT do Azure armazena mensagens para dispositivos e aguarda o dispositivo se conectar. Esses comandos são armazenados por dois dias, permitindo que os dispositivos se conectem esporadicamente, devido a problemas de conectividade ou energia, para receber esses comandos. O Hub IoT do Azure mantém uma fila por dispositivo para cada dispositivo.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Proteger o processamento e o armazenamento na nuvem

Desde comunicações criptografadas até o processamento de dados na nuvem, os aceleradores de solução ajudam a manter os dados seguros. Ele fornece flexibilidade para implementar criptografia adicional e gerenciamento de chaves de segurança.

Usando Azure Active Directory (AAD) para autenticação e autorização de usuário, os aceleradores de solução do Azure IoT podem fornecer um modelo de autorização baseado em políticas para dados na nuvem, permitindo o gerenciamento de acesso fácil que pode ser auditado e revisado. Esse modelo também permite a revogação quase instantânea de acesso a dados na nuvem e de dispositivos conectados aos aceleradores de solução de IoT do Azure.

Depois que os dados estiverem na nuvem, eles poderão ser processados e armazenados em qualquer fluxo de trabalho definido pelo usuário. O acesso a cada parte dos dados é controlado com Azure Active Directory, dependendo do serviço de armazenamento usado.

Todas as chaves usadas pela infraestrutura de IoT são armazenadas na nuvem no armazenamento seguro, com a capacidade de se sobrepor caso as chaves precisem ser reprovisionadas. Os dados podem ser armazenados em [Azure Cosmos DB](../articles/cosmos-db/introduction.md) ou em bancos de dado [SQL](../articles/sql-database/sql-database-faq.md), permitindo a definição do nível de segurança desejado. Além disso, o Azure fornece uma maneira de monitorar e auditar todo o acesso aos seus dados para alertá-lo sobre qualquer intrusão ou acesso não autorizado.

## <a name="conclusion"></a>Conclusão

A Internet das Coisas começa com suas coisas, as coisas mais importantes para as empresas. A IoT pode fornecer um valor incrível a uma empresa, reduzindo os custos, aumentando a receita e transformando os negócios. O sucesso dessa transformação depende muito da escolha do software e do provedor de serviços de IoT corretos. Isso significa encontrar um provedor que não só catalise essa transformação ao compreender as necessidades e os requisitos dos negócios, mas também fornece serviços e software criados com segurança, privacidade, transparência e conformidade como considerações de design principais. A Microsoft tem ampla experiência no desenvolvimento e implantação de software e serviços seguros e continua a ser líder nessa nova idade de Internet das Coisas.

Os aceleradores de solução criam medidas de segurança por design, permitindo o monitoramento seguro de ativos para melhorar a eficiência, impulsionar o desempenho operacional para habilitar a inovação e empregar análises de dados avançadas para transformar as empresas. Com sua abordagem em camadas para segurança, vários recursos de segurança e padrões de design, os aceleradores de solução ajudam a implantar uma infraestrutura que pode ser confiável para transformar qualquer negócio.

## <a name="additional-information"></a>Informação adicional

Cada Solution Accelerator cria instâncias de serviços do Azure, como:

* [**Hub IOT do Azure**](https://azure.microsoft.com/services/iot-hub/): seu gateway que conecta a nuvem a dispositivos. Você pode dimensionar para milhões de conexões por Hub e processar grandes volumes de dados com suporte de autenticação por dispositivo, ajudando-o a proteger sua solução.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): um serviço de banco de dados escalonável e totalmente indexado para os data semiestruturados que gerenciam os metadados para os dispositivos que você provisiona, como atributos, configuração e propriedades de segurança. O Azure Cosmos DB oferece processamento de alto desempenho e alta taxa de transferência, indexação independente de esquema de dados e uma interface de consulta SQL rica.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): processamento de fluxo em tempo real na nuvem que permite desenvolver e implantar rapidamente uma solução de análise de baixo custo para descobrir informações em tempo real de dispositivos, sensores, infraestrutura e aplicativos. Os dados desse serviço totalmente gerenciado podem ser dimensionados para qualquer volume e, ao mesmo tempo, obter alta taxa de transferência, baixa latência e resiliência.

* [**Serviços de Azure app**](https://azure.microsoft.com/services/app-service/): uma plataforma de nuvem para criar aplicativos Web e móveis avançados que se conectam a dados em qualquer lugar; na nuvem ou no local. Crie aplicações móveis cativantes para iOS, Android e Windows. Integre-se com seu SaaS (software como serviço) e aplicativos empresariais com conectividade integrada a dezenas de serviços baseados em nuvem e aplicativos empresariais. Codifique em sua linguagem favorita e IDE — .NET, Node. js, PHP, Python ou Java — para criar aplicativos Web e APIs mais rápido do que nunca.

* [**Aplicativos lógicos**](https://azure.microsoft.com/services/app-service/logic/): o recurso aplicativos lógicos do serviço Azure App ajuda a integrar sua solução de IOT aos seus sistemas de linha de negócios existentes e a automatizar processos de fluxo de trabalho. Os aplicativos lógicos permitem que os desenvolvedores projetem fluxos de trabalho que começam de um gatilho e, em seguida, executam uma série de etapas — regras e ações que usam conectores poderosos para integração com seus processos de negócios. Os aplicativos lógicos oferecem conectividade pronta para uso a um vasto ecossistema de aplicativos SaaS, baseados em nuvem e locais.

* [**Armazenamento de BLOBs do Azure**](https://azure.microsoft.com/services/storage/): armazenamento em nuvem confiável e econômico para os dados que seus dispositivos enviam para a nuvem.