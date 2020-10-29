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
ms.openlocfilehash: b45c5cd1a750ee4b3f182920c4ee2f2e47756867
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899331"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Segurança e governação da empresa para a Azure Machine Learning

Neste artigo, você vai aprender sobre as funcionalidades de segurança disponíveis para Azure Machine Learning.

Quando se utiliza um serviço de cloud, a melhor prática é restringir o acesso apenas aos utilizadores que dele necessitem. Comece por compreender o modelo de autenticação e autorização utilizado pelo serviço. Também pode querer restringir o acesso à rede ou juntar recursos de forma segura na sua rede no local com a nuvem. A encriptação de dados também é vital, tanto em repouso como enquanto os dados se movem entre os serviços. Também pode querer criar polícias para impor determinadas configurações ou registar-se quando são criadas configurações não conformes. Finalmente, é necessário ser capaz de monitorizar o serviço e produzir um registo de auditoria de toda a atividade.

> [!NOTE]
> A informação neste artigo funciona com a versão 1.0.83.1 ou superior da Azure Machine Learning Python.

## <a name="authentication"></a>Autenticação

A autenticação multi-factor é suportada se o Azure Ative Directory (Azure AD) estiver configurado para o utilizar. Aqui está o processo de autenticação:

1. O cliente assina no Azure AD e recebe um token Azure Resource Manager.  Os utilizadores e os principais serviços são totalmente suportados.
1. O cliente apresenta o símbolo ao Azure Resource Manager e a toda a Azure Machine Learning.
1. O serviço Machine Learning fornece um sinal de serviço de Machine Learning para o alvo do computação do utilizador (por exemplo, Machine Learning Compute). Este token é utilizado pelo alvo de computação do utilizador para voltar a ligar para o serviço machine learning após a execução estar concluída. O âmbito é limitado ao espaço de trabalho.

