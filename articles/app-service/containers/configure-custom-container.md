---
title: Configure um recipiente linux personalizado
description: Saiba como configurar um recipiente Linux personalizado no Serviço de Aplicações Azure. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373878"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configure um recipiente linux personalizado para o Serviço de Aplicações Azure

Este artigo mostra-lhe como configurar um recipiente Linux personalizado para funcionar no Serviço de Aplicações Azure.

Este guia fornece conceitos e instruções fundamentais para a contentorização de aplicações Linux no Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [recipiente personalizado](quickstart-docker-go.md) e o [tutorial.](tutorial-custom-docker-image.md) Há também uma [aplicação multi-contentores quickstart](quickstart-multi-container.md) e [tutorial.](tutorial-multi-container-app.md)

## <a name="configure-port-number"></a>Configurar o número da porta

O servidor web na sua imagem personalizada pode utilizar uma porta diferente de 80. Informe o Azure sobre a porta que o seu recipiente personalizado utiliza utilizando a definição de aplicações `WEBSITES_PORT`. A página do GitHub para o [exemplo de Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que tem de definir `WEBSITES_PORT` como _8000_. Pode defini-lo executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando na Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

O seu recipiente personalizado pode utilizar variáveis ambientais que precisam de ser fornecidas externamente. Pode passá-los correndo [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando na Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Este método funciona tanto para aplicações de um único contentor como para aplicações multi-contentores, onde as variáveis ambientais são especificadas no ficheiro *docker-compose.yml.*

## <a name="use-persistent-shared-storage"></a>Use armazenamento partilhado persistente

Pode utilizar o diretório */home* no sistema de ficheiros da sua aplicação para persistir ficheiros em reinícios e partilhá-los em instâncias. O `/home` na sua aplicação é fornecido para permitir que a sua aplicação de contentores aceda ao armazenamento persistente.

Quando o armazenamento persistente é desativado, então escreve para o diretório `/home` não são persistidos em reinícios de aplicações ou em vários casos. A única exceção é o diretório `/home/LogFiles`, que é usado para armazenar os registos do Docker e dos contentores. Quando o armazenamento persistente é ativado, todos os escritos para o diretório `/home` são persistidos e podem ser acedidos por todos os casos de uma aplicação de escala.

Por defeito, o armazenamento persistente está *ativado* e a definição não está exposta nas Definições de Aplicação. Para desative-o, defina a definição de aplicações `WEBSITES_ENABLE_APP_SERVICE_STORAGE` executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando na Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Também pode [configurar o seu próprio armazenamento persistente.](how-to-serve-content-from-azure-storage.md)

## <a name="enable-ssh"></a>Ativar o SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Para obter um recipiente personalizado para suportar o SSH, deve adicioná-lo ao próprio Dockerfile.

> [!TIP]
> Todos os recipientes linux incorporados adicionaram as instruções de SSH nos seus repositórios de imagem. Pode seguir as seguintes instruções com o [repositório Nó.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) para ver como é ativado lá.

- Utilize a instrução [RUN](https://docs.docker.com/engine/reference/builder/#run) para instalar o servidor SSH e definir a palavra-passe para a conta raiz para `"Docker!"`. Por exemplo, para uma imagem baseada no [Alpine Linux,](https://hub.docker.com/_/alpine)precisa dos seguintes comandos:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Esta configuração não permite ligações externas ao recipiente. O SSH só está disponível através de `https://<app-name>.scm.azurewebsites.net` e autenticado com as credenciais de publicação.

- Adicione [este ficheiro sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) ao seu repositório de imagem e utilize a instrução [COPY](https://docs.docker.com/engine/reference/builder/#copy) para copiar o ficheiro para o */etc/ssh/diretório.* Para obter mais informações sobre *sshd_config* ficheiros, consulte [a documentação OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O ficheiro *sshd_config* tem de incluir os itens seguintes:
    > - `Ciphers` tem de incluir, pelo menos, um item na lista `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` tem de incluir, pelo menos, um item na lista `hmac-sha1,hmac-sha1-96`.

- Utilize as instruções [DEPOSIÇÃO](https://docs.docker.com/engine/reference/builder/#expose) para abrir a porta 2222 no recipiente. Embora a palavra-passe de raiz seja conhecida, a porta 2222 é inacessível a partir da internet. É acessível apenas por contentores dentro da rede de pontes de uma rede virtual privada.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- No script de arranque do seu recipiente, ligue o servidor SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Por exemplo, veja como o recipiente padrão [Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) inicia o servidor SSH.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configure aplicativos multi-contentores

- [Use armazenamento persistente em Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitações de pré-visualização](#preview-limitations)
- [Opções de Composição De Docker](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Use armazenamento persistente em Docker Compose

Aplicações multi-contentores como o WordPress precisam de armazenamento persistente para funcionar corretamente. Para o ativar, a configuração do Docker Compose deve apontar para um local de armazenamento *fora* do seu recipiente. Os locais de armazenamento dentro do seu contentor não persistem alterações para além do reinício da aplicação.

Ative o armazenamento persistente definindo a definição de aplicações `WEBSITES_ENABLE_APP_SERVICE_STORAGE`, utilizando as definições de definição de definições de config da [Webapp az](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) na Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

No seu ficheiro *docker-compose.yml,* mapeie a opção `volumes` para `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` é uma variável de ambiente no Serviço de Aplicações, que está mapeado para um armazenamento persistente para a sua aplicação. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limitações de pré-visualização

O multi-contentor encontra-se atualmente em pré-visualização. As seguintes funcionalidades da plataforma app service não são suportadas:

- Autenticação / Autorização
- Identidades Geridas

### <a name="docker-compose-options"></a>Opções de Composição De Docker

As seguintes listas mostram opções de configuração de Docker Compor suportadas e não apoiadas:

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
- portos que não 80 e 8080 (ignorados)

> [!NOTE]
> Quaisquer outras opções não explicitamente chamadas são ignoradas na Pré-Visualização Pública.

## <a name="configure-vnet-integration"></a>Configure integração VNet

A utilização de um recipiente personalizado com integração VNet pode requerer uma configuração adicional do recipiente. Ver [Integrar a sua aplicação com uma Rede Virtual Azure.](../web-sites-integrate-with-vnet.md)

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implantação do repositório de contentores privados](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Tutorial: Aplicação WordPress multi-contentor](tutorial-multi-container-app.md)
