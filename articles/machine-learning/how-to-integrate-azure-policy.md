---
title: Auditoria e gestão do cumprimento das políticas
titleSuffix: Azure Machine Learning
description: Aprenda a usar a Política Azure para usar políticas incorporadas para a aprendizagem automática Azure.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: d371ea077b479ad2256e782dadd5d037f53004f2
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900133"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Auditar e gerir a Azure Machine Learning usando a Política Azure

[A Azure Policy](/azure/governance/policy) é uma ferramenta de governação que lhe permite garantir que os recursos da Azure estão em conformidade com as suas políticas. Com a Azure Machine Learning, pode atribuir as seguintes políticas:

* **Chave gerida pelo cliente** : Auditar ou impor se os espaços de trabalho devem utilizar uma chave gerida pelo cliente.
* **Ligação privada** : Audite se os espaços de trabalho utilizam um ponto final privado para comunicar com uma rede virtual.

As políticas podem ser definidas em diferentes âmbitos, tais como a nível de subscrição ou grupo de recursos. Para mais informações, consulte a documentação da [Política Azure.](/azure/governance/policy/overview)

## <a name="built-in-policies"></a>Políticas incorporadas

A Azure Machine Learning fornece um conjunto de políticas que você pode usar para cenários comuns com Azure Machine Learning. Pode atribuir estas definições de política à subscrição existente ou usá-las como base para criar as suas próprias definições personalizadas. Para obter uma lista completa das políticas incorporadas para a aprendizagem automática Azure, consulte [as políticas incorporadas para a aprendizagem automática Azure.](/azure/governance/policy/samples/built-in-policies#machine-learning)

Para visualizar as definições políticas incorporadas relacionadas com a Azure Machine Learning, utilize os seguintes passos:

1. Ir para __a Política Azure__ no [portal Azure](https://portal.azure.com).
1. Selecione __Definições__ .
1. Para __tipo__ , selecione _Built-in_ , e para __categoria__ , selecione __Machine Learning__ .

A partir daqui, pode selecionar definições de política para as visualizar. Ao visualizar uma definição, pode utilizar o link __Atribua__ para atribuir a política a um âmbito específico e configurar os parâmetros para a política. Para mais informações, consulte [Atribuir uma política - portal](/azure/governance/policy/assign-policy-portal).

Também pode atribuir políticas utilizando [Azure PowerShell,](/azure/governance/policy/assign-policy-powershell) [Azure CLI,](https://docs.microsoft.com/azure/governance/policy/assign-policy-azurecli)e [modelos](/azure/governance/policy/assign-policy-template).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Encriptação de espaços de trabalho com chave gerida pelo cliente

Controla se os espaços de trabalho devem ser encriptados com uma chave gerida pelo cliente (CMK), ou utilizando uma chave gerida pela Microsoft para encriptar métricas e metadados. Para obter mais informações sobre a utilização da CMK, consulte a secção [DB do Azure Cosmos](concept-enterprise-security.md#azure-cosmos-db) do artigo de segurança da empresa.

Para configurar esta política, desafie o parâmetro de efeito para __auditar__ ou __negar__ . Se estiver definido para __auditoria,__ pode criar espaços de trabalho sem CMK e um evento de aviso é criado no registo de atividade.

Se a política estiver definida para __negar,__ então não pode criar um espaço de trabalho a menos que especifique um CMK. Tentar criar um espaço de trabalho sem CMK resulta num erro semelhante `Resource 'clustername' was disallowed by policy` e cria um erro no registo de atividade. O identificador de política também é devolvido como parte deste erro.

## <a name="workspaces-should-use-private-link"></a>Os espaços de trabalho devem usar link privado

Controla se os espaços de trabalho devem utilizar o Azure Private Link para comunicar com a Rede Virtual Azure. Para obter mais informações sobre a utilização de link privado, consulte [o link privado Configure para um espaço de trabalho.](how-to-configure-private-link.md)

Para configurar esta política, desafie o parâmetro de efeito para a __auditoria__ . Se criar um espaço de trabalho sem utilizar ligações privadas, é criado um evento de aviso no registo de atividades.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do Azure Policy](/azure/governance/policy/overview)
* [Políticas incorporadas para a aprendizagem automática Azure](policy-reference.md)
* [Trabalhar com políticas de segurança com o Azure Security Center](/azure/security-center/tutorial-security-policy)