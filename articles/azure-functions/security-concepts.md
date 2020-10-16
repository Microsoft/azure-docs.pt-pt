---
title: Assegurar funções de Azure
description: Saiba como tornar o seu código de função em funcionamento em Azure mais seguro de ataques comuns.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: cd97193fdf6549e667578e36f0be9104e4381d30
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102311"
---
# <a name="securing-azure-functions"></a>Assegurar funções de Azure

Em muitos aspetos, o planeamento para o desenvolvimento seguro, implementação e funcionamento de funções sem servidor é praticamente o mesmo que para qualquer aplicação baseada na Web ou na nuvem. [O Azure App Service](../app-service/index.yml) fornece a infraestrutura de hospedagem para as suas aplicações de função. Este artigo fornece estratégias de segurança para executar o seu código de função e como o Serviço de Aplicações pode ajudá-lo a proteger as suas funções. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Para um conjunto de recomendações de segurança que seguem o [Benchmark de Segurança Azure,](../security/benchmarks/overview.md)consulte [a Linha de Base de Segurança Azure para funções de Azure](security-baseline.md).

## <a name="secure-operation"></a>Operação segura 

Esta secção guia-o para configurar e executar a sua aplicação de função o mais seguro possível. 

### <a name="security-center"></a>Centro de Segurança

O Security Center integra-se com a sua aplicação de função no portal. Fornece, gratuitamente, uma avaliação rápida das potenciais vulnerabilidades de segurança relacionadas com a configuração. As aplicações de função que executam num plano dedicado também podem usar as funcionalidades de segurança em tempo real do Security Center, por um custo adicional. Para saber mais, consulte [Protect your Azure App Service web apps and APIs](../security-center/defender-for-app-service-introduction.md). 

### <a name="log-and-monitor"></a>Registar e monitorizar

Um para detetar ataques é através da atividade de monitorização da atividade e da análise de registo. As funções integram-se com o Application Insights para recolher dados de registo, desempenho e erro para a sua aplicação de função. O Application Insights deteta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e a compreender como as suas funções são utilizadas. Para saber mais, consulte [as Funções Do Monitor Azure](functions-monitoring.md).

As funções também se integram com registos do Monitor Azure para permitir a consolidação de registos de aplicações de funções com eventos do sistema para uma análise mais fácil. Pode utilizar definições de diagnóstico para configurar a exportação de streaming de registos e métricas da plataforma para as suas funções para o destino à sua escolha, como um espaço de trabalho Logs Analytics. Para saber mais, consulte [as funções de Azure monitor de monitorização com registos do monitor Azure](functions-monitor-log-analytics.md). 

Para a deteção e automatização de resposta a nível da empresa, transmita os seus registos e eventos para um espaço de trabalho do Logs Analytics. Em seguida, pode ligar o Azure Sentinel a este espaço de trabalho. Para saber mais, veja [o que é Azure Sentinel.](../sentinel/overview.md)  

