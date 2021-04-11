---
title: Funcionalidades de pré-visualização na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
description: Lista atualizada de funcionalidades atualmente em pré-visualização
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 775785e1b5130499d69b269e72f5c774e9e5f3f9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024086"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>Funcionalidades de pré-visualização para PostgreSQL - Hiperescala (Citus)

A Azure Database for PostgreSQL - Hyperscale (Citus) oferece pré-visualizações para funcionalidades inéditas. As versões de pré-visualização são fornecidas sem um acordo de nível de serviço, e não são recomendadas para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Para mais informações, consulte [Termos de Utilização Suplementares para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="features-currently-in-preview"></a>Funcionalidades atualmente em pré-visualização

Aqui estão as funcionalidades atualmente disponíveis para pré-visualização:

* **[Armazenamento colunar](concepts-hyperscale-columnar.md)**.
  Guarde as colunas de mesas selecionadas (em vez de linhas) contíguas no disco. Suporta a compressão no disco. Bom para cargas de trabalho de armazenamento de dados e analíticos.
* **[PostgreSQL 12 e 13](concepts-hyperscale-versions.md)**.
  Utilize a versão mais recente da base de dados no seu grupo de servidores.
* **[Nível básico](concepts-hyperscale-tiers.md)**. Executar um grupo de servidor usando apenas um nó coordenador e nenhum nó de trabalhador. Uma forma económica de fazer testes e desenvolvimento iniciais, e lidar com pequenas cargas de trabalho de produção.
* **[Leia réplicas](howto-hyperscale-read-replicas-portal.md)** (atualmente apenas na mesma região). Quaisquer alterações que ocorram ao grupo de servidor primário refletem-se na sua réplica, e as consultas contra a réplica não causam nenhuma carga extra no original.
  As réplicas são uma ferramenta útil para melhorar o desempenho para cargas de trabalho apenas de leitura.
* **[Gerido PgBouncer](concepts-hyperscale-limits.md#managed-pgbouncer-preview)**.
  Um pooler de ligação que permite que muitos clientes se conectem ao grupo de servidor de uma só vez, limitando o número de ligações ativas. Satisfaz os pedidos de ligação, mantendo o nó coordenador a funcionar sem problemas.
* **[PgAudit.](concepts-hyperscale-audit.md)** Fornece sessão detalhada e auditoria de objetos através da instalação de registo postgresQL padrão. Produz registos de auditoria necessários para passar em determinadas auditorias de certificação do governo, financeiros ou ISO.

### <a name="available-regions-for-preview-features"></a>Regiões disponíveis para funcionalidades de pré-visualização

A extensão pgAudit está disponível em todas as [regiões suportadas pela Hyperscale (Citus)](concepts-hyperscale-configuration-options.md#regions).
As outras funcionalidades de pré-visualização estão disponíveis apenas nos **EUA.**

## <a name="does-my-server-group-have-access-to-preview-features"></a>O meu grupo de servidores tem acesso a funcionalidades de pré-visualização?

Para determinar se o seu grupo de servidor Hyperscale (Citus) tem funcionalidades de pré-visualização ativadas, navegue na página **geral** do grupo do servidor no portal Azure.
Se vir o Tier da **propriedade: Básico (pré-visualização)** ou **Tier: Standard (pré-visualização)** então o seu grupo de servidor tem acesso às funcionalidades de pré-visualização.

### <a name="how-to-get-access"></a>Como ter acesso

Ao criar um novo grupo de servidores Hyperscale (Citus), verifique a caixa Ativar as **funcionalidades de pré-visualização.**

## <a name="contact-us"></a>Contacte-nos

Informe-nos sobre a sua experiência utilizando funcionalidades de pré-visualização, enviando um e-mail [para Ask Azure DB para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).
(Este endereço de e-mail não é um canal de suporte técnico. Para problemas técnicos, abra um pedido de [apoio](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).)
