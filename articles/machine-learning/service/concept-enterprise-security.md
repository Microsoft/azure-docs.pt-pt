---
title: Segurança empresarial
titleSuffix: Azure Machine Learning service
description: 'Use o serviço de Azure Machine Learning com segurança: autenticação, autorização, segurança de rede, criptografia de dados e monitoramento.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: ebecb69e57c620b2eb84568757c8e3e6f1cb1663
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946395"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Segurança da empresa para o serviço Azure Machine Learning

Neste artigo, você aprenderá sobre os recursos de segurança disponíveis com o serviço Azure Machine Learning.

Ao usar um serviço de nuvem, é uma prática recomendada restringir o acesso somente aos usuários que precisam dele. Isso começa compreendendo o modelo de autenticação e autorização usado pelo serviço. Talvez você também queira restringir o acesso à rede ou unir com segurança recursos em sua rede local com a nuvem. A criptografia de dados também é vital, em repouso e enquanto os dados se movem entre os serviços. Por fim, você precisa ser capaz de monitorar o serviço e produzir um log de auditoria de todas as atividades.

## <a name="authentication"></a>Authentication

A autenticação multifator terá suporte se o Azure Active Directory (Azure AD) estiver configurado para o mesmo.

* O cliente faz logon no Azure AD e obtém Azure Resource Manager token.  Os usuários e as entidades de serviço têm suporte total.
* O cliente apresenta um token para Azure Resource Manager & todos os serviços de Azure Machine Learning
* Azure Machine Learning serviço fornece um token de Azure Machine Learning para a computação do usuário. Por exemplo, Computação do Machine Learning. Esse token é usado pela computação do usuário para chamar de volta para o serviço Azure Machine Learning (limita o escopo ao espaço de trabalho) após a execução ser concluída.

