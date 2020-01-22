---
title: Segurança empresarial
titleSuffix: Azure Machine Learning
description: 'Use Azure Machine Learning com segurança: autenticação, autorização, segurança de rede, criptografia de dados e monitoramento.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/09/2020
ms.openlocfilehash: bc083a95ebf6c7ecfabfef87e606f99053ba58bb
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312418"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Segurança corporativa para Azure Machine Learning

Neste artigo, você aprenderá sobre os recursos de segurança disponíveis para Azure Machine Learning.

Quando você usa um serviço de nuvem, uma prática recomendada é restringir o acesso somente aos usuários que precisam dele. Comece compreendendo o modelo de autenticação e autorização usado pelo serviço. Talvez você também queira restringir o acesso à rede ou unir com segurança recursos em sua rede local com a nuvem. A criptografia de dados também é vital, em repouso e enquanto os dados se movem entre os serviços. Por fim, você precisa ser capaz de monitorar o serviço e produzir um log de auditoria de todas as atividades.

> [!NOTE]
> As informações neste artigo funcionam com o Azure Machine Learning SDK do Python versão 1.0.83.1 ou superior.

## <a name="authentication"></a>Autenticação

A autenticação multifator terá suporte se o Azure Active Directory (Azure AD) estiver configurado para usá-lo. Este é o processo de autenticação:

1. O cliente entra no Azure AD e Obtém um token de Azure Resource Manager.  Os usuários e as entidades de serviço têm suporte total.
1. O cliente apresenta o token para Azure Resource Manager e todos os Azure Machine Learning.
1. O serviço de Machine Learning fornece um token de serviço de Machine Learning para o destino de computação do usuário (por exemplo, Computação do Machine Learning). Esse token é usado pelo destino de computação do usuário para retornar ao serviço de Machine Learning após a execução ser concluída. O escopo é limitado ao espaço de trabalho.

