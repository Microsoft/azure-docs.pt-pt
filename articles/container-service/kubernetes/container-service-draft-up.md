---
title: (PRECADO) Utilizar rascunho com serviço de contentores Azure e registo de contentores Azure
description: Crie um cluster do Kubernetes no ACS e um Azure Container Registry para criar a sua primeira aplicação no Azure com o Draft.
author: squillace
ms.service: container-service
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 039879cb725c20af3c40698947ea9abe189d08ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82731768"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(PRECADO) Utilize o Projeto com o Serviço de Contentores Azure e o Registo de Contentores Azure para construir e implementar uma aplicação na Kubernetes

> [!TIP]
> Para a versão atualizada, este artigo que utiliza o Serviço Azure Kubernetes, consulte [Use Draft com o Serviço Azure Kubernetes (AKS)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

O [Draft](https://aka.ms/draft) é uma nova ferramenta de código aberto que permite programar facilmente aplicações baseadas em contentores e implementá-las em clusters do Kubernetes sem que sejam precisos grandes conhecimentos de Docker e Kubernetes -- ou inclusivamente instalá-las. A utilização de ferramentas como o Draft permite-lhe a si e às suas equipas concentrarem-se na criação da aplicação com o Kubernetes em vez de dedicarem tanta atenção à infraestrutura.

Pode utilizar o Draft com qualquer registo de imagens do Docker e com qualquer cluster do Kubernetes, incluindo no local. Este tutorial mostra como usar ACS com Kubernetes e ACR para criar um pipeline de desenvolvimento vivo mas seguro em Kubernetes usando o Draft, e como usar o Azure DNS para expor esse pipeline de desenvolvedor para outros verem num domínio.


## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry
Pode [criar um Azure Container Registry ](../../container-registry/container-registry-get-started-azure-cli.md) facilmente, mas os passos são os seguintes:

1. Crie um grupo de recursos Azure para gerir o seu registo ACR e o cluster Kubernetes em ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Crie um registo de imagem ACR utilizando [a az acr criar](/cli/azure/acr#az-acr-create) e certifique-se de que a `--admin-enabled` opção está definida para `true` .
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


1. Faça o download do rascunho para o seu ambiente https://github.com/Azure/draft/releases e instale no seu PATH para que o comando possa ser utilizado.
2. Baixe o leme para o seu ambiente https://github.com/kubernetes/helm/releases e [instale-o no seu PATH para que o comando possa ser utilizado](https://helm.sh/docs/intro/quickstart#install-helm).
3. Configure o Draft para utilizar o seu registo e criar subdomínios para cada gráfico do Helm que crie. Para configurar o Draft, precisa:
   - Do nome do Azure Container Registry (neste exemplo, `draftacsdemo`)
   - Da chave de registo, ou palavra-passe, de `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`

   A chamada `draft init` e o processo de configuração solicitam-lhe os valores acima; note que o formato URL para o URL de registo é o nome de registo (neste exemplo, ) mais `draftacsdemo` `.azurecr.io` . O seu nome de utilizador é o nome de registo por si só. Da primeira vez que o executa, o processo é parecido com o seguinte.
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

O repositório do Draft inclui [seis aplicações de exemplo simples](https://github.com/Azure/draft/tree/master/examples). Clone o repo e vamos usar o [exemplo java.](https://github.com/Azure/draft/tree/master/examples/example-java) Mude para os exemplos/diretório java, e escreva `draft create` para construir a aplicação. Deve ser semelhante ao exemplo seguinte.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

A saída inclui um Dockerfile e um gráfico do Helm. Para criar e implementar, basta escrever `draft up`. A saída é extensa, mas deve ser como o exemplo seguinte.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Veja de forma segura a sua aplicação

O seu contentor está agora em FUNCIONA. Para vê-lo, utilize o `draft connect` comando, que cria uma ligação segura ao IP do cluster com uma porta específica para a sua aplicação para que possa vê-lo localmente. Se for bem sucedido, procure que o URL se conecte à sua aplicação na primeira linha após o indicador **SUCESSO.**

> [!NOTE]
> Se receber uma mensagem a dizer que não há cápsulas prontas, aguarde um momento e relemisse, ou pode ver as cápsulas a ficarem prontas `kubectl get pods -w` e depois tentar novamente quando o fizerem.

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

No exemplo anterior, pode escrever `curl -s http://localhost:46143` para receber a resposta, `Hello World, I'm Java!` . Quando você CTRL+ ou CMD+C (dependendo do ambiente do seu SO), o túnel seguro é demolido e pode continuar a iterar.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Partilhar a sua aplicação configurando um domínio de implementação com o Azure DNS

Já executou o ciclo de iteração do desenvolvedor que o Draft cria nos passos anteriores. No entanto, pode partilhar a sua aplicação através da internet através de:
1. Instalação de uma entrada no seu cluster ACS (para fornecer um endereço IP público para exibir a aplicação)
2. Delegando o seu domínio personalizado para Azure DNS e mapeando o seu domínio para o endereço IP ACS atribui ao seu controlador de entrada

### <a name="use-helm-to-install-the-ingress-controller"></a>Utilize o leme para instalar o controlador de entrada.
Utilize **o leme** para procurar e instalar , um controlador de `stable/traefik` entrada, para ativar pedidos de entrada para as suas construções.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Agora, defina um observador no controlador `ingress` para capturar o valor do IP externo quando for implementado. Este endereço IP é o endereço que vai ser mapeado para o seu domínio de implementação na secção seguinte.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

Neste caso, o IP externo do domínio de implementação é `13.64.108.240`. Já pode mapear o seu domínio para este IP.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mapear o IP ingress para um subdomínio personalizado

O Draft cria uma versão para cada gráfico do Helm que cria -- cada aplicação em que está a trabalhar. Cada um obtém um nome gerado que é usado por **rascunho** como um _subdomínio_ no topo do domínio de _implantação_ de raiz que controla. (Neste exemplo, usamos `squillace.io` como domínio de implantação.) Para ativar este comportamento de subdomínio, tem de criar um registo A `'*.draft'` para as suas entradas DENS para o seu domínio de implantação, de modo a que cada subdomínio gerado seja encaminhado para o controlador de entrada do cluster Kubernetes. 

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
3. Adicione os servidores DNS que lhe forem dados ao fornecedor do seu domínio de implementação, o que lhe vai permitir utilizar o DNS do Azure para reencaminhar o seu domínio como quiser. A forma como o faz varia de acordo com o domínio; [delegar os seus servidores de nomes de domínio para Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) contém alguns dos detalhes que deve saber. 
4. Uma vez que o seu domínio tenha sido delegado no Azure DNS, crie uma entrada de registo para o mapeamento de domínio de implementação para o `ingress` IP a partir do passo 2 da secção anterior.
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
5. Reinstalar **esboço**

   1. Remova a **redigido** do cluster digitando `helm delete --purge draft` . 
   2. Reinstalar **o projeto** utilizando o mesmo `draft-init` comando, mas com a `--ingress-enabled` opção:
      ```bash
      draft init --ingress-enabled
      ```
      Responda às instruções como fez da primeira vez, acima. No entanto, tem mais uma pergunta a responder, utilizando o caminho de domínio completo que configurava com o DNS do Azure.

6. Introduza o seu domínio de nível superior para entrar (por exemplo, draft.example.com): draft.squillace.io
7. Quando ligar `draft up` desta vez, poderá ver a sua aplicação (ou `curl` ele) no URL do `<appname>.draft.<domain>.<top-level-domain>` formulário. No caso deste exemplo, `http://handy-labradoodle.draft.squillace.io` . 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Passos seguintes

Agora que já tem um cluster do Kubernetes no ACS, pode investigar com o [Azure Container Registry](../../container-registry/container-registry-intro.md) para criar mais e diferentes implementações deste cenário. Por exemplo, pode criar um conjunto de registos DNS de domínio draft._basedomain.toplevel_ que controle aspetos de subdomínios mais profundos para implementações do ACS específicas.






