---
title: Problemas de CI-CD, Azure DevOps e GitHub em ADF
description: Utilize diferentes métodos para resolver problemas de CI-CD em ADF.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.openlocfilehash: 4be015b1a8ba4b6fc6ea3acc74318f9a8b298e8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418101"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Problemas de CI-CD, Azure DevOps e GitHub em ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para a implementação contínua de Integration-Continuous (CI-CD), problemas de Azure DevOps e GitHub na Azure Data Factory.

Se tiver dúvidas ou problemas na utilização de técnicas de controlo de fontes ou de devOps, aqui estão alguns artigos que poderá achar úteis:

- Consulte o [Controlo de Origem em ADF](source-control.md) para saber como é praticado o controlo de origem na ADF. 
- Consulte o  [CI-CD em ADF](continuous-integration-deployment.md) para saber mais sobre como o DevOps CI-CD é praticado em ADF.
 
## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Connect to Git repositório falhou devido a inquilino diferente

#### <a name="issue"></a>Problema
    
Por vezes encontra problemas de autenticação como o estado HTTP 401. Especialmente quando se tem vários inquilinos com conta de hóspedes, as coisas podem tornar-se mais complicadas.

#### <a name="cause"></a>Causa

O que observamos é que o símbolo foi obtido do inquilino original, mas a ADF está no inquilino convidado e tentando usar o símbolo para visitar DevOps em inquilino convidado. Este não é o comportamento esperado.

#### <a name="recommendation"></a>Recomendação

Em vez disso, devias usar o símbolo emitido pelo inquilino convidado. Por exemplo, você tem que atribuir o mesmo Diretório Azure Ative para ser seu inquilino convidado, bem como seus DevOps, para que possa definir corretamente o comportamento simbólico e usar o inquilino correto.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Os parâmetros do modelo no ficheiro de parâmetros não são válidos

#### <a name="issue"></a>Problema

Se eliminarmos um gatilho na sucursal de Dev, que já está disponível no ramo de Teste ou Produção com **a mesma** configuração (como frequência e intervalo), então a implementação do gasoduto de libertação terá sucesso e o gatilho correspondente será eliminado nos respetivos ambientes. Mas se tiver uma configuração **diferente** (como frequência e intervalo) para o gatilho em ambientes de Teste/Produção e se eliminar o mesmo gatilho em Dev, então a implementação falha com um erro.

#### <a name="cause"></a>Causa

O gasoduto CI/CD falha com o seguinte erro:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Recomendação

O erro ocorre porque muitas vezes apagamos um gatilho, que é parametrizado, portanto, os parâmetros não estarão disponíveis no modelo ARM (porque o gatilho já não existe). Uma vez que o parâmetro já não está no modelo ARM, temos de atualizar os parâmetros ultrapassados no pipeline DevOps. Caso contrário, sempre que os parâmetros do modelo ARM mudarem, devem atualizar os parâmetros ultrapassados no gasoduto DevOps (na tarefa de implantação).

### <a name="updating-property-type-is-not-supported"></a>A atualização do tipo de propriedade não é suportada

#### <a name="issue"></a>Problema

O gasoduto de libertação ci/CD falha com o seguinte erro:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Causa

Isto deve-se a um tempo de integração com o mesmo nome na fábrica-alvo, mas com um tipo diferente. Integração O tempo de execução tem de ser do mesmo tipo quando se implementa.

#### <a name="recommendation"></a>Recomendação

- Consulte abaixo as melhores práticas para CI/CD:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Os tempos de integração não mudam frequentemente e são semelhantes em todas as fases do seu CI/CD, por isso a Data Factory espera que tenha o mesmo nome e tipo de tempo de integração em todas as fases do CI/CD. Se o nome e os tipos & propriedades forem diferentes, certifique-se de que correspondem à configuração de tempo de execução de integração de origem e alvo e, em seguida, implemente o pipeline de desbloqueio.
- Se quiser partilhar os tempos de integração em todas as fases, considere usar uma fábrica ternuy apenas para conter os tempos de integração partilhada. Você pode usar esta fábrica partilhada em todos os seus ambientes como um tipo de tempo de execução de integração ligado.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>A criação ou atualização de documentos falhou devido a referência inválida

#### <a name="issue"></a>Problema

Ao tentar publicar alterações numa Fábrica de Dados, obtém-se a seguinte mensagem de erro:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>Sintoma

Desprenderam a configuração do Git e voltaram a contê-la com a bandeira "Importar recursos", que define a Fábrica de Dados como "sincronizada". Isto significa que não há alterações na publicação.

#### <a name="resolution"></a>Resolução

Desprete a configuração do Git e volte a instalá-la, e certifique-se de não verificar a caixa de verificação "importar recursos existentes".

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Data Factory move-se falhando de um grupo de recursos para outro

#### <a name="issue"></a>Problema

Não é possível mover a Data Factory de um Grupo de Recursos para outro, falhando com o seguinte erro:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Resolução

Tem de eliminar as IRS SSIS-IR e Shared para permitir o funcionamento do movimento. Se não quiser eliminar os tempos de integração, então a melhor maneira é seguir o documento de cópia e clone para fazer a cópia e depois de o fazer, eliminar a antiga Data Factory.

###  <a name="unable-to-export-and-import-arm-template"></a>Não pode exportar e importar modelo ARM

#### <a name="issue"></a>Problema

Não é possível exportar e importar o modelo ARM. Nenhum erro estava no portal, no entanto, no rastreio do navegador, você poderia ver o seguinte erro:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Causa

