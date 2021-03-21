---
title: Selecionando o tipo de implementação certo - Base de Dados Azure para MySQL
description: Este artigo descreve quais os fatores a considerar antes de implementar a Base de Dados Azure para o MySQL como infraestrutura como serviço (IaaS) ou plataforma como um serviço (PaaS).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 125431e6630ccfdd9e0e5d6b2a4ec5fa9b9e58fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736190"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Escolha a opção right MySQL Server em Azure

Com o Azure, as cargas de trabalho do seu servidor MySQL podem funcionar numa infraestrutura de máquinas virtuais hospedada como um serviço (IaaS) ou como uma plataforma hospedada como um serviço (PaaS). O PaaS tem várias opções de implementação, e existem níveis de serviço dentro de cada opção de implementação. Quando escolher entre IaaS e PaaS, deve decidir se pretende gerir a sua base de dados, aplicar patches e fazer backups, ou se pretende delegar estas operações no Azure.

Ao tomar a sua decisão, considere as seguintes duas opções:

- **Base de Dados Azure para o MySQL**. Esta opção é um motor de base de dados MySQL totalmente gerido com base na versão estável da edição comunitária mySQL. Esta base de dados relacional como um serviço (DBaaS), hospedado na plataforma cloud Azure, insere-se na categoria industrial do PaaS.

  Com uma instância gerida do MySQL em Azure, pode utilizar funcionalidades incorporadas viz patching automatizado, alta disponibilidade, backups automatizados, escala elástica, segurança de nível empresarial, conformidade e governação, monitorização e alerta que de outra forma requerem uma configuração extensiva quando o MySQL Server está no local ou num VM Azure. Ao utilizar o MySQL como serviço, paga-se à medida que vai, com opções para escalar ou escalar para um maior controlo sem interrupção.
  
  [Azure Database for MySQL](overview.md), alimentado pela edição comunitária MySQL está disponível em dois modos de implantação:

  - [Single Server](single-server-overview.md) é um serviço de base de dados totalmente gerido com requisitos mínimos para personalizações da base de dados. A plataforma de servidor único foi concebida para lidar com a maioria das funções de gestão da base de dados, tais como patching, backups, alta disponibilidade, segurança com a configuração e controlo mínimos do utilizador. A arquitetura está otimizada para proporcionar 99,99% de disponibilidade em zona de disponibilidade única. Os servidores individuais são mais adequados para aplicações nativas em nuvem concebidas para lidar com remendos automatizados sem a necessidade de controlo granular na programação de remendos e configurações personalizadas de configuração do MySQL.

  - [O Flexible Server (Preview)](flexible-server/overview.md) é um serviço de base de dados totalmente gerido, concebido para fornecer mais controlo granular e flexibilidade sobre as funções de gestão da base de dados e as definições de configuração. Em geral, o serviço proporciona mais flexibilidade e personalizações de configuração do servidor em comparação com a implementação de um único servidor com base nos requisitos do utilizador. A arquitetura flexível do servidor permite que os utilizadores optem pela alta disponibilidade dentro de uma única zona de disponibilidade e em várias zonas de disponibilidade. Os servidores flexíveis também fornecem controlos de otimização de custos melhores com a capacidade de iniciar/parar o seu servidor e SKUs rebentados, ideais para cargas de trabalho que não necessitam de capacidade de computação completa continuamente.

  Os servidores flexíveis são os mais adequados para:

  - Desenvolvimento de aplicações que requer um melhor controlo e personalização do motor MySQL.
  - Zona redundante alta disponibilidade
  - Janelas de manutenção geridas

- **MySQL em Azure VMs**. Esta opção insere-se na categoria industrial do IaaS. Com este serviço, pode executar o MySQL Server dentro de uma máquina virtual gerida na plataforma cloud Azure. Todas as versões e edições recentes do MySQL podem ser instaladas na máquina virtual.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Comparar as opções de implantação do MySQL em Azure

As principais diferenças entre estas opções constam do quadro seguinte:

