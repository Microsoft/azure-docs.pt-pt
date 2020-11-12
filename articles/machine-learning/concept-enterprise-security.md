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
ms.date: 09/09/2020
ms.openlocfilehash: eaaedc4404419e6028250a994077dd3df20b83d4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539656"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Segurança e governação da empresa para a Azure Machine Learning

Neste artigo, você vai aprender sobre as funcionalidades de segurança disponíveis para Azure Machine Learning.

Quando se utiliza um serviço de cloud, a melhor prática é restringir o acesso apenas aos utilizadores que dele necessitem. Comece por compreender o modelo de autenticação e autorização utilizado pelo serviço. Também pode querer restringir o acesso à rede ou juntar recursos de forma segura na sua rede no local com a nuvem. A encriptação de dados também é vital, tanto em repouso como enquanto os dados se movem entre os serviços. Também pode querer criar políticas para impor determinadas configurações ou registar-se quando são criadas configurações não conformes. Finalmente, é necessário ser capaz de monitorizar o serviço e produzir um registo de auditoria de toda a atividade.

> [!NOTE]
> A informação neste artigo funciona com a versão 1.0.83.1 ou superior da Azure Machine Learning Python.

## <a name="authentication--authorization"></a>Autenticação & autorização

A maioria dos recursos de autenticação para a Azure Machine Learning utilizam o Azure Ative Directory (Azure AD) para autenticação e controlo de acesso baseado em funções (Azure RBAC) para autorização. As exceções a isto são:

* __SSH__ : Pode permitir o acesso do SSH a alguns recursos de computação, tais como a instância computacional Azure Machine Learning. O acesso SSH utiliza a autenticação baseada em chaves. Para obter mais informações sobre a criação de teclas SSH, consulte [criar e gerir as teclas SSH](../virtual-machines/linux/create-ssh-keys-detailed.md). Para obter informações sobre como permitir o acesso ao SSH, consulte [criar e gerir a instância de cálculo Azure Machine Learning](how-to-create-manage-compute-instance.md).
* __Modelos implementados como serviços web__ : As implementações de serviço web podem utilizar o controlo de acesso baseado em __chaves__ ou __símbolos.__ As chaves são cordas estáticas. As fichas são recuperadas utilizando uma conta AZure AD. Para obter mais informações, consulte a [autenticação configurar para modelos implementados como serviço web.](how-to-authenticate-web-service.md)

Os serviços específicos em que a Azure Machine Learning conta, como os serviços de armazenamento de dados Azure, têm os seus próprios métodos de autenticação e autorização. Para obter mais informações sobre a autenticação dos serviços de armazenamento, consulte [o Connect to storage services](how-to-access-data.md).

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

A autenticação multi-factor é suportada se o Azure Ative Directory (Azure AD) estiver configurado para o utilizar. Aqui está o processo de autenticação:

1. O cliente assina no Azure AD e recebe um token Azure Resource Manager.  Os utilizadores e os principais serviços são totalmente suportados.
1. O cliente apresenta o símbolo ao Azure Resource Manager e a toda a Azure Machine Learning.
1. O serviço Machine Learning fornece um sinal de serviço de Machine Learning para o alvo do computação do utilizador (por exemplo, Machine Learning Compute). Este token é utilizado pelo alvo de computação do utilizador para voltar a ligar para o serviço machine learning após a execução estar concluída. O âmbito é limitado ao espaço de trabalho.

