---
title: Segurança e governação empresariais
titleSuffix: Azure Machine Learning
description: 'Utilize de forma segura o Azure Machine Learning: autenticação, autorização, segurança da rede, encriptação de dados e monitorização.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992034"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Segurança e governação da empresa para a Azure Machine Learning

Neste artigo, você vai aprender sobre as funcionalidades de segurança e governação disponíveis para Azure Machine Learning. Estas funcionalidades são úteis para administradores, DevOps e MLOps que pretendem criar uma configuração segura que esteja em conformidade com as políticas das suas empresas. Com a Azure Machine Learning e a plataforma Azure, pode:

* Restringir o acesso a recursos e operações por conta de utilizador ou grupos
* Restringir as comunicações de rede de entrada e saída
* Criptografe os dados em trânsito e em repouso
* Scaneie para vulnerabilidades
* Aplicar e auditar políticas de configuração

## <a name="restrict-access-to-resources-and-operations"></a>Restringir o acesso a recursos e operações

[O Azure Ative Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) é o prestador de serviços de identidade da Azure Machine Learning. Permite-lhe criar e gerir os objetos de segurança (utilizador, grupo, principal de serviço e identidade gerida) que são utilizados para _autenticar_ os recursos da Azure. A autenticação multi-factor é suportada se o Azure AD estiver configurado para a utilizar.

Aqui está o processo de autenticação para Azure Machine Learning usando a autenticação de vários fatores em Azure AD:

1. O cliente assina no Azure AD e recebe um token Azure Resource Manager.
1. O cliente apresenta o símbolo ao Azure Resource Manager e a toda a Azure Machine Learning.
1. A Azure Machine Learning fornece um token de serviço de machine learning para o alvo do computação do utilizador (por exemplo, cluster de computação Azure Machine Learning). Este token é utilizado pelo alvo de computação do utilizador para voltar a ligar para o serviço machine learning após a execução estar concluída. O âmbito é limitado ao espaço de trabalho.