| Atributo          | Base de Dados do Azure para MySQL<br/>Servidor Único |Base de Dados do Azure para MySQL<br/>Servidor Flexível  |MySQL em VMs Azure                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| Suporte da versão MySQL | 5.6, 5.7 & 8.0| 5.7 & 8.0 | Qualquer versão|
| Escalagem de cálculo | Suportado (A escala de e para o nível básico não é suportada)| Suportado | Suportado|
| Tamanho do armazenamento | 5 GiB a 16 TiB| 5 GiB a 16 TiB | 32 GiB a 32.767 GiB|
| Escala de armazenamento on-line | Suportado| Suportado| Não suportado|
| Dimensionamento de armazenamento automático | Suportado| Não suportado na pré-visualização| Não suportado|
| Escala adicional de IOPs | Não suportado| Suportado| Não suportado|
| Conectividade da rede | - Pontos finais públicos com firewall do servidor.<br/> - Acesso privado com suporte private link.|- Pontos finais públicos com firewall do servidor.<br/> - Acesso privado com integração de Rede Virtual.| - Pontos finais públicos com firewall do servidor.<br/> - Acesso privado com suporte private link.|
| Contrato de nível de serviço (SLA) | 99,99% de disponibilidade SLA |Sem SLA na pré-visualização| 99,99% usando Zonas de Disponibilidade|
| Patching do sistema operativo| Automático  | Automático com controlo de janela de manutenção personalizada | Gerido por utilizadores finais |
| Patching MySQL     | Automático  | Automático com controlo de janela de manutenção personalizada | Gerido por utilizadores finais |
| Elevada disponibilidade | HA incorporado dentro de uma única zona de disponibilidade| HA incorporado dentro e em todas as zonas de disponibilidade | Gerido sob medida usando clustering, replicação, etc.|
| Redundância entre zonas | Não suportado | Suportado | Suportado|
| Colocação de zona | Não suportado | Suportado | Suportado|
| Cenários híbridos | Suportado com [replicação de dados](./concepts-data-in-replication.md)| Não disponível na pré-visualização | Gerido por utilizadores finais |
| Réplicas de leitura | Suportado (até 5 réplicas)| Suportado (até 10 réplicas)| Gerido por utilizadores finais |
| Backup | Automatizado com retenção de 7-35 dias | Automatizado com retenção de 1-35 dias | Gerido por utilizadores finais |
| Monitorização das operações de base de dados | Suportado | Suportado | Gerido por utilizadores finais |
| Recuperação após desastre | Apoiado com armazenamento de backup geo redundante e região transversal ler réplicas | Não suportado na pré-visualização| Personalizado Gerido com tecnologias de replicação |
| Query Performance Insight | Suportado | Não disponível na pré-visualização| Gerido por utilizadores finais |
| Preços de Instâncias Reservadas | Suportado | Não disponível na pré-visualização | Suportado |
| Autenticação do Azure AD | Suportado | Não disponível na pré-visualização | Não suportado|
| Encriptação de dados em repouso | Suportado com chaves geridas pelo cliente | Suportado com chaves geridas pelo serviço | Não suportado|
| SSL/TLS | Ativado por padrão com suporte para TLS v1.2, 1.1 e 1.0 | Aplicado com TLS v1.2 | Suportado com TLS v1.2, 1.1 e 1.0 |
| Gestão de Frotas | Apoiado com Azure CLI, PowerShell, REST e Azure Resource Manager | Apoiado com Azure CLI, PowerShell, REST e Azure Resource Manager  | Suportado para VMs com Azure CLI, PowerShell, REST e Azure Resource Manager |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivações empresariais para escolher PaaS ou IaaS

Existem vários fatores que podem influenciar a sua decisão de escolher PaaS ou IaaS para hospedar as suas bases de dados MySQL.

### <a name="cost"></a>Custo

A redução de custos é muitas vezes a principal consideração que determina a melhor solução para hospedar as suas bases de dados. Isto é verdade quer seja uma startup com pouco dinheiro ou uma equipa numa empresa estabelecida que opera sob restrições orçamentais apertadas. Esta secção descreve os fundamentos de faturação e licenciamento em Azure, uma vez que se aplicam à Base de Dados Azure para o MySQL e o MySQL em VMs Azure.

#### <a name="billing"></a>Faturação