[![Autenticação em Aprendizagem automática Azure](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Para obter mais informações, consulte [Configurar a autenticação para os recursos de aprendizagem automática Azure e fluxos de trabalho.](how-to-setup-authentication.md) Este artigo fornece informações e exemplos sobre a autenticação, incluindo a utilização de princípios de serviço e fluxos de trabalho automatizados.

### <a name="authentication-for-web-service-deployment"></a>Autenticação para implantação de serviço web

A Azure Machine Learning suporta duas formas de autenticação para serviços web: chave e token. Cada serviço web pode ativar apenas uma forma de autenticação de cada vez.

|Método de autenticação|Descrição|Azure Container Instances|AKS|
|---|---|---|---|
|Chave|As chaves estão estáticas e não precisam de ser refrescadas. As chaves podem ser regeneradas manualmente.|Desativado por padrão| Ativado por predefinição|
|Token|As fichas expiram após um período de tempo especificado e precisam de ser atualizadas.| Não disponível| Desativado por padrão |

Para obter exemplos de código, consulte a [secção de autenticação do serviço web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorização

Você pode criar vários espaços de trabalho, e cada espaço de trabalho pode ser compartilhado por várias pessoas. Quando partilha um espaço de trabalho, pode controlar o acesso ao mesmo atribuindo estas funções aos utilizadores:

* Proprietário
* Contribuinte
* Leitor

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

Se as funções incorporadas não satisfaçam as suas necessidades, pode criar papéis personalizados. As funções personalizadas são suportadas para controlar todas as operações dentro de um espaço de trabalho, tais como criar um cálculo, submeter uma corrida, registar uma datastore ou implementar um modelo. As funções personalizadas podem ter lido, escrito ou apagado permissões sobre os vários recursos de um espaço de trabalho, tais como clusters, datastores, modelos e pontos finais. Pode disponibilizar a função a um nível específico do espaço de trabalho, a um nível específico de grupo de recursos ou a um nível de subscrição específico. Para obter mais informações, consulte [Gerir os utilizadores e as funções num espaço de trabalho de Aprendizagem automática Azure.](how-to-assign-roles.md)

> [!WARNING]
> O Azure Machine Learning é apoiado com a colaboração entre negócios do Azure Ative Directory, mas não é atualmente apoiado com a colaboração entre negócios e consumidores do Azure Ative Directory.

### <a name="securing-compute-targets-and-data"></a>Assegurar metas e dados de computação

Os proprietários e colaboradores podem usar todos os alvos de cálculo e lojas de dados que estão anexados ao espaço de trabalho.  

Cada espaço de trabalho também tem uma identidade gerida associada ao sistema que tem o mesmo nome que o espaço de trabalho. A identidade gerida tem as seguintes permissões sobre recursos anexados utilizados no espaço de trabalho.

Para obter mais informações sobre identidades geridas, consulte [identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

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

## <a name="network-security"></a>Segurança da rede

A Azure Machine Learning conta com outros serviços Azure para obter recursos compute. Os recursos de cálculo (metas de computação) são usados para treinar e implementar modelos. Pode criar estes alvos de computação numa rede virtual. Por exemplo, pode utilizar a Azure Data Science Virtual Machine para treinar um modelo e, em seguida, implementar o modelo para AKS.  

Para mais informações, consulte [o isolamento da rede virtual e a visão geral da privacidade.](how-to-network-security-overview.md)

Também pode ativar o Azure Private Link para o seu espaço de trabalho. O Private Link permite-lhe restringir as comunicações ao seu espaço de trabalho a partir de uma Rede Virtual Azure. Para mais informações, consulte [Como configurar o Private Link](how-to-configure-private-link.md).

## <a name="data-encryption"></a>Encriptação de dados

> [!IMPORTANT]
> Para encriptação de grau de produção durante __o treino,__ a Microsoft recomenda a utilização do cluster de cálculo Azure Machine Learning. Para encriptação de nível de produção durante __a inferência,__ a Microsoft recomenda a utilização do Serviço Azure Kubernetes.
>
> A exemplo de computação de aprendizagem automática Azure é um ambiente dev/teste. Ao usá-lo, recomendamos que guarde os seus ficheiros, como cadernos e scripts, numa partilha de ficheiros. Os seus dados devem ser armazenados numa data-loja.

### <a name="encryption-at-rest"></a>Encriptação inativa

> [!IMPORTANT]
> Se o seu espaço de trabalho contiver dados sensíveis, recomendamos que se estabeleça a [bandeira hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) enquanto cria o seu espaço de trabalho. A `hbi_workspace` bandeira só pode ser colocada quando um espaço de trabalho é criado. Não pode ser alterado para um espaço de trabalho existente.

A `hbi_workspace` bandeira controla a quantidade de [dados que](#microsoft-collected-data) a Microsoft recolhe para fins de diagnóstico e permite [encriptação adicional em ambientes geridos pela Microsoft](../security/fundamentals/encryption-atrest.md). Além disso, permite as seguintes ações:

* Começa a encriptar o disco de risco local no seu cluster de cálculo Azure Machine Learning desde que não tenha criado nenhum cluster anterior nessa subscrição. Caso contrário, você precisa levantar um bilhete de apoio para permitir a encriptação do disco de risco dos seus clusters de computação 
* Limpa o disco local de armazenamento temporário entre as execuções
* Passa credenciais de segurança para a sua conta de armazenamento, registo de contentores e conta SSH da camada de execução para os seus clusters de computação usando o seu cofre-chave
* Permite a filtragem IP para garantir que os lotes subjacentes não podem ser chamados por outros serviços externos que não o AzureMachineLearningService

#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

A Azure Machine Learning armazena instantâneos, saídas e registos na conta de armazenamento Azure Blob que está ligada ao espaço de trabalho Azure Machine Learning e à sua subscrição. Todos os dados armazenados no armazenamento Azure Blob estão encriptados em repouso com as teclas geridas pela Microsoft.

Para obter informações sobre como utilizar as suas próprias chaves para os dados armazenados no armazenamento Azure Blob, consulte [a encriptação do Armazenamento Azure com as chaves geridas pelo cliente no Cofre da Chave Azure](../storage/common/storage-encryption-keys-portal.md).

Os dados de treino também são normalmente armazenados no armazenamento do Azure Blob para que seja acessível a alvos de computação de treino. Este armazenamento não é gerido pela Azure Machine Learning, mas montado para calcular alvos como um sistema de ficheiros remotos.

Se precisar de __rodar ou revogar__ a sua chave, pode fazê-lo a qualquer momento. Ao rodar uma chave, a conta de armazenamento começará a usar a nova chave (versão mais recente) para encriptar os dados em repouso. Ao revogar (desativar) uma chave, a conta de armazenamento cuida de pedidos falhados. Normalmente demora uma hora para que a rotação ou a revogação sejam eficazes.

Para obter informações sobre a regeneração das teclas de acesso, consulte [as teclas de acesso ao armazenamento Regenerate](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

A Azure Machine Learning armazena métricas e metadados num exemplo de DB do Azure Cosmos. Este caso está associado a uma subscrição da Microsoft gerida pela Azure Machine Learning. Todos os dados armazenados no Azure Cosmos DB estão encriptados em repouso com as teclas geridas pela Microsoft.

Para utilizar as suas próprias chaves (geridas pelo cliente) para encriptar a instância DB do Azure Cosmos, pode criar uma instância dedicada cosmos DB para uso com o seu espaço de trabalho. Recomendamos esta abordagem se quiser armazenar os seus dados, tais como informações de histórico de execução, fora da instância de DB cosmos multi-arrendatário hospedada na nossa subscrição da Microsoft. 

Para permitir o provisionamento de uma instância de DB cosmos na sua subscrição com chaves geridas pelo cliente, execute as seguintes ações:

* Registe os fornecedores de recursos Microsoft.MachineLearning e Microsoft.DocumentDB na sua subscrição, se ainda não for feito.

* Utilize os seguintes parâmetros ao criar o espaço de trabalho Azure Machine Learning. Ambos os parâmetros são obrigatórios e suportados nos modelos SDK, CLI, REST APIs e Resource Manager.

    * `resource_cmk_uri`: Este parâmetro é o recurso completo URI da chave gerida pelo cliente no seu cofre chave, incluindo as [informações](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)da versão para a chave . 

    * `cmk_keyvault`: Este parâmetro é o ID de recurso do cofre chave na sua subscrição. Este cofre-chave precisa de estar na mesma região e subscrição que você usará para o espaço de trabalho Azure Machine Learning. 
    
        > [!NOTE]
        > Esta instância do cofre chave pode ser diferente do cofre chave que é criado pela Azure Machine Learning quando você forja o espaço de trabalho. Se pretender utilizar a mesma instância de cofre para o espaço de trabalho, passe o mesmo cofre-chave enquanto abaste o espaço de trabalho utilizando o [parâmetro key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Se precisar de __rodar ou revogar__ a sua chave, pode fazê-lo a qualquer momento. Ao rodar uma chave, a Cosmos DB começará a usar a nova chave (versão mais recente) para encriptar dados em repouso. Ao revogar (desativar) uma chave, a Cosmos DB cuida de pedidos falhados. Normalmente demora uma hora para que a rotação ou a revogação sejam eficazes.

Para obter mais informações sobre as chaves geridas pelo cliente com a Cosmos DB, consulte [as chaves geridas pelo cliente para a sua conta DB Azure Cosmos](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Registo de Contentores do Azure

Todas as imagens do contentor no seu registo (Registo do Contentor Azure) estão encriptadas em repouso. O Azure encripta automaticamente uma imagem antes de a armazenar e desencriptada quando a Azure Machine Learning puxa a imagem.

Para utilizar as suas próprias chaves (geridas pelo cliente) para encriptar o registo do seu contentor Azure, é necessário criar o seu próprio ACR e anexá-lo enquanto forja o espaço de trabalho ou encriptar a instância padrão que é criada no momento do fornecimento do espaço de trabalho.

> [!IMPORTANT]
> A Azure Machine Learning requer que a conta de administração seja ativada no seu Registo de Contentores Azure. Por predefinição, esta definição é desativada quando cria um registo de contentores. Para obter informações sobre a habilitação da conta de administração, consulte [a conta Admin](/azure/container-registry/container-registry-authentication#admin-account).
>
> Uma vez criado um registo de contentores Azure para um espaço de trabalho, não o elimine. Ao fazê-lo, quebrará o seu espaço de trabalho de Aprendizagem de Máquinas Azure.

Para um exemplo de criação de um espaço de trabalho utilizando um registo de contentores Azure existente, consulte os seguintes artigos:

* [Crie um espaço de trabalho para a Azure Machine Learning com Azure CLI](how-to-manage-workspace-cli.md).
* [Crie um espaço de trabalho com Python SDK.](how-to-manage-workspace.md?tabs=python#create-a-workspace)
* [Use um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem de máquinas Azure](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Instância de Contentor do Azure

Pode encriptar um recurso Azure Container Instance (ACI) implantado utilizando chaves geridas pelo cliente. A chave gerida pelo cliente utilizada para o ACI pode ser armazenada no Cofre da Chave Azure para o seu espaço de trabalho. Para obter informações sobre a geração de uma chave, consulte [os dados de encriptação com uma chave gerida pelo cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Para utilizar a chave ao implementar um modelo para Azure Container Instance, crie uma nova configuração de implantação utilizando `AciWebservice.deploy_configuration()` . Forneça as informações-chave utilizando os seguintes parâmetros:

* `cmk_vault_base_url`: O URL do cofre-chave que contém a chave.
* `cmk_key_name`: O nome da chave.
* `cmk_key_version`: A versão da chave.

Para obter mais informações sobre a criação e utilização de uma configuração de implantação, consulte os seguintes artigos:

* [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) referência
* [Onde e como implementar](how-to-deploy-and-where.md)
* [Implementar um modelo no Azure Container Instances](how-to-deploy-azure-container-instance.md)

Para obter mais informações sobre a utilização de uma chave gerida pelo cliente com ACI, consulte [os dados de encriptação com uma chave gerida pelo cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pode encriptar um recurso de Serviço Azure Kubernetes implantado utilizando chaves geridas pelo cliente a qualquer momento. Para mais informações, consulte [Bring your own keys with Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md). 

Este processo permite-lhe encriptar tanto os Dados como o Disco OS das máquinas virtuais implantadas no cluster Kubernetes.

> [!IMPORTANT]
> Este processo funciona apenas com a versão 1.17 ou superior da AKS K8s. A Azure Machine Learning adicionou suporte para AKS 1.17 em 13 de janeiro de 2020.

#### <a name="machine-learning-compute"></a>Cálculo de Aprendizagem automática

O disco OS para cada nó de computação armazenado no Azure Storage é encriptado com chaves geridas pela Microsoft nas contas de armazenamento Azure Machine Learning. Este alvo de computação é efémero, e os clusters são tipicamente reduzidos quando não há filas. A máquina virtual subjacente é desavisionada e o disco de SO é eliminado. A encriptação do disco Azure não é suportada para o disco DE.

Cada máquina virtual também tem um disco temporário local para operações de SO. Se quiser, pode usar o disco para encenar dados de treino. O disco é encriptado por padrão para espaços de trabalho com o `hbi_workspace` parâmetro definido para `TRUE` . Este ambiente é de curta duração apenas durante a duração da sua execução, e o suporte de encriptação está limitado apenas a chaves geridas pelo sistema.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks pode ser usado em gasodutos Azure Machine Learning. Por predefinição, o Databricks File System (DBFS) utilizado pelo Azure Databricks é encriptado utilizando uma chave gerida pela Microsoft. Para configurar a Azure Databricks para utilizar chaves geridas pelo cliente, consulte [as teclas geridas pelo cliente no DBFS (raiz) padrão .](/azure/databricks/security/customer-managed-keys-dbfs)

### <a name="encryption-in-transit"></a>Encriptação de dados em circulação

A Azure Machine Learning utiliza o TLS para garantir a comunicação interna entre vários microserviços de aprendizagem automática Azure. Todo o acesso a Azure Storage também ocorre sobre um canal seguro.

Para garantir chamadas externas feitas para o ponto final de pontuação, o Azure Machine Learning utiliza O TLS. Para obter mais informações, consulte [o Use TLS para garantir um serviço web através do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Usando o cofre da chave Azure

A Azure Machine Learning utiliza o exemplo do Cofre da Chave Azure associado ao espaço de trabalho para armazenar credenciais de vários tipos:

* A cadeia de ligação de conta de armazenamento associada
* Palavras-passe para instâncias de repositório de contentores Azure
* Cadeias de ligação a lojas de dados

As palavras-passe e as chaves do SSH para calcular alvos como Azure HDInsight e VMs são armazenadas num cofre de chaves separado que está associado à subscrição da Microsoft. O Azure Machine Learning não armazena senhas ou chaves fornecidas pelos utilizadores. Em vez disso, gera, autoriza e armazena as suas próprias chaves SSH para ligar a VMs e HDInsight para executar as experiências.

Cada espaço de trabalho tem uma identidade gerida associada ao sistema que tem o mesmo nome que o espaço de trabalho. Esta identidade gerida tem acesso a todas as chaves, segredos e certificados no cofre das chaves.

## <a name="data-collection-and-handling"></a>Recolha e tratamento de dados

### <a name="microsoft-collected-data"></a>Dados recolhidos pela Microsoft

A Microsoft pode recolher informações de identificação não-utilizadoras como nomes de recursos (por exemplo, o nome do conjunto de dados ou o nome da experiência de aprendizagem automática), ou variáveis de ambiente de trabalho para fins de diagnóstico. Todos estes dados são armazenados utilizando chaves geridas pela Microsoft no armazenamento alojado em subscrições próprias da Microsoft e seguem as [normas padrão de política de privacidade e tratamento de dados da Microsoft.](https://privacy.microsoft.com/privacystatement)

A Microsoft também recomenda não armazenar informações sensíveis (como segredos-chave de contas) em variáveis ambientais. As variáveis ambientais são registadas, encriptadas e armazenadas por nós. Da mesma forma, ao nomear [run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true), evite incluir informações sensíveis, tais como nomes de utilizadores ou nomes de projetos secretos. Estas informações podem aparecer em registos de telemetria acessíveis aos engenheiros do Microsoft Support.

Pode optar por não recolher dados de diagnóstico, definindo o `hbi_workspace` parâmetro para o fornecimento do espaço de `TRUE` trabalho. Esta funcionalidade é suportada ao utilizar os modelos AzureML Python SDK, CLI, REST APIs ou Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Dados gerados pela Microsoft

Ao utilizar serviços como Machine Learning Automatizado, a Microsoft pode gerar dados transitórios e pré-processados para a formação de vários modelos. Estes dados são armazenados numa loja de dados no seu espaço de trabalho, o que lhe permite impor controlos de acesso e encriptação adequadamente.

Também pode querer encriptar [as informações de diagnóstico registadas a partir do seu ponto final implantado](how-to-enable-app-insights.md) na sua instância Azure Application Insights.

## <a name="monitoring"></a>Monitorização

### <a name="metrics"></a>Métricas

Pode utilizar as métricas do Azure Monitor para visualizar e monitorizar métricas para o seu espaço de trabalho de aprendizagem de máquinas Azure. No [portal Azure,](https://portal.azure.com)selecione o seu espaço de trabalho e, em seguida, selecione **Métricas:**

[![Screenshot mostrando métricas de exemplo para um espaço de trabalho](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

As métricas incluem informações sobre execuções, implementações e registos.

Para obter mais informações, consulte [métricas no Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics).

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

O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção contra ameaças avançada entre cargas de trabalho na cloud híbrida. Para a aprendizagem automática Azure, deve permitir a digitalização do seu recurso de registo de contentores Azure e dos recursos do Serviço Azure Kubernetes. Consulte [a imagem do Registo de Contentores Azure pelo Security Center](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) e a [integração dos Serviços Azure Kubernetes com o Security Center](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration).

## <a name="data-flow-diagrams"></a>Diagramas de fluxo de dados

### <a name="create-workspace"></a>Criar área de trabalho

O diagrama seguinte mostra o fluxo de trabalho criar espaço de trabalho.

* Você insiná-lo no Azure AD de um dos clientes suportados Azure Machine Learning (Azure CLI, Python SDK, portal Azure) e solicitar o token apropriado do Azure Resource Manager.
* Você chama Azure Resource Manager para criar o espaço de trabalho. 
* A Azure Resource Manager contacta o fornecedor de recursos Azure Machine Learning para providenciar o espaço de trabalho.

Os recursos adicionais são criados na subscrição do utilizador durante a criação do espaço de trabalho:

* Cofre-chave (para armazenar segredos)
* Uma conta de armazenamento Azure (incluindo blob e partilha de ficheiros)
* Registo do Contentor de Azure (para armazenar imagens do Docker para inferência/pontuação e experimentação)
* Insights de Aplicação (para armazenar telemetria)

O utilizador também pode fornecer outros alvos computacional que estejam ligados a um espaço de trabalho (como o Serviço Azure Kubernetes ou VMs) conforme necessário.

[![Criar fluxo de trabalho do espaço de trabalho](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Guardar código fonte (scripts de treino)

O diagrama seguinte mostra o fluxo de trabalho do instantâneo do código.

Associados a um espaço de trabalho Azure Machine Learning estão diretórios (experiências) que contêm o código-fonte (scripts de formação). Estes scripts são armazenados na sua máquina local e na nuvem (no armazenamento Azure Blob para a sua subscrição). Os instantâneos de código são utilizados para execução ou inspeção para auditoria histórica.

[![Fluxo de trabalho de instantâneo de código](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot.png#lightbox)

### <a name="training"></a>Formação

O diagrama seguinte mostra o fluxo de trabalho de treino.

* A Azure Machine Learning é chamada com o ID instantâneo para a imagem de código guardada na secção anterior.
* O Azure Machine Learning cria um ID de execução (opcional) e um token de serviço de machine learning, que é mais tarde utilizado por alvos computacionais como Machine Learning Compute/VMs para comunicar com o serviço de Machine Learning.
* Você pode escolher um alvo de computação gerido (como Machine Learning Compute) ou um alvo de computação não gerido (como VMs) para executar trabalhos de formação. Aqui estão os fluxos de dados para ambos os cenários:
   * VMs/HDInsight, acedido por credenciais SSH num cofre chave na subscrição da Microsoft. A Azure Machine Learning executa o código de gestão no alvo do cálculo que:

   1. Prepara o ambiente. (Docker é uma opção para VMs e computadores locais. Consulte os seguintes passos para machine learning compute para entender como funciona as experiências em contentores Docker.)
   1. Descarrega o código.
   1. Configura variáveis e configurações ambientais.
   1. Executa scripts de utilizador (a imagem de código mencionada na secção anterior).

   * Machine Learning Compute, acedido através de uma identidade gerida pelo espaço de trabalho.
Como o Machine Learning Compute é um alvo de computação gerido (isto é, é gerido pela Microsoft) funciona sob a subscrição da Microsoft.

   1. A construção remota do Docker é iniciada, se necessário.
   1. O código de gestão é escrito para a partilha de Ficheiros Azure do utilizador.
   1. O recipiente é iniciado com um comando inicial. Ou seja, código de gestão, tal como descrito no passo anterior.

#### <a name="querying-runs-and-metrics"></a>Consultas e métricas

No diagrama de fluxo abaixo, este passo ocorre quando o alvo do computação de treino escreve as métricas de corrida de volta para Azure Machine Learning a partir do armazenamento na base de dados do Cosmos DB. Os clientes podem ligar para a Azure Machine Learning. A Machine Learning, por sua vez, irá retirar métricas da base de dados da Cosmos DB e devolvê-las ao cliente.

[![Fluxo de trabalho de formação](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics.png#lightbox)

### <a name="creating-web-services"></a>Criação de serviços web

O diagrama seguinte mostra o fluxo de trabalho da inferência. Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para previsão, mais frequentemente em dados de produção.

Aqui estão os detalhes:

* O utilizador regista um modelo utilizando um cliente como o Azure Machine Learning SDK.
* O utilizador cria uma imagem utilizando um modelo, um ficheiro de pontuação e outras dependências de modelos.
* A imagem docker é criada e armazenada no Registo do Contentor Azure.
* O serviço web é implantado para o alvo de computação (Instâncias de Contentores/AKS) utilizando a imagem criada no passo anterior.
* Os detalhes do pedido de pontuação são armazenados no Application Insights, que está na subscrição do utilizador.
* A telemetria também é empurrada para a subscrição Microsoft/Azure.

[![Fluxo de trabalho de inferência](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing.png#lightbox)

## <a name="audit-and-manage-compliance"></a>Auditar e gerir a conformidade

[A Azure Policy](/azure/governance/policy) é uma ferramenta de governação que lhe permite garantir que os recursos da Azure estão em conformidade com as suas políticas. Com a Azure Machine Learning, pode atribuir as seguintes políticas:

* **Chave gerida pelo cliente** : Auditar ou impor se os espaços de trabalho devem utilizar uma chave gerida pelo cliente.
* **Ligação privada** : Audite se os espaços de trabalho utilizam um ponto final privado para comunicar com uma rede virtual.

Para obter mais informações sobre a Política Azure, consulte a documentação da [Política Azure](/azure/governance/policy/overview).

Para obter mais informações sobre as políticas específicas do Azure Machine Learning, consulte [a Auditoria e gerencie o cumprimento da Política Azure.](how-to-integrate-azure-policy.md)

## <a name="resource-locks"></a>Bloqueios de recurso

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Próximas etapas

* [Serviços web secure Azure Machine Learning com TLS](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implementado como um serviço web](how-to-consume-web-service.md)
* [Use a azure machine learning com firewall Azure](how-to-access-azureml-behind-firewall.md)
* [Use a azure machine learning com rede virtual Azure](how-to-network-security-overview.md)
* [Melhores práticas para a construção de sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Construir uma recomendação em tempo real API em Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
