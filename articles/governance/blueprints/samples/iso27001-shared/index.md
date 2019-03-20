---
title: Exemplos - esquema Serviços Partilhados ISO 27001 - Descrição geral
description: Descrição geral e arquitetura do exemplo de esquema Serviços Partilhados ISO 27001.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
---
# <a name="overview-of-the-azure-blueprints-iso-27001-shared-services-blueprint-sample"></a>Descrição geral do exemplo de esquema Serviços Partilhados ISO 27001 do Azure Blueprints

O exemplo de esquema Serviços Partilhados ISO 27001 fornece um conjunto de grades de proteção de políticas e padrões de infraestrutura conformes que contribuem para o atestado da norma ISO 27001. Este esquema ajuda os clientes a implementarem arquiteturas com base na cloud que oferecem soluções para cenários que têm requisitos de acreditação ou conformidade.

O exemplo de esquema de [carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001](../iso27001-ase-sql-workload/index.md) expande este exemplo.

## <a name="architecture"></a>Arquitetura

O exemplo de esquema Serviços Partilhados ISO 27001 implementa uma infraestrutura de base no Azure que pode ser utilizada por organizações para alojar várias cargas de trabalho com base na abordagem de Datacenter Virtual (VDC).
O VDC é um conjunto comprovado de arquiteturas de referência, ferramentas de automatização e modelos de cativação utilizados pela Microsoft com os seus clientes empresariais de maior dimensão. O exemplo de esquema Serviços Partilhados baseia-se num ambiente VDC do Azure totalmente nativo, apresentado abaixo.

![Estrutura de exemplo do esquema Serviços Partilhados ISO 27001](../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png)

Este ambiente é composto por vários serviços do Azure utilizados para fornecer uma infraestrutura de serviços partilhados segura, completamente monitorizada e preparada para empresas, com base nas normas ISO 27001. Este ambiente é composto por:

- Funções de [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) utilizadas para a separação de funções de uma perspetiva de plano de controlo. Antes da implementação de qualquer infraestrutura, são definidas três funções:
  - A função NetOps tem os direitos para gerir o ambiente de rede, incluindo as definições da firewall, definições do NSG, encaminhamento e outras funcionalidades de rede
  - A função SecOps tem os direitos necessários para implementar e gerir o [Centro de Segurança do Azure](../../../../security-center/security-center-intro.md), definir [Políticas do Azure](../../../policy/overview.md) e outros direitos relacionados com segurança
  - A função SysOps tem os direitos necessários para definir [Políticas do Azure](../../../policy/overview.md) na subscrição, gerir o [Log Analytics](../../../../azure-monitor/overview.md) em todo o ambiente, entre outros direitos operacionais
- O [Log Analytics](../../../../azure-monitor/overview.md) é implementado como o primeiro serviço do Azure para garantir que todas as ações e serviços são registados numa localização central desde o momento em que inicia a implementação segura
- Uma rede virtual que suporta sub-redes para conectividade para um datacenter no local, uma pilha de entrada e saída para conectividade Internet e uma sub-rede de serviço partilhado com NSGs e ASGs para microssegmentação completa, que contém:
  - Um anfitrião jumpbox ou bastion utilizado para fins de gestão, ao qual só é possível aceder através de uma [Azure Firewall](../../../../firewall/overview.md) implementada na sub-rede da pilha de entrada
  - Duas máquinas virtuais com o Active Directory Domain Services (AD DS) e DNS acessíveis apenas através da jumpbox e que só podem ser configurada para replicar o AD através de uma ligação VPN ou [ExpressRoute](../../../../expressroute/expressroute-introduction.md) (não implementada pelo esquema)
  - Utilização do [Observador de Rede do Azure](../../../../network-watcher/network-watcher-monitoring-overview.md) e de proteção contra DDoS padrão
- Uma instância do [Azure Key Vault](../../../../key-vault/key-vault-whatis.md) utilizada para alojar segredos utilizados para as VMs implementadas no ambiente de serviços partilhados

Todos estes elementos obedecem às práticas comprovadas publicados no [Centro de Arquitetura do Azure - Arquiteturas de Referência](/azure/architecture/reference-architectures/).

> [!NOTE]
> A infraestrutura dos Serviços Partilhados ISO 27001 concebe uma arquitetura básica para cargas de trabalho.
> Continua a ser necessário implementar as cargas de trabalho atrás desta arquitetura básica.

Para obter mais informações, veja a [documentação do Datacenter Virtual](/azure/architecture/vdc/).

## <a name="next-steps"></a>Passos seguintes

Analisou a descrição geral e a arquitetura do exemplo de esquema Serviços Partilhados ISO 27001.
A seguir, visite os artigos seguintes para saber mais sobre o mapeamento de controlo e como implementar este exemplo:

> [!div class="nextstepaction"]
> [Esquema Serviços Partilhados ISO 27001 - Mapeamento de controlo](./control-mapping.md)
> [Esquema Serviços Partilhados ISO 27001 - Passos de implementação](./deploy.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).