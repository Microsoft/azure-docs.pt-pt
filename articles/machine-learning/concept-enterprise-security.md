---
title: Segurança empresarial
titleSuffix: Azure Machine Learning
description: 'Utilize de forma segura o Azure Machine Learning: autenticação, autorização, segurança da rede, encriptação de dados e monitorização.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 359fd7fc787db5710deca75dd562215d25ed9148
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437494"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Segurança empresarial para Aprendizagem automática Azure

Neste artigo, você vai aprender sobre funcionalidades de segurança disponíveis para Azure Machine Learning.

Quando utiliza um serviço na nuvem, a melhor prática é restringir o acesso apenas aos utilizadores que dele necessitem. Comece por compreender o modelo de autenticação e autorização utilizado pelo serviço. Também pode querer restringir o acesso à rede ou juntar recursos de forma segura na sua rede no local com a nuvem. A encriptação de dados também é vital, tanto em repouso como enquanto os dados se movem entre serviços. Por fim, é necessário poder monitorizar o serviço e produzir um registo de auditoria de toda a atividade.

> [!NOTE]
> A informação deste artigo funciona com a versão SDK De Aprendizagem automática Azure Python 1.0.83.1 ou superior.

## <a name="authentication"></a>Autenticação

A autenticação de vários fatores é suportada se o Azure Ative Directory (Azure AD) estiver configurado para usá-lo. Aqui está o processo de autenticação:

1. O cliente entra na Azure AD e recebe um token Azure Resource Manager.  Os utilizadores e os diretores de serviço são totalmente suportados.
1. O cliente apresenta o símbolo ao Azure Resource Manager e a toda a Aprendizagem automática Azure.
1. O serviço de Machine Learning fornece um serviço de Machine Learning para o objetivo da computação do utilizador (por exemplo, Machine Learning Compute). Esta ficha é utilizada pelo alvo de cálculo do utilizador para voltar a ligar para o serviço de Machine Learning após a conclusão da execução. O âmbito é limitado ao espaço de trabalho.

