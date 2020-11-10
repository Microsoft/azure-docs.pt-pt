---
title: Modelos de encriptação de dados no Microsoft Azure
description: Este artigo fornece uma visão geral dos modelos de encriptação de dados no Microsoft Azure.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: a00dc5beb2bde02f71b40f6eb374502136c37c67
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410492"
---
# <a name="data-encryption-models"></a>Modelos de encriptação de dados

Uma compreensão dos vários modelos de encriptação e dos seus prós e contras é essencial para entender como os vários fornecedores de recursos em Azure implementam encriptação em Rest. Estas definições são partilhadas em todos os fornecedores de recursos em Azure para garantir a linguagem comum e a taxonomia.

Existem três cenários para encriptação do lado do servidor:

- Encriptação do lado do servidor usando chaves de Service-Managed
  - Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação
  - Microsoft gere as chaves
  - Funcionalidade de nuvem completa

- Encriptação do lado do servidor usando chaves geridas pelo cliente no Cofre da Chave Azure
  - Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação
  - O cliente controla as chaves através do Cofre da Chave Azure
  - Funcionalidade de nuvem completa

- Encriptação do lado do servidor utilizando chaves geridas pelo cliente em hardware controlado pelo cliente
  - Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação
  - Chaves de controlo do cliente no hardware controlado pelo cliente
  - Funcionalidade de nuvem completa

Os modelos de encriptação do lado do servidor referem-se à encriptação que é executada pelo serviço Azure. Neste modelo, o Fornecedor de Recursos executa as operações de encriptação e desencriptação. Por exemplo, o Azure Storage pode receber dados em operações de texto simples e executar a encriptação e desencriptação internamente. O Fornecedor de Recursos poderá utilizar chaves de encriptação que são geridas pela Microsoft ou pelo cliente, dependendo da configuração fornecida.

