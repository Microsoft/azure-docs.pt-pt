---
title: Segurança empresarial
titleSuffix: Azure Machine Learning service
description: 'Use com segurança o serviço de Azure Machine Learning: autenticação, autorização, segurança de rede, criptografia de dados e monitoramento.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: 81e8601ac83d43bde0767e38eb387f489d76125b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165248"
---
# <a name="enterprise-security-for-the-azure-machine-learning-service"></a>Segurança da empresa para o serviço de Azure Machine Learning

Neste artigo, você aprenderá sobre os recursos de segurança disponíveis para o serviço de Azure Machine Learning.

Quando você usa um serviço de nuvem, uma prática recomendada é restringir o acesso somente aos usuários que precisam dele. Comece compreendendo o modelo de autenticação e autorização usado pelo serviço. Talvez você também queira restringir o acesso à rede ou unir com segurança recursos em sua rede local com a nuvem. A criptografia de dados também é vital, em repouso e enquanto os dados se movem entre os serviços. Por fim, você precisa ser capaz de monitorar o serviço e produzir um log de auditoria de todas as atividades.

## <a name="authentication"></a>Authentication

A autenticação multifator terá suporte se o Azure Active Directory (Azure AD) estiver configurado para usá-lo. Este é o processo de autenticação:

1. O cliente entra no Azure AD e Obtém um token de Azure Resource Manager.  Os usuários e as entidades de serviço têm suporte total.
1. O cliente apresenta o token para Azure Resource Manager e todos os serviços de Azure Machine Learning.
1. O serviço de Machine Learning fornece um token de serviço de Machine Learning para o destino de computação do usuário (por exemplo, Computação do Machine Learning). Esse token é usado pelo destino de computação do usuário para retornar ao serviço de Machine Learning após a execução ser concluída. O escopo é limitado ao espaço de trabalho.