Para obter mais recomendações de segurança para a observabilidade, consulte a [linha de base de segurança Azure para as Funções Azure](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>RequerEM HTTPS

Por predefinição, os clientes podem ligar-se aos pontos finais da função utilizando http ou HTTPS. Deve redirecionar HTTP para HTTPs porque HTTPS utiliza o protocolo SSL/TLS para fornecer uma ligação segura, que é encriptada e autenticada. Para aprender como, consulte [Impor HTTPS](../app-service/configure-ssl-bindings.md#enforce-https).

Quando necessitar de HTTPS, também deverá requerer a versão mais recente do TLS. Para saber como, consulte [as versões Do 'Enforce TLS'.](../app-service/configure-ssl-bindings.md#enforce-tls-versions)

Para obter mais informações, consulte [ligações Secure (TSL)](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>Teclas de acesso de função

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Chave do sistema 

Extensões específicas podem requerer uma chave gerida pelo sistema para aceder a pontos finais webhook. As teclas do sistema são concebidas para pontos finais de função específicos de extensão que são chamadas por componentes internos. Por exemplo, o [gatilho da Grelha de Eventos](functions-bindings-event-grid-trigger.md) requer que a subscrição utilize uma chave do sistema quando chama o ponto final do gatilho. Funções Duradouras também usam teclas de sistema para chamar [APIs de extensão de tarefa durável](durable/durable-functions-http-api.md). 

O âmbito das teclas do sistema é determinado pela extensão, mas geralmente aplica-se a toda a aplicação de função. As teclas do sistema só podem ser criadas através de extensões específicas, e não é possível definir explicitamente os seus valores. Tal como outras teclas, pode gerar um novo valor para a chave a partir do portal ou utilizando as APIs chave.

#### <a name="keys-comparison"></a>Comparação de chaves

A tabela a seguir compara as utilizações para vários tipos de chaves de acesso:

| Ação                                        | Âmbito                    | Chaves válidas         |
|-----------------------------------------------|--------------------------|--------------------|
| Executar uma função                            | Função específica        | Função           |
| Executar uma função                            | Qualquer função             | Função ou hospedeiro   |
| Chame um ponto final de administração                        | Aplicação de funções             | Hospedeiro (apenas mestre) |
| Ligue para APIs de extensão de tarefa durável              | App de função<sup>1</sup> | Sistema<sup>2</sup> |
| Ligue para um Webhook específico de extensão (interno) | App de função<sup>1</sup> | sistema<sup>2</sup> |

<sup>1</sup> Âmbito determinado pela extensão.  
<sup>2</sup> Nomes específicos definidos por extensão.

Para saber mais sobre as teclas de acesso, consulte o [artigo de vindca do gatilho HTTP](functions-bindings-http-webhook-trigger.md#obtaining-keys).


#### <a name="secret-repositories"></a>Repositórios secretos

Por predefinição, as chaves são armazenadas num recipiente de armazenamento Blob na conta fornecida pela `AzureWebJobsStorage` definição. Pode utilizar definições de aplicação específicas para anular este comportamento e armazenar chaves num local diferente.

|Localização  |Definição | Valor | Descrição  |
|---------|---------|---------|---------|
|Conta de armazenamento diferente     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | Armazena chaves no armazenamento blob de uma segunda conta de armazenamento, com base no URL SAS fornecido. As chaves são encriptadas antes de serem armazenadas utilizando um segredo exclusivo da sua aplicação de função. |
|Sistema de ficheiros   | `AzureWebJobsSecretStorageType`   |  `files`       | As chaves são persistiu no sistema de ficheiros, encriptadas antes do armazenamento usando um segredo exclusivo da sua aplicação de função. |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | O cofre deve ter uma política de acesso correspondente à identidade gerida atribuída pelo sistema do recurso de alojamento. A política de acesso deve conceder à identidade as seguintes permissões secretas: `Get` `Set` , , e `List` `Delete` . <br/>Ao correr localmente, a identidade do desenvolvedor é utilizada e as definições devem estar no [local.settings.jsficheiro](functions-run-local.md#local-settings-file). | 
|Segredos de Kubernetes  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (opcional) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Suportado apenas durante a execução do tempo de execução das Funções em Kubernetes. Quando `AzureWebJobsKubernetesSecretName` não está definido, o repositório é considerado apenas para ler. Neste caso, os valores devem ser gerados antes da implantação. As Ferramentas Principais das Funções Azure geram os valores automaticamente ao implementar em Kubernetes.|

### <a name="authenticationauthorization"></a>Autenticação/autorização

Embora as teclas de função possam fornecer alguma mitigação para acesso indesejado, a única forma de garantir verdadeiramente os pontos finais da sua função é implementando a autenticação positiva dos clientes que acedem às suas funções. Em seguida, pode tomar decisões de autorização com base na identidade.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Permissões

Como em qualquer aplicação ou serviço, o objetivo é executar a sua app de função com as mais baixas permissões possíveis. 

#### <a name="user-management-permissions"></a>Permissões de gestão de utilizadores

As funções suportam o controlo de acesso baseado em [funções incorporada (Azure RBAC)](../role-based-access-control/overview.md). As funções azure suportadas por Funções são [Contribuinte,](../role-based-access-control/built-in-roles.md#contributor) [Proprietário](../role-based-access-control/built-in-roles.md#owner)e [Leitor.](../role-based-access-control/built-in-roles.md#owner) 

As permissões são eficazes ao nível da aplicação de função. A função colaboradora é necessária para executar a maioria das tarefas de nível de aplicação de funções. Apenas a função Proprietário pode eliminar uma aplicação de função. 

#### <a name="organize-functions-by-privilege"></a>Organizar funções por privilégio 

As cadeias de ligação e outras credenciais armazenadas nas definições de aplicação conferem a todas as funções da aplicação de função o mesmo conjunto de permissões no recurso associado. Considere minimizar o número de funções com acesso a credenciais específicas, movendo funções que não usam essas credenciais para uma aplicação de função separada. Pode sempre utilizar técnicas como [acorrentação de funções](/learn/modules/chain-azure-functions-data-using-bindings/) para passar dados entre funções em diferentes aplicações de funções.  

#### <a name="managed-identities"></a>Identidades geridas

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Para obter mais informações, consulte [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure.](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)

#### <a name="restrict-cors-access"></a>Restringir o acesso ao CORS

[A partilha de recursos de origem cruzada (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) é uma forma de permitir que as aplicações web que executam noutro domínio façam pedidos aos seus pontos finais de gatilho HTTP. O Serviço de Aplicações fornece suporte incorporado para entregar os cabeçalhos CORS necessários em pedidos HTTP. As regras CORS são definidas a nível de aplicação de função.  

Embora seja tentador usar um wildcard que permite a todos os sites aceder ao seu ponto final. Mas isto derrota o propósito do CORS, que é ajudar a prevenir ataques de scripts trans-sites. Em vez disso, adicione uma entrada CORS separada para o domínio de cada aplicação web que deve aceder ao seu ponto final. 

### <a name="managing-secrets"></a>Gerir segredos 

Para poder conectar-se aos vários serviços e recursos necessários para executar o seu código, as aplicações de função precisam de ser capazes de aceder a segredos, como cadeias de ligação e chaves de serviço. Esta secção descreve como armazenar segredos exigidos pelas suas funções.

Nunca guarde segredos no seu código de função. 

#### <a name="application-settings"></a>Definições da aplicação

Por predefinição, armazena cadeias de ligação e segredos utilizados pela sua aplicação de função e encadernações como configurações de aplicação. Isto disponibiliza estas credenciais tanto ao seu código de função como às várias encadernações utilizadas pela função. O nome de definição de aplicação (chave) é usado para recuperar o valor real, que é o segredo. 

Por exemplo, cada aplicação de função requer uma conta de armazenamento associada, que é usada pelo tempo de execução. Por predefinição, a ligação a esta conta de armazenamento é armazenada numa definição de aplicação denominada `AzureWebJobsStorage` .

As definições de aplicações e as cadeias de ligação são armazenadas encriptadas no Azure. Só são desencriptadas antes de serem injetadas na memória de processo da sua aplicação quando a aplicação começa. As chaves de encriptação são rodadas regularmente. Se preferir gerir o armazenamento seguro dos seus segredos, a definição da aplicação deve, em vez disso, ser referência ao Cofre da Chave Azure. 

Também pode encriptar as definições por padrão no local.settings.jsno ficheiro ao desenvolver funções no seu computador local. Para saber mais, consulte a `IsEncrypted` propriedade no ficheiro de [configurações locais.](functions-run-local.md#local-settings-file)  

#### <a name="key-vault-references"></a>Referências do Cofre-Chave

Embora as definições de aplicação sejam suficientes para a maioria das funções, pode querer partilhar os mesmos segredos em vários serviços. Neste caso, o armazenamento redundante de segredos resulta em mais vulnerabilidades potenciais. Uma abordagem mais segura é um serviço central de armazenamento secreto e usar referências a este serviço em vez dos próprios segredos.      

[Azure Key Vault](../key-vault/general/overview.md) é um serviço que fornece gestão de segredos centralizados, com controlo total sobre políticas de acesso e histórico de auditoria. Pode utilizar uma referência key Vault no lugar de uma cadeia de ligação ou chave nas definições da sua aplicação. Para saber mais, consulte [referências do Key Vault para o Serviço de Aplicações e Funções Azure](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="set-usage-quotas"></a>Definir quotas de utilização

Considere definir uma quota de utilização em funções em execução num plano de consumo. Quando estabelece um limite diário de GB-sec sobre a execução total de funções na sua aplicação de funções, a execução é interrompida quando o limite é atingido. Isto pode potencialmente ajudar a mitigar o código malicioso que executa as suas funções. Para aprender a estimar o consumo para as suas funções, consulte [os custos do plano de consumo](functions-consumption-costs.md)estimado. 

### <a name="data-validation"></a>Validação de dados

Os gatilhos e encadernações utilizados pelas suas funções não fornecem qualquer validação adicional de dados. O seu código deve validar todos os dados recebidos de um gatilho ou de uma ligação de entrada. Se um serviço a montante estiver comprometido, não pretende que entradas não trituradas fluam através das suas funções. Por exemplo, se a sua função armazenar dados de uma fila de Armazenamento Azure numa base de dados relacional, deve validar os dados e parametrizar os seus comandos para evitar ataques de injeção DE SQL. 

Não assuma que os dados que entram na sua função já foram validados ou higienizados. Também é uma boa ideia verificar se os dados que estão a ser escritos para as ligações de saída são válidos. 

### <a name="handle-errors"></a>Processar erros

Embora pareça básico, é importante escrever um bom erro nas suas funções. Erros não manipulados bolha para o hospedeiro e são tratados pelo tempo de execução. Diferentes encadernações lidam com o processamento de erros de forma diferente. Para saber mais, consulte [o manuseamento de erros do Azure Functions](functions-bindings-error-pages.md).

### <a name="disable-remote-debugging"></a>Desativar a depuração remota

Certifique-se de que a depuração remota está desativada, exceto quando estiver a desativar ativamente as suas funções. Pode desativar a depuração remota no separador **Definições Gerais** da sua aplicação de função **Configuração** no portal. 

### <a name="restrict-cors-access"></a>Restringir o acesso ao CORS

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Não use wildcards na sua lista de origens permitidas. Em vez disso, enuqueia os domínios específicos a partir dos quais espera receber pedidos.

### <a name="store-data-encrypted"></a>Armazenar dados encriptados

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Implementação segura

As funções Azure que fazem uma integração facilitam a publicação do código de projeto de função local ao Azure. É importante entender como funciona a implantação ao considerar a segurança para uma topologia de funções Azure.   

### <a name="deployment-credentials"></a>Credenciais de implementação

As implementações do Serviço de Aplicações requerem um conjunto de credenciais de implementação. Estas credenciais de implementação são usadas para garantir a implementação da aplicação da sua função. As credenciais de implementação são geridas pela plataforma Do Serviço de Aplicações e são encriptadas em repouso. 

Existem dois tipos de credenciais de implantação:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

Neste momento, o Key Vault não é suportado para credenciais de implantação. Para saber mais sobre a gestão de credenciais de implementação, consulte as credenciais de [implementação Configure para o Azure App Service](../app-service/deploy-configure-credentials.md).

### <a name="disable-ftp"></a>Desativar o FTP

Por predefinição, cada aplicação de função tem um ponto final FTP ativado. O ponto final FTP é acedido com recurso a credenciais de implantação. 

FTP não é recomendado para a implementação do seu código de função. As implementações ftp são manuais e exigem que sincronize os gatilhos. Para saber mais, consulte a [implementação ftp](functions-deployment-technologies.md#ftp). 

Quando não está a planear usar FTP, deve desativá-lo no portal. Se optar por utilizar FTP, deverá [impor FTPS](../app-service/deploy-ftp.md#enforce-ftps).

### <a name="secure-the-scm-endpoint"></a>Fixe o ponto final scm

Cada aplicação de função tem um ponto final de serviço correspondente `scm` que é utilizado pelo serviço Advanced Tools (Kudu) para implementações e outras extensões do site do Serviço de [Aplicações](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions). O ponto final scm para uma aplicação de função é sempre um URL no formulário `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` . Quando utilizar o isolamento da rede para proteger as suas funções, também deve ter em conta este ponto final. 

Ao ter um ponto final separado do scm, é possível controlar implementações e outras funcionalidades avançadas de ferramentas para aplicações de função que estejam isoladas ou a funcionar numa rede virtual. O ponto final scm suporta a autenticação básica (utilizando credenciais de implantação) e o único sinal com as credenciais do portal Azure. Para saber mais, consulte [o Accessing the Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

### <a name="continuous-security-validation"></a>Validação contínua da segurança

Uma vez que a segurança deve ser considerada um passo em dia no processo de desenvolvimento, faz sentido também implementar validações de segurança num ambiente de implantação contínua. Isto às vezes é chamado DevSecOps. Utilizando Azure DevOps para o seu pipeline de implantação, vamos integrar a validação no processo de implementação. Para obter mais informações, consulte [Saiba como adicionar validação contínua de segurança ao seu pipeline CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Segurança da rede

Restringir o acesso à rede à sua aplicação de função permite-lhe controlar quem pode aceder aos pontos finais das suas funções. As funções aproveitam a infraestrutura do Serviço de Aplicações para permitir que as suas funções acedam a recursos sem utilizar endereços via internet ou para restringir o acesso à Internet a um ponto final de função. Para saber mais sobre estas opções de networking, consulte [as opções de networking do Azure Functions](functions-networking-options.md).

### <a name="set-access-restrictions"></a>Definir restrições de acesso

As restrições de acesso permitem-lhe definir listas de regras de permitir/negar para controlar o tráfego na sua aplicação. As regras são avaliadas por ordem prioritária. Se não houver regras definidas, então a sua aplicação aceitará o tráfego de qualquer endereço. Para saber mais, consulte as [Restrições de Acesso ao Serviço de Aplicações Azure #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>O acesso a sites privados

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>Implemente a sua app de função isoladamente

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Use um serviço de gateway

Os serviços gateway, tais como [Azure Application Gateway](../application-gateway/overview.md) e [Azure Front Door](../frontdoor/front-door-overview.md) permitem configurar uma Firewall de Aplicação Web (WAF). As regras da WAF são usadas para monitorizar ou bloquear ataques detetados, que fornecem uma camada extra de proteção para as suas funções. Para configurar um WAF, a sua aplicação de função precisa de estar a funcionar num ASE ou a utilizar pontos de final privados (pré-visualização). Para saber mais, consulte [utilização de pontos finais privados.](../app-service/networking/private-endpoint.md)    

## <a name="next-steps"></a>Passos seguintes

+ [Linha de Base de Segurança Azure para Funções Azure](security-baseline.md)
+ [Diagnóstico de funções Azure](functions-diagnostics.md)