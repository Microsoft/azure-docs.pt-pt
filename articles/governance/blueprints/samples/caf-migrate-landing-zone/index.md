---
title: Exemplos - Esquema Zona de destino de migração do CAF - Descrição geral
description: Descrição geral e arquitetura do exemplo de esquema Zona de destino de migração do CAF.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: a3d697fcc6ef991bec80df02addcac5d4243005a
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876497"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Descrição geral do exemplo de esquema Zona de destino de migração do Microsoft Cloud Adoption Framework para o Azure

O esquema Zona de destino de migração do Microsoft Cloud Adoption Framework para o Azure (CAF) é um conjunto de infraestrutura que o ajuda a preparar-se para migrar a sua primeira carga de trabalho e a gerir o seu património de cloud em linha com o CAF.

O exemplo de esquema [Fundação do CAF](../caf-foundation/index.md) expande este exemplo.

## <a name="architecture"></a>Arquitetura

O exemplo de esquema Zona de destino de migração do CAF implementa recursos de infraestrutura essenciais no Azure que as organizações podem utilizar para preparar as respetivas subscrições para migrar máquinas virtuais. Também ajuda a aplicar os controlos de governação necessários para gerir os respetivos patrimónios de cloud. Este exemplo irá implementar e impor recursos, políticas e modelos que permitirão a uma organização começar a utilizar o Azure com toda a confiança.

![Zona de destino de migração do CAF. A imagem descreve o que é instalado como parte da documentação de orientação do CAF para criar uma zona de destino inicial ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

Este ambiente é composto por vários serviços do Azure utilizados para fornecer uma governação segura, completamente monitorizada e preparada para empresas. Este ambiente é composto por:

- Uma instância do [Azure Key Vault](../../../../key-vault/key-vault-whatis.md) utilizada para alojar segredos utilizados para os Certificados, Chaves e Segredos implementados no ambiente de serviços partilhados
- Uma implementação do [Log Analytics](../../../../azure-monitor/overview.md) para garantir que todos os serviços e ações são registados numa localização central desde o momento em que inicia a sua migração
- Uma implementação do [Centro de Segurança do Azure](../../../../security-center/security-center-intro.md) (versão standard) que fornece proteção contra ameaças para as suas cargas de trabalho migradas.
- Uma implementação da [Rede Virtual do Azure](../../../../virtual-network/virtual-networks-overview.md) para fornecer uma rede isolada e sub-redes para a sua máquina virtual.
- Uma implementação do [Projeto do Azure Migrate](../../../..//migrate/migrate-overview.md) para obter capacidades de deteção e avaliação. Estamos a adicionar as ferramentas para permitir a Avaliação de servidores, Migração de servidores, Avaliação de bases de dados e Migração de Bases de dados.  


Todos estes elementos obedecem às práticas comprovadas publicados no [Centro de Arquitetura do Azure - Arquiteturas de Referência](/azure/architecture/reference-architectures/).

> [!NOTE]
> O esquema Migração do CAF estabelece uma zona de destino para as cargas de trabalho. Continua a ter de realizar a avaliação e migração das suas Máquinas Virtuais/Bases de Dados sobre esta arquitetura base.

Para obter mais informações, veja o artigo [Microsoft Cloud Adoption Framework para o Azure - Migração](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Passos seguintes

Analisou a descrição geral e a arquitetura do exemplo de esquema Zona de destino de migração do CAF.

> [!div class="nextstepaction"]
>  [Esquema Zona de destino de migração do CAF - Passos de implementação](./deploy.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).