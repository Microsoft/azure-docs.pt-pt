---
title: Encriptação de dados com aprendizagem da máquina Azure
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning computa e as lojas de dados fornece encriptação de dados em repouso e em trânsito.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 211ef9571b5a126686b4583330dc0f80863fd47e
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992051"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Encriptação de dados com Azure Machine Learning

A Azure Machine Learning utiliza uma variedade de serviços de armazenamento de dados Azure e recursos de cálculo ao treinar modelos e realizar inferência. Cada um deles tem a sua própria história sobre como fornecem encriptação para dados em repouso e em trânsito. Neste artigo, conheça cada um e qual é o melhor para o seu cenário.

> [!IMPORTANT]
> Para encriptação de grau de produção durante __o treino,__ a Microsoft recomenda a utilização do cluster de cálculo Azure Machine Learning. Para encriptação de nível de produção durante __a inferência,__ a Microsoft recomenda a utilização do Serviço Azure Kubernetes.
>
> A exemplo de computação de aprendizagem automática Azure é um ambiente dev/teste. Ao usá-lo, recomendamos que guarde os seus ficheiros, como cadernos e scripts, numa partilha de ficheiros. Os seus dados devem ser armazenados numa data-loja.

## <a name="encryption-at-rest"></a>Encriptação inativa

> [!IMPORTANT]
> Se o seu espaço de trabalho contiver dados sensíveis, recomendamos que se estabeleça a [bandeira hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) enquanto cria o seu espaço de trabalho. A `hbi_workspace` bandeira só pode ser colocada quando um espaço de trabalho é criado. Não pode ser alterado para um espaço de trabalho existente.

