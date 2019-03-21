---
title: Descrição geral da base de dados do Azure para o serviço de base de dados relacional MySQL
description: Descrição geral da base de dados do Azure para o serviço de base de dados relacional MySQL.
ms.service: mysql
author: ajlam
ms.author: andrela
ms.custom: mvc
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 2852cab05fab8e15b7e60a22f54cc866d2f0f178
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295407"
---
# <a name="what-is-azure-database-for-mysql"></a>O que é a base de dados do Azure para MySQL?

Base de dados do Azure para MySQL é um serviço de base de dados relacional na cloud da Microsoft com base na [MySQL Community Edition](https://www.mysql.com/products/community/) (disponível sob a licença GPLv2) o motor de base de dados, as versões 5.6 e 5.7. Fornece a base de dados do Azure para MySQL:

- Elevada disponibilidade incorporada sem custos adicionais.
- Desempenho previsível com preços de pay as you go, inclusive.
- Dimensionamento conforme necessário em segundos.
- Segurança para proteger os dados confidenciais inativos e em movimento.
- Cópias de segurança automáticas e restauro para um ponto anterior no tempo até 35 dias.
- Segurança e conformidade de nível empresarial.

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos à gestão de máquinas virtuais e de infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação, com a plataforma e as ferramentas open-source da sua preferência, e garantir a velocidade e a eficiência exigidas pelo seu negócio sem ter de adquirir novas competências.

![Base de dados do Azure para MySQL de diagrama conceptual](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Este artigo é uma introdução à base de dados do Azure para MySQL principais conceitos e funcionalidades relacionadas com o desempenho, escalabilidade e capacidade de gerenciamento, com ligações para explorar detalhes. Veja estes guias de introdução para começar:

- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Para obter um conjunto de amostras da CLI do Azure, veja:

- [Exemplos da CLI do Azure para a base de dados do Azure para MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos
A base de dados do Azure para o serviço MySQL oferece vários escalões de serviço: Básico, fins gerais e com otimização de memória. Cada escalão oferece desempenho e capacidades diferentes para suportar desde cargas de trabalho de base de dados leves até pesadas. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Ver [escalões de preço](concepts-service-tiers.md) para obter detalhes.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Como decidir quando aumentar e reduzir verticalmente? Utilize a monitorização de desempenho incorporada e as funcionalidades de alerta juntamente com as classificações de desempenho baseadas em vCores. Com estas ferramentas, pode avaliar rapidamente o impacto do dimensionamento vertical ou horizontal dos vCores com base nas suas necessidades de desempenho atuais ou estimadas. Consulte [Alertas](howto-alert-on-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais
O contrato de nível de serviço (SLA) do Azure líder da indústria, que garante 99,99% de disponibilidade, com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Em cada base de dados do Azure para o servidor MySQL, pode tirar partido da segurança incorporadas, tolerância a falhas e proteção de dados que caso contrário, teria de comprar ou conceber, criar e gerir. Com a base de dados do Azure para MySQL, pode utilizar o restauro de ponto no tempo para recuperar um servidor para um estado anterior, até 35 dias.

## <a name="secure-your-data"></a>Proteger os dados
Serviços de base de dados do Azure tem um tradição de segurança de dados que mantém a base de dados do Azure para MySQL, com funcionalidades que limitam o acesso, proteger os dados inativos e em movimento e ajudá-lo a monitorizar a atividade. Visite o [Centro de Fidedignidade do Azure](https://www.microsoft.com/en-us/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure.

A base de dados do Azure para o serviço MySQL utiliza a encriptação de armazenamento para dados inativos e está em conformidade com a FIPS 140-2. Os dados, incluindo as cópias de segurança, são encriptados em disco (à exceção dos ficheiros temporários criados pelo motor durante a execução de consultas). O serviço utiliza a cifra AES de 256 bits que está incluída na encriptação de armazenamento do Azure, sendo as chaves geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.

Por predefinição, a base de dados do Azure para o serviço MySQL está configurado para exigir [segurança de ligação SSL](./concepts-ssl-connection-security.md) para dados em movimento na rede. A imposição de ligações SSL entre a base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação. Opcionalmente, pode desativar a exigência de SSL para ligar ao seu serviço de base de dados, se a aplicação de cliente não suportar a conectividade SSL.

## <a name="contacts"></a>Contactos
Para quaisquer dúvidas ou sugestões, poderá ter informações sobre como trabalhar com a base de dados do Azure para MySQL, envie um e-mail para a base de dados do Azure para MySQL equipe ([ @Ask DB do Azure para MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Tenha em atenção que não se trata de um alias de suporte técnico.

Além disso, considere os seguintes pontos de contacto, conforme adequado:

- Para contactar o Suporte do Azure, [crie um pedido no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Passos Seguintes
Agora que leia uma introdução à base de dados do Azure para MySQL e respondeu à pergunta "O que é Azure da base de dados para o MySQL?", está pronto para:

- Consulte a página de preços para ver as comparações de preços e as calculadoras. [Preços](https://azure.microsoft.com/pricing/details/mysql/)
- Comece por criar o seu primeiro servidor. [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Crie seu primeiro aplicativo usando o seu idioma preferencial: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