[![Autenticação em Aprendizagem automática Azure](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Para mais informações, consulte [A autenticação configurar os recursos e fluxos de trabalho do Azure Machine Learning.](how-to-setup-authentication.md) Este artigo fornece informações e exemplos sobre a autenticação, incluindo a utilização de diretores de serviço e fluxos de trabalho automatizados.

### <a name="authentication-for-web-service-deployment"></a>Autenticação para implementação de serviço web

O Azure Machine Learning suporta duas formas de autenticação para serviços web: chave e ficha. Cada serviço web só pode permitir uma forma de autenticação de cada vez.

|Método de autenticação|Descrição|Azure Container Instances|AKS|
|---|---|---|---|
|Chave|As chaves estão estáticas e não precisam de ser refrescadas. As chaves podem ser regeneradas manualmente.|Desativado por defeito| Ativado por predefinição|
|Certificado de|Os tokens expiram após um período de tempo especificado e precisam de ser atualizados.| Não disponível| Desativado por defeito |

Para exemplos de código, consulte a [secção de autenticação do serviço web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorização

Você pode criar vários espaços de trabalho, e cada espaço de trabalho pode ser partilhado por várias pessoas. Ao partilhar um espaço de trabalho, pode controlar o acesso ao mesmo atribuindo estas funções aos utilizadores:

* Proprietário
* Contribuinte
* Leitor

A tabela seguinte lista algumas das principais operações de Aprendizagem automática do Azure e as funções que podem desempenhar:

| Operação de aprendizagem automática azure | Proprietário | Contribuinte | Leitor |
| ---- |:----:|:----:|:----:|
| Criar área de trabalho | ✓ | ✓ | |
| Partilhar espaço de trabalho | ✓ | |  |
| Atualizar espaço de trabalho para edição da Enterprise | ✓ | |
| Criar o alvo da computação | ✓ | ✓ | |
| Anexar o alvo do cálculo | ✓ | ✓ | |
| Anexar lojas de dados | ✓ | ✓ | |
| Executar experiência | ✓ | ✓ | |
| Ver corridas/métricas | ✓ | ✓ | ✓ |
| Registar o modelo | ✓ | ✓ | |
| Criar imagem | ✓ | ✓ | |
| Implementar serviço web | ✓ | ✓ | |
| Ver modelos/imagens | ✓ | ✓ | ✓ |
| Chamar serviço web | ✓ | ✓ | ✓ |

Se os papéis incorporados não atenderem às suas necessidades, pode criar papéis personalizados. As funções personalizadas são suportadas apenas para operações no espaço de trabalho e na Computação de Aprendizagem automática. As funções personalizadas podem ter lido, escrito ou apagado permissões no espaço de trabalho e no recurso computacional nesse espaço de trabalho. Pode disponibilizar o papel a um nível específico do espaço de trabalho, a um nível específico de grupo de recursos ou a um nível de subscrição específico. Para mais informações, consulte [Gerir utilizadores e papéis num espaço](how-to-assign-roles.md)de trabalho azure machine learning .

> [!WARNING]
> A Azure Machine Learning não é atualmente apoiada com a colaboração azure Ative Directory business-to-business.

### <a name="securing-compute-targets-and-data"></a>Assegurar alvos e dados de computação

Proprietários e colaboradores podem usar todos os alvos de cálculo e lojas de dados que estão ligados ao espaço de trabalho.  

Cada espaço de trabalho também tem uma identidade gerida associada ao sistema que tem o mesmo nome que o espaço de trabalho. A identidade gerida tem as seguintes permissões sobre os recursos anexados utilizados no espaço de trabalho.

Para obter mais informações sobre identidades geridas, consulte [identidades geridas para os recursos Do Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Recurso | Permissões |
| ----- | ----- |
| Área de trabalho | Contribuinte |
| Conta de armazenamento | Contribuinte de dados blob de armazenamento |
| Key Vault | Acesso a todas as chaves, segredos, certificados |
| Registo de Contentores do Azure | Contribuinte |
| Grupo de recursos que contém o espaço de trabalho | Contribuinte |
| Grupo de recursos que contém o cofre chave (se diferente daquele que contém o espaço de trabalho) | Contribuinte |

Não recomendamos que os administradores revoguem o acesso da identidade gerida aos recursos mencionados na tabela anterior. Pode restabelecer o acesso utilizando a operação de resincronização das teclas.

O Azure Machine Learning cria uma `aml-` aplicação adicional (o nome começa com ou) `Microsoft-AzureML-Support-App-`com acesso ao nível dos contribuintes na sua subscrição para cada região do espaço de trabalho. Por exemplo, se tiver um espaço de trabalho no Leste dos EUA e um no Norte da Europa na mesma subscrição, verá duas destas aplicações. Estas aplicações permitem ao Azure Machine Learning ajudá-lo a gerir os recursos da computação.

## <a name="network-security"></a>Segurança da rede

O Azure Machine Learning conta com outros serviços Azure para recursos de computação. Os recursos computacionais (alvos de computação) são usados para treinar e implementar modelos. Pode criar estes alvos de computação numa rede virtual. Por exemplo, pode utilizar a Máquina Virtual de Ciência de Dados Azure para treinar um modelo e depois implementar o modelo para AKS.  

Para mais informações, consulte [Como executar experiências e inferêncianuma rede virtual](how-to-enable-virtual-network.md).

Também pode ativar o Azure Private Link para o seu espaço de trabalho. O Private Link permite-lhe restringir as comunicações ao seu espaço de trabalho a partir de uma Rede Virtual Azure. Para mais informações, consulte [Como configurar o Private Link](how-to-configure-private-link.md).

> [!TIP]
> Pode combinar rede virtual e Private Link em conjunto para proteger a comunicação entre o seu espaço de trabalho e outros recursos Azure. No entanto, algumas combinações requerem um espaço de trabalho da edição da Enterprise. Utilize a tabela seguinte para entender quais os cenários que requerem a edição da Enterprise:
>
> | Cenário | Enterprise</br>edição | Básico</br>edição |
> | ----- |:-----:|:-----:| 
> | Nenhuma rede virtual ou link privado | ✔ | ✔ |
> | Espaço de trabalho sem Private Link. Outros recursos (com exceção do Registo de Contentores Azure) numa rede virtual | ✔ | ✔ |
> | Espaço de trabalho sem Private Link. Outros recursos com Private Link | ✔ | |
> | Espaço de trabalho com Private Link. Outros recursos (com exceção do Registo de Contentores Azure) numa rede virtual | ✔ | ✔ |
> | Espaço de trabalho e qualquer outro recurso com Private Link | ✔ | |
> | Espaço de trabalho com Private Link. Outros recursos sem Private Link ou rede virtual | ✔ | ✔ |
> | Registo de contentores azure em rede virtual | ✔ | |
> | Chaves geridas pelo cliente para espaço de trabalho | ✔ | |
> 

> [!WARNING]
> A pré-visualização de instâncias de computação azure Machine Learning não é suportada num espaço de trabalho onde o Private Link está ativado.
> 
> O Azure Machine Learning não suporta a utilização de um Serviço Azure Kubernetes que tenha ligação privada ativada. Em vez disso, pode utilizar o Serviço Azure Kubernetes numa rede virtual. Para mais informações, consulte [a experimentação e os trabalhos de inferência do Secure Azure ML dentro](how-to-enable-virtual-network.md)de uma Rede Virtual Azure .

## <a name="data-encryption"></a>Encriptação de dados

### <a name="encryption-at-rest"></a>Encriptação inativa

> [!IMPORTANT]
> Se o seu espaço de trabalho contiver dados sensíveis, recomendamos que coloque a [bandeira hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) enquanto cria o seu espaço de trabalho. Isto controla a quantidade de dados que a Microsoft recolhe para fins de diagnóstico e permite encriptação adicional em ambientes geridos pela Microsoft.

Para obter mais informações sobre como funciona a encriptação em repouso em Azure, consulte a encriptação de [dados do Azure em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

O Azure Machine Learning armazena instantâneos, saídas e registos na conta de armazenamento Azure Blob que está ligada ao espaço de trabalho azure machine learning e à sua subscrição. Todos os dados armazenados no armazenamento do Azure Blob são encriptados em repouso com as chaves geridas pela Microsoft.

Para obter informações sobre como usar as suas próprias chaves para dados armazenados no armazenamento do Azure Blob, consulte a [encriptação do Armazenamento Azure com chaves geridas pelo cliente no Cofre de Chaves Azure](../storage/common/storage-encryption-keys-portal.md).

Os dados de formação também são normalmente armazenados no armazenamento do Azure Blob para que seja acessível a metas de computação de formação. Este armazenamento não é gerido pela Azure Machine Learning, mas montado para calcular alvos como um sistema de ficheiros remotos.

Se precisar __de rodar ou revogar__ a sua chave, pode fazê-lo a qualquer momento. Ao rodar uma tecla, a conta de armazenamento começará a utilizar a nova tecla (versão mais recente) para encriptar os dados em repouso. Ao revogar (desativar) uma chave, a conta de armazenamento trata de pedidos falhados. Normalmente demora uma hora para que a rotação ou a revogação sejam eficazes.

Para obter informações sobre a regeneração das teclas de acesso, consulte [Reregenerar as chaves](how-to-change-storage-access-key.md)de acesso ao armazenamento .

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

O Azure Machine Learning armazena métricas e metadados numa instância de Azure Cosmos DB. Este caso está associado a uma subscrição da Microsoft gerida pelo Azure Machine Learning. Todos os dados armazenados no Azure Cosmos DB são encriptados em repouso com chaves geridas pela Microsoft.

Para utilizar as suas próprias chaves (geridas pelo cliente) para encriptar a instância Azure Cosmos DB, pode criar uma instância db cosmos dedicada para uso com o seu espaço de trabalho. Recomendamos esta abordagem se quiser armazenar os seus dados, tais como informações de histórico de execução, fora da instância cosmos DB multi-inquilino alojada na nossa subscrição da Microsoft. 

Para permitir o fornecimento de uma instância Cosmos DB na sua subscrição com chaves geridas pelo cliente, execute as seguintes ações:

* Ative as principais capacidades geridas pelo cliente para a Cosmos DB. Neste momento, deve solicitar o acesso à utilização desta capacidade. Para isso, por [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)favor contacte.

* Registe os fornecedores de recursos Azure Machine Learning e Azure Cosmos DB na sua subscrição, se não for feito já.

* Autorize a App de Aprendizagem Automática (em Gestão de Identidade e Acesso) com permissões dos colaboradores na sua subscrição.

    ![Autorizar a 'App de Aprendizagem automática Azure' em Gestão de Identidade e Acesso no portal](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Utilize os seguintes parâmetros ao criar o espaço de trabalho Azure Machine Learning. Ambos os parâmetros são obrigatórios e suportados nos modelos SDK, CLI, REST APIs e Resource Manager.

    * `resource_cmk_uri`: Este parâmetro é o recurso uri completo da chave gerida pelo cliente no seu cofre chave, incluindo as [informações](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)da versão para a chave . 

    * `cmk_keyvault`: Este parâmetro é o iD de recurso do cofre chave na sua subscrição. Este cofre-chave precisa de estar na mesma região e subscrição que irá utilizar para o espaço de trabalho azure machine learning. 
    
        > [!NOTE]
        > Esta instância chave do cofre pode ser diferente da chave do cofre que é criado pela Azure Machine Learning quando você fornecer o espaço de trabalho. Se quiser utilizar a mesma instância chave do cofre para o espaço de trabalho, passe o mesmo cofre chave enquanto aprovisiona o espaço de trabalho utilizando o [parâmetro key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Esta instância Cosmos DB é criada num grupo de recursos gerido pela Microsoft na sua subscrição. 

> [!IMPORTANT]
> * Se precisar de eliminar esta instância Cosmos DB, tem de eliminar o espaço de trabalho Azure Machine Learning que o utiliza. 
> * As [__Unidades de Pedido__](../cosmos-db/request-units.md) padrão para esta conta Cosmos DB estão fixadas em __8000__. Mudar este valor não é suportado. 

Se precisar __de rodar ou revogar__ a sua chave, pode fazê-lo a qualquer momento. Ao rodar uma tecla, cosmos DB começará a usar a nova tecla (versão mais recente) para encriptar dados em repouso. Ao revogar (desativar) uma chave, cosmos DB cuida de pedidos falhados. Normalmente demora uma hora para que a rotação ou a revogação sejam eficazes.

Para obter mais informações sobre as chaves geridas pelo cliente com a Cosmos DB, consulte [as chaves geridas pelo cliente da Configure para a sua conta Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Registo de Contentores do Azure

Todas as imagens do contentor no seu registo (Registo de Contentores Azure) estão encriptadas em repouso. O Azure encripta automaticamente uma imagem antes de a armazenar e desencripta-a quando o Azure Machine Learning puxa a imagem.

Para utilizar as suas próprias chaves (geridas pelo cliente) para encriptar o seu Registo de Contentores Azure, precisa de criar o seu próprio ACR e anexá-lo enquanto aprovisiona o espaço de trabalho ou encriptar a instância predefinida que é criada no momento do fornecimento do espaço de trabalho.

Para um exemplo de criação de um espaço de trabalho utilizando um registo de contentores azure existente, consulte os seguintes artigos:

* [Crie um espaço de trabalho para o Azure Machine Learning com o Azure CLI](how-to-manage-workspace-cli.md).
* [Use um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para o Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Instância de Contentor do Azure

Pode encriptar um recurso Azure Container Instance (ACI) implantado utilizando chaves geridas pelo cliente. A chave gerida pelo cliente utilizada para a ACI pode ser armazenada no Cofre de Chaves Azure para o seu espaço de trabalho. Para obter informações sobre a geração de uma chave, consulte [Encriptar dados com uma chave gerida pelo cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Para utilizar a tecla ao implementar um modelo para a Instância `AciWebservice.deploy_configuration()`de Contentores Azure, crie uma nova configuração de implementação utilizando . Forneça as informações-chave utilizando os seguintes parâmetros:

* `cmk_vault_base_url`: O URL do cofre da chave que contém a chave.
* `cmk_key_name`: O nome da chave.
* `cmk_key_version`: A versão da chave.

Para obter mais informações sobre a criação e utilização de uma configuração de implementação, consulte os seguintes artigos:

* [Referência AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Onde e como implementar](how-to-deploy-and-where.md)
* [Implementar um modelo para instâncias de contentores azure](how-to-deploy-azure-container-instance.md)

Para obter mais informações sobre a utilização de uma chave gerida pelo cliente com ACI, consulte [encriptar dados com uma chave gerida pelo cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pode encriptar um recurso de serviço Azure Kubernetes implantado utilizando chaves geridas pelo cliente a qualquer momento. Para mais informações, consulte Traga as suas próprias chaves com o [Serviço Azure Kubernetes](../aks/azure-disk-customer-managed-keys.md). 

Este processo permite-lhe encriptar tanto os Dados como o Disco OS das máquinas virtuais implantadas no cluster Kubernetes.

> [!IMPORTANT]
> Este processo funciona apenas com a versão AKS K8s 1.17 ou superior. Azure Machine Learning adicionou suporte para AKS 1.17 em 13 de janeiro de 2020.

#### <a name="machine-learning-compute"></a>Computação de Aprendizagem automática

O disco OS para cada nó computacional armazenado no Armazenamento Azure é encriptado com chaves geridas pela Microsoft nas contas de armazenamento de Machine Learning Azure. Este alvo computacional é efémero, e os clusters são tipicamente reduzidos quando não há corridas em fila. A máquina virtual subjacente é desprovisionada e o disco de SO é eliminado. A encriptação do disco azure não é suportada para o disco OS.

Cada máquina virtual também tem um disco temporário local para operações de SO. Se quiser, pode usar o disco para encenar dados de treino. O disco é encriptado por padrão `hbi_workspace` para espaços `TRUE`de trabalho com o parâmetro definido para . Este ambiente é de curta duração apenas durante a duração da sua execução, e o suporte de encriptação está limitado apenas a chaves geridas pelo sistema.

#### <a name="azure-databricks"></a>Azure Databricks

Os tijolos de dados azure podem ser usados em oleodutos Azure Machine Learning. Por padrão, o Sistema de Ficheiros Databricks (DBFS) utilizado pelo Azure Databricks é encriptado utilizando uma chave gerida pela Microsoft. Para configurar os Bricks Azure para utilizar as chaves geridas pelo cliente, consulte [as chaves geridas pelo cliente no DBFS predefinido (raiz)](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Encriptação de dados em circulação

Pode utilizar o TLS para garantir a comunicação interna entre os microserviços de Aprendizagem automática do Azure e para garantir chamadas externas ao ponto final de pontuação. Todo o acesso ao Armazenamento Azure também ocorre sobre um canal seguro.

Para mais informações, consulte [Use TLS para garantir um serviço web através](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)do Azure Machine Learning .

### <a name="using-azure-key-vault"></a>Usando o cofre da chave azure

O Azure Machine Learning utiliza a instância Azure Key Vault associada ao espaço de trabalho para armazenar credenciais de vários tipos:

* A cadeia de ligação à conta de armazenamento associada
* Palavras-passe para casos de repositório de contentores azure
* Cordas de ligação às lojas de dados

As palavras-passe e as chaves do SSH para calcular alvos como o Azure HDInsight e os VMs são armazenados num cofre separado que está associado à subscrição da Microsoft. O Azure Machine Learning não armazena senhas ou chaves fornecidas pelos utilizadores. Em vez disso, gera, autoriza e armazena as suas próprias chaves SSH para ligar aos VMs e hDInsight para executar as experiências.

Cada espaço de trabalho tem uma identidade gerida associada ao sistema que tem o mesmo nome que o espaço de trabalho. Esta identidade gerida tem acesso a todas as chaves, segredos e certificados no cofre chave.

## <a name="data-collection-and-handling"></a>Recolha e manuseamento de dados

### <a name="microsoft-collected-data"></a>Microsoft recolheu dados

A Microsoft pode recolher informações de identificação não-utilizadora como nomes de recursos (por exemplo, o nome do conjunto de dados, ou o nome da experiência de aprendizagem automática), ou variáveis do ambiente de trabalho para fins de diagnóstico. Todos estes dados são armazenados utilizando chaves geridas pela Microsoft no armazenamento hospedados em subscrições próprias da Microsoft e seguem a política padrão de [privacidade da Microsoft e os padrões](https://privacy.microsoft.com/privacystatement)de tratamento de dados.

A Microsoft também recomenda não armazenar informações sensíveis (como segredos chave de conta) em variáveis ambientais. As variáveis ambientais são registadas, encriptadas e armazenadas por nós. Da mesma forma ao nomear [runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py), evite incluir informações sensíveis, tais como nomes de utilizadores ou nomes secretos do projeto. Estas informações podem aparecer em registos de telemetria acessíveis aos engenheiros do Microsoft Support.

Pode optar por não fazer os `hbi_workspace` dados de `TRUE` diagnóstico recolhidos, definindo o parâmetro para o fornecimento do espaço de trabalho. Esta funcionalidade é suportada ao utilizar os modelos AzureML Python SDK, CLI, REST APIs ou Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Dados gerados pela Microsoft

Ao utilizar serviços como o Automated Machine Learning, a Microsoft pode gerar dados transitórios e pré-processados para treinar vários modelos. Estes dados são armazenados numa loja de dados no seu espaço de trabalho, o que lhe permite impor controlos de acesso e encriptação adequadamente.

Também pode querer encriptar as informações de [diagnóstico registadas a partir do seu ponto final implantado](how-to-enable-app-insights.md) na sua instância Deinsights de Aplicação Azure.

## <a name="monitoring"></a>Monitorização

### <a name="metrics"></a>Métricas

Pode utilizar métricas do Monitor Azure para visualizar e monitorizar métricas para o seu espaço de trabalho de Aprendizagem automática Azure. No [portal Azure,](https://portal.azure.com)selecione o seu espaço de trabalho e, em seguida, selecione **Métricas:**

[![Screenshot mostrando métricas de exemplo para um espaço de trabalho](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

As métricas incluem informações sobre execuções, implementações e registos.

Para mais informações, consulte [Métricas no Monitor Azure](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Registo de atividades

Pode ver o registo de atividade de um espaço de trabalho para ver várias operações que são realizadas no espaço de trabalho. O registo inclui informações básicas como o nome da operação, iniciador de eventos e carimbo de tempo.

Esta imagem mostra o registo de atividade de um espaço de trabalho:

[![Screenshot mostrando o registo de atividade de um espaço de trabalho](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Os detalhes do pedido de pontuação são armazenados em Insights de Aplicação. Application Insights é criado na sua subscrição quando cria um espaço de trabalho. As informações registadas incluem campos como:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* Código de Estado
* Requestid
* Duração

> [!IMPORTANT]
> Algumas ações no espaço de trabalho azure machine learning não registam informações para o registo de atividade. Por exemplo, o início de uma execução de formação e o registo de um modelo não estão registados.
>
> Algumas destas ações aparecem na área de **Atividades** do seu espaço de trabalho, mas estas notificações não indicam quem iniciou a atividade.

## <a name="data-flow-diagrams"></a>Diagramas de fluxo de dados

### <a name="create-workspace"></a>Criar área de trabalho

O diagrama seguinte mostra a criação do fluxo de trabalho do espaço de trabalho.

* Você inscreveu-se no Azure AD a partir de um dos clientes de Machine Learning Azure suportados (Azure CLI, Python SDK, portal Azure) e solicita o token de Gestor de Recursos Azure apropriado.
* Liga para o Gestor de Recursos Azure para criar o espaço de trabalho. 
* O Azure Resource Manager contacta o fornecedor de recursos azure machine learning para fornecer o espaço de trabalho.

Recursos adicionais são criados na subscrição do utilizador durante a criação do espaço de trabalho:

* Cofre chave (para armazenar segredos)
* Uma conta de armazenamento Azure (incluindo blob e partilha de ficheiros)
* Registo de contentores azure (para armazenar imagens do Docker para inferência/pontuação e experimentação)
* Insights de aplicação (para armazenar telemetria)

O utilizador também pode fornecer outros alvos de computação que estão ligados a um espaço de trabalho (como o Serviço Azure Kubernetes ou VMs) conforme necessário.

[![Criar fluxo de trabalho no espaço de trabalho](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Guardar código fonte (scripts de treino)

O diagrama seguinte mostra o fluxo de trabalho de instantâneo de código.

Associados a um espaço de trabalho azure machine learning são diretórios (experiências) que contêm o código fonte (scripts de treino). Estes scripts são armazenados na sua máquina local e na nuvem (no armazenamento Azure Blob para a sua subscrição). Os instantâneos de código são utilizados para execução ou inspeção para auditoria histórica.

[![Fluxo de trabalho instantâneo de código](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Formação

O diagrama seguinte mostra o fluxo de trabalho de treino.

* O Azure Machine Learning é chamado com o ID instantâneo para o instantâneo de código guardado na secção anterior.
* O Azure Machine Learning cria um ID de execução (opcional) e um token de serviço de Machine Learning, que é mais tarde utilizado por alvos de computação como Machine Learning Compute/VMs para comunicar com o serviço de Machine Learning.
* Você pode escolher um alvo de computação gerido (como Machine Learning Compute) ou um alvo de computação não gerido (como VMs) para executar trabalhos de formação. Aqui estão os fluxos de dados para ambos os cenários:
   * VMs/HDInsight, acedido por credenciais SSH num cofre chave na subscrição da Microsoft. O Azure Machine Learning executa o código de gestão no objetivo computacional que:

   1. Prepara o ambiente. (Docker é uma opção para VMs e computadores locais. Consulte os seguintes passos para a Machine Learning Compute para entender como funcionam as experiências em contentores Docker.)
   1. Descarrega o código.
   1. Configura variáveis e configurações ambientais.
   1. Executa as scripts do utilizador (o instantâneo de código mencionado na secção anterior).

   * Machine Learning Compute, acessado através de uma identidade gerida pelo espaço de trabalho.
Como a Machine Learning Compute é um alvo de cálculo gerido (isto é, é gerido pela Microsoft) é gerido pela Microsoft) que é executado sob a sua subscrição da Microsoft.

   1. A construção remota do Docker é iniciada, se necessário.
   1. O código de gestão está escrito na partilha de Ficheiros Azure do utilizador.
   1. O recipiente é iniciado com um comando inicial. Ou seja, código de gestão, tal como descrito no passo anterior.

#### <a name="querying-runs-and-metrics"></a>Consultas e métricas

No diagrama de fluxo abaixo, este passo ocorre quando o alvo computacional de treino escreve as métricas de execução de volta para Azure Machine Learning a partir do armazenamento na base de dados Cosmos DB. Os clientes podem ligar para a Aprendizagem automática azure. O Machine Learning, por sua vez, retirará métricas da base de dados cosmos DB e devolvê-las-á ao cliente.

[![Fluxo de trabalho de formação](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Criação de serviços web

O diagrama seguinte mostra o fluxo de trabalho de inferência. Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para previsão, mais frequentemente em dados de produção.

Aqui estão os detalhes:

* O utilizador regista um modelo utilizando um cliente como o Azure Machine Learning SDK.
* O utilizador cria uma imagem utilizando um modelo, um ficheiro de pontuação e outras dependências do modelo.
* A imagem do Docker é criada e armazenada no Registo de Contentores Azure.
* O serviço web é implantado para o alvo da computação (Container Instances/AKS) utilizando a imagem criada no passo anterior.
* Os detalhes do pedido de pontuação são armazenados em Insights de Aplicação, que está na subscrição do utilizador.
* A telemetria também é empurrada para a subscrição Microsoft/Azure.

[![Fluxo de trabalho de inferência](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* [Serviços web Secure Azure Machine Learning com TLS](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implementado como um serviço web](how-to-consume-web-service.md)
* [Como executar as previsões do lote](how-to-use-parallel-run-step.md)
* [Monitorize os seus modelos de Aprendizagem automática Azure com Insights de Aplicação](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Utilize machine learning azure com rede virtual Azure](how-to-enable-virtual-network.md)
* [Boas práticas para a construção de sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Construa uma recomendação em tempo real API no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