A `hbi_workspace` bandeira controla a quantidade de [dados que](#microsoft-collected-data) a Microsoft recolhe para fins de diagnóstico e permite [encriptação adicional em ambientes geridos pela Microsoft](../security/fundamentals/encryption-atrest.md). Além disso, permite as seguintes ações:

* Começa a encriptar o disco de risco local no seu cluster de cálculo Azure Machine Learning desde que não tenha criado nenhum cluster anterior nessa subscrição. Caso contrário, você precisa levantar um bilhete de apoio para permitir a encriptação do disco de risco dos seus clusters de computação 
* Limpa o disco local de armazenamento temporário entre as execuções
* Passa credenciais de segurança para a sua conta de armazenamento, registo de contentores e conta SSH da camada de execução para os seus clusters de computação usando o seu cofre-chave
* Permite a filtragem IP para garantir que os lotes subjacentes não podem ser chamados por outros serviços externos que não o AzureMachineLearningService
* Por favor, note que as instâncias computacional não são suportadas no espaço de trabalho da HBI

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

A Azure Machine Learning armazena instantâneos, saídas e registos na conta de armazenamento Azure Blob que está ligada ao espaço de trabalho Azure Machine Learning e à sua subscrição. Todos os dados armazenados no armazenamento Azure Blob estão encriptados em repouso com as teclas geridas pela Microsoft.

Para obter informações sobre como utilizar as suas próprias chaves para os dados armazenados no armazenamento Azure Blob, consulte [a encriptação do Armazenamento Azure com as chaves geridas pelo cliente no Cofre da Chave Azure](../storage/common/customer-managed-keys-configure-key-vault.md).

Os dados de treino também são normalmente armazenados no armazenamento do Azure Blob para que seja acessível a alvos de computação de treino. Este armazenamento não é gerido pela Azure Machine Learning, mas montado para calcular alvos como um sistema de ficheiros remotos.

Se precisar de __rodar ou revogar__ a sua chave, pode fazê-lo a qualquer momento. Ao rodar uma chave, a conta de armazenamento começará a usar a nova chave (versão mais recente) para encriptar os dados em repouso. Ao revogar (desativar) uma chave, a conta de armazenamento cuida de pedidos falhados. Normalmente demora uma hora para que a rotação ou a revogação sejam eficazes.

Para obter informações sobre a regeneração das teclas de acesso, consulte [as teclas de acesso ao armazenamento Regenerate](how-to-change-storage-access-key.md).

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

A Azure Machine Learning armazena metadados num exemplo de DB do Azure Cosmos. Este caso está associado a uma subscrição da Microsoft gerida pela Azure Machine Learning. Todos os dados armazenados no Azure Cosmos DB estão encriptados em repouso com as teclas geridas pela Microsoft.

Para utilizar as suas próprias chaves (geridas pelo cliente) para encriptar a instância DB do Azure Cosmos, pode criar uma instância dedicada cosmos DB para uso com o seu espaço de trabalho. Recomendamos esta abordagem se quiser armazenar os seus dados, tais como informações de histórico de execução, fora da instância de DB cosmos multi-arrendatário hospedada na nossa subscrição da Microsoft. 

Para permitir o provisionamento de uma instância de DB cosmos na sua subscrição com chaves geridas pelo cliente, execute as seguintes ações:

* Registe os fornecedores de recursos Microsoft.MachineLearning e Microsoft.DocumentDB na sua subscrição, se ainda não for feito.

* Utilize os seguintes parâmetros ao criar o espaço de trabalho Azure Machine Learning. Ambos os parâmetros são obrigatórios e suportados nos modelos SDK, CLI, REST APIs e Resource Manager.

    * `resource_cmk_uri`: Este parâmetro é o recurso completo URI da chave gerida pelo cliente no seu cofre chave, incluindo as [informações](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)da versão para a chave . 

    * `cmk_keyvault`: Este parâmetro é o ID de recurso do cofre chave na sua subscrição. Este cofre-chave precisa de estar na mesma região e subscrição que você usará para o espaço de trabalho Azure Machine Learning. 
    
        > [!NOTE]
        > Esta instância do cofre chave pode ser diferente do cofre chave que é criado pela Azure Machine Learning quando você forja o espaço de trabalho. Se pretender utilizar a mesma instância de cofre para o espaço de trabalho, passe o mesmo cofre-chave enquanto abaste o espaço de trabalho utilizando o [parâmetro key_vault](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Se precisar de __rodar ou revogar__ a sua chave, pode fazê-lo a qualquer momento. Ao rodar uma chave, a Cosmos DB começará a usar a nova chave (versão mais recente) para encriptar dados em repouso. Ao revogar (desativar) uma chave, a Cosmos DB cuida de pedidos falhados. Normalmente demora uma hora para que a rotação ou a revogação sejam eficazes.

Para obter mais informações sobre as chaves geridas pelo cliente com a Cosmos DB, consulte [as chaves geridas pelo cliente para a sua conta DB Azure Cosmos](../cosmos-db/how-to-setup-cmk.md).

### <a name="azure-container-registry"></a>Registo de Contentores do Azure

Todas as imagens do contentor no seu registo (Registo do Contentor Azure) estão encriptadas em repouso. O Azure encripta automaticamente uma imagem antes de a armazenar e desencriptada quando a Azure Machine Learning puxa a imagem.

Para utilizar as suas próprias chaves (geridas pelo cliente) para encriptar o registo do seu contentor Azure, é necessário criar o seu próprio ACR e anexá-lo enquanto forja o espaço de trabalho ou encriptar a instância padrão que é criada no momento do fornecimento do espaço de trabalho.

> [!IMPORTANT]
> A Azure Machine Learning requer que a conta de administração seja ativada no seu Registo de Contentores Azure. Por predefinição, esta definição é desativada quando cria um registo de contentores. Para obter informações sobre a habilitação da conta de administração, consulte [a conta Admin](../container-registry/container-registry-authentication.md#admin-account).
>
> Uma vez criado um registo de contentores Azure para um espaço de trabalho, não o elimine. Ao fazê-lo, quebrará o seu espaço de trabalho de Aprendizagem de Máquinas Azure.

Para um exemplo de criação de um espaço de trabalho utilizando um registo de contentores Azure existente, consulte os seguintes artigos:

* [Crie um espaço de trabalho para a Azure Machine Learning com Azure CLI](how-to-manage-workspace-cli.md).
* [Crie um espaço de trabalho com Python SDK.](how-to-manage-workspace.md?tabs=python#create-a-workspace)
* [Use um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem de máquinas Azure](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Instância de Contentor do Azure

Pode encriptar um recurso Azure Container Instance (ACI) implantado utilizando chaves geridas pelo cliente. A chave gerida pelo cliente utilizada para o ACI pode ser armazenada no Cofre da Chave Azure para o seu espaço de trabalho. Para obter informações sobre a geração de uma chave, consulte [os dados de encriptação com uma chave gerida pelo cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Para utilizar a chave ao implementar um modelo para Azure Container Instance, crie uma nova configuração de implantação utilizando `AciWebservice.deploy_configuration()` . Forneça as informações-chave utilizando os seguintes parâmetros:

* `cmk_vault_base_url`: O URL do cofre-chave que contém a chave.
* `cmk_key_name`: O nome da chave.
* `cmk_key_version`: A versão da chave.

Para obter mais informações sobre a criação e utilização de uma configuração de implantação, consulte os seguintes artigos:

* [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) referência
* [Onde e como implementar](how-to-deploy-and-where.md)
* [Implementar um modelo no Azure Container Instances](how-to-deploy-azure-container-instance.md)

Para obter mais informações sobre a utilização de uma chave gerida pelo cliente com ACI, consulte [os dados de encriptação com uma chave gerida pelo cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pode encriptar um recurso de Serviço Azure Kubernetes implantado utilizando chaves geridas pelo cliente a qualquer momento. Para mais informações, consulte [Bring your own keys with Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md). 

Este processo permite-lhe encriptar tanto os Dados como o Disco OS das máquinas virtuais implantadas no cluster Kubernetes.

> [!IMPORTANT]
> Este processo funciona apenas com a versão 1.17 ou superior da AKS K8s. A Azure Machine Learning adicionou suporte para AKS 1.17 em 13 de janeiro de 2020.

### <a name="machine-learning-compute"></a>Cálculo de Aprendizagem automática

O disco OS para cada nó de computação armazenado no Azure Storage é encriptado com chaves geridas pela Microsoft nas contas de armazenamento Azure Machine Learning. Este alvo de computação é efémero, e os clusters são tipicamente reduzidos quando não há filas. A máquina virtual subjacente é desavisionada e o disco de SO é eliminado. A encriptação do disco Azure não é suportada para o disco DE.

Cada máquina virtual também tem um disco temporário local para operações de SO. Se quiser, pode usar o disco para encenar dados de treino. O disco é encriptado por padrão para espaços de trabalho com o `hbi_workspace` parâmetro definido para `TRUE` . Este ambiente é de curta duração apenas durante a duração da sua execução, e o suporte de encriptação está limitado apenas a chaves geridas pelo sistema.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks pode ser usado em gasodutos Azure Machine Learning. Por predefinição, o Databricks File System (DBFS) utilizado pelo Azure Databricks é encriptado utilizando uma chave gerida pela Microsoft. Para configurar a Azure Databricks para utilizar chaves geridas pelo cliente, consulte [as teclas geridas pelo cliente no DBFS (raiz) padrão .](/azure/databricks/security/customer-managed-keys-dbfs)

### <a name="microsoft-generated-data"></a>Dados gerados pela Microsoft

Ao utilizar serviços como Machine Learning Automatizado, a Microsoft pode gerar dados transitórios e pré-processados para a formação de vários modelos. Estes dados são armazenados numa loja de dados no seu espaço de trabalho, o que lhe permite impor controlos de acesso e encriptação adequadamente.

Também pode querer encriptar [as informações de diagnóstico registadas a partir do seu ponto final implantado](how-to-enable-app-insights.md) na sua instância Azure Application Insights.

## <a name="encryption-in-transit"></a>Encriptação de dados em circulação

A Azure Machine Learning utiliza o TLS para garantir a comunicação interna entre vários microserviços de aprendizagem automática Azure. Todo o acesso a Azure Storage também ocorre sobre um canal seguro.

Para garantir chamadas externas feitas para o ponto final de pontuação, o Azure Machine Learning utiliza O TLS. Para obter mais informações, consulte [o Use TLS para garantir um serviço web através do Azure Machine Learning](./how-to-secure-web-service.md).

## <a name="data-collection-and-handling"></a>Recolha e tratamento de dados

### <a name="microsoft-collected-data"></a>Dados recolhidos pela Microsoft

A Microsoft pode recolher informações de identificação não-utilizadoras como nomes de recursos (por exemplo, o nome do conjunto de dados ou o nome da experiência de aprendizagem automática), ou variáveis de ambiente de trabalho para fins de diagnóstico. Todos estes dados são armazenados utilizando chaves geridas pela Microsoft no armazenamento alojado em subscrições próprias da Microsoft e seguem as [normas padrão de política de privacidade e tratamento de dados da Microsoft.](https://privacy.microsoft.com/privacystatement)

A Microsoft também recomenda não armazenar informações sensíveis (como segredos-chave de contas) em variáveis ambientais. As variáveis ambientais são registadas, encriptadas e armazenadas por nós. Da mesma forma, ao nomear [run_id](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py), evite incluir informações sensíveis, tais como nomes de utilizadores ou nomes de projetos secretos. Estas informações podem aparecer em registos de telemetria acessíveis aos engenheiros do Microsoft Support.

Pode optar por não recolher dados de diagnóstico, definindo o `hbi_workspace` parâmetro para o fornecimento do espaço de `TRUE` trabalho. Esta funcionalidade é suportada ao utilizar os modelos AzureML Python SDK, CLI, REST APIs ou Azure Resource Manager.

## <a name="using-azure-key-vault"></a>Usando o cofre da chave Azure

A Azure Machine Learning utiliza o exemplo do Cofre da Chave Azure associado ao espaço de trabalho para armazenar credenciais de vários tipos:

* A cadeia de ligação de conta de armazenamento associada
* Palavras-passe para instâncias de repositório de contentores Azure
* Cadeias de ligação a lojas de dados

As palavras-passe e as chaves do SSH para calcular alvos como Azure HDInsight e VMs são armazenadas num cofre de chaves separado que está associado à subscrição da Microsoft. O Azure Machine Learning não armazena senhas ou chaves fornecidas pelos utilizadores. Em vez disso, gera, autoriza e armazena as suas próprias chaves SSH para ligar a VMs e HDInsight para executar as experiências.

Cada espaço de trabalho tem uma identidade gerida associada ao sistema que tem o mesmo nome que o espaço de trabalho. Esta identidade gerida tem acesso a todas as chaves, segredos e certificados no cofre das chaves.

## <a name="next-steps"></a>Próximos passos

* [Ligue-se ao armazenamento Azure](how-to-access-data.md)
* [Obter dados a partir de um arquivo de dados](how-to-create-register-datasets.md)
* [Ligar-se a dados](how-to-connect-data-ui.md)
* [Preparar com conjuntos de dados](how-to-train-with-datasets.md)