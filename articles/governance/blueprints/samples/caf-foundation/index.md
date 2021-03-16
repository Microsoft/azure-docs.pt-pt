---
title: Descrição geral do exemplo de esquema Fundação do CAF
description: Descrição geral e arquitetura do exemplo de esquema da Fundação do Framework de Adoção da Cloud (CAF) para o Azure.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 43c962ca472d2933e2f1add01b22b5fc969163fe
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471727"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Descrição geral do exemplo de esquema Fundação do Microsoft Cloud Adoption Framework para o Azure

O esquema Fundação do Microsoft Cloud Adoption Framework para o Azure (CAF) implementa um conjunto de controlos de política e recursos de infraestrutura fundamentais necessários para a sua primeira aplicação do Azure de grau de produção. Este esquema de fundação baseia-se no padrão recomendado encontrado no CAF.

## <a name="architecture"></a>Arquitetura

O exemplo de esquema Fundação do CAF implementa recursos de infraestrutura recomendados no Azure que as organizações podem utilizar para aplicar os controlos de fundação que são necessários para gerir o respetivo património de cloud. Este exemplo irá implementar e impor recursos, políticas e modelos que permitirão a uma organização começar a utilizar o Azure com toda a confiança.

:::image type="complex" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="Fundação do C A F. A imagem descreve o que é instalado como parte da documentação de orientação do C A F para criar uma fundação para começar a utilizar o Azure." border="false":::
   Descreve uma arquitetura do Azure que é obtida através da implementação do esquema da Fundação do C A F.  Aplica-se a uma subscrição com grupos de recursos constituída por uma conta de armazenamento para armazenar registos e o Log Analytics configurado para armazenar na conta de armazenamento. Também mostra o Azure Key Vault configurado com a configuração padrão do Centro de Segurança do Azure. Todas estas infraestruturas fundamentais são acedidas através do Azure Active Directory e impostas com o Azure Policy.     
:::image-end:::

Esta implementação incorpora vários serviços do Azure utilizados para fornecer uma fundação segura, totalmente monitorizada e pronta para empresas. Este ambiente é composto por:

- Uma instância do [Azure Key Vault](../../../../key-vault/general/overview.md) utilizada para alojar segredos utilizados para as VMs implementadas no ambiente de serviços partilhados
- Uma implementação do [Log Analytics](../../../../azure-monitor/overview.md) para garantir que todos os serviços e ações são registados numa localização central desde o momento em que inicia a implementação segura em [Contas de Armazenamento](../../../../storage/common/storage-introduction.md) para registo de diagnósticos
- Uma implementação do [Centro de Segurança do Azure](../../../../security-center/security-center-introduction.md) (versão standard) que fornece proteção contra ameaças para as suas cargas de trabalho migradas
- O esquema também define e implementa as definições do [Azure Policy](../../../policy/overview.md):
  - Definições do Policy:
    - Identificação (CostCenter) aplicada a grupos de recursos
    - Acrescentar recursos no grupo de recursos com a etiqueta CostCenter
    - Região do Azure permitida para Recursos e Grupos de Recursos
    - SKUs de Conta de Armazenamento permitidos (escolha durante a implementação)
    - SKUs de VM do Azure permitidos (escolha durante a implementação)
    - Pedir a implementação do Observador de Rede 
    - Pedir a Encriptação de transferência segura da Conta de Armazenamento do Azure
    - Negar tipos de recurso (escolha durante a implementação)  
  - Iniciativas do Policy:
    - Ativar a monitorização no Centro de Segurança do Azure (mais de 100 definições de políticas)

Todos estes elementos obedecem às práticas comprovadas publicados no [Centro de Arquitetura do Azure - Arquiteturas de Referência](/azure/architecture/reference-architectures/).

> [!NOTE]
> O esquema Fundação do CAF estabelece uma arquitetura base para as cargas de trabalho.
> Continua a ser necessário implementar as cargas de trabalho atrás desta arquitetura básica.

Para obter mais informações, veja o artigo [Microsoft Cloud Adoption Framework para o Azure - Preparado](/azure/cloud-adoption-framework/ready/).

## <a name="next-steps"></a>Passos seguintes

Analisou a descrição geral e a arquitetura do exemplo de esquema Fundação do CAF.

> [!div class="nextstepaction"]
> [Esquema Fundação do CAF - Passos de implementação](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
