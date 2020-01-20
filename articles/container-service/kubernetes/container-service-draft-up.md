---
title: PRETERIDO Usar o rascunho com o serviço de contêiner do Azure e o registro de contêiner do Azure
description: Crie um cluster do Kubernetes no ACS e um Azure Container Registry para criar a sua primeira aplicação no Azure com o Draft.
author: squillace
ms.service: container-service
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 8d688d2918c9100019d033e93e9a3dca9e492de2
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271139"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>PRETERIDO Usar o rascunho com o serviço de contêiner do Azure e o registro de contêiner do Azure para compilar e implantar um aplicativo no kubernetes

> [!TIP]
> Para obter a versão atualizada deste artigo que usa o serviço kubernetes do Azure, consulte [usar o rascunho com o AKs (serviço kubernetes do Azure)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

O [Draft](https://aka.ms/draft) é uma nova ferramenta de código aberto que permite programar facilmente aplicações baseadas em contentores e implementá-las em clusters do Kubernetes sem que sejam precisos grandes conhecimentos de Docker e Kubernetes -- ou inclusivamente instalá-las. A utilização de ferramentas como o Draft permite-lhe a si e às suas equipas concentrarem-se na criação da aplicação com o Kubernetes em vez de dedicarem tanta atenção à infraestrutura.

Pode utilizar o Draft com qualquer registo de imagens do Docker e com qualquer cluster do Kubernetes, incluindo no local. Este tutorial mostra como usar o ACS com kubernetes e ACR para criar um pipeline de desenvolvedor dinâmico, mas seguro, no kubernetes usando o rascunho e como usar o DNS do Azure para expor esse pipeline de desenvolvedor para que outras pessoas vejam em um domínio.


## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry
Pode [criar um Azure Container Registry ](../../container-registry/container-registry-get-started-azure-cli.md) facilmente, mas os passos são os seguintes:

1. Crie um grupo de recursos do Azure para gerenciar seu registro ACR e o cluster kubernetes no ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Crie um registro de imagem ACR usando [AZ ACR Create](/cli/azure/acr#az-acr-create) e verifique se a opção `--admin-enabled` está definida como `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Criar um Azure Container Service com o Kubernetes

Agora, está pronto para utilizar [az acs create](/cli/azure/acs#az-acs-create) para criar um cluster do ACS tendo o Kubernetes como o valor de `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Uma vez que o Kubernetes não é o tipo de orquestrador predefinido, confirme que utiliza o comutador `--orchestrator-type kubernetes`.

Quando bem-sucedida, a saída é semelhante ao seguinte.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Uma vez que já tem um cluster, pode utilizar o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) para importar as credenciais. Agora, tem um ficheiro de configuração local para o seu cluster, de que o Helm e o Draft precisam para fazer o seu trabalho.

## <a name="install-and-configure-draft"></a>Instalar e configurar o Draft


1. Baixe o rascunho do seu ambiente em https://github.com/Azure/draft/releases e instale-o em seu caminho para que o comando possa ser usado.
2. Baixe o Helm para seu ambiente em https://github.com/kubernetes/helm/releases e [Instale-o em seu caminho para que o comando possa ser usado](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Configure o Draft para utilizar o seu registo e criar subdomínios para cada gráfico do Helm que crie. Para configurar o Draft, precisa:
   - Do nome do Azure Container Registry (neste exemplo, `draftacsdemo`)
   - Da chave de registo, ou palavra-passe, de `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`

   Chame `draft init` e o processo de configuração solicitará os valores acima; Observe que o formato da URL para a URL do registro é o nome do registro (neste exemplo, `draftacsdemo`) mais `.azurecr.io`. Seu nome de usuário é o nome do registro por conta própria. Da primeira vez que o executa, o processo é parecido com o seguinte.
   ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
   ```

Está agora pronto para implementar uma aplicação.


## <a name="build-and-deploy-an-application"></a>Criar e implementar uma aplicação

O repositório do Draft inclui [seis aplicações de exemplo simples](https://github.com/Azure/draft/tree/master/examples). Clone o repositório e vamos usar o [exemplo de Java](https://github.com/Azure/draft/tree/master/examples/example-java). Altere para o diretório de exemplos/Java e digite `draft create` para compilar o aplicativo. Deve ser semelhante ao exemplo seguinte.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

A saída inclui um Dockerfile e um gráfico do Helm. Para criar e implementar, basta escrever `draft up`. A saída é extensa, mas deve ser semelhante ao exemplo a seguir.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Exibir seu aplicativo com segurança

O contêiner agora está em execução no ACS. Para exibi-lo, use o comando `draft connect`, que cria uma conexão segura com o IP do cluster com uma porta específica para seu aplicativo para que você possa exibi-lo localmente. Se for bem-sucedido, procure a URL para se conectar ao seu aplicativo na primeira linha após o indicador de **êxito** .

> [!NOTE]
> Se você receber uma mensagem informando que nenhum pods esteve pronto, aguarde um momento e tente novamente, ou você pode assistir ao pods que fica pronto com `kubectl get pods -w` e, em seguida, tentar novamente quando o fizer.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

No exemplo anterior, você pode digitar `curl -s http://localhost:46143` para receber a resposta `Hello World, I'm Java!`. Quando você CTRL + ou CMD + C (dependendo do seu ambiente de so), o túnel seguro é interrompido e você pode continuar Iterando.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Compartilhando seu aplicativo Configurando um domínio de implantação com o DNS do Azure

Você já realizou o loop de iteração do desenvolvedor que o rascunho cria nas etapas anteriores. No entanto, você pode compartilhar seu aplicativo pela Internet:
1. Instalando uma entrada em seu cluster do ACS (para fornecer um endereço IP público no qual exibir o aplicativo)
2. Delegar seu domínio personalizado ao DNS do Azure e mapear seu domínio para o endereço IP que o ACS atribui ao controlador de entrada

### <a name="use-helm-to-install-the-ingress-controller"></a>Use Helm para instalar o controlador de entrada.
Use **Helm** para procurar e instalar `stable/traefik`, um controlador de entrada, para habilitar solicitações de entrada para suas compilações.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Agora, defina um observador no controlador `ingress` para capturar o valor do IP externo quando for implementado. Esse endereço IP será mapeado para o domínio de implantação na próxima seção.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

Neste caso, o IP externo do domínio de implementação é `13.64.108.240`. Já pode mapear o seu domínio para este IP.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mapear o IP de entrada para um subdomínio personalizado

O Draft cria uma versão para cada gráfico do Helm que cria -- cada aplicação em que está a trabalhar. Cada um recebe um nome gerado que é usado pelo **rascunho** como um _subdomínio_ sobre o _domínio de implantação_ raiz que você controla. (Neste exemplo, usamos `squillace.io` como o domínio de implantação.) Para habilitar esse comportamento de subdomínio, você deve criar um registro A para `'*.draft'` em suas entradas DNS para seu domínio de implantação, para que cada subdomínio gerado seja roteado para o controlador de entrada do cluster kubernetes. 

O seu fornecedor de domínio tem a sua própria forma de atribuir servidores DNS; para [delegar os seus servidores de nomes para o DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md), tem de seguir os passos abaixo:

1. Crie um grupo de recursos para a sua zona.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Crie uma zona DNS para o seu domínio.
   Utilize o comando [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) para obter os servidores de nomes e delegar o controlo de DNS para o DNS do Azure no seu domínio.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Adicione os servidores DNS que lhe forem dados ao fornecedor do seu domínio de implementação, o que lhe vai permitir utilizar o DNS do Azure para reencaminhar o seu domínio como quiser. A maneira como você faz isso varia de acordo com o domínio fornecido; [delegue seus nameservers de domínio para o DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md) contém alguns dos detalhes que você deve saber. 
4. Depois que o domínio tiver sido delegado ao DNS do Azure, crie uma entrada de conjunto de registros para o mapeamento de domínio de implantação para o `ingress` IP da etapa 2 da seção anterior.
   ```azurecli
   az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
   ```
   A saída é semelhante ao seguinte.
   ```json
   {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
   }
   ```
5. Reinstalar o **rascunho**

   1. Remova o **rascunho** do cluster digitando `helm delete --purge draft`. 
   2. Reinstale o **rascunho** usando o mesmo comando `draft-init`, mas com a opção `--ingress-enabled`:
      ```bash
      draft init --ingress-enabled
      ```
      Responda às solicitações como você fez na primeira vez, acima. No entanto, você tem mais uma pergunta para responder, usando o caminho de domínio completo que você configurou com o DNS do Azure.

6. Insira seu domínio de nível superior para entrada (por exemplo, draft.example.com): draft.squillace.io
7. Ao chamar `draft up` desta vez, você poderá ver seu aplicativo (ou `curl`-lo) na URL do formulário `<appname>.draft.<domain>.<top-level-domain>`. No caso deste exemplo, `http://handy-labradoodle.draft.squillace.io`. 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Passos seguintes

Agora que já tem um cluster do Kubernetes no ACS, pode investigar com o [Azure Container Registry](../../container-registry/container-registry-intro.md) para criar mais e diferentes implementações deste cenário. Por exemplo, pode criar um conjunto de registos DNS de domínio draft._basedomain.toplevel_ que controle aspetos de subdomínios mais profundos para implementações do ACS específicas.