[![Autenticação no serviço de Azure Machine Learning](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Autenticação para implantação de serviço Web

O Azure Machine Learning dá suporte a duas formas de autenticação para serviços Web: chave e token. Cada serviço Web pode habilitar apenas uma forma de autenticação de cada vez.

|Método de autenticação|Azure Container Instances|AKS|
|---|---|---|
|Chave|Desabilitado por padrão| Habilitado por padrão|
|Certificado de| Não disponível| Desabilitado por padrão |

#### <a name="authentication-with-keys"></a>Autenticação com chaves

Ao habilitar a autenticação de chave para uma implantação, você cria automaticamente chaves de autenticação.

* A autenticação é habilitada por padrão quando você implanta no AKS (serviço kubernetes do Azure).
* A autenticação é desabilitada por padrão quando você implanta em instâncias de contêiner do Azure.

Para habilitar a autenticação de chave, `auth_enabled` use o parâmetro ao criar ou atualizar uma implantação.

Se a autenticação de chave estiver habilitada, você `get_keys` poderá usar o método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se precisar de voltar a gerar uma chave, utilize [ `service.regen_key` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Autenticação com tokens

Quando você habilita a autenticação de token para um serviço Web, os usuários devem apresentar um Azure Machine Learning token Web JSON ao serviço Web para acessá-lo.

* A autenticação de token é desabilitada por padrão quando você implanta no serviço kubernetes do Azure.
* Não há suporte para autenticação de token quando você implanta em instâncias de contêiner do Azure.

Para controlar a autenticação de tokens `token_auth_enabled` , use o parâmetro ao criar ou atualizar uma implantação.

Se a autenticação de token estiver habilitada, você `get_token` poderá usar o método para recuperar um token Web JSON (JWT) e o tempo de expiração desse token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Você precisará solicitar um novo token após a hora do `refresh_by` token.
>
> É altamente recomendável que você crie seu espaço de trabalho do Azure Machine Learning na mesma região que o cluster do serviço kubernetes do Azure. 
>
> Para autenticar com um token, o serviço Web fará uma chamada para a região em que seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho estiver indisponível, você não poderá buscar um token para o serviço Web, mesmo que o cluster esteja em uma região diferente do seu espaço de trabalho. O resultado é que a autenticação do Azure AD não está disponível até que a região do seu espaço de trabalho esteja disponível novamente. 
>
> Além disso, quanto maior a distância entre a região do cluster e a região do seu espaço de trabalho, mais tempo será levado para buscar um token.

## <a name="authorization"></a>Autorização

Pode criar várias áreas de trabalho, e cada área de trabalho pode ser partilhada por várias pessoas. Ao compartilhar um espaço de trabalho, você pode controlar o acesso a ele atribuindo essas funções aos usuários:

* Owner
* Contribuinte
* Leitor

A tabela a seguir lista algumas das principais operações de serviço Azure Machine Learning e as funções que podem executá-las:

| Azure Machine Learning operação de serviço | Owner | Contribuinte | Leitor |
| ---- |:----:|:----:|:----:|
| Criar área de trabalho | ✓ | ✓ | |
| Espaço de trabalho de compartilhamento | ✓ | |  |
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

| Resource | Permissões |
| ----- | ----- |
| Área de trabalho | Contribuinte |
| Conta de armazenamento | Contribuinte de Dados do Armazenamento de Blobs |
| Key Vault | Acesso a todas as chaves, segredos, certificados |
| Registo de Contentores do Azure | Contribuinte |
| Grupo de recursos que contém o espaço de trabalho | Contribuinte |
| Grupo de recursos que contém o cofre de chaves (se for diferente daquele que contém o espaço de trabalho) | Contribuinte |

Não recomendamos que os administradores revoguem o acesso da identidade gerenciada aos recursos mencionados na tabela anterior. Você pode restaurar o acesso usando a operação de ressincronização de chaves.

O serviço de Azure Machine Learning cria um aplicativo adicional (o nome começa `aml-`com) com o acesso no nível de colaborador em sua assinatura para cada região do espaço de trabalho. Por exemplo, se você tiver um espaço de trabalho no leste dos EUA e outro espaço de trabalho em Europa Setentrional na mesma assinatura, você verá dois desses aplicativos. Esses aplicativos permitem que o serviço Azure Machine Learning para ajudá-lo a gerenciar recursos de computação.

## <a name="network-security"></a>Segurança da rede

O serviço de Azure Machine Learning depende de outros serviços do Azure para recursos de computação. Os recursos de computação (destinos de computação) são usados para treinar e implantar modelos. Você pode criar esses destinos de computação em uma rede virtual. Por exemplo, você pode usar o Azure Máquina Virtual de Ciência de Dados para treinar um modelo e, em seguida, implantar o modelo no AKS.  

Para obter mais informações, consulte [como executar experimentos e inferência em uma rede virtual](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Encriptação de dados

### <a name="encryption-at-rest"></a>Encriptação inativa

#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

O serviço de Azure Machine Learning armazena instantâneos, saída e logs na conta de armazenamento de BLOBs do Azure que está vinculada ao espaço de trabalho do Azure Machine Learning Service e à sua assinatura. Todos os dados armazenados no armazenamento de BLOBs do Azure são criptografados em repouso com chaves gerenciadas pela Microsoft.

Para obter informações sobre como usar suas próprias chaves para dados armazenados no armazenamento de BLOBs do Azure, consulte [criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente no Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Os dados de treinamento normalmente também são armazenados no armazenamento de BLOBs do Azure para que ele seja acessível ao treinamento de destinos de computação. Esse armazenamento não é gerenciado pelo Azure Machine Learning, mas montado em destinos de computação como um sistema de arquivos remoto.

Para obter informações sobre como regenerar as chaves de acesso para as contas de armazenamento do Azure usadas com seu espaço de trabalho, consulte [regenerar chaves de acesso de armazenamento](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

O serviço de Azure Machine Learning armazena métricas e metadados na instância Azure Cosmos DB associada a uma assinatura da Microsoft gerenciada pelo serviço Azure Machine Learning. Todos os dados armazenados no Azure Cosmos DB são criptografados em repouso com chaves gerenciadas pela Microsoft.

#### <a name="azure-container-registry"></a>Registo de Contentores do Azure

Todas as imagens de contêiner no registro (registro de contêiner do Azure) são criptografadas em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e a descriptografa imediatamente quando o serviço de Azure Machine Learning efetua pull da imagem.

#### <a name="machine-learning-compute"></a>Computação do Machine Learning

O disco do sistema operacional para cada nó de computação armazenado no armazenamento do Azure é criptografado com chaves gerenciadas pela Microsoft em Azure Machine Learning contas de armazenamento de serviço. Esse destino de computação é efêmero, e os clusters são normalmente reduzidos quando não há execuções na fila. A máquina virtual subjacente é desprovisionada e o disco do sistema operacional é excluído. Não há suporte para Azure Disk Encryption para o disco do sistema operacional.

Cada máquina virtual também tem um disco temporário local para operações do sistema operacional. Se desejar, você pode usar o disco para preparar os dados de treinamento. O disco não está criptografado.
Para obter mais informações sobre como a criptografia em repouso funciona no Azure, consulte [criptografia de dados do Azure em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Criptografia em trânsito

Você pode usar SSL para proteger a comunicação interna entre os microserviços Azure Machine Learning e para proteger chamadas externas para o ponto de extremidade de pontuação. Todo o acesso ao armazenamento do Azure também ocorre em um canal seguro.

Para obter mais informações, consulte [usar SSL para proteger um serviço Web por meio de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Usando Azure Key Vault

O serviço de Azure Machine Learning usa a instância Azure Key Vault associada ao espaço de trabalho para armazenar credenciais de vários tipos:

* A cadeia de conexão da conta de armazenamento associada
* Senhas para instâncias do repositório de contêiner do Azure
* Cadeias de conexão para armazenamentos de dados

As senhas e chaves SSH para computar destinos como o Azure HDInsight e VMs são armazenadas em um cofre de chaves separado que está associado à assinatura da Microsoft. O serviço de Azure Machine Learning não armazena nenhuma senha ou chave fornecida pelos usuários. Em vez disso, ele gera, autoriza e armazena suas próprias chaves SSH para se conectar às VMs e ao HDInsight para executar os experimentos.

Cada espaço de trabalho tem uma identidade gerenciada atribuída pelo sistema associada que tem o mesmo nome que o espaço de trabalho. Essa identidade gerenciada tem acesso a todas as chaves, segredos e certificados no cofre de chaves.

## <a name="monitoring"></a>Monitorização

### <a name="metrics"></a>Métricas

Você pode usar Azure Monitor métricas para exibir e monitorar as métricas para seu espaço de trabalho de serviço do Azure Machine Learning. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho e, em seguida, selecione **métricas**:

[![Captura de tela mostrando métricas de exemplo para um espaço de trabalho](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

As métricas incluem informações sobre execuções, implantações e registros.

Para obter mais informações, consulte [métricas em Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Registo de atividades

Você pode exibir o log de atividades de um espaço de trabalho para ver várias operações executadas no espaço de trabalho. O log inclui informações básicas, como o nome da operação, o iniciador do evento e o carimbo de data/hora.

Esta captura de tela mostra o log de atividades de um espaço de trabalho:

[![Captura de tela mostrando o log de atividades de um espaço de trabalho](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

Os detalhes da solicitação de pontuação são armazenados em Application Insights. Application Insights é criado em sua assinatura quando você cria um espaço de trabalho. As informações registradas incluem campos como HTTPMethod, UserAgent, computable, RequestUrl, StatusCode, RequestId e Duration.

> [!IMPORTANT]
> Algumas ações no espaço de trabalho Azure Machine Learning não registram informações no log de atividades. Por exemplo, o início de uma execução de treinamento e o registro de um modelo não são registrados.
>
> Algumas dessas ações aparecem na área **atividades** do seu espaço de trabalho, mas essas notificações não indicam quem iniciou a atividade.

## <a name="data-flow-diagrams"></a>Diagramas de fluxo de dados

### <a name="create-workspace"></a>Criar área de trabalho

O diagrama a seguir mostra o fluxo de trabalho Create Workspace.

* O usuário entra no Azure AD de um dos clientes do serviço de Azure Machine Learning com suporte (CLI do Azure, SDK do Python, portal do Azure) e solicita o token de Azure Resource Manager apropriado.
* O usuário chama Azure Resource Manager para criar o espaço de trabalho. 
* Azure Resource Manager contata o provedor de recursos de serviço Azure Machine Learning para provisionar o espaço de trabalho.

Recursos adicionais são criados na assinatura do usuário durante a criação do espaço de trabalho:

* Key Vault (para armazenar segredos)
* Uma conta de armazenamento do Azure (incluindo BLOB e compartilhamento de arquivos)
* Registro de contêiner do Azure (para armazenar imagens do Docker para inferência/Pontuação e experimentação)
* Application Insights (para armazenar telemetria)

O usuário também pode provisionar outros destinos de computação anexados a um espaço de trabalho (como o serviço kubernetes do Azure ou VMs), conforme necessário.

[![Criar fluxo de trabalho](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Salvar código-fonte (scripts de treinamento)

O diagrama a seguir mostra o fluxo de trabalho de instantâneo de código.

Associado a um espaço de trabalho de serviço do Azure Machine Learning são diretórios (experimentos) que contêm o código-fonte (scripts de treinamento). Esses scripts são armazenados em seu computador local e na nuvem (no armazenamento de BLOBs do Azure para sua assinatura). Os instantâneos de código são usados para execução ou inspeção para auditoria histórica.

[![Fluxo de trabalho de instantâneo de código](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>Formação

O diagrama a seguir mostra o fluxo de trabalho de treinamento.

* O serviço de Azure Machine Learning é chamado com a ID de instantâneo para o instantâneo de código salvo na seção anterior.
* O serviço de Azure Machine Learning cria uma ID de execução (opcional) e um token de serviço de Machine Learning, que é usado posteriormente por destinos de computação, como Computação do Machine Learning/VMs, para se comunicar com o serviço de Machine Learning.
* Você pode escolher um destino de computação gerenciado (como Computação do Machine Learning) ou um destino de computação não gerenciado (como VMs) para executar seus trabalhos de treinamento. Aqui estão os fluxos de dados para ambos os cenários:
   * VMs/HDInsight, acessadas por credenciais SSH em um cofre de chaves na assinatura da Microsoft. O serviço de Azure Machine Learning executa o código de gerenciamento no destino de computação que:

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

No diagrama de fluxo abaixo, essa etapa ocorre quando o destino de computação de treinamento grava as métricas de execução de volta para o serviço de Azure Machine Learning do armazenamento no banco de dados Cosmos DB. Os clientes podem chamar o serviço de Azure Machine Learning. Machine Learning, por sua vez, transformará as métricas de pull do banco de dados Cosmos DB e as retornará ao cliente.

[![Fluxo de trabalho de treinamento](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Criando Serviços Web

O diagrama a seguir mostra o fluxo de trabalho de inferência. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção.

Aqui estão os detalhes:

* O usuário registra um modelo usando um cliente como o SDK do Azure Machine Learning.
* O usuário cria uma imagem usando um modelo, um arquivo de Pontuação e outras dependências de modelo.
* A imagem do Docker é criada e armazenada no registro de contêiner do Azure.
* O serviço Web é implantado no destino de computação (instâncias de contêiner/AKS) usando a imagem criada na etapa anterior.
* Os detalhes da solicitação de pontuação são armazenados em Application Insights, que está na assinatura do usuário.
* A telemetria também é enviada por push para a assinatura do Microsoft/Azure.

[![Fluxo de trabalho de inferência](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>Passos seguintes

* [Proteger serviços da web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implantado como um serviço Web](how-to-consume-web-service.md)
* [Como executar previsões de batch](how-to-run-batch-predictions.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [SDK do serviço Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usar o serviço de Azure Machine Learning com a rede virtual do Azure](how-to-enable-virtual-network.md)
* [Práticas recomendadas para a criação de sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Criar uma API de recomendação em tempo real no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
