---
title: Executar exemplo de recipiente de estivador executar comando
titleSuffix: Azure Cognitive Services
description: Estivar executar comando para recipiente de saúde
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: af8fec56c32b52e2af584e59f08db6cc7129c9c5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947276"
---
## <a name="install-the-container"></a>Instale o recipiente

Existem várias formas de instalar e executar o Text Analytics para recipiente de saúde. 

- Utilize o [portal Azure](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) para criar um recurso Text Analytics e use o Docker para obter o seu recipiente.
- Utilize os seguintes scripts PowerShell e Azure CLI para automatizar a implementação de recursos e a configuração do contentor.

### <a name="run-the-container-locally"></a>Executar o contentor localmente

Para executar o recipiente no seu próprio ambiente depois de descarregar a imagem do recipiente, encontre o seu ID de imagem:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Execute o seguinte `docker run` comando. Substitua os espaços reservados abaixo pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave para o seu recurso Text Analytics. Pode encontrá-lo na página **chave e ponta final** do seu recurso, no portal Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O ponto final para aceder à API de Análise de Texto. Pode encontrá-lo na página **chave e ponta final** do seu recurso, no portal Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | A identificação da imagem do seu recipiente. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | O diretório de entrada para o contentor. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Este comando:

- Assume que o diretório de entrada existe na máquina de anfitrião
- Executa um texto analytics para saúde recipiente a partir da imagem do recipiente
- Atribui 6 núcleos de CPU e 12 gigabytes (GB) de memória
- Expõe a porta TCP 5000 e atribui uma pseudo-TTY para o contentor
- Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

### <a name="demo-ui-to-visualize-output"></a>Demo UI para visualizar a saída

> [!NOTE]
> A demonstração só está disponível com o Text Analytics para recipiente de saúde.

O contentor fornece APIs de ponto final de predição de consulta com base em REST.  Também fornecemos uma ferramenta de visualização no recipiente que é acessível por aparpência `/demo` ao ponto final do recipiente. Por exemplo:

```
http://<serverURL>:5000/demo
```

Utilize o pedido de cURL de exemplo abaixo para submeter uma consulta ao recipiente que implementou substituindo a `serverURL` variável pelo valor adequado.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Instale o recipiente utilizando a Azure Web App para contentores

A azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) é um recurso Azure dedicado a executar contentores na nuvem. Traz capacidades fora da caixa, tais como autoscaling, suporte de contentores estivadores e composição de estiva, suporte HTTPS e muito mais.

> [!NOTE]
> Utilizando a App Web Azure, obterá automaticamente um domínio sob a forma de `<appservice_name>.azurewebsites.net`

Execute este script PowerShell utilizando o CLI Azure para criar uma Aplicação Web para Contentores, utilizando a sua subscrição e a imagem do recipiente em HTTPS. Aguarde que o script esteja completo (aproximadamente 25-30 minutos) antes de submeter o primeiro pedido.

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Instale o recipiente utilizando a instância do contentor Azure

Também pode utilizar uma Instância de Contentores Azure (ACI) para facilitar a implantação. O ACI é um recurso que permite executar contentores Docker a pedido num ambiente Azure gerido e sem servidor. 

Veja [como utilizar as instâncias do contentor Azure](../../containers/azure-container-instance-recipe.md) para etapas na implantação de um recurso ACI utilizando o portal Azure. Também pode utilizar o script abaixo do PowerShell utilizando o Azure CLI, que criará um ACI na sua subscrição utilizando a imagem do recipiente.  Aguarde que o script esteja completo (aproximadamente 25-30 minutos) antes de submeter o primeiro pedido.  Devido ao limite do número máximo de CPUs por recurso ACI, não selecione esta opção se espera submeter mais de 5 documentos grandes (aproximadamente 5000 caracteres cada) por pedido.
Consulte o artigo de apoio regional da [ACI](../../../container-instances/container-instances-region-availability.md) para obter informações sobre a disponibilidade. 

> [!NOTE] 
> As instâncias do recipiente Azure não incluem suporte HTTPS para os domínios construídos. Se precisar de HTTPS, terá de o configurar manualmente, incluindo a criação de um certificado e o registo de um domínio. Pode encontrar instruções para o fazer com o NGINX abaixo.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Conectividade ACI segura

Por predefinição, não existe qualquer garantia quando se utiliza a ACI com API de contentores. Isto porque normalmente os contentores funcionam como parte de uma cápsula que é protegida do exterior por uma ponte de rede. No entanto, pode modificar um recipiente com um componente frontal, mantendo o ponto final do recipiente em privado. Os exemplos a seguir usam [o NGINX](https://www.nginx.com) como porta de entrada para suportar a autenticação HTTPS/SSL e o certificado de cliente.

> [!NOTE]
> NGINX é um servidor HTTP de código aberto e de alto desempenho e procuração. Um recipiente NGINX pode ser utilizado para pôr termo a uma ligação TLS para um único recipiente. São também possíveis soluções de rescisão TLS baseadas em ingresss em NGINX mais complexas.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Configurar o NGINX como um portal de entrada

O NGINX utiliza [ficheiros de configuração](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) para ativar funcionalidades em tempo de execução. Para permitir a rescisão de TLS para outro serviço, deve especificar um certificado SSL para encerrar a ligação TLS e  `proxy_pass` especificar um endereço para o serviço. Uma amostra é fornecida abaixo.


> [!NOTE]
> `ssl_certificate` espera que um caminho seja especificado dentro do sistema de ficheiros local do contentor NGINX. O endereço especificado `proxy_pass` deve estar disponível na rede do contentor NGINX.

O recipiente NGINX carregará todos os ficheiros no `_.conf_` que são montados `/etc/nginx/conf.d/` no caminho de configuração HTTP.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Exemplo Docker compor arquivo

O exemplo abaixo mostra como um ficheiro [de composição de estiva](https://docs.docker.com/compose/reference/overview) pode ser criado para implantar o NGINX e o Text Analytics para recipientes de saúde:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Para iniciar este ficheiro de composição do Docker, execute o seguinte comando a partir de uma consola ao nível da raiz do ficheiro:

```bash
docker-compose up
```

Para mais informações, consulte a documentação da NGINX sobre a rescisão do [NGINX SSL](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).