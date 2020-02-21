---
title: Segurança de base de dados - Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece segurança de dados e proteção de base de dados para os seus dados.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 145473dfce3d9036b2262ccf3f65f570a2939ef3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524585"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Segurança em Azure Cosmos DB - visão geral

Este artigo discute as melhores práticas de segurança da base de dados e as principais funcionalidades disponibilizadas pelo Azure Cosmos DB para o ajudar a prevenir, detetar e responder a falhas de segurança da base de dados.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Novidades na segurança da Azure Cosmos DB

Encriptação inativa está agora disponível para documentos e cópias de segurança armazenadas no Azure Cosmos DB em todas as regiões do Azure. Encriptação inativa é aplicada automaticamente para clientes novos e existentes nestas regiões. Não é necessário configurar nada; obter a mesma excelente latência, débito, disponibilidade e é de funcionalidade como antes, com o benefício de saber os seus dados seguros e protegidos com encriptação em repouso.

## <a name="how-do-i-secure-my-database"></a>Como posso garantir a minha base de dados

Segurança de dados é uma responsabilidade partilhada entre si, o cliente e o seu fornecedor de base de dados. Dependendo do fornecedor de base de dados que escolher, pode variar a quantidade de responsabilidade que ser executadas. Se optar por uma solução no local, terá de fornecer tudo, desde a proteção de ponto final para a segurança física do seu hardware - que não é tarefa fácil. Se optar por um fornecedor de base de dados de cloud de PaaS, como o Azure Cosmos DB, reduz consideravelmente sua área de preocupação. A imagem seguinte, emprestada do livro branco Shared [Responsibilities for Cloud Computing](https://aka.ms/sharedresponsibility) da Microsoft, mostra como a sua responsabilidade diminui com um fornecedor paaS como o Azure Cosmos DB.

![Responsabilidades de fornecedor de cliente e a base de dados](./media/database-security/nosql-database-security-responsibilities.png)

O diagrama anterior mostra cloud de alto nível de componentes de segurança, mas que itens precisa se preocupar sobre especificamente para a sua solução de base de dados? E como pode comparar soluções entre si?

Recomendamos a seguinte lista de verificação dos requisitos em que pretende comparar sistemas de base de dados:

- Segurança de rede e as definições da firewall
- Autenticação de utilizador e controles de usuário detalhada
- Capacidade para replicar dados globalmente para falhas regionais
- Capacidade de falhar de um centro de dados para outro
- Replicação de dados locais dentro de um centro de dados
- Cópias de segurança de dados automática
- Restauro de dados eliminados de cópias de segurança
- Proteger e isolar os dados confidenciais
- Monitorização de ataques
- Responder a ataques
- Capacidade de dados de georreplicação cerca de cumprir para restrições de governação de dados
- Proteção física dos servidores nos centros de dados protegidos
- Certificações

E embora possa parecer óbvio, recentes violações da [base de dados em larga escala lembram-nos](https://thehackernews.com/2017/01/mongodb-database-security.html) a importância simples, mas crítica, dos seguintes requisitos:

- Servidores remendados que são mantidos atualizados
- HTTPS por criptografia SSL/padrão
- Contas administrativas com palavras-passe fortes

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Como é que a Azure Cosmos DB segura a minha base de dados

Vamos examinar novamente a lista anterior - o número desses requisitos de segurança do Azure Cosmos DB fornece? Cada um.

Vamos examinar cada um em detalhes.

|Requisito de segurança|Abordagem de segurança do Azure Cosmos DB|
|---|---|
|Segurança da rede|Usando um firewall IP é a primeira camada de proteção para proteger a sua base de dados. O Azure Cosmos DB suporta a política de controlado por controlos de acesso baseado em IP para o suporte de firewall de entrada. Os controlos de acesso baseados em IP são semelhantes às regras de firewall utilizadas pelos sistemas tradicionais de base de dados, mas são expandidos de modo a que uma conta de base de dados Azure Cosmos só seja acessível a partir de um conjunto aprovado de máquinas ou serviços na nuvem. <br><br>O Azure Cosmos DB permite-lhe permitir um endereço IP específico (168.61.48.0), um intervalo IP (168.61.48.0/8) e combinações de IPs e intervalos. <br><br>Todos os pedidos provenientes de máquinas fora desta lista de permissões são bloqueados pelo Azure Cosmos DB. Pedidos de máquinas aprovadas e, em seguida, os serviços de cloud tem de concluir o processo de autenticação para obter o controlo de acesso aos recursos.<br><br>Saiba mais no suporte à [firewall Da Azure Cosmos DB.](firewall-support.md)|
|Autorização|O Azure Cosmos DB utiliza o código de autenticação de mensagens baseada em hash (HMAC) para autorização. <br><br>Cada solicitação é protegido por hash com a chave secreta de conta e o hash de com codificação base 64 subsequente é enviado com cada chamada para o Azure Cosmos DB. Para validar o pedido, o serviço do Azure Cosmos DB utiliza a chave secreta correto e as propriedades para gerar um hash e, em seguida, ele compara o valor no pedido. Se os dois valores corresponderem, a operação está autorizada com êxito e o processamento do pedido, caso contrário, há uma falha de autorização e a solicitação é rejeitada.<br><br>Pode utilizar uma [chave principal,](secure-access-to-data.md#master-keys)ou um símbolo de [recurso](secure-access-to-data.md#resource-tokens) que permite o acesso de grãos finos a um recurso como um documento.<br><br>Saiba mais em [Garantir o acesso aos recursos DB da Azure Cosmos.](secure-access-to-data.md)|
|Os utilizadores e permissões|Utilizando a chave principal para a conta, pode criar recursos de utilizador e recursos de permissão por base de dados. Um token de recurso está associado a uma permissão numa base de dados e determina se o utilizador tem acesso (ler, ler ou não aceder) a um recurso de aplicação na base de dados. Recursos de aplicativos incluem o contentor, documentos, anexos, procedimentos armazenados, acionadores e UDFs. O token de recursos, em seguida, é utilizado durante a autenticação para fornecer ou negar o acesso ao recurso.<br><br>Saiba mais em [Garantir o acesso aos recursos DB da Azure Cosmos.](secure-access-to-data.md)|
|Integração do Active Directory (RBAC)| Também pode fornecer ou restringir o acesso à conta Cosmos, base de dados, contentor e ofertas (entrada) utilizando o controlo de acesso (IAM) no portal Azure. IAM fornece controlo de acesso baseado em funções e integra-se com o Active Directory. Você pode usar incorporado em papéis ou papéis personalizados para indivíduos e grupos. Consulte o artigo de [integração do Diretório Ativo](role-based-access-control.md) para obter mais informações.|
|Replicação global|O Azure Cosmos DB oferece distribuição global chave na mão, que permite-lhe replicar os seus dados para qualquer um dos datacenters de nível mundial do Azure com o clique de um botão. Replicação global permite-lhe dimensioná-las globalmente e fornecer acesso de latência baixa aos seus dados em todo o mundo.<br><br>No contexto de segurança, os replicação global garante proteção de dados contra falhas regionais.<br><br>Saiba mais em [Distribuir dados globalmente](distribute-data-globally.md).|
|Ativações pós-falha regionais|Se tiver replicado seus dados em mais de um Datacenter, do Azure Cosmos DB passa automaticamente suas operações deve um Datacenter regional ficam offline. Pode criar uma lista prioritária de regiões de ativação pós-falha, as regiões em que os dados são replicados a utilizar. <br><br>Saiba mais em [Falhas Regionais em Azure Cosmos DB.](high-availability.md)|
|Replicação local|Mesmo dentro de um único centro de dados, o Azure Cosmos DB replica automaticamente dados para alta disponibilidade, dando-lhe a escolha dos níveis de [consistência.](consistency-levels.md) Esta replicação garante uma [disponibilidade](https://azure.microsoft.com/support/legal/sla/cosmos-db) de 99,99% para todas as contas unipessoais da região e todas as contas multi-regiões com consistência descontraída, e 99,999% lê disponibilidade em todas as contas de bases de dados multi-regiões.|
|Cópias de segurança online automáticas|As bases de dados da Azure Cosmos são regularmente armazenadas e armazenadas numa loja georedundant. <br><br>Saiba mais em [backup automático on-line e restaure com Azure Cosmos DB](online-backup-and-restore.md).|
|Dados de restauro eliminado|As cópias de segurança online automatizadas podem ser utilizadas para recuperar dados que pode ter acidentalmente eliminado até aproximadamente 30 dias após o evento. <br><br>Saiba mais em [backup automático on-line e restaure com Azure Cosmos DB](online-backup-and-restore.md)|
|Proteger e isolar os dados confidenciais|Todos os dados das regiões listadas em "O que há de novo"? está agora encriptado em repouso.<br><br>Os dados pessoais e outros dados confidenciais podem ser isolados para o contêiner específico e de leitura-gravação ou acesso só de leitura pode ser limitado a utilizadores específicos.|
|Monitor de ataques|Utilizando registos de registos de [registos de auditoria e de atividade,](logging.md)pode monitorizar a sua conta para uma atividade normal e anormal. Pode ver as operações que foram realizadas nos seus recursos, quem a iniciou a operação, aquando da ocorrência da operação, o estado da operação e, muito mais como o mostrado na captura de ecrã após esta tabela.|
|Responder a ataques|Depois de ter de contactar o suporte do Azure para comunicar um potencial ataque, um processo de resposta a incidentes passo 5 é inicializado. O objetivo do processo do passo 5 é restaurar o mais rapidamente possível operações de segurança de serviço normal e depois é detetado um problema e é iniciada uma investigação.<br><br>Saiba mais na [Resposta de Segurança do Microsoft Azure na Nuvem](https://aka.ms/securityresponsepaper).|
|Perímetro geográfico|O Azure Cosmos DB garante a governação de dados para regiões de soberania (por exemplo, Alemanha, China, gov (US)).|
|Instalações protegidas|Dados no Azure Cosmos DB são armazenados em SSDs nos centros de dados protegidos do Azure.<br><br>Saiba mais nos [datacenters globais](https://www.microsoft.com/en-us/cloud-platform/global-datacenters) da Microsoft|
|Encriptação de HTTPS/SSL/TLS|Todas as ligações ao suporte DO Azure Cosmos DB HTTPS. Azure Cosmos DB também suporta TLS 1.2.<br>É possível impor um servidor de servidor de versão TLS mínimo. Para isso, contacte [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).|
|Encriptação inativa|Todos os dados armazenados no Azure Cosmos DB são encriptados em descanso. Saiba mais na [encriptação Do DD Do Azure Cosmos em repouso](./database-encryption-at-rest.md)|
|Servidores corrigidas|Como uma base de dados gerida, o Azure Cosmos DB elimina a necessidade de gerenciar e aplicar um patch servidores, que tem feito para, automaticamente.|
|Contas administrativas com palavras-passe fortes|É difícil de acreditar, ainda precisamos de mencionar esse requisito, mas ao contrário de alguns dos nossos concorrentes, é impossível ter uma conta administrativa com nenhuma palavra-passe no Azure Cosmos DB.<br><br> Segurança através de SSL e HMAC autenticação baseada em segredo está embutida por predefinição.|
|Certificações de proteção de dados e segurança| Para a lista mais atualizada de certificações consulte o site de [conformidade azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) em geral, bem como o mais recente Documento de [Conformidade Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) com todas as certificações (pesquisa pela Cosmos). Para uma leitura mais focada confira o post de 25 de abril de 2018 [Azure #CosmosDB: Seguro, privado, conforme que inclui SOCS 1/2 Tipo 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High, entre muitos outros.

A imagem que se segue mostra como pode utilizar registos de registos de auditoria e de atividade para monitorizar a sua conta: registos de atividade ![para o Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre chaves principais e fichas de recursos, consulte [O acesso à Azure Cosmos DB](secure-access-to-data.md).

Para obter mais informações sobre a exploração madeireira de auditoria, consulte [a exploração de diagnóstico sinuosa de diagnóstico da Azure Cosmos DB](logging.md).

Para obter mais informações sobre as certificações da Microsoft, consulte [o Azure Trust Center](https://azure.microsoft.com/support/trust-center/).