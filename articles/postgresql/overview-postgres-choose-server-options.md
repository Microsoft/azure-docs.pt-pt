---
title: Escolha a opção de servidor PostgreSQL certa em Azure
description: Fornece diretrizes para escolher a opção de servidor PostgreSQL certa para as suas implementações.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: db80ae66761e49ce90f25a22f0e74cf8a1300796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797236"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Escolha a opção de servidor PostgreSQL certa em Azure

Com o Azure, as suas cargas de trabalho do PostgreSQL Server podem funcionar numa infraestrutura de máquinas virtuais hospedada como um serviço (IaaS) ou como uma plataforma hospedada como um serviço (PaaS). O PaaS tem múltiplas opções de implementação, cada uma com vários níveis de serviço. Quando escolher entre IaaS e PaaS, deve decidir se pretende gerir a sua base de dados, aplicar patches e fazer backups, ou se pretende delegar estas operações no Azure.

Ao tomar a sua decisão, considere as seguintes três opções em PaaS ou, em alternativa, em execução em VMs Azure (IaaS)
- [Base de dados Azure para PostgreSQL Single Server](./overview-single-server.md)
- [Base de dados Azure para PostgreSQL Flexible Server](./flexible-server/overview.md)
- [Base de dados Azure para a hiperescala pós-SQL (Citus)](hyperscale/index.yml)

A opção **PostgreSQL em VMs Azure** enquadra-se na categoria industrial do IaaS. Com este serviço, pode executar o PostgreSQL Server dentro de uma máquina virtual totalmente gerida na plataforma cloud Azure. Todas as versões e edições recentes do PostgreSQL podem ser instaladas numa máquina virtual IaaS. Na diferença mais significativa da Base de Dados Azure para PostgreSQL, postgreSQL em VMs Azure oferece controlo sobre o motor de base de dados. No entanto, este controlo tem o custo da responsabilidade de gerir as VMs e muitas tarefas da administração de bases de dados (DBA). Estas tarefas incluem a manutenção e remendar servidores de bases de dados, recuperação de bases de dados e design de alta disponibilidade.

As principais diferenças entre estas opções constam do quadro seguinte:

| **Atributo** | **Postgres em Azure VMs** | **PostgreSQL como PaaS** |
| ----- | ----- | ----- |
| <B> Disponibilidade SLA |- 99,99% com conjuntos de disponibilidade <br> - 99,95% com VMs individuais | - Servidor Único – 99,99% <br> - Servidor Flexível - Não disponível durante a pré-visualização <br> - Hiperescala (Citus) - 99,95% (quando se ativa a elevada disponibilidade)|
| <B> Patching de OS e PostgreSQL | - Gerido pelo cliente | - Servidor Único – Automático <br> - Servidor Flexível – Automático com janela gerida pelo cliente opcional <br> - Hiperescala (Citus) – Automática |
| <B> Alta disponibilidade | - Clientes arquitetos, implementam, testam e mantêm alta disponibilidade. As capacidades podem incluir agrupamento, replicação, etc. | - Servidor Único: incorporado <br> - Servidor Flexível: incorporado <br> - Hiperescala (Citus): construída com standby |
| <B> Redundância de zona | - Os VMs Azure podem ser configurado para funcionar em diferentes zonas de disponibilidade. Para uma solução no local, os clientes devem criar, gerir e manter o seu próprio centro de dados secundário. | - Servidor Único: Não <br> - Servidor flexível: Sim <br> - Hiperescala (Citus): Não |
| <B> Cenário híbrido | - Gerido pelo cliente |- Servidor Único: Réplica de leitura <br> - Servidor Flexível: Não disponível durante a pré-visualização <br> - Hiperescala (Citus): Não |
| <B> Backup e Restauro | - Gerido pelo Cliente | - Servidor Único: incorporado com configuração do utilizador para local e geo <br> - Servidor Flexível: incorporado com configuração do utilizador no armazenamento redundante de zona <br> - Hiperescala (Citus): incorporado |
| <B> Operações de monitorização da base de dados | - Gerido pelo Cliente | - Servidor Único, Servidor Flexível e Hiperescala (Citus): Todos oferecem aos clientes a capacidade de definir alertas na operação da base de dados e agir ao atingir limiares. |
| <B> Proteção avançada de ameaças | - Os clientes devem construir esta proteção para si mesmos. |- Servidor Único: Sim <br> - Servidor Flexível: Não disponível durante a pré-visualização <br> - Hiperescala (Citus): Não |
| <B> Recuperação de Desastres | - Gerido pelo Cliente | - Servidor Único: Cópia de segurança geo redundante e réplica de leitura geo <br> - Servidor Flexível: Não disponível durante a pré-visualização <br> - Hiperescala (Citus): Não |
| <B> Desempenho Inteligente | - Gerido pelo Cliente | - Servidor Único: Sim <br> - Servidor Flexível: Não disponível durante a pré-visualização <br> - Hiperescala (Citus): Não |

## <a name="total-cost-of-ownership-tco"></a>Custo total de propriedade (TCO)

