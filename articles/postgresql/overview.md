---
title: Descrição geral do serviço de base de dados relacional da Base de Dados do Azure para PostgreSQL
description: Fornece uma descrição geral da Base de Dados do Azure para o serviço de base de dados relacional PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/14/2018
ms.openlocfilehash: 318778a83c82b0ddb88f8bbd852442ab389fedb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680323"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é a Base de Dados do Azure para PostgreSQL?

Base de dados do Azure para PostgreSQL é um serviço de base de dados relacional na cloud da Microsoft criado para programadores com base na versão de Comunidade de open source [PostgreSQL](https://www.postgresql.org/) motor de base de dados, as versões 9,5, 9.6 e 10. A Base de Dados do Azure para PostgreSQL oferece:

- Elevada disponibilidade incorporada sem custos adicionais
- Desempenho previsível com preços pay as you go inclusivos
- Dimensionamento conforme necessário no prazo de segundos
- Segurança para proteger os dados confidenciais inativos e em movimento
- Cópias de segurança automáticas e restauro para um ponto anterior no tempo até 35 dias
- Segurança e conformidade de nível empresarial

Todas estas funcionalidades não exigem praticamente qualquer ação de administração e são fornecidas sem custos adicionais. Estas capacidades permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e aceleração do seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos para a gestão de máquinas virtuais e de infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação, com a plataforma e as ferramentas de código aberto da sua preferência, e garantir a velocidade e a eficiência exigidas pelo seu negócio sem ter de aprender novas competências. 

Este artigo apresenta os conceitos e as funcionalidades essenciais da Base de Dados do Azure para PostgreSQL relacionados com desempenho, escalabilidade e capacidade de gestão. Veja estes guias de introdução para começar:

- [Criar uma Base de Dados do Azure para PostgreSQL com o portal do Azure](quickstart-create-server-database-portal.md)
- [Criar uma Base de Dados do Azure para PostgreSQL com a CLI do Azure](quickstart-create-server-database-azure-cli.md)

Para obter um conjunto de amostras da CLI do Azure, veja:

- [Amostras de CLI do Azure para a Base de Dados do Azure para PostgreSQL](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos
Base de dados do Azure para o serviço PostgreSQL oferece três escalões de preço: Básico, fins gerais e com otimização de memória. Cada escalão oferece capacidades de recursos diferentes para suportar as suas cargas de trabalho de base de dados. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Ver [escalões de preço](concepts-pricing-tiers.md) para obter detalhes.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Como decidir quando aumentar ou reduzir verticalmente? Utilize as funcionalidades de monitorização e alertas incorporadas do Azure. Com estas ferramentas, pode avaliar rapidamente o impacto do aumento ou redução verticais consoante as necessidades de desempenho ou armazenamento atuais ou estimadas. Consulte [Alertas](howto-alert-on-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais
O contrato de nível de serviço (SLA) do Azure líder da indústria, que garante 99,99% de disponibilidade, com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Em cada servidor de Base de Dados do Azure para PostgreSQL, pode tirar partido da segurança, da tolerância a falhas e da proteção de dados incorporadas, que, de outra forma, teria de comprar ou conceber, e criar e gerir. Com a Base de Dados do Azure para PostgreSQL, cada escalão de preço oferece um conjunto abrangente de funcionalidades e opções de continuidade de negócio que pode utilizar para assegurar o funcionamento operacional. Pode utilizar o [restauro para um ponto anterior no tempo](howto-restore-server-portal.md) para repor um estado anterior de uma base de dados, até 35 dias. Além disso, se o datacenter que aloja as suas bases de dados sofrer um período de indisponibilidade, pode restaurar as bases de dados a partir de cópias georredundantes de cópias de segurança recentes.

## <a name="secure-your-data"></a>Proteger os dados
Por norma, os serviços de base de dados do Azure asseguram a segurança dos dados. O mesmo se aplica à Base de Dados do Azure para PostgreSQL ao dispor de funcionalidades que limitam o acesso, protegem dados inativos e em movimento, e ajudam a monitorizar a atividade. Visite o [Centro de Fidedignidade do Azure](https://azure.microsoft.com/overview/trusted-cloud/) para obter informações sobre a segurança da plataforma do Azure.

A base de dados do Azure para o serviço PostgreSQL utiliza a encriptação de armazenamento para dados inativos e está em conformidade com a FIPS 140-2. Os dados, incluindo as cópias de segurança, estão encriptados em disco (à exceção dos ficheiros temporários criados pelo motor durante a execução de consultas). O serviço utiliza a cifra AES de 256 bits que está incluída na encriptação de armazenamento do Azure, sendo as chaves geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.

Por predefinição, a Base de Dados do Azure para o serviço PostgreSQL está configurada para exigir [Segurança de ligação SSL](./concepts-ssl-connection-security.md) para dados em movimento na rede. A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação. Opcionalmente, pode desativar a exigência de SSL para ligar ao seu serviço de base de dados, se a aplicação de cliente não suportar a conectividade SSL.

## <a name="contacts"></a>Contactos
Relativamente a dúvidas ou sugestões sobre como trabalhar com a Base de Dados do Azure para PostgreSQL, envie um e-mail para a Equipa da Base de Dados do Azure para PostgreSQL ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Tenha em atenção que não se trata de um alias de suporte técnico.

Além disso, considere os seguintes pontos de contacto, conforme adequado:
- Para contactar o Suporte do Azure, [crie um pedido no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Passos Seguintes
- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver as comparações de preços e as calculadoras.
- Comece por [criar a sua primeira Base de Dados do Azure para PostgreSQL](./quickstart-create-server-database-portal.md).
- Crie seu primeiro aplicativo em Python, PHP, Ruby, C\#, Java, node. js: [Bibliotecas de ligação](./concepts-connection-libraries.md)
