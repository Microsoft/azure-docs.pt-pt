---
title: Segurança da base de dados - Azure Cosmos DB
description: Saiba como a Azure Cosmos DB fornece proteção de bases de dados e segurança de dados para os seus dados.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: mjbrown
ms.openlocfilehash: 19b4c8466e88159839ce1f43a5ba282b1bb3ec9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94636933"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Segurança no Azure Cosmos DB – descrição geral
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo discute as melhores práticas de segurança da base de dados e as principais funcionalidades disponibilizadas pelo Azure Cosmos DB para o ajudar a prevenir, detetar e responder a falhas de segurança da base de dados.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Novidades na segurança da Azure Cosmos DB

A encriptação em repouso está agora disponível para documentos e cópias de segurança armazenados em Azure Cosmos DB em todas as regiões do Azure. A encriptação em repouso é aplicada automaticamente para clientes novos e existentes nestas regiões. Não há necessidade de configurar nada; e obtém a mesma grande latência, produção, disponibilidade e funcionalidade como antes, com o benefício de saber que os seus dados são seguros e seguros com encriptação em repouso.

## <a name="how-do-i-secure-my-database"></a>Como posso proteger a minha base de dados

A segurança dos dados é uma responsabilidade partilhada entre si, o cliente e o seu fornecedor de bases de dados. Dependendo do fornecedor de bases de dados que escolher, a quantidade de responsabilidade que transporta pode variar. Se escolher uma solução no local, precisa de fornecer tudo, desde a proteção de ponto final até à segurança física do seu hardware - o que não é tarefa fácil. Se escolher um fornecedor de base de dados de nuvem PaaS, como a Azure Cosmos DB, a sua área de preocupação diminui consideravelmente. A imagem a seguir, emprestada do livro branco Da Microsoft,., mostra como a sua [responsabilidade](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) diminui com um fornecedor de PaaS como o Azure Cosmos DB.

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="Responsabilidades do fornecedor de clientes e de base de dados":::

O diagrama anterior mostra componentes de segurança em nuvem de alto nível, mas quais os itens com que precisa de se preocupar especificamente para a sua solução de base de dados? E como se podem comparar soluções umas com as outras?

Recomendamos a seguinte lista de requisitos para comparar sistemas de base de dados:

- Definições de segurança de rede e firewall
- Autenticação do utilizador e controlos de utilizador finos
- Capacidade de replicar dados globalmente para falhas regionais
- Capacidade de falhar de um centro de dados para outro
- Replicação de dados locais dentro de um centro de dados
- Backups automáticos de dados
- Restauração de dados eliminados de backups
- Proteger e isolar dados sensíveis
- Monitorização de ataques
- Respondendo a ataques
- Capacidade de geo-vedação de dados para aderir às restrições de governação de dados
- Proteção física dos servidores em centros de dados protegidos
- Certificações

