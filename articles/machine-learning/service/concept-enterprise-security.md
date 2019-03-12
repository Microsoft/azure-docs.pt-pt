---
title: Segurança empresarial
titleSuffix: Azure Machine Learning service
description: 'Utilizar o serviço Azure Machine Learning em segurança: autenticação, autorização, segurança de rede, a encriptação de dados e monitorização.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: b950e7d38235d089c6236c76136d8ec2fc7a1f74
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731322"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Segurança empresarial para o serviço Azure Machine Learning

Neste artigo, aprenderá sobre recursos de segurança disponíveis com o Azure serviço Machine learning.

Quando utilizar um serviço em nuvem, é melhor prática para restringir o acesso apenas aos utilizadores que precisam dele. Esta ação inicia por compreender o modelo de autenticação e autorização utilizado pelo serviço. Pode também querer restringir o acesso de rede ou ingressar com segurança a recursos na sua rede no local com aqueles na cloud. Encriptação de dados também é vital, inativos e enquanto os dados são movidos entre serviços. Por fim, precisa ser capaz de monitorizar o serviço e produzir um log de auditoria de todas as atividades.

## <a name="authentication"></a>Authentication
Multi-Factor authentication é suportada se o Azure Active Directory (Azure AD) estiver configurado para o mesmo.
* Cliente inicia sessão no Azure AD e obtém do Azure Resource Manager token.  Os utilizadores e os principais de serviço são totalmente suportados.
* Cliente apresenta o token para o Azure Resource Manager e todos os serviços do Azure Machine Learning
* Serviço de Machine Learning do Azure fornece um token do Azure Machine Learning para a computação de utilizador. Por exemplo, a computação do Machine Learning. Este token é utilizado pelo utilizador do Azure Machine Learning de computação para a chamada de retorno no serviço Azure Machine Learning (limita o âmbito para a área de trabalho) após a execução estiver concluída.