[![Autenticação em Aprendizagem automática Azure](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Para obter mais informações, consulte [o espaço de trabalho autenticação para aprendizagem automática Azure.](how-to-setup-authentication.md)

### <a name="azure-rbac"></a>RBAC do Azure

Você pode criar vários espaços de trabalho, e cada espaço de trabalho pode ser compartilhado por várias pessoas. Pode controlar quais as funcionalidades ou operações a que os utilizadores do espaço de trabalho podem aceder atribuindo a sua conta AD AD a funções de Azure RBAC. Seguem-se os papéis incorporados:

* Proprietário
* Contribuinte
* Leitor

Os proprietários e colaboradores podem usar todos os alvos de cálculo e lojas de dados que estão anexados ao espaço de trabalho.  

A tabela a seguir enumera algumas das principais operações de Aprendizagem automática do Azure e as funções que as podem desempenhar:

| Operação Azure Machine Learning | Proprietário | Contribuinte | Leitor |
| ---- |:----:|:----:|:----:|
| Criar área de trabalho | ✓ | ✓ | |
| Partilhar espaço de trabalho | ✓ | |  |
| Criar alvo de cálculo | ✓ | ✓ | |
| Anexar o alvo do cálculo | ✓ | ✓ | |
| Anexar lojas de dados | ✓ | ✓ | |
| Experiência de execução | ✓ | ✓ | |
| Ver runs/métricas | ✓ | ✓ | ✓ |
| Registar o modelo | ✓ | ✓ | |
| Criar imagem | ✓ | ✓ | |
| Implementar serviço web | ✓ | ✓ | |
| Ver modelos/imagens | ✓ | ✓ | ✓ |
| Serviço web de chamada | ✓ | ✓ | ✓ |

Se as funções incorporadas não satisfaçam as suas necessidades, pode criar papéis personalizados. As funções personalizadas controlam todas as operações dentro de um espaço de trabalho, tais como criar um cálculo, submeter uma corrida, registar uma loja de dados ou implementar um modelo. As funções personalizadas podem ter lido, escrito ou apagado permissões sobre os vários recursos de um espaço de trabalho, tais como clusters, datastores, modelos e pontos finais. Pode disponibilizar a função a um nível específico do espaço de trabalho, a um nível específico de grupo de recursos ou a um nível de subscrição específico. Para obter mais informações, consulte [Gerir os utilizadores e as funções num espaço de trabalho de Aprendizagem automática Azure.](how-to-assign-roles.md)

> [!IMPORTANT]
> A Azure Machine Learning depende de outros serviços Azure Blob e Azure Kubernetes Services. Cada serviço Azure tem as suas próprias configurações Azure RBAC. Para atingir o nível de controlo de acesso pretendido, poderá ter de aplicar as configurações Azure RBAC para Azure Machine Learning e as dos serviços utilizados com a Azure Machine Learning.

> [!WARNING]
> O Azure Machine Learning é apoiado com a colaboração entre negócios do Azure Ative Directory, mas não é atualmente apoiado com a colaboração entre negócios e consumidores do Azure Ative Directory.

### <a name="managed-identities"></a>Identidades geridas

Cada espaço de trabalho também tem uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) associada ao sistema que tem o mesmo nome que o espaço de trabalho. A identidade gerida é usada para aceder de forma segura aos recursos utilizados pelo espaço de trabalho. Tem as seguintes permissões sobre os recursos anexados:

| Recurso | Permissões |
| ----- | ----- |
| Área de trabalho | Contribuinte |
| Conta de armazenamento | Contribuinte de Dados do Armazenamento de Blobs |
| Key Vault | Acesso a todas as chaves, segredos, certificados |
| Registo de Contentores do Azure | Contribuinte |
| Grupo de recursos que contém o espaço de trabalho | Contribuinte |
| Grupo de recursos que contém o cofre chave (se diferente daquele que contém o espaço de trabalho) | Contribuinte |

Não recomendamos que os administradores revoguem o acesso da identidade gerida aos recursos mencionados na tabela anterior. Pode restaurar o acesso utilizando a operação das teclas de ressínc.

O Azure Machine Learning cria uma aplicação adicional (o nome começa com `aml-` ou ) com acesso ao `Microsoft-AzureML-Support-App-` nível do contribuinte na sua subscrição para cada região do espaço de trabalho. Por exemplo, se tiver um espaço de trabalho no Leste dos EUA e um no Norte da Europa na mesma subscrição, verá duas destas aplicações. Estas aplicações permitem ao Azure Machine Learning ajudá-lo a gerir os recursos computacional.

Opcionalmente, pode configurar as suas próprias identidades geridas para uso com máquinas virtuais Azure e cluster de cálculo de aprendizagem automática Azure. Com um VM, a identidade gerida pode ser usada para aceder ao seu espaço de trabalho a partir do SDK, em vez da conta AZure AD do utilizador individual. Com um cluster de cálculo, a identidade gerida é usada para aceder a recursos como as lojas de dados seguras a que o utilizador que executa o trabalho de formação pode não ter acesso. Para obter mais informações, consulte [o espaço de trabalho autenticação para aprendizagem automática Azure.](how-to-setup-authentication.md)

## <a name="network-security-and-isolation"></a>Segurança da rede e isolamento

Para restringir o acesso físico aos recursos de Aprendizagem automática Azure, pode utilizar a Rede Virtual Azure (VNet). Os VNets permitem criar ambientes de rede que estejam parcialmente, ou totalmente, isolados da internet pública. Isto reduz a superfície de ataque para a sua solução, bem como as chances de exfiltração de dados.

Para mais informações, consulte [o isolamento da rede virtual e a visão geral da privacidade.](how-to-network-security-overview.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Encriptação de dados

A Azure Machine Learning utiliza uma variedade de recursos computacional e lojas de dados. Para saber mais sobre como cada um destes suporta encriptação de dados em repouso e em trânsito, consulte [encriptação de dados com Azure Machine Learning](concept-data-encryption.md).

### <a name="microsoft-generated-data"></a>Dados gerados pela Microsoft

Ao utilizar serviços como Machine Learning Automatizado, a Microsoft pode gerar dados transitórios e pré-processados para a formação de vários modelos. Estes dados são armazenados numa loja de dados no seu espaço de trabalho, o que lhe permite impor controlos de acesso e encriptação adequadamente.

Também pode querer encriptar [as informações de diagnóstico registadas a partir do seu ponto final implantado](how-to-enable-app-insights.md) na sua instância Azure Application Insights.

## <a name="monitoring"></a>Monitorização

Existem vários cenários de monitorização com a Azure Machine Learning, dependendo do papel e do que está a ser monitorizado.

| Função | Monitorização a utilizar | Description |
| ---- | ----- | ----- |
| Administrador, DevOps, MLOps | [Métricas do Monitor Azure,](#azure-monitor) [log de atividade,](#activity-log) [digitalização da vulnerabilidade](#vulnerability-scanning) | Informação de nível de serviço |
| Cientista de Dados, MLOps | [Monitor corre](#monitor-runs) | Informações registadas durante os treinos |
| MLOps | [Recolher dados de modelos,](how-to-enable-data-collection.md) [Monitor com Insights de Aplicação](how-to-enable-app-insights.md) | Informação registada por modelos implementados como serviços web ou módulos IoT Edge|

### <a name="monitor-runs"></a>Monitor corre

Pode monitorizar as experiências em Azure Machine Learning, incluindo registar informações a partir dos seus scripts de treino. Esta informação pode ser vista através do SDK, Azure CLI e estúdio. Para obter mais informações, veja os seguintes artigos:

* [Iniciar, monitorizar e cancelar treinos](how-to-manage-runs.md)
* [Ativar registos](how-to-track-experiments.md)
* [Ver registos](how-to-monitor-view-training-logs.md)
* [Visualizar execuções com TensorBoard](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

Pode utilizar as métricas do Azure Monitor para visualizar e monitorizar métricas para o seu espaço de trabalho de aprendizagem de máquinas Azure. No [portal Azure,](https://portal.azure.com)selecione o seu espaço de trabalho e, em seguida, selecione **Métricas:**

[![Screenshot mostrando métricas de exemplo para um espaço de trabalho](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

As métricas incluem informações sobre execuções, implementações e registos.

Para obter mais informações, consulte [métricas no Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

### <a name="activity-log"></a>Registo de atividades

Pode ver o registo de atividade de um espaço de trabalho para ver várias operações que são realizadas no espaço de trabalho. O registo inclui informações básicas como o nome da operação, iniciador de eventos e marca de tempo.

Esta imagem mostra o registo de atividade de um espaço de trabalho:

[![Screenshot mostrando o registo de atividade de um espaço de trabalho](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Os detalhes do pedido de pontuação são armazenados em Insights de Aplicação. O Application Insights é criado na sua subscrição quando cria um espaço de trabalho. As informações registadas incluem campos como:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* Código de Estado
* RequestId
* Duração

> [!IMPORTANT]
> Algumas ações no espaço de trabalho Azure Machine Learning não registam informações no registo de atividades. Por exemplo, o início de uma formação e o registo de um modelo não são registados.
>
> Algumas destas ações aparecem na área de **Atividades** do seu espaço de trabalho, mas estas notificações não indicam quem iniciou a atividade.

### <a name="vulnerability-scanning"></a>Análise de vulnerabilidades

O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção contra ameaças avançada entre cargas de trabalho na cloud híbrida. Para a aprendizagem automática Azure, deve permitir a digitalização do seu recurso de registo de contentores Azure e dos recursos do Serviço Azure Kubernetes. Consulte [a imagem do Registo de Contentores Azure pelo Security Center](../security-center/defender-for-container-registries-introduction.md) e a [integração dos Serviços Azure Kubernetes com o Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Auditar e gerir a conformidade

[A Azure Policy](../governance/policy/index.yml) é uma ferramenta de governação que lhe permite garantir que os recursos da Azure estão em conformidade com as suas políticas. Com a Azure Machine Learning, pode atribuir as seguintes políticas:

* **Chave gerida pelo cliente** : Auditar ou impor se os espaços de trabalho devem utilizar uma chave gerida pelo cliente.
* **Ligação privada** : Audite se os espaços de trabalho utilizam um ponto final privado para comunicar com uma rede virtual.

Para obter mais informações sobre a Política Azure, consulte a documentação da [Política Azure](../governance/policy/overview.md).

Para obter mais informações sobre as políticas específicas do Azure Machine Learning, consulte [a Auditoria e gerencie o cumprimento da Política Azure.](how-to-integrate-azure-policy.md)

## <a name="resource-locks"></a>Bloqueios de recurso

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Passos seguintes

* [Serviços web secure Azure Machine Learning com TLS](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implementado como um serviço web](how-to-consume-web-service.md)
* [Use a azure machine learning com firewall Azure](how-to-access-azureml-behind-firewall.md)
* [Use a azure machine learning com rede virtual Azure](how-to-network-security-overview.md)
* [Encriptação de dados em repouso e em trânsito](concept-data-encryption.md)
* [Construir uma recomendação em tempo real API em Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
