---
title: nuvem de primavera az
description: Gerencie a nuvem de primavera azure usando o Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298782"
---
# <a name="az-spring-cloud"></a>az primavera-nuvem

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Gerencie a nuvem de primavera azure usando o Azure CLI

>[!Note]
> A Nuvem de primavera Azure está atualmente em pré-visualização.  Estes comandos podem ser alterados ou removidos numa futura libertação.

| az primavera-nuvem |  |
|------|------:|
| [az primavera-nuvem criar](#az-spring-cloud-create) | Crie uma instância azure spring cloud. |
| [az primavera-nuvem apagar](#az-spring-cloud-delete) | Elimine uma instância azure spring cloud. |
| [az lista de nuvens de primavera](#az-spring-cloud-list) | Enumerar todos os casos da Nuvem de primavera Azure no determinado grupo de recursos, caso contrário, lista os IDs de subscrição. |
| [az primavera-cloud show](#az-spring-cloud-show) | Mostre os detalhes para uma Nuvem de primavera Azure. |

| az app nuvem de primavera | Comandos para gerir aplicações na Nuvem de primavera Azure.  |
| ---- | ----: |
| [az app de nuvem de primavera criar](#az-spring-cloud-app-create) | Crie uma nova aplicação com uma implementação padrão na Nuvem de primavera Azure. |
| [az app nuvem de primavera excluir](#az-spring-cloud-app-delete) | Elimine uma aplicação na Nuvem de primavera Azure. |
| [az mol-cloud app implementar](#az-spring-cloud-app-deploy) | Implemente a partir do código fonte ou de um binário pré-construído para uma aplicação e atualize configurações relacionadas. |
| [az lista de aplicativos de nuvem de primavera](#az-spring-cloud-app-list) | Liste todas as aplicações na Nuvem de primavera Azure. |
| [az primavera-nuvem app reiniciar](#az-spring-cloud-app-restart) | Reiniciar as instâncias da aplicação utilizando os padrãos de implementação da produção. |
| [escala de aplicativo sinuoso de nuvem de primavera](#az-spring-cloud-app-scale) | Escala manualmente uma aplicação ou as suas implementações. |
| [az spring-cloud app set-deployment](#az-spring-cloud-app-set-deployment) | Detete a implantação da produção de uma aplicação. |
| [az primavera-nuvem show](#az-spring-cloud-app-show) | Mostre os detalhes de uma aplicação na Nuvem de primavera Azure. |
| [az spring-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Mostre registos de construção para a mais recente implantação a partir da fonte. Incumprimentos na implantação da produção. |
| [az primavera-nuvem começar](#az-spring-cloud-app-start) | Inicie as instâncias da aplicação utilizando os padrãode implementação da produção. |
| [az paragem de aplicativo de nuvem de primavera](#az-spring-cloud-app-stop) | Pare as instâncias da aplicação usando os padrãos de implementação da produção. |
| [az atualização de aplicativo de nuvem de primavera](#az-spring-cloud-app-update) | Atualize a configuração da aplicação especificada. |

| az mol-cloud app encadernação | Comandos para gerir ligações com os Serviços de Dados Azure.  A aplicação deve ser reiniciada antes que estas definições entrem em vigor. |
| --- | ---: |
| [az lista de ligação de aplicativo de nuvem de primavera](#az-spring-cloud-app-binding-list) | Enumere todas as encadernações de serviço numa aplicação. |
| [az primavera-nuvem de ligação remover](#az-spring-cloud-app-binding-remove) | Remova uma ligação de serviço da aplicação. |
| [az primavera-nuvem app show de ligação](#az-spring-cloud-app-binding-show) | Mostre os detalhes de uma ligação de serviço. |
| [az primavera-nuvem app ligar cosmos adicionar](#az-spring-cloud-app-binding-cosmos-add) | Ligue um Azure CosmosDB com a app. |
| [az primavera-nuvem app binding cosmos update](#az-spring-cloud-app-binding-cosmos-update) | Atualize uma ligação de serviço Azure CosmosDB. |
| [az app de nuvem de primavera ligando mysql adicionar](#az-spring-cloud-app-binding-mysql-add) | Ligue uma Base de Dados Azure para MySQL com a aplicação. |
| [az app de nuvem de primavera ligando atualização mysql](#az-spring-cloud-app-binding-mysql-update) | Atualize uma Base de Dados Azure para a ligação ao serviço MySQL. |
| [az primavera-nuvem app ligando redis adicionar](#az-spring-cloud-app-binding-redis-add) | Ligue um Azure Cache para Redis com a aplicação. |
| [az primavera-nuvem app binding redis update](#az-spring-cloud-app-binding-redis-update) | Atualize um Cache Azure para a ligação ao serviço Redis. |

| az spring-cloud app implantação | Comandos para gerir o ciclo de vida de implantação de uma aplicação em Azure Spring Cloud. |
| --- | ---: |
| [az spring-cloud app implementação criar](#az-spring-cloud-app-deployment-create) | Crie uma implementação de encenação para a aplicação. |
| [az mol-cloud app eliminar](#az-spring-cloud-app-deployment-delete) | Apague uma implementação da aplicação. |
| [az lista de implementação de aplicativos de nuvem de primavera](#az-spring-cloud-app-deployment-list) | Enumerar todas as implementações numa aplicação. |
| [az spring-cloud app deployment show](#az-spring-cloud-app-deployment-show) | Mostre detalhes da implantação. |

| az primavera-nuvem config-server | Comandos para gerir o Servidor De Config azure Spring Cloud. |
| --- | ---: |
| [az primavera-nuvem config-servidor claro](#az-spring-cloud-config-server-clear) | Apagar todas as definições no Servidor Config. |
| [az mol-cloud config-server conjunto](#az-spring-cloud-config-server-set) | Defina o Config Server a partir de um ficheiro YAML. |
| [az primavera-nuvem config-server show](#az-spring-cloud-config-server-show) | Mostre a configuração do Servidor Config. |
| [az mol-nuvem config conjunto git](#az-spring-cloud-config-server-git-set) | Defina propriedades git para o Servidor Config.  Os valores anteriores serão substituídos. |
| [az primavera-nuvem config servidor git repo adicionar](#az-spring-cloud-config-server-git-repo-add) | Adicione um novo config de repositório git ao Servidor Config. |
| [az primavera-nuvem config servidor git repo lista](#az-spring-cloud-config-server-git-repo-list) | Enumerar todos os configs de repositório git para o Config Server. |
| [az primavera-nuvem config servidor git repo remover](#az-spring-cloud-config-server-git-repo-remove) | Retire o repositório de git especificado do Servidor Config. |

| az spring-cloud ponto final | Comandos para gerir testes de ponto final na Nuvem de primavera de Azure |
| --- | ---: |
| [az spring-cloud test-endpoint desativar](#az-spring-cloud-test-endpoint-disable) | Desativar o ponto final do teste. |
| [az mol-cloud test-endpoint permitir](#az-spring-cloud-test-endpoint-enable) | Ativar o ponto final do teste. |
| [az lista de ponto final de teste de nuvem de primavera](#az-spring-cloud-test-endpoint-list) | Lista de chaves finais de teste. |
| [az spring-cloud test-endpoint renovar chave](#az-spring-cloud-test-endpoint-renew-key) | Regenerar uma chave de ponto final de teste. |

## <a name="az-spring-cloud-create"></a>az primavera-nuvem criar

Crie uma nova aplicação com uma implementação padrão na Nuvem de primavera Azure.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome para este caso Azure Spring Cloud. |
| --grupo de recursos -g | Especifica o grupo de recursos para esta aplicação.  Configure o grupo predefinido usando`az configure --defaults group=<name>` |

| Parâmetros Opcionais | |
| --- | ---: |
| --localização -l | Especifica a localização do servidor para esta aplicação.  Encontre locais válidos usando`az account list-locations` |
| - não esperar | Não para que as operações de longo prazo terminem.

### <a name="examples"></a>Exemplos

Crie uma nova Nuvem de primavera Azure em WestUS

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az primavera-nuvem apagar

Elimine uma instância azure spring cloud.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da instância Azure Spring Cloud a ser eliminado. |
| --grupo de recursos -g | Nome do grupo de recursos a que pertence a Nuvem de primavera Azure. |

| Parâmetros Opcionais | |
| --- | ---: |
| - sem espera | Não espere que as operações de longa duração terminem. |

### <a name="example"></a>Exemplo

Elimine uma instância azure Spring Cloud chamada "MyService" do "MyResourceGroup".

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az lista de nuvens de primavera

Enumerar todos os casos da Nuvem de primavera Azure associados ao determinado grupo de recursos. Se nenhum grupo de recursos for especificado, enumere os IDs de subscrição.

```azurecli
az spring-cloud list --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --grupo de recursos -g | Nome do grupo de recursos. |

## <a name="az-spring-cloud-show"></a>az primavera-cloud show

Mostre os detalhes para a especificada instância azure spring cloud.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da instância Azure Spring Cloud. |
| --grupo de recursos -g | Nome do Grupo de Recursos a que pertence a instância azure Spring Cloud.

## <a name="az-spring-cloud-app-create"></a>az app de nuvem de primavera criar

Crie uma nova aplicação numa Nuvem de primavera Azure.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| -cpu | Número de núcleos virtuais por exemplo.  Padrão: 1. |
| --habilitar-persistente-armazenamento | Valor booleano.  Se for verdade, monta um disco de 50GB com caminho predefinido. |
| -contagem de instâncias | Número de casos.  Padrão: 1. |
| --é público | Valor booleano.  Se for verdade, atribui um domínio público. |
| -memória | Número de GB de memória por exemplo.  Padrão: 1. |

### <a name="examples"></a>Exemplos

Crie uma aplicação com a configuração padrão.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Crie uma aplicação acessível ao público com 3 instâncias.  Cada instância tem 3 GB de memória e 2 núcleos CPU.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az app nuvem de primavera excluir

Elimina uma aplicação na Nuvem de primavera Azure.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-app-deploy"></a>az mol-cloud app implementar

Implemente uma aplicação para o Azure Spring Cloud a partir do código fonte ou de um binário pré-construído, e atualize configurações relacionadas.

```azurecli
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

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| -cpu | Número de núcleos de IPC virtuais por exemplo. |
| --implantação -d | Nome de uma implementação de aplicações existente.  Incumprimentos na implantação de produção, se não especificados. |
| -env | Variáveis ambientais separadas pelo espaço no formato 'key[=value]' |
| -contagem de instâncias | Número de casos. |
| --jar-caminho | Se fornecido, desloque o jarro do dado caminho. Caso contrário, implante a pasta atual como alcatrão. |
| --jvm-options | Uma corda contendo opções JVM.  Utilize '=' em vez de ' ' para evitar erros de análise de conchas. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m`. |
| -memória | Número de GB de memória por exemplo. |
| - não esperar | Não espere que as operações de longa duração terminem. |
| -versão runtime | Versão runtime do idioma usado na aplicação.  Valores `Java_11`permitidos: . `Java_8` |
| --módulo-alvo | Módulo infantil a ser implantado.  Necessário quando vários pacotes de frascos são construídos a partir do código fonte. |
| -versão | Versão de implantação.  Inalterado se não estiver definido. |

### <a name="examples"></a>Exemplos

Implemente o código fonte numa aplicação. Isto irá embalar o atual diretório, construir um binário usando o Serviço de Construção Pivotal e, em seguida, implantar para a app.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Implemente um frasco pré-construído para uma aplicação utilizando opções JVM e variáveis ambientais.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Implemente o código fonte para uma implementação específica de uma aplicação.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az lista de aplicativos de nuvem de primavera

Liste todas as aplicações na instância Azure Spring Cloud.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Parâmetros Obrigatórios | |
| --- | ---: |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-app-restart"></a>az primavera-nuvem app reiniciar

Reiniciar as instâncias da aplicação.  Incumprimentos na implantação da produção.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome da implementação existente da app.  Incumprimentos na implantação de produção, se não especificados. |
| - não esperar | Não espere que as operações de longa duração terminem. |

## <a name="az-spring-cloud-app-scale"></a>escala de aplicativo sinuoso de nuvem de primavera

Escala manualmente uma aplicação ou as suas implementações.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| -cpu | Número de núcleos de CPU virtuais por instância de aplicação. |
| --implantação -d | Nome da implementação existente da app.  Incumprimentos na implantação de produção, se não especificados. |
| -contagem de instâncias | Número de casos desta aplicação. |
| -memória | Número de GB de memória por instância de aplicação. |
| - não esperar | Não espere que as operações de longa duração terminem. |

### <a name="examples"></a>Exemplos

Aumentar uma aplicação para 4 núcleos CPU e 8 GB de memória por exemplo.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Dimensione uma implementação da app para 5 instâncias.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az spring-cloud app set-deployment

Detete as opções de configuração para a implementação da produção da aplicação.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da app. |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| - não esperar | Não espere que as operações de longa duração terminem. |

### <a name="examples"></a>Exemplos

Troque uma implementação de encenação da app para a produção.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az primavera-nuvem show

Mostre os detalhes de uma aplicação na Nuvem de primavera Azure.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az spring-cloud app show-deploy-log

Mostre o registo de construção da última implementação a partir do código fonte.  Incumprimentos no ambiente de produção.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da app.  Incumprimentos no ambiente de produção. |

## <a name="az-spring-cloud-app-start"></a>az primavera-nuvem começar

Inicia as instâncias da aplicação.  Incumprimentos no ambiente de produção.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da app.  Incumprimentos no ambiente de produção. |
| - não esperar | Não espere que as operações de longa duração terminem. |

## <a name="az-spring-cloud-app-stop"></a>az paragem de aplicativo de nuvem de primavera

Pare os casos da aplicação.  Incumprimentos no ambiente de produção.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da app.  Incumprimentos no ambiente de produção. |
| - não esperar | Não espere que as operações de longa duração terminem. |

## <a name="az-spring-cloud-app-update"></a>az atualização de aplicativo de nuvem de primavera

Atualize a configuração armazenada de uma aplicação.

```azurecli
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

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da app.  Incumprimentos no ambiente de produção. |
| --habilitar-persistente-armazenamento | Boolean.  Se for verdade, monte um disco de 50GB com o caminho predefinido. |
| -env | Variáveis ambientais separadas pelo espaço no formato 'key[=value]' |
| --é público | Boolean.  Se for verdade, atribua um domínio público à aplicação. |
| --jvm-options | Uma corda contendo opções JVM.  Utilize '=' em vez de ' ' para evitar erros de análise de conchas. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m`. |
| - não esperar | Não espere que as operações de longa duração terminem. |
| -versão runtime | Versão runtime do idioma usado na aplicação.  Valores `Java_11`permitidos: . `Java_8` |

### <a name="example"></a>Exemplo

Adicione uma variável ambiental para a aplicação.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az lista de ligação de aplicativo de nuvem de primavera

Enumere todas as encadernações de serviço numa aplicação.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-app-binding-remove"></a>az primavera-nuvem de ligação remover

Remova uma ligação de serviço da aplicação.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome | O nome da ligação de serviço a remover. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-app-binding-show"></a>az primavera-nuvem app show de ligação

Mostre os detalhes de uma ligação de serviço.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az primavera-nuvem app ligar cosmos adicionar

Ligue um Azure Cosmos DB com a app.

```azurecli
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

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -api-tipo | Especifique o tipo API utilizando um dos seguintes valores: cassandra, gremlin, mongo, sql, tabela. |
| -app | Nome da aplicação. |
| -nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

|Parâmetros Opcionais | |
| --- | ---: |
| --nome da coleção | Nome da coleção.  Necessário quando utilizar Gremlin. |
| -nome da base de dados | Nome da base de dados.  Necessário ao utilizar Mongo, SQL e Gremlin. |
| --espaço-chave | Cassandra espaço-chave.  Necessário quando se usa a Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az primavera-nuvem app binding cosmos update

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

|Parâmetros Opcionais | |
| --- | ---: |
| --nome da coleção | Nome da coleção.  Necessário quando utilizar Gremlin. |
| -nome da base de dados | Nome da base de dados.  Necessário ao utilizar Mongo, SQL e Gremlin. |
| --espaço-chave | Cassandra espaço-chave.  Necessário quando se usa a Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az app de nuvem de primavera ligando mysql adicionar

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome da base de dados | Nome da base de dados. |
| --chave | Chave API do serviço. |
| -nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --recurso-id | Id de recurso Azure do serviço para se ligar. |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |
| --username | Nome de utilizador para acesso à base de dados. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az app de nuvem de primavera ligando atualização mysql

Atualize a ligação de ligação de serviço da app a uma Base de Dados Azure para MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| -nome da base de dados | Nome da base de dados. |
| --chave | Chave API do serviço. |
| --username | Nome de utilizador para acesso à base de dados. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az primavera-nuvem app ligando redis adicionar

Ligue um Azure Cache para Redis com a aplicação.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --recurso-id | Id de recursos Azure do serviço com o que pretende ligar. |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| --desactivação-ssl | Desativar tls. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az primavera-nuvem app binding redis update

Atualize uma ligação de serviço para Azure Cache para Redis.

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| --desactivação-ssl | Desativar tls. |

## <a name="az-spring-cloud-app-deployment-create"></a>az spring-cloud app implementação criar

Crie uma implementação de encenação para a aplicação.

Para implementar código ou atualizar as definições `az spring-cloud app deploy --deployment <staging-deployment>` para uma implementação existente, use <staging deployment>ou 'az spring-cloud app update --deploy .

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| -cpu | Número de núcleos de CPU virtuais por exemplo.  Padrão: 1 |
| -env | Variáveis ambientais separadas pelo espaço no formato 'key[=value]' |
| -contagem de instâncias | Número de casos. Padrão: 1. |
| --jar-caminho | Se fornecido, desloque o jarro.  Caso contrário, implante a pasta atual como alcatrão. |
| --jvm-options | Uma corda contendo opções JVM.  Utilize '=' em vez de ' ' para evitar erros de análise de conchas. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m`. |
| -memória | Número de GB de memória por exemplo. |
| - não esperar | Não espere que as operações de longa duração terminem. |
| -versão runtime | Versão runtime do idioma usado na aplicação.  Valores `Java_11`permitidos: . `Java_8` |
| --skip-clone-settings | Criar uma implantação de encenação clonando as definições de implantação de produção atuais. |
| --módulo-alvo | Módulo infantil a ser implantado.  Necessário quando vários pacotes de frascos são construídos a partir do código fonte. |
| -versão | Versão de implantação.  Inalterado se não estiver definido. |

### <a name="examples"></a>Exemplos

Implemente o código fonte para uma nova implementação da aplicação.  Isto irá embalar o atual diretório, construir um binário usando o Sistema de Construção Pivotal e, em seguida, implantar.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Implemente um frasco pré-construído para uma aplicação com opções JVM e variáveis ambientais.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az mol-cloud app eliminar

Apague uma implementação da aplicação.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome | Nome do destacamento. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-app-deployment-list"></a>az lista de implementação de aplicativos de nuvem de primavera

Enumerar todas as implementações numa aplicação.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-app-deployment-show"></a>az spring-cloud app deployment show

Mostre detalhes de um destacamento.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -app | Nome da aplicação. |
| -nome | Nome do destacamento. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| --serviço -s | Nome da Nuvem de primavera Azure.  Pode configurar o serviço `az configure --defaults spring-cloud=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-config-server-clear"></a>az primavera-nuvem config-servidor claro

Apagar todas as definições de configuração no Servidor Config.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-config-server-set"></a>az mol-cloud config-server conjunto

Detete as definições de configuração no Servidor Config utilizando um ficheiro YAML.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --config-file | Caminho de ficheiro para um manifesto YAML para a configuração do Servidor Config. |
| -nome | Nome da Nuvem de primavera Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| - não esperar | Não para que as operações de longo prazo terminem.

## <a name="az-spring-cloud-config-server-show"></a>az primavera-nuvem config-server show

Mostre as definições do Config Server.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-config-server-git-set"></a>az mol-nuvem config-servidor git conjunto

Detete as propriedades de git para o Config Server.  Isto irá substituir todas as propriedades existentes.

```azurecli
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

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| -uri | URI do config adicionado. |

| Parâmetros Opcionais | |
| --- | ---: |
| -adiar | Guarde temporariamente o objeto na cache local em vez de enviar para o Azure.  Use `az cache` para ver /limpar. |
| ---host-key | Chave de acolhimento para o config adicionado. |
| --algoritmo de porta-chaves de anfitrião | Algoritmo chave anfitrião para o config adicionado. |
| -rótulo | Etiqueta do config adicionado. |
| --password | Senha do config adicionado. |
| --private-key | Chave privada do config adicionado. |
| --search-paths | Caminhos de busca do config adicionado.  Utilize delimitadores de vírina para múltiplos caminhos. |
| --rigoroso-anfitrião-chave-checking | Permite uma verificação rigorosa da chave do hospedeiro do config adicionado. |
| --username | Nome de utilizador do config adicionado. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az primavera-nuvem config-servidor git repo adicionar

```azurecli
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

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| -repo-name | URI do repo. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| -uri | URI do config adicionado. |

| Parâmetros Opcionais | |
| --- | ---: |
| -adiar | Guarde temporariamente o objeto na cache local em vez de enviar para o Azure.  Use `az cache` para ver /limpar. |
| ---host-key | Chave de acolhimento para o config adicionado. |
| --algoritmo de porta-chaves de anfitrião | Algoritmo chave anfitrião para o config adicionado. |
| -rótulo | Etiqueta do config adicionado. |
| --password | Senha do config adicionado. |
| --padrão | Padrão para o repo.  Utilize delimitadores de vírina para múltiplos caminhos.|
| --private-key | Chave privada do config adicionado. |
| --search-paths | Caminhos de busca do config adicionado.  Utilize delimitadores de vírina para múltiplos caminhos. |
| --rigoroso-anfitrião-chave-checking | Permite uma verificação rigorosa da chave do hospedeiro do config adicionado. |
| --username | Nome de utilizador do config adicionado. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az primavera-nuvem config-servidor git repo lista

Lista rit repos definidos no Servidor Config

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| -adiar | Guarde temporariamente o objeto na cache local em vez de enviar para o Azure.  Use `az cache` para ver /limpar. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az primavera-nuvem config-servidor git repo remover

Remova uma configuração existente de git repo do Config Server.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| -repo-name | URI do repo. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| -adiar | Guarde temporariamente o objeto na cache local em vez de enviar para o Azure.  Use `az cache` para ver /limpar. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az spring-cloud test-endpoint desativar

Desativar o ponto final do teste da Nuvem de primavera Azure

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az mol-cloud test-endpoint permitir

Ativar o ponto final de teste para a nuvem de mola Azure. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |

## <a name="az-spring-cloud-test-endpoint-list"></a>az lista de ponto final de teste de nuvem de primavera 

Enumera rindo as chaves finais de teste disponíveis para a Nuvem de primavera Azure.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |

| Parâmetros Opcionais | |
| --- | ---: |
| -app | Nome da aplicação. |
| --implantação -d | Nome de uma implementação existente da app.  Incumprimentos à produção se não especificados. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az spring-cloud test-endpoint renovar chave

Regenerar uma chave de ponta de teste para a Nuvem de primavera Azure.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| -nome | Nome da Nuvem de primavera Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo `az configure --defaults group=<name>`predefinido utilizando . |
| -tipo | Tipo de chave final de teste.  Valores permitidos: Primário, Secundário. |
