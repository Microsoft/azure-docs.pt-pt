---
title: Visão geral - Base de Dados Azure para MySQL
description: Saiba mais sobre a Base de Dados Azure para o serviço MySQL, um serviço de base de dados relacional na nuvem da Microsoft com base na Edição Comunitária MySQL.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 37bc99d9f83f185a5372fd45634351987b85e20b
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763665"
---
# <a name="what-is-azure-database-for-mysql"></a>O que é a Base de Dados do Azure para MySQL?

Azure Database for MySQL é um serviço de base de dados relacional na nuvem da Microsoft com base no motor de base [de dados MySQL Community Edition](https://www.mysql.com/products/community/) (disponível sob a licença GPLv2), versões 5.6, 5.7 e 8.0. A azure Database for MySQL entrega:

- Elevada disponibilidade incorporada sem custos adicionais.
- Desempenho previsível com preços de pay as you go, inclusive.
- Dimensionamento conforme necessário em segundos.
- Segurança para proteger os dados confidenciais inativos e em movimento.
- Cópias de segurança automáticas e restauro para um ponto anterior no tempo até 35 dias.
- Segurança e conformidade de nível empresarial.

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos à gestão de máquinas virtuais e de infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação, com a plataforma e as ferramentas open-source da sua preferência, e garantir a velocidade e a eficiência exigidas pelo seu negócio sem ter de adquirir novas competências.

![Base de Dados Azure para diagrama conceptual mySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Este artigo é uma introdução à Base de Dados Azure para conceitos e funcionalidades centrais do MySQL relacionadas com desempenho, escalabilidade e gerenciabilidade, com links para explorar detalhes. Veja estes guias de introdução para começar:

- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Para obter um conjunto de amostras da CLI do Azure, veja:

- [Exemplo da CLI do Azure para a Base de Dados do Azure para MySQL](sample-scripts-azure-cli.md)

## <a name="automated-patching"></a>Aplicação de patches automatizada
O serviço executa patching automatizado do hardware subjacente, sistema operativo e motor de base de dados. O patching inclui atualizações de segurança e software para o hardware subjacente, o SISTEMA e o motor da base de dados. Para o motor MySQL, as atualizações de versão menor são automáticas e incluídas como parte do lançamento de patching. Quando a comunidade lança uma versão menor, é automaticamente integrada como parte do ciclo de testes para o serviço. O teste da versão menor é realizado em algumas das cargas de trabalho canónicas para o MySQL. O lançamento de versões menores do motor MySQL é avaliado para fiabilidade (sem falhas), disponibilidade, segurança e desempenho. Nem todas as versões menores são lançadas para produção no serviço, mas é evalida com base na criticidade das correções de bugs e no novo valor incremental. Isto é para encontrar o equilíbrio certo entre o novo valor incremental e minimizar as variáveis no sistema de estabilidade. Não é necessária nenhuma ação do utilizador ou configurações para remendar. A frequência de remendação é gerida com base na criticidade da carga útil. Em geral, o serviço segue o horário de lançamento mensal como parte da integração e libertação contínuas. Os utilizadores podem subscrever a [notificação de manutenção planeada](concepts-monitoring.md) para receber a notificação da próxima manutenção 72 horas antes do evento.

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos
A Base de Dados Azure para o serviço MySQL oferece vários níveis de serviço: Básico, Propósito Geral e Memória Otimizada. Cada escalão oferece desempenho e capacidades diferentes para suportar desde cargas de trabalho de base de dados leves até pesadas. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte [os níveis de preços](concepts-service-tiers.md) para mais detalhes.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Como decidir quando aumentar e reduzir verticalmente? Utilize a monitorização de desempenho incorporada e as funcionalidades de alerta juntamente com as classificações de desempenho baseadas em vCores. Com estas ferramentas, pode avaliar rapidamente o impacto do dimensionamento vertical ou horizontal dos vCores com base nas suas necessidades de desempenho atuais ou estimadas. Consulte [Alertas](howto-alert-on-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais
O contrato de nível de serviço (SLA) do Azure líder da indústria, que garante 99,99% de disponibilidade, com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Com cada Base de Dados Azure para o servidor MySQL, você aproveita a segurança incorporada, tolerância a falhas e proteção de dados que de outra forma teria de comprar ou projetar, construir e gerir. Com a Azure Database para o MySQL, pode utilizar o restauro pontual para recuperar um servidor para um estado anterior, desde há 35 dias.

## <a name="secure-your-data"></a>Proteger os dados
Os serviços de base de dados Azure têm uma tradição de segurança de dados que a Azure Database para o MySQL defende, com funcionalidades que limitam o acesso, protegem os dados em repouso e em movimento, e ajudam a monitorizar a atividade. Visite o [Centro de Fidedignidade do Azure](https://www.microsoft.com/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure. Para obter mais informações sobre a Base de Dados Azure para funcionalidades de segurança mySQL, consulte a [visão geral](concepts-security.md)de segurança .

## <a name="contacts"></a>Contactos
Para quaisquer perguntas ou sugestões que possa ter sobre trabalhar com a Azure Database para o MySQL, envie um e-mail para a Base de Dados Azure para a MySQL Team ([ @Ask Azure DB for MySQL).](mailto:AskAzureDBforMySQL@service.microsoft.com) Este endereço de e-mail não é um pseudónimo de suporte técnico.

Além disso, considere os seguintes pontos de contacto, conforme adequado:

- Para contactar o Suporte do Azure, [crie um pedido no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Passos seguintes
Agora que leu uma introdução à Base de Dados Azure para o MySQL e respondeu à pergunta "O que é a Base de Dados Azure para o MySQL?" Está pronto para:

- Consulte a página de preços para ver as comparações de preços e as calculadoras. [Preços](https://azure.microsoft.com/pricing/details/mysql/)
- Comece por criar o seu primeiro servidor. [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Construa a sua primeira aplicação utilizando o seu idioma preferido:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Ir](./connect-go.md)
