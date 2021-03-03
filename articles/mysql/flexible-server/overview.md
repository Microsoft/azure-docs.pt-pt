---
title: Visão geral - Base de Dados Azure para MySQL - Servidor Flexível
description: Saiba mais sobre a Base de Dados Azure para o servidor MySQL Flexible, um serviço de base de dados relacional na nuvem da Microsoft com base na Edição Comunitária mySQL.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
ms.openlocfilehash: 46b056c22a6d1c70a729f5b9558f53c685a878f0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732858"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Base de Dados Azure para MySQL - Servidor Flexível (Pré-visualização)

A azure Database for MySQL alimentado pela edição comunitária MySQL está disponível em dois modos de implementação:
- Servidor Único 
- Servidor Flexível (Pré-visualização)

Neste artigo, forneceremos uma visão geral e introdução aos conceitos fundamentais do modelo flexível de implementação do servidor. Para obter informações sobre como decidir qual a opção de implementação adequada para a sua carga de trabalho, consulte [a escolha da opção de servidor MySQL certa em Azure](./../select-right-deployment-type.md).

## <a name="overview"></a>Descrição Geral

A Azure Database for MySQL Flexible Server é um serviço de base de dados totalmente gerido projetado para fornecer mais controlo granular e flexibilidade sobre funções de gestão de bases de dados e configurações de configuração. Em geral, o serviço proporciona mais flexibilidade e personalizações de configuração do servidor com base nos requisitos do utilizador. A arquitetura flexível do servidor permite que os utilizadores optem pela alta disponibilidade dentro de uma única zona de disponibilidade e em várias zonas de disponibilidade. Os servidores flexíveis também fornecem controlos de otimização de custos melhores com a capacidade de parar/iniciar o seu servidor e skus rebentados, ideais para cargas de trabalho que não necessitam de capacidade de computação completa continuamente. O serviço suporta atualmente a versão comunitária do MySQL 5.7 e 8.0. O serviço está atualmente em pré-visualização, disponível hoje em grande variedade de regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/services/)

Servidores flexíveis são os mais adequados para 
- Desenvolvimentos de aplicações que requerem melhor controlo e personalização.
- Zona redundante alta disponibilidade
- Janelas de manutenção geridas

