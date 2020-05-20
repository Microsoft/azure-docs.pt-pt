---
title: Assegurar funções azure
description: Saiba como tornar o seu código de função em funcionamento em Azure mais seguro de ataques comuns.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 7336974a0f982f2dd2394f16d7f6d520ecb8571d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664418"
---
# <a name="securing-azure-functions"></a>Assegurar funções azure

Em muitos aspetos, o planeamento para desenvolvimento seguro, implementação e funcionamento de funções sem servidorés é praticamente o mesmo que para qualquer aplicação hospedada na web ou na nuvem. [O Azure App Service](/azure/app-service/) fornece a infraestrutura de hospedagem para as suas aplicações de função. Este artigo fornece estratégias de segurança para executar o seu código de função, e como o Serviço de Aplicações pode ajudá-lo a garantir as suas funções. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Para um conjunto de recomendações de segurança que seguem o [Benchmark de Segurança Azure,](/azure/security/benchmarks/overview)consulte a [Linha de Base de Segurança Azure para funções Azure](security-baseline.md).

## <a name="secure-operation"></a>Operação segura 

Esta secção guia-o na configuração e execução da sua aplicação de funções o mais segura mente possível. 

### <a name="security-center"></a>Centro de Segurança

O Security Center integra-se com a sua aplicação de funções no portal. Fornece, gratuitamente, uma avaliação rápida das potenciais vulnerabilidades de segurança relacionadas com a configuração. As aplicações de função que executam um plano dedicado também podem usar as funcionalidades de segurança em tempo real do Security Center, por um custo adicional. Para saber mais, consulte [Protect your Azure App Service web apps e APIs](../security-center/security-center-app-services.md). 

### <a name="log-and-monitor"></a>Log and monitor

Um para detetar ataques é através da atividade de monitorização da atividade e da analítica de registo. As funções integram-se com os Insights de Aplicação para recolher dados de registo, desempenho e erro para a sua aplicação de função. Application Insights deteta automaticamente anomalias de desempenho e inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e a entender como as suas funções são usadas. Para saber mais, consulte as [Funções Monitor Azure](functions-monitoring.md).

As funções também se integram com registos do Monitor Azure para permitir consolidar registos de aplicações de função com eventos do sistema para uma análise mais fácil. Pode utilizar configurações de diagnóstico para configurar a exportação de diários de bordo de registos e métricas da plataforma para as suas funções para o destino à sua escolha, como um espaço de trabalho de Logs Analytics. Para saber mais, consulte a Monitorização das [Funções Azure com registos de monitores Azure](functions-monitor-log-analytics.md). 

Para deteção de ameaças e automatização de resposta ao nível da empresa, transmita os seus registos e eventos para um espaço de trabalho de Logs Analytics. Em seguida, pode ligar o Azure Sentinel a este espaço de trabalho. Para saber mais, veja [o que é Azure Sentinel.](../sentinel/overview.md)  