TCO é muitas vezes a principal consideração que determina a melhor solução para hospedar as suas bases de dados. Isto é verdade quer seja uma startup com pouco dinheiro ou uma equipa numa empresa estabelecida que opera sob restrições orçamentais apertadas. Esta secção descreve os fundamentos de faturação e licenciamento em Azure, uma vez que se aplicam à Base de Dados Azure para PostgreSQL e PostgreSQL em VMs Azure.

## <a name="billing"></a>Faturação

A Azure Database for PostgreSQL está atualmente disponível como um serviço em vários níveis com diferentes preços de recursos. Todos os recursos são cobrados de hora a hora a uma taxa fixa. Para obter as informações mais recentes sobre os níveis de serviço, tamanhos de cálculo e valores de armazenamento atualmente suportados, consulte [a página de preços](https://azure.microsoft.com/pricing/details/postgresql/server/) Pode ajustar dinamicamente os níveis de serviço e os tamanhos de cálculo para corresponder às variadas necessidades de produção da sua aplicação. Você é cobrado para o tráfego de Internet de saída a [taxas regulares de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

Com a Azure Database para PostgreSQL, a Microsoft configura automaticamente, patches e atualiza o software da base de dados. Estas ações automatizadas reduzem os custos da sua administração. Além disso, a Azure Database for PostgreSQL tem capacidades [automatizadas de ligação de backup.]() Estas capacidades ajudam-no a obter poupanças significativas de custos, especialmente quando se tem um grande número de bases de dados. Em contraste, com PostgreSQL em VMs Azure pode escolher e executar qualquer versão PostgreSQL. No entanto, é necessário pagar o VM proviscedo, o custo de armazenamento associado aos dados, a cópia de segurança, a monitorização dos dados e o armazenamento de registos e os custos para o tipo específico de licença PostgreSQL utilizado (se houver).

A Azure Database for PostgreSQL fornece alta disponibilidade incorporada para qualquer tipo de interrupção de nível de nó, mantendo ainda a garantia de 99,99% SLA para o serviço. No entanto, para uma alta disponibilidade de base de dados dentro de VMs, utiliza as opções de alta disponibilidade, como [a Replicação de Streaming,](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) que estão disponíveis numa base de dados PostgreSQL. A utilização de uma opção de alta disponibilidade suportada não fornece um SLA adicional. Mas permite-lhe alcançar uma disponibilidade de base de dados superior a 99,99% a custos adicionais e despesas administrativas.

Para obter mais informações sobre preços, consulte os seguintes artigos:
- [Base de Dados Azure para preços pós-SQL](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administração

Para muitas empresas, a decisão de transitar para um serviço de nuvem tem tanto a ver com descarregar a complexidade da administração como com o custo.

Com IaaS, Microsoft:

- Administra a infraestrutura subjacente.
- Fornece patching automatizado para hardware subjacente e SISTEMA

Com PaaS, Microsoft:

- Administra a infraestrutura subjacente.
- Fornece patching automatizado para hardware subjacente, sistema operativo e motor de base de dados.
- Gere a elevada disponibilidade da base de dados.
- Executa automaticamente cópias de segurança e replica todos os dados para fornecer recuperação de desastres.
- Encripta os dados em repouso e em movimento por padrão.
- Monitoriza o seu servidor e fornece funcionalidades para insights de desempenho de consulta e recomendações de desempenho.

Com a Azure Database for PostgreSQL, pode continuar a administrar a sua base de dados. Mas já não precisas de gerir o motor da base de dados, o sistema operativo ou o hardware. Exemplos de itens que pode continuar a administrar incluem:

- Bases de Dados
- Iniciar sessão
- Afinação de índices
- Ajuste de consultas
- Auditoria
- Segurança

Além disso, configurar alta disponibilidade para outro centro de dados requer uma configuração ou administração mínimas.

- Com PostgreSQL em VMs Azure, tem controlo total sobre o sistema operativo e a configuração de instância do servidor PostgreSQL. Com um VM, você decide quando atualizar ou atualizar o sistema operativo e o software de base de dados e quais patches aplicar. Também decide quando instalar qualquer software adicional, como uma aplicação antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar muito o patching, a cópia de segurança e a alta disponibilidade. Pode controlar o tamanho do VM, o número de discos e as suas configurações de armazenamento. Para obter mais informações, consulte [os tamanhos de serviço de máquina virtual e cloud para Azure.](../virtual-machines/sizes.md)

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Hora de mudar para o Serviço Azure PostgreSQL (PaaS)

- A Azure Database for PostgreSQL é a solução certa para aplicações desenhadas na nuvem quando a produtividade do desenvolvedor e o tempo rápido para o mercado de novas soluções são críticos. Com uma funcionalidade programática que é como a DBA, o serviço é adequado para arquitetos e desenvolvedores em nuvem porque reduz a necessidade de gestão do sistema operativo subjacente e da base de dados.

- Quando pretende evitar o tempo e a despesa de adquirir novo hardware no local, o PostgreSQL em VMs Azure é a solução certa para aplicações que requerem um controlo granular e personalização do motor PostgreSQL não suportado pelo serviço ou que exijam acesso ao SISTEMA subjacente.

## <a name="next-steps"></a>Passos seguintes

- Consulte a Base de Dados Azure para [preços pós-SQL](https://azure.microsoft.com/pricing/details/postgresql/server/).
- Comece por criar o seu primeiro servidor.