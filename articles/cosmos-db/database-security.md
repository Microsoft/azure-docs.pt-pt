---
title: Segurança de base de dados - Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece segurança de dados e proteção de base de dados para os seus dados.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: rimman
ms.openlocfilehash: d37f373e98835846c2d29130781706c400086c0e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077246"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Segurança no Azure Cosmos DB - descrição geral

Este artigo discute as práticas recomendadas de segurança da base de dados e os principais recursos oferecidos pelo Azure Cosmos DB para o ajudar a prevenir, detetar e responder a falhas de banco de dados.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>O que há de novo na segurança do Azure Cosmos DB?

Encriptação inativa está agora disponível para documentos e cópias de segurança armazenadas no Azure Cosmos DB em todas as regiões do Azure. Encriptação inativa é aplicada automaticamente para clientes novos e existentes nestas regiões. Não é necessário configurar nada; obter a mesma excelente latência, débito, disponibilidade e é de funcionalidade como antes, com o benefício de saber os seus dados seguros e protegidos com encriptação em repouso.

## <a name="how-do-i-secure-my-database"></a>Como posso proteger meu banco de dados? 

Segurança de dados é uma responsabilidade partilhada entre si, o cliente e o seu fornecedor de base de dados. Dependendo do fornecedor de base de dados que escolher, pode variar a quantidade de responsabilidade que ser executadas. Se optar por uma solução no local, terá de fornecer tudo, desde a proteção de ponto final para a segurança física do seu hardware - que não é tarefa fácil. Se optar por um fornecedor de base de dados de cloud de PaaS, como o Azure Cosmos DB, reduz consideravelmente sua área de preocupação. A imagem seguinte, emprestadas da Microsoft [responsabilidades partilhadas para a computação em nuvem](https://aka.ms/sharedresponsibility) white paper, mostra como a sua responsabilidade diminui com um provedor de PaaS como o Azure Cosmos DB.

![Responsabilidades de fornecedor de cliente e a base de dados](./media/database-security/nosql-database-security-responsibilities.png)

O diagrama anterior mostra cloud de alto nível de componentes de segurança, mas que itens precisa se preocupar sobre especificamente para a sua solução de base de dados? E como pode comparar soluções entre si? 

Recomendamos a seguinte lista de verificação dos requisitos em que pretende comparar sistemas de base de dados:

- Segurança de rede e as definições da firewall
- Autenticação de utilizador e controles de usuário detalhada
- Capacidade para replicar dados globalmente para falhas regionais
- Capacidade para executar as ativações pós-falha a partir do Centro de dados para outro
- Replicação de dados locais dentro de um centro de dados
- Cópias de segurança de dados automática
- Restauro de dados eliminados de cópias de segurança
- Proteger e isolar os dados confidenciais
- Monitorização de ataques
- Responder a ataques
- Capacidade de dados de georreplicação cerca de cumprir para restrições de governação de dados
- Proteção física dos servidores nos centros de dados protegidos
- Certificações

E embora possa parecer óbvio, recentes [violações de base de dados em grande escala](https://thehackernews.com/2017/01/mongodb-database-security.html) lembre-na importância de simples, mas críticas dos seguintes requisitos:
- Patches em servidores que são mantidos atualizados
- HTTPS por criptografia SSL/padrão
- Contas administrativas com palavras-passe fortes

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Como é que o Azure Cosmos DB proteger meu banco de dados?

Vamos examinar novamente a lista anterior - o número desses requisitos de segurança do Azure Cosmos DB fornece? Cada um.

Vamos examinar cada um em detalhes.

|Requisito de segurança|Abordagem de segurança do Azure Cosmos DB|
|---|---|
|Segurança da rede|Usando um firewall IP é a primeira camada de proteção para proteger a sua base de dados. O Azure Cosmos DB suporta a política de controlado por controlos de acesso baseado em IP para o suporte de firewall de entrada. Os controlos de acesso baseado em IP são semelhantes às regras de firewall utilizadas pelos sistemas de base de dados tradicionais, mas eles são expandidos para que uma conta de base de dados do Azure Cosmos DB só é acessível a partir de um conjunto aprovado de máquinas ou serviços cloud. <br><br>O Azure Cosmos DB permite-lhe permitir um endereço IP específico (168.61.48.0), um intervalo IP (168.61.48.0/8) e combinações de IPs e intervalos. <br><br>Todos os pedidos provenientes de máquinas fora desta lista de permissões são bloqueados pelo Azure Cosmos DB. Pedidos de máquinas aprovadas e, em seguida, os serviços de cloud tem de concluir o processo de autenticação para obter o controlo de acesso aos recursos.<br><br>Saiba mais em [suporte da firewall do Azure Cosmos DB](firewall-support.md).|
|Autorização|O Azure Cosmos DB utiliza o código de autenticação de mensagens baseada em hash (HMAC) para autorização. <br><br>Cada solicitação é protegido por hash com a chave secreta de conta e o hash de com codificação base 64 subsequente é enviado com cada chamada para o Azure Cosmos DB. Para validar o pedido, o serviço do Azure Cosmos DB utiliza a chave secreta correto e as propriedades para gerar um hash e, em seguida, ele compara o valor no pedido. Se os dois valores corresponderem, a operação está autorizada com êxito e o processamento do pedido, caso contrário, há uma falha de autorização e a solicitação é rejeitada.<br><br>Pode utilizar tanto um [chave mestra](secure-access-to-data.md#master-keys), ou uma [token de recursos](secure-access-to-data.md#resource-tokens) que permite o acesso detalhado para um recurso, como um documento.<br><br>Saiba mais em [proteger o acesso aos recursos do Azure Cosmos DB](secure-access-to-data.md).|
|Os utilizadores e permissões|Utilizar a chave mestra para a conta, pode criar recursos de utilizador e os recursos de permissão por base de dados. Um token de recurso está associado uma permissão numa base de dados e determina se o utilizador tem acesso (leitura / escrita, só de leitura, ou sem acesso) para um recurso de aplicação na base de dados. Recursos de aplicativos incluem o contentor, documentos, anexos, procedimentos armazenados, acionadores e UDFs. O token de recursos, em seguida, é utilizado durante a autenticação para fornecer ou negar o acesso ao recurso.<br><br>Saiba mais em [proteger o acesso aos recursos do Azure Cosmos DB](secure-access-to-data.md).|
|Integração do Active Directory (RBAC)| Também pode fornecer acesso à conta de base de dados usando o controle de acesso (IAM) no portal do Azure, conforme mostrado na captura de ecrã a seguir à tabela. IAM fornece controlo de acesso baseado em funções e integra-se com o Active Directory. Pode usar funções incorporadas ou funções personalizadas para indivíduos e grupos, conforme mostrado na imagem seguinte.|
|Replicação global|O Azure Cosmos DB oferece distribuição global chave na mão, que permite-lhe replicar os seus dados para qualquer um dos datacenters de nível mundial do Azure com o clique de um botão. Replicação global permite-lhe dimensioná-las globalmente e fornecer acesso de latência baixa aos seus dados em todo o mundo.<br><br>No contexto de segurança, os replicação global garante proteção de dados contra falhas regionais.<br><br>Saiba mais em [Distribuir dados globalmente](distribute-data-globally.md).|
|Ativações pós-falha regionais|Se tiver replicado seus dados em mais de um Datacenter, do Azure Cosmos DB passa automaticamente suas operações deve um Datacenter regional ficam offline. Pode criar uma lista prioritária de regiões de ativação pós-falha, as regiões em que os dados são replicados a utilizar. <br><br>Saiba mais em [ativações pós-falha regionais no Azure Cosmos DB](high-availability.md).|
|Replicação local|Mesmo dentro de um único centro de dados, do Azure Cosmos DB replica automaticamente os dados para elevada disponibilidade, dando-lhe a opção de [níveis de consistência](consistency-levels.md). Isso garante que um 99,99% [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/cosmos-db) para todas as contas de região única e todas as contas de várias regiões com consistência flexível e 99,999% de disponibilidade em todas as contas de base de dados de várias regiões de leitura.|
|Cópias de segurança online automáticas|Bases de dados do Cosmos DB do Azure são uma cópia de segurança regularmente e armazenados num arquivo georedundant. <br><br>Saiba mais em [cópia de segurança online automática e restauro com o Azure Cosmos DB](online-backup-and-restore.md).|
|Dados de restauro eliminado|As cópias de segurança online automatizadas podem ser utilizadas para recuperar dados que pode ter acidentalmente eliminado até aproximadamente 30 dias após o evento. <br><br>Saiba mais em [cópia de segurança online automática e restauro com o Azure Cosmos DB](online-backup-and-restore.md)|
|Proteger e isolar os dados confidenciais|Todos os dados nas regiões listados no que há de novo? agora são encriptados em descanso.<br><br>Os dados pessoais e outros dados confidenciais podem ser isolados para o contêiner específico e de leitura-gravação ou acesso só de leitura pode ser limitado a utilizadores específicos.|
|Monitor de ataques|Usando [registos de atividade e registo de auditoria](logging.md), pode monitorizar a sua conta para a atividade normal e anormal. Pode ver as operações que foram realizadas nos seus recursos, quem a iniciou a operação, aquando da ocorrência da operação, o estado da operação e, muito mais como o mostrado na captura de ecrã após esta tabela.|
|Responder a ataques|Depois de ter de contactar o suporte do Azure para comunicar um potencial ataque, um processo de resposta a incidentes passo 5 é inicializado. O objetivo do processo do passo 5 é restaurar o mais rapidamente possível operações de segurança de serviço normal e depois é detetado um problema e é iniciada uma investigação.<br><br>Saiba mais em [resposta de segurança do Microsoft Azure na Cloud](https://aka.ms/securityresponsepaper).|
|Perímetro geográfico|O Azure Cosmos DB garante a governação de dados para regiões de soberania (por exemplo, Alemanha, China, gov (US)).|
|Instalações protegidas|Dados no Azure Cosmos DB são armazenados em SSDs nos centros de dados protegidos do Azure.<br><br>Saiba mais em [datacenters globais da Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Encriptação de HTTPS/SSL/TLS|Todas as interações do serviço de cliente do Azure Cosmos DB são compatíveis com o SSL/TLS 1.2. Além disso, todos os replicação de datacenter Centro de dados e para várias dentro é SSL/TLS 1.2 impostas.|
|Encriptação inativa|Todos os dados armazenados no Azure Cosmos DB são encriptados em descanso. Saiba mais em [do Azure Cosmos DB a encriptação inativa](./database-encryption-at-rest.md)|
|Servidores corrigidas|Como uma base de dados gerida, o Azure Cosmos DB elimina a necessidade de gerenciar e aplicar um patch servidores, que tem feito para, automaticamente.|
|Contas administrativas com palavras-passe fortes|É difícil de acreditar, ainda precisamos de mencionar esse requisito, mas ao contrário de alguns dos nossos concorrentes, é impossível ter uma conta administrativa com nenhuma palavra-passe no Azure Cosmos DB.<br><br> Segurança através de SSL e HMAC autenticação baseada em segredo está embutida por predefinição.|
|Certificações de proteção de dados e segurança|Para mais até a lista de dados das certificações geral, consulte [site de conformidade do Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) , bem como a disponibilidade mais recente [documentos de conformidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) com todas as certificações (procure Cosmos). Para mais focalizada, leia confira a postagem de dia 25 de Abril de 2018 [#CosmosDB do Azure: Seguro, privadas e em conformidade](https://azure.microsoft.com/blog/azure-cosmosdb-secure-private-compliant/) que inclui SOCS 1/2 tipo 2, HITRUST, PCI DSS nível 1, ISO 27001, HIPAA, FedRAMP elevado e muitos outros.

Captura de ecrã seguinte mostra a integração do Active Directory (RBAC) com o controlo de acesso (IAM) no portal do Azure: ![Controlo de acesso (IAM) no portal do Azure - segurança da base de dados de demonstração](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

Captura de ecrã seguinte mostra como pode utilizar os registos de atividade e registo de auditoria para monitorizar a sua conta: ![Registos de atividades para o Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais detalhes sobre o mestre de chaves e tokens de recursos, consulte [proteger o acesso aos dados do Azure Cosmos DB](secure-access-to-data.md).

Para obter mais detalhes sobre o registo de auditoria, consulte [registo de diagnósticos do Azure Cosmos DB](logging.md).

Para obter mais detalhes sobre as certificações da Microsoft, consulte [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/).
