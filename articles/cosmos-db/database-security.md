---
title: Segurança da base de dados - Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece proteção de bases de dados e segurança de dados para os seus dados.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mjbrown
ms.openlocfilehash: 8a0ef4c5abcd4c4137a878adf6a7fff9e80c2764
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273516"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Segurança no Azure Cosmos DB – descrição geral

Este artigo discute as melhores práticas de segurança da base de dados e as principais funcionalidades disponibilizadas pelo Azure Cosmos DB para o ajudar a prevenir, detetar e responder a falhas de segurança da base de dados.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Novidades na segurança da Azure Cosmos DB

A encriptação em repouso está agora disponível para documentos e backups armazenados em Azure Cosmos DB em todas as regiões de Azure. A encriptação em repouso é aplicada automaticamente para clientes novos e existentes nestas regiões. Não há necessidade de configurar nada; e obtém a mesma grande latência, entrada, disponibilidade e funcionalidade como antes, com o benefício de saber que os seus dados são seguros e seguros com encriptação em repouso.

## <a name="how-do-i-secure-my-database"></a>Como posso garantir a minha base de dados

A segurança de dados é uma responsabilidade partilhada entre si, o cliente e o seu fornecedor de base de dados. Dependendo do fornecedor de base de dados que escolher, a quantidade de responsabilidade que tem pode variar. Se escolher uma solução no local, precisa de fornecer tudo, desde a proteção de ponta até à segurança física do seu hardware - o que não é tarefa fácil. Se escolher um fornecedor de bases de dados paaS em nuvem, como o Azure Cosmos DB, a sua área de preocupação diminui consideravelmente. A imagem seguinte, emprestada do livro branco Shared [Responsibilities for Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) da Microsoft, mostra como a sua responsabilidade diminui com um fornecedor paaS como o Azure Cosmos DB.

![Responsabilidades do fornecedor de clientes e bases de dados](./media/database-security/nosql-database-security-responsibilities.png)

O diagrama anterior mostra componentes de segurança em nuvem de alto nível, mas com que itens precisa de se preocupar especificamente com a sua solução de base de dados? E como podem comparar soluções entre si?

Recomendamos a seguinte lista de requisitos para comparar sistemas de base de dados:

- Definições de segurança da rede e firewall
- Autenticação do utilizador e controlos finos dos utilizadores
- Capacidade de replicar dados globalmente para falhas regionais
- Capacidade de falhar de um centro de dados para outro
- Replicação de dados locais dentro de um centro de dados
- Backups automáticos de dados
- Restauração de dados eliminados a partir de cópias de segurança
- Proteger e isolar dados sensíveis
- Monitorização de ataques
- Responder a ataques
- Capacidade de geo-vedar dados para aderir às restrições de governança de dados
- Proteção física de servidores em centros de dados protegidos
- Certificações