Para obter mais recomendações de segurança para a observabilidade, consulte a linha de base de [segurança Azure para funções Azure](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>Exigir HTTPS

Por padrão, os clientes podem ligar-se aos pontos finais de função utilizando http ou HTTPS. Deve redirecionar http para HTTPs porque HTTPS utiliza o protocolo SSL/TLS para fornecer uma ligação segura, que é encriptada e autenticada. Para saber como, consulte [Enforce HTTPS](../app-service/configure-ssl-bindings.md#enforce-https).

Quando necessitar de HTTPS, deve também exigir a versão TLS mais recente. Para saber como, ver [ver ver verversões Enforce TLS](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

Para mais informações, consulte [Ligações Seguras (TSL)](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>Teclas de acesso de função

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Chave do sistema 

Extensões específicas podem requerer uma chave gerida pelo sistema para aceder a pontos finais do webhook. As teclas do sistema foram concebidas para pontos finais de função específicos de extensão, chamados por componentes internos. Por exemplo, o gatilho da Grelha de [Eventos](functions-bindings-event-grid-trigger.md) requer que a subscrição utilize uma chave do sistema ao ligar para o ponto final do gatilho. Funções Duráveis também usam teclas do sistema para chamar APIs de extensão de [tarefadurável](durable/durable-functions-http-api.md). 

O âmbito das teclas do sistema é determinado pela extensão, mas geralmente aplica-se a toda a aplicação de função. As chaves do sistema só podem ser criadas por extensões específicas, e não é possível definir explicitamente os seus valores. Tal como outras teclas, pode gerar um novo valor para a chave a partir do portal ou utilizando as APIs chave.

#### <a name="keys-comparison"></a>Comparação de chaves

A tabela seguinte compara as utilizações para vários tipos de teclas de acesso:

| Ação                                        | Âmbito                    | Chaves válidas         |
|-----------------------------------------------|--------------------------|--------------------|
| Executar uma função                            | Função específica        | Função           |
| Executar uma função                            | Qualquer função             | Função ou anfitrião   |
| Chame um ponto final de administração                        | Function app             | Anfitrião (apenas mestre) |
| Chamar APIs de extensão de tarefa sonável              | App de funções<sup>1</sup> | Sistema<sup>2</sup> |
| Ligue para um Webhook específico de extensão (interno) | App de funções<sup>1</sup> | sistema<sup>2</sup> |

<sup>1</sup> Âmbito determinado pela extensão.  
<sup>2</sup> Nomes específicos definidos por extensão.

Para saber mais sobre as teclas de acesso, consulte o artigo de [ligação](functions-bindings-http-webhook-trigger.md#obtaining-keys)http trigger .

### <a name="authenticationauthorization"></a>Autenticação/autorização

Embora as teclas de função possam fornecer alguma mitigação para acesso indesejado, a única maneira de garantir verdadeiramente os pontos finais da sua função é implementando autenticação positiva dos clientes que acedem às suas funções. Pode então tomar decisões de autorização com base na identidade.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Permissões

Como em qualquer aplicação ou serviço, o objetivo é executar a sua app de funções com as mais baixas permissões possíveis. 

#### <a name="user-management-permissions"></a>Permissões de gestão do utilizador

As funções suportam o controlo de acesso baseado em [funções azure (RBAC)](../role-based-access-control/overview.md). As funções RBAC suportadas por Funções são [Contributor,](../role-based-access-control/built-in-roles.md#contributor) [Proprietário](../role-based-access-control/built-in-roles.md#owner)e [Leitor.](../role-based-access-control/built-in-roles.md#owner) 

As permissões são eficazes ao nível da aplicação de funções. A função Contributiva é necessária para executar a maioria das tarefas de nível de aplicação de função. Apenas a função Proprietário pode eliminar uma aplicação de função. 

#### <a name="organize-functions-by-privilege"></a>Organizar funções por privilégio 

As cordas de ligação e outras credenciais armazenadas nas definições de aplicação conferem a todas as funções na aplicação de funções o mesmo conjunto de permissões no recurso associado. Considere minimizar o número de funções com acesso a credenciais específicas movendo funções que não usam essas credenciais para uma aplicação de função separada. Pode sempre utilizar técnicas como a corrente de [funções](/learn/modules/chain-azure-functions-data-using-bindings/) para passar dados entre funções em diferentes aplicações de função.  

#### <a name="managed-identities"></a>Identidades geridas

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Para mais informações, consulte [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### <a name="restrict-cors-access"></a>Restringir o acesso cors

[A partilha de recursos de origem cruzada (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) é uma forma de permitir que as aplicações web que executam noutro domínio façam pedidos para os seus pontos finais de gatilho HTTP. O Serviço de Aplicações fornece suporte incorporado para entregar os cabeçalhos CORS necessários em pedidos HTTP. As regras cors são definidas a nível de aplicação de funções.  

Embora seja tentador usar um wildcard que permite a todos os sites aceder ao seu ponto final. Mas isto derrota o propósito do CORS, que é ajudar a prevenir ataques de guionismo seletivas. Em vez disso, adicione uma entrada CORS separada para o domínio de cada aplicação web que deve aceder ao seu ponto final. 

### <a name="managing-secrets"></a>Gerir segredos 

Para poder ligar-se aos vários serviços e recursos necessários para executar o seu código, as aplicações de função precisam de ser capazes de aceder a segredos, como cordas de ligação e chaves de serviço. Esta secção descreve como armazenar segredos exigidos pelas suas funções.

Nunca guarde segredos no seu código de função. 

#### <a name="application-settings"></a>Definições da aplicação

Por padrão, armazena cordas de ligação e segredos utilizados pela sua aplicação de funções e encadernações como definições de aplicação. Isto disponibiliza estas credenciais tanto para o seu código de função como para as várias encadernações utilizadas pela função. O nome de definição de aplicação (chave) é usado para recuperar o valor real, que é o segredo. 

Por exemplo, cada aplicação de função requer uma conta de armazenamento associada, que é usada pelo tempo de execução. Por predefinição, a ligação a esta conta de armazenamento é armazenada numa definição de aplicação denominada `AzureWebJobsStorage` .

As definições de aplicativos e as cordas de ligação são armazenadas encriptadas no Azure. Só são desencriptados antes de serem injetados na memória de processo da sua aplicação quando a aplicação começar. As chaves de encriptação são rodadas regularmente. Se preferir, em vez disso, gerir o armazenamento seguro dos seus segredos, a definição de aplicações deve ser, em vez disso, referências ao Azure Key Vault. 

#### <a name="key-vault-references"></a>Referências do cofre chave

Embora as definições de aplicação sejam suficientes para a maioria das funções, pode querer partilhar os mesmos segredos em vários serviços. Neste caso, o armazenamento redundante de segredos resulta em vulnerabilidades mais potenciais. Uma abordagem mais segura é para um serviço de armazenamento secreto central e usar referências a este serviço em vez dos próprios segredos.      

[Azure Key Vault](../key-vault/general/overview.md) é um serviço que fornece gestão centralizada de segredos, com total controlo sobre políticas de acesso e histórico de auditoria. Pode utilizar uma referência do Cofre chave no lugar de uma cadeia de ligação ou chave nas definições da sua aplicação. Para saber mais, consulte Utilize referências de cofre de chaves para serviço de [aplicações e funções azure](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="set-usage-quotas"></a>Definir quotas de utilização

Considere definir uma quota de utilização em funções em execução num plano de consumo. Quando estabelece um limite diário de GB-seg na execução total das funções na sua aplicação de função, a execução é interrompida quando o limite é atingido. Isto pode potencialmente ajudar a atenuar o código malicioso que executa as suas funções. Para aprender a estimar o consumo das suas funções, consulte estimar [os custos do plano de consumo.](functions-consumption-costs.md) 

### <a name="data-validation"></a>Validação de dados

Os gatilhos e encadernações utilizados pelas suas funções não fornecem qualquer validação adicional de dados. O seu código deve validar quaisquer dados recebidos de um gatilho ou de uma ligação de entrada. Se um serviço a montante estiver comprometido, não pretende que as inputs não validadas fluam através das suas funções. Por exemplo, se a sua função armazenar dados de uma fila de Armazenamento Azure numa base de dados relacional, deve validar os dados e parâmetros dos seus comandos para evitar ataques de injeção SQL. 

Não assuma que os dados que entram na sua função já foram validados ou desinsertados. Também é uma boa ideia verificar se os dados que estão a ser escritos para encadernações de saída são válidos. 

### <a name="handle-errors"></a>Processar erros

Embora pareça básico, é importante escrever um bom manuseamento de erros nas suas funções. Erros não manipulados bolha para o hospedeiro e são tratados pelo tempo de execução. Diferentes encadernações lidam com o processamento de erros de forma diferente. Para saber mais, consulte o manuseamento de erros das [Funções Azure.](functions-bindings-error-pages.md)

### <a name="disable-remote-debugging"></a>Desativar a depuração remota

Certifique-se de que a depuração remota está desativada, exceto quando estiver a depurar ativamente as suas funções. Pode desativar a depuração remota no separador **Definições Gerais** da sua aplicação de função **Configuração** no portal. 

### <a name="restrict-cors-access"></a>Restringir o acesso cors

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Não use wildcards na sua lista de origens permitidas. Em vez disso, enumera os domínios específicos a partir dos quais espera receber pedidos.

### <a name="store-data-encrypted"></a>Armazenar dados encriptados

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Implementação segura

A Ferramenta Azure Tools, que está a fazer uma integração, facilita a publicação do código do projeto de função local para o Azure. É importante entender como funciona a implantação quando se considera a segurança de uma topologia de funções Azure.   

### <a name="deployment-credentials"></a>Credenciais de implementação

As implementações do Serviço de Aplicações requerem um conjunto de credenciais de implementação. Estas credenciais de implementação são usadas para proteger as implementações da sua aplicação de função. As credenciais de implementação são geridas pela plataforma do Serviço de Aplicações e são encriptadas em repouso. 

Existem dois tipos de credenciais de implantação:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

Neste momento, o Cofre Chave não é suportado para credenciais de implantação. Para saber mais sobre a gestão das credenciais de implementação, consulte as credenciais de [implementação da Configuração para](../app-service/deploy-configure-credentials.md)o Serviço de Aplicações Azure .

### <a name="disable-ftp"></a>Desativar ftp

Por predefinição, cada aplicação de função tem um ponto final FTP ativado. O ponto final do FTP é acedido utilizando credenciais de implantação. 

O FTP não é recomendado para a implementação do seu código de função. As implementações ftp são manuais, e exigem que sincronize os gatilhos. Para saber mais, consulte a [implantação ftp](functions-deployment-technologies.md#ftp). 

Quando não está a planear usar FTP, deve desativá-lo no portal. Se optar por utilizar ftp, deve [impor ftps](../app-service/deploy-ftp.md#enforce-ftps).

### <a name="secure-the-scm-endpoint"></a>Fixe o ponto final do scm

Cada aplicação de função tem um ponto final de serviço correspondente `scm` que usado pelo serviço Advanced Tools (Kudu) para implementações e [outras extensões](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)do site do Serviço de Aplicações. O ponto final do scm para uma aplicação de função é sempre um URL na forma `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` . Quando utiliza o isolamento da rede para assegurar as suas funções, também deve ter em conta este ponto final. 

Ao ter um ponto final separado, pode controlar implementações e outras funcionalidades de ferramentas avançadas para aplicação de funções isolada ou em funcionamento numa rede virtual. O ponto final do scm suporta a autenticação básica (utilizando credenciais de implantação) e um único sinal com as credenciais do portal Azure. Para saber mais, consulte [Aceder ao serviço Kudu.](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) 

### <a name="continuous-security-validation"></a>Validação contínua de segurança

Uma vez que a segurança tem de ser considerada um passo no processo de desenvolvimento, faz sentido também implementar validações de segurança num ambiente de implantação contínua. Isto às vezes chama-se DevSecOps. Utilizando o Azure DevOps para o seu pipeline de implantação, integra a validação no processo de implementação. Para mais informações, consulte [Saiba como adicionar validação contínua de segurança ao seu pipeline CI/CD](/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Segurança da rede

Restringir o acesso à rede à sua aplicação de função permite controlar quem pode aceder aos pontos finais das suas funções. As funções aproveitam a infraestrutura do Serviço de Aplicações para permitir que as suas funções acedam a recursos sem utilizar endereços de saída da Internet ou para restringir o acesso à Internet a um ponto final de função. Para saber mais sobre estas opções de networking, consulte as opções de [networking do Azure Functions.](functions-networking-options.md)

### <a name="set-access-restrictions"></a>Definir restrições de acesso

As restrições de acesso permitem definir listas de regras de permitir/negar para controlar o tráfego na sua aplicação. As regras são avaliadas por ordem prioritária. Se não houver regras definidas, então a sua aplicação aceitará o tráfego a partir de qualquer endereço. Para saber mais, consulte as restrições de acesso ao serviço de [aplicações do Azure #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>O acesso a sites privados

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>Implemente a sua aplicação de função isoladamente

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Use um serviço de gateway

Os serviços gateway, como [o Azure Application Gateway](../application-gateway/overview.md) e o [Azure Front Door](../frontdoor/front-door-overview.md) permitem-lhe criar uma Firewall de Aplicação Web (WAF). As regras waf são usadas para monitorizar ou bloquear ataques detetados, que fornecem uma camada extra de proteção para as suas funções. Para configurar uma WAF, a sua aplicação de funções precisa de ser funcionada numa ASE ou utilizando pontos finais privados (pré-visualização). Para saber mais, consulte [Usar pontos finais privados](../app-service/networking/private-endpoint.md).    

## <a name="next-steps"></a>Passos seguintes

+ [Linha de base de segurança azure para funções azure](security-baseline.md)
+ [Diagnósticos de Funções Azure](functions-diagnostics.md)
        