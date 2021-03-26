---
title: Integração e entrega contínuas no Azure Data Factory
description: Aprenda a usar a integração contínua e a entrega para mover os oleodutos data factory de um ambiente (desenvolvimento, teste, produção) para outro.
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 24aa3bc455bf716c074526d707594bd3b4170619
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564182"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integração e entrega contínuas no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Descrição Geral

A integração contínua é a prática de testar cada alteração feita na sua base de código automaticamente e o mais cedo possível. A entrega contínua segue os testes que ocorrem durante a integração contínua e emite as alterações para um sistema de teste ou de produção.

No Azure Data Factory, a integração contínua e entrega contínua (CI/CD) significa mover pipelines do Data Factory de um ambiente (desenvolvimento, teste, produção) para outro. A Azure Data Factory utiliza [modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para armazenar a configuração das suas várias entidades ADF (pipelines, conjuntos de dados, fluxos de dados, e assim por diante). Existem dois métodos sugeridos para promover uma fábrica de dados para outro ambiente:

-    Implantação automatizada utilizando a integração da Data Factory com [a Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)
-    Carre faça o upload manual de um modelo de Gestor de Recursos utilizando a integração UX da Data Factory com o Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo de vida CI/CD

Abaixo está uma amostra geral do ciclo de vida CI/CD numa fábrica de dados Azure que está configurada com Azure Repos Git. Para obter mais informações sobre como configurar um repositório de Git, consulte [o controlo de origem na Azure Data Factory](source-control.md).

1.  Uma fábrica de dados de desenvolvimento é criada e configurada com Azure Repos Git. Todos os desenvolvedores devem ter permissão para autoria de recursos da Data Factory, como oleodutos e conjuntos de dados.

1.  Um desenvolvedor [cria um ramo de recurso](source-control.md#creating-feature-branches) para fazer uma mudança. Depuram o seu oleoduto com as suas mudanças mais recentes. Para obter mais informações sobre como depurar um gasoduto, consulte [o desenvolvimento iterativo e a depuração com a Azure Data Factory.](iterative-development-debugging.md)

1.  Depois de um desenvolvedor estar satisfeito com as suas alterações, eles criam um pedido de puxar da sua filial de recurso para o ramo principal ou de colaboração para obter as suas alterações revistas pelos pares.

1.  Após a aprovação de um pedido de puxão e as alterações são fundidas no ramo principal, as alterações são publicadas na fábrica de desenvolvimento.

1.  Quando a equipa estiver pronta para implementar as alterações numa fábrica de teste ou UAT (User Accept Testing), a equipa vai para o seu lançamento de Azure Pipelines e implementa a versão desejada da fábrica de desenvolvimento para a UAT. Esta implementação ocorre como parte de uma tarefa de Azure Pipelines e utiliza parâmetros de modelo do Gestor de Recursos para aplicar a configuração apropriada.

1.  Depois de verificadas as alterações na fábrica de ensaios, implantar-se na fábrica de produção utilizando a próxima tarefa do lançamento dos gasodutos.

> [!NOTE]
> Apenas a fábrica de desenvolvimento está associada a um repositório de git. As fábricas de teste e produção não devem ter um repositório de git associado a eles e só devem ser atualizados através de um oleoduto Azure DevOps ou através de um modelo de Gestão de Recursos.

A imagem abaixo destaca os diferentes passos deste ciclo de vida.

![Diagrama de integração contínua com gasodutos Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizar a integração contínua utilizando os lançamentos dos Gasodutos Azure

Segue-se um guia para a criação de um lançamento da Azure Pipelines que automatiza a implantação de uma fábrica de dados em vários ambientes.

### <a name="requirements"></a>Requisitos

-   Uma subscrição Azure ligada ao Servidor da Fundação Visual Studio Team ou ao Azure Repos que utiliza o ponto final do [serviço Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Uma fábrica de dados configurada com integração do Azure Repos Git.

-   Um [cofre-chave Azure](https://azure.microsoft.com/services/key-vault/) que contém os segredos para cada ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurar um lançamento de Azure Pipelines

1.  Em [Azure DevOps,](https://dev.azure.com/)abra o projeto que está configurado com a sua fábrica de dados.

1.  No lado esquerdo da página, selecione **Pipelines** e, em seguida, selecione **Versões**.

    ![Selecione Pipelines, Lançamentos](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione **Novo oleoduto**, ou, se tiver os oleodutos existentes, selecione **Novo** e, em seguida, Novo oleoduto **de lançamento**.

1.  Selecione o modelo **de trabalho vazio.**

    ![Selecione trabalho vazio](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Na **caixa de nomes** de palco, insira o nome do seu ambiente.

1.  **Selecione Adicionar artefacto** e, em seguida, selecione o repositório de git configurado com a sua fábrica de dados de desenvolvimento. Selecione o ramo de [publicação](source-control.md#configure-publishing-settings) do repositório para o **ramo Predefinido**. Por defeito, esta sucursal de publicação é `adf_publish` . Para a **versão Predefinitiva**, selecione **mais recente do ramo predefinido**.

    ![Adicionar um artefacto](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicione uma tarefa de implementação do Gestor de Recursos Azure:

    a.  Na vista do palco, **selecione Ver as tarefas do palco**.

    ![Vista de palco](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Criar uma nova tarefa. Procure a **implementação do modelo ARM** e, em seguida, selecione **Adicionar**.

    c.  Na tarefa De implementação, selecione a subscrição, o grupo de recursos e a localização para a fábrica de dados-alvo. Fornecer credenciais, se necessário.

    d.  Na lista **de Ação,** selecione **Criar ou atualizar o grupo de recursos**.

    e.  Selecione o botão elipse **(...**) ao lado da caixa **Modelo.** Navegue pelo modelo Azure Resource Manager que é gerado no seu ramo de publicação do repositório de git configurado. Procure o ficheiro `ARMTemplateForFactory.json` na pasta do ramo <FactoryName> adf_publish.

    f.  Selecione **...** ao lado da caixa **de parâmetros do modelo** para escolher o ficheiro de parâmetros. Procure o ficheiro `ARMTemplateParametersForFactory.json` na pasta do ramo <FactoryName> adf_publish.

    exemplo,  Selecione **...** ao lado da caixa de **parâmetros do modelo de substituição** e introduza os valores de parâmetros desejados para a fábrica de dados-alvo. Para credenciais que vêm de Azure Key Vault, insira o nome do segredo entre as aspas duplas. Por exemplo, se o nome do segredo for cred1, insira **"$(cred1)"** por este valor.

    h. Selecione **Incremental** para o **modo de implementação**.

    > [!WARNING]
    > No modo de implementação completo, os recursos que existem no grupo de recursos mas que não estão especificados no novo modelo de Gestor de Recursos serão **eliminados**. Para mais informações, consulte os [modos de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/deployment-modes.md)

    ![Implantação de prod de fábrica de dados](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Guarde o oleoduto de libertação.

1. Para desencadear uma versão, selecione **Criar verção**. Para automatizar a criação de lançamentos, veja [os gatilhos de lançamento do Azure DevOps](/azure/devops/pipelines/release/triggers)

   ![Selecione Criar ver lançamento](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Nos cenários ci/CD, o tipo de execução de integração (IR) em diferentes ambientes deve ser o mesmo. Por exemplo, se tiver um IR auto-hospedado no ambiente de desenvolvimento, o mesmo IR também deve ser do tipo auto-hospedado em outros ambientes, como o teste e a produção. Da mesma forma, se estiver a partilhar tempos de integração em várias fases, tem de configurar os tempos de integração como auto-hospedados em todos os ambientes, tais como desenvolvimento, teste e produção.

### <a name="get-secrets-from-azure-key-vault"></a>Obtenha segredos do Azure Key Vault

Se tiver segredos para passar num modelo de Gestor de Recursos Azure, recomendamos que utilize o Cofre da Chave Azure com a versão Azure Pipelines.

Há duas maneiras de lidar com segredos:

1.  Adicione os segredos ao arquivo de parâmetros. Para obter mais informações, consulte [use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](../azure-resource-manager/templates/key-vault-parameter.md).

    Crie uma cópia do ficheiro de parâmetros que é enviado para a filial de publicação. Desa esta medida os valores dos parâmetros que pretende obter do Cofre de Chaves utilizando este formato:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Quando se utiliza este método, o segredo é retirado automaticamente do cofre da chave.

    O ficheiro de parâmetros também tem de estar na sucursal de publicação.

1. Adicione uma [tarefa de Azure Key Vault](/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da tarefa de implementação do Gestor de Recursos Azure descrita na secção anterior:

    1.  No separador **Tarefas,** crie uma nova tarefa. Procure **por Azure Key Vault** e adicione-o.

    1.  Na tarefa Key Vault, selecione a subscrição na qual criou o cofre de chaves. Forneça credenciais se necessário e, em seguida, selecione o cofre da chave.

    ![Adicione uma tarefa de Cofre de Chaves](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões ao agente da Azure Pipelines

A tarefa Azure Key Vault pode falhar com um erro de Acesso Negado se as permissões corretas não estiverem definidas. Descarregue os registos para o lançamento e localize o ficheiro .ps1 que contém o comando para dar permissões ao agente Azure Pipelines. Pode executar o comando diretamente. Ou pode copiar o ID principal do ficheiro e adicionar a política de acesso manualmente no portal Azure. `Get` e `List` são as permissões mínimas necessárias.

### <a name="updating-active-triggers"></a>Atualizar gatilhos ativos

A implementação pode falhar se tentar atualizar os gatilhos ativos. Para atualizar os gatilhos ativos, é necessário detê-los manualmente e reiniciá-los após a colocação. Pode fazê-lo utilizando uma tarefa Azure PowerShell:

1.  No separador **Tarefas** do lançamento, adicione uma tarefa **Azure PowerShell.** Escolha a versão de tarefa 4.*. 

1.  Selecione a subscrição em que a sua fábrica está.

1.  Selecione **o Caminho do Ficheiro do Script** como o tipo de script. Isto requer que guarde o seu script PowerShell no seu repositório. O seguinte script PowerShell pode ser usado para parar os gatilhos:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Pode completar passos semelhantes (com a `Start-AzDataFactoryV2Trigger` função) para reiniciar os gatilhos após a colocação.

A equipa de fábrica de dados forneceu uma [amostra de script pré e pós-implantação](#script) localizado na parte inferior deste artigo. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Promover manualmente um modelo de Gestor de Recursos para cada ambiente

1. Na lista **de modelos ARM,** selecione **Modelo DE BRAÇO de Exportação** para exportar o modelo de Gestor de Recursos para a sua fábrica de dados no ambiente de desenvolvimento.

   ![Exporte um modelo de gestor de recursos](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Nas suas fábricas de dados de teste e produção, selecione **O Modelo DE BRAÇO de Importação.** Esta ação leva-o ao portal Azure, onde pode importar o modelo exportado. Selecione **Construa o seu próprio modelo no editor** para abrir o editor de modelo do Gestor de Recursos.

   ![Construa o seu próprio modelo](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selecione **o ficheiro De carga** e, em seguida, selecione o modelo gerado de Gestor de Recursos. Esta é a **arm_template.jsno** ficheiro localizado no ficheiro .zip exportado no passo 1.

   ![Modelo de edição](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Na secção de definições, introduza os valores de configuração, como credenciais de serviço ligadas. Quando terminar, selecione **Comprar** para implementar o modelo de Gestor de Recursos.

   ![Secção de definições](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Utilizar parâmetros personalizados com o modelo do Resource Manager

Se a sua fábrica de desenvolvimento tiver um repositório de git associado, pode anular os parâmetros padrão do modelo do Gestor de Recursos do modelo do Gestor de Recursos gerado pela publicação ou exportação do modelo. É possível que queira anular a configuração de parâmetros padrão do Gestor de Recursos nestes cenários:

* Utiliza CI/CD automatizado e pretende alterar algumas propriedades durante a implementação do Gestor de Recursos, mas as propriedades não são parametrizadas por padrão.
* A sua fábrica é tão grande que o modelo de Gestor de Recursos predefinido é inválido porque tem mais do que os parâmetros máximos permitidos (256).

    Para lidar com o limite do parâmetro 256 personalizado, existem três opções:    
  
    * Utilize o ficheiro de parâmetros personalizados e remova propriedades que não necessitam de parametrização, ou seja, propriedades que podem manter um valor padrão e, portanto, diminuir a contagem de parâmetros.
    * A lógica do refactor no fluxo de dados para reduzir parâmetros, por exemplo, os parâmetros do pipeline todos têm o mesmo valor, pode apenas usar parâmetros globais em vez disso.
    * Divida uma fábrica de dados em vários fluxos de dados.

Para anular a configuração de parâmetros predefinido do Gestor de Recursos, vá ao hub **'Gerir'** e selecione o **modelo ARM** na secção "Controlo de origem". Na secção de **configuração do parâmetro ARM,** clique em **Editar** o ícone em "Editar a configuração do parâmetro" para abrir o editor de código de configuração do modelo do Gestor de Recursos.

![Gerir parâmetros personalizados](media/author-management-hub/management-hub-custom-parameters.png)

> [!NOTE]
> **A configuração do parâmetro ARM** só está ativada no modo GIT. Atualmente está desativado no modo "modo vivo" ou no modo "Data Factory".

A criação de uma configuração de parâmetros personalizado do Gestor de Recursos cria um ficheiro nomeado **arm-template-parameters-definition.jsna** pasta raiz do seu ramo git. Tens de usar esse nome exato do ficheiro.

![Arquivo de parâmetros personalizados](media/continuous-integration-deployment/custom-parameters.png)

Ao publicar a partir do ramo de colaboração, a Data Factory lerá este ficheiro e utilizará a sua configuração para gerar quais propriedades são parametrizadas. Se não for encontrado nenhum ficheiro, o modelo predefinido é utilizado.

Ao exportar um modelo de Gestor de Recursos, a Data Factory lê este ficheiro a partir de qualquer ramo em que esteja a trabalhar, e não no ramo de colaboração. Pode criar ou editar o ficheiro a partir de uma sucursal privada, onde pode testar as suas alterações selecionando o **Modelo ARM de Exportação** na UI. Em seguida, pode fundir o ficheiro no ramo de colaboração.

> [!NOTE]
> Uma configuração de parâmetros personalizado do Gestor de Recursos não altera o limite de parâmetro do modelo ARM de 256. Permite-lhe escolher e diminuir o número de propriedades parametrizadas.

### <a name="custom-parameter-syntax"></a>Sintaxe de parâmetro personalizado

Seguem-se algumas diretrizes a seguir quando criar o ficheiro de parâmetros personalizados, **arm-template-parameters-definition.jsem .** O ficheiro é composto por uma secção para cada tipo de entidade: gatilho, pipeline, serviço ligado, conjunto de dados, tempo de integração e fluxo de dados.

* Insira o caminho da propriedade sob o tipo de entidade relevante.
* Definir um nome de propriedade indica `*` que pretende parametrizar todas as propriedades por baixo (apenas até ao primeiro nível, não recursivamente). Também pode fornecer exceções a esta configuração.
* Definir o valor de uma propriedade como uma corda indica que deseja parametrizar a propriedade. Utilize o formato `<action>:<name>:<stype>`.
   *  `<action>` pode ser um destes caracteres:
      * `=` significa manter o valor atual como o valor padrão para o parâmetro.
      * `-` significa não manter o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos do Azure Key Vault para cordas de ligação ou chaves.
   * `<name>` é o nome do parâmetro. Se estiver em branco, tem o nome da propriedade. Se o valor começar com um `-` personagem, o nome é encurtado. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria encurtado para `AzureStorage1_connectionString` .
   * `<stype>` é o tipo de parâmetro. Se `<stype>` estiver em branco, o tipo predefinido é `string` . Valores apoiados: `string` , , , , e `securestring` `int` `bool` `object` `secureobject` `array` .
* Especificar uma matriz no ficheiro de definição indica que a propriedade correspondente no modelo é uma matriz. A Data Factory itera através de todos os objetos da matriz utilizando a definição especificada no objeto de execução de integração da matriz. O segundo objeto, uma corda, torna-se o nome da propriedade, que é usada como o nome para o parâmetro para cada iteração.
* Uma definição não pode ser específica para uma instância de recursos. Qualquer definição aplica-se a todos os recursos desse tipo.
* Por padrão, todas as cordas seguras, como os segredos do Cofre de Chaves, e cordas seguras, como cordas de ligação, chaves e fichas, são parametrizadas.
 
### <a name="sample-parameterization-template"></a>Modelo de parametrização da amostra

Aqui está um exemplo de como uma configuração de parâmetro de gestor de recursos pode parecer:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Aqui está uma explicação de como o modelo anterior é construído, dividido por tipo de recurso.

#### <a name="pipelines"></a>Pipelines
    
* Qualquer propriedade no caminho `activities/typeProperties/waitTimeInSeconds` é parametrizada. Qualquer atividade num oleoduto que tenha uma propriedade de nível de código denominada `waitTimeInSeconds` (por exemplo, a `Wait` atividade) é parametrizada como um número, com um nome padrão. Mas não terá um valor predefinido no modelo de Gestor de Recursos. Será uma entrada obrigatória durante a implementação do Gestor de Recursos.
* Da mesma forma, uma propriedade chamada `headers` (por exemplo, numa `Web` atividade) é parametrizada com o tipo `object` (JObject). Tem um valor padrão, que é o mesmo valor da fábrica de origem.

#### <a name="integrationruntimes"></a>IntegraçãoRuntimes

* Todas as propriedades sob o caminho `typeProperties` são parametrizadas com os respetivos valores predefinidos. Por exemplo, existem duas propriedades sob `IntegrationRuntimes` propriedades do tipo: `computeProperties` e `ssisProperties` . Ambos os tipos de propriedade são criados com os respetivos valores e tipos predefinidos (Objeto).

#### <a name="triggers"></a>Acionadores

* Em `typeProperties` baixo, duas propriedades são parametrizadas. O primeiro é `maxConcurrency` , que é especificado para ter um valor padrão e é de tipo `string` . Tem o nome de parâmetro `<entityName>_properties_typeProperties_maxConcurrency` padrão.
* A `recurrence` propriedade também é parametrizada. Sob ele, todas as propriedades a esse nível são especificadas para serem parametrizadas como cordas, com valores padrão e nomes de parâmetros. Uma exceção é a `interval` propriedade, que é parametrizada como `int` tipo. O nome do parâmetro é sufixado com `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Da mesma forma, a `freq` propriedade é uma corda e é parametrizada como uma corda. No entanto, a `freq` propriedade é parametrizada sem um valor padrão. O nome é encurtado e sufixado. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Os serviços ligados são únicos. Como os serviços e conjuntos de dados ligados têm uma vasta gama de tipos, pode fornecer personalização específica do tipo. Neste exemplo, para todos os serviços de tipo `AzureDataLakeStore` ligados, será aplicado um modelo específico. Para todos os outros `*` (via), será aplicado um modelo diferente.
* A `connectionString` propriedade será parametrizada como um `securestring` valor. Não terá um valor padrão. Terá um nome de parâmetro encurtado que é sufixado com `connectionString` .
* A propriedade `secretAccessKey` passa a ser um `AzureKeyVaultSecret` (por exemplo, num serviço ligado ao Amazon S3). É automaticamente parametrizado como um cofre de chave Azure e recolhido do cofre de chaves configurado. Também pode parametrizar o cofre da chave em si.

#### <a name="datasets"></a>Conjuntos de dados

* Embora a personalização específica do tipo esteja disponível para conjuntos de dados, pode fornecer configuração sem ter explicitamente uma \* configuração de nível. No exemplo anterior, todas as propriedades do conjunto de `typeProperties` dados são parametrizadas.

> [!NOTE]
> **Alertas e matrizes azure**  se configurados para um gasoduto não são atualmente suportados como parâmetros para as implementações da ARM. Para reaplicar os alertas e matrizes em novos ambientes, siga a [Monitorização, Alertas e Matrizes da Data Factory.](./monitor-using-azure-monitor.md#data-factory-metrics)
> 

### <a name="default-parameterization-template"></a>Modelo de parametrização padrão

Abaixo está o modelo de parametrização padrão atual. Se precisar de adicionar apenas alguns parâmetros, editar este modelo diretamente pode ser uma boa ideia porque não perderá a estrutura de parametrização existente.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Exemplo: parametrização de um ID interativo do cluster interativo Azure Databricks

O exemplo a seguir mostra como adicionar um único valor ao modelo de parametrização padrão. Queremos apenas adicionar um ID de cluster interativo Azure Databricks existente para um serviço ligado a Databricks ao ficheiro de parâmetros. Note que este ficheiro é o mesmo que o ficheiro anterior, exceto a adição de `existingClusterId` no campo de propriedades de `Microsoft.DataFactory/factories/linkedServices` .

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Modelos de gestor de recursos ligados

Se configurar CI/CD para as suas fábricas de dados, poderá exceder os limites do modelo do Azure Resource Manager à medida que a sua fábrica cresce. Por exemplo, um limite é o número máximo de recursos num modelo de Gestor de Recursos. Para acomodar grandes fábricas enquanto gera o modelo completo de Gestor de Recursos para uma fábrica, a Data Factory gera agora modelos de Gestor de Recursos ligados. Com esta funcionalidade, toda a carga útil da fábrica é dividida em vários ficheiros para que não fique limitado pelos limites.

Se configurar o Git, os modelos ligados são gerados e guardados ao lado dos modelos completos do Gestor de Recursos no ramo adf_publish numa nova pasta chamada linkedTemplates:

![Pasta de modelos de gestor de recursos ligados](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos de Gestor de Recursos ligados geralmente consistem de um modelo mestre e um conjunto de modelos de crianças que estão ligados ao mestre. O modelo dos pais é chamado ArmTemplate_master.js, e os modelos de criança são nomeados com o padrão ArmTemplate_0.js, ArmTemplate_1.js, e assim por diante. 

Para utilizar modelos ligados em vez do modelo completo de Gestor de Recursos, atualize a sua tarefa CI/CD para apontar para ArmTemplate_master.jsem vez de ArmTemplateForFactory.js(o modelo completo do Gestor de Recursos). O Gestor de Recursos também requer que faça o upload dos modelos ligados para uma conta de armazenamento para que o Azure possa aceder aos mesmos durante a implementação. Para obter mais informações, consulte [a implementação de modelos de Gestor de Recursos ligados com VSTS](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts).

Lembre-se de adicionar os scripts data Factory no seu pipeline CI/CD antes e depois da tarefa de implantação.

Se não tiver Git configurado, pode aceder aos modelos ligados através do **modelo DE ARM de exportação** na lista de **modelos ARM.**

## <a name="hotfix-production-environment"></a>Ambiente de produção de hotfix

Se colocar uma fábrica para produção e perceber que há um bug que precisa de ser corrigido imediatamente, mas não pode implantar o atual ramo de colaboração, talvez precise de implantar um hotfix. Esta abordagem é conhecida como engenharia de correção rápida ou QFE.

1.    Em Azure DevOps, vá para o lançamento que foi implantado para a produção. Encontre o último compromisso que foi enviado.

2.    A partir da mensagem de compromisso, obtenha a identificação comprometedora do ramo de colaboração.

3.    Crie um novo ramo de hotfix a partir desse compromisso.

4.    Vá ao Azure Data Factory UX e mude para o ramo hotfix.

5.    Utilizando o Azure Data Factory UX, corrija o bug. Teste as suas alterações.

6.    Depois de verificada a correção, selecione **o modelo DE BRAÇO de Exportação** para obter o modelo de Gestor de Recursos de hotfix.

7.    Verifique manualmente esta construção no ramo adf_publish.

8.    Se configurar o seu pipeline de desbloqueio para ativar automaticamente com base em adf_publish check-ins, uma nova versão começará automaticamente. Caso contrário, faça fila manualmente para um desbloqueio.

9.    Desloque o desbloqueio de hotfix para as fábricas de teste e de produção. Esta versão contém a carga útil de produção anterior mais a correção que fez no passo 5.

10.   Adicione as alterações do hotfix ao ramo de desenvolvimento para que os lançamentos posteriores não incluam o mesmo bug.

Veja o vídeo abaixo de um tutorial de vídeo aprofundado sobre como corrigir quentemente os seus ambientes. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="exposure-control-and-feature-flags"></a>Controlo de exposição e bandeiras de características

Ao trabalhar numa equipa, há casos em que pode fundir mudanças, mas não quer que sejam geridas em ambientes elevados como o PROD e o QA. Para lidar com este cenário, a equipa da ADF recomenda [o conceito de DevOps de utilização de bandeiras de características.](/azure/devops/migrate/phase-features-with-feature-flags) Em ADF, você pode combinar [parâmetros globais](author-global-parameters.md) e a [atividade se condicionar](control-flow-if-condition-activity.md) conjuntos de lógica com base nestas bandeiras ambientais.

Para aprender a configurar uma bandeira de recurso, veja o tutorial de vídeo abaixo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="best-practices-for-cicd"></a>Melhores práticas para CI/CD

Se estiver a utilizar a integração do Git com a sua fábrica de dados e tiver um pipeline ci/CD que move as suas mudanças de desenvolvimento para teste e depois para a produção, recomendamos estas boas práticas:

-   **Integração de Git**. Configure apenas a sua fábrica de dados de desenvolvimento com a integração de Git. As alterações ao teste e à produção são implementadas através de CI/CD e não necessitam de integração do Git.

-   **Script pré e pós-implantação.** Antes do passo de implementação do Gestor de Recursos em CI/CD, é necessário completar certas tarefas, como parar e reiniciar os gatilhos e realizar a limpeza. Recomendamos que utilize scripts PowerShell antes e depois da tarefa de implantação. Para obter mais informações, consulte [os gatilhos ativos da Atualização](#updating-active-triggers). A equipa de fábrica de dados [forneceu um script](#script) para usar localizado na parte inferior desta página.

-   **Integração tempos de execução e partilha.** Os tempos de integração não mudam frequentemente e são semelhantes em todas as fases do seu CI/CD. Assim, a Data Factory espera que tenha o mesmo nome e tipo de tempo de integração em todas as fases do CI/CD. Se quiser partilhar os tempos de integração em todas as fases, considere usar uma fábrica ternuy apenas para conter os tempos de integração partilhada. Você pode usar esta fábrica partilhada em todos os seus ambientes como um tipo de tempo de execução de integração ligado.

-   **Implementação de ponto final privado gerido**. Se um ponto final privado já existir numa fábrica e tentar implantar um modelo ARM que contenha um ponto final privado com o mesmo nome mas com propriedades modificadas, a implementação falhará. Por outras palavras, pode implantar com sucesso um ponto final privado desde que tenha as mesmas propriedades que já existe na fábrica. Se qualquer propriedade for diferente entre ambientes, pode sobrepor-se a essa propriedade, parametrizando essa propriedade e fornecendo o respetivo valor durante a implantação.

-   **Cofre de Chaves**. Quando utiliza serviços ligados cujas informações de ligação são armazenadas no Cofre da Chave Azure, é aconselhável manter cofres-chave separados para diferentes ambientes. Também pode configurar níveis de permissão separados para cada cofre de chaves. Por exemplo, pode não querer que os membros da sua equipa tenham permissões para segredos de produção. Se seguir esta abordagem, recomendamos que mantenha os mesmos nomes secretos em todas as fases. Se mantiver os mesmos nomes secretos, não precisa de parametrizar cada cadeia de ligação em ambientes CI/CD porque a única coisa que muda é o nome do cofre chave, que é um parâmetro separado.

-  **Nomeação de recursos** Devido às restrições do modelo ARM, podem surgir problemas de implantação se os seus recursos contiverem espaços no nome. A equipa da Azure Data Factory recomenda a utilização de caracteres '_' ou '-' em vez de espaços para recursos. Por exemplo, "Pipeline_1" seria um nome preferível sobre o "Pipeline 1".

## <a name="unsupported-features"></a>Funcionalidades não suportadas

- Por design, a Data Factory não permite a recolha de cerejas de compromissos ou a publicação seletiva de recursos. As publicações incluirão todas as alterações feitas na fábrica de dados.

    - As entidades da fábrica de dados dependem umas das outras. Por exemplo, os gatilhos dependem de oleodutos e os gasodutos dependem de conjuntos de dados e outros oleodutos. A publicação seletiva de um subconjunto de recursos pode levar a comportamentos e erros inesperados.
    - Em raras ocasiões, quando precisar de publicação seletiva, considere usar um hotfix. Para mais informações, consulte [o ambiente de produção da Hotfix.](#hotfix-production-environment)

- A equipa da Azure Data Factory não recomenda a atribuição de controlos Azure RBAC a entidades individuais (oleodutos, conjuntos de dados, etc.) numa fábrica de dados. Por exemplo, se um programador tiver acesso a um pipeline ou um conjunto de dados, deverá conseguir aceder a todos os pipelines ou conjuntos de dados na fábrica de dados. Se sente que precisa implementar muitas funções do Azure dentro de uma fábrica de dados, veja a implementação de uma segunda fábrica de dados.

-   Não pode publicar de agências privadas.

-   Não pode atualmente acolher projetos no Bitbucket.

-   Não se pode exportar e importar alertas e matrizes como parâmetros. 

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> Guião pré e pós-implantação da amostra

O seguinte script de amostra pode ser usado para parar os gatilhos antes de ser implantado e reiniciá-los depois. O script também inclui código para eliminar recursos que foram removidos. Guarde o script num repositório de git Azure DevOps e refira-o através de uma tarefa Azure PowerShell utilizando a versão 4.*.

Ao executar um script pré-implantação, terá de especificar uma variação dos seguintes parâmetros no campo Argumentos do **Script.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Ao executar um script pós-implantação, terá de especificar uma variação dos seguintes parâmetros no campo Argumentos do **Script.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Tarefa do Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Aqui está o script que pode ser usado para pré e pós-implantação. Contabiliza recursos e referências de recursos eliminados.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```