Criou um papel de cliente como utilizador e não teve a permissão necessária. Quando a fábrica é carregada na UI, verifica-se uma série de valores de controlo de exposição para a fábrica. Neste caso, a função de acesso do utilizador não tem permissão para aceder a *consultasFeaturesValue* API. Para aceder a esta API, a funcionalidade de parâmetros globais é desligada. A trajetória do código de exportação DA ARM baseia-se, em parte, na característica dos parâmetros globais.

#### <a name="resolution"></a>Resolução

Para resolver o problema, tem de adicionar a seguinte permissão à sua função: *Microsoft.DataFactory/fábricas/consultasFeaturesValue/action*. Esta permissão deve ser incluída por padrão na função "Data Factory Contributor".

###  <a name="automatic-publishing-for-cicd-without-clicking-publish-button"></a>Publicação automática para CI/CD sem clicar no botão Publicar  

#### <a name="issue"></a>Problema

A publicação manual com clique de botão no portal ADF não permite o funcionamento automático do CI/CD.

#### <a name="cause"></a>Causa

Até recentemente, a única forma de publicar o pipeline ADF para implementações era usar o botão do Portal ADF. Agora, pode tornar o processo automático. 

#### <a name="resolution"></a>Resolução

O processo CI/CD foi melhorado. A funcionalidade **de publicação automatizada** retira, valida e exporta todas as funcionalidades do modelo Azure Resource Manager (ARM) do ADF UX. Torna a lógica consumível através de um pacote npm disponível ao [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) público. Isto permite-lhe desencadear programáticamente estas ações em vez de ter de ir ao UI ADF e fazer um clique de botão. Isto proporciona aos seus oleodutos CI/CD uma **verdadeira** experiência de integração contínua. Siga as [melhorias da publicação de ADF CI/CD](./continuous-integration-deployment-improvements.md) para mais detalhes. 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>Não pode publicar por causa do limite de modelo de 4mb ARM  

#### <a name="issue"></a>Problema

Não é possível implementar porque atingiu o limite do Gestor de Recursos Azure de 4mb de tamanho total do modelo. Precisa de uma solução para implementar depois de ultrapassar o limite. 

#### <a name="cause"></a>Causa

O Gestor de Recursos Azure restringe o tamanho do modelo a ser de 4mb. Limite o tamanho do seu modelo a 4 MB e cada ficheiro de parâmetro a 64 KB. O limite de 4-MB aplica-se ao estado final do modelo depois de ter sido expandido com definições de recursos iterativos e valores para variáveis e parâmetros. Mas ultrapassou o limite. 

#### <a name="resolution"></a>Resolução

Para obter soluções pequenas a médias, um único modelo é mais fácil de compreender e manter. Pode ver todos os recursos e valores num único ficheiro. Para cenários avançados, os modelos associados permitem-lhe dividir a solução em componentes direcionados. Siga as melhores práticas na [utilização de modelos ligados e aninhados.](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell)

### <a name="cannot-connect-to-git-enterprise-cloud"></a>Não é possível ligar-se à Nuvem Empresarial GIT 

##### <a name="issue"></a>Problema

Não é possível ligar-se à GIT Enterprise Cloud por questões de permissão. Pode ver erro como **422 - Entidade Inprocessável.**

#### <a name="cause"></a>Causa

* Está a usar a Git Enterprise no servidor prem. 
* Não configuraste o Oauth para a ADF. 
* A sua URL está mal configurada.

##### <a name="resolution"></a>Resolução

Concedes acesso à ADF no início. Em seguida, tem de usar o URL correto para ligar à GIT Enterprise. A configuração deve ser definida para a(s) organização(s) do cliente. Por exemplo, a ADF tentará *https://hostname/api/v3/search/repositories?q=user%3 <customer credential> ....* no início e falhará. Então, *https://hostname/api/v3/orgs/ <org> / <repo> tentará...* e terá sucesso. 
 
### <a name="recover-from-a-deleted-data-factory"></a>Recuperar de uma fábrica de dados eliminada

#### <a name="issue"></a>Problema
O cliente eliminou a fábrica de dados ou o grupo de recursos que contém a Data Factory. Ele gostaria de saber como restaurar uma fábrica de dados eliminada.

#### <a name="cause"></a>Causa

Só é possível recuperar a Data Factory se o cliente tiver o controlo de Origem configurado (DevOps ou Git). Isto trará todos os recursos publicados mais recentes e **não** irá restaurar o pipeline inédito, conjunto de dados e serviço ligado.

Se não houver controlo de Origem, a recuperação de uma Fábrica de Dados Eliminada do backend não é possível porque uma vez que o serviço recebe o comando eliminado, a instância é eliminada e não foi armazenada nenhuma cópia de segurança.

#### <a name="resolution"></a>Resolução

Para recuperar a Fábrica de Dados Eliminada que tem Controlo de Origem consulte os passos abaixo:

 * Criar uma nova Fábrica de Dados Azure.

 * Reconfigure git com as mesmas definições, mas certifique-se de importar os recursos existentes da Data Factory para o repositório selecionado, e escolha novo ramo.

 * Crie um pedido de puxar para fundir as alterações ao ramo de colaboração e publicar.

 * Se o cliente tiver um Tempo de Integração Auto-hospedado em ADF eliminado, eles terão de criar uma nova instância em novo ADF, também desinstalar e reinstalar o caso na sua máquina on-prem/VM com a nova chave obtida. Após a instalação do IR estar concluída, o cliente terá de alterar o Serviço Linked para apontar para o novo IR e testar a ligação ou falhará com **a referência inválida** por erro.



## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda na resolução de problemas, experimente os seguintes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack overflow forum para data factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