![Diagrama conceptual flexível do servidor](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="high-availability-within-and-across-availability-zones"></a>Alta disponibilidade dentro e em todas as zonas de disponibilidade

O modelo flexível de implementação do servidor foi concebido para suportar uma elevada disponibilidade dentro de uma única zona de disponibilidade e em várias zonas de disponibilidade. A arquitetura separa o cálculo e o armazenamento. O motor da base de dados funciona numa máquina virtual, enquanto os ficheiros de dados residem no armazenamento do Azure. O armazenamento mantém três cópias sincronizadas localmente redundantes dos ficheiros de base de dados que garantem a durabilidade dos dados em todos os momentos. 

Dentro de uma única zona de disponibilidade, se o servidor se desligar devido a eventos planeados ou não planeados, o serviço mantém uma elevada disponibilidade dos servidores utilizando o seguinte procedimento automatizado:

1. É a provisionado um novo VM compute.
2. O armazenamento com ficheiros de dados está mapeado para a nova Máquina Virtual
3. O motor de base de dados MySQL é colocado online na nova Máquina Virtual.
4. As aplicações do cliente podem voltar a ligar-se assim que o servidor estiver pronto para aceitar ligações.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Diagrama conceptual de alta disponibilidade de zona única"::: 

Se a zona redundante de alta disponibilidade for configurada, as disposições de serviço e mantém um servidor de espera quente em toda a zona de disponibilidade dentro da mesma região de Azure. As alterações de dados no servidor de origem são sincronizadas replicadas no servidor de espera para garantir a perda de dados zero. Com a zona redundante alta disponibilidade, uma vez que o evento de failover planeado ou não planeado é desencadeado, o servidor de espera entra imediatamente em linha e está disponível para processar transações recebidas. O tempo típico de failover varia entre 60 e 120 segundos. Isto permite que o serviço suporte a alta disponibilidade e proporcione uma melhor resiliência com tolerância para falhas de zona de disponibilidade única numa determinada região de Azure. 

Consulte [conceitos de alta disponibilidade](concepts-high-availability.md) para mais detalhes.

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Zona redundante diagrama conceptual de alta disponibilidade"::: 

## <a name="automated-patching-with-managed-maintenance-window"></a>Remendação automatizada com janela de manutenção gerida

O serviço executa patching automatizado do hardware subjacente, sistema operativo e motor de base de dados. O patching inclui atualizações de segurança e software. Para o motor MySQL, as atualizações de versão menores também estão incluídas como parte do lançamento de manutenção planeado. Os utilizadores podem configurar o calendário de remendos para serem geridos pelo sistema ou definir o seu horário personalizado. Durante o programa de manutenção, o patch é aplicado e o servidor pode necessitar de um reinício como parte do processo de remendação para completar a atualização. Com o horário personalizado, os utilizadores podem tornar o seu ciclo de remendos previsível e escolher uma janela de manutenção com o mínimo impacto para o negócio. Em geral, o serviço segue o horário de lançamento mensal como parte da integração e libertação contínuas.

Consulte [a Manutenção Programada](concepts-maintenance.md) para mais detalhes. 

## <a name="automatic-backups"></a>Backups automáticos

O serviço de servidor flexível cria automaticamente cópias de segurança do servidor e armazena-as no armazenamento localmente redundante ou geo-redundante do utilizador. As cópias de segurança podem ser utilizadas para restaurar o seu servidor em qualquer ponto no tempo dentro do período de retenção de backup. O período de retenção de backup predefinido é de sete dias. A retenção pode ser configurada opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits. 

Consulte [conceitos de backup](concepts-backup-restore.md) para saber mais.

## <a name="network-isolation"></a>Isolamento de Rede

Tem duas opções de ligação em rede para ligar à sua Base de Dados Azure para o MySQL Flexible Server. As opções são **acesso privado (integração VNet)** e **acesso público (endereços IP permitidos)**. 

* **Acesso privado (Integração VNet)** – Pode implantar o seu servidor flexível na sua [Rede Virtual Azure.](../../virtual-network/virtual-networks-overview.md) As redes virtuais Azure fornecem comunicação de rede privada e segura. Os recursos numa rede virtual podem comunicar através de endereços IP privados.

   Escolha a opção de Integração VNet se quiser as seguintes capacidades:
   * Conecte-se dos recursos Azure na mesma rede virtual ao seu servidor flexível usando endereços IP privados
   * Utilize VPN ou ExpressRoute para ligar de recursos não-Azure ao seu servidor flexível
   * Sem ponto final público

* **Acesso público (endereços IP autorizados)** – Pode implementar o seu servidor flexível com um ponto final público. O ponto final público é um endereço DNS publicamente resolúvel. A frase "endereços IP permitidos" refere-se a uma série de IPs que escolhe dar permissão para aceder ao seu servidor. Estas permissões são chamadas **regras de firewall.**

Consulte [conceitos de networking](concepts-networking.md) para saber mais.

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos

O serviço de servidor flexível está disponível em três níveis SKU: Burstable, General Purpose e Memory Optimized. O nível Burstable é o mais adequado para o desenvolvimento de baixo custo e cargas de trabalho de baixa concordância que não necessitam de capacidade computacional completa continuamente. O Objetivo Geral e a Memória Otimizados são mais adequados para cargas de trabalho de produção que requerem alta concordância, escala e desempenho previsível. Pode construir a sua primeira aplicação numa pequena base de dados por alguns dólares por mês e, em seguida, ajustar a balança de forma perfeita para atender às necessidades da sua solução. O dimensionamento de armazenamento está on-line e suporta o crescimento automático do armazenamento. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Só paga pelos recursos que consome. 

Consulte [os conceitos de Computação e Armazenamento](concepts-compute-storage.md) para saber mais.

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>Dimensione a sua carga de trabalho de leitura com até 10 réplicas de leitura

MySQL é um dos motores de base de dados populares para executar aplicações web e móveis à escala da Internet. Muitos dos nossos clientes usam-na para os seus serviços de educação online, serviços de streaming de vídeo, soluções de pagamento digital, plataformas de e-commerce, serviços de jogos, portais de notícias, governo e sites de saúde. Estes serviços são necessários para servir e escalar à medida que o tráfego na web ou aplicação móvel aumenta.

Do lado das aplicações, a aplicação é normalmente desenvolvida em Java ou php e migrada para funcionar em [conjuntos de escala de máquina virtual Azure](../../virtual-machine-scale-sets/overview.md)   ou [Azure App Services](../../app-service/overview.md)ou são   contentorizadas para funcionar no [Serviço Azure Kubernetes (AKS)](../../aks/intro-kubernetes.md). Com o conjunto de escalas de máquinas virtuais, o App Service ou a AKS como infraestrutura subjacente, o dimensionamento de aplicações é simplificado através do fornecimento instantâneo de novos VMs e replicando os componentes apátridas de aplicações para atender aos pedidos, mas muitas vezes, a base de dados acaba por ser um estrangulamento como componente imponente centralizado.

A funcionalidade de réplica de leitura permite-lhe replicar dados de uma Base de Dados Azure para o servidor flexível MySQL para um servidor apenas de leitura. Pode replicar-se a partir do servidor de **origem até 10 réplicas**. As réplicas são atualizadas assíncronamente utilizando a tecnologia de [replicação baseada na posição de ficheiro (binlog)](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)do motor MySQL. Pode utilizar uma solução de procuração de equilibrador de carga como [o ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) para reduzir perfeitamente a carga de trabalho da sua aplicação para ler réplicas sem qualquer custo de refactor de aplicações. 

Consulte [os conceitos de Replica](concepts-read-replicas.md) para saber mais. 


## <a name="stopstart-server-to-optimize-cost"></a>Servidor stop/start para otimizar o custo

O serviço de servidor flexível permite-lhe parar e iniciar o servidor on-demand para otimizar o custo. A faturação do nível do cálculo é interrompida imediatamente quando o servidor é parado. Isto pode permitir-lhe ter poupanças significativas de custos durante o desenvolvimento, teste e para cargas de trabalho previsíveis de produção limitadas ao tempo. O servidor permanece em estado de paragem durante sete dias, a menos que re-reinicie mais cedo. 

Consulte [os conceitos do Servidor](concept-servers.md) para saber mais. 

## <a name="enterprise-grade-security-and-privacy"></a>Segurança e privacidade de grau empresarial

O serviço de servidor flexível utiliza o módulo criptográfico validado FIPS 140-2 para encriptação de armazenamento de dados em repouso. Os dados, incluindo cópias de segurança e ficheiros temporários criados durante a execução de consultas são encriptados. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento Azure, e as teclas podem ser geridas pelo sistema (padrão). 

O serviço encripta dados em movimento com a segurança da camada de transporte aplicada por padrão. Os Servidores Flexíveis suportam apenas ligações encriptadas utilizando a Segurança da Camada de Transporte (TLS 1.2) e todas as ligações de entrada com TLS 1.0 e TLS 1.1 serão negadas. 

Veja [como usar ligações encriptadas em servidores flexíveis](https://docs.mongodb.com/manual/tutorial/configure-ssl) para saber mais.

Servidores flexíveis permitem o acesso privado completo aos servidores utilizando a integração [da rede virtual Azure](../../virtual-network/virtual-networks-overview.md) (VNet). Os servidores da rede virtual Azure só podem ser alcançados e conectados através de endereços IP privados. Com a integração do VNet, o acesso público é negado e os servidores não podem ser alcançados usando pontos finais públicos. 

Consulte [conceitos de networking](concepts-networking.md) para saber mais.


## <a name="monitoring-and-alerting"></a>Monitorização e alertas

O serviço de servidor flexível está equipado com funcionalidades de monitorização e alerta de desempenho incorporadas. Todas as métricas Azure têm uma frequência de um minuto, e cada métrica fornece 30 dias de história. Pode configurar alertas sobre as métricas. O serviço expõe as métricas do servidor do anfitrião para monitorizar a utilização dos recursos e permite configurar registos de consulta lenta. Utilizando estas ferramentas, pode otimizar rapidamente as suas cargas de trabalho e configurar o seu servidor para um melhor desempenho. 

Consulte [conceitos de monitorização](concepts-monitoring.md) para saber mais.

## <a name="migration"></a>Migração

O serviço executa a versão comunitária do MySQL. Isto permite a compatibilidade total da aplicação e requer um custo mínimo de refactor para migrar a aplicação existente desenvolvida no motor MySQL para um único serviço de servidor. A migração para o servidor único pode ser realizada utilizando uma das seguintes opções:

- **Despejo e Restauro** – Para migrações offline, onde os utilizadores podem pagar algum tempo de inatividade, despejar e restaurar usando ferramentas comunitárias como mysqldump/mydumper podem fornecer uma forma mais rápida de migrar. Consulte a Migração utilizando o despejo e restaure-o para obter mais detalhes. 
- **Serviço de Migração de Bases de Dados Azure** – Para migrações perfeitas e simplificadas para um único servidor com tempo mínimo de inatividade, [o Serviço de Migração da Base de Dados Azure](../../dms/tutorial-mysql-azure-mysql-online.md) pode ser alavancado. 

## <a name="azure-regions"></a>Regiões do Azure

Uma das vantagens de executar a sua carga de trabalho em Azure é o seu alcance global. O servidor flexível da Azure Database for MySQL está disponível hoje nas seguintes regiões de Azure:

| Region | Disponibilidade | Zona redundante HA | 
| --- | --- | --- |
| Europa Ocidental | :heavy_check_mark: | :heavy_check_mark: |
| Europa do Norte | :heavy_check_mark: | :heavy_check_mark: |
| Sul do Reino Unido | :heavy_check_mark: | :x: | 
| E.U.A. Leste 2 | :heavy_check_mark: | :heavy_check_mark: |
| E.U.A. Oeste 2 | :heavy_check_mark: | :heavy_check_mark: |
| E.U.A. Central | :heavy_check_mark: | :x: | 
| E.U.A. Leste | :heavy_check_mark: | :heavy_check_mark: |
| Canadá Central | :heavy_check_mark: | :x: | 
| Sudeste Asiático | :heavy_check_mark: | :heavy_check_mark: |
| Coreia do Sul Central | :heavy_check_mark: | :x: | 
| Leste do Japão | :heavy_check_mark: | :x: | 
| Leste da Austrália | :heavy_check_mark: | :heavy_check_mark: |

Estamos a trabalhar para adicionar novas regiões em breve.

## <a name="contacts"></a>Contactos
Para quaisquer perguntas ou sugestões que possa ter na Base de Dados Azure para o servidor flexível MySQL, envie um e-mail para a Base de Dados Azure para a Equipa MySQL ([ @Ask Azure DB para o MySQL).](mailto:AskAzureDBforMySQL@service.microsoft.com) Este endereço de e-mail não é um pseudónimo de suporte técnico.

Além disso, considere os seguintes pontos de contacto, conforme adequado:

- Para contactar o Suporte do Azure, [crie um pedido no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Passos seguintes
Agora que leu uma introdução à Base de Dados Azure para o modo de implementação de um único servidor MySQL, está pronto para:

- Crie o seu primeiro servidor. 
  - [Criar uma base de dados Azure para servidor flexível MySQL utilizando o portal Azure](quickstart-create-server-portal.md)
  - [Criar uma base de dados Azure para servidor flexível MySQL utilizando O Azure CLI](quickstart-create-server-cli.md)
  - [Gerir uma base de dados Azure para o MySQL Flexible Server utilizando o Azure CLI](how-to-manage-server-portal.md)

- Construa a sua primeira aplicação utilizando o seu idioma preferido:
  - [Python](connect-python.md)
  - [Php](connect-php.md)
