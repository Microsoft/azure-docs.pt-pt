---
title: nuvem de primavera az
description: Gerir a nuvem de primavera Azure usando o Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80298782"
---
# <a name="az-spring-cloud"></a>az nuvem de primavera

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Gerir a nuvem de primavera Azure usando o Azure CLI

>[!Note]
> A azure Spring Cloud está atualmente em pré-visualização.  Estes comandos podem ser alterados ou removidos numa futura libertação.

| az nuvem de primavera |  |
|------|------:|
| [az primavera-nuvem criar](#az-spring-cloud-create) | Crie uma instância Azure Spring Cloud. |
| [az nuvem de primavera delete](#az-spring-cloud-delete) | Apague uma instância da Nuvem de primavera Azure. |
| [lista de nuvens de primavera az](#az-spring-cloud-list) | Listar todas as instâncias Azure Spring Cloud no grupo de recursos dado, caso contrário, liste os IDs de subscrição. |
| [az primavera-nuvem show](#az-spring-cloud-show) | Mostre os detalhes para uma Nuvem de primavera Azure. |

| app az primavera nuvem | Comandos para gerir aplicativos na Nuvem de primavera de Azure.  |
| ---- | ----: |
| [app az primavera-cloud criar](#az-spring-cloud-app-create) | Crie uma nova aplicação com uma implementação padrão na Nuvem de primavera do Azure. |
| [app az nuvem de primavera delete](#az-spring-cloud-app-delete) | Elimine uma aplicação na Nuvem de primavera de Azure. |
| [app az primavera-cloud implementar](#az-spring-cloud-app-deploy) | Implemente a partir do código fonte ou de um binário pré-construído para uma aplicação e atualização de configurações relacionadas. |
| [az lista de aplicativos de nuvem de primavera](#az-spring-cloud-app-list) | Listar todas as aplicações na Nuvem de primavera de Azure. |
| [app az primavera-cloud reiniciar](#az-spring-cloud-app-restart) | Reiniciar as instâncias da aplicação utilizando padrão de implementação de produção. |
| [escala de aplicativo az nuvem de primavera](#az-spring-cloud-app-scale) | Dimensione manualmente uma aplicação ou as suas implementações. |
| [az definição de aplicativo de nuvem de primavera](#az-spring-cloud-app-set-deployment) | Definir a implementação de produção de uma aplicação. |
| [az primavera-cloud show](#az-spring-cloud-app-show) | Mostre os detalhes de uma aplicação na Azure Spring Cloud. |
| [az spring-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Mostrar registos de construção para a mais recente implantação a partir da fonte. Incumprimentos à implantação da produção. |
| [az primavera-cloud app começar](#az-spring-cloud-app-start) | Inicie casos da aplicação utilizando padrão de implementação de produção. |
| [az primavera nuvem paragem](#az-spring-cloud-app-stop) | Parar as instâncias da aplicação usando padrão de implementação de produção. |
| [az atualização de aplicativos de nuvem de primavera](#az-spring-cloud-app-update) | Atualize a configuração da aplicação especificada. |

| az primavera-cloud app binding | Comandos para gerir encadernações com a Azure Data Services.  A aplicação deve ser reiniciada antes que estas definições produzam efeitos. |
| --- | ---: |
| [lista de ligação az-cloud app](#az-spring-cloud-app-binding-list) | Enumere todas as encadernações de serviço numa aplicação. |
| [az aplicativo de mola-cloud remover](#az-spring-cloud-app-binding-remove) | Remova uma ligação de serviço da aplicação. |
| [az primavera-cloud app show de ligação](#az-spring-cloud-app-binding-show) | Mostre os detalhes de uma ligação de serviço. |
| [az primavera nuvem ligação cosmos adicionar](#az-spring-cloud-app-binding-cosmos-add) | Ligue um Azure CosmosDB com a aplicação. |
| [az spring-cloud app ligando cosmos atualização](#az-spring-cloud-app-binding-cosmos-update) | Atualize uma ligação de serviço Azure CosmosDB. |
| [app de mola-nuvem az ligando mysql adicionar](#az-spring-cloud-app-binding-mysql-add) | Ligue uma base de dados Azure para o MySQL com a aplicação. |
| [app az primavera-cloud ligando atualização mysql](#az-spring-cloud-app-binding-mysql-update) | Atualize uma base de dados Azure para a ligação do serviço MySQL. |
| [az aplicativo de mola-cloud ligação redis adicionar](#az-spring-cloud-app-binding-redis-add) | Ligue um Cache Azure para redis com a aplicação. |
| [az app de ligação de molas redis atualização](#az-spring-cloud-app-binding-redis-update) | Atualize uma cache Azure para a ligação de serviço redis. |

| implementação de aplicativos az nuvem de primavera | Comandos para gerir o ciclo de vida de implementação de uma aplicação em Azure Spring Cloud. |
| --- | ---: |
| [implementação de aplicativos az nuvem de primavera criar](#az-spring-cloud-app-deployment-create) | Crie uma implementação de encenação para a aplicação. |
| [implementação de aplicativos az nuvem de primavera eliminar](#az-spring-cloud-app-deployment-delete) | Elimine uma implementação da aplicação. |
| [lista de implementação de aplicativos de nuvem de primavera az](#az-spring-cloud-app-deployment-list) | Listar todas as implementações numa aplicação. |
| [az primavera-cloud show de implementação de aplicativo](#az-spring-cloud-app-deployment-show) | Mostrar detalhes da implantação. |

| az mola-nuvem config-servidor | Comandos para gerir o Azure Spring Cloud Config Server. |
| --- | ---: |
| [az mola-nuvem config-servidor claro](#az-spring-cloud-config-server-clear) | Apoufique todas as definições no Servidor Config. |
| [az mola-nuvem conjunto config-servidor](#az-spring-cloud-config-server-set) | Defina o Servidor Config a partir de um ficheiro YAML. |
| [az primavera-nuvem config-servidor show](#az-spring-cloud-config-server-show) | Mostrar a configuração do Servidor Config. |
| [az mola nuvem config servidor git conjunto](#az-spring-cloud-config-server-git-set) | Defina propriedades de git para o Servidor Config.  Os valores anteriores serão substituídos. |
| [az primavera-cloud config servidor git repo adicionar](#az-spring-cloud-config-server-git-repo-add) | Adicione um novo config de repositório de git ao Servidor Config. |
| [az primavera-cloud config servidor git lista de repo](#az-spring-cloud-config-server-git-repo-list) | Lista todos os configs de repositório de git para o Servidor Config. |
| [az primavera-cloud config servidor git repo remover](#az-spring-cloud-config-server-git-repo-remove) | Remova o repositório de git especificado do Servidor Config. |

| az primavera-nuvem ponto final | Comandos para gerir testes de ponto final em Azure Spring Cloud |
| --- | ---: |
| [az mola-nuvem teste-ponto final para desativar](#az-spring-cloud-test-endpoint-disable) | Desative o ponto final do teste. |
| [az mola-nuvem-teste-ponto final permitir](#az-spring-cloud-test-endpoint-enable) | Ativar o ponto final do teste. |
| [lista az primavera-nuvem test-endpoint](#az-spring-cloud-test-endpoint-list) | Listar as teclas do ponto final do teste. |
| [az primavera-nuvem teste-fim-chave](#az-spring-cloud-test-endpoint-renew-key) | Regenerar uma chave de ponta de teste. |

## <a name="az-spring-cloud-create"></a>az primavera-nuvem criar

Crie uma nova aplicação com uma implementação padrão na Nuvem de primavera do Azure.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome -n | Nome para este exemplo de Azure Spring Cloud. |
| --grupo de recursos -g | Especifica o grupo de recursos para esta aplicação.  Configure o grupo predefinido usando`az configure --defaults group=<name>` |

| Parâmetros Opcionais | |
| --- | ---: |
| --localização -l | Especifica a localização do servidor para esta aplicação.  Encontre locais válidos usando`az account list-locations` |
| -- sem espera | Não durante muito tempo as operações de longo prazo estão concluídas.

### <a name="examples"></a>Exemplos

Criar uma nova Nuvem de primavera Azure em WestUS

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az nuvem de primavera delete

Apague uma instância da Nuvem de primavera Azure.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome -n | Nome da instância Azure Spring Cloud a ser eliminada. |
| --grupo de recursos -g | Nome do grupo de recursos a que pertence a Nuvem de primavera de Azure. |

| Parâmetros Opcionais | |
| --- | ---: |
| - não esperar | Não espere que as operações de longo prazo terminem. |

### <a name="example"></a>Exemplo

Elimine uma instância Azure Spring Cloud chamada 'MyService' de 'MyResourceGroup'.

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>lista de nuvens de primavera az

Lista todas as instâncias Azure Spring Cloud associadas ao grupo de recursos. Se não for especificado nenhum grupo de recursos, liste os IDs de subscrição.

```azurecli
az spring-cloud list --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --grupo de recursos -g | Nome do grupo de recursos. |

## <a name="az-spring-cloud-show"></a>az primavera-nuvem show

Mostre os detalhes para a instância de Azure Spring Cloud especificada.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome -n | Nome do exemplo da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do Grupo de Recursos a que pertence a azure Spring Cloud.

## <a name="az-spring-cloud-app-create"></a>app az primavera-cloud criar

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
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --cpu | Número de núcleos virtuais por exemplo.  Predefinição: 1. |
| --ativar o armazenamento persistente | Valor booleano.  Se for verdade, monta um disco de 50GB com o caminho predefinido. |
| --contagem de instâncias | Número de casos.  Predefinição: 1. |
| --é público | Valor booleano.  Se for verdade, atribui um domínio público. |
| --memória | Número de GB de memória por exemplo.  Predefinição: 1. |

### <a name="examples"></a>Exemplos

Crie uma aplicação com a configuração padrão.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Crie uma aplicação acessível ao público com 3 instâncias.  Cada instância tem 3 GB de memória e 2 núcleos de CPU.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>app az nuvem de primavera delete

Elimina uma aplicação na Nuvem de primavera do Azure.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-deploy"></a>app az primavera-cloud implementar

Implemente uma aplicação para a Nuvem de primavera Azure a partir do código fonte ou de um binário pré-construído, e atualize configurações relacionadas.

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
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --cpu | Número de núcleos CPI virtuais por exemplo. |
| --implantação -d | Nome de uma aplicação existente.  Predefinições na implantação da produção se não for especificado. |
| --env | Variáveis de ambiente separadas pelo espaço no formato 'chave[=valor]». |
| --contagem de instâncias | Número de casos. |
| --jar-caminho | Se fornecido, desloque o jarro de um caminho dado. Caso contrário, insera a pasta atual como alcatrão. |
| --jvm-opções | Uma corda contendo opções JVM.  Utilize '=' em vez de ' ' para evitar erros de análise de conchas. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m` . |
| --memória | Número de GB de memória por exemplo. |
| -- sem espera | Não espere que as operações de longo prazo terminem. |
| --versão do tempo de execução | Versão runtime do idioma utilizado na aplicação.  Valores permitidos: `Java_11` `Java_8` . . . |
| --módulo-alvo | Módulo infantil a ser implantado.  Necessário quando vários pacotes de frascos são construídos a partir do código fonte. |
| -versão | Versão de implantação.  Inalterado se não estiver definido. |

### <a name="examples"></a>Exemplos

Implemente o código fonte para uma aplicação. Isto irá embalar o diretório atual, construir um binário usando o Pivotal Build Service e, em seguida, implementar para a app.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Implemente um frasco pré-construído para uma aplicação usando opções JVM e variáveis ambientais.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Implemente o código fonte para uma implementação específica de uma aplicação.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az lista de aplicativos de nuvem de primavera

Listar todas as aplicações na instância Azure Spring Cloud.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Parâmetros Obrigatórios | |
| --- | ---: |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-restart"></a>app az primavera-cloud reiniciar

Reiniciar instâncias da aplicação.  Incumprimentos da produção.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome da implementação existente da app.  Predefinições na implantação da produção se não for especificado. |
| -- sem espera | Não espere que as operações de longo prazo terminem. |

## <a name="az-spring-cloud-app-scale"></a>escala de aplicativo az nuvem de primavera

Dimensione manualmente uma aplicação ou as suas implementações.

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
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --cpu | Número de núcleos de CPU virtuais por instância de aplicação. |
| --implantação -d | Nome da implementação existente da app.  Predefinições na implantação da produção se não for especificado. |
| --contagem de instâncias | Número de casos desta aplicação. |
| --memória | Número de GB de memória por instância de aplicação. |
| -- sem espera | Não espere que as operações de longo prazo terminem. |

### <a name="examples"></a>Exemplos

Dimensione uma aplicação para 4 núcleos de CPU e 8 GB de memória por exemplo.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Escale a implementação da app para 5 instâncias.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az definição de aplicativo de nuvem de primavera

Desaprote as opções de configuração para a implementação da aplicação.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da aplicação. |
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| -- sem espera | Não espere que as operações de longo prazo terminem. |

### <a name="examples"></a>Exemplos

Troque uma implementação de encenação da app para a produção.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az primavera-cloud show

Mostre os detalhes de uma aplicação na Azure Spring Cloud.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az spring-cloud app show-deploy-log

Mostrar o registo de construção da última implantação a partir do código fonte.  Incumprimentos ao ambiente de produção.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da aplicação.  Incumprimentos do ambiente de produção. |

## <a name="az-spring-cloud-app-start"></a>az primavera-cloud app começar

Inicia instâncias da aplicação.  Incumprimentos ao ambiente de produção.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da aplicação.  Incumprimentos do ambiente de produção. |
| -- sem espera | Não espere que as operações de longo prazo terminem. |

## <a name="az-spring-cloud-app-stop"></a>az primavera nuvem paragem

Pare as instâncias da aplicação.  Incumprimentos do ambiente de produção.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da aplicação.  Incumprimentos do ambiente de produção. |
| -- sem espera | Não espere que as operações de longo prazo terminem. |

## <a name="az-spring-cloud-app-update"></a>az atualização de aplicativos de nuvem de primavera

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
| --nome -n | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implementação existente da aplicação.  Incumprimentos do ambiente de produção. |
| --ativar o armazenamento persistente | O Boolean.  Se for verdade, monte um disco de 50GB com o caminho predefinido. |
| --env | Variáveis de ambiente separadas pelo espaço no formato 'chave[=valor]». |
| --é público | O Boolean.  Se for verdade, atribua um domínio público à aplicação. |
| --jvm-opções | Uma corda contendo opções JVM.  Utilize '=' em vez de ' ' para evitar erros de análise de conchas. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m` . |
| -- sem espera | Não espere que as operações de longo prazo terminem. |
| --versão do tempo de execução | Versão runtime do idioma utilizado na aplicação.  Valores permitidos: `Java_11` `Java_8` . . . |

### <a name="example"></a>Exemplo

Adicione uma variável ambiental para a aplicação.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>lista de ligação az-cloud app

Enumere todas as encadernações de serviço numa aplicação.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --app | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-binding-remove"></a>az aplicativo de mola-cloud remover

Remova uma ligação de serviço da aplicação.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --app | Nome da aplicação. |
| --nome | Nome da ligação de serviço a remover. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-binding-show"></a>az primavera-cloud app show de ligação

Mostre os detalhes de uma ligação de serviço.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --app | Nome da aplicação. |
| --nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az primavera nuvem ligação cosmos adicionar

Ligue um Azure Cosmos DB com a aplicação.

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
| --tipo api | Especifique o tipo API utilizando um dos seguintes valores: cassandra, gremlin, mongo, sql, tabela. |
| --app | Nome da aplicação. |
| --nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

|Parâmetros Opcionais | |
| --- | ---: |
| --nome de coleção | O nome da coleção.  Necessário para a utilização de Gremlin. |
| --nome da base de dados | O nome da base de dados.  Necessário para a utilização de Mongo, SQL e Gremlin. |
| --espaço-chave | Espaço-chave cassandra.  Necessário ao usar a Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az spring-cloud app ligando cosmos atualização

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
| --app | Nome da aplicação. |
| --nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

|Parâmetros Opcionais | |
| --- | ---: |
| --nome de coleção | O nome da coleção.  Necessário para a utilização de Gremlin. |
| --nome da base de dados | O nome da base de dados.  Necessário para a utilização de Mongo, SQL e Gremlin. |
| --espaço-chave | Espaço-chave cassandra.  Necessário ao usar a Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>app de mola-nuvem az ligando mysql adicionar

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
| --app | Nome da aplicação. |
| --nome da base de dados | O nome da base de dados. |
| ---chave | Chave da API do serviço. |
| --nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --id de recursos | Identificação de recurso Azure do serviço para se ligar. |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |
| --username | Nome de utilizador para acesso à base de dados. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>app az primavera-cloud ligando atualização mysql

Atualize a ligação de ligação de serviço à aplicação a uma Base de Dados Azure para o MySQL.

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
| --app | Nome da aplicação. |
| --nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --nome da base de dados | O nome da base de dados. |
| ---chave | Chave da API do serviço. |
| --username | Nome de utilizador para acesso à base de dados. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az aplicativo de mola-cloud ligação redis adicionar

Ligue um Cache Azure para redis com a aplicação.

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
| --app | Nome da aplicação. |
| --nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --id de recursos | Identificação de recursos Azure do serviço com o que pretende ligar. |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --desativação | Desativar os TLS. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az app de ligação de molas redis atualização

Atualize uma ligação de serviço para Azure Cache para Redis.

| Parâmetros Obrigatórios | |
| --- | ---: |
| --app | Nome da aplicação. |
| --nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --desativação | Desativar os TLS. |

## <a name="az-spring-cloud-app-deployment-create"></a>implementação de aplicativos az nuvem de primavera criar

Crie uma implementação de encenação para a aplicação.

Para implementar código ou atualizar as definições para uma implementação, utilização `az spring-cloud app deploy --deployment <staging-deployment>` ou atualização de aplicações de nuvem de mola az --implantação <staging deployment> .

| Parâmetros Obrigatórios | |
| --- | ---: |
| --app | Nome da aplicação. |
| --nome | Nome da ligação de serviço. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --cpu | Número de núcleos de CPU virtuais por exemplo.  Predefinição: 1 |
| --env | Variáveis de ambiente separadas pelo espaço no formato 'chave[=valor]». |
| --contagem de instâncias | Número de casos. Predefinição: 1. |
| --jar-caminho | Se for fornecido, desloque o jarro.  Caso contrário, insera a pasta atual como alcatrão. |
| --jvm-opções | Uma corda contendo opções JVM.  Utilize '=' em vez de ' ' para evitar erros de análise de conchas. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m` . |
| --memória | Número de GB de memória por exemplo. |
| -- sem espera | Não espere que as operações de longo prazo terminem. |
| --versão do tempo de execução | Versão runtime do idioma utilizado na aplicação.  Valores permitidos: `Java_11` `Java_8` . . . |
| --definições de clones de salto | Crie uma colocação de encenação clonando as definições atuais de implantação da produção. |
| --módulo-alvo | Módulo infantil a ser implantado.  Necessário quando vários pacotes de frascos são construídos a partir do código fonte. |
| -versão | Versão de implantação.  Inalterado se não estiver definido. |

### <a name="examples"></a>Exemplos

Implemente o código fonte para uma nova implementação da aplicação.  Isto irá embalar o diretório atual, construir um binário usando o Sistema de Construção Pivotal e, em seguida, implementar.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Implemente um frasco pré-construído para uma aplicação com opções JVM e variáveis ambientais.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>implementação de aplicativos az nuvem de primavera eliminar

Elimine uma implementação da aplicação.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --app | Nome da aplicação. |
| --nome | Nome da implantação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-deployment-list"></a>lista de implementação de aplicativos de nuvem de primavera az

Listar todas as implementações numa aplicação.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --app | Nome da aplicação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-deployment-show"></a>az primavera-cloud show de implementação de aplicativo

Mostrar detalhes de uma implantação.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --app | Nome da aplicação. |
| --nome | Nome da implantação. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --serviço-s | Nome da Nuvem de primavera de Azure.  Pode configurar o serviço predefinido utilizando `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-config-server-clear"></a>az mola-nuvem config-servidor claro

Apagar todas as definições de configuração no Servidor Config.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-config-server-set"></a>az mola-nuvem conjunto config-servidor

Definir definições de configuração no Servidor Config utilizando um ficheiro YAML.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --config-file | Caminho de arquivo para um manifesto YAML para a configuração do Servidor Config. |
| --nome | Nome da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| -- sem espera | Não durante muito tempo as operações de longo prazo estão concluídas.

## <a name="az-spring-cloud-config-server-show"></a>az primavera-nuvem config-servidor show

Mostrar as definições do Servidor Config.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-config-server-git-set"></a>az mola-nuvem config-servidor git conjunto

Desfie as propriedades do git para o Servidor Config.  Isto substituirá todas as propriedades de git existentes.

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
| --nome | Nome da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --uri | URI do config adicionado. |

| Parâmetros Opcionais | |
| --- | ---: |
| --adiar | Guarde temporariamente o objeto na cache local em vez de enviar para Azure.  Use `az cache` para ver /limpar. |
| ---chave de hospedeiro | Chave de anfitrião para o config adicionado. |
| --host-key-algorithm | Algoritmo chave de anfitrião para o config adicionado. |
| --rótulo | Etiqueta do config adicionado. |
| --password | Palavra-passe do config adicionado. |
| --chave privada | Chave privada do config adicionado. |
| --caminhos de pesquisa | Caminhos de pesquisa do config adicionado.  Use limonistas de vírgula para vários caminhos. |
| --estrita-host-key-checking | Permite uma verificação rigorosa da chave de anfitrião do config adicionado. |
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
| --nome | Nome da Nuvem de primavera de Azure. |
| --repo-nome | URI do repo. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| --uri | URI do config adicionado. |

| Parâmetros Opcionais | |
| --- | ---: |
| --adiar | Guarde temporariamente o objeto na cache local em vez de enviar para Azure.  Use `az cache` para ver /limpar. |
| ---chave de hospedeiro | Chave de anfitrião para o config adicionado. |
| --host-key-algorithm | Algoritmo chave de anfitrião para o config adicionado. |
| --rótulo | Etiqueta do config adicionado. |
| --password | Palavra-passe do config adicionado. |
| -padrão | Padrão para o repo.  Use limonistas de vírgula para vários caminhos.|
| --chave privada | Chave privada do config adicionado. |
| --caminhos de pesquisa | Caminhos de pesquisa do config adicionado.  Use limonistas de vírgula para vários caminhos. |
| --estrita-host-key-checking | Permite uma verificação rigorosa da chave de anfitrião do config adicionado. |
| --username | Nome de utilizador do config adicionado. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az primavera-cloud config-servidor git lista de repo

Listar todos os repos de git definidos no Servidor Config

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --adiar | Guarde temporariamente o objeto na cache local em vez de enviar para Azure.  Use `az cache` para ver /limpar. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az primavera-nuvem config-servidor git repo remover

Remova uma configuração de repo de git existente do Servidor Config.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem de primavera de Azure. |
| --repo-nome | URI do repo. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --adiar | Guarde temporariamente o objeto na cache local em vez de enviar para Azure.  Use `az cache` para ver /limpar. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az mola-nuvem teste-ponto final para desativar

Desativar o ponto final do teste da Nuvem de primavera de Azure

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az mola-nuvem-teste-ponto final permitir

Ativar o ponto final do teste para a Nuvem de primavera de Azure. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-test-endpoint-list"></a>lista az primavera-nuvem test-endpoint 

Lista as teclas de ponto final de teste disponíveis para a Nuvem de primavera Azure.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |

| Parâmetros Opcionais | |
| --- | ---: |
| --app | Nome da aplicação. |
| --implantação -d | Nome de uma implementação existente da aplicação.  Incumprimentos à produção se não for especificado. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az primavera-nuvem teste-fim-chave

Regenerar uma chave de ponta de teste para a Nuvem de primavera de Azure.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Parâmetros Obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem de primavera de Azure. |
| --grupo de recursos -g | Nome do grupo de recursos.  Pode configurar o grupo predefinido utilizando `az configure --defaults group=<name>` . |
| -tipo | Tipo de chave de ponto final de teste.  Valores permitidos: Primário, Secundário. |
