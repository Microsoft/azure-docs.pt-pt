---
title: Visão geral - Base de Dados Azure para MySQL
description: Saiba mais sobre a Base de Dados Azure para o serviço MySQL, um serviço de base de dados relacional na nuvem da Microsoft com base na Edição Comunitária MySQL.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 20b54afdfbb5641def921ffc17b3c38f6650acab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736224"
---
# <a name="what-is-azure-database-for-mysql"></a>O que é a Base de Dados do Azure para MySQL?

Azure Database for MySQL é um serviço de base de dados relacional na nuvem da Microsoft com base no motor de base [de dados MySQL Community Edition](https://www.mysql.com/products/community/) (disponível sob a licença GPLv2), versões 5.6, 5.7 e 8.0. A Base de Dados do Azure para MySQL fornece:

- Elevada disponibilidade incorporada.
- Proteção de dados utilizando cópias de segurança automáticas e restauro pontual por um período máximo de 35 dias.
- Manutenção automatizada para hardware subjacente, sistema operativo e motor de base de dados para manter o serviço seguro e atualizado.
- Desempenho previsível, com preços pay as you go inclusivos.
- Escalagem elástica em segundos.
- Controlos de otimização de custos com a capacidade de parar/iniciar o servidor. 
- Segurança de nível empresarial e conformidade líder do setor para proteger dados sensíveis em repouso e em movimento.
- Monitorização e automatização para simplificar a gestão e monitorização para implementações em larga escala.
- Experiência de apoio líder na indústria.

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos à gestão de máquinas virtuais e de infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação, com a plataforma e as ferramentas open-source da sua preferência, e garantir a velocidade e a eficiência exigidas pelo seu negócio sem ter de adquirir novas competências.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Base de Dados Azure para diagrama conceptual mySQL":::

## <a name="deployment-models"></a>Modelos de implementação

A azure Database for MySQL alimentado pela edição comunitária MySQL está disponível em dois modos de implementação:
- Servidor Único 
- Servidor Flexível (Pré-visualização)
  
### <a name="azure-database-for-mysql---single-server"></a>Base de Dados Azure para MySQL - Servidor Único

Azure Database for MySQL Single Server é um serviço de base de dados totalmente gerido com requisitos mínimos para personalizações de bases de dados. A plataforma de servidor único foi concebida para lidar com a maioria das funções de gestão da base de dados, tais como patching, backups, alta disponibilidade, segurança com a configuração e controlo mínimos do utilizador. A arquitetura está otimizada para alta disponibilidade incorporada com 99,99% de disponibilidade em zona de disponibilidade única. Suporta a versão comunitária do MySQL 5.6, 5.7 e 8.0. O serviço está geralmente disponível hoje em dia em grande variedade de regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/services/)

A opção de implementação Servidor Único oferece três escalões de preço: Básico, Fins Geral e Otimizada para Memória. Cada escalão oferece capacidades de recursos diferentes para suportar as suas cargas de trabalho de base de dados. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte [Escalões de preço](concepts-pricing-tiers.md) para obter mais detalhes.

Os servidores individuais são mais adequados para aplicações nativas em nuvem concebidas para lidar com remendos automatizados sem a necessidade de controlo granular na programação de remendos e configurações personalizadas de configuração do MySQL. 

Para uma visão geral detalhada do modo de implementação de um único servidor, consulte [uma visão geral do servidor único](single-server-overview.md).

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Base de Dados Azure para MySQL - Servidor Flexível (Pré-visualização)

A Azure Database for MySQL Flexible Server é um serviço de base de dados totalmente gerido projetado para fornecer mais controlo granular e flexibilidade sobre funções de gestão de bases de dados e configurações de configuração. Em geral, o serviço proporciona mais flexibilidade e personalizaçãos com base nos requisitos do utilizador. A arquitetura flexível do servidor permite que os utilizadores optem pela alta disponibilidade dentro de uma única zona de disponibilidade e em várias zonas de disponibilidade. Os servidores flexíveis proporcionam controlos de otimização de custos com a capacidade de parar/iniciar o servidor e o nível de computação rebentado, ideal para cargas de trabalho que não necessitam de capacidade de computação completa continuamente. O serviço suporta a versão comunitária do MySQL 5.7 e 8.0. O serviço encontra-se atualmente em pré-visualização pública, disponível hoje em grande variedade de regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/services/)

Servidores flexíveis são os mais adequados para 
- Desenvolvimentos de aplicações que requerem melhor controlo e personalização.
- Zona redundante alta disponibilidade
- Janelas de manutenção geridas

Para uma visão geral detalhada do modo de implementação flexível do servidor, consulte [a visão geral do servidor flexível](flexible-server/overview.md).

## <a name="contacts"></a>Contactos
Para quaisquer perguntas ou sugestões que possa ter sobre trabalhar com a Azure Database para o MySQL, envie um e-mail para a Base de Dados Azure para a MySQL Team ([ @Ask Azure DB for MySQL).](mailto:AskAzureDBforMySQL@service.microsoft.com) Este endereço de e-mail não é um pseudónimo de suporte técnico.

Além disso, considere os seguintes pontos de contacto, conforme adequado:

- Para contactar o Suporte do Azure, [crie um pedido no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os dois modos de implementação do Azure Database para o MySQL e escolha as opções certas com base nas suas necessidades.

- [Servidor Único](single-server/index.yml)
- [Servidor Flexível](flexible-server/index.yml)
- [Escolha a opção de implementação MySQL certa para a sua carga de trabalho](select-right-deployment-type.md)