[![autenticação no Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Para obter mais informações, consulte [Configurar a autenticação para Azure Machine Learning recursos e fluxos de trabalho](how-to-setup-authentication.md). Este artigo fornece informações e exemplos de autenticação, incluindo o uso de entidades de serviço e fluxos de trabalho automatizados.


### <a name="authentication-for-web-service-deployment"></a>Autenticação para implantação de serviço Web

O Azure Machine Learning dá suporte a duas formas de autenticação para serviços Web: chave e token. Cada serviço Web pode habilitar apenas uma forma de autenticação de cada vez.

|Método de autenticação|Descrição|Azure Container Instances|AKS|
|---|---|---|---|
|Chave|As chaves são estáticas e não precisam ser atualizadas. As chaves podem ser geradas novamente manualmente.|Desabilitado por padrão| Ativado por predefinição|
|Certificado de|Tokens expiram após um período de tempo especificado e precisam ser atualizados.| Não disponível| Desabilitado por padrão |

Para obter exemplos de código, consulte a [seção autenticação de serviço Web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorização

Pode criar várias áreas de trabalho, e cada área de trabalho pode ser partilhada por várias pessoas. Ao compartilhar um espaço de trabalho, você pode controlar o acesso a ele atribuindo essas funções aos usuários:

* Proprietário
* Contribuinte
* Leitor

A tabela a seguir lista algumas das principais operações de Azure Machine Learning e as funções que podem executá-las:

| Azure Machine Learning operação | Proprietário | Contribuinte | Leitor |
| ---- |:----:|:----:|:----:|
| Criar área de trabalho | ✓ | ✓ | |
| Espaço de trabalho de compartilhamento | ✓ | |  |
| Atualizar espaço de trabalho para Enterprise Edition | ✓ | |
| Criar destino de computação | ✓ | ✓ | |
| Anexar destino de computação | ✓ | ✓ | |
| Anexar armazenamentos de dados | ✓ | ✓ | |
| Execute experimentação | ✓ | ✓ | |
| Exibir execuções/métricas | ✓ | ✓ | ✓ |
| Registar o modelo | ✓ | ✓ | |
| Criar imagem | ✓ | ✓ | |
| Implantar serviço Web | ✓ | ✓ | |
| Exibir modelos/imagens | ✓ | ✓ | ✓ |
| Chamar serviço Web | ✓ | ✓ | ✓ |

Se as funções internas não atenderem às suas necessidades, você poderá criar funções personalizadas. As funções personalizadas têm suporte apenas para operações no espaço de trabalho e Computação do Machine Learning. As funções personalizadas podem ter permissões de leitura, gravação ou exclusão no espaço de trabalho e no recurso de computação nesse espaço de trabalho. Você pode tornar a função disponível em um nível de espaço de trabalho específico, um nível de grupo de recursos específico ou um nível de assinatura específico. Para obter mais informações, consulte [gerenciar usuários e funções em um espaço de trabalho Azure Machine Learning](how-to-assign-roles.md).

### <a name="securing-compute-targets-and-data"></a>Protegendo dados e destinos de computação

Os proprietários e colaboradores podem usar todos os destinos de computação e armazenamentos de dados anexados ao espaço de trabalho.  

Cada espaço de trabalho também tem uma identidade gerenciada atribuída pelo sistema associada que tem o mesmo nome que o espaço de trabalho. A identidade gerenciada tem as seguintes permissões nos recursos anexados usados no espaço de trabalho.

Para obter mais informações sobre identidades gerenciadas, consulte [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Recurso | Permissões |
| ----- | ----- |
| Área de trabalho | Contribuinte |
| Conta de armazenamento | Colaborador de dados do blob de armazenamento |
| Key vault | Acesso a todas as chaves, segredos, certificados |
| Azure Container Registry | Contribuinte |
| Grupo de recursos que contém o espaço de trabalho | Contribuinte |
| Grupo de recursos que contém o cofre de chaves (se for diferente daquele que contém o espaço de trabalho) | Contribuinte |

Não recomendamos que os administradores revoguem o acesso da identidade gerenciada aos recursos mencionados na tabela anterior. Você pode restaurar o acesso usando a operação de ressincronização de chaves.

Azure Machine Learning cria um aplicativo adicional (o nome começa com `aml-` ou `Microsoft-AzureML-Support-App-`) com acesso no nível de colaborador em sua assinatura para cada região do espaço de trabalho. Por exemplo, se você tiver um espaço de trabalho no leste dos EUA e um em Europa Setentrional na mesma assinatura, verá dois desses aplicativos. Esses aplicativos permitem Azure Machine Learning para ajudá-lo a gerenciar recursos de computação.

## <a name="network-security"></a>Segurança da rede

Azure Machine Learning se baseia em outros serviços do Azure para recursos de computação. Os recursos de computação (destinos de computação) são usados para treinar e implantar modelos. Você pode criar esses destinos de computação em uma rede virtual. Por exemplo, você pode usar o Azure Máquina Virtual de Ciência de Dados para treinar um modelo e, em seguida, implantar o modelo no AKS.  

Para obter mais informações, consulte [como executar experimentos e inferência em uma rede virtual](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Encriptação de dados

### <a name="encryption-at-rest"></a>Encriptação inativa

> [!IMPORTANT]
> Se seu espaço de trabalho contiver dados confidenciais, recomendamos definir o [sinalizador hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) ao criar seu espaço de trabalho. Isso controla a quantidade de dados que a Microsoft coleta para fins de diagnóstico e habilita a criptografia adicional em ambientes gerenciados da Microsoft.


#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

O Azure Machine Learning armazena instantâneos, saída e logs na conta de armazenamento de BLOBs do Azure que está vinculada ao espaço de trabalho Azure Machine Learning e à sua assinatura. Todos os dados armazenados no armazenamento de BLOBs do Azure são criptografados em repouso com chaves gerenciadas pela Microsoft.

Para obter informações sobre como usar suas próprias chaves para dados armazenados no armazenamento de BLOBs do Azure, consulte [criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente no Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Os dados de treinamento normalmente também são armazenados no armazenamento de BLOBs do Azure para que ele seja acessível ao treinamento de destinos de computação. Esse armazenamento não é gerenciado pelo Azure Machine Learning, mas montado em destinos de computação como um sistema de arquivos remoto.

Para obter informações sobre como regenerar as chaves de acesso, consulte [regenerar chaves de acesso de armazenamento](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning armazena métricas e metadados em uma instância de Azure Cosmos DB. Essa instância está associada a uma assinatura da Microsoft gerenciada pelo Azure Machine Learning. Todos os dados armazenados no Azure Cosmos DB são criptografados em repouso com chaves gerenciadas pela Microsoft.

Para usar suas próprias chaves (gerenciadas pelo cliente) para criptografar a instância de Azure Cosmos DB, você pode criar uma instância de Cosmos DB dedicada para uso com seu espaço de trabalho. Recomendamos essa abordagem se você quiser armazenar seus dados, como informações de histórico de execução, fora da instância de Cosmos DB multilocatário hospedada em nossa assinatura da Microsoft. 

> [!NOTE]
> Esse recurso está disponível no momento apenas no leste dos EUA, oeste dos EUA 2, centro-sul dos EUA.

Para habilitar o provisionamento de uma instância de Cosmos DB em sua assinatura com chaves gerenciadas pelo cliente, execute as seguintes ações:

* Habilite os recursos principais gerenciados pelo cliente para Cosmos DB. Neste momento, você deve solicitar acesso para usar esse recurso. Para fazer isso, entre em contato com [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

* Registre o Azure Machine Learning e Azure Cosmos DB provedores de recursos em sua assinatura, se ainda não tiver feito isso.

* Autorize o aplicativo Machine Learning (no gerenciamento de identidade e acesso) com permissões de colaborador em sua assinatura.

    ![Autorizar o ' Azure Machine Learning app ' no gerenciamento de identidade e acesso no portal](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Use os parâmetros a seguir ao criar o espaço de trabalho Azure Machine Learning. Ambos os parâmetros são obrigatórios e têm suporte no SDK, CLI, APIs REST e modelos do Resource Manager.

    * `resource_cmk_uri`: esse parâmetro é o URI de recurso completo da chave gerenciada pelo cliente no cofre de chaves, incluindo as [informações de versão da chave](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: esse parâmetro é a ID de recurso do cofre de chaves em sua assinatura. Esse cofre de chaves precisa estar na mesma região e assinatura que você usará para o espaço de trabalho Azure Machine Learning. 
    
        > [!NOTE]
        > Essa instância do cofre de chaves pode ser diferente do cofre de chaves criado pelo Azure Machine Learning ao provisionar o espaço de trabalho. Se você quiser usar a mesma instância do cofre de chaves para o espaço de trabalho, passe o mesmo cofre de chaves ao provisionar o espaço de trabalho usando o [parâmetro key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Essa instância de Cosmos DB é criada em um grupo de recursos gerenciado pela Microsoft em sua assinatura. 

> [!IMPORTANT]
> * Se você precisar excluir esta instância de Cosmos DB, deverá excluir o espaço de trabalho Azure Machine Learning que a utiliza. 
> * As [__unidades de solicitação__](../cosmos-db/request-units.md) padrão para essa conta de Cosmos DB são definidas em __8000__. Não há suporte para a alteração desse valor. 

Para obter mais informações sobre chaves gerenciadas pelo cliente com Cosmos DB, consulte [Configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

Todas as imagens de contêiner no registro (registro de contêiner do Azure) são criptografadas em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e a descriptografa quando Azure Machine Learning efetua pull da imagem.

Para usar suas próprias chaves (gerenciadas pelo cliente) para criptografar o registro de contêiner do Azure, você precisa criar seu próprio ACR e anexá-lo ao provisionar o espaço de trabalho ou criptografar a instância padrão que é criada no momento do provisionamento do espaço de trabalho.

Para obter um exemplo de como criar um espaço de trabalho usando um registro de contêiner do Azure existente, consulte os seguintes artigos:

* [Crie um espaço de trabalho para Azure Machine Learning com CLI do Azure](how-to-manage-workspace-cli.md).
* [Use um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Instância de Contentor do Azure

A instância de contêiner do Azure não dá suporte à criptografia de disco. Se você precisar de criptografia de disco, recomendamos [a implantação em uma instância do serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md) . Nesse caso, você também pode querer usar o suporte de Azure Machine Learning para controles de acesso baseado em função para impedir implantações em uma instância de contêiner do Azure em sua assinatura.

#### <a name="azure-kubernetes-service"></a>Serviço Kubernetes do Azure

Você pode criptografar um recurso implantado do serviço kubernetes do Azure usando chaves gerenciadas pelo cliente a qualquer momento. Para obter mais informações, consulte [traga suas próprias chaves com o serviço kubernetes do Azure](../aks/azure-disk-customer-managed-keys.md). 

Esse processo permite que você criptografe os dados e o disco do sistema operacional das máquinas virtuais implantadas no cluster kubernetes.

> [!IMPORTANT]
> Esse processo funciona apenas com o AKS K8s versão 1,17 ou superior. Azure Machine Learning adicionou suporte para AKS 1,17 em 13 de janeiro de 2020.

#### <a name="machine-learning-compute"></a>Computação do Machine Learning

O disco do sistema operacional para cada nó de computação armazenado no armazenamento do Azure é criptografado com chaves gerenciadas pela Microsoft em Azure Machine Learning contas de armazenamento. Esse destino de computação é efêmero, e os clusters são normalmente reduzidos quando não há execuções na fila. A máquina virtual subjacente é desprovisionada e o disco do sistema operacional é excluído. Não há suporte para Azure Disk Encryption para o disco do sistema operacional.

Cada máquina virtual também tem um disco temporário local para operações do sistema operacional. Se desejar, você pode usar o disco para preparar os dados de treinamento. O disco é criptografado por padrão para espaços de trabalho com o parâmetro `hbi_workspace` definido como `TRUE`. Esse ambiente é de curta duração apenas durante a execução, e o suporte à criptografia é limitado apenas a chaves gerenciadas pelo sistema.

Para obter mais informações sobre como a criptografia em repouso funciona no Azure, consulte [criptografia de dados do Azure em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Encriptação de dados em circulação

Você pode usar SSL para proteger a comunicação interna entre os microserviços Azure Machine Learning e para proteger chamadas externas para o ponto de extremidade de pontuação. Todo o acesso ao armazenamento do Azure também ocorre em um canal seguro.

Para obter mais informações, consulte [usar SSL para proteger um serviço Web por meio de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Usando Azure Key Vault

Azure Machine Learning usa a instância Azure Key Vault associada ao espaço de trabalho para armazenar credenciais de vários tipos:

* A cadeia de conexão da conta de armazenamento associada
* Senhas para instâncias do repositório de contêiner do Azure
* Cadeias de conexão para armazenamentos de dados

As senhas e chaves SSH para computar destinos como o Azure HDInsight e VMs são armazenadas em um cofre de chaves separado que está associado à assinatura da Microsoft. Azure Machine Learning não armazena nenhuma senha ou chave fornecida pelos usuários. Em vez disso, ele gera, autoriza e armazena suas próprias chaves SSH para se conectar às VMs e ao HDInsight para executar os experimentos.

Cada espaço de trabalho tem uma identidade gerenciada atribuída pelo sistema associada que tem o mesmo nome que o espaço de trabalho. Essa identidade gerenciada tem acesso a todas as chaves, segredos e certificados no cofre de chaves.

## <a name="data-collection-and-handling"></a>Coleta e manipulação de dados

### <a name="microsoft-collected-data"></a>Dados coletados pela Microsoft

A Microsoft pode coletar informações que não são de usuário, como nomes de recursos (por exemplo, o nome do conjunto de dados ou o nome do experimento do Machine Learning) ou variáveis de ambiente de trabalho para fins de diagnóstico. Todos esses dados são armazenados usando chaves gerenciadas pela Microsoft no armazenamento hospedado em assinaturas de propriedade da Microsoft e seguem [os padrões de política de privacidade padrão da Microsoft e de manipulação de dados](https://privacy.microsoft.com/privacystatement).

A Microsoft também recomenda o não armazenamento de informações confidenciais (como segredos de chave de conta) em variáveis de ambiente. As variáveis de ambiente são registradas, criptografadas e armazenadas por nós.

Você pode recusar os dados de diagnóstico que estão sendo coletados definindo o parâmetro `hbi_workspace` como `TRUE` ao provisionar o espaço de trabalho. Essa funcionalidade tem suporte ao usar o SDK Python do AzureML, a CLI, as APIs REST ou os modelos de Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Dados gerados pela Microsoft

Ao usar serviços como o Machine Learning automatizado, a Microsoft pode gerar dados temporários e pré-processados para treinar vários modelos. Esses dados são armazenados em um datastore em seu espaço de trabalho, o que permite que você aplique controles de acesso e criptografia adequadamente.

Você também pode querer criptografar [as informações de diagnóstico registradas de seu ponto de extremidade implantado](how-to-enable-app-insights.md) em sua instância do aplicativo Azure insights.

## <a name="monitoring"></a>Monitorização

### <a name="metrics"></a>Métricas

Você pode usar Azure Monitor métricas para exibir e monitorar as métricas de seu espaço de trabalho Azure Machine Learning. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho e, em seguida, selecione **métricas**:

[Captura de tela de ![mostrando métricas de exemplo para um espaço de trabalho](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

As métricas incluem informações sobre execuções, implantações e registros.

Para obter mais informações, consulte [métricas em Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Registo de atividades

Você pode exibir o log de atividades de um espaço de trabalho para ver várias operações executadas no espaço de trabalho. O log inclui informações básicas, como o nome da operação, o iniciador do evento e o carimbo de data/hora.

Esta captura de tela mostra o log de atividades de um espaço de trabalho:

[Captura de tela ![mostrando o log de atividades de um espaço de trabalho](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Os detalhes da solicitação de pontuação são armazenados em Application Insights. Application Insights é criado em sua assinatura quando você cria um espaço de trabalho. As informações registradas incluem campos como:

* HTTPMethod
* UserAgent
* Computatype
* RequestUrl
* StatusCode
* RequestId
* Duração

> [!IMPORTANT]
> Algumas ações no espaço de trabalho Azure Machine Learning não registram informações no log de atividades. Por exemplo, o início de uma execução de treinamento e o registro de um modelo não são registrados.
>
> Algumas dessas ações aparecem na área **atividades** do seu espaço de trabalho, mas essas notificações não indicam quem iniciou a atividade.

## <a name="data-flow-diagrams"></a>Diagramas de fluxo de dados

### <a name="create-workspace"></a>Criar área de trabalho

O diagrama a seguir mostra o fluxo de trabalho Create Workspace.

* Você entra no Azure AD de um dos clientes Azure Machine Learning com suporte (CLI do Azure, Python SDK, portal do Azure) e solicita o token de Azure Resource Manager apropriado.
* Você chama Azure Resource Manager para criar o espaço de trabalho. 
* Azure Resource Manager contata o provedor de recursos de Azure Machine Learning para provisionar o espaço de trabalho.

Recursos adicionais são criados na assinatura do usuário durante a criação do espaço de trabalho:

* Key Vault (para armazenar segredos)
* Uma conta de armazenamento do Azure (incluindo BLOB e compartilhamento de arquivos)
* Registro de contêiner do Azure (para armazenar imagens do Docker para inferência/Pontuação e experimentação)
* Application Insights (para armazenar telemetria)

O usuário também pode provisionar outros destinos de computação anexados a um espaço de trabalho (como o serviço kubernetes do Azure ou VMs), conforme necessário.

[fluxo de trabalho criar ![](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Salvar código-fonte (scripts de treinamento)

O diagrama a seguir mostra o fluxo de trabalho de instantâneo de código.

Associado a um espaço de trabalho Azure Machine Learning são os diretórios (experimentos) que contêm o código-fonte (scripts de treinamento). Esses scripts são armazenados em seu computador local e na nuvem (no armazenamento de BLOBs do Azure para sua assinatura). Os instantâneos de código são usados para execução ou inspeção para auditoria histórica.

[fluxo de trabalho de instantâneo de código ![](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Formação

O diagrama a seguir mostra o fluxo de trabalho de treinamento.

* Azure Machine Learning é chamado com a ID de instantâneo para o instantâneo de código salvo na seção anterior.
* Azure Machine Learning cria uma ID de execução (opcional) e um token de serviço de Machine Learning, que é usado posteriormente por destinos de computação como Computação do Machine Learning/VMs para se comunicar com o serviço Machine Learning.
* Você pode escolher um destino de computação gerenciado (como Computação do Machine Learning) ou um destino de computação não gerenciado (como VMs) para executar trabalhos de treinamento. Aqui estão os fluxos de dados para ambos os cenários:
   * VMs/HDInsight, acessadas por credenciais SSH em um cofre de chaves na assinatura da Microsoft. Azure Machine Learning executa o código de gerenciamento no destino de computação que:

   1. Prepara o ambiente. (O Docker é uma opção para VMs e computadores locais. Consulte as etapas a seguir para Computação do Machine Learning entender como funciona a execução de experimentos em contêineres do Docker.)
   1. Baixa o código.
   1. Define as configurações e variáveis de ambiente.
   1. Executa scripts de usuário (o instantâneo de código mencionado na seção anterior).

   * Computação do Machine Learning, acessado por meio de uma identidade gerenciada por espaço de trabalho.
Como Computação do Machine Learning é um destino de computação gerenciado (ou seja, é gerenciado pela Microsoft), ele é executado em sua assinatura da Microsoft.

   1. A construção remota do Docker é inicializada, se necessário.
   1. O código de gerenciamento é gravado no compartilhamento de arquivos do Azure do usuário.
   1. O contêiner é iniciado com um comando inicial. Ou seja, o código de gerenciamento, conforme descrito na etapa anterior.

#### <a name="querying-runs-and-metrics"></a>Consultando execuções e métricas

No diagrama de fluxo abaixo, essa etapa ocorre quando o destino de computação de treinamento grava as métricas de execução de volta para Azure Machine Learning do armazenamento no banco de dados Cosmos DB. Os clientes podem chamar Azure Machine Learning. Machine Learning, por sua vez, transformará as métricas de pull do banco de dados Cosmos DB e as retornará ao cliente.

[fluxo de trabalho de treinamento ![](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Criando Serviços Web

O diagrama a seguir mostra o fluxo de trabalho de inferência. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção.

Aqui estão os detalhes:

* O usuário registra um modelo usando um cliente como o SDK do Azure Machine Learning.
* O usuário cria uma imagem usando um modelo, um arquivo de Pontuação e outras dependências de modelo.
* A imagem do Docker é criada e armazenada no registro de contêiner do Azure.
* O serviço Web é implantado no destino de computação (instâncias de contêiner/AKS) usando a imagem criada na etapa anterior.
* Os detalhes da solicitação de pontuação são armazenados em Application Insights, que está na assinatura do usuário.
* A telemetria também é enviada por push para a assinatura do Microsoft/Azure.

[fluxo de trabalho de inferência de ![](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* [Proteger serviços da web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implantado como um serviço Web](how-to-consume-web-service.md)
* [Como executar previsões de batch](how-to-use-parallel-run-step.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usar Azure Machine Learning com a rede virtual do Azure](how-to-enable-virtual-network.md)
* [Práticas recomendadas para a criação de sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Criar uma API de recomendação em tempo real no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