E embora possa parecer óbvio, recentes violações da [base de dados em larga escala lembram-nos](https://thehackernews.com/2017/01/mongodb-database-security.html) a importância simples, mas crítica, dos seguintes requisitos:

- Servidores remendados que são mantidos atualizados
- HTTPS por encriptação padrão/TLS
- Contas administrativas com senhas fortes

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Como é que a Azure Cosmos DB segura a minha base de dados

Vejamos a lista anterior - quantos desses requisitos de segurança a Azure Cosmos DB fornece? Cada um deles.

Vamos investigar cada um em detalhe.

|Requisito de segurança|A abordagem de segurança da Azure Cosmos DB|
|---|---|
|Segurança da rede|A utilização de uma firewall IP é a primeira camada de proteção para proteger a sua base de dados. A Azure Cosmos DB apoia controlos de acesso baseados em IP para suporte de firewall de entrada. Os controlos de acesso baseados em IP são semelhantes às regras de firewall utilizadas pelos sistemas tradicionais de base de dados, mas são expandidos de modo a que uma conta de base de dados Azure Cosmos só seja acessível a partir de um conjunto aprovado de máquinas ou serviços na nuvem. Saiba mais em [Azure Cosmos DB artigo](firewall-support.md) de suporte à firewall.<br><br>O Azure Cosmos DB permite-lhe ativar um endereço IP específico (168.61.48.0), uma gama IP (168.61.48.0/8) e combinações de IPs e gamas. <br><br>Todos os pedidos provenientes de máquinas fora desta lista permitida são bloqueados pela Azure Cosmos DB. Os pedidos de máquinas aprovadas e serviços em nuvem devem então completar o processo de autenticação a ter acesso aos recursos.<br><br> Você pode usar [etiquetas](../virtual-network/service-tags-overview.md) de serviço de rede virtual para alcançar o isolamento da rede e proteger os seus recursos Azure Cosmos DB da Internet geral. Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Especificando o nome da etiqueta de serviço (por exemplo, AzureCosmosDB) no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente.|
|Autorização|A Azure Cosmos DB utiliza código de autenticação de mensagens baseada selada em hash (HMAC) para autorização. <br><br>Cada pedido é hashed usando a chave da conta secreta, e o hash codificado base-64 subsequente é enviado com cada chamada para Azure Cosmos DB. Para validar o pedido, o serviço Azure Cosmos DB utiliza a chave secreta correta e propriedades para gerar um hash, então compara o valor com o do pedido. Se os dois valores coincidirem, a operação é autorizada com sucesso e o pedido é processado, caso contrário existe uma falha de autorização e o pedido é rejeitado.<br><br>Pode utilizar uma [chave principal,](secure-access-to-data.md#master-keys)ou um símbolo de [recurso](secure-access-to-data.md#resource-tokens) que permite o acesso de grãos finos a um recurso como um documento.<br><br>Saiba mais em [Garantir o acesso aos recursos DB da Azure Cosmos.](secure-access-to-data.md)|
|Utilizadores e permissões|Utilizando a chave principal para a conta, pode criar recursos de utilizador e recursos de permissão por base de dados. Um token de recurso está associado a uma permissão numa base de dados e determina se o utilizador tem acesso (ler, ler ou não aceder) a um recurso de aplicação na base de dados. Os recursos de aplicação incluem contentores, documentos, anexos, procedimentos armazenados, gatilhos e UDFs. O token de recurso é então utilizado durante a autenticação para fornecer ou negar o acesso ao recurso.<br><br>Saiba mais em [Garantir o acesso aos recursos DB da Azure Cosmos.](secure-access-to-data.md)|
|Integração ativa do diretório (RBAC)| Também pode fornecer ou restringir o acesso à conta Cosmos, base de dados, contentor e ofertas (entrada) utilizando o controlo de acesso (IAM) no portal Azure. A IAM fornece controlo de acesso baseado em papéis e integra-se com o Ative Directory. Você pode usar incorporado em papéis ou papéis personalizados para indivíduos e grupos. Consulte o artigo de [integração do Diretório Ativo](role-based-access-control.md) para obter mais informações.|
|Replicação global|O Azure Cosmos DB oferece uma distribuição global chave na mão, o que lhe permite replicar os seus dados a qualquer um dos centros de dados mundiais do Azure com o clique de um botão. A replicação global permite escalar globalmente e fornecer acesso de baixa latência aos seus dados em todo o mundo.<br><br>No contexto da segurança, a replicação global garante a proteção de dados contra falhas regionais.<br><br>Saiba mais em [Distribuir dados globalmente](distribute-data-globally.md).|
|Ativações pós-falha regionais|Se tiver replicado os seus dados em mais de um centro de dados, o Azure Cosmos DB passa automaticamente sobre as suas operações caso um centro de dados regional fique offline. Pode criar uma lista prioritária de regiões de failover utilizando as regiões em que os seus dados são replicados. <br><br>Saiba mais em [Falhas Regionais em Azure Cosmos DB.](high-availability.md)|
|Replicação local|Mesmo dentro de um único centro de dados, o Azure Cosmos DB replica automaticamente dados para alta disponibilidade, dando-lhe a escolha dos níveis de [consistência.](consistency-levels.md) Esta replicação garante uma [disponibilidade](https://azure.microsoft.com/support/legal/sla/cosmos-db) de 99,99% para todas as contas unipessoais da região e todas as contas multi-regiões com consistência descontraída, e 99,999% lê disponibilidade em todas as contas de bases de dados multi-regiões.|
|Backups online automatizados|As bases de dados da Azure Cosmos são regularmente armazenadas e armazenadas numa loja georedundant. <br><br>Saiba mais em [backup automático on-line e restaure com Azure Cosmos DB](online-backup-and-restore.md).|
|Restaurar os dados eliminados|As cópias de segurança online automatizadas podem ser utilizadas para recuperar dados que pode ter apagado acidentalmente até ~30 dias após o evento. <br><br>Saiba mais em [backup automático on-line e restaure com Azure Cosmos DB](online-backup-and-restore.md)|
|Proteger e isolar dados sensíveis|Todos os dados das regiões listadas em "O que há de novo"? está agora encriptado em repouso.<br><br>Os dados pessoais e outros dados confidenciais podem ser isolados em recipientes específicos e ler-escrita, ou o acesso apenas a leitura pode ser limitado a utilizadores específicos.|
|Monitor para ataques|Utilizando registos de registos de [registos de auditoria e de atividade,](logging.md)pode monitorizar a sua conta para uma atividade normal e anormal. Pode ver que operações foram realizadas nos seus recursos, quem iniciou a operação, quando ocorreu a operação, o estado da operação, e muito mais como mostrado na imagem que se segue a esta tabela.|
|Responder a ataques|Uma vez contactado o apoio do Azure para relatar um possível ataque, um processo de resposta a incidentes de 5 passos é iniciado. O objetivo do processo de 5 etapas é restaurar a segurança e as operações normais de serviço o mais rapidamente possível após a deteção de um problema e iniciar uma investigação.<br><br>Saiba mais na [Resposta de Segurança do Microsoft Azure na Nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Geo-esgrima|A Azure Cosmos DB garante a governação de dados para as regiões soberanas (por exemplo, Alemanha, China, EUA Gov).|
|Instalações protegidas|Os dados em Azure Cosmos DB são armazenados em SSDs nos centros de dados protegidos do Azure.<br><br>Saiba mais nos [datacenters globais](https://www.microsoft.com/en-us/cloud-platform/global-datacenters) da Microsoft|
|Encriptação HTTPS/SSL/TLS|Todas as ligações ao suporte DO Azure Cosmos DB HTTPS. Azure Cosmos DB também suporta TLS 1.2.<br>É possível impor um servidor de servidor de versão TLS mínimo. Para isso, por [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)favor contacte.|
|Encriptação inativa|Todos os dados armazenados no Azure Cosmos DB estão encriptados em repouso. Saiba mais na [encriptação Do DD Do Azure Cosmos em repouso](./database-encryption-at-rest.md)|
|Servidores remendados|Como base de dados gerida, o Azure Cosmos DB elimina a necessidade de gerir e corrigir servidores, o que é feito para si, automaticamente.|
|Contas administrativas com senhas fortes|É difícil acreditar que precisamos mencionar este requisito, mas ao contrário de alguns dos nossos concorrentes, é impossível ter uma conta administrativa sem senha no Azure Cosmos DB.<br><br> A segurança através de TLS e hmac de autenticação baseada em segredo é assada por padrão.|
|Certificações de segurança e proteção de dados| Para a lista mais atualizada de certificações consulte o site de [conformidade azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) em geral, bem como o mais recente Documento de [Conformidade Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) com todas as certificações (pesquisa pela Cosmos). Para uma leitura mais focada confira o post de 25 de abril de 2018 [Azure #CosmosDB: Seguro, privado, conforme que inclui SOCS 1/2 Tipo 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High, entre muitos outros.

A imagem que se segue mostra como pode utilizar registos de registos de auditoria e de atividade para monitorizar a sua conta: ![Registos de atividade para Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre chaves principais e fichas de recursos, consulte [O acesso à Azure Cosmos DB](secure-access-to-data.md).

Para obter mais informações sobre a exploração madeireira de auditoria, consulte [a exploração de diagnóstico sinuosa de diagnóstico da Azure Cosmos DB](logging.md).

Para obter mais informações sobre as certificações da Microsoft, consulte [o Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
