---
title: Configurar os contentores de cliente - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como configurar aplicações node. js a funcionar no serviço de aplicações do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 1e5faa8d356b891d825586414c0a1a1b9fa47090
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001886"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configurar um contentor personalizado do Linux para o serviço de aplicações do Azure

Este artigo mostra-lhe como configurar um contentor personalizado do Linux para executar no serviço de aplicações do Azure.

Este guia fornece os conceitos chave e instruções para contentores de aplicações do Linux no serviço de aplicações. Se nunca tiver utilizado o serviço de aplicações do Azure, siga os [guia de introdução do contentor personalizado](quickstart-docker-go.md) e [tutorial](tutorial-custom-docker-image.md) primeiro. Há também uma [início rápido para aplicações de vários contentores](quickstart-multi-container.md) e [tutorial](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Configurar o número de porta

O servidor web na sua imagem personalizada, pode utilizar uma porta diferente da 80. Indicar ao Auzre sobre a porta que utiliza o seu personalizado utilizando o `WEBSITES_PORT` definição de aplicação. A página do GitHub para o [exemplo de Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que tem de definir `WEBSITES_PORT` como _8000_. Pode configurá-lo, executando [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando no Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

Seu contentor personalizado, pode utilizar variáveis de ambiente que tem de ser fornecida externamente. Poderá passá-los no executando [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando no Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Este método funciona para aplicações de contentor único ou aplicações de vários contentores, onde as variáveis de ambiente estão especificadas na *docker-Compose* ficheiro.

## <a name="use-persistent-shared-storage"></a>Utilizar o armazenamento partilhado persistente

Pode utilizar o */home* diretório no sistema de ficheiros da sua aplicação para manter os ficheiros entre reinícios e partilhá-los entre instâncias. O `/home` na sua aplicação é fornecida para ativar a sua aplicação de contentor para aceder ao armazenamento persistente.

Quando armazenamento persistente está desabilitado, em seguida, escreve o `/home` diretório não são mantidos em reinícios da aplicação ou em várias instâncias. A única exceção é o `/home/LogFiles` diretório, que é utilizado para armazenar os registos de Docker e do contentor. Quando o armazenamento persistente estiver ativado, todas as escritas para o `/home` directory são mantidas e pode ser acedido por todas as instâncias de uma aplicação de escalamento horizontal.

Por predefinição, o armazenamento persistente é *desativada*. Para ativar ou desativá-lo, defina o `WEBSITES_ENABLE_APP_SERVICE_STORAGE` definição de aplicação executando [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando no Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> Também pode [configurar o seu próprio armazenamento persistente](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Ativar o SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Por ordem para um contentor personalizado suportar o SSH, tem de adicioná-lo para o Dockerfile em si.

> [!TIP]
> Todos os contentores de Linux incorporados adicionou as instruções do SSH em seus repositórios de imagem. Pode seguir as instruções seguintes com o [repositório de node. js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) para ver como está ativado lá.

- Utilize o [execute](https://docs.docker.com/engine/reference/builder/#run) instruções para instalar o servidor SSH e definir a palavra-passe da conta de raiz para `"Docker!"`. Por exemplo, para uma imagem com base numa [Alpine Linux](https://hub.docker.com/_/alpine), terá dos seguintes comandos:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Esta configuração não permite ligações externas ao contentor. SSH está disponível apenas através de `https://<app-name>.scm.azurewebsites.net` e autenticado com as credenciais de publicação.

- Adicione [neste ficheiro sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) para o repositório de imagens e use o [cópia](https://docs.docker.com/engine/reference/builder/#copy) instruções para copiar o ficheiro para o */etc/ssh/* diretório. Para obter mais informações sobre *sshd_config* arquivos, consulte [OpenBSD documentação](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O ficheiro *sshd_config* tem de incluir os itens seguintes:
    > - `Ciphers` tem de incluir, pelo menos, um item na lista `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` tem de incluir, pelo menos, um item na lista `hmac-sha1,hmac-sha1-96`.

- Utilize o [expor](https://docs.docker.com/engine/reference/builder/#expose) instruções para abrir a porta 2222 no contentor. Embora a palavra-passe de raiz é conhecida, porta 2222 não está acessível a partir da internet. É acessível apenas por contêineres dentro da rede de ponte de uma rede virtual privada.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- No script de arranque para o seu contentor, inicie o servidor SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Por exemplo, veja como a predefinição [node. js 10.14 contentor](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) inicia o servidor SSH.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurar aplicações de vários contentores

- [Utilizar o armazenamento persistente no Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitações de pré-visualização](#preview-limitations)
- [Opções do docker Compose](#docker-compose-options)
- [Opções de configuração do Kubernetes](#kubernetes-configuration-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Utilizar o armazenamento persistente no Docker Compose

Aplicações de vários contentores, como o WordPress precisam de armazenamento persistente para funcionar corretamente. Para ativá-la, a configuração do Docker Compose tem de apontar para uma localização de armazenamento *fora* seu contentor. Localizações de armazenamento no interior do contentor não mantêm as alterações para além de reiniciar a aplicação.

Ativar o armazenamento persistente, definindo a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplicação configuração, utilizando o [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando no Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

No seu *docker-Compose* de arquivos, mapear os `volumes` a opção de `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` é uma variável de ambiente no Serviço de Aplicações, que está mapeado para um armazenamento persistente para a sua aplicação. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="use-custom-storage-in-docker-compose"></a>Utilizar o armazenamento personalizado no Docker Compose

O armazenamento do Azure (ficheiros do Azure ou BLOBs do Azure) pode ser montado aplicações de vários contentores com o id de personalizada. Para ver o nome do id de personalizada, execute [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

No seu *docker-Compose* de arquivos, mapear os `volumes` a opção de `custom-id`. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

### <a name="preview-limitations"></a>Limitações de pré-visualização

Com vários contentores estão atualmente em pré-visualização. As seguintes funcionalidades de plataforma do serviço de aplicações não são suportadas:

- Autenticação/Autorização
- Identidades Geridas

### <a name="docker-compose-options"></a>Opções do docker Compose

As listas seguintes mostram suportadas e não suportadas Docker Compose as opções de configuração:

#### <a name="supported-options"></a>Opções suportadas

- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Opções não suportadas

- build (não é permitida)
- depends_on (ignorada)
- networks (ignorada)
- secrets (ignorada)
- portas que não seja a 80 e 8080 (ignorados)

> [!NOTE]
> Quaisquer outras opções não explicitamente chamadas são ignoradas em pré-visualização pública.

### <a name="kubernetes-configuration-options"></a>Opções de configuração do Kubernetes

As seguintes opções de configuração são suportadas para Kubernetes:

- args
- command
- containers
- image
- nome
- ports
- spec

> [!NOTE]
> Quaisquer outras opções não explicitamente chamadas não são suportadas em pré-visualização pública.
>

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implemente a partir do repositório de contentor privado](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Tutorial: Aplicação do WordPress com vários contentores](tutorial-multi-container-app.md)
