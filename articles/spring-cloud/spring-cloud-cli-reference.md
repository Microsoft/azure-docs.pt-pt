---
title: AZ Spring Cloud
description: Gerenciar a nuvem Spring do Azure usando o CLI do Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: jeconnoc
ms.openlocfilehash: a1f82d4efa7756b44ca5ed9859aa872c1f55b565
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607829"
---
# <a name="az-spring-cloud"></a>AZ Spring-Cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Gerenciar a nuvem Spring do Azure usando o CLI do Azure

>[!Note]
> O Azure Spring Cloud está atualmente em versão prévia.  Esses comandos podem ser alterados ou removidos em uma versão futura.

| AZ Spring-Cloud |  |
|------|------:|
| [AZ Spring-Cloud Create](#az-spring-cloud-create) | Crie uma instância do Azure Spring Cloud. |
| [AZ Spring-Cloud Delete](#az-spring-cloud-delete) | Exclua uma instância do Azure Spring Cloud. |
| [lista AZ Spring-Cloud](#az-spring-cloud-list) | Liste todas as instâncias do Azure Spring Cloud no grupo de recursos fornecido, caso contrário, liste as IDs de assinatura. |
| [AZ Spring-Cloud show](#az-spring-cloud-show) | Mostre os detalhes de uma nuvem do Azure Spring. |

| AZ Spring-Cloud app | Comandos para gerenciar aplicativos na nuvem do Azure Spring.  |
| ---- | ----: |
| [AZ Spring-Cloud app Create](#az-spring-cloud-app-create) | Crie um novo aplicativo com uma implantação padrão na nuvem do Azure Spring. |
| [AZ Spring-Cloud app Delete](#az-spring-cloud-app-delete) | Exclua um aplicativo na nuvem Spring do Azure. |
| [AZ Spring-Cloud app Deploy](#az-spring-cloud-app-deploy) | Implante do código-fonte ou de um binário pré-compilado para um aplicativo e atualize as configurações relacionadas. |
| [lista de aplicativos AZ Spring-Cloud](#az-spring-cloud-app-list) | Liste todos os aplicativos na nuvem do Azure Spring. |
| [AZ Spring-Cloud app Restart](#az-spring-cloud-app-restart) | Reinicie as instâncias do aplicativo usando padrões de implantação de produção. |
| [dimensão de aplicativo AZ Spring-Cloud](#az-spring-cloud-app-scale) | Dimensione manualmente um aplicativo ou suas implantações. |
| [AZ Spring-Cloud app Set – implantação](#az-spring-cloud-app-set-deployment) | Defina a implantação de produção de um aplicativo. |
| [AZ Spring-Cloud App Show](#az-spring-cloud-app-show) | Mostre os detalhes de um aplicativo na nuvem do Azure Spring. |
| [AZ Spring-Cloud App Show-Deploy-log](#az-spring-cloud-app-show-deploy-log) | Mostre os logs de compilação para a implantação mais recente da origem. O padrão é a implantação de produção. |
| [aplicativo AZ Spring-Cloud app Start](#az-spring-cloud-app-start) | Inicie as instâncias do aplicativo usando padrões de implantação de produção. |
| [AZ Spring-Cloud app Stop](#az-spring-cloud-app-stop) | Interrompa as instâncias do aplicativo usando padrões de implantação de produção. |
| [AZ Spring-Cloud app Update](#az-spring-cloud-app-update) | Atualizar a configuração do aplicativo especificado. |

| AZ Spring-Cloud app BIND | Comandos para gerenciar associações com os serviços de dados do Azure.  O aplicativo deve ser reiniciado para que essas configurações entrem em vigor. |
| --- | ---: |
| [lista de associação de aplicativo AZ Spring-Cloud](#az-spring-cloud-app-binding-list) | Listar todas as associações de serviço em um aplicativo. |
| [AZ Spring-Cloud app BIND remove](#az-spring-cloud-app-binding-remove) | Remova uma associação de serviço do aplicativo. |
| [AZ Spring-Cloud app BIND show](#az-spring-cloud-app-binding-show) | Mostrar os detalhes de uma associação de serviço. |
| [AZ Spring-Cloud app BIND Cosmos Add](#az-spring-cloud-app-binding-cosmos-add) | Associar um CosmosDB do Azure ao aplicativo. |
| [atualização de Cosmos AZ Spring-Cloud app BIND](#az-spring-cloud-app-binding-cosmos-update) | Atualizar uma associação do serviço CosmosDB do Azure. |
| [AZ Spring-Cloud app BIND MySQL Add](#az-spring-cloud-app-binding-mysql-add) | Associe um banco de dados do Azure para MySQL com o aplicativo. |
| [atualização do MySQL AZ Spring-Cloud app BIND](#az-spring-cloud-app-binding-mysql-update) | Atualizar uma associação de serviço de banco de dados do Azure para MySQL. |
| [AZ Spring-Cloud app BIND Redis Add](#az-spring-cloud-app-binding-redis-add) | Associe um cache do Azure para Redis com o aplicativo. |
| [atualização de Redis AZ Spring-Cloud app BIND](#az-spring-cloud-app-binding-redis-update) | Atualizar um cache do Azure para associação de serviço Redis. |

| AZ Spring – implantação de aplicativo em nuvem | Comandos para gerenciar o ciclo de vida da implantação de um aplicativo no Azure Spring Cloud. |
| --- | ---: |
| [AZ Spring-Cloud app Deployment criar](#az-spring-cloud-app-deployment-create) | Crie uma implantação de preparo para o aplicativo. |
| [AZ Spring-Cloud app Deployment excluir](#az-spring-cloud-app-deployment-delete) | Exclua uma implantação do aplicativo. |
| [lista de implantação de aplicativo AZ Spring-Cloud](#az-spring-cloud-app-deployment-list) | Listar todas as implantações em um aplicativo. |
| [AZ Spring-Cloud app Deployment show](#az-spring-cloud-app-deployment-show) | Mostrar detalhes da implantação. |

| AZ Spring-Cloud CONFIG-Server | Comandos para gerenciar o servidor de configuração do Azure Spring Cloud. |
| --- | ---: |
| [AZ Spring-Cloud config-servidor limpar](#az-spring-cloud-config-server-clear) | Apague todas as configurações no servidor de configuração. |
| [AZ Spring-Cloud CONFIG-Server Set](#az-spring-cloud-config-server-set) | Defina o servidor de configuração de um arquivo YAML. |
| [AZ Spring-Cloud config-servidor show](#az-spring-cloud-config-server-show) | Mostrar configuração do servidor de configuração. |
| [AZ Spring-Cloud CONFIG Server git Set](#az-spring-cloud-config-server-git-set) | Defina as propriedades do git para o servidor de configuração.  Os valores anteriores serão substituídos. |
| [AZ Spring-Cloud CONFIG Server git Repositório adicionar](#az-spring-cloud-config-server-git-repo-add) | Adicione uma nova configuração de repositório git ao servidor de configuração. |
| [AZ Spring-Cloud CONFIG Server lista de repositórios git](#az-spring-cloud-config-server-git-repo-list) | Listar todas as configurações do repositório Git para o servidor de configuração. |
| [AZ Spring-Cloud CONFIG Server git remove](#az-spring-cloud-config-server-git-repo-remove) | Remova o repositório git especificado do servidor de configuração. |

| AZ Spring-Cloud Test-Endpoint | Comandos para gerenciar o teste de ponto de extremidade no Azure Spring Cloud |
| --- | ---: |
| [AZ Spring-Cloud Test-Endpoint Disable](#az-spring-cloud-test-endpoint-disable) | Desabilitar ponto de extremidade de teste. |
| [AZ Spring-Cloud Test-Endpoint Enable](#az-spring-cloud-test-endpoint-enable) | Habilitar ponto de extremidade de teste. |
| [AZ Spring-Cloud Test-Endpoint List](#az-spring-cloud-test-endpoint-list) | Listar chaves de ponto de extremidade de teste. |
| [AZ Spring-Cloud Test-Endpoint Renovate-Key](#az-spring-cloud-test-endpoint-renew-key) | Regenerar uma chave de ponto de extremidade de teste. |

## <a name="az-spring-cloud-create"></a>AZ Spring-Cloud Create

Crie um novo aplicativo com uma implantação padrão na nuvem do Azure Spring.

```cli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome para esta instância do Azure Spring Cloud. |
| --Resource-Group-g | Especifica o grupo de recursos para este aplicativo.  Configurar o grupo padrão usando `az configure --defaults group=<name>` |

| Parâmetros opcionais | |
| --- | ---: |
| --local-l | Especifica o local do servidor para este aplicativo.  Localizar locais válidos usando `az account list-locations` |
| --sem espera | Não para concluir operações de longa execução.

### <a name="examples"></a>Exemplos

Criar uma nova nuvem do Azure Spring na Westus

```cli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>AZ Spring-Cloud Delete

Exclua uma instância do Azure Spring Cloud.

```cli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome da instância do Azure Spring Cloud a ser excluída. |
| --Resource-Group-g | Nome do grupo de recursos ao qual a nuvem do Azure Spring pertence. |

| Parâmetros opcionais | |
| --- | ---: |
| -sem espera | Não aguarde até que as operações de longa execução sejam concluídas. |

### <a name="example"></a>Exemplo

Exclua uma instância do Azure Spring Cloud chamada ' MyService ' de ' MyResource be'.

```cli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>lista AZ Spring-Cloud

Lista todas as instâncias do Azure Spring Cloud associadas ao grupo de recursos fornecido. Se nenhum grupo de recursos for especificado, liste as IDs de assinatura.

```cli
az spring-cloud list --resource-group -g
```

| Parâmetros necessários | |
| --- | ---: |
| --Resource-Group-g | Nome do grupo de recursos. |

## <a name="az-spring-cloud-show"></a>AZ Spring-Cloud show

Mostre os detalhes da instância especificada do Azure Spring Cloud.

```cli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome da instância do Azure Spring Cloud. |
| --Resource-Group-g | Nome do grupo de recursos ao qual a instância do Azure Spring Cloud pertence.

## <a name="az-spring-cloud-app-create"></a>AZ Spring-Cloud app Create

Crie um novo aplicativo em uma nuvem do Azure Spring.

```cli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --CPU | Número de núcleos virtuais por instância.  Padrão: 1. |
| --Habilitar-armazenamento persistente | Valor booliano.  Se for true, montará um disco de 50 GB com o caminho padrão. |
| --contagem de instâncias | Número de instâncias.  Padrão: 1. |
| --é-público | Valor booliano.  Se for true, atribuirá um domínio público. |
| --memória | Número de GB de memória por instância.  Padrão: 1. |

### <a name="examples"></a>Exemplos

Crie um aplicativo com a configuração padrão.

```cli
az spring-cloud app create -n MyApp -s MyService
```

Crie um aplicativo publicamente acessível com 3 instâncias.  Cada instância tem 3 GB de memória e 2 núcleos de CPU.

```cli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>AZ Spring-Cloud app Delete

Exclui um aplicativo na nuvem Spring do Azure.

```cli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>AZ Spring-Cloud app Deploy

Implante um aplicativo na Spring Cloud do Azure a partir do código-fonte ou de um binário pré-compilado e atualize as configurações relacionadas.

```cli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --CPU | Número de núcleos de CPI virtuais por instância. |
| --implantação-d | Nome de uma implantação de aplicativo existente.  O padrão será a implantação de produção se não for especificado. |
| --env | Variáveis de ambiente separadas por espaços no formato ' Key [= value] '. |
| --contagem de instâncias | Número de instâncias. |
| --jar-caminho | Se fornecido, implante o JAR do caminho fornecido. Caso contrário, implante a pasta atual como um tar. |
| --JVM-opções | Uma cadeia de caracteres que contém opções de JVM.  Use ' = ' em vez de ' ' para evitar erros de análise de Shell. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memória | Número de GB de memória por instância. |
| --sem espera | Não aguarde até que as operações de longa execução sejam concluídas. |
| --versão de tempo de execução | Versão de tempo de execução do idioma usado no aplicativo.  Valores permitidos: `Java_11`, `Java_8`. |
| --módulo de destino | Módulo filho a ser implantado.  Necessário quando vários pacotes JAR são criados a partir do código-fonte. |
| --versão | Versão da implantação.  Inalterado se não estiver definido. |

### <a name="examples"></a>Exemplos

Implante o código-fonte em um aplicativo. Isso irá empacotar o diretório atual, criar um binário usando o serviço de compilação dinâmica e, em seguida, implantar no aplicativo.

```cli
az spring-cloud app deploy -n MyApp -s MyService
```

Implante um jar pré-criado em um aplicativo usando opções de JVM e variáveis de ambiente.

```cli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Implante o código-fonte em uma implantação específica de um aplicativo.

```cli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>lista de aplicativos AZ Spring-Cloud

Lista todos os aplicativos na instância do Azure Spring Cloud.

```cli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Parâmetros necessários | |
| --- | ---: |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>AZ Spring-Cloud app Restart

Reinicie as instâncias do aplicativo.  O padrão é a implantação de produção.

```cli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação-d | Nome da implantação existente do aplicativo.  O padrão será a implantação de produção se não for especificado. |
| --sem espera | Não aguarde até que as operações de longa execução sejam concluídas. |

## <a name="az-spring-cloud-app-scale"></a>dimensão de aplicativo AZ Spring-Cloud

Dimensione manualmente um aplicativo ou suas implantações.

```cli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --CPU | Número de núcleos de CPU virtual por instância de aplicativo. |
| --implantação-d | Nome da implantação existente do aplicativo.  O padrão será a implantação de produção se não for especificado. |
| --contagem de instâncias | Número de instâncias deste aplicativo. |
| --memória | Número de GB de memória por instância de aplicativo. |
| --sem espera | Não aguarde a conclusão das operações de execução longa. |

### <a name="examples"></a>Exemplos

Escalar verticalmente um aplicativo para 4 núcleos de CPU e 8 GB de memória por instância.

```cli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Escale horizontalmente uma implantação do aplicativo em 5 instâncias.

```cli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>AZ Spring-Cloud app Set – implantação

Defina as opções de configuração para a implantação de produção do aplicativo.

```cli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Parâmetros necessários | |
| --- | ---: |
| --implantação-d | Nome de uma implantação existente do aplicativo. |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --sem espera | Não aguarde a conclusão das operações de execução longa. |

### <a name="examples"></a>Exemplos

Troque uma implantação de preparo do aplicativo para produção.

```cli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>AZ Spring-Cloud App Show

Mostre os detalhes de um aplicativo na nuvem do Azure Spring.

```cli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>AZ Spring-Cloud App Show-Deploy-log

Mostrar o log de compilação da última implantação do código-fonte.  O padrão é o ambiente de produção.

```cli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação-d | Nome de uma implantação existente do aplicativo.  O padrão é o ambiente de produção. |

## <a name="az-spring-cloud-app-start"></a>aplicativo AZ Spring-Cloud app Start

Inicia instâncias do aplicativo.  O padrão é o ambiente de produção.

```cli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação-d | Nome de uma implantação existente do aplicativo.  O padrão é o ambiente de produção. |
| --sem espera | Não aguarde a conclusão das operações de execução longa. |

## <a name="az-spring-cloud-app-stop"></a>AZ Spring-Cloud app Stop

Interrompa as instâncias do aplicativo.  O padrão é o ambiente de produção.

```cli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação-d | Nome de uma implantação existente do aplicativo.  O padrão é o ambiente de produção. |
| --sem espera | Não aguarde a conclusão das operações de execução longa. |

## <a name="az-spring-cloud-app-update"></a>AZ Spring-Cloud app Update

Atualize a configuração armazenada de um aplicativo.

```cli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Parâmetros necessários | |
| --- | ---: |
| --Name-n | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação-d | Nome de uma implantação existente do aplicativo.  O padrão é o ambiente de produção. |
| --Habilitar-armazenamento persistente | Boolean.  Se for true, monte um disco de 50 GB com o caminho padrão. |
| --env | Variáveis de ambiente separadas por espaços no formato ' Key [= value] '. |
| --é-público | Boolean.  Se for true, atribua um domínio público ao aplicativo. |
| --JVM-opções | Uma cadeia de caracteres que contém opções de JVM.  Use ' = ' em vez de ' ' para evitar erros de análise de Shell. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --sem espera | Não aguarde a conclusão das operações de execução longa. |
| --versão de tempo de execução | Versão de tempo de execução do idioma usado no aplicativo.  Valores permitidos: `Java_11`, `Java_8`. |

### <a name="example"></a>Exemplo

Adicione uma variável de ambiente para o aplicativo.

```cli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>lista de associação de aplicativo AZ Spring-Cloud

Listar todas as associações de serviço em um aplicativo.

```cli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>AZ Spring-Cloud app BIND remove

Remova uma associação de serviço do aplicativo.

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da Associação de serviço a ser removida. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>AZ Spring-Cloud app BIND show

Mostrar os detalhes de uma associação de serviço.

```cli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da Associação de serviço. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>AZ Spring-Cloud app BIND Cosmos Add

Associar um Azure Cosmos DB ao aplicativo.

```cli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros necessários | |
| --- | ---: |
| --tipo de API | Especifique o tipo de API usando um dos seguintes valores: Cassandra, Gremlin, Mongo, SQL, Table. |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da Associação de serviço. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

|Parâmetros opcionais | |
| --- | ---: |
| --coleção-nome | Nome da coleção.  Necessário ao usar Gremlin. |
| --nome-do-banco de dados | Nome do banco de dados.  Necessário ao usar Mongo, SQL e Gremlin. |
| --espaço-chave | Cassandra-espaço de chave.  Necessário ao usar Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>atualização de Cosmos AZ Spring-Cloud app BIND

```cli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da Associação de serviço. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

|Parâmetros opcionais | |
| --- | ---: |
| --coleção-nome | Nome da coleção.  Necessário ao usar Gremlin. |
| --nome-do-banco de dados | Nome do banco de dados.  Necessário ao usar Mongo, SQL e Gremlin. |
| --espaço-chave | Cassandra-espaço de chave.  Necessário ao usar Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>AZ Spring-Cloud app BIND MySQL Add

```cli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome-do-banco de dados | Nome do banco de dados. |
| --chave | Chave de API do serviço. |
| --nome | Nome da Associação de serviço. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --ID-do-recurso | ID de recurso do Azure do serviço com o qual associar. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |
| --username | Nome de usuário para acesso ao banco de dados. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>atualização do MySQL AZ Spring-Cloud app BIND

Atualize a conexão de associação de serviço do aplicativo para um banco de dados do Azure para MySQL.

```cli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da Associação de serviço. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --nome-do-banco de dados | Nome do banco de dados. |
| --chave | Chave de API do serviço. |
| --username | Nome de usuário para acesso ao banco de dados. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>AZ Spring-Cloud app BIND Redis Add

Associe um cache do Azure para Redis com o aplicativo.

```cli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da Associação de serviço. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --ID-do-recurso | ID de recurso do Azure do serviço com o qual você deseja associar. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --desabilitar-SSL | Desabilitar SSL. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>atualização de Redis AZ Spring-Cloud app BIND

Atualize uma associação de serviço para o cache do Azure para Redis.

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da Associação de serviço. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --desabilitar-SSL | Desabilitar SSL. |

## <a name="az-spring-cloud-app-deployment-create"></a>AZ Spring-Cloud app Deployment criar

Crie uma implantação de preparo para o aplicativo.

Para implantar o código ou atualizar as configurações para uma implantação existente, use `az spring-cloud app deploy --deployment <staging-deployment>` ou "AZ Spring-Cloud app Update--implantação <staging deployment>.

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da Associação de serviço. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --CPU | Número de núcleos de CPU virtual por instância.  Padrão: 1 |
| --env | Variáveis de ambiente separadas por espaços no formato ' Key [= value] '. |
| --contagem de instâncias | Número de instâncias. Padrão: 1. |
| --jar-caminho | Se fornecido, implante jar.  Caso contrário, implante a pasta atual como um tar. |
| --JVM-opções | Uma cadeia de caracteres que contém opções de JVM.  Use ' = ' em vez de ' ' para evitar erros de análise de Shell. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memória | Número de GB de memória por instância. |
| --sem espera | Não aguarde até que as operações de longa execução sejam concluídas. |
| --versão de tempo de execução | Versão de tempo de execução do idioma usado no aplicativo.  Valores permitidos: `Java_11`, `Java_8`. |
| --ignorar-clonar-configurações | Crie uma implantação de preparo clonando as configurações de implantação de produção atuais. |
| --módulo de destino | Módulo filho a ser implantado.  Necessário quando vários pacotes JAR são criados a partir do código-fonte. |
| --versão | Versão da implantação.  Inalterado se não estiver definido. |

### <a name="examples"></a>Exemplos

Implante o código-fonte em uma nova implantação do aplicativo.  Isso irá empacotar o diretório atual, criar um binário usando o sistema de compilação dinâmica e, em seguida, implantar.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Implante um jar predefinido em um aplicativo com opções de JVM e variáveis de ambiente.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>AZ Spring-Cloud app Deployment excluir

Exclua uma implantação do aplicativo.

```cli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da implantação. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>lista de implantação de aplicativo AZ Spring-Cloud

Listar todas as implantações em um aplicativo.

```cli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>AZ Spring-Cloud app Deployment show

Mostrar detalhes de uma implantação.

```cli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Parâmetros necessários | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --nome | Nome da implantação. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço-s | Nome da nuvem do Azure Spring.  Você pode configurar o serviço padrão usando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>AZ Spring-Cloud config-servidor limpar

Apague todas as definições de configuração no servidor de configuração.

```cli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>AZ Spring-Cloud CONFIG-Server Set

Defina as definições de configuração no servidor de configuração usando um arquivo YAML.

```cli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Parâmetros necessários | |
| --- | ---: |
| --arquivo de configuração | Caminho do arquivo para um manifesto do YAML para a configuração do servidor de configuração. |
| --nome | Nome da nuvem do Azure Spring. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --sem espera | Não para concluir operações de longa execução.

## <a name="az-spring-cloud-config-server-show"></a>AZ Spring-Cloud config-servidor show

Mostrar as configurações do servidor de configuração.

```cli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>AZ Spring-Cloud CONFIG-Server git Set

Defina as propriedades do git para o servidor de configuração.  Isso substituirá todas as propriedades git existentes.

```cli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --URI | URI da configuração adicionada. |

| Parâmetros opcionais | |
| --- | ---: |
| --adiar | Armazene temporariamente o objeto no cache local em vez de enviar para o Azure.  Use `az cache` para exibir/limpar. |
| --host-chave | Chave de host para a configuração adicionada. |
| --host-chave-algoritmo | Algoritmo de chave do host para a configuração adicionada. |
| --rótulo | Rótulo da configuração adicionada. |
| --password | Senha da configuração adicionada. |
| --chave privada | Chave privada da configuração adicionada. |
| --caminhos de pesquisa | Caminhos de pesquisa da configuração adicionada.  Use delimitadores de vírgula para vários caminhos. |
| --verificação de chave de host estrito | Habilita a verificação de chave de host estrita da configuração adicionada. |
| --username | Nome de usuário da configuração adicionada. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>AZ Spring-Cloud CONFIG-Server git Repositório Add

```cli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --nome do repositório | URI do repositório. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --URI | URI da configuração adicionada. |

| Parâmetros opcionais | |
| --- | ---: |
| --adiar | Armazene temporariamente o objeto no cache local em vez de enviar para o Azure.  Use `az cache` para exibir/limpar. |
| --host-chave | Chave de host para a configuração adicionada. |
| --host-chave-algoritmo | Algoritmo de chave do host para a configuração adicionada. |
| --rótulo | Rótulo da configuração adicionada. |
| --password | Senha da configuração adicionada. |
| --padrão | Padrão para o repositório.  Use delimitadores de vírgula para vários caminhos.|
| --chave privada | Chave privada da configuração adicionada. |
| --caminhos de pesquisa | Caminhos de pesquisa da configuração adicionada.  Use delimitadores de vírgula para vários caminhos. |
| --verificação de chave de host estrito | Habilita a verificação de chave de host estrita da configuração adicionada. |
| --username | Nome de usuário da configuração adicionada. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>AZ Spring-Cloud config-servidor git repositório lista

Listar todos os repositórios git definidos no servidor de configuração

```cli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --adiar | Armazene temporariamente o objeto no cache local em vez de enviar para o Azure.  Use `az cache` para exibir/limpar. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>AZ Spring-Cloud CONFIG-Server git Repositório remove

Remova uma configuração de repositório git existente do servidor de configuração.

```cli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --nome do repositório | URI do repositório. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --adiar | Armazene temporariamente o objeto no cache local em vez de enviar para o Azure.  Use `az cache` para exibir/limpar. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>AZ Spring-Cloud Test-Endpoint Disable

Desabilitar ponto de extremidade de teste da nuvem do Azure Spring

```cli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>AZ Spring-Cloud Test-Endpoint Enable

Habilite o ponto de extremidade de teste para a nuvem do Azure Spring. 

```cli 
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>AZ Spring-Cloud Test-Endpoint List 

Liste as chaves de ponto de extremidade de teste disponíveis para a nuvem do Azure Spring.

```cli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --aplicativo | Nome do aplicativo. |
| --implantação-d | Nome de uma implantação existente do aplicativo.  O padrão será a produção, se não for especificado. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>AZ Spring-Cloud Test-Endpoint Renovate-Key

Regenerar uma chave de ponto de extremidade de teste para a nuvem do Azure Spring.

```cli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Parâmetros necessários | |
| --- | ---: |
| --nome | Nome da nuvem do Azure Spring. |
| --Resource-Group-g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --tipo | Tipo de chave de ponto de extremidade de teste.  Valores permitidos: primário, secundário. |
