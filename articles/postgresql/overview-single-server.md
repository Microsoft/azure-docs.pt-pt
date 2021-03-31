---
title: Base de dados Azure para servidor único pós-SQL
description: Fornece uma visão geral da Base de Dados Azure para o Servidor Único PostgreSQL.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 18d944cd2b3bfde1a0ee5fc53e83e4016b11b01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379221"
---
# <a name="azure-database-for-postgresql-single-server"></a>Base de dados Azure para servidor único pós-SQL

[A azure Database for PostgreSQL](./overview.md) powered by the PostgreSQL community edition está disponível em três modos de implementação:

- Servidor Único
- Servidor Flexível (Pré-visualização)
- Hyperscale (Citus)

Neste artigo, forneceremos uma visão geral e introdução aos conceitos fundamentais do modelo de implementação de servidor único. Para saber mais sobre o modo de implementação flexível do servidor, consulte a [visão geral flexível do servidor](./flexible-server/overview.md) e a visão geral do Hiperescalo (Citus), respectivamente.

## <a name="overview"></a>Descrição Geral

Single Server é um serviço de base de dados totalmente gerido com requisitos mínimos para personalizações da base de dados. A plataforma de servidor único foi concebida para lidar com a maioria das funções de gestão da base de dados, tais como patching, backups, alta disponibilidade, segurança com a configuração e controlo mínimos do utilizador. A arquitetura está otimizada para proporcionar 99,99% de disponibilidade em zona de disponibilidade única. Suporta a versão comunitária de PostgreSQL 9.5, 9.6, 10 e 11. O serviço está geralmente disponível hoje em dia em grande variedade de regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/services/)

Os servidores individuais são mais adequados para aplicações nativas em nuvem concebidas para lidar com remendos automatizados sem a necessidade de controlo granular na programação de remendos e configurações de configuração postgreSQL personalizadas.

## <a name="high-availability"></a>Elevada disponibilidade

O modelo de implementação de servidor único está otimizado para uma elevada disponibilidade incorporada e elasticidade a um custo reduzido. A arquitetura separa o cálculo e o armazenamento. O motor da base de dados funciona num recipiente de computação proprietário, enquanto os ficheiros de dados residem no armazenamento do Azure. O armazenamento mantém três cópias sincronizadas localmente redundantes dos ficheiros de base de dados que garantem a durabilidade dos dados.

Durante eventos de failover planeados ou não planeados, se o servidor se desligar, o serviço mantém uma elevada disponibilidade dos servidores utilizando o seguinte procedimento automatizado:

1. É a provisionado um novo recipiente de computação.
2. O armazenamento com ficheiros de dados está mapeado para o novo recipiente.
3. O motor de base de dados PostgreSQL é colocado on-line no novo recipiente de computação.
4. O serviço Gateway garante falhas transparentes, garantindo que não são necessárias alterações laterais de aplicação.
   
 :::image type="content" source="./media/overview/overview-azure-postgres-single-server.png" alt-text="Base de dados Azure para servidor único pós-SQL":::

O tempo típico de failover varia entre 60 e 120 segundos. O design nativo em nuvem do serviço de servidor único permite-lhe suportar 99,99% da disponibilidade eliminando o custo de standby quente passivo.

A indústria da Azure liderando 99,99% do acordo de nível de serviço de disponibilidade (SLA), alimentado por uma rede global de datacenters geridos pela Microsoft, ajuda a manter as suas aplicações em funcionamento 24 horas por dia, 7 dias por semana.

## <a name="automated-patching"></a>Aplicação de patches automatizada

O serviço executa patching automatizado do hardware subjacente, sistema operativo e motor de base de dados. O patching inclui atualizações de segurança e software. Para o motor PostgreSQL, as atualizações de versão menores são automáticas e incluídas como parte do ciclo de remendos. Não é necessária nenhuma ação do utilizador ou configurações para remendar. A frequência de remendação é gerida com base na criticidade da carga útil. Em geral, o serviço segue o horário de lançamento mensal como parte da integração e libertação contínuas. Os utilizadores podem subscrever a [notificação de manutenção planeada]() para receber a notificação da próxima manutenção 72 horas antes do evento.

## <a name="automatic-backups"></a>Backups automáticos

O serviço de servidor único cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante (LRS) ou geo-redundante. As cópias de segurança podem ser utilizadas para restaurar o seu servidor em qualquer ponto no tempo dentro do período de retenção de backup. O período de retenção de backup predefinido é de sete dias. A retenção pode ser configurada opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits. Consulte [backups](./concepts-backup.md) para mais detalhes.

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos

O serviço de servidor único está disponível em três níveis SKU: Básico, Final Geral e Memória Otimizada. O nível básico é mais adequado para o desenvolvimento de baixo custo e cargas de trabalho de baixa concordância. O Objetivo Geral e a Memória Otimizados são mais adequados para cargas de trabalho de produção que requerem alta concordância, escala e desempenho previsível. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. O dimensionamento de armazenamento está on-line e suporta o crescimento automático do armazenamento. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Só paga pelos recursos que consome. Consulte [Escalões de preço]() para obter mais detalhes.

## <a name="enterprise-grade-security-compliance-and-governance"></a>Segurança, conformidade e governação de grau empresarial

O serviço de servidor único utiliza o módulo criptográfico validado FIPS 140-2 para encriptação de armazenamento de dados em repouso. Os dados, incluindo cópias de segurança e ficheiros temporários criados durante a execução de consultas são encriptados. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento Azure, e as teclas podem ser geridas pelo sistema (padrão) ou [geridas pelo cliente](). O serviço encripta dados em movimento com segurança da camada de transporte (SSL/TLS) aplicada por padrão. O serviço suporta as versões TLS 1.2, 1.1 e 1.0 com a capacidade de impor a [versão mínima TLS]().

O serviço permite o acesso privado aos servidores utilizando link privado e fornece uma funcionalidade avançada de proteção contra ameaças. A proteção avançada de ameaças deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

Além da autenticação nativa, o serviço de servidor único suporta a autenticação do Azure Ative Directory. A autenticação AZure AD é um mecanismo de ligação aos servidores PostgreSQL utilizando identidades definidas e geridas em Azure AD. Com a autenticação Azure AD, pode gerir as identidades dos utilizadores da base de dados e outros serviços Azure numa localização central, o que simplifica e centraliza o controlo de acessos.

[O registo de auditoria]() (na pré-visualização) está disponível para rastrear toda a atividade do nível da base de dados.

O serviço de servidor único está em conformidade com todas as certificações líderes do setor, como FedRAMP, HIPAA, PCI DSS. Visite o [Centro de Fidedignidade do Azure]() para obter informações sobre a segurança da plataforma do Azure.

Para obter mais informações sobre a Base de Dados Azure para funcionalidades de segurança postgreSQL, consulte a [visão geral]()de segurança .

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

O serviço de servidor único está equipado com funcionalidades de monitorização e alerta de desempenho incorporadas. Todas as métricas Azure têm uma frequência de um minuto, e cada métrica fornece 30 dias de história. Pode configurar alertas sobre as métricas. O serviço permite configurar registos de consulta lenta e vem com uma funcionalidade diferenciada [da loja de consultas.](./concepts-query-store.md) A Consulta Store simplifica a resolução de problemas de desempenho, ajudando-o a encontrar rapidamente as consultas mais longas e intensivas de recursos. Utilizando estas ferramentas, pode otimizar rapidamente as suas cargas de trabalho e configurar o seu servidor para um melhor desempenho. Consulte [a Monitorização](./concepts-monitoring.md) para obter mais detalhes.

## <a name="migration"></a>Migração

O serviço executa a versão comunitária do PostgreSQL. Isto permite a compatibilidade total da aplicação e requer um custo mínimo de refactor para migrar a aplicação existente desenvolvida no motor PostgreSQL para um único serviço de servidor. A migração para o servidor único pode ser realizada utilizando uma das seguintes opções:

- **Despejo e Restauro** – Para migrações offline, onde os utilizadores podem pagar algum tempo de inatividade, despejar e restaurar usando ferramentas comunitárias como Pg_dump e Pg_restore podem fornecer uma forma mais rápida de migrar. Consulte [a Migração utilizando o despejo e restaure-o](./howto-migrate-using-dump-and-restore.md) para obter mais detalhes.
- **Serviço de Migração de Bases de Dados Azure** – Para migrações perfeitas e simplificadas para um único servidor com tempo mínimo de inatividade, o Serviço de Migração da Base de Dados Azure pode ser alavancado. Ver [DMS via portal](../dms/tutorial-postgresql-azure-postgresql-online-portal.md) e [DMS via CLI](../dms/tutorial-postgresql-azure-postgresql-online.md).

## <a name="contacts"></a>Contactos

Para quaisquer perguntas ou sugestões que possa ter sobre trabalhar com a Azure Database para PostgreSQL, envie um e-mail para a Base de Dados Azure para a Equipa PostgreSQL ([ @Ask Azure DB para PostgreSQL).](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) Este endereço de e-mail não é um pseudónimo de suporte técnico.

Além disso, considere os seguintes pontos de contacto, conforme adequado:

- Para contactar o Suporte do Azure, [crie um pedido no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Passos seguintes

Agora que leu uma introdução à Base de Dados Azure para o modo de implementação de um único servidor postgreSQL, está pronto para:
- Crie o seu primeiro servidor.
