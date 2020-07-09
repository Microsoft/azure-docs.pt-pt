---
title: Perguntas Mais Frequentes (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Gestd Instance frequentemente fez perguntas (FAQ)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 88f92117dc07fc241ca714851956e386cd10d617
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135031"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL Gestd Instance frequentemente fez perguntas (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo contém as perguntas mais comuns sobre [a Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Funcionalidades suportadas

**Onde posso encontrar uma lista de funcionalidades suportadas no SQL Managed Instance?**

Para obter uma lista de funcionalidades suportadas em SQL Managed Instance, consulte [as funcionalidades de Instância Gerida Azure SQL](../database/features-comparison.md).

Para diferenças de sintaxe e comportamento entre Azure SQL Managed Instance e SQL Server, consulte [as diferenças T-SQL do SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Limites de recursos de & de especificação tecnológica
 
**Onde posso encontrar características técnicas e limites de recursos para a SQL Managed Instance?**

Para obter as características de geração de hardware disponíveis, consulte [diferenças técnicas nas gerações de hardware](resource-limits.md#hardware-generation-characteristics).
Para os níveis de serviço disponíveis e as suas características, consulte [diferenças técnicas entre os níveis de serviço.](resource-limits.md#service-tier-characteristics)

## <a name="known-issues--bugs"></a>Problemas conhecidos & insetos

**Onde posso encontrar problemas e insetos conhecidos?**

Para bugs e questões conhecidas, veja [questões conhecidas.](../database/doc-changes-updates-release-notes.md#known-issues)

## <a name="new-features"></a>Novas funcionalidades

**Onde posso encontrar as últimas funcionalidades e as funcionalidades na pré-visualização pública?**

Para funcionalidades novas e de pré-visualização, consulte [as notas de lançamento](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Tempos de implantação 

**Quanto tempo demora a criar ou atualizar instâncias, ou a restaurar uma base de dados?**

O tempo esperado para criar uma instância gerida ou alterar o nível de serviço (vCores, armazenamento) depende de vários fatores. Dê uma olhada nas [operações de gestão.](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-conventions"></a>Convenções de nomenclatura

**Um caso gerido pode ter o mesmo nome que um exemplo do SQL Server no local?**

A alteração de um nome de instância gerida não é suportada.

A zona de DNS predefinido *.database.windows.net* para uma instância gerida pode ser alterada. 

Para utilizar outra zona DNS em vez do padrão, por exemplo, *.contoso.com*: 
- Use o CliConfig para definir um pseudónimo. A ferramenta é apenas um invólucro de definições de registo, por isso pode ser feito usando a política de grupo ou um script também.
- Utilize *CNAME* com a opção *TrustServerCertificate=true.*

## <a name="move-a-database-from-sql-managed-instance"></a>Mover uma base de dados da SQL Managed Instance 

**Como posso mover uma base de dados do SQL Managed Instance de volta para SQL Server ou Azure SQL Database?**

Pode [exportar uma base de dados para BACPAC](../database/database-export.md) e depois [importar o ficheiro BACPAC.](../database/database-import.md) Esta é a abordagem recomendada se a sua base de dados for inferior a 100 GB.

A replicação transacional pode ser usada se todas as tabelas da base de dados tiverem chaves primárias.

As `COPY_ONLY` cópias de segurança nativas retiradas do SQL Managed Instance não podem ser restauradas no SQL Managed Server porque o SQL Managed Instance tem uma versão de base de dados mais alta em comparação com o SQL Server.

## <a name="migrate-an-instance-database"></a>Migrar uma base de dados de casos

**Como posso migrar a minha base de dados de casos para a Base de Dados Azure SQL?**

Uma opção é [exportar a base de dados para um BACPAC](../database/database-export.md) e, em seguida, [importar o ficheiro BACPAC.](../database/database-import.md) 

Esta é a abordagem recomendada se a sua base de dados for inferior a 100 GB. A replicação transacional pode ser usada se todas as tabelas da base de dados tiverem chaves primárias.

## <a name="switch-hardware-generation"></a>Mudar geração de hardware 

**Posso mudar a minha geração de hardware SQL Managed Instance entre a Gen 4 e a Gen 5 online?**

A comutação on-line automatizada entre gerações de hardware é possível se ambas as gerações de hardware estiverem disponíveis na região onde o SQL Managed Instance é a provisionado. Neste caso, pode consultar a [página de visão geral](../database/service-tiers-vcore.md)do modelo vCore , que explica como alternar entre gerações de hardware.

Trata-se de uma operação de longa duração, uma vez que uma nova instância gerida será a provisionada em segundo plano e as bases de dados serão automaticamente transferidas entre as instâncias antigas e as novas, com uma rápida falha no final do processo. 

**E se ambas as gerações de hardware não forem suportadas na mesma região?**

Se ambas as gerações de hardware não forem suportadas na mesma região, a mudança na geração de hardware é possível, mas deve ser feita manualmente. Isto requer que você fornece um novo caso na região onde a geração de hardware procurado está disponível, e manualmente retroicordá-lo e restaurar dados entre as instâncias antigas e novas.

**E se não houver endereços IP suficientes para a realização de operação de atualização?**

Caso não existam endereços IP suficientes na sub-rede onde a sua instância gerida é alocada, terá de criar uma nova sub-rede e uma nova instância gerida no seu interior. Sugerimos também que a nova sub-rede seja criada com mais endereços IP atribuídos para que futuras operações de atualização evitem situações semelhantes. (Para o tamanho adequado da sub-rede, verifique [como determinar o tamanho de uma sub-rede VNet](vnet-subnet-determine-size.md).) Após o fornecimento da nova instância, pode fazer o back up manual e restaurar os dados entre as instâncias antigas e novas ou efetuar [a restauração pontual](point-in-time-restore.md?tabs=azure-powershell)transversal . 


## <a name="tune-performance"></a>Otimizar o desempenho

**Como sinto o desempenho de SQL Managed Instance?**

SQL Caso gerido no nível de finalidade geral utiliza armazenamento remoto, pelo que o tamanho dos dados e ficheiros de registo é importante para o desempenho. Para obter mais informações, consulte [o impacto do tamanho do ficheiro de registo no desempenho da sql Managed Instance](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)para fins gerais .

Se a sua carga de trabalho consistir em muitas pequenas transações, considere mudar o tipo de ligação de proxy para modo de redirecionamento.

## <a name="maximum-storage-size"></a>Tamanho máximo de armazenamento

**Qual é o tamanho máximo de armazenamento para SQL Managed Instance?**

O tamanho do armazenamento para sql Gestd Instance depende do nível de serviço selecionado (Final Geral ou Critical De Negócio). Para limitações de armazenamento destes níveis de serviço, consulte [as características do nível de serviço](../database/service-tiers-general-purpose-business-critical.md).

  
## <a name="networking-requirements"></a>Requisitos de rede 

**Quais são as restrições atuais de entrada/saída nSG na sub-rede de Instância Gerida?**

As regras de NSG e UDR necessárias são documentadas [aqui,](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)e automaticamente definidas pelo serviço.
Por favor, tenha em mente que estas regras são apenas as que precisamos para manter o serviço. Para ligar a instâncias geridas e utilizar diferentes funcionalidades, terá de definir regras adicionais, com regras específicas, que precisa de manter.

**Como posso definir regras NSG de entrada em portos de gestão?**

A SQL Managed Instance é responsável pela definição de regras sobre portas de gestão. Isto é conseguido através da configuração da [sub-rede com o nome de serviço.](connectivity-architecture-overview.md#service-aided-subnet-configuration)
Isto é para garantir um fluxo ininterrupto do tráfego de gestão, a fim de cumprir um SLA.

**Posso obter os intervalos ip de origem que são usados para o tráfego de gestão de entrada?**

Sim. Pode analisar o tráfego que passa pelo seu grupo de segurança de redes [configurando os registos de fluxo do Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group).

**Posso definir o NSG para controlar o acesso ao ponto final de dados (porta 1433)?**

Sim. Depois de ser a provisionada uma Instância Gerida, pode definir o NSG que controla o acesso à porta 1433. É aconselhável reduzir ao máximo o seu intervalo de IP.

**Posso definir a firewall NVA ou no local para filtrar o tráfego de gestão de saída com base em FQDNs?**

Não. Isto não é apoiado por várias razões:
-   O tráfego de encaminhamento que represente a resposta ao pedido de gestão de entrada seria assimétrico e não podia funcionar.
-   O tráfego de encaminhamento que vai para o armazenamento seria afetado por restrições de produção e latência para que desta forma não sejamos capazes de fornecer qualidade e disponibilidade de serviço esperada.
-   Com base na experiência, estas configurações são propensas a erros e não são reutilizáveis.

**Posso definir o NVA ou firewall para o tráfego de não gestão de saída?**

Sim. A forma mais simples de o conseguir é adicionar a regra 0/0 a uma UDR associada à sub-rede de instância gerida para encaminhar o tráfego através da NVA.
 
**Quantos endereços IP preciso para uma Instância Gerida?**

A sub-rede deve ter um número suficiente de [endereços IP](connectivity-architecture-overview.md#network-requirements)disponíveis . Para determinar o tamanho da sub-rede VNet para a ocorrência gerida do SQL, consulte [determine o tamanho e o intervalo da sub-rede necessárias para a instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet). 

**E se não houver endereços IP suficientes para a realização de operação de atualização de instâncias?**

Caso não existam [endereços IP suficientes](connectivity-architecture-overview.md#network-requirements) na sub-rede onde a sua instância gerida é alocada, terá de criar uma nova sub-rede e uma nova instância gerida no seu interior. Sugerimos também que a nova sub-rede seja criada com mais endereços IP atribuídos para que futuras operações de atualização evitem situações semelhantes. Após o fornecimento da nova instância, pode fazer o back up manual e restaurar os dados entre as instâncias antigas e novas ou efetuar [a restauração pontual](point-in-time-restore.md?tabs=azure-powershell)transversal .

**Preciso de uma sub-rede vazia para criar uma Instância Gerida?**

Não. Pode utilizar uma sub-rede vazia ou uma sub-rede que já contenha Instâncias Geridas. 

**Posso alterar o intervalo de endereços da sub-rede?**

Não se houver casos geridos dentro. Esta é uma limitação de infraestrutura de rede Azure. Só é permitido adicionar espaço adicional ao [endereço a uma sub-rede vazia.](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings) 

**Posso mudar o meu caso para outra sub-rede?**

Não. Esta é uma limitação de design de instância gerida atual. No entanto, pode prever uma nova instância noutra sub-rede e fazer retrocevesimento manual e restaurar dados entre o antigo e o novo caso ou realizar [restauro pontual](point-in-time-restore.md?tabs=azure-powershell)transversal .

**Preciso de uma rede virtual vazia para criar uma Instância Gerida?**

Isto não é necessário. Pode [criar uma rede virtual para Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) ou [configurar uma rede virtual existente para Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet).

**Posso colocar uma Instância Gerida com outros serviços numa sub-rede?**

Não. Atualmente não apoiamos a colocação de Casos Geridos numa sub-rede que já contenha outros tipos de recursos.

## <a name="connectivity"></a>Conectividade 

**Posso ligar-me à minha instância gerida usando o endereço IP?**

Não, isto não é apoiado. O nome de anfitrião de uma instância gerida mapeia para o equilibrador de carga em frente ao cluster virtual da Instância Gerida. Como um cluster virtual pode hospedar várias Instâncias Geridas, uma ligação não pode ser encaminhada para a instância gerida adequada sem especificar o seu nome.
Para obter mais informações sobre a arquitetura de cluster virtual SQL Managed Instance, consulte [a arquitetura de conectividade de cluster virtual.](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)

**Um caso gerido pode ter um endereço IP estático?**

Atualmente, isto não é apoiado.

Em situações raras, mas necessárias, talvez tenhamos de fazer uma migração on-line de um caso gerido para um novo cluster virtual. Se necessário, esta migração deve-se a mudanças na nossa pilha de tecnologia destinadas a melhorar a segurança e a fiabilidade do serviço. Migrar para um novo cluster virtual resulta na alteração do endereço IP que está mapeado para o nome de anfitrião de instância gerida. O serviço de instância gerida não reclama suporte a endereço IP estático e reserva-se o direito de alterá-lo sem aviso prévio como parte de ciclos de manutenção regulares.

Por esta razão, desencorajamos vivamente a imutabilidade do endereço IP, uma vez que poderia causar tempo de inatividade desnecessário.

**A Managed Instance tem um ponto final público?**

Sim. A Managed Instance tem um ponto final público que é por padrão usado apenas para a gestão de serviços, mas um cliente pode habilitar o acesso aos dados também. Para obter mais detalhes, consulte [a Utilização sql Managed Instance com pontos finais públicos](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-securely). Para configurar o ponto final público, vá ao [ponto final público configure em SQL Managed Instance](public-endpoint-configure.md).

**Como é que a Managed Instance controla o acesso ao ponto final público?**

A Instância Gerida controla o acesso ao ponto final público tanto a nível da rede como ao nível da aplicação.

Os serviços de gestão e implantação conectam-se a uma instância gerida utilizando um [ponto final de gestão](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#management-endpoint) que mapeia para um equilibrador de carga externo. O tráfego só é encaminhado para os nós se for recebido num conjunto de portas predefinido que apenas os componentes de gestão da instância gerida usam. Uma firewall incorporada nos nós é configurada para permitir o tráfego apenas a partir das gamas IP da Microsoft. Os certificados autenticam mutuamente toda a comunicação entre componentes de gestão e o plano de gestão. Para mais detalhes, consulte [a arquitetura de conectividade para sql Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#virtual-cluster-connectivity-architecture).

**Posso utilizar o ponto final público para aceder aos dados nas bases de dados de Instância Gerida?**

Sim. O cliente terá de permitir o acesso de dados de ponto final público a partir do [Portal Azure](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM e configurar o NSG para bloquear o acesso à porta de dados (porta número 3342). Para obter mais informações, consulte o [ponto final público Configure em Azure SQL Managed Instance](public-endpoint-configure.md) e Use [Azure SQL Managed Instance de forma segura com o ponto final público](public-endpoint-overview.md). 

**Posso especificar uma porta personalizada para o ponto final de dados SQL?**

Não, esta opção não está disponível.  Para o ponto final de dados privados, a Instância Gerida utiliza o número de porta padrão 1433 e para o ponto final de dados públicos, a Managed Instance utiliza o número de porta padrão 3342.

**Qual é a forma recomendada de ligar instâncias geridas colocadas em diferentes regiões?**

O prescêdo do circuito da Rota Expresso é a forma preferida de o fazer. Isto não deve ser misturado com o espreguiçadamento da rede virtual entre regiões que não é suportado devido à [restrição](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)interna relacionada com o balançador de carga .

Se o perspitamento do circuito De Rota Expresso não for possível, a única outra opção é criar ligação VPN Local-a-Local[(portal Azure,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell), [Azure CLI).](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)


## <a name="mitigate-data-exfiltration-risks"></a>Mitigar riscos de exfiltração de dados  

**Como posso mitigar os riscos de exfiltração de dados?**

Para mitigar quaisquer riscos de exfiltração de dados, recomenda-se aos clientes a aplicação de um conjunto de definições e controlos de segurança:

- Ligue [encriptação de dados transparentes (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) em todas as bases de dados.
- Desligue o tempo de execução da linguagem comum (CLR). Isto também é recomendado no local.
- Utilize apenas a autenticação do Azure Ative Directory (Azure AD).
- Aceda ao caso com uma conta DBA de baixo privilégio.
- Configure o acesso à caixa de salto JIT para a conta sysadmin.
- Ligue [a auditoria DO SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integre-a com mecanismos de alerta.
- Ligue [a Deteção](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) de Ameaças a partir da suíte avançada de [segurança de dados (ADS).](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="cost-saving-use-cases"></a>Casos de utilização de economia de custos

**Onde posso encontrar casos de utilização e consequentes economias de custos com a SQL Managed Instance?**

Estudos de caso de caso de caso de caso de caso de casos geridos pela SQL:

- [Rio Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Todos os escritos](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Para se compreender melhor os benefícios, custos e riscos associados à implementação do Azure SQL Managed Instance, há também um estudo da Forrester: [O Impacto Económico Total da Microsoft Azure SQL Database Managed Instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns"></a>DNS

**Posso configurar um DNS personalizado para sql Managed Instance?**

Sim. Ver [como configurar um DNS personalizado para Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

**Posso refrescar o DNS?**

Atualmente, não fornecemos uma funcionalidade para atualizar a configuração do servidor DNS para SQL Managed Instance.

A configuração do DNS é eventualmente renovada:

- Quando o contrato de arrendamento DHCP expirar.
- No upgrade da plataforma.

Como uma solução alternativa, desclasse a SQL Managed Instance para 4 vCores e atualize-a novamente depois. Isto tem um efeito colateral de refrescar a configuração do DNS.


## <a name="change-time-zone"></a>Alterar fuso horário

**Posso mudar o fuso horário para um caso gerido?**

A configuração do fuso horário pode ser definida quando uma instância gerida é prevista pela primeira vez. Mudar o fuso horário de um caso gerido existente não é suportado. Para mais informações, consulte [as limitações do fuso horário.](timezones-overview.md#limitations)

As soluções alternativas incluem a criação de uma nova instância gerida com o fuso horário adequado e, em seguida, ou executar uma cópia de segurança manual e restaurar, ou o que recomendamos, realizando uma [restauração pontual transversal no tempo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Resolver problemas de desempenho

**Como resolvo problemas de desempenho com a SQL Managed Instance?**

Para uma comparação de desempenho entre SQL Managed Instance e SQL Server, um bom ponto de partida são [as melhores práticas para comparação de desempenho entre Azure SQL Managed Instance e SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

O carregamento de dados é frequentemente mais lento em SQL Managed Instance do que no SQL Server devido ao modelo obrigatório de recuperação total e [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) na produção de registo de transações. Por vezes, isto pode ser trabalhado carregando dados transitórios em temporário em vez da base de dados do utilizador, ou usando colunas agrupadas ou tabelas otimizadas para a memória.


## <a name="restore-encrypted-backup"></a>Restaurar cópias de segurança encriptadas

**Posso restaurar a minha base de dados encriptada para a SQL Managed Instance?**

Sim, não precisa de desencriptar a sua base de dados para a restaurar para a SQL Managed Instance. É necessário fornecer um certificado/chave utilizado como protetor de chave de encriptação no sistema de origem para a SQL Managed Instance para ser capaz de ler dados a partir do ficheiro de backup encriptado. Há duas maneiras possíveis de fazê-lo:

- *Faça o upload do certificado-protector para a SQL Managed Instance*. Pode ser feito apenas com o PowerShell. O [guião da amostra](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descreve todo o processo.
- *Faça o upload assimétrico do protector-chave para o Cofre da Chave Azure e aponte para o SQL Managed Instance .* Esta abordagem assemelha-se a uma caixa de utilização TDE (BYOK) que também utiliza a integração do Key Vault para armazenar a chave de encriptação. Se não quiser utilizar a chave como protetor de chave de encriptação e apenas quiser disponibilizar a chave para a SQL Managed Instance para restaurar a(s) base de dados encriptada, siga as instruções para configurar o [BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption), e não verifique a caixa de verificação **Faça da chave selecionada o protetor TDE predefinido**.

Assim que disponibilizar o protetor de encriptação para a SQL Managed Instance, pode proceder ao procedimento de restauro da base de dados padrão.

## <a name="purchasing-models-and-benefits"></a>Modelos e benefícios de compra

**Que modelos de compra estão disponíveis para a SQL Managed Instance?**

SQL Managed Instance oferece [modelo de compra baseado em vCore.](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)

**Que benefícios de custo estão disponíveis para a SQL Managed Instance?**

Pode economizar custos com os benefícios Azure SQL das seguintes formas:
-   Maximizar os investimentos existentes em licenças no local e economizar até 55% com [a Azure Hybrid Benefit.](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell) 
-   Comprometa-se a fazer uma reserva de recursos computacional e poupe até 33 por cento com [o Benefício de Instância Reservada.](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity) Combine isto com o benefício Azure Hybrid para uma poupança até 82%. 
-   Poupe até 55% contra os preços da lista com [o Azure Dev/Test Pricing Benefit](https://azure.microsoft.com/pricing/dev-test/) que oferece tarifas com desconto para o seu desenvolvimento contínuo e teste de cargas de trabalho.

**Quem é elegível para o benefício da Instância Reservada?**

Para ser elegível para benefício de instância reservada, o seu tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para obter mais informações sobre reservas, consulte [O Benefício de Instância Reservada.](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity) 

**É possível cancelar, trocar ou reembolsar reservas?**

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Faturação para Instância Gerida e armazenamento de backup

**Quais são as opções de preços da SQL Managed Instance?**

Para explorar as opções de preços de Exemplos Geridos, consulte [a página de preços](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Como posso rastrear o custo da faturação para o meu caso gerido?**

Pode fazê-lo utilizando a [solução Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/). Navegue para **subscrições** no [portal Azure](https://portal.azure.com) e selecione **Análise de Custos.** 

Utilize a opção **custos acumulados** e, em seguida, filtre pelo **tipo de recurso** como `microsoft.sql/managedinstances` .

**Quanto custam as cópias de segurança automatizadas?**

Obtém a mesma quantidade de espaço de armazenamento de backup gratuito que o espaço de armazenamento de dados reservado adquirido, independentemente do conjunto do período de retenção de backup. Se o seu consumo de armazenamento de backup estiver dentro do espaço de armazenamento gratuito de backup atribuído, cópias de segurança automatizadas em caso gerido não terão custos adicionais para si, pelo que serão gratuitas. Exceder o armazenamento de backup acima do espaço livre resultará em custos de cerca de $0,20 - $0,24 por GB/mês nas regiões dos EUA, ou consulte a página de preços para mais detalhes para a sua região. Para obter mais detalhes, consulte [o consumo de armazenamento de backup explicado.](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)

**Como posso monitorizar os custos de faturação para o meu consumo de armazenamento de reserva?**

Pode monitorizar o custo do armazenamento de cópias de segurança através do Portal Azure. Para obter instruções, consulte [os custos do Monitor para cópias de segurança automatizadas.](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs) 

**Como posso otimizar os meus custos de armazenamento de reserva na área gerida?**

Para otimizar os custos de armazenamento de backup, consulte [afinação de backup fina em SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="password-policy"></a>Política de senha 

**Que políticas de palavra-passe são aplicadas para os logins SQL De Instância Gerida SQL?**

A política de palavra-passe de sql Managed Instance para logins SQL herda as políticas da plataforma Azure que são aplicadas aos VMs formando cluster virtual mantendo o exemplo gerido. De momento não é possível alterar nenhuma destas definições, uma vez que estas definições são definidas pelo Azure e herdadas por exemplo gerida.

 > [!IMPORTANT]
 > A plataforma Azure pode alterar os requisitos de política sem notificar os serviços que dependem dessas políticas.

**Quais são as políticas atuais da plataforma Azure?**

Cada login deve definir a sua palavra-passe no início do login e alterar a sua palavra-passe depois de atingir a idade máxima.

| **Política** | **Definição de Segurança** |
| --- | --- |
| Idade máxima da senha | 42 dias |
| Idade mínima da senha | 1 dia |
| Comprimento mínimo da palavra-passe | 10 caracteres |
| A palavra-passe deve satisfazer os requisitos de complexidade | Ativado |

**É possível desativar a complexidade e expiração da palavra-passe em SQL Managed Instance no nível de login?**

Sim, é possível controlar campos de CHECK_POLICY e CHECK_EXPIRATION ao nível de login. Pode verificar as definições atuais executando o seguinte comando T-SQL:

```sql
SELECT *
FROM sys.sql_logins
```

Depois disso, pode modificar as definições de login especificadas executando:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(substituir 'teste' pelo nome de login pretendido e ajustar os valores de política e de expiração)

## <a name="azure-feedback-and-support"></a>Feedback e suporte do Azure

**Onde posso deixar as minhas ideias para melhorias de exemplos geridos pela SQL?**

Pode votar num novo recurso de Instância Gerida ou colocar uma nova ideia de melhoria na votação no [SQL Managed Instance Feedback Forum](https://feedback.azure.com/forums/915676-sql-managed-instance). Desta forma, pode contribuir para o desenvolvimento do produto e ajudar-nos a priorizar as nossas potenciais melhorias.

**Como posso criar um pedido de apoio ao Azure?**

Para aprender a criar o pedido de suporte do Azure, consulte [Como criar o pedido de suporte do Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)

