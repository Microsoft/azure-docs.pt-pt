---
title: Descrição geral do exemplo de esquema Zona de destino de migração do CAF
description: Descrição geral e arquitetura do exemplo de esquema da zona de destino da Migração do Framework de Adoção da Cloud (CAF) para o Azure.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 9cbf1c1a4ac4cf7a58ed27ec05cc77997da1366d
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472197"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Descrição geral do exemplo de esquema Zona de destino de migração do Microsoft Cloud Adoption Framework para o Azure

O esquema Zona de destino de migração do Microsoft Cloud Adoption Framework para o Azure (CAF) é um conjunto de infraestrutura que o ajuda a preparar-se para migrar a sua primeira carga de trabalho e a gerir o seu património de cloud em linha com o CAF.

O exemplo de esquema [Fundação do CAF](../caf-foundation/index.md) expande este exemplo.

## <a name="architecture"></a>Arquitetura

O exemplo de esquema Zona de destino de migração do CAF implementa recursos de infraestrutura essenciais no Azure que as organizações podem utilizar para preparar as respetivas subscrições para migrar máquinas virtuais. Também ajuda a aplicar os controlos de governação necessários para gerir os respetivos patrimónios de cloud. Este exemplo irá implementar e impor recursos, políticas e modelos que permitirão a uma organização começar a utilizar o Azure com toda a confiança.

:::image type="complex" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="Zona de destino de Migração do C A F. A imagem descreve o que é instalado como parte da documentação de orientação do C A F para criar uma zona de destino inicial." border="false":::
   Descreve uma arquitetura do Azure que é obtida através da implementação do esquema de migração do C A F.  Aplica-se a uma subscrição com grupos de recursos constituída por uma rede virtual do Azure, uma conta de armazenamento para armazenar registos e o Log Analytics configurado para armazenar na conta de armazenamento.  Também mostra o Azure Key Vault configurado e a configuração inicial do Azure Migrate criada.  Todas estas infraestruturas fundamentais são acedidas através do Azure Active Directory.     
:::image-end:::

Este ambiente é composto por vários serviços do Azure utilizados para fornecer uma governação segura, completamente monitorizada e preparada para empresas. Este ambiente é composto por:

- Uma instância do [Azure Key Vault](../../../../key-vault/general/overview.md) utilizada para alojar segredos utilizados para os Certificados, Chaves e Segredos implementados no ambiente de serviços partilhados
- Uma implementação do [Log Analytics](../../../../azure-monitor/overview.md) para garantir que todos os serviços e ações são registados numa localização central desde o momento em que inicia a sua migração
- Uma implementação da [Rede Virtual do Azure](../../../../virtual-network/virtual-networks-overview.md) para fornecer uma rede isolada e sub-redes para a sua máquina virtual.
- Uma implementação do [Projeto do Azure Migrate](../../../../migrate/migrate-services-overview.md) para obter capacidades de deteção e avaliação. Estamos a adicionar as ferramentas para permitir a Avaliação de servidores, Migração de servidores, Avaliação de bases de dados e Migração de Bases de dados.  


Todos estes elementos obedecem às práticas comprovadas publicados no [Centro de Arquitetura do Azure - Arquiteturas de Referência](/azure/architecture/reference-architectures/).

> [!NOTE]
> O esquema Migração do CAF estabelece uma zona de destino para as cargas de trabalho. Continua a ter de realizar a avaliação e migração das suas Máquinas Virtuais/Bases de Dados sobre esta arquitetura base.

Para obter mais informações, veja o artigo [Microsoft Cloud Adoption Framework para o Azure - Migração](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Passos seguintes

Analisou a descrição geral e a arquitetura do exemplo de esquema Zona de destino de migração do CAF.

> [!div class="nextstepaction"]
> [Esquema Zona de destino de Migração do CAF - Passos para implementação](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
