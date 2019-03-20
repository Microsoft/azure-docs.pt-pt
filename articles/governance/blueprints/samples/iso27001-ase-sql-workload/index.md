---
title: Exemplo - esquema de carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001 - Descrição geral
description: Descrição geral e arquitetura do exemplo de esquema de carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
---
# <a name="overview-of-the-azure-blueprints-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Descrição geral do exemplo de esquema de carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001 do Azure Blueprints

O exemplo do esquema de carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001 fornece uma infraestrutura adicional para o exemplo de esquema [Serviços Partilhados ISO 27001](../iso27001-shared/index.md).
Este esquema ajuda os clientes a implementarem arquiteturas com base na cloud que oferecem soluções para cenários que têm requisitos de acreditação ou conformidade.

Existem dois exemplos de esquema ISO 27001: este exemplo e o exemplo de esquema [Serviços Partilhados ISO 27001](../iso27001-shared/index.md).

> [!IMPORTANT]
> Este exemplo depende da infraestrutura implementada pelo exemplo de esquema [Serviços Partilhados ISO 27001](../iso27001-shared/index.md). Tem de ser implementado primeiro.

## <a name="architecture"></a>Arquitetura

O exemplo de esquema de carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001 implementa uma plataforma como um ambiente Web baseado num serviço. O ambiente pode ser utilizado para alojar várias aplicações Web, APIs Web e instâncias da Base de Dados SQL que seguem as normas ISO 27001. Este exemplo de esquema depende do exemplo de esquema [Serviços Partilhados ISO 27001](../iso27001-shared/index.md).

![Estrutura do exemplo de esquema de carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001](../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png)

Este ambiente é composto por vários serviços do Azure utilizados para fornecer uma infraestrutura de carga de trabalho segura, completamente monitorizada e preparada para empresas, com base nas normas ISO 27001. Este ambiente é composto por:

- Função de [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) chamada DevOps, que dispõe de direitos para implementar e gerir recursos num [Ambiente do Serviço de Aplicações do Azure](../../../../app-service/environment/intro.md) implementado pelo exemplo de esquema
- [Políticas do Azure](../../../policy/overview.md) para bloquear os serviços que podem ser implementados no ambiente e negar a criação de qualquer recurso de endereço IP público (PIP)
- Uma rede virtual que contém uma única sub-rede e colocada em modo peering num ambiente de [serviços partilhados](../iso27001-shared/index.md) pré-existente e que força todo o tráfego a passar pela firewall dos [serviços partilhados](../iso27001-shared/index.md). A rede virtual aloja os seguintes recursos:
  - Um [Ambiente do Serviço de Aplicações do Azure](../../../../app-service/environment/intro.md) que pode ser utilizado para alojar uma ou mais aplicações Web, APIs Web ou funções
  - Uma instância do [Azure Key Vault](../../../../key-vault/key-vault-whatis.md) com um ponto final de serviço VNet, para armazenar segredos utilizados pelas aplicações em execução no ambiente de carga de trabalho
  - Uma instância de servidor da [Base de Dados SQL do Azure](../../../../sql-database/sql-database-technical-overview.md) com um ponto final de serviço VNet, para alojar as bases de dados utilizadas para aplicações no ambiente de carga de trabalho

## <a name="next-steps"></a>Passos seguintes

Analisou a descrição geral e a arquitetura do exemplo de esquema de carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001. A seguir, visite os artigos seguintes para saber mais sobre o mapeamento de controlo e como implementar este exemplo:

> [!div class="nextstepaction"]
> [Esquema de carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001 - Mapeamento de controlo](./control-mapping.md)
> [Esquema de carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001 - Passos de implementação](./deploy.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).