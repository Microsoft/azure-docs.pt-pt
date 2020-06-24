---
title: Configure um recipiente Linux personalizado
description: Saiba como configurar um recipiente Linux personalizado no Azure App Service. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 57281bedb34078dff6878d69be1bfe7f7300f545
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905804"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configure um recipiente Linux personalizado para o Serviço de Aplicações Azure

Este artigo mostra-lhe como configurar um recipiente Linux personalizado para funcionar no Azure App Service.

Este guia fornece conceitos e instruções fundamentais para a contentorização de aplicações Linux no Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [quickstart](quickstart-docker-go.md) e [tutorial](tutorial-custom-docker-image.md) do recipiente personalizado. Há também um quickstart e tutorial [de aplicativo multi-contentores.](quickstart-multi-container.md) [tutorial](tutorial-multi-container-app.md)

## <a name="configure-port-number"></a>Configure o número da porta

O servidor web na sua imagem personalizada pode usar uma porta que não seja 80. Informe o Azure sobre a porta que o seu recipiente personalizado utiliza utilizando a definição da `WEBSITES_PORT` aplicação. A página do GitHub para o [exemplo de Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que tem de definir `WEBSITES_PORT` como _8000_. Pode defini-lo executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando na Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

O seu recipiente personalizado pode utilizar variáveis ambientais que precisam de ser fornecidas externamente. Pode passá-los executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando na Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Este método funciona tanto para aplicações de contentores individuais como para aplicações multi-contentores, onde as variáveis ambientais são especificadas no ficheiro *docker-compose.yml.*

## <a name="use-persistent-shared-storage"></a>Use armazenamento partilhado persistente

Pode utilizar o *diretório /home* no sistema de ficheiros da sua aplicação para persistir ficheiros em todo o recomeço e partilhá-los em todos os casos. A `/home` aplicação na sua aplicação é fornecida para permitir que a sua aplicação de contentores tenha acesso a um armazenamento persistente.

Quando o armazenamento persistente é desativado, então escreve para o `/home` diretório não são persistidos em recomeçar apps ou em vários casos. A única exceção é o `/home/LogFiles` diretório, que é usado para armazenar o Docker e os troncos de contentores. Quando o armazenamento persistente é ativado, todos os escritos para o `/home` diretório são persistidos e podem ser acedidos por todos os casos de uma aplicação de escala.

Por predefinição, o armazenamento persistente é *ativado* e a definição não está exposta nas Definições de Aplicação. Para desativá-la, defina a definição da `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplicação executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando na Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Também pode [configurar o seu próprio armazenamento persistente.](how-to-serve-content-from-azure-storage.md)

## <a name="enable-ssh"></a>Ativar SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Para que um recipiente personalizado suporte o SSH, deve adicioná-lo ao próprio Dockerfile.

> [!TIP]
> Todos os recipientes Linux incorporados adicionaram as instruções do SSH nos seus repositórios de imagem. Pode consultar as seguintes instruções com o [repositórioNode.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) para ver como está ativado.

- Utilize as instruções [RUN](https://docs.docker.com/engine/reference/builder/#run) para instalar o servidor SSH e definir a palavra-passe para a conta raiz para `"Docker!"` . Por exemplo, para uma imagem baseada em [Alpine Linux,](https://hub.docker.com/_/alpine)precisa dos seguintes comandos:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Esta configuração não permite ligações externas ao recipiente. O SSH só está disponível `https://<app-name>.scm.azurewebsites.net` através e autenticado com as credenciais de publicação.

- Adicione [este ficheiro sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) ao seu repositório de imagem e utilize a instrução [COPY](https://docs.docker.com/engine/reference/builder/#copy) para copiar o ficheiro para o */etc/ssh/diretório.* Para obter mais informações sobre *sshd_config* ficheiros, consulte [a documentação do OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O ficheiro *sshd_config* tem de incluir os itens seguintes:
    > - `Ciphers` tem de incluir, pelo menos, um item na lista `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` tem de incluir, pelo menos, um item na lista `hmac-sha1,hmac-sha1-96`.

- Utilize as instruções [EXPOR](https://docs.docker.com/engine/reference/builder/#expose) para abrir a porta 2222 no recipiente. Embora a palavra-passe de raiz seja conhecida, a porta 2222 é inacessível a partir da internet. É acessível apenas por contentores dentro da rede de pontes de uma rede virtual privada.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- No script de arranque do seu recipiente, inicie o servidor SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Por exemplo, veja como o [recipiente padrãoNode.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) começa o servidor SSH.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configure aplicativos multi-contentores

- [Use armazenamento persistente em Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitações de pré-visualização](#preview-limitations)
- [Opções de Composição de Docker](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Use armazenamento persistente em Docker Compose

Aplicações multi-contentores como o WordPress precisam de armazenamento persistente para funcionar corretamente. Para o ativar, a sua configuração Docker Compose deve indicar um local de armazenamento *fora do* seu contentor. Os locais de armazenamento dentro do seu contentor não persistem alterações para além do reinício da aplicação.

Permitir o armazenamento persistente definindo a definição da `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplicação, utilizando o comando de configuração de [appsettings az webapp](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) em Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

No seu ficheiro *docker-compose.yml,* mapear a `volumes` opção para `${WEBAPP_STORAGE_HOME}` . 

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

O multi-contentor está atualmente em pré-visualização. As seguintes funcionalidades da plataforma do Serviço de Aplicações não são suportadas:

- Autenticação / Autorização
- Identidades geridas

### <a name="docker-compose-options"></a>Opções de Composição de Docker

As seguintes listas mostram opções de configuração de Composição de Docker suportadas e não suportadas:

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
- portas que não 80 e 8080 (ignoradas)

> [!NOTE]
> Quaisquer outras opções não explicitamente chamadas são ignoradas na Visualização Pública.

## <a name="configure-vnet-integration"></a>Configurar a integração VNet

A utilização de um recipiente personalizado com integração VNet pode requerer uma configuração adicional do recipiente. Ver [Integrar a sua aplicação com uma Rede Virtual Azure.](../web-sites-integrate-with-vnet.md)

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implantação do repositório de contentores privados](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Tutorial: App WordPress multi-contentor](tutorial-multi-container-app.md)