![Captura de tela mostrando como funciona a autenticação no serviço Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-for-web-service-deployment"></a>Autenticação para implantação de serviço Web

O Azure Machine Learning dá suporte a duas formas de autenticação para serviços Web, chave e token. Cada WebService só pode habilitar uma forma de autenticação de cada vez.

|Método de Autenticação|ACI|AKS|
|---|---|---|
|Chave|Desabilitado por padrão| Habilitado por padrão|
|Certificado de| Indisponível| Desabilitado por padrão |

#### <a name="authentication-with-keys"></a>Autenticação com chaves

Ao habilitar a autenticação de chave para uma implantação, você cria automaticamente chaves de autenticação.

* A autenticação é habilitada por padrão quando você está implantando no serviço kubernetes do Azure.
* A autenticação é desabilitada por padrão quando você está implantando em instâncias de contêiner do Azure.

Para habilitar a autenticação de chave, `auth_enabled` use o parâmetro ao criar ou atualizar uma implantação.

Se a autenticação de chave estiver habilitada, você `get_keys` poderá usar o método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se você precisar regenerar uma chave, use[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>Autenticação com tokens

Quando você habilita a autenticação de token para um serviço Web, um usuário deve fornecer um Azure Machine Learning token Web JSON para o serviço Web para acessá-lo.

* A autenticação de token é desabilitada por padrão quando você está implantando no serviço kubernetes do Azure.
* Não há suporte para autenticação de token quando você está implantando em instâncias de contêiner do Azure.

Para controlar a autenticação de token, `token_auth_enabled` use o parâmetro ao criar ou atualizar uma implantação.

Se a autenticação de token estiver habilitada, você `get_token` poderá usar o método para recuperar um token JWT e o tempo de expiração desse token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Será necessário solicitar um novo token após a hora do `refresh_by` token.
>
> É altamente recomendável que você crie seu espaço de trabalho Azure Machine Learning na mesma região que o cluster do serviço kubernetes do Azure. Para autenticar com um token, o serviço Web fará uma chamada para a região em que seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho estiver indisponível, você não poderá buscar um token para o WebService mesmo, se o cluster estiver em uma região diferente do seu espaço de trabalho. Isso efetivamente resulta na indisponibilidade da autenticação do Azure AD até que a região do seu espaço de trabalho esteja disponível novamente. Além disso, quanto maior a distância entre a região do cluster e a região do seu espaço de trabalho, mais tempo será levado para buscar um token.

## <a name="authorization"></a>Autorização

Pode criar várias áreas de trabalho, e cada área de trabalho pode ser partilhada por várias pessoas. Ao compartilhar um espaço de trabalho, você pode controlar o acesso a ele atribuindo as seguintes funções aos usuários:

* Owner
* Contribuinte
* Leitor

A tabela a seguir lista algumas das principais operações de serviço Azure Machine Learning e as funções que podem executá-las:

| Azure Machine Learning operação de serviço | Owner | Contribuinte | Leitor |
| ---- |:----:|:----:|:----:|
| Criar Área de Trabalho | ✓ | ✓ | |
| Espaço de trabalho de compartilhamento | ✓ | |  |
| Criar computação | ✓ | ✓ | |
| Anexar computação | ✓ | ✓ | |
| Anexar repositórios de armazenamento | ✓ | ✓ | |
| Executar um experimento | ✓ | ✓ | |
| Exibir execuções/métricas | ✓ | ✓ | ✓ |
| Registar o modelo | ✓ | ✓ | |
| Criar imagem | ✓ | ✓ | |
| Implantar serviço Web | ✓ | ✓ | |
| Exibir modelos/imagens | ✓ | ✓ | ✓ |
| Chamar serviço Web | ✓ | ✓ | ✓ |

Se as funções internas forem insuficientes para suas necessidades, você também poderá criar funções personalizadas. As únicas funções personalizadas às quais damos suporte são para operações no espaço de trabalho e Computação do Machine Learning. As funções personalizadas podem ter permissões de leitura, gravação ou exclusão no espaço de trabalho e no recurso de computação nesse espaço de trabalho. Você pode tornar a função disponível em um nível de espaço de trabalho específico, um nível de grupo de recursos específico ou um nível de assinatura específico. Para obter mais informações, consulte [gerenciar usuários e funções em um espaço de trabalho Azure Machine Learning](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Protegendo dados e computação

Os proprietários e colaboradores podem usar todos os destinos de computação e armazenamentos de dados anexados ao espaço de trabalho.  
Cada espaço de trabalho também tem uma identidade gerenciada atribuída pelo sistema associada (com o mesmo nome que o espaço de trabalho) com as seguintes permissões nos recursos anexados usados no espaço de trabalho:

Para obter mais informações sobre identidades gerenciadas, consulte [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Resource | Permissões |
| ----- | ----- |
| Área de trabalho | Contribuinte |
| Conta de Armazenamento | Contribuinte de Dados do Armazenamento de Blobs |
| Cofre de Chaves | Acesso a todas as chaves, segredos, certificados |
| Azure Container Registry | Contribuinte |
| Grupo de recursos que contém o espaço de trabalho | Contribuinte |
| Grupo de recursos que contém o Key Vault (se for diferente daquele que contém o espaço de trabalho) | Contribuinte |

É recomendável que os administradores não revoguem o acesso da identidade gerenciada aos recursos mencionados acima. O acesso pode ser restaurado com a operação de ressincronização de chaves.

Azure Machine Learning serviço cria um aplicativo adicional (o nome começa `aml-`com) com o acesso no nível de colaborador em sua assinatura para cada região do espaço de trabalho. Por exemplo, Se você tiver um espaço de trabalho no leste dos EUA e outro espaço de trabalho em Europa Setentrional na mesma assinatura, você verá dois aplicativos como esses. Isso é necessário para que Azure Machine Learning serviço possa ajudar a gerenciar recursos de computação.

## <a name="network-security"></a>Segurança da rede

O serviço de Azure Machine Learning depende de outros serviços do Azure para recursos de computação. Os recursos de computação (destinos de computação) são usados para treinar e implantar modelos. Esses destinos de computação podem ser criados dentro de uma rede virtual. Por exemplo, você pode usar o Máquina Virtual de Ciência de Dados da Microsoft para treinar um modelo e, em seguida, implantar o modelo no AKS (serviço kubernetes do Azure).  

Para obter mais informações, consulte [como executar experimentos e inferência em uma rede virtual](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Encriptação de dados

### <a name="encryption-at-rest"></a>Encriptação inativa

#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Azure Machine Learning serviço armazena instantâneos, saídas e logs na conta de armazenamento de BLOBs do Azure que está vinculada ao espaço de trabalho do serviço Azure Machine Learning e reside na assinatura do usuário. Todos os dados armazenados no armazenamento de BLOBs do Azure são criptografados em repouso usando chaves gerenciadas pela Microsoft.

Para obter mais informações sobre como trazer suas próprias chaves para os dados armazenados no armazenamento de BLOBs do Azure, consulte [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Os dados de treinamento normalmente também são armazenados no armazenamento de BLOBs do Azure para que possam ser acessados para o treinamento de computação. Esse armazenamento não é gerenciado pelo Azure Machine Learning, mas montado para computação como um sistema de arquivos remoto.

Para obter informações sobre como regenerar as chaves de acesso para as contas de armazenamento do Azure usadas com seu espaço de trabalho, consulte o artigo [regenerar chaves de acesso de armazenamento](how-to-change-storage-access-key.md) .

#### <a name="cosmos-db"></a>BD do Cosmos

Azure Machine Learning serviço armazena métricas e metadados para o Cosmos DB que reside em uma assinatura da Microsoft gerenciada pelo serviço Azure Machine Learning. Todos os dados armazenados no Cosmos DB são criptografados em repouso usando chaves gerenciadas pela Microsoft.

#### <a name="azure-container-registry-acr"></a>ACR (registro de contêiner do Azure)

Todas as imagens de contêiner no seu registro (ACR) são criptografadas em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e a descriptografa imediatamente quando Azure Machine Learning serviço extrai a imagem.

#### <a name="machine-learning-compute"></a>Computação do Machine Learning

O disco do sistema operacional para cada nó de computação é armazenado no armazenamento do Azure é criptografado usando chaves gerenciadas pela Microsoft em Azure Machine Learning contas de armazenamento de serviço. Esse destino de computação é efêmero, e os clusters são normalmente reduzidos quando não há execuções na fila. A máquina virtual subjacente é desprovisionada e o disco de so foi excluído. O disco do sistema operacional não oferece suporte à criptografia de disco do Azure.
Cada máquina virtual também tem um disco temporário local para operações do sistema operacional. O disco também pode ser usado opcionalmente para preparar dados de treinamento. O disco não está criptografado.
Para obter mais informações sobre como a criptografia em repouso funciona no Azure, consulte [criptografia de dados do Azure em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Criptografia em trânsito

A comunicação interna entre vários Azure Machine Learning micro serviços e a comunicação externa de chamar o ponto de extremidade de Pontuação tem suporte usando SSL. Todo o acesso ao armazenamento do Azure também está em um canal seguro.
Para obter mais informações, consulte [Secure Azure Machine Learning Web Services usando SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Usando Azure Key Vault

Key Vault instância associada ao espaço de trabalho é usada pelo serviço Azure Machine Learning para armazenar credenciais de vários tipos:

* A cadeia de conexão da conta de armazenamento associada
* Senhas para instâncias do repositório de contêiner do Azure
* Cadeias de conexão para armazenamentos de dados.

As senhas e chaves SSH para computar destinos como HDI HDInsight e VM são armazenadas em um Key Vault separado que está associado à assinatura da Microsoft. Azure Machine Learning serviço não armazena nenhuma senha ou chave fornecida pelo usuário, em vez disso, ele gera, autoriza e armazena suas próprias chaves SSH para se conectar ao VM/HDInsight para executar os experimentos.
Cada espaço de trabalho tem uma identidade gerenciada atribuída pelo sistema associada (com o mesmo nome que o espaço de trabalho) que tem acesso a todas as chaves, segredos e certificados no Key Vault.

## <a name="monitoring"></a>Monitorização

### <a name="metrics"></a>Métricas

Azure Monitor métricas podem ser usadas para exibir e monitorar métricas para seu espaço de trabalho de serviço do Azure Machine Learning. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho e, em seguida, use o link __métricas__ .

![Captura de tela mostrando métricas de exemplo para um espaço de trabalho](./media/enterprise-readiness/workspace-metrics.png)

As métricas incluem informações sobre execuções, implantações e registros.

Para obter mais informações, consulte [métricas em Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Registo de atividades

Você pode ver o log de atividades no espaço de trabalho para ver várias operações executadas no espaço de trabalho e obter as informações básicas, como o nome da operação, evento iniciado por, carimbo de data/hora etc.

A captura de tela a seguir mostra o log de atividades para um espaço de trabalho:

![Captura de tela mostrando o log de atividades em um espaço de trabalho](./media/enterprise-readiness/workspace-activity-log.png)

Os detalhes da solicitação de pontuação são armazenados no Application insights, que é criado na assinatura do usuário durante a criação do espaço de trabalho. As informações registradas incluem campos como HTTPMethod, UserAgent, computable, RequestUrl, StatusCode, RequestId, Duration, etc.

> [!IMPORTANT]
> Algumas ações dentro do espaço de trabalho Azure Machine Learning não registram informações no log de atividades. Por exemplo, iniciar uma execução de treinamento ou registrar um modelo.
>
> Algumas dessas ações aparecem na área __atividades__ do seu espaço de trabalho, no entanto, elas não indicam quem iniciou a atividade.

## <a name="data-flow-diagram"></a>Diagrama de fluxo de dados

### <a name="create-workspace"></a>Criar área de trabalho

O diagrama a seguir mostra o fluxo de trabalho Create Workspace.
O usuário faz logon no Azure AD de qualquer um dos clientes do serviço de Azure Machine Learning com suporte (CLI, SDK do Python, portal do Azure) e solicita o token de Azure Resource Manager apropriado. Em seguida, o usuário chama Azure Resource Manager para criar o espaço de trabalho.  Azure Resource Manager contata o provedor de recursos de serviço Azure Machine Learning para provisionar o espaço de trabalho.  Recursos adicionais são criados na assinatura do cliente durante a criação do espaço de trabalho:

* Keyvault (para armazenar segredos)
* Uma conta de armazenamento do Azure (incluindo blob & FileShare)
* Registro de contêiner do Azure (para armazenar imagens do Docker para inferência/Pontuação e experimentação)
* Application Insights (para armazenar telemetria)

Outras computações anexadas a um espaço de trabalho (serviço kubernetes do Azure, VM etc.) também podem ser provisionadas pelos clientes conforme necessário.

![Captura de tela mostrando criar fluxo de trabalho](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Salvar código-fonte (scripts de treinamento)

O diagrama a seguir mostra o fluxo de trabalho de instantâneo de código.
Associado a um espaço de trabalho de serviço do Azure Machine Learning são diretórios (experimentos), que contém o código-fonte (scripts de treinamento).  Esses scripts são armazenados na máquina local do cliente e na nuvem (no armazenamento de BLOBs do Azure em assinatura do cliente). Os instantâneos de código são usados para execução ou inspeção para auditoria histórica.

![Captura de tela mostrando criar fluxo de trabalho](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Formação

O diagrama a seguir mostra o fluxo de trabalho de treinamento.

* Azure Machine Learning serviço é chamado com a ID de instantâneo para o instantâneo de código salvo acima
* Azure Machine Learning serviço cria a ID de execução (opcional) & Azure Machine Learning token de serviço, que é usado posteriormente pelos destinos de computação como Computação do Machine Learning/VM para se comunicar de volta com o serviço Azure Machine Learning
* Você pode escolher uma computação gerenciada (por exemplo, Computação do Machine Learning) ou computação não gerenciada (ex. VM) para executar seus trabalhos de treinamento. O fluxo de dados é explicado para os dois cenários abaixo:
* (VM/HDInsight – acessado usando credenciais SSH no Key Vault na assinatura da Microsoft) Azure Machine Learning serviço executa o código de gerenciamento no destino de computação que:

   1. Prepara o ambiente. (O Docker é uma opção para VM e local também. Consulte as etapas a seguir para Computação do Machine Learning entender como funciona a execução do experimento no contêiner do Docker.)
   1. Baixa o código.
   1. Configura variáveis de ambiente e configurações.
   1. Executa o script do usuário (instantâneo de código mencionado acima).

* (Computação do Machine Learning – acessado usando a identidade gerenciada pelo espaço de trabalho) Como Computação do Machine Learning é uma computação gerenciada, ela é gerenciada pela Microsoft, como resultado, ela é executada na assinatura da Microsoft.

   1. A construção remota do Docker é inicializada, se necessário.
   1. Grava o código de gerenciamento no compartilhamento de usuários do Azure.
   1. Inicia o contêiner com um comando inicial, ou seja, o código de gerenciamento, conforme descrito na etapa anterior.

#### <a name="querying-runs-and-metrics"></a>Consultando execuções e métricas

Essa etapa é mostrada no fluxo em que a computação de treinamento grava as métricas de *execução* de volta para o serviço de Azure Machine Learning de onde elas são armazenadas no cosmos DB. Os clientes podem chamar Azure Machine Learning serviço que, por sua vez, realizará métricas de pull do Cosmos DB e retornará ao cliente.

![Captura de tela mostrando criar fluxo de trabalho](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Criando Serviços Web

O diagrama a seguir mostra o fluxo de trabalho de inferência. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção.
Veja os detalhes abaixo:

* O usuário registra um modelo usando um cliente como o SDK do Azure ML
* O usuário cria a imagem usando modelo, arquivo de Pontuação e outras dependências de modelo
* A imagem do Docker é criada e armazenada no ACR
* O WebService é implantado no destino de computação (ACI/AKS) usando a imagem criada acima
* Os detalhes da solicitação de pontuação são armazenados no Application insights, que está na assinatura do usuário
* A telemetria também é enviada por push para a assinatura do Microsoft/Azure

![Captura de tela mostrando criar fluxo de trabalho](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>Passos seguintes

* [Proteger serviços da web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implementado como um serviço web](how-to-consume-web-service.md)
* [Como executar previsões de batch](how-to-run-batch-predictions.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [SDK do serviço Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usar o serviço de Azure Machine Learning com redes virtuais do Azure](how-to-enable-virtual-network.md)
* [Práticas recomendadas para a criação de sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Criar uma API de recomendação em tempo real no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
