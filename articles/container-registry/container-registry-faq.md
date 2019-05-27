---
title: O Azure Container Registry - perguntas mais frequentes
description: Respostas para perguntas mais frequentes sobre relacionados com o serviço do Azure Container Registry
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: 86efb6b655405500f994a5a5ec7acbd18c645004
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957854"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Perguntas mais frequentes sobre o Azure Container Registry

Este artigo aborda perguntas mais frequentes e problemas conhecidos sobre o Azure Container Registry.

## <a name="resource-management"></a>Gestão de recursos

- [Pode criar um Azure container registry com um modelo do Resource Manager?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Existe a análise de imagens no ACR de vulnerabilidade de segurança?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Como configurar o Kubernetes com o Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Como posso obter as credenciais de administrador para um registo de contentor?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Como posso obter as credenciais de administrador num modelo do Resource Manager?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Eliminação da replicação falha com o estado de proibido apesar da replicação é excluída com a CLI do Azure ou o Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Pode criar um Azure Container Registry com um modelo do Resource Manager?

Sim. Eis [um modelo](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) que pode usar para criar um registo.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Existe a análise de imagens no ACR de vulnerabilidade de segurança?

Sim. Veja a documentação do [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Como configurar o Kubernetes com o Azure Container Registry?

Consulte a documentação para [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e os passos para [Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Como posso obter as credenciais de administrador para um registo de contentor?

> [!IMPORTANT]
> A conta de utilizador de administrador foi concebida para um único utilizador aceder ao registo, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta de administrador com vários utilizadores. Identidade individual é recomendada para utilizadores e os principais de serviço para cenários sem periféricos. Ver [descrição geral da autenticação](container-registry-authentication.md).

Antes de obter as credenciais de administrador, certifique-se de que o utilizador de administrador do registo está ativado.

Para obter as credenciais com a CLI do Azure:

```azurecli
az acr credential show -n myRegistry
```

Utilizar o Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Como posso obter as credenciais de administrador num modelo do Resource Manager?

> [!IMPORTANT]
> A conta de utilizador de administrador foi concebida para um único utilizador aceder ao registo, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta de administrador com vários utilizadores. Identidade individual é recomendada para utilizadores e os principais de serviço para cenários sem periféricos. Ver [descrição geral da autenticação](container-registry-authentication.md).

Antes de obter as credenciais de administrador, certifique-se de que o utilizador de administrador do registo está ativado.

Para obter a primeira palavra-passe:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Para obter a segunda palavra-passe:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Eliminação da replicação falha com o estado de proibido apesar da replicação é excluída com a CLI do Azure ou o Azure PowerShell

O erro é visto quando o utilizador tenha permissões sobre um registo, mas não tem permissões ao nível do leitor na subscrição. Para resolver este problema, atribua permissões de leitor na subscrição para o utilizador:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>Operações de registo

- [Como posso acessar o Docker Registry V2 em API HTTP?](#how-do-i-access-docker-registry-http-api-v2)
- [Como faço para excluir todos os manifestos que não são referenciados por qualquer marca num repositório?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Por que não a utilização de quota de registo reduz depois de eliminar imagens?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Como posso validar alterações de quota de armazenamento?](#how-do-i-validate-storage-quota-changes)
- [Como posso autenticar com o meu registo ao executar a CLI num contentor?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [O Azure Container Registry oferece configuração apenas do TLS versão 1.2 e como ativar a versão do TLS 1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [O Azure Container Registry suporta conteúdo confiar?](#does-azure-container-registry-support-content-trust)
- [Como posso conceder acesso a imagens de extração ou push sem permissão para gerir o recurso de registro?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Como posso habilitar a quarentena de imagem automático para um registo](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Como posso acessar o Docker Registry V2 em API HTTP?

ACR suporta o Docker Registry V2 em API HTTP. As APIs que pode ser acessadas em `https://<your registry login server>/v2/`. Exemplo: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Como faço para excluir todos os manifestos que não são referenciados por qualquer marca num repositório?

Se estiver no bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Para o Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Nota: Pode adicionar `-y` no comando delete para ignorar a confirmação.

Para obter mais informações, consulte [eliminar imagens de contentor no Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Por que não a utilização de quota de registo reduz depois de eliminar imagens?

Esta situação pode acontecer se as camadas subjacentes ainda estão a ser referenciadas por outras imagens de contentor. Se eliminar uma imagem com nenhuma referência, a utilização do registo de atualizações de dentro de alguns minutos.

### <a name="how-do-i-validate-storage-quota-changes"></a>Como posso validar alterações de quota de armazenamento?

Crie uma imagem com uma camada de 1GB a utilizar o seguinte ficheiro de docker. Isto garante que a imagem tem uma camada que não é partilhada por qualquer outra imagem no registo.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Crie e envie a imagem para o seu registo com a CLI do docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Deverá conseguir ver que a utilização do armazenamento aumentou no portal do Azure, ou pode consultar a utilização com a CLI.

```bash
az acr show-usage -n myregistry
```

Eliminar a imagem com a CLI do Azure ou o portal e verificar o uso atualizado dentro de alguns minutos.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Como posso autenticar com o meu registo ao executar a CLI num contentor?

Terá de executar o contentor de CLI do Azure ao montar o socket de Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

O contentor, instala `docker`:

```bash
apk --update add docker
```

Em seguida, autenticar com o seu registo:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>O Azure Container Registry oferece configuração apenas do TLS versão 1.2 e como ativar a versão do TLS 1.2?

Sim. Ativar o TLS com qualquer cliente de docker recentes (versão 18.03.0 e acima). 

### <a name="does-azure-container-registry-support-content-trust"></a>O Azure Container Registry suporta conteúdo confiar?

Sim, pode usar imagens fidedignas no Azure Container Registry, uma vez que o [Docker Notary](https://docs.docker.com/notary/getting_started/) foi integrado e pode ser ativado. Para obter detalhes, consulte [conteúdo de confiança no Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Onde posso encontrar o ficheiro para o thumbprint localizado?

Em `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Chaves públicas e certificados de todas as funções (exceto as funções de delegação) são armazenados no `root.json`.
* Chaves públicas e certificados da função de delegação são armazenados no ficheiro JSON da sua função principal (por exemplo `targets.json` para o `targets/releases` função).

Recomenda-se para verificar esses certificados e chaves públicas após a verificação de TUF geral feita pelo cliente do Docker e Notary.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Como posso conceder acesso a imagens de extração ou push sem permissão para gerir o recurso de registro?

Suporta ACR [funções personalizadas](container-registry-roles.md) que fornecer diferentes níveis de permissões. Especificamente, `AcrPull` e `AcrPush` funções permitem aos utilizadores para imagens de solicitação e/ou push sem a permissão para gerir o recurso de registro no Azure.

* Portal do Azure: O registo -> controlo de acesso (IAM) -> Adicionar (selecione `AcrPull` ou `AcrPush` para a função).
* CLI do Azure: Encontre o ID de recurso do Registro, executando o seguinte comando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Pode atribuir a `AcrPull` ou `AcrPush` função a um utilizador (o exemplo seguinte utiliza `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Em alternativa, atribua a função a um principal de serviço identificado pelo respetivo ID de aplicação:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

O detentor, em seguida, é capaz de autenticar e imagens no registo de acesso.

* Para autenticar para um registo:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Para listar repositórios:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Para solicitar uma imagem:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Com a utilização de apenas o `AcrPull` ou `AcrPush` função, o detentor não tem permissão para gerir o recurso de registro no Azure. Por exemplo, `az acr list` ou `az acr show -n myRegistry` não mostrará o registo.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Como posso habilitar a quarentena de imagem automático para um registo?

Quarentena de imagem está atualmente uma funcionalidade de pré-visualização do ACR. Pode ativar o modo de quarentena de um registo para que apenas as imagens que passaram com êxito a análise de segurança são visíveis para os utilizadores normais. Para obter detalhes, consulte a [repositório do GitHub de ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics"></a>Diagnóstico

- [Falha de pull do docker com o erro: net/http: pedido foi cancelada ao aguardar por ligação (Client.Timeout foi excedido ao aguardar a cabeçalhos)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [push do docker for concluída com êxito, mas falha de pull do docker com o erro: não autorizado: autenticação necessária](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Ativar e obter os registos de depuração do daemon do docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Novas permissões de utilizador podem não estar em vigor imediatamente depois de atualizar](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Informações de autenticação não são fornecidas no formato correto em chamadas diretas de API de REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Por que não o portal do Azure lista todos os meus repositórios ou etiquetas?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>Falha de pull do docker com o erro: net/http: pedido foi cancelada ao aguardar por ligação (Client.Timeout foi excedido ao aguardar a cabeçalhos)

 - Se este erro é um problema transitório, em seguida, repita será concluída com êxito. 
 - Se `docker pull` falhar continuamente, em seguida, pode haver um problema com o daemon do docker. O problema geralmente pode ser mitigado ao reiniciar o daemon do docker. 
 - Se continuar a ver este problema após reiniciar daemon do docker, o problema poderia ser alguns problemas de conectividade de rede com a máquina. Para verificar se gerais de rede na máquina está em bom estado, experimente um comando como `ping www.bing.com`.
 - Deve ter sempre um mecanismo de repetição em todas as operações de cliente de docker.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>push do docker for concluída com êxito, mas falha de pull do docker com o erro: não autorizado: autenticação necessária

Este erro pode acontecer com a versão do Red Hat do daemon do docker, onde `--signature-verification` está ativada por predefinição. Pode verificar as opções de daemon do docker para Red Hat Enterprise Linux (RHEL) ou Fedora executando o seguinte comando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Por exemplo, o servidor de 28 de Fedora tem as seguintes opções de daemon do docker:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Com o `--signature-verification=false` em falta, `docker pull` falha com um erro semelhante a:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Para resolver o erro:
1. Adicione a opção `--signature-verification=false` ao ficheiro de configuração do daemon do docker `/etc/sysconfig/docker`. Por exemplo:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Reinicie o serviço de daemon do docker, executando o seguinte comando:

  ```bash
  sudo systemctl restart docker.service
  ```

Detalhes da `--signature-verification` pode ser encontrado ao executar `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Ativar e obter os registos de depuração do daemon do docker  

Inicie `dockerd` com o `debug` opção. Primeiro, crie o ficheiro de configuração do daemon do docker (`/etc/docker/daemon.json`) se não existir e adicionar o `debug` opção:

```json
{   
    "debug": true   
}
```

Em seguida, reinicie o daemon. Por exemplo, com o Ubuntu 14.04:

```bash
sudo service docker restart
```

Detalhes podem ser encontrados no [documentação do Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Os registos podem ser gerados em diferentes localizações, dependendo do seu sistema. Por exemplo, para o Ubuntu 14.04, ele tem `/var/log/upstart/docker.log`.   
Ver [documentação do Docker](https://docs.docker.com/engine/admin/#read-the-logs) para obter detalhes.    

 * Para o Docker para Windows, os registos são gerados sob % LOCALAPPDATA%/docker/. No entanto não pode conter todas as informações de depuração ainda.   

   Para acessar o registo de daemon completo, poderá ter alguns passos adicionais:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Agora tem acesso a todos os ficheiros da execução de VM `dockerd`. O registo está em `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Novas permissões de utilizador podem não estar em vigor imediatamente depois de atualizar

Quando novos concede permissões (novas funções) para um principal de serviço, a alteração poderão não entrar em vigor imediatamente. Existem duas razões possíveis:

* Atraso de atribuição de função de Azure Active Directory. Normalmente é rápido, mas pode demorar minutos devido a atraso de propagação.
* Atraso de permissão no servidor de token do ACR. Este processo poderá demorar até 10 minutos. Para atenuar, pode `docker logout` e, em seguida, autenticar novamente com o mesmo utilizador após 1 minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Atualmente ACR não suporta a eliminação de replicação inicial pelos utilizadores. A solução alternativa é incluir a replicação inicial criar no modelo, mas ignore a sua criação, adicionando `"condition": false` conforme mostrado abaixo:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Informações de autenticação não são fornecidas no formato correto em chamadas diretas de API de REST

Pode encontrar uma `InvalidAuthenticationInfo` erro, especialmente com o `curl` ferramenta com a opção `-L`, `--location` (a seguir redirecionamentos).
Por exemplo, a obter o blob com `curl` com `-L` opção e a autenticação básica:

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

A causa raiz é que alguns `curl` implementações seguem redirecionamentos com cabeçalhos do pedido original.

Para resolver o problema, terá de seguir redirecionamentos manualmente, sem os cabeçalhos. Imprimir os cabeçalhos de resposta com o `-D -` opção da `curl` e, em seguida, extraia: o `Location` cabeçalho:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Por que não o portal do Azure lista todos os meus repositórios ou etiquetas? 

Se estiver a utilizar o browser Edge, pode ver no máximo 100 repositórios ou etiquetas listadas. Se o seu registo tiver mais de 100 repositórios ou etiquetas, recomendamos que utilize o browser Firefox ou o Chrome para listar todas elas.

## <a name="tasks"></a>Tarefas

- [Como posso batch execuções de cancelar?](#how-do-i-batch-cancel-runs)
- [Como posso incluir a pasta de projeto no comando de compilação do az acr?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Como posso batch execuções de cancelar?

Os seguintes comandos cancelar todas as tarefas em execução no registo especificada.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Como posso incluir a pasta de projeto no comando de compilação do az acr?

Se passar uma pasta de origem local para o `az acr build` comando, o `.git` pasta está excluída do pacote carregado por predefinição. Pode criar um `.dockerignore` ficheiro com a definição seguinte. Ele informa ao comando para restaurar todos os ficheiros em `.git` no pacote carregado. 

```
!.git/**
```

Esta definição aplica-se também ao `az acr run` comando.

## <a name="cicd-integration"></a>Integração de CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Ações do GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)


## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais](container-registry-intro.md) sobre o Azure Container Registry.