E embora possa parecer óbvio, [recentes falhas na base de dados em larga escala recordam-nos](https://thehackernews.com/2017/01/mongodb-database-security.html) a importância simples, mas crítica, dos seguintes requisitos:

- Servidores remendados que são mantidos atualizados
- HTTPS por encriptação padrão/TLS
- Contas administrativas com senhas fortes

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Como é que o Azure Cosmos DB protege a minha base de dados

Vamos olhar para trás na lista anterior- quantos desses requisitos de segurança a Azure Cosmos DB fornece? Cada um deles.

Vamos investigar cada um em detalhe.

|Requisito de segurança|A abordagem de segurança de Azure Cosmos DB|
|---|---|
|Segurança da rede|A utilização de uma firewall IP é a primeira camada de proteção para proteger a sua base de dados. O Azure Cosmos DB suporta controlos de acesso baseados em IP orientados por políticas para suporte a firewall de entrada. Os controlos de acesso baseados em IP são semelhantes às regras de firewall utilizadas pelos sistemas de base de dados tradicionais, mas são expandidos de modo a que uma conta de base de dados Azure Cosmos só seja acessível a partir de um conjunto aprovado de máquinas ou serviços na nuvem. Saiba mais no artigo [de suporte ao firewall da Azure Cosmos.](how-to-configure-firewall.md)<br><br>O Azure Cosmos DB permite-lhe ativar um endereço IP específico (168.61.48.0), uma gama IP (168.61.48.0/8) e combinações de IPs e intervalos. <br><br>Todos os pedidos provenientes de máquinas fora desta lista permitida são bloqueados pela Azure Cosmos DB. Os pedidos de máquinas aprovadas e serviços de nuvem devem então completar o processo de autenticação para ser dado controlo de acesso aos recursos.<br><br> Pode utilizar [tags de serviço de rede virtuais](../virtual-network/service-tags-overview.md) para alcançar o isolamento da rede e proteger os seus recursos DB Azure Cosmos da Internet geral. Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da etiqueta de serviço (por exemplo, AzureCosmosDB) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente.|
|Autorização|A Azure Cosmos DB utiliza código de autenticação de mensagens baseadas em haxixe (HMAC) para autorização. <br><br>Cada pedido é hashed usando a chave de conta secreta, e o haxixe codificado subsequente base-64 é enviado com cada chamada para Azure Cosmos DB. Para validar o pedido, o serviço DB Azure Cosmos utiliza a chave secreta e propriedades corretas para gerar um haxixe, comparando o valor com o do pedido. Se os dois valores coincidirem, a operação é autorizada com sucesso e o pedido é processado, caso contrário existe uma falha de autorização e o pedido é rejeitado.<br><br>Pode utilizar uma [chave primária,](#primary-keys)ou um [token](secure-access-to-data.md#resource-tokens) de recursos que permite o acesso a um recurso de grãos finos, como um documento.<br><br>Saiba mais sobre [a garantia de acesso aos recursos DB da Azure Cosmos](secure-access-to-data.md).|
|Utilizadores e permissões|Utilizando a chave principal para a conta, pode criar recursos de utilizador e recursos de permissão por base de dados. Um token de recurso está associado a uma permissão numa base de dados e determina se o utilizador tem acesso (read-write, read-only ou sem acesso) a um recurso de aplicação na base de dados. Os recursos de aplicação incluem contentores, documentos, anexos, procedimentos armazenados, gatilhos e UDFs. O token de recursos é então utilizado durante a autenticação para fornecer ou negar o acesso ao recurso.<br><br>Saiba mais sobre [a garantia de acesso aos recursos DB da Azure Cosmos](secure-access-to-data.md).|
|Integração de diretórios ativos (Azure RBAC)| Também pode fornecer ou restringir o acesso à conta Cosmos, base de dados, contentor e ofertas (produção) utilizando o controlo de acesso (IAM) no portal Azure. O IAM fornece controlo de acesso baseado em funções e integra-se com o Ative Directory. Você pode usar papéis ou papéis personalizados para indivíduos e grupos. Consulte o artigo [de integração do Ative Directory](role-based-access-control.md) para obter mais informações.|
|Replicação global|O Azure Cosmos DB oferece uma distribuição global chave na mão, que permite replicar os seus dados em qualquer um dos centros de dados mundiais da Azure com o clique de um botão. A replicação global permite escalar globalmente e fornecer acesso de baixa latência aos seus dados em todo o mundo.<br><br>No contexto da segurança, a replicação global garante a proteção de dados contra falhas regionais.<br><br>Saiba mais em [Distribuir dados globalmente](distribute-data-globally.md).|
|Ativações pós-falha regionais|Se tiver replicado os seus dados em mais de um centro de dados, a Azure Cosmos DB passa automaticamente sobre as suas operações caso um centro de dados regional fique offline. Pode criar uma lista prioritária de regiões de failover utilizando as regiões em que os seus dados são replicados. <br><br>Saiba mais em [Falhas Regionais em Azure Cosmos DB](high-availability.md).|
|Replicação local|Mesmo dentro de um único centro de dados, a Azure Cosmos DB replica automaticamente dados para uma elevada disponibilidade, dando-lhe a escolha dos níveis de [consistência.](consistency-levels.md) Esta replicação garante uma disponibilidade de 99,99% [de SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) para todas as contas de região única e todas as contas multi-regiões com consistência descontraída, e 99,999% lêem disponibilidade em todas as contas de base de dados multi-regiões.|
|Backups on-line automatizados|As bases de dados da Azure Cosmos são armazenadas regularmente e armazenadas numa loja geodes redundante. <br><br>Saiba mais em [backup online automático e restaure com a Azure Cosmos DB](online-backup-and-restore.md).|
|Restaurar dados eliminados|As cópias de segurança on-line automatizadas podem ser usadas para recuperar dados que pode ter acidentalmente apagado até ~30 dias após o evento. <br><br>Saiba mais em [backup online automático e restaure com a Azure Cosmos DB](online-backup-and-restore.md)|
|Proteger e isolar dados sensíveis|Todos os dados nas regiões listadas em Quais as novidades? está agora encriptado em repouso.<br><br>Os dados pessoais e outros dados confidenciais podem ser isolados a recipientes específicos e ler-escrever, ou o acesso apenas de leitura pode ser limitado a utilizadores específicos.|
|Monitor para ataques|Ao utilizar [registos de registos de auditoria e de atividade,](./monitor-cosmos-db.md)pode monitorizar a sua conta para uma atividade normal e anormal. Pode ver que operações foram realizadas nos seus recursos, que iniciaram a operação, quando ocorreu a operação, o estado da operação, e muito mais como mostrado na imagem seguinte a esta tabela.|
|Responder a ataques|Uma vez contactado o apoio do Azure para relatar um possível ataque, um processo de resposta a incidentes de 5 etapas é iniciado. O objetivo do processo de 5 etapas é restaurar a segurança e operações normais do serviço o mais rápido possível após a deteção de um problema e uma investigação é iniciada.<br><br>Saiba mais na [Microsoft Azure Security Response na Cloud](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Geo-esgrima|AZure Cosmos DB assegura a governação de dados para regiões soberanas (por exemplo, Alemanha, China, EUA Gov).|
|Instalações protegidas|Os dados em Azure Cosmos DB são armazenados em SSDs nos centros de dados protegidos do Azure.<br><br>Saiba mais nos [datacenters globais da Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Encriptação HTTPS/SSL/TLS|Todas as ligações com o suporte DB da Azure Cosmos HTTPS. AZure Cosmos DB também suporta TLS 1.2.<br>É possível impor um mínimo de versão TLS lado do servidor. Para tal, [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) contacte.|
|Encriptação de dados inativos|Todos os dados armazenados no Azure Cosmos DB estão encriptados em repouso. Saiba mais em [Azure Cosmos DB encriptação em repouso](./database-encryption-at-rest.md)|
|Servidores remendados|Como uma base de dados gerida, a Azure Cosmos DB elimina a necessidade de gerir e corrigir servidores, o que é feito por si, automaticamente.|
|Contas administrativas com senhas fortes|É difícil acreditar que precisamos mesmo de mencionar este requisito, mas ao contrário de alguns dos nossos concorrentes, é impossível ter uma conta administrativa sem senha no Azure Cosmos DB.<br><br> A segurança através da autenticação baseada em segredo TLS e HMAC é cozida por padrão.|
|Certificações de segurança e proteção de dados| Para obter a lista de certificações mais atualizada, consulte o site geral de conformidade do [Azure,](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) bem como o mais recente [Documento de Conformidade Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) com todas as certificações (pesquisa por Cosmos). Para uma leitura mais focada, confira o post de 25 de abril de 2018 [Azure #CosmosDB: Seguro, privado, conforme que inclui SOCS 1/2 Tipo 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High, entre muitos outros.

A imagem que se segue mostra como pode utilizar registos de registos de auditoria e de atividade para monitorizar a sua conta: :::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="Registos de atividade para Azure Cosmos DB":::

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Chaves primárias

As chaves primárias fornecem acesso a todos os recursos administrativos da conta de base de dados. Chaves primárias:

- Fornecer acesso a contas, bases de dados, utilizadores e permissões. 
- Não é possível utilizar acesso granular a contentores e documentos.
- São criados durante a criação de uma conta.
- Pode ser regenerado a qualquer momento.

Cada conta é composta por duas chaves primárias: uma chave primária e uma chave secundária. O objetivo das chaves duplas é que possa regenerar, ou rolar chaves, proporcionando acesso contínuo à sua conta e dados.

Além das duas chaves primárias da conta cosmos DB, existem duas chaves só de leitura. Estas chaves só de leitura permitem a leitura das operações na conta. As teclas só de leitura não fornecem acesso aos recursos de permissões de leitura.

As chaves primárias, secundárias, lidas apenas e as teclas primárias de leitura podem ser recuperadas e regeneradas usando o portal Azure. Para obter instruções, consulte [ver, copiar e regenerar as teclas de acesso](manage-with-cli.md#regenerate-account-key).

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controlo de acesso (IAM) no portal Azure - demonstrando segurança na base de dados NoSQL":::

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre chaves primárias e fichas de recursos, consulte [garantir o acesso aos dados DB da Azure Cosmos](secure-access-to-data.md).

Para obter mais informações sobre o registo de auditoria, consulte [a Azure Cosmos DB para fazer a sessão de diagnóstico](./monitor-cosmos-db.md).

Para obter mais informações sobre as certificações da Microsoft, consulte [o Azure Trust Center](https://azure.microsoft.com/support/trust-center/).