![Servidor](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Cada uma das encriptações do lado do servidor nos modelos de repouso implica características distintas da gestão das chaves. Isto inclui onde e como as chaves de encriptação são criadas, e armazenadas, bem como os modelos de acesso e os principais procedimentos de rotação.  

Para encriptação do lado do cliente, considere o seguinte:

- Serviços Azure não podem ver dados desencriptados
- Os clientes gerem e armazenam chaves no local (ou noutras lojas seguras). As chaves não estão disponíveis para os serviços da Azure
- Funcionalidade de nuvem reduzida

Os modelos de encriptação suportados no Azure dividem-se em dois grupos principais: "Encriptação do Cliente" e "Encriptação do lado do servidor", como mencionado anteriormente. Independentemente da encriptação no modelo de repouso utilizado, os serviços Azure recomendam sempre a utilização de um transporte seguro, como TLS ou HTTPS. Por conseguinte, a encriptação nos transportes deve ser abordada pelo protocolo de transporte e não deve ser um fator importante para determinar qual a encriptação no modelo de repouso a utilizar.

## <a name="client-encryption-model"></a>Modelo de encriptação do cliente

O modelo de encriptação do cliente refere-se à encriptação que é realizada fora do Fornecedor de Recursos ou Azure pelo serviço ou aplicação de chamada. A encriptação pode ser realizada pela aplicação de serviço em Azure, ou por uma aplicação em execução no centro de dados do cliente. Em qualquer dos casos, ao alavancar este modelo de encriptação, o Fornecedor de Recursos Azure recebe uma bolha encriptada de dados sem a capacidade de desencriptar os dados de qualquer forma ou ter acesso às chaves de encriptação. Neste modelo, a gestão chave é feita pelo serviço de chamada/aplicação e é opaca ao serviço Azure.

![Cliente](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Encriptação do lado do servidor usando chaves geridas pelo serviço

Para muitos clientes, o requisito essencial é garantir que os dados são encriptados sempre que estão em repouso. A encriptação do lado do servidor utilizando chaves geridas pelo serviço permite que os clientes marquem o recurso específico (Conta de Armazenamento, DB SQL, etc.) para encriptação e deixando todos os aspetos de gestão chave, tais como emissão de chaves, rotação e backup para a Microsoft. A maioria dos serviços Azure que suportam a encriptação em repouso normalmente suportam este modelo de descarregamento da gestão das chaves de encriptação para Azure. O fornecedor de recursos Azure cria as chaves, coloca-as em armazenamento seguro e recupera-as quando necessário. Isto significa que o serviço tem acesso total às chaves e o serviço tem controlo total sobre a gestão do ciclo de vida credencial.

![gerido](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

A encriptação do lado do servidor utilizando as teclas geridas pelo serviço resolve-se rapidamente a necessidade de ter encriptação em repouso com sobrecarga baixa para o cliente. Quando disponível, um cliente normalmente abre o portal Azure para o fornecedor de subscrição e recursos alvo e verifica uma caixa indicando, eles gostariam que os dados fossem encriptados. Em alguma encriptação do lado do servidor de Gestores de Recursos com teclas geridas pelo serviço está por defeito.

A encriptação do lado do servidor com as teclas geridas pela Microsoft implica que o serviço tem acesso total à loja e gerir as chaves. Embora alguns clientes possam querer gerir as chaves porque sentem que ganham maior segurança, o custo e o risco associados a uma solução de armazenamento de chaves personalizada devem ser considerados na avaliação deste modelo. Em muitos casos, uma organização pode determinar que as restrições de recursos ou riscos de uma solução no local podem ser maiores do que o risco de gestão em nuvem da encriptação em chaves de repouso.  No entanto, este modelo pode não ser suficiente para as organizações que têm requisitos para controlar a criação ou ciclo de vida das chaves de encriptação ou para ter pessoal diferente a gerir as chaves de encriptação de um serviço do que aqueles que gerem o serviço (isto é, a segregação da gestão chave do modelo de gestão geral para o serviço).

### <a name="key-access"></a>Acesso à chave

Quando a encriptação do lado do servidor com teclas geridas pelo serviço é utilizada, a criação, armazenamento e acesso ao serviço são geridos pelo serviço. Normalmente, os fornecedores de recursos Azure fundacionais armazenarão as Chaves de Encriptação de Dados numa loja que está próxima dos dados e rapidamente disponível e acessível enquanto as Chaves de Encriptação chave são armazenadas numa loja interna segura.

**Vantagens**

- Configuração simples
- Microsoft gere rotação chave, backup e redundância
- O cliente não tem o custo associado à implementação ou o risco de um esquema de gestão de chaves personalizado.

**Desvantagens**

- Sem controlo do cliente sobre as chaves de encriptação (especificação chave, ciclo de vida, revogação, etc.)
- Sem capacidade de segregar a gestão chave do modelo de gestão global para o serviço

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação do lado do servidor usando chaves geridas pelo cliente no Cofre da Chave Azure

Para cenários em que o requisito é encriptar os dados em repouso e controlar as chaves de encriptação, os clientes podem usar encriptação do lado do servidor utilizando chaves geridas pelo cliente no Cofre de Chaves. Alguns serviços podem armazenar apenas a chave de encriptação raiz no Cofre de Chaves Azure e armazenar a Chave de Encriptação de Dados encriptada numa localização interna mais próxima dos dados. Nesse cenário, os clientes podem trazer as suas próprias chaves para o Key Vault (BYOK – Bring Your Own Key), ou gerar novas, e usá-las para encriptar os recursos desejados. Enquanto o Fornecedor de Recursos executa as operações de encriptação e desencriptação, utiliza a chave de encriptação configurada como chave de raiz para todas as operações de encriptação.

Perda das chaves de encriptação significa perda de dados. Por esta razão, as chaves não devem ser apagadas. As teclas devem ser apoiadas sempre que criadas ou giradas. [O Soft-Delete](../../key-vault/general/soft-delete-overview.md) deve ser ativado em qualquer chave de encriptação do cofre. Em vez de eliminar uma chave, o conjunto pode ser falso ou fixado a data de validade.

### <a name="key-access"></a>Acesso à Chave

O modelo de encriptação do lado do servidor com chaves geridas pelo cliente no Cofre da Chave Azure envolve o serviço que acede às chaves para encriptar e desencriptar conforme necessário. A encriptação nas chaves de repouso é tornada acessível a um serviço através de uma política de controlo de acesso. Esta política concede ao serviço acesso à identidade para receber a chave. Um serviço Azure em execução em nome de uma subscrição associada pode ser configurado com uma identidade nessa subscrição. O serviço pode realizar a autenticação do Azure Ative Directory e receber um token de autenticação identificando-se como esse serviço agindo em nome da subscrição. Esse símbolo pode então ser apresentado ao Key Vault para obter uma chave a que foi dado acesso.

Para operações que utilizem chaves de encriptação, pode ser concedido um acesso a qualquer uma das seguintes operações: desencriptação, encriptação, desembrulharKey, wrapKey, verificar, assinar, obter, lista, atualizar, criar, importar, eliminar, backup e restaurar.

Para obter uma chave para utilização na encriptação ou desencriptação de dados em repouso, a identidade do serviço que a instância de serviço do Gestor de Recursos irá executar como deve ter UnwrapKey (para obter a chave para a desencriptação) e WrapKey (para inserir uma chave no cofre chave ao criar uma nova chave).

>[!NOTE]
>Para obter mais detalhes sobre a autorização do Key Vault consulte a página do cofre da chave na documentação do [Cofre da Chave Azure](../../key-vault/general/secure-your-key-vault.md).

**Vantagens**

- Controlo total sobre as teclas utilizadas – as chaves de encriptação são geridas no Cofre chave do cliente sob o controlo do cliente.
- Capacidade de encriptar vários serviços a um mestre
- Pode segregar a gestão chave do modelo de gestão global para o serviço
- Pode definir serviço e localização chave em todas as regiões

**Desvantagens**

- O cliente tem total responsabilidade pela gestão de acessos chave
- O cliente tem total responsabilidade pela gestão chave do ciclo de vida
- Configuração adicional & configuração sobrecarga

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Encriptação do lado do servidor utilizando chaves geridas pelo cliente em hardware controlado pelo cliente

Alguns serviços Azure permitem o modelo de gestão de chaves host Your Own Key (HYOK). Este modo de gestão é útil em cenários onde existe a necessidade de encriptar os dados em repouso e gerir as chaves num repositório proprietário fora do controlo da Microsoft. Neste modelo, o serviço deve recuperar a chave de um site externo. As garantias de desempenho e disponibilidade são impactadas e a configuração é mais complexa. Além disso, uma vez que o serviço tem acesso ao DEK durante as operações de encriptação e desencriptação, as garantias de segurança globais deste modelo são semelhantes às quando as chaves são geridas pelo cliente no Azure Key Vault.  Como resultado, este modelo não é adequado para a maioria das organizações, a menos que tenham requisitos específicos de gestão. Devido a estas limitações, a maioria dos serviços Azure não suporta encriptação do lado do servidor usando chaves geridas pelo servidor em hardware controlado pelo cliente.

### <a name="key-access"></a>Acesso à Chave

Quando a encriptação do lado do servidor utilizando chaves geridas pelo serviço no hardware controlado pelo cliente é utilizada, as teclas são mantidas num sistema configurado pelo cliente. Os serviços Azure que suportam este modelo fornecem um meio de estabelecer uma ligação segura a uma loja de chaves fornecida pelo cliente.

**Vantagens**

- Controlo total sobre a chave raiz utilizada – as chaves de encriptação são geridas por um cliente fornecido
- Capacidade de encriptar vários serviços a um mestre
- Pode segregar a gestão chave do modelo de gestão global para o serviço
- Pode definir serviço e localização chave em todas as regiões

**Desvantagens**

- Total responsabilidade pelo armazenamento, segurança, desempenho e disponibilidade
- Responsabilidade total pela gestão de acessos chave
- Responsabilidade total pela gestão chave do ciclo de vida
- Configuração, configuração e custos de manutenção em curso
- Aumento da dependência da disponibilidade de rede entre o datacenter do cliente e os datacenters da Azure.

## <a name="supporting-services"></a>Serviços de apoio
Os serviços Azure que suportam cada modelo de encriptação:

| Produto, Recurso ou Serviço | Server-Side usando a chave Service-Managed   | Server-Side usando a chave Customer-Managed | Client-Side usando a chave Client-Managed  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **IA e Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Yes                | Yes                | -                  |
| Serviços Cognitivos do Azure         | Yes                | Yes                | -                  |
| Azure Machine Learning           | Yes                | Yes                | -                  |
| Azure Machine Learning Studio (clássico) | Yes         | Pré-visualização, RSA 2048-bit | -               |
| Content Moderator                | Yes                | Yes                | -                  |
| Face                             | Yes                | Yes                | -                  |
| Compreensão de Idiomas           | Yes                | Yes                | -                  |
| Personalizador                     | Yes                | Yes                | -                  |
| Criador de FAQ                        | Yes                | Yes                | -                  |
| Serviços de Voz                  | Yes                | Yes                | -                  |
| Tradução de Texto                  | Yes                | Yes                | -                  |
| Power BI                         | Yes                | Sim, RSA 4096-bit  | -                  |
| **Análise**                    |                    |                    |                    |
| Azure Stream Analytics           | Yes                | N/D\*              | -                  |
| Hubs de Eventos                       | Yes                | Yes                | -                  |
| Funções                        | Yes                | Yes                | -                  |
| Azure Analysis Services          | Sim                | -                  | -                  |
| Catálogo de Dados do Azure               | Yes                | -                  | -                  |
| Azure HDInsight                  | Yes                | Todos                | -                  |
| Insights de aplicação do monitor Azure | Yes                | Yes                | -                  |
| Azure Monitor Log Analytics      | Yes                | Yes                | -                  |
| Azure Data Explorer              | Yes                | Yes                | -                  |
| Azure Data Factory               | Yes                | Yes                | -                  |
| Azure Data Lake Store            | Sim                | Sim, RSA 2048-bit  | -                  |
| **Contentores**                   |                    |                    |                    |
| Azure Kubernetes Service         | Yes                | Yes                | -                  |
| Container Instances              | Yes                | Yes                | -                  |
| Container Registry               | Yes                | Yes                | -                  |
| **Computação**                      |                    |                    |                    |
| Máquinas Virtuais                 | Yes                | Yes                | -                  |
| Conjunto de escala de máquina virtual        | Yes                | Yes                | -                  |
| SAP HANA                         | Yes                | Yes                | -                  |
| Serviço de Aplicações                      | Yes                | Sim, é o seu\*\*            | -                  |
| Automatização                       | Yes                | Sim, é o seu\*\*            | -                  |
| Funções do Azure                  | Yes                | Sim, é o seu\*\*            | -                  |
| Portal do Azure                     | Yes                | Sim, é o seu\*\*            | -                  |
| Logic Apps                       | Yes                | Yes                | -                  |
| Aplicações geridas pelo Azure       | Yes                | Sim, é o seu\*\*            | -                  |
| Service Bus                      | Yes                | Yes                | -                  |
| Recuperação de sites                    | Yes                | Yes                | -                  |
| **Bases de dados**                    |                    |                    |                    |
| SQL Server nas Máquinas Virtuais   | Yes                | Yes                | Yes                |
| Base de Dados SQL do Azure               | Yes                | Sim, RSA 3072-bit  | Yes                |
| Base de Dados Azure SQL para MariaDB   | Yes                | -                  | -                  |
| Base de Dados Azure SQL para MySQL     | Yes                | Yes                | -                  |
| Base de Dados Azure SQL para PostgreSQL | Yes               | Yes                | -                  |
| Azure Synapse Analytics          | Yes                | Sim, RSA 3072-bit  | -                  |
| SQL Server Stretch Database      | Yes                | Sim, RSA 3072-bit  | Yes                |
| Armazenamento de Tabelas                    | Yes                | Yes                | Sim                |
| Azure Cosmos DB                  | Sim                | Yes                | -                  |
| Azure Databricks                 | Yes                | Yes                | -                  |
| Azure Database Migration Service | Yes                | N/D\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Yes                | -                  | Yes                |
| Repositórios do Azure                      | Yes                | -                  | Yes                |
| **Identidade**                     |                    |                    |                    |
| Azure Active Directory           | Yes                | -                  | -                  |
| Azure Active Directory Domain Services | Yes          | Yes                | -                  |
| **Integração**                  |                    |                    |                    |
| Service Bus                      | Yes                | Yes                | Yes                |
| Event Grid                       | Yes                | -                  | -                  |
| Gestão de API                   | Yes                | -                  | -                  |
| **Serviços IoT**                 |                    |                    |                    |
| IoT Hub                          | Yes                | Yes                | Yes                |
| Provisão de dispositivos de hub IoT      | Yes                | Yes                | -                  |
| **Gestão e Governação**    |                    |                    |                    |
| Azure Site Recovery              | Yes                | -                  | -                  |
| Azure Migrate                    | Yes                | Yes                | -                  |
| **Media**                        |                    |                    |                    |
| Serviços de Multimédia                   | Yes                | -                  | Yes                |
| **Segurança**                     |                    |                    |                    |
| Centro de Segurança do Azure para IoT    | Yes                | Yes                | -                  |
| Azure Sentinel                   | Yes                | Yes                | -                  |
| **Armazenamento**                      |                    |                    |                    |
| Blob Storage                     | Sim                | Yes                | Yes                |
| Armazenamento Premium Blob             | Yes                | Yes                | Yes                |
| Armazenamento de Discos                     | Yes                | Yes                | -                  |
| Armazenamento ultra disco               | Yes                | Yes                | -                  |
| Armazenamento de discos geridos             | Yes                | Yes                | -                  |
| Armazenamento de Ficheiros                     | Sim                | Yes                | -                  |
| Armazenamento Premium de Arquivo             | Yes                | Yes                | -                  |
| File Sync                         | Yes                | Yes                | -                  |
| Armazenamento de Filas                    | Yes                | Yes                | Yes                |
| Avere vFXT                       | Yes                | -                  | -                  |
| Cache do Azure para Redis            | Yes                | N/D\*              | -                  |
| Azure NetApp Files               | Yes                | Yes                | -                  |
| Armazenamento de Arquivo                  | Yes                | Yes                | -                  |
| StorSimple                       | Yes                | Yes                | Yes                |
| Azure Backup                     | Yes                | Yes                | Yes                |
| Data Box                         | Yes                | -                  | Yes                |
| Data Box Edge                    | Yes                | Yes                | -                  |

\* Este serviço não persiste em dados. Caches transitórios, se houver, são encriptados com uma chave Microsoft.

\*\* Este serviço suporta o armazenamento de dados no seu próprio Key Vault, Conta de Armazenamento ou outro serviço que já suporta Server-Side Encriptação com Customer-Managed Chave.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [a encriptação é usada no Azure.](encryption-overview.md)
- Saiba como o Azure usa [encriptação dupla](double-encryption.md) para mitigar ameaças que vêm com dados encriptadores.