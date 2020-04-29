---
title: Perguntas mais frequentes
description: Respostas para perguntas frequentes relacionadas com o serviço de registo de contentores do Azure
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 7452b5dd3c952a13a28566914d2fe513689d4751
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618797"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Perguntas frequentes sobre o Registo de Contentores de Azure

Este artigo aborda frequentemente perguntas e questões conhecidas sobre o Registo de Contentores de Azure.

## <a name="resource-management"></a>Gestão de recursos

- [Posso criar um registo de contentores Azure usando um modelo de Gestor de Recursos?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Existe vulnerabilidade de segurança à procura de imagens no ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Como configurar kubernetes com registo de contentores Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Como consigo credenciais de administração para um registo de contentores?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Como consigo credenciais de administração num modelo de Gestor de Recursos?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [A eliminação da replicação falha com o estado proibido, embora a replicação seja eliminada utilizando o Azure CLI ou O PowerShell Azure](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [As regras da firewall são atualizadas com sucesso, mas não fazem efeito](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Posso criar um registo de contentores Azure usando um modelo de Gestor de Recursos?

Sim. Aqui está [um modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) que pode usar para criar um registo.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Existe vulnerabilidade de segurança à procura de imagens no ACR?

Sim. Consulte a documentação do [Azure Security Center,](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [Aqua.](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Como configurar kubernetes com registo de contentores Azure?

Consulte a documentação para [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e passos para [o Serviço Azure Kubernetes](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Como consigo credenciais de administração para um registo de contentores?

> [!IMPORTANT]
> A conta de utilizador administrativo foi concebida para que um único utilizador aceda ao registo, principalmente para efeitos de teste. Não recomendamos a partilha das credenciais de conta de administração com vários utilizadores. A identidade individual é recomendada para utilizadores e diretores de serviço para cenários sem cabeça. Consulte a [visão geral da autenticação](container-registry-authentication.md).

Antes de obter credenciais de administrador, certifique-se de que o utilizador administrativo do registo está ativado.

Para obter credenciais usando o Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Utilizando a Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Como consigo credenciais de administração num modelo de Gestor de Recursos?

> [!IMPORTANT]
> A conta de utilizador administrativo foi concebida para que um único utilizador aceda ao registo, principalmente para efeitos de teste. Não recomendamos a partilha das credenciais de conta de administração com vários utilizadores. A identidade individual é recomendada para utilizadores e diretores de serviço para cenários sem cabeça. Consulte a [visão geral da autenticação](container-registry-authentication.md).

Antes de obter credenciais de administrador, certifique-se de que o utilizador administrativo do registo está ativado.

Para obter a primeira senha:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Para obter a segunda senha:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>A eliminação da replicação falha com o estado proibido, embora a replicação seja eliminada utilizando o Azure CLI ou O PowerShell Azure

O erro é visto quando o utilizador tem permissões num registo, mas não tem permissões ao nível do Leitor na subscrição. Para resolver este problema, atribua permissões ao Leitor na subscrição ao utilizador:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>As regras da firewall são atualizadas com sucesso, mas não fazem efeito

Leva algum tempo para propagar alterações nas regras da firewall. Depois de alterar as definições de firewall, aguarde alguns minutos antes de verificar esta alteração.


## <a name="registry-operations"></a>Operações de registo

- [Como posso aceder ao Registo de Docker HTTP API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Como posso apagar todos os manifestos que não são referenciados por qualquer etiqueta num repositório?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Porque é que o uso da quota de registo não diminui após a apagar imagens?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Como posso validar as alterações das quotas de armazenamento?](#how-do-i-validate-storage-quota-changes)
- [Como é que eu autenticar com o meu registo quando executo o CLI num contentor?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Como ativar o TLS 1.2?](#how-to-enable-tls-12)
- [O Registo de Contentores Azure suporta o Content Trust?](#does-azure-container-registry-support-content-trust)
- [Como posso dar acesso a puxar ou empurrar imagens sem permissão para gerir o recurso de registo?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Como posso permitir a quarentena automática de imagem para um registo?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Como posso permitir o acesso anónimo?](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Como posso aceder ao Registo de Docker HTTP API V2?

ACR suporta Docker Registry HTTP API V2. As APIs podem ser `https://<your registry login server>/v2/`acedidas a . Exemplo: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Como posso apagar todos os manifestos que não são referenciados por qualquer etiqueta num repositório?

Se estiver em festa:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Para powershell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Nota: Pode `-y` adicionar o comando de eliminação para não confirmar a confirmação.

Para mais informações, consulte [Apagar imagens de contentores no Registo de Contentores De Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Porque é que o uso da quota de registo não diminui após a apagar imagens?

Esta situação pode acontecer se as camadas subjacentes ainda estiverem a ser referenciadas por outras imagens de contentores. Se eliminar uma imagem sem referências, o registo atualiza-se em poucos minutos.

### <a name="how-do-i-validate-storage-quota-changes"></a>Como posso validar as alterações das quotas de armazenamento?

Crie uma imagem com uma camada de 1GB utilizando o seguinte ficheiro de estivador. Isto garante que a imagem tem uma camada que não é partilhada por qualquer outra imagem no registo.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Construa e empurre a imagem para o seu registo utilizando o docker CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Você deve ser capaz de ver que o uso de armazenamento aumentou no portal Azure, ou você pode consultar o uso usando o CLI.

```azurecli
az acr show-usage -n myregistry
```

Elimine a imagem utilizando o Azure CLI ou portal e verifique a utilização atualizada em poucos minutos.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Como é que eu autenticar com o meu registo quando executo o CLI num contentor?

É necessário executar o recipiente Azure CLI montando a tomada Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

No recipiente, `docker`instale:

```bash
apk --update add docker
```

Em seguida, autenticar com o seu registo:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Como ativar o TLS 1.2?

Ativar o TLS 1.2 utilizando qualquer cliente docker recente (versão 18.03.0 e acima). 

> [!IMPORTANT]
> A partir de 13 de janeiro de 2020, o Registo de Contentores Azure exigirá todas as ligações seguras dos servidores e aplicações para utilizar o TLS 1.2. O suporte para TLS 1.0 e 1.1 será retirado.

### <a name="does-azure-container-registry-support-content-trust"></a>O Registo de Contentores Azure suporta o Content Trust?

Sim, pode utilizar imagens fidedignas no Registo de Contentores Azure, uma vez que o [Docker Notary](https://docs.docker.com/notary/getting_started/) foi integrado e pode ser ativado. Para mais detalhes, consulte content trust no registo de [contentores Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Onde está o ficheiro da impressão digital?

Em `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* As chaves públicas e os certificados de todas as `root.json`funções (com exceção das funções de delegação) estão armazenados na .
* As chaves públicas e os certificados do papel de delegação estão `targets.json` armazenados `targets/releases` no ficheiro JSON da sua função-mãe (por exemplo, para o papel).

Sugere-se verificar essas chaves e certificados públicos após a verificação global do TUF feita pelo Docker e pelo cliente Notário.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Como posso dar acesso a puxar ou empurrar imagens sem permissão para gerir o recurso de registo?

A ACR suporta [funções personalizadas](container-registry-roles.md) que fornecem diferentes níveis de permissões. `AcrPull` Especificamente, `AcrPush` e as funções permitem que os utilizadores puxem e/ou empurrem imagens sem a permissão para gerir o recurso de registo em Azure.

* Portal Azure: O seu registo -> Controlo de `AcrPull` Acesso `AcrPush` (IAM) -> Adicionar (Selecione ou para a Função).
* Azure CLI: Encontre a identificação do recurso do registo executando o seguinte comando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Em seguida, pode `AcrPull` `AcrPush` atribuir a ou a função a um utilizador (o seguinte exemplo utiliza): `AcrPull`

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Ou, atribuir a função a um princípio de serviço identificado pelo seu ID de aplicação:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

O designado é então capaz de autenticar e aceder a imagens no registo.

* Autenticar um registo:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Para enumerar repositórios:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Para puxar uma imagem:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Com o uso `AcrPull` apenas do ou `AcrPush` papel, o designado não tem a permissão para gerir o recurso de registo em Azure. Por `az acr list` exemplo, `az acr show -n myRegistry` ou não vai mostrar o registo.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Como posso permitir a quarentena automática de imagem para um registo?

A quarentena de imagem é atualmente uma característica de pré-visualização do ACR. Pode ativar o modo de quarentena de um registo de modo que apenas as imagens que tenham passado com sucesso são visíveis para os utilizadores normais. Para mais detalhes, consulte o [repo Do Cr GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Como posso permitir o acesso anónimo?

A criação de um registo de contentores Azure para acesso anónimo (público) é atualmente uma funcionalidade de pré-visualização. Para permitir o acesso do público, abra um bilhete de apoio para https://aka.ms/acr/support/create-ticket. Para mais detalhes, consulte o [Azure Feedback Forum](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries).


## <a name="diagnostics-and-health-checks"></a>Diagnósticos e verificações de saúde

- [Verifique a saúde com`az acr check-health`](#check-health-with-az-acr-check-health)
- [estivador puxa falhas com erro: net/http: pedido cancelado enquanto aguarda a ligação (Cliente.Timeout ultrapassado enquanto aguarda cabeçalhos)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push sucede mas puxar estivador falha com erro: não autorizado: autenticação necessária](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`sucede, mas os comandos de estivadores falham com erro: não autorizado: autenticação necessária](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Ativar e obter os registos de depuração do daemon do estivador](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Novas permissões de utilizador podem não ser eficazes imediatamente após a atualização](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [As informações de autenticação não são dadas no formato correto nas chamadas diretas rest API](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Porque é que o portal Azure não enumera todos os meus repositórios ou etiquetas?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Porque é que o portal Azure não consegue obter repositórios ou etiquetas?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Porque é que o meu pedido de pressão ou pressão falha com a operação proibida?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Como posso recolher vestígios de http no Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Verifique a saúde com`az acr check-health`

Para resolver problemas de ambiente comum e problemas de registo, consulte [Verificar a saúde de um registo de contentores Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>estivador puxa falhas com erro: net/http: pedido cancelado enquanto aguarda a ligação (Cliente.Timeout ultrapassado enquanto aguarda cabeçalhos)

 - Se este erro for uma questão transitória, então a nova tentativa terá sucesso.
 - Se `docker pull` falhar continuamente, pode haver um problema com o daemon docker. O problema pode geralmente ser atenuado reiniciando o daemon docker. 
 - Se continuar a ver este problema depois de reiniciar o Daemon do Docker, então o problema pode ser alguns problemas de conectividade da rede com a máquina. Para verificar se a rede geral da máquina é saudável, execute o seguinte comando para testar a conectividade do ponto final. A `az acr` versão mínima que contém este comando de verificação de conectividade é de 2.2.9. Atualize o seu Azure CLI se estiver a utilizar uma versão mais antiga.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Deves ter sempre um mecanismo de retenção em todas as operações dos clientes do Docker.

### <a name="docker-pull-is-slow"></a>Docker pull é lento
Utilize [esta](http://www.azurespeed.com/Azure/Download) ferramenta para testar a velocidade de descarregamento da rede de máquinas. Se a rede de máquinas for lenta, considere utilizar o Azure VM na mesma região que o seu registo. Isto geralmente dá-lhe uma velocidade de rede mais rápida.

### <a name="docker-push-is-slow"></a>O empurrão do Docker é lento.
Utilize [esta](http://www.azurespeed.com/Azure/Upload) ferramenta para testar a velocidade de carregamento da sua rede de máquinas. Se a rede de máquinas for lenta, considere utilizar o Azure VM na mesma região que o seu registo. Isto geralmente dá-lhe uma velocidade de rede mais rápida.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker push sucede mas puxão de estiva falha com erro: não autorizado: autenticação necessária

Este erro pode acontecer com a versão Red Hat `--signature-verification` do daemon Docker, onde é ativado por padrão. Você pode verificar as opções de daemon Docker para Red Hat Enterprise Linux (RHEL) ou Fedora executando o seguinte comando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Por exemplo, o Fedora 28 Server tem as seguintes opções de daemon do estivador:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Com `--signature-verification=false` falta, `docker pull` falha com um erro semelhante a:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Para resolver o erro:
1. Adicione a `--signature-verification=false` opção ao ficheiro `/etc/sysconfig/docker`de configuração da daemon Dodocker . Por exemplo:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Reiniciar o serviço Docker daemon executando o seguinte comando:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Os `--signature-verification` detalhes podem ser `man dockerd`encontrados correndo .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login sucede mas docker falha com erro: não autorizado: autenticação necessária

Certifique-se de que utiliza um URL `docker push myregistry.azurecr.io/myimage:latest`de servidor de minúsculas minúsculas, por exemplo, mesmo que o nome do recurso de registo seja maiúsculo ou misto, como `myRegistry`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Ativar e obter os registos de depuração do daemon Docker    

Comece `dockerd` com `debug` a opção. Em primeiro lugar, crie o`/etc/docker/daemon.json`ficheiro de configuração do Daemon Docker () se não existir, e adicione a opção: `debug`

```json
{    
    "debug": true    
}
```

Então, reinicie o daemon. Por exemplo, com Ubuntu 14.04:

```bash
sudo service docker restart
```

Os detalhes podem ser encontrados na documentação do [Docker.](https://docs.docker.com/engine/admin/#enable-debugging)    

 * Os registos podem ser gerados em diferentes locais, dependendo do seu sistema. Por exemplo, para Ubuntu 14.04, é `/var/log/upstart/docker.log`.    
Consulte a [documentação do Docker](https://docs.docker.com/engine/admin/#read-the-logs) para obter detalhes.    

 * Para o Docker for Windows, os registos são gerados em %LOCALAPPDATA%/docker/. No entanto, pode ainda não conter todas as informações sobre depuração.    

   Para aceder ao registo completo do daemon, poderá necessitar de alguns passos extras:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Agora tem acesso a todos os ficheiros do VM em execução. `dockerd` O tronco `/var/log/docker.log`está em .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Novas permissões de utilizador podem não ser eficazes imediatamente após a atualização

Quando concede novas permissões (novas funções) a um diretor de serviço, a alteração pode não entrar em vigor imediatamente. Há duas razões possíveis:

* Atraso na atribuição de funções de Diretório Ativo Azure. Normalmente é rápido, mas pode levar minutos devido a atraso de propagação.
* Atraso de permissão no servidor de fichas ACR. Isto pode levar até 10 minutos. Para mitigar, `docker logout` pode e depois autenticar novamente com o mesmo utilizador após 1 minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Atualmente, a ACR não suporta a eliminação da replicação domiciliária pelos utilizadores. A suposição é incluir a replicação doméstica criar no `"condition": false` modelo, mas saltar a sua criação adicionando como mostrado abaixo:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>As informações de autenticação não são dadas no formato correto nas chamadas diretas rest API

Pode encontrar `InvalidAuthenticationInfo` um erro, `curl` especialmente utilizando `-L` `--location` a ferramenta com a opção (para seguir redirecionamentos).
Por exemplo, buscar a `curl` bolha `-L` utilizando com opção e autenticação básica:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

pode resultar na seguinte resposta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

A causa principal `curl` é que algumas implementações seguem redirecionamentos com cabeçalhos do pedido original.

Para resolver o problema, tem de seguir redirecionamentos manualmente sem os cabeçalhos. Imprima os cabeçalhos `curl` de resposta com `Location` a opção `-D -` de extrair: o cabeçalho:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Porque é que o portal Azure não enumera todos os meus repositórios ou etiquetas? 

Se estiver a utilizar o navegador Microsoft Edge/IE, pode ver no máximo 100 repositórios ou tags. Se o seu registo tiver mais de 100 repositórios ou etiquetas, recomendamos que utilize o navegador Firefox ou Chrome para enumerar todos eles.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Porque é que o portal Azure não consegue obter repositórios ou etiquetas?

O navegador pode não ser capaz de enviar o pedido de rebuscamento de repositórios ou etiquetas para o servidor. Pode haver várias razões como:

* Falta de conectividade de rede
* Firewall
* Bloqueadores de anúncios
* Erros dNS

Contacte o seu administrador de rede ou verifique a configuração e a conectividade da sua rede. Tente `az acr check-health -n yourRegistry` correr utilizando o seu AZURE CLI para verificar se o seu ambiente é capaz de se ligar ao Registo de Contentores. Além disso, também pode experimentar uma sessão incógnita ou privada no seu navegador para evitar qualquer cache ou cookies de navegador.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Porque é que o meu pedido de pressão ou pressão falha com a operação proibida?

Aqui estão alguns cenários em que as operações talvez não sejam permitidas:
* Os registos clássicos já não são suportados. Por favor, atualize para um [SKUs](https://aka.ms/acr/skus) suportado usando a [atualização az acr](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) ou o portal Azure.
* A imagem ou repositório talvez bloqueado para que não possa ser apagado ou atualizado. Pode utilizar o comando [de repositório az acr show](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) para ver os atributos atuais.
* Algumas operações são proibidas se a imagem estiver em quarentena. Saiba mais sobre [quarentena.](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)

### <a name="how-do-i-collect-http-traces-on-windows"></a>Como posso recolher vestígios de http no Windows?

#### <a name="prerequisites"></a>Pré-requisitos

- Ativar a desencriptação https no violinista:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Activao Docker a usar um proxy através do Docker ui:<https://docs.docker.com/docker-for-windows/#proxies>
- Certifique-se de que reverte quando estiver completo.  O Docker não vai trabalhar com isto ativado e o violinista não está a funcionar.

#### <a name="windows-containers"></a>Contentores do Windows

Configure Proxy Docker para 127.0.0.1:8888

#### <a name="linux-containers"></a>Contentores do Linux

Encontre o ip do interruptor virtual Docker vm:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configure o proxy Docker para a saída do comando anterior e a porta 8888 (por exemplo 10.0.75.1:8888)

## <a name="tasks"></a>Tarefas

- [Como posso cancelar as corridas?](#how-do-i-batch-cancel-runs)
- [Como incluo a pasta .git no comando de construção az acr?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [As tarefas suportam o GitLab para os gatilhos de Origem?](#does-tasks-support-gitlab-for-source-triggers)
- [Que serviço de gestão de repositórios git suporta as Tarefas?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Como posso cancelar as corridas?

Os seguintes comandos cancelam todas as tarefas de execução no registo especificado.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Como incluo a pasta .git no comando de construção az acr?

Se passar uma pasta de `az acr build` origem `.git` local para o comando, a pasta é excluída da embalagem carregada por padrão. Pode criar `.dockerignore` um ficheiro com a seguinte definição. Diz ao comando para restaurar `.git` todos os ficheiros no pacote carregado. 

`!.git/**`

Esta definição também `az acr run` se aplica ao comando.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>As tarefas suportam o GitLab para os gatilhos de Origem?

Atualmente não apoiamos o GitLab para os gatilhos source.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Que serviço de gestão de repositórios git suporta as Tarefas?

| Serviço Git | Contexto de origem | Construção manual | Construção automática através do gatilho de compromisso |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Sim | Sim |
| Repositórios do Azure | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Sim | Sim |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Sim | Não |
| BitBucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Sim | Não |

## <a name="run-error-message-troubleshooting"></a>Executar resolução de problemas de mensagem de erro

| Mensagem de erro | Guia de resolução de problemas |
|---|---|
|Nenhum acesso foi configurado para o VM, portanto não foram encontradas subscrições|Isto pode acontecer se `az login --identity` estiver a usar na sua Tarefa ACR. Este é um erro transitório e ocorre quando a atribuição do papel da sua Identidade Gerida não se propagou. Esperar alguns segundos antes de voltar a tentar funciona.|

## <a name="cicd-integration"></a>Integração CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Ações gitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais](container-registry-intro.md) sobre o Registo de Contentores Azure.