[![Autenticação em Aprendizagem automática Azure](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Cada espaço de trabalho tem uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) associada ao sistema que tem o mesmo nome que o espaço de trabalho. Esta identidade gerida é usada para aceder de forma segura aos recursos utilizados pelo espaço de trabalho. Tem as seguintes permissões Azure RBAC sobre recursos anexados:

| Recurso | Permissões |
| ----- | ----- |
| Área de trabalho | Contribuinte |
| Conta de armazenamento | Contribuinte de Dados do Armazenamento de Blobs |
| Key Vault | Acesso a todas as chaves, segredos, certificados |
| Registo de Contentores do Azure | Contribuinte |
| Grupo de recursos que contém o espaço de trabalho | Contribuinte |
| Grupo de recursos que contém o cofre chave (se diferente daquele que contém o espaço de trabalho) | Contribuinte |

Não recomendamos que os administradores revoguem o acesso da identidade gerida aos recursos mencionados na tabela anterior. Pode restaurar o acesso utilizando a [operação das teclas de ressínc.](how-to-change-storage-access-key.md)

O Azure Machine Learning também cria uma aplicação adicional (o nome começa com `aml-` ou ) com acesso ao `Microsoft-AzureML-Support-App-` nível do contribuinte na sua subscrição para cada região do espaço de trabalho. Por exemplo, se tiver um espaço de trabalho no Leste dos EUA e um no Norte da Europa na mesma subscrição, verá duas destas aplicações. Estas aplicações permitem ao Azure Machine Learning ajudá-lo a gerir os recursos computacional.

Também pode configurar as suas próprias identidades geridas para uso com máquinas virtuais Azure e cluster de cálculo de aprendizagem automática Azure. Com um VM, a identidade gerida pode ser usada para aceder ao seu espaço de trabalho a partir do SDK, em vez da conta AZure AD do utilizador individual. Com um cluster de cálculo, a identidade gerida é usada para aceder a recursos como as lojas de dados seguras a que o utilizador que executa o trabalho de formação pode não ter acesso. Para obter mais informações, consulte [o espaço de trabalho autenticação para aprendizagem automática Azure.](how-to-setup-authentication.md)

> [!TIP]
> Existem algumas exceções à utilização de Azure AD e Azure RBAC no Azure Machine Learning:
> * Pode opcionalmente permitir o acesso do __SSH__ a recursos de computação, tais como a azure machine learning computação instância e cluster de computação. O acesso ao SSH baseia-se em pares de chaves públicos/privados, não em Azure AD. O acesso ao SSH não é regido pelo Azure RBAC.
> * Pode autenticar-se em modelos implementados como serviços web (pontos finais de inferência) utilizando a __autenticação__ baseada em chaves __ou__ símbolos. As teclas são cordas estáticas, enquanto as fichas são recuperadas usando um objeto de segurança Azure AD. Para obter mais informações, consulte a [autenticação configurar para modelos implementados como serviço web.](how-to-authenticate-web-service.md)

Para obter mais informações, veja os seguintes artigos:
* [Autenticação para espaço de trabalho de aprendizagem automática Azure](how-to-setup-authentication.md)
* [Gerir o acesso à Aprendizagem automática Azure](how-to-assign-roles.md)
* [Ligar aos serviços de armazenamento](how-to-access-data.md)
* [Use o cofre da chave Azure para segredos durante o treino](how-to-use-secrets-in-runs.md)
* [Use identidade gerida a Azure AD com Azure Machine Learning](how-to-use-managed-identities.md)
* [Use a identidade gerida a Azure AD com o seu serviço web](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Segurança da rede e isolamento

Para restringir o acesso à rede aos recursos de aprendizagem automática Azure, pode utilizar [a Rede Virtual Azure (VNet)](../virtual-network/virtual-networks-overview.md). Os VNets permitem criar ambientes de rede que estejam parcialmente, ou totalmente, isolados da internet pública. Isto reduz a superfície de ataque para a sua solução, bem como as chances de exfiltração de dados.

Pode utilizar uma porta de entrada de rede privada virtual (VPN) para ligar clientes individuais, ou a sua própria rede, ao VNet

O espaço de trabalho Azure Machine Learning pode usar [o Azure Private Link](../private-link/private-link-overview.md) para criar um ponto final privado atrás do VNet. Isto fornece um conjunto de endereços IP privados que podem ser usados para aceder ao espaço de trabalho a partir do VNet. Alguns dos serviços em que o Azure Machine Learning depende também podem utilizar o Azure Private Link, mas alguns dependem de grupos de segurança de rede ou de encaminhamento definido pelo utilizador.

Para obter mais informações, consulte os documentos seguintes:

* [Isolamento de rede virtual e visão geral da privacidade](how-to-network-security-overview.md)
* [Recursos de área de trabalho seguros](how-to-secure-workspace-vnet.md)
* [Ambiente de preparação seguro](how-to-secure-training-vnet.md)
* [Ambiente de inferência seguro](how-to-secure-inferencing-vnet.md)
* [Use o estúdio numa rede virtual segura](how-to-enable-studio-virtual-network.md)
* [Utilizar DNS personalizado](how-to-custom-dns.md)
* [Configurar a firewall](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Encriptação de dados

A Azure Machine Learning utiliza uma variedade de recursos computacional e lojas de dados na plataforma Azure. Para saber mais sobre como cada um destes suporta encriptação de dados em repouso e em trânsito, consulte [encriptação de dados com Azure Machine Learning](concept-data-encryption.md).

Ao implementar modelos como serviços web, pode permitir que a segurança da camada de transporte (TLS) criptografe dados em trânsito. Para mais informações, consulte [Configurar um serviço web seguro.](how-to-secure-web-service.md)

## <a name="vulnerability-scanning"></a>Análise de vulnerabilidades

[O Azure Security Center](../security-center/security-center-introduction.md) fornece uma gestão unificada de segurança e proteção avançada de ameaças através de cargas de cloud híbridas. Para a aprendizagem automática Azure, deve permitir a digitalização do seu recurso de registo de [contentores Azure](../container-registry/container-registry-intro.md) e dos recursos do Serviço Azure Kubernetes. Para obter mais informações, consulte [a imagem do Registo de Contentores Azure pelo Security Center](../security-center/defender-for-container-registries-introduction.md) e a [integração dos Serviços Azure Kubernetes com o Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Auditar e gerir a conformidade

[A Azure Policy](../governance/policy/index.yml) é uma ferramenta de governação que lhe permite garantir que os recursos da Azure estão em conformidade com as suas políticas. Pode definir políticas para permitir ou impor configurações específicas, tais como se o seu espaço de trabalho Azure Machine Learning utiliza um ponto final privado. Para obter mais informações sobre a Política Azure, consulte a documentação da [Política Azure](../governance/policy/overview.md). Para obter mais informações sobre as políticas específicas do Azure Machine Learning, consulte [a Auditoria e gerencie o cumprimento da Política Azure.](how-to-integrate-azure-policy.md)

## <a name="next-steps"></a>Próximos passos

* [Serviços web secure Azure Machine Learning com TLS](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implementado como um serviço web](how-to-consume-web-service.md)
* [Use a azure machine learning com firewall Azure](how-to-access-azureml-behind-firewall.md)
* [Use a azure machine learning com rede virtual Azure](how-to-network-security-overview.md)
* [Encriptação de dados em repouso e em trânsito](concept-data-encryption.md)
* [Construir uma recomendação em tempo real API em Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
