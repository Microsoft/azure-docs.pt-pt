---
title: Visão geral do serviço de banco de dados relacional do banco de dados do Azure para MySQL
description: Saiba mais sobre o serviço de banco de dados do Azure para MySQL, um serviço de banco de dados relacional no Microsoft Cloud baseado no MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 08/13/2019
ms.openlocfilehash: f193dceaf26361fececde9df9df6769e2a971543
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178111"
---
# <a name="what-is-azure-database-for-mysql"></a>O que é a Base de Dados do Azure para MySQL?

O banco de dados do Azure para MySQL é um serviço de banco de dados relacional no Microsoft Cloud baseado no [MySQL Community Edition](https://www.mysql.com/products/community/) (disponível no mecanismo de banco de dados licença do GPLv2), versões 5,6, 5,7 e 8,0. O banco de dados do Azure para MySQL fornece:

- Elevada disponibilidade incorporada sem custos adicionais.
- Desempenho previsível com preços de pay as you go, inclusive.
- Dimensionamento conforme necessário em segundos.
- Segurança para proteger os dados confidenciais inativos e em movimento.
- Cópias de segurança automáticas e restauro para um ponto anterior no tempo até 35 dias.
- Segurança e conformidade de nível empresarial.

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos à gestão de máquinas virtuais e de infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação, com a plataforma e as ferramentas open-source da sua preferência, e garantir a velocidade e a eficiência exigidas pelo seu negócio sem ter de adquirir novas competências.

![Diagrama conceitual do banco de dados do Azure para MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Este artigo é uma introdução aos conceitos e recursos principais do banco de dados do Azure para MySQL relacionados ao desempenho, escalabilidade e capacidade de gerenciamento, com links para explorar detalhes. Veja estes guias de introdução para começar:

- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Para obter um conjunto de amostras da CLI do Azure, veja:

- [Exemplos de CLI do Azure para o banco de dados do Azure para MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos
O serviço banco de dados do Azure para MySQL oferece várias camadas de serviço: básico, Uso Geral e otimizado para memória. Cada escalão oferece desempenho e capacidades diferentes para suportar desde cargas de trabalho de base de dados leves até pesadas. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte [tipos de preço](concepts-service-tiers.md) para obter detalhes.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Como decidir quando aumentar e reduzir verticalmente? Utilize a monitorização de desempenho incorporada e as funcionalidades de alerta juntamente com as classificações de desempenho baseadas em vCores. Com estas ferramentas, pode avaliar rapidamente o impacto do dimensionamento vertical ou horizontal dos vCores com base nas suas necessidades de desempenho atuais ou estimadas. Consulte [Alertas](howto-alert-on-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais
O contrato de nível de serviço (SLA) do Azure líder da indústria, que garante 99,99% de disponibilidade, com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Com cada servidor de banco de dados do Azure para MySQL, você tira proveito da segurança interna, da tolerância a falhas e da proteção do dado que, de outra forma, você teria de comprar ou projetar, criar e gerenciar. Com o banco de dados do Azure para MySQL, você pode usar a restauração pontual para recuperar um servidor para um estado anterior, de volta a 35 dias.

## <a name="secure-your-data"></a>Proteja os seus dados
Os serviços de banco de dados do Azure têm uma tradição de segurança de data que o banco de dados do Azure para MySQL mantém, com recursos que limitam o acesso, protegem dados em repouso e em movimento e ajudam a monitorar a atividade. Visite o [Centro de Fidedignidade do Azure](https://www.microsoft.com/en-us/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure.

O serviço banco de dados do Azure para MySQL usa o módulo de criptografia FIPS 140-2 validado para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco com a exceção de arquivos temporários criados durante a execução de consultas. O serviço utiliza a cifra AES de 256 bits que está incluída na encriptação de armazenamento do Azure, sendo as chaves geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.

Por padrão, o serviço de banco de dados do Azure para MySQL é configurado para exigir [segurança de conexão SSL](./concepts-ssl-connection-security.md) para dados em movimento pela rede. A imposição de ligações SSL entre a base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação. Opcionalmente, pode desativar a exigência de SSL para ligar ao seu serviço de base de dados, se a aplicação de cliente não suportar a conectividade SSL.

## <a name="contacts"></a>Contactos
Para perguntas ou sugestões que você pode ter sobre como trabalhar com o banco de dados do Azure para MySQL, envie um email para a equipe do banco de dados do Azure para MySQL ([@Ask BD do Azure para MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Este endereço de email não é um alias de suporte técnico.

Além disso, considere os seguintes pontos de contacto, conforme adequado:

- Para contactar o Suporte do Azure, [crie um pedido no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Passos seguintes
Agora que você leu uma introdução ao banco de dados do Azure para MySQL e respondeu à pergunta "o que é o banco de dados do Azure para MySQL?" Você está pronto para:

- Consulte a página de preços para ver as comparações de preços e as calculadoras. [Preços](https://azure.microsoft.com/pricing/details/mysql/)
- Comece por criar o seu primeiro servidor. [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Crie seu primeiro aplicativo usando sua linguagem preferida: [Python](./connect-python.md) | [node. js](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [php](./connect-php.md) | [.net (C#)](./connect-csharp.md)2[go](./connect-go.md)