![Captura de ecrã que mostra como funciona a autenticação no serviço Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>Chaves de autenticação para implementação do serviço Web

Quando ativar a autenticação para uma implementação, são criadas automaticamente as chaves de autenticação.

* Autenticação está ativada por predefinição, quando estiver a implementar no serviço Kubernetes do Azure.
* Autenticação está desativada por predefinição, quando estiver a implementar no Azure Container Instances.

Para controlar a autenticação, use o `auth_enabled` parâmetro quando estiver a criar ou atualizar uma implementação.

Se a autenticação estiver ativada, pode utilizar o `get_keys` método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se precisar de voltar a gerar uma chave, utilize [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>Autorização

Pode criar várias áreas de trabalho, e cada área de trabalho pode ser partilhada por várias pessoas. Quando partilha uma área de trabalho, pode controlar o acesso ao mesmo ao atribuir as seguintes funções para os utilizadores:
* Proprietário
* Contribuinte
* Leitor
    
A tabela seguinte lista algumas das operações de serviço principais do Azure Machine Learning e as funções que podem executá-los:

| Serviço Azure Machine Learning operação | Proprietário | Contribuinte | Leitor |
| ---- |:----:|:----:|:----:|
| Criar Área de Trabalho | ✓ | ✓ | |
| Compartilhe a área de trabalho | ✓ | |  |
| Criar a computação | ✓ | ✓ | |
| Anexar computação | ✓ | ✓ | |
| Anexar arquivos de dados | ✓ | ✓ | |
| Executar uma experiência | ✓ | ✓ | |
| Ver execuções/métricas | ✓ | ✓ | ✓ |
| Registar o modelo | ✓ | ✓ | |
| Criar imagem | ✓ | ✓ | |
| Implementar o serviço web | ✓ | ✓ | |
| Modelos de exibição/imagens | ✓ | ✓ | ✓ |
| Chamar o serviço web | ✓ | ✓ | ✓ |

Se as funções incorporadas não são suficientes para as suas necessidades, também pode criar funções personalizadas. Tenha em atenção que as funções personalizadas só que suportamos são para operações da área de trabalho e a computação do Machine Learning. As funções personalizadas podem ter ler, escrever ou eliminar as permissões na área de trabalho e o recurso de computação nessa área de trabalho. Pode disponibilizar a função num nível de área de trabalho específica, um nível de grupo de recursos específico ou um nível de subscrição específica. Para obter mais informações, consulte [gerir utilizadores e funções numa área de trabalho do Azure Machine Learning](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Proteger dados e computação
Os proprietários e contribuintes podem utilizar todas as computação destinos e armazenamentos de dados que estão ligados à área de trabalho.  
Cada área de trabalho também tem um associados atribuído de sistema de identidade gerido (com o mesmo nome que a área de trabalho) com as seguintes permissões no anexado recursos utilizados na área de trabalho:

Para obter mais informações sobre identidades geridas, consulte [geridos identidades para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Recurso | Permissões |
| ----- | ----- |
| Área de trabalho | Contribuinte | 
| Conta de Armazenamento | Contribuinte de dados de Blob de armazenamento | 
| Key Vault | Acesso a todos os certificados de chaves, segredos, | 
| Azure Container Registry | Contribuinte | 
| Grupo de recursos que contém a área de trabalho | Contribuinte | 
| Grupo de recursos que contém o Cofre de chaves (se diferente daquela que contém a área de trabalho) | Contribuinte | 

Recomenda-se que os administradores não revoga o acesso de a identidade gerida para os recursos mencionados acima. Acesso pode ser restaurado com a operação de ressincronização de chaves.

O serviço de Machine Learning do Azure cria uma aplicação adicional (nome inicia com aml-) com o acesso de nível de Contribuidor na sua subscrição para cada região da área de trabalho. Para ex. Se tiver uma área de trabalho na região E.U.A. leste e outra área de trabalho na Europa do Norte na mesma subscrição verá 2 tais aplicativos. Isto é necessário para que os recursos de computação de serviço pode ajudar a gerir o Azure Machine Learning.


## <a name="network-security"></a>Segurança da rede

O serviço Azure Machine Learning depende de outros serviços do Azure para recursos de computação. Recursos de computação (destinos de computação) são utilizados para preparar e implementar modelos. Estes computação destinos podem ser criados numa rede virtual. Por exemplo, pode utilizar a máquina de Virtual de ciência de dados do Microsoft para preparar um modelo e, em seguida, implementar o modelo para o Azure Kubernetes Service (AKS).  

Para obter mais informações, consulte [como a execução de experimentações e inferência numa rede virtual](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Encriptação de dados

### <a name="encryption-at-rest"></a>Encriptação inativa
#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
O serviço de Machine Learning do Azure armazena os instantâneos, saídas e registos na conta de armazenamento de Blobs do Azure que está associada à área de trabalho do serviço do Azure Machine Learning e reside na subscrição do utilizador. Todos os dados armazenados no armazenamento de Blobs do Azure são encriptados em descanso ao utilizar Microsoft-Managed chaves.

Para obter mais informações sobre como colocar suas próprias chaves para os dados armazenados no armazenamento de Blobs do Azure, consulte [encriptação do serviço de armazenamento a utilizar chaves geridas pelo cliente no Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Os dados de preparação, normalmente, também são armazenados no armazenamento de Blobs do Azure para que seja acessível a computação de treinamento. Este armazenamento não é gerenciado pelo Azure Machine Learning mas montado para computação como um sistema de ficheiros remota.

#### <a name="cosmos-db"></a>Cosmos DB
O serviço de Machine Learning do Azure armazena as métricas e metadados para o Cosmos DB que reside numa subscrição Microsoft gerenciada pelo serviço Azure Machine Learning. Todos os dados armazenados no Cosmos DB são encriptados em descanso ao utilizar chaves geridas da Microsoft.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)
Todas as imagens de contentor no seu registo (ACR) são encriptadas em inatividade. O Azure automaticamente criptografa uma imagem antes de os armazenar e desencripta-lo no momento quando o serviço Azure Machine Learning extrai a imagem.

#### <a name="machine-learning-compute"></a>Computação do Machine Learning
O disco do SO para cada nó de computação é armazenado no armazenamento do Azure está encriptado com chaves geridas da Microsoft nas contas de armazenamento do serviço Azure Machine Learning. Este computação é efémeras e clusters normalmente são reduzidos verticalmente quando existirem não existem execuções em fila. A máquina virtual subjacente é desaprovisionada e eliminado do disco do SO. Encriptação de disco do Azure não é suportada para o disco do SO.
Cada máquina virtual também tem um disco local temporário para operações de sistema operacional. Este disco também pode ser, opcionalmente, utilizado para testar dados n o treinamento. Este disco não está encriptado. Para obter mais informações sobre como funciona a encriptação em repouso no Azure, consulte [dados do Azure encriptação em repouso](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest). 

### <a name="encryption-in-transit"></a>Encriptação em trânsito
Ambos os comunicação interna entre as várias microsserviços do Azure Machine Learning e comunicação externa de chamar o ponto final de classificação são suportadas através de SSL. Todo o acesso de armazenamento do Azure também é através de um canal seguro. Para obter mais informações, consulte [proteger o Azure Machine Learning web services usando SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Com o Azure Key Vault
Instância do Key Vault associada com a área de trabalho é utilizada pelo serviço Azure Machine Learning para armazenar credenciais de vários tipos:
* A cadeia de ligação de conta de armazenamento associado
* Palavras-passe para as instâncias de repositório de contentor do Azure
* Ligação de seqüências de caracteres de dados de arquivos. 

Palavras-passe SSH e chaves para destinos, como o HDInsight do HDI e a VM de computação são armazenadas num cofre separado que esteja associado a subscrição do Microsoft. O serviço do Azure Machine Learning armazenar as palavras-passe ou chaves fornecido pelo usuário em vez disso, ele gera, autoriza e armazena as suas próprias chaves SSH para ligar à VM/HDInsight para executar as experimentações. Cada área de trabalho têm uma associado atribuído o sistema gerido identidade (com o mesmo nome que a área de trabalho) que tem acesso a todas as chaves, segredos e certificados no Cofre de chaves.

 
## <a name="monitoring"></a>Monitorização

Os utilizadores podem ver o registo de atividade na área de trabalho para ver várias operações realizadas na área de trabalho e ver as informações básicas, como o nome da operação, o evento iniciado por, timestamp, etc.

Captura de ecrã seguinte mostra o registo de atividades para uma área de trabalho:

![Captura de ecrã que mostra registo de atividades numa área de trabalho](./media/enterprise-readiness/workspace-activity-log.png)


Detalhes do pedido de classificação são armazenadas no AppInsights, o que é criado na subscrição do utilizador ao criar a área de trabalho. Isto inclui campos como HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, duração etc.


## <a name="data-flow-diagram"></a>Diagrama de fluxo de dados

### <a name="create-workspace"></a>Criar Área de Trabalho
O diagrama seguinte mostra o fluxo de trabalho de área de trabalho de criar.
Utilizador inicia sessão no Azure AD a partir de qualquer um dos clientes de serviço suportados do Azure Machine Learning (portal do Azure com a CLI, o SDK de Python,) e solicita o token do Azure Resource Manager adequado.  Utilizador, em seguida, chama o Azure Resource Manager para criar a área de trabalho.  Fornecedor de recursos para aprovisionar a área de trabalho do serviço do Azure Resource Manager contactos do Azure Machine Learning.  Recursos adicionais são criados na subscrição do cliente durante a criação da área de trabalho:
* Cofre de chaves (para armazenar segredos)
* Uma conta de armazenamento do Azure (incluindo o Blob & partilha de ficheiros)
* O Azure Container Registry (para armazenar imagens do docker para inferência e experimentação)
* O Application Insights (para armazenar a telemetria)

Outras computações anexadas a uma área de trabalho (serviço Kubernetes do Azure, etc. VM) também podem ser aprovisionadas pelos clientes, conforme necessário. 

![Captura de ecrã que mostra criar fluxo de trabalho de área de trabalho](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Guarde o código de origem (scripts de preparação)
O diagrama seguinte mostra o fluxo de trabalho de instantâneo do código.
Associado a um Azure Machine Learning área de trabalho do serviço são diretórios (experimentações), que contém o código de origem (scripts de preparação).  Eles são armazenados no computador do cliente de local e na cloud (no armazenamento de Blobs do Azure com a subscrição do cliente). Estes instantâneos de código são utilizados para execução ou de inspeção de histórico de auditoria.

![Captura de ecrã que mostra criar fluxo de trabalho de área de trabalho](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Formação
O diagrama seguinte mostra o fluxo de trabalho de treinamento.
* O serviço do Azure Machine Learning é chamado com o ID do instantâneo para o instantâneo de código guardado acima
* O Azure Machine Learning, que cria o serviço executar ID (opcional) e o token de serviço do Azure Machine Learning, que é posteriormente utilizado pelos destinos de computação, como o Machine Learning computação/VM para falar com o serviço Azure Machine Learning
* Pode escolher qualquer uma computação gerida (ex. Computação de aprendizagem da máquina) ou de computação não-gerenciada (ex. VM) para executar suas tarefas de formação. Fluxo de dados é explicado para ambos os cenários a seguir:
* (VM/HDInsight/Local – acedidos utilizando as credenciais SSH no Key Vault na subscrição da Microsoft) O serviço do Azure Machine Learning executa código de gerenciamento no destino de computação que:
    1.  Prepara o ambiente (observação: O docker é uma opção para a VM/Local também. Veja os passos para a computação do Machine Learning abaixo compreender como em execução experimentação sobre funciona de contentor do docker)
    2.  O código de downloads
    3.  Configura as variáveis de ambiente/configurações
    4.  Executa o script de utilizador (instantâneo de código mencionado acima)
* (Computação do machine Learning – acessada usando a identidade de área de trabalho gerenciada) Tenha em atenção que uma vez que a computação do Machine Learning é uma computação gerida ou seja, gerida pela Microsoft, assim é executado sob a subscrição da Microsoft.
    1.  Construção de Docker remota é iniciada, se necessário
    2.  Escreve um código de gestão para o usuário partilha de ficheiros do Azure
    3.  O contentor começa com inicial de comando ou seja, código de gerenciamento no passo acima


#### <a name="querying-runs--metrics"></a>Consulta de execuções e métricas
Este passo é mostrado no fluxo gravações de computação de treinamento de onde o *métricas executar* volta para o serviço Azure Machine Learning a partir de onde é armazenado no Cosmos DB. Os clientes podem chamar o serviço Azure Machine Learning, que por sua vez retirará métricas do Cosmos DB e retorná-lo novamente para o cliente.

![Captura de ecrã que mostra criar fluxo de trabalho de área de trabalho](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Criação de web services
O diagrama seguinte mostra o fluxo de trabalho de inferência no qual o modelo é implementado como um serviço web.
Consulte os detalhes abaixo:
* Utilizador regista um modelo a utilizar um cliente, como o SDK do Azure ML
* Utilizador cria a imagem usando o modelo, o ficheiro de classificação e outras dependências de modelo
* A imagem do Docker é criada e armazenada no ACR
* Serviço Web é implementada para o destino de computação com a imagem criada acima (ACI/AKS)
* Detalhes do pedido de classificação são armazenadas no AppInsights, o que está na subscrição do utilizador
* Telemetria também é emitidos via push para a subscrição do Microsoft/Azure

![Captura de ecrã que mostra criar fluxo de trabalho de área de trabalho](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>Passos Seguintes

* [Proteger serviços da web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implementado como um serviço web](how-to-consume-web-service.md)
* [Como executar previsões de batch](how-to-run-batch-predictions.md)
* [Monitorizar os seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Serviço de Machine Learning do Azure SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Utilizar o serviço Azure Machine Learning com redes virtuais do Azure](how-to-enable-virtual-network.md)
* [Melhores práticas para criar sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Criar uma API de recomendação em tempo real no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
