---
title: Auditoria e gestão do cumprimento das políticas
titleSuffix: Azure Machine Learning
description: Aprenda a usar a Azure Policy para utilizar políticas incorporadas para a Azure Machine Learning para garantir que os seus espaços de trabalho estão em conformidade com os seus requisitos.
author: aashishb
ms.author: aashishb
ms.date: 03/12/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 21b07130e99ad4fac9a0a9b2d11aca852a1f205f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584317"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Auditar e gerir a Azure Machine Learning usando a Política Azure

[A Azure Policy](../governance/policy/index.yml) é uma ferramenta de governação que lhe permite garantir que os recursos da Azure estão em conformidade com as suas políticas. Com a Azure Machine Learning, pode atribuir as seguintes políticas:

* **Chave gerida pelo cliente**: Auditar ou impor se os espaços de trabalho devem utilizar uma chave gerida pelo cliente.
* **Ligação privada**: Auditar ou impor se os espaços de trabalho utilizam um ponto final privado para comunicar com uma rede virtual.
* **Ponto final privado**: Configurar a sub-rede da Rede Virtual Azure onde deve ser criado o ponto final privado.
* **Zona privada do DNS**: Configurar a zona privada de DNS para utilizar para o link privado.

As políticas podem ser definidas em diferentes âmbitos, tais como a nível de subscrição ou grupo de recursos. Para mais informações, consulte a documentação da [Política Azure.](../governance/policy/overview.md)

## <a name="built-in-policies"></a>Políticas incorporadas

A Azure Machine Learning fornece um conjunto de políticas que você pode usar para cenários comuns com Azure Machine Learning. Pode atribuir estas definições de política à subscrição existente ou usá-las como base para criar as suas próprias definições personalizadas. Para obter uma lista completa das políticas incorporadas para a aprendizagem automática Azure, consulte [as políticas incorporadas para a aprendizagem automática Azure.](../governance/policy/samples/built-in-policies.md#machine-learning)

Para visualizar as definições políticas incorporadas relacionadas com a Azure Machine Learning, utilize os seguintes passos:

1. Ir para __a Política Azure__ no [portal Azure](https://portal.azure.com).
1. Selecione __Definições__.
1. Para __tipo__, selecione _Built-in_, e para __categoria__, selecione __Machine Learning__.

A partir daqui, pode selecionar definições de política para as visualizar. Ao visualizar uma definição, pode utilizar o link __Atribua__ para atribuir a política a um âmbito específico e configurar os parâmetros para a política. Para mais informações, consulte [Atribuir uma política - portal](../governance/policy/assign-policy-portal.md).

Também pode atribuir políticas utilizando [Azure PowerShell,](../governance/policy/assign-policy-powershell.md) [Azure CLI,](../governance/policy/assign-policy-azurecli.md)e [modelos](../governance/policy/assign-policy-template.md).

## <a name="workspace-encryption-with-customer-managed-key"></a>Encriptação do espaço de trabalho com chave gerida pelo cliente

Controla se um espaço de trabalho deve ser encriptado com uma chave gerida pelo cliente ou utilizando uma chave gerida pela Microsoft para encriptar métricas e metadados. Para obter mais informações sobre a utilização da chave gerida pelo cliente, consulte a secção DB do [Azure Cosmos](concept-data-encryption.md#azure-cosmos-db) do artigo de encriptação de dados.

Para configurar esta política, desafie o parâmetro de efeito para __auditar__ ou __negar__. Se estiver definido para __a auditoria,__ pode criar um espaço de trabalho sem uma chave gerida pelo cliente e um evento de aviso é criado no registo de atividade.

Se a política estiver definida para __negar,__ então não pode criar um espaço de trabalho a menos que especifique uma chave gerida pelo cliente. Tentar criar um espaço de trabalho sem uma chave gerida pelo cliente resulta num erro semelhante `Resource 'clustername' was disallowed by policy` e cria um erro no registo de atividade. O identificador de política também é devolvido como parte deste erro.

## <a name="workspace-should-use-private-link"></a>O espaço de trabalho deve usar link privado

Controla se um espaço de trabalho deve usar o Azure Private Link para comunicar com a Rede Virtual Azure. Para obter mais informações sobre a utilização de link privado, consulte [o link privado Configure para um espaço de trabalho.](how-to-configure-private-link.md)

Para configurar esta política, desafie o parâmetro de efeito para __auditar__ ou __negar__. Se estiver definido para __a auditoria,__ pode criar um espaço de trabalho sem utilizar link privado e um evento de aviso é criado no registo de atividades.

Se a política estiver definida para __negar,__ então não pode criar um espaço de trabalho a menos que utilize um link privado. Tentar criar um espaço de trabalho sem uma ligação privada resulta num erro. O erro também é registado no registo de atividade. O identificador de política é devolvido como parte deste erro.

## <a name="workspace-should-use-private-endpoint"></a>Espaço de trabalho deve usar ponto final privado

Configura um espaço de trabalho para criar um ponto final privado dentro da sub-rede especificada de uma Rede Virtual Azure.

Para configurar esta política, desloque o parâmetro de efeito para __ImplementarIfNotExists__. Descreva o __privateEndpointSubnetID__ para o ID do Gestor de Recursos Azure da sub-rede.
## <a name="workspace-should-use-private-dns-zones"></a>Espaço de trabalho deve usar zonas privadas de DNS

Configura um espaço de trabalho para usar uma zona privada de DNS, sobressaíndo a resolução padrão de DNS para um ponto final privado.

Para configurar esta política, desloque o parâmetro de efeito para __ImplementarIfNotExists__. Desconfiem do __PrivateDnsZoneId__ para o ID do Gestor de Recursos Azure da zona privada de DNS para utilizar. 

## <a name="next-steps"></a>Passos seguintes

* [Documentação do Azure Policy](../governance/policy/overview.md)
* [Políticas incorporadas para a aprendizagem automática Azure](policy-reference.md)
* [Trabalhar com políticas de segurança com o Azure Security Center](../security-center/tutorial-security-policy.md)