A Azure Database for MySQL está atualmente disponível como um serviço em vários níveis com diferentes preços de recursos. Todos os recursos são cobrados de hora a hora a uma taxa fixa. Para obter as informações mais recentes sobre os níveis de serviço suportados atualmente, tamanhos de cálculo e valores de armazenamento, consulte [a página de preços](https://azure.microsoft.com/pricing/details/mysql/). Pode ajustar dinamicamente os níveis de serviço e os tamanhos de cálculo para corresponder às variadas necessidades de produção da sua aplicação. Você é cobrado para o tráfego de Internet de saída a [taxas regulares de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

Com a Azure Database para o MySQL, a Microsoft configura automaticamente, patches e atualiza o software da base de dados. Estas ações automatizadas reduzem os custos da sua administração. Além disso, a Azure Database para o MySQL tem [capacidades de backup automatizada.](./concepts-backup.md) Estas capacidades ajudam-no a obter poupanças significativas de custos, especialmente quando se tem um grande número de bases de dados. Em contraste, com o MySQL em VMs Azure pode escolher e executar qualquer versão MySQL. Independentemente da versão MySQL que utilizar, paga pelo VM proviscrito, custo de armazenamento associado aos dados, backup, dados de monitorização e armazenamento de registos e os custos para o tipo de licença MySQL específico utilizado (se houver).

A Azure Database for MySQL fornece alta disponibilidade incorporada para qualquer tipo de interrupção de nível de nó, mantendo ainda a garantia de 99,99% SLA para o serviço. No entanto, para uma alta disponibilidade de base de dados dentro de VMs, utiliza as opções de alta disponibilidade, como [a replicação MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html) que estão disponíveis numa base de dados MySQL. A utilização de uma opção de alta disponibilidade suportada não fornece um SLA adicional. Mas permite-lhe alcançar uma disponibilidade de base de dados superior a 99,99% a custos adicionais e despesas administrativas.

Para obter mais informações sobre preços, consulte os seguintes artigos:

- [Base de Dados Azure para preços MySQL](https://azure.microsoft.com/pricing/details/mysql/)
- [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de transitar para um serviço de nuvem tem tanto a ver com descarregar a complexidade da administração como com o custo.

Com IaaS, Microsoft:

- Administra a infraestrutura subjacente.
- Fornece patching automatizado para hardware subjacente e SISTEMA.
  
Com PaaS, Microsoft:

- Administra a infraestrutura subjacente.
- Fornece patching automatizado para hardware subjacente, sistema operativo e motor de base de dados.
- Gere a elevada disponibilidade da base de dados.
- Executa automaticamente cópias de segurança e replica todos os dados para fornecer recuperação de desastres.
- Encripta os dados em repouso e em movimento por padrão.
- Monitoriza o seu servidor e fornece funcionalidades para insights de desempenho de consulta e recomendações de desempenho

A lista que se segue descreve considerações administrativas para cada opção:

- Com a Azure Database para o MySQL, pode continuar a administrar a sua base de dados. Mas já não precisas de gerir o motor da base de dados, o sistema operativo ou o hardware. Exemplos de itens que pode continuar a administrar incluem:

  - Bases de Dados
  - Iniciar sessão
  - Afinação de índices
  - Ajuste de consultas
  - Auditoria
  - Segurança

  Além disso, configurar alta disponibilidade para outro centro de dados requer uma configuração ou administração mínimas.

- Com o MySQL em VMs Azure, tem controlo total sobre o sistema operativo e a configuração de instância do servidor MySQL. Com um VM, você decide quando atualizar ou atualizar o sistema operativo e o software de base de dados e quais patches aplicar. Também decide quando instalar qualquer software adicional, como uma aplicação antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar muito o patching, a cópia de segurança e a alta disponibilidade. Pode controlar o tamanho do VM, o número de discos e as suas configurações de armazenamento. Para obter mais informações, consulte [os tamanhos de serviço de máquina virtual e cloud para Azure.](../virtual-machines/sizes.md)

### <a name="time-to-move-to-azure"></a>Hora de se mudar para Azure.

- A Azure Database for MySQL é a solução certa para aplicações desenhadas na nuvem quando a produtividade do desenvolvedor e o tempo rápido para o mercado de novas soluções são críticos. Com uma funcionalidade programática que é como a DBA, o serviço é adequado para arquitetos e desenvolvedores em nuvem porque reduz a necessidade de gestão do sistema operativo subjacente e da base de dados.

- Quando pretende evitar o tempo e a despesa de aquisição de novo hardware no local, o MySQL em VMs Azure é a solução certa para aplicações que requerem um controlo granular e personalização do motor MySQL não suportado pelo serviço ou que exijam acesso ao SISTEMA subjacente. Esta solução também é adequada para a migração de aplicações e bases de dados existentes no local para Azure intactas, para os casos em que a Base de Dados Azure para o MySQL é um mau ajuste.

Como não há necessidade de alterar as camadas de apresentação, aplicação e dados, poupa tempo e orçamento na rearquecação da sua solução existente. Em vez disso, pode focar-se em migrar todas as suas soluções para a Azure e abordar algumas otimizações de desempenho que a plataforma Azure poderá necessitar.

## <a name="next-steps"></a>Passos seguintes

- Consulte [a Base de Dados Azure para os preços do MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
- Começa por [criar o teu primeiro servidor.](./quickstart-create-mysql-server-database-using-azure-portal.